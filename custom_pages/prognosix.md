---
title: PrognosiX
fullscreen: false
hidden: false
---
# Scan Pipeline: How It Works

This document explains the full lifecycle of a CBCT scan — from the moment a client computes a SHA-256 hash to the point where nerve segmentation masks are ready for download. No code is included here; see [Creating a Scan](./creating-a-scan.md) and [Enhancing a Scan](./enhancing-a-scan.md) for implementation guides.

***

## Overview

Processing a scan happens in two independent stages:

| Stage                 | What happens                                                              | Duration    |
| --------------------- | ------------------------------------------------------------------------- | ----------- |
| **De-identification** | PHI is stripped from DICOM metadata using the Google Cloud Healthcare API | 25 – 55 min |
| **Enhancement**       | AI nerve segmentation inference runs on the clean DICOM data              | 5 – 45 min  |

Enhancement cannot begin until de-identification completes. De-identification begins automatically when the upload lands in cloud storage — no second API call is required.

***

## Stage 1: Upload & De-identification

```mermaid
flowchart TD
    A([Client]) --> B["Compute SHA-256 of ZIP locally"]
    B --> C["POST /api/practices/:id/scans\n{ sha256, filename, sizeBytes }"]

    C --> D{Scan already\nexists?}
    D -->|"Yes — same practice + SHA-256"| E["200 · Return existing scan\nno upload URL issued"]
    D -->|No| F["201 · Create scan record\nstatus: pending\nIssue GCS resumable upload URL"]

    F --> G["Client PUT bytes\ndirectly to GCS\n⚠ bytes never touch the backend"]
    G --> H[/"GCS: OBJECT_FINALIZE\nevent fired"/]

    H --> I["Pub/Sub push → backend\nPOST /internal/pubsub/gcs-finalize"]
    I --> J{Scan status\n== pending?}
    J -->|"No — already processing\nor later"| K["ACK · no-op\n(idempotent redelivery guard)"]
    J -->|Yes| L["Publish message to\ndeid queue\nthen commit\npending → processing"]

    L --> M[/"Deid worker\npulls message"/]

    M --> N["① Create ephemeral\nDICOM stores in\nHealthcare API"]
    N --> O["② Unzip source.zip\nto GCS work prefix"]
    O --> P["③ Import .dcm files\ninto source store\n⏱ LRO · 5 – 15 min"]
    P --> Q["④ Validate DICOM structure\n1 study · 1 series\nCBCT SOP class allowlist"]
    Q --> R["⑤ De-identify source → dest\nstrip ALL tags\n⏱ LRO · 20 – 40 min"]
    R --> S["⑥ Export dest store\nto GCS work prefix\nuncompressed DICOM"]
    S --> T["⑦ Stream-zip exported\nfiles → deidentified.zip"]
    T --> U["⑧ PATCH /internal/scans/:id\nreport deidentified.zip path"]
    U --> V["⑨ Delete source.zip\n(PHI removed from storage)"]
    V --> W["⑩ Clean up work files\n& ephemeral DICOM stores"]
    W --> X["ACK message"]

    U -->|"processing → deidentified"| Y([Scan ready for\nenhancement])
```

***

## Stage 1: Failure & Retry Paths

The deid worker **nacks on every error** — both transient and permanent. Pub/Sub redelivers automatically with exponential backoff. After five failed deliveries, the message is dead-lettered.

```mermaid
flowchart TD
    A[/"Worker encounters\nany error"/]

    A --> B["NACK\n(ack deadline set to 0)"]
    B --> C[/"Pub/Sub redelivers\nwith backoff"/]
    C --> D{Attempt\nnumber}
    D -->|"1 – 4"| E["Worker retries\nfrom beginning"]
    E --> A
    D -->|"5th nack"| F[/"Message\ndead-lettered"/]

    F --> G["Pub/Sub push →\nPOST /internal/pubsub/deid-dlq"]
    G --> H["processing → failed\nfailure_reason populated"]
    H --> I["Sentry alert\n+ Slack / email"]
    I --> J([Scan stuck in\nfailed state])

    J --> K["Operator calls\nPOST /internal/scans/:id/requeue"]
    K --> L["failed → processing\nRepublish to deid queue"]
    L --> E

    style F fill:#f5c6cb,stroke:#c00
    style H fill:#f5c6cb,stroke:#c00
    style J fill:#f5c6cb,stroke:#c00
```

### What causes a permanent failure (requires re-upload)

| Failure point           | Cause                                    | Recoverable?                           |
| ----------------------- | ---------------------------------------- | -------------------------------------- |
| Import LRO              | ZIP contains no `.dcm` files             | No — re-upload required                |
| Validation              | More than one study or series in the ZIP | No — re-upload required                |
| Validation              | SOP Class UID not on CBCT allowlist      | No — re-upload required                |
| Deidentify LRO          | Healthcare API quota exceeded            | Yes — operator requeue                 |
| PATCH callback 404/409  | Scan row in unexpected state             | Yes — operator investigation           |
| Wall-clock cap (50 min) | LRO stalled indefinitely                 | Yes — operator requeue after diagnosis |

***

## Scan Status Lifecycle

```mermaid
stateDiagram-v2
    [*] --> pending : POST /scans\n(upload registered)
    pending --> processing : GCS OBJECT_FINALIZE\n(deid queue message published)
    processing --> deidentified : Worker PATCH callback\n(deidentified.zip written)
    processing --> failed : DLQ consumer\n(5 worker nacks)
    failed --> processing : Operator requeue\n(republish to deid queue)
    deidentified --> [*]
```

> `valid` and `rejected` appear in the status allowlist for legacy rows only and are not part of the active pipeline.

***

## Stage 2: Enhancement

Enhancement is triggered explicitly via a separate API call after the scan reaches `deidentified`. A `ScanEnhancement` job is created — the scan's own status does not change.

```mermaid
flowchart TD
    A([Scan status:\ndeidentified]) --> B["POST /api/practices/:id/scans/:id/enhancements\n{ modelId: 'vertex-cbct-v1' }"]

    B --> C["Publish to enhance queue\nthen commit\nInsert ScanEnhancement\nstatus: processing"]

    C --> D[/"Enhance worker\npulls message"/]

    D --> E["① Download deidentified.zip\nfrom GCS"]
    E --> F["② Parse & sort\nDICOM slices"]
    F --> G["③ Encode slices as\nfloat16 pixel bytes\n+ normalization params"]
    G --> H["④ Batch slices\nrespecting 1.2 MB\npayload ceiling"]
    H --> I["⑤ Send batches to\nVertex AI endpoint\n⏱ ~5 – 45 min total"]
    I --> J["⑥ Collect uint8\nmask per slice"]
    J --> K["⑦ Stream-zip masks\n→ enhanced.zip\n(one .bin per slice)"]
    K --> L["⑧ PATCH /internal/scan-enhancements/:id\nreport enhanced.zip path"]
    L --> M["ACK message"]
    L -->|"processing → done"| N([Enhancement ready\nenhancedStoragePath populated])
```

***

## Enhancement: Failure & Retry Paths

Enhancement uses the same nack-based retry model as de-identification.

```mermaid
flowchart TD
    A[/"Enhance worker\nencounters any error"/]
    A --> B["NACK"]
    B --> C[/"Pub/Sub redelivers"/]
    C --> D{5th nack?}
    D -->|No| E["Worker retries"]
    E --> A
    D -->|Yes| F[/"Dead-lettered to\nenhance DLQ topic"/]
    F --> G["Pub/Sub push →\nPOST /internal/pubsub/enhance-dlq"]
    G --> H["processing → failed\nfailure_reason populated"]
    H --> I["Sentry alert\n+ Slack / email"]
    I --> J([Enhancement stuck\nin failed state])
    J --> K["Queue a new enhancement job\nPOST /enhancements again\nwith a fresh enhancementId"]

    style F fill:#f5c6cb,stroke:#c00
    style H fill:#f5c6cb,stroke:#c00
    style J fill:#f5c6cb,stroke:#c00
```

> Unlike the deid pipeline, there is no operator requeue endpoint for enhancements — create a new job via the API.

***

## ScanEnhancement Status Lifecycle

```mermaid
stateDiagram-v2
    [*] --> processing : POST /enhancements\n(enhance queue message published)
    processing --> done : Worker PATCH callback\n(enhanced.zip written)
    processing --> failed : DLQ consumer\n(5 worker nacks)
    done --> [*]
    failed --> [*] : Queue a new job
```

***

## Full End-to-End Timeline

```mermaid
timeline
    title Scan from upload to enhancement complete
    section Upload
        0 min : Client computes SHA-256
               : POST /scans → receive uploadUrl
               : PUT bytes to GCS
    section De-identification
        ~1 min : GCS OBJECT_FINALIZE fires
                : Pub/Sub delivers to backend
                : Worker pulls message
                : Import LRO begins
        ~15 min : Import LRO completes
                 : Validation passes
                 : Deidentify LRO begins
        ~45 min : Deidentify LRO completes
                 : Export LRO completes
                 : deidentified.zip written
                 : Scan status → deidentified
    section Enhancement
        ~46 min : POST /enhancements
                 : Worker pulls message
                 : Vertex AI inference begins
        ~55 min : Inference complete
                 : enhanced.zip written
                 : ScanEnhancement status → done
```

<br />
