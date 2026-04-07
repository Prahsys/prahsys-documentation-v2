---
title: Start Pay Schedule
excerpt: >-
  Activates the pay schedule for an order. The order must already have pay
  schedule data configured

  (via the Update or Create Order endpoint) but not yet started.


  If a `startOn` date is provided, the schedule is deferred to that date — no
  payment is processed immediately.

  An invoice is sent to the customer via their configured notification
  preferences (email/SMS).


  If no `startOn` date is provided and `payOnStart` is true, the first payment
  is processed immediately

  using the stored billing token (or a provided `session`). A billing token must
  be attached to the order

  pay schedule or a pay session must be specified in the start request body.


  If no `startOn` date is provided and `payOnStart` is false, the schedule is
  activated today with the first

  payment due one period from now. An invoice is sent to the customer.


  For autopay schedules, a tokenized billing method must be stored before
  activation. A `session` can be

  provided to tokenize a new payment method as part of this request.


  ## Behavior Matrix


  The behavior of this endpoint depends on three parameters: `autopay` (set on
  the pay schedule),

  `startOn`, and `payOnStart`. Below is a reference for all combinations.

  The `startOn` example uses +7 days from today for illustration; any future
  date works.


  | autopay | startOn | payOnStart | Immediate Payment? | start_date |
  current_due_date    |

  |---------|---------|------------|--------------------|------------|---------------------|

  | true    | +7d     | true       | No                 | startOn    |
  startOn             |

  | true    | +7d     | false      | No                 | startOn    | startOn +
  1 period  |

  | true    | null    | true       | Yes                | today      | today + 1
  period    |

  | true    | null    | false      | No                 | today      | today + 1
  period    |

  | false   | +7d     | true       | No                 | startOn    |
  startOn             |

  | false   | +7d     | false      | No                 | startOn    | startOn +
  1 period  |

  | false   | null    | true       | Yes                | today      | today + 1
  period    |

  | false   | null    | false      | No                 | today      | today + 1
  period    |


  **Key notes:**

  - When `startOn` is provided, no payment is processed regardless of other
  parameters. An invoice is sent instead.

  - When `payOnStart` is true and `startOn` is set, the first payment is **due**
  on the `startOn` date (not processed immediately).

  - When `payOnStart` is true and `startOn` is null, the first payment is
  processed immediately.

  - "1 period" refers to the pay schedule's configured frequency (e.g., 1 month
  for monthly).
api:
  file: payments-openapi.json
  operationId: startPaySchedule
hidden: false
---