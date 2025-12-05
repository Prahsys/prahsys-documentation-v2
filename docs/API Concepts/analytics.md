---
title: Analytics
excerpt: >-
  Prahsys provides powerful analytics endpoints that give you deep insights into
  your payment activity, customer behavior, transaction patterns, and payout
  details. These endpoints are designed to help you build dashboards, generate
  reports, and monitor payment trends in real-time.  ##
deprecated: false
hidden: false
icon: far fa-chart-pie
metadata:
  robots: index
---
## What You Can Analyze

Prahsys offers four types of analytics to help you understand different aspects of your payment operations:

* **Payment Analytics** - Track payment volume, success rates, revenue trends, and payment methods
* **Customer Analytics** - Monitor customer acquisition, retention, and activity patterns
* **Transaction Analytics** - Analyze transaction types, operation patterns, and success rates
* **Payout Analytics** - Understand settlement timing, fee structures, and payout amounts

## How Analytics Work

All analytics endpoints share a common set of powerful features that give you flexibility in how you query and group your data.

### Time Periods

You can specify time periods in two ways:

**Relative Periods** - Use the `forLast` parameter to query recent time periods:

```bash
# Last 30 days
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/payments?forLast=30d" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

Available values for `forLast`:

* `1h` - Last hour
* `24h` - Last 24 hours
* `7d` - Last 7 days
* `30d` - Last 30 days
* `3mo` - Last 3 months
* `6mo` - Last 6 months
* `1y` - Last year

**Absolute Periods** - Use `periodStart` and `periodEnd` parameters to specify exact date ranges:

```bash
# Specific date range (January 2025)
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/payments?periodStart=2025-01-01T00:00:00Z&periodEnd=2025-01-31T23:59:59Z" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

<Callout icon="📘" theme="info">
  You **cannot** use both `forLast` and `periodStart`/`periodEnd` in the same query. Choose one approach based on your needs.
</Callout>

### Intervals and Grouping

The `interval` parameter controls how your data is grouped over time. This is essential for creating time-series charts and understanding trends.

Available intervals:

* `hour` - Group by hour (best for short time periods like 1-24 hours)
* `day` - Group by day (default, ideal for 7-30 days)
* `week` - Group by week (good for 3-6 months)
* `month` - Group by month (ideal for 6-12 months)
* `year` - Group by year (for multi-year analysis)

```bash
# Daily breakdown for the last 30 days
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/payments?forLast=30d&interval=day" \
  -H "Authorization: Bearer YOUR_API_KEY"

# Monthly breakdown for the last year
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/payments?forLast=1y&interval=month" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

### Timezone Handling

By default, all analytics use UTC timezone. You can specify a different timezone using the `timezone` parameter to ensure intervals align with your business hours.

```bash
# Group by day in Phoenix timezone
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/payments?forLast=30d&interval=day&timezone=America/Phoenix" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

Supported timezone formats:

* **IANA timezone identifiers**: `America/Phoenix`, `America/Los_Angeles`, `America/New_York`, `Europe/London`, etc.
* **Common abbreviations**: `MST`, `MDT`, `PST`, `PDT`, `EST`, `EDT`, etc.

<Callout icon="❗️">
  **Important** 

  The timezone affects how intervals are calculated. 

  For example, with `interval=day&timezone=America/Phoenix`, data is grouped by Phoenix days (midnight to midnight Phoenix time), not UTC days.
</Callout>

## Response Structure

All analytics endpoints return data in a consistent format, making it easy to work with different analytics types using the same code patterns.

### Common Response Format

Every analytics response is wrapped in a standard API response:

```json
{
  "success": true,
  "statusCode": 200,
  "offset": null,
  "limit": null,
  "data": {
    // Analytics data here
  }
}
```

### Analytics Data Structure

Within the `data` object, all analytics responses follow this pattern:

```json
{
  "periodStart": "2025-11-28T00:00:00.000000Z",
  "periodEnd": "2025-12-05T00:00:00.000000Z",
  "timezone": "UTC",

  // Period-level aggregate metrics
  "totalMetric1": 1234,
  "totalMetric2": 5678,

  // Interval configuration
  "interval": "day",

  // Per-interval breakdown
  "intervals": [
    {
      "intervalStart": "2025-11-28T00:00:00.000000Z",
      "intervalEnd": "2025-11-29T00:00:00.000000Z",
      "totalMetric1": 176,
      "totalMetric2": 811
    },
    {
      "intervalStart": "2025-11-29T00:00:00.000000Z",
      "intervalEnd": "2025-11-30T00:00:00.000000Z",
      "totalMetric1": 182,
      "totalMetric2": 824
    }
    // ... more intervals
  ]
}
```

### Period-Level vs Interval-Level Data

**Period-Level Data** - The top-level metrics represent aggregates for the entire time period you queried. Use these for summary cards, totals, and overall statistics.

**Interval-Level Data** - The `intervals` array breaks down the same metrics into smaller time chunks based on your `interval` parameter. Use these for:

* Time-series charts and graphs
* Identifying trends over time
* Spotting anomalies or patterns
* Creating detailed timeline visualizations

### Understanding the Intervals Array

Each object in the `intervals` array contains:

* `intervalStart` - The beginning of this time interval (ISO 8601 timestamp)
* `intervalEnd` - The end of this time interval (ISO 8601 timestamp)
* All the same metrics as the period-level data, but only for this specific interval

**Empty Intervals:** If there's no activity during a particular interval, Prahsys automatically includes that interval with zero values. This ensures you have complete time-series data without gaps, making it easier to create charts and visualizations.

Example with an empty interval:

```json
{
  "intervalStart": "2025-11-30T00:00:00.000000Z",
  "intervalEnd": "2025-12-01T00:00:00.000000Z",
  "numPaymentsCreated": 0,
  "numPaymentsProcessed": 0,
  "totalCapturedAmount": 0,
  "totalRefundedAmount": 0
  // ... all other metrics set to 0 or null
}
```

**Charting with Intervals:** The intervals array is designed to be used directly with charting libraries:

```javascript
// Example: Creating a revenue chart
const chartData = response.data.intervals.map(interval => ({
  date: new Date(interval.intervalStart),
  revenue: interval.totalNetCapturedAmount
}));

// Now use chartData with your preferred charting library
```
