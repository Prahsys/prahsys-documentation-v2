---
title: Analytics
deprecated: false
hidden: false
icon: far fa-chart-pie
metadata:
  robots: index
---
Prahsys provides powerful analytics endpoints that give you deep insights into your payment activity, customer behavior, transaction patterns, and payout details. These endpoints are designed to help you build dashboards, generate reports, and monitor payment trends in real-time.

## What You Can Analyze

Prahsys offers four types of analytics to help you understand different aspects of your payment operations:

* **Payment Analytics** - Track payment volume, success rates, revenue trends, and payment methods
* **Customer Analytics** - Monitor customer acquisition, retention, and activity patterns
* **Transaction Analytics** - Analyze transaction types, operation patterns, and success rates
* **Payout Analytics** - Understand settlement timing, fee structures, and payout amounts

## Common Use Cases

**Building Dashboards**
Pull analytics data to populate real-time dashboards showing key metrics like daily revenue, success rates, and customer growth.

**Generating Reports**
Create periodic reports (weekly, monthly, quarterly) to track business performance and identify trends over time.

**Monitoring Payment Trends**
Track payment patterns to identify peak transaction times, seasonal trends, and potential issues before they become problems.

## How Analytics Work

All analytics endpoints share a common set of powerful features that give you flexibility in how you query and group your data.

### Time Periods

You can specify time periods in two ways:

**Relative Periods** - Use the `forLast` parameter to query recent time periods:

```bash
# Last 30 days
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/payments?forLast=30d&filter[merchantId]=123456" \
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
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/payments?periodStart=2025-01-01T00:00:00Z&periodEnd=2025-01-31T23:59:59Z&filter[merchantId]=123456" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

> **Note:** You cannot use both `forLast` and `periodStart`/`periodEnd` in the same query. Choose one approach based on your needs.

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
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/payments?forLast=30d&interval=day&filter[merchantId]=123456" \
  -H "Authorization: Bearer YOUR_API_KEY"

# Monthly breakdown for the last year
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/payments?forLast=1y&interval=month&filter[merchantId]=123456" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

### Timezone Handling

By default, all analytics use UTC timezone. You can specify a different timezone using the `timezone` parameter to ensure intervals align with your business hours.

```bash
# Group by day in Phoenix timezone
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/payments?forLast=30d&interval=day&timezone=America/Phoenix&filter[merchantId]=123456" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

Supported timezone formats:

* **IANA timezone identifiers**: `America/Phoenix`, `America/Los_Angeles`, `America/New_York`, `Europe/London`, etc.
* **Common abbreviations**: `MST`, `MDT`, `PST`, `PDT`, `EST`, `EDT`, etc.

> **Important:** The timezone affects how intervals are calculated. For example, with `interval=day&timezone=America/Phoenix`, data is grouped by Phoenix days (midnight to midnight Phoenix time), not UTC days.

### Filtering and Scoping

All analytics endpoints require you to specify which merchant(s) to analyze using either `merchantId` or `channelPartnerId` filters.

**Single Merchant:**

```bash
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/payments?forLast=30d&filter[merchantId]=123456" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

**Multiple Merchants:**

```bash
# Comma-separated merchant IDs
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/payments?forLast=30d&filter[merchantId]=123456,789012,345678" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

**Channel Partner (all associated merchants):**

```bash
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/payments?forLast=30d&filter[channelPartnerId]=999888" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

Each analytics type also supports additional filters specific to that data type. See the individual analytics sections below for details.

For more information on filtering, see our [Filtering and Sorting](https://docs.prahsys.com/docs/filtering-and-sorting) documentation.

### Pagination

When querying analytics with many intervals (like daily data for a full year), you can use pagination to retrieve results in manageable chunks.

```bash
# Get first 100 intervals
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/payments?forLast=1y&interval=day&filter[merchantId]=123456&offset=0&limit=100" \
  -H "Authorization: Bearer YOUR_API_KEY"

# Get next 100 intervals
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/payments?forLast=1y&interval=day&filter[merchantId]=123456&offset=100&limit=100" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

Parameters:

* `offset` - Number of intervals to skip (must be >= 0)
* `limit` - Number of intervals to return (must be > 0)

For more information on pagination, see our [Pagination](https://docs.prahsys.com/docs/pagination) documentation.

## Available Analytics Types

### Payment Analytics

Payment analytics help you understand your revenue, payment volumes, and payment method distribution.

**Endpoint:** `GET /n1/organization/{organizationId}/analytics/payments`

**Common Filters:**

* `filter[status]` - Filter by payment status (comma-separated for multiple)
  * Values: `CAPTURED`, `AUTHORIZED`, `PARTIALLY_CAPTURED`, `CANCELLED`, `REFUNDED`, `PARTIALLY_REFUNDED`, `DECLINED`, `ABORTED`, `FAILURE`, `FAILED`, `PENDING`, `SUCCESS`, `UNKNOWN`, `VERIFIED`, `SETTLING`, `SETTLED`
* `filter[source]` - Filter by payment source
  * Values: `CARD_PRESENT`, `INTERNET`
* `filter[successful]` - Show only successful payments
  * Values: `true`, `false`
* `filter[failed]` - Show only failed payments
  * Values: `true`, `false`
* `filter[refunded]` - Show only refunded payments
  * Values: `true`, `false`
* `filter[inProgress]` - Show only in-progress payments
  * Values: `true`, `false`
* `filter[merchantId]` - Filter by merchant ID(s) (required)
* `filter[channelPartnerId]` - Filter by channel partner ID (alternative to merchantId)

**Response Fields:**

_Period-Level Metrics (aggregate for entire period):_

* `numPaymentsCreated` - Total number of payment records created
* `numPaymentsProcessed` - Number of payments with funds captured (captured amount > 0)
* `numPaymentsIncomplete` - Number of payments created but funds not yet captured
* `numPaymentsWithRefunds` - Number of payments that have had one or more refunds
* `numCardPresentPayments` - Number of card present payments (e.g., terminal transactions)
* `numCardNotPresentPayments` - Number of card not present payments (e.g., online payments)
* `totalCapturedAmount` - Total amount captured across all payments
* `totalRefundedAmount` - Total amount refunded across all payments
* `totalNetCapturedAmount` - Net captured amount (captured - refunded)
* `averageNetCapturedAmount` - Average net captured amount per payment
* `minNetCapturedAmount` - Minimum net captured amount
* `maxNetCapturedAmount` - Maximum net captured amount

_Interval-Level Metrics:_
The `intervals` array contains the same metrics broken down by your chosen interval (hour, day, week, month, or year), plus `intervalStart` and `intervalEnd` timestamps for each interval.

**Example: Last 7 Days of Payment Activity**

```bash
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/payments?forLast=7d&interval=day&filter[merchantId]=123456" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

Response:

```json
{
  "success": true,
  "statusCode": 200,
  "offset": null,
  "limit": null,
  "data": {
    "periodStart": "2025-11-28T00:00:00.000000Z",
    "periodEnd": "2025-12-05T00:00:00.000000Z",
    "timezone": "UTC",
    "numPaymentsCreated": 487,
    "numPaymentsProcessed": 438,
    "numPaymentsIncomplete": 49,
    "numPaymentsWithRefunds": 12,
    "numCardPresentPayments": 324,
    "numCardNotPresentPayments": 114,
    "totalCapturedAmount": 52340.75,
    "totalRefundedAmount": 1240.50,
    "totalNetCapturedAmount": 51100.25,
    "averageNetCapturedAmount": 116.67,
    "minNetCapturedAmount": 5.00,
    "maxNetCapturedAmount": 2850.00,
    "interval": "day",
    "intervals": [
      {
        "intervalStart": "2025-11-28T00:00:00.000000Z",
        "intervalEnd": "2025-11-29T00:00:00.000000Z",
        "numPaymentsCreated": 68,
        "numPaymentsProcessed": 62,
        "numPaymentsIncomplete": 6,
        "numPaymentsWithRefunds": 2,
        "numCardPresentPayments": 45,
        "numCardNotPresentPayments": 17,
        "totalCapturedAmount": 7420.50,
        "totalRefundedAmount": 185.25,
        "totalNetCapturedAmount": 7235.25,
        "averageNetCapturedAmount": 116.70,
        "minNetCapturedAmount": 8.50,
        "maxNetCapturedAmount": 1250.00
      },
      {
        "intervalStart": "2025-11-29T00:00:00.000000Z",
        "intervalEnd": "2025-11-30T00:00:00.000000Z",
        "numPaymentsCreated": 71,
        "numPaymentsProcessed": 64,
        "numPaymentsIncomplete": 7,
        "numPaymentsWithRefunds": 1,
        "numCardPresentPayments": 48,
        "numCardNotPresentPayments": 16,
        "totalCapturedAmount": 7680.25,
        "totalRefundedAmount": 95.00,
        "totalNetCapturedAmount": 7585.25,
        "averageNetCapturedAmount": 118.52,
        "minNetCapturedAmount": 5.00,
        "maxNetCapturedAmount": 980.00
      }
      // ... 5 more daily intervals
    ]
  }
}
```

**Example: Successful Payments Only**

```bash
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/payments?forLast=30d&filter[successful]=true&filter[merchantId]=123456" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

**Example: Card Present vs Internet Payments**

```bash
# Card present only
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/payments?forLast=30d&filter[source]=CARD_PRESENT&filter[merchantId]=123456" \
  -H "Authorization: Bearer YOUR_API_KEY"

# Internet payments only
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/payments?forLast=30d&filter[source]=INTERNET&filter[merchantId]=123456" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

***

### Customer Analytics

Customer analytics help you track customer acquisition, retention, and activity patterns.

**Endpoint:** `GET /n1/organization/{organizationId}/analytics/customers`

**Common Filters:**

* `filter[merchantId]` - Filter by merchant ID(s) (required)
* `filter[channelPartnerId]` - Filter by channel partner ID (alternative to merchantId)

**Response Fields:**

_Period-Level Metrics (aggregate for entire period):_

* `activeCustomers` - Number of customers with at least one payment created in the period
* `newCustomers` - Number of new customers added in the period
* `returningCustomers` - Number of customers who were active in the period but were created before the period started

_Interval-Level Metrics:_
The `intervals` array contains the same metrics broken down by your chosen interval, plus `intervalStart` and `intervalEnd` timestamps.

**Example: Monthly Customer Growth**

```bash
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/customers?forLast=6mo&interval=month&filter[merchantId]=123456" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

Response:

```json
{
  "success": true,
  "statusCode": 200,
  "offset": null,
  "limit": null,
  "data": {
    "periodStart": "2025-06-05T00:00:00.000000Z",
    "periodEnd": "2025-12-05T00:00:00.000000Z",
    "activeCustomers": 1847,
    "newCustomers": 723,
    "returningCustomers": 1124,
    "interval": "month",
    "intervals": [
      {
        "intervalStart": "2025-06-05T00:00:00.000000Z",
        "intervalEnd": "2025-07-05T00:00:00.000000Z",
        "activeCustomers": 298,
        "newCustomers": 114,
        "returningCustomers": 184
      },
      {
        "intervalStart": "2025-07-05T00:00:00.000000Z",
        "intervalEnd": "2025-08-05T00:00:00.000000Z",
        "activeCustomers": 312,
        "newCustomers": 127,
        "returningCustomers": 185
      },
      {
        "intervalStart": "2025-08-05T00:00:00.000000Z",
        "intervalEnd": "2025-09-05T00:00:00.000000Z",
        "activeCustomers": 305,
        "newCustomers": 119,
        "returningCustomers": 186
      }
      // ... 3 more monthly intervals
    ]
  }
}
```

**Example: Daily Active Customers**

```bash
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/customers?forLast=7d&interval=day&filter[merchantId]=123456" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

***

### Transaction Analytics

Transaction analytics help you understand transaction types, operation patterns, and success rates across your payment processing.

**Endpoint:** `GET /n1/organization/{organizationId}/analytics/transactions`

**Common Filters:**

* `filter[source]` - Filter by transaction source
  * Values: `CARD_PRESENT`, `INTERNET`, `MAIL_ORDER`, `MERCHANT`, `MOTO`, `PAYER_PRESENT`, `SERVICE_PROVIDER`, `TELEPHONE_ORDER`, `VOICE_RESPONSE`
* `filter[merchantId]` - Filter by merchant ID(s) (required)
* `filter[channelPartnerId]` - Filter by channel partner ID (alternative to merchantId)

**Response Fields:**

_Period-Level Metrics (aggregate for entire period):_

By Operation Type:

* `totalTransactions` - Total number of all transactions
* `authorizeTransactions` - Number of authorization operations
* `captureTransactions` - Number of capture operations
* `payTransactions` - Number of direct payment operations
* `refundTransactions` - Number of refund operations
* `verifyTransactions` - Number of card verification operations
* `voidTransactions` - Number of void/cancel operations

By Result:

* `successfulTransactions` - Number of successful transactions
* `failedTransactions` - Number of failed transactions
* `pendingTransactions` - Number of pending transactions
* `unknownTransactions` - Number of transactions with unknown status

_Interval-Level Metrics:_
The `intervals` array contains the same metrics broken down by your chosen interval, plus `intervalStart` and `intervalEnd` timestamps.

**Example: Weekly Transaction Volume**

```bash
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/transactions?forLast=3mo&interval=week&filter[merchantId]=123456" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

Response:

```json
{
  "success": true,
  "statusCode": 200,
  "offset": null,
  "limit": null,
  "data": {
    "periodStart": "2025-09-05T00:00:00.000000Z",
    "periodEnd": "2025-12-05T00:00:00.000000Z",
    "totalTransactions": 2847,
    "authorizeTransactions": 742,
    "captureTransactions": 718,
    "payTransactions": 1124,
    "refundTransactions": 204,
    "verifyTransactions": 38,
    "voidTransactions": 21,
    "successfulTransactions": 2568,
    "failedTransactions": 257,
    "pendingTransactions": 18,
    "unknownTransactions": 4,
    "interval": "week",
    "intervals": [
      {
        "intervalStart": "2025-09-05T00:00:00.000000Z",
        "intervalEnd": "2025-09-12T00:00:00.000000Z",
        "totalTransactions": 218,
        "authorizeTransactions": 57,
        "captureTransactions": 55,
        "payTransactions": 86,
        "refundTransactions": 16,
        "verifyTransactions": 3,
        "voidTransactions": 1,
        "successfulTransactions": 196,
        "failedTransactions": 20,
        "pendingTransactions": 2,
        "unknownTransactions": 0
      },
      {
        "intervalStart": "2025-09-12T00:00:00.000000Z",
        "intervalEnd": "2025-09-19T00:00:00.000000Z",
        "totalTransactions": 224,
        "authorizeTransactions": 58,
        "captureTransactions": 56,
        "payTransactions": 88,
        "refundTransactions": 18,
        "verifyTransactions": 2,
        "voidTransactions": 2,
        "successfulTransactions": 202,
        "failedTransactions": 19,
        "pendingTransactions": 3,
        "unknownTransactions": 0
      }
      // ... more weekly intervals
    ]
  }
}
```

**Example: Card Present Transactions**

```bash
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/transactions?forLast=30d&filter[source]=CARD_PRESENT&filter[merchantId]=123456" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

**Example: Daily Transaction Success Rate**

```bash
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/transactions?forLast=7d&interval=day&filter[merchantId]=123456" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

You can calculate the success rate from the response:

```javascript
const successRate = (data.successfulTransactions / data.totalTransactions * 100).toFixed(2);
console.log(`Success rate: ${successRate}%`);
```

***

### Payout Analytics

Payout analytics help you understand settlement patterns, fee structures, and cash flow timing.

**Endpoint:** `GET /n1/organization/{organizationId}/analytics/payouts`

**Common Filters:**

* `filter[settlementDate]` - Filter by settlement date with operators
  * Examples: `=2025-05-29`, `>=2025-05-01`, `<=2025-05-31`
* `filter[depositDate]` - Filter by deposit date with operators
  * Examples: `>=2025-05-01`, `<2025-06-01`
* `filter[settlementDateBetween]` - Filter by settlement date range
  * Format: `2025-05-01,2025-05-31` (comma-separated dates)
* `filter[depositDateBetween]` - Filter by deposit date range
  * Format: `2025-05-01,2025-05-31` (comma-separated dates)
* `filter[merchantId]` - Filter by merchant ID(s) (required)
* `filter[channelPartnerId]` - Filter by channel partner ID (alternative to merchantId)

**Response Fields:**

_Period-Level Metrics (aggregate for entire period):_

* `totalSettlementAmount` - Total settled payout amount
* `totalFeeAmount` - Total amount of fees charged for payouts
* `totalNetAmount` - Total net payout amount (settlement - fees)
* `totalTransactionsPending` - Number of transactions pending settlement
* `totalTransactionsSettled` - Number of transactions that have been settled
* `totalTransactionsSettling` - Number of transactions currently in the settlement process

_Interval-Level Metrics:_
The `intervals` array contains the same metrics broken down by your chosen interval, plus `intervalStart` and `intervalEnd` timestamps.

**Example: Monthly Payout Summary**

```bash
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/payouts?forLast=6mo&interval=month&filter[merchantId]=123456" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

Response:

```json
{
  "success": true,
  "statusCode": 200,
  "offset": null,
  "limit": null,
  "data": {
    "periodStart": "2025-06-05T00:00:00.000000Z",
    "periodEnd": "2025-12-05T00:00:00.000000Z",
    "totalSettlementAmount": 294850.75,
    "totalFeeAmount": 7371.27,
    "totalNetAmount": 287479.48,
    "totalTransactionsPending": 87,
    "totalTransactionsSettled": 5412,
    "totalTransactionsSettling": 41,
    "interval": "month",
    "intervals": [
      {
        "intervalStart": "2025-06-05T00:00:00.000000Z",
        "intervalEnd": "2025-07-05T00:00:00.000000Z",
        "totalSettlementAmount": 48720.50,
        "totalFeeAmount": 1218.01,
        "totalNetAmount": 47502.49,
        "totalTransactionsPending": 14,
        "totalTransactionsSettled": 892,
        "totalTransactionsSettling": 7
      },
      {
        "intervalStart": "2025-07-05T00:00:00.000000Z",
        "intervalEnd": "2025-08-05T00:00:00.000000Z",
        "totalSettlementAmount": 51240.75,
        "totalFeeAmount": 1281.02,
        "totalNetAmount": 49959.73,
        "totalTransactionsPending": 16,
        "totalTransactionsSettled": 938,
        "totalTransactionsSettling": 8
      }
      // ... 4 more monthly intervals
    ]
  }
}
```

**Example: Payouts by Settlement Date Range**

```bash
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/payouts?filter[settlementDateBetween]=2025-05-01,2025-05-31&filter[merchantId]=123456" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

**Example: Recent Pending Settlements**

```bash
curl "https://api.prahsys.com/n1/organization/Z70B874W63DW/analytics/payouts?forLast=7d&interval=day&filter[merchantId]=123456" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

You can calculate fee percentage from the response:

```javascript
const feePercentage = (data.totalFeeAmount / data.totalSettlementAmount * 100).toFixed(2);
console.log(`Average fee: ${feePercentage}%`);
```

***

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

When using pagination, the `offset` and `limit` fields will reflect your query parameters:

```json
{
  "success": true,
  "statusCode": 200,
  "offset": 0,
  "limit": 100,
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

***

## Authentication

All analytics endpoints require authentication using your API key. Include your API key in the `Authorization` header as a Bearer token.

```bash
Authorization: Bearer YOUR_API_KEY
```

Make sure you're using the correct API key for your environment:

* Test environment: Keys starting with `sk_test_`
* Live environment: Keys starting with `sk_live_`

For more information on API keys and authentication, see our [API Keys documentation](https://docs.prahsys.com/docs/api-keys).

***

## Related Documentation

* [API Keys & Authentication](https://docs.prahsys.com/docs/api-keys) - Learn how to authenticate API requests
* [Pagination](https://docs.prahsys.com/docs/pagination) - Detailed guide on paginating through large datasets
* [Filtering and Sorting](https://docs.prahsys.com/docs/filtering-and-sorting) - Advanced filtering techniques for analytics queries