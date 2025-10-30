---
title: Pagination
excerpt: >-
  For endpoints that return collections of resources, Prahsys uses offset-based
  pagination to limit the number of objects returned in a single response. This
  approach helps optimize performance and manage large datasets effectively.
deprecated: false
hidden: false
metadata:
  title: Pagination | Prahsys Documentation
  description: >-
    Learn how to paginate API results with Prahsys. This guide covers pagination
    parameters, implementation, and best practices for handling large data sets
  keywords:
    - API pagination
    - offset pagination
    - limit results
    - API query parameters
    - result batching
    - page navigation
    - large result sets
    - API result management
    - data retrieval
    - pagination parametersRetry
  robots: index
---
## Pagination Parameters

The following properties control pagination:

```json
{
  "offset": 20, // Number of records to skip
  "limit": 10, // Maximum number of records per page
  "total": 100 // Total number of records available
}
```

### Understanding Pagination Parameters

#### Offset:

Specifies the starting index of the first record to return. Use this to skip records you've already received.

```shell
Records: [0][1][2][3][4][5][6][7][8][9][10][11][12]...
                   ^
                   |
             offset=3 starts here

Result:           [3][4][5][6][7]...  (returning records starting from index 3)


For pagination example:
-----------------------
Page 1: offset=0, limit=5  → [0][1][2][3][4]
Page 2: offset=5, limit=5  → [5][6][7][8][9]
Page 3: offset=10, limit=5 → [10][11][12][13][14]
```

#### Limit

Controls how many records are returned in a single response. This helps manage response size and load time.

```shell
Records: [0][1][2][3][4][5][6][7][8][9][10][11][12]...
                   ^        ^
                   |        |
             offset=3       |
                     limit=4 (return 4 records)

Result:           [3][4][5][6]  (only these 4 records are returned)
```

#### Total

Indicates the total number of records available that match your query. Use this to calculate total pages and implement pagination controls.

```shell
All matching records (total=15):
  [0][1][2][3][4][5][6][7][8][9][10][11][12][13][14]
|←------- Your query matched 15 total records ------→|

Pagination calculation:
Total pages = Math.ceil(total ÷ limit) = Math.ceil(15 ÷ 5) = 3 pages

Page 1: offset=0,  limit=5  → [0][1][2][3][4]
Page 2: offset=5,  limit=5  → [5][6][7][8][9]
Page 3: offset=10, limit=5  → [10][11][12][13][14]
```

## Using Pagination in Requests

Apply pagination in your API requests using query parameters:

```shell Request with pagination
curl -X GET "https://api.prahsys.com/payments/n1/merchant/{merchantId}/payments?offset=20&limit=10" \ 
-H "Authorization: Bearer $PRAHSYS_API_KEY"
```

## Response Format

Paginated responses include pagination metadata alongside the results:

```json Paginated response
{
  "success": true,
  "message": "Payments retrieved successfully",
  "data": [
    // Array of payment records
  ],
  "offset": 20,
  "limit": 10,
  "total": 100
}
```

## Implementing Pagination

### Calculating Pages

To implement pagination in your application, you can use the pagination metadata to calculate:

* **Current page**: `Math.floor(offset / limit) + 1`
* **Total pages**: `Math.ceil(total / limit)`
* **Next page offset**: `offset + limit` (if current page \< total pages)
* **Previous page offset**: `Math.max(0, offset - limit)` (if current page > 1)

## Best Practices

* **Set reasonable page sizes**: The default `limit` is typically 10 if not specified. The maximum allowed `limit` is 100 per request.
* **Cache results when appropriate**: To reduce the number of API calls, consider caching results when the data doesn't change frequently.
* **Implement pagination controls**: Provide users with controls to navigate between pages (first, previous, next, last).
* **Combine with filtering**: Use filtering to reduce the dataset size before applying pagination for better performance.
* **Handle empty results**: When `total` is 0, no records match your query. Display an appropriate message to users.
* **Implement graceful error handling**: Handle cases where pagination parameters might be invalid.

## Combining with Filtering and Sorting

For more complex data retrieval, you can combine pagination with [filtering and sorting](https://new-docs.prahsys.com/docs/filtering-and-sorting#/):

```bash title="Combined pagination, filtering and sorting"
curl -X GET "https://api.prahsys.com/payments/n1/merchant/{merchantId}/payments?offset=20&limit=10&filter[status]=COMPLETED&sort=-amount" \
-H "Authorization: Bearer $PRAHSYS_API_KEY"
```

This request:

1. Returns the third page of results (**items 20-29**)
2. Includes only payments with a status of **COMPLETED**
3. Sorts results by amount in **descending** order (**highest first**)

For more information on combining these features, refer to the [Filtering & Sorting](https://new-docs.prahsys.com/docs/filtering-and-sorting#/) documentation.
