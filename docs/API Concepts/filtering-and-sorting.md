---
title: Filtering and Sorting
excerpt: >-
  Prahsys APIs provide powerful filtering and sorting capabilities to help you
  efficiently manage and retrieve data. These features work consistently across
  our list endpoints, making it easy to find exactly what you need.
deprecated: false
hidden: false
icon: far fa-filter-list
metadata:
  title: Filtering & Sorting | Prahsys Documentation
  description: >-
    Learn how to filter and sort API results with Prahsys. This guide covers
    filter operators, sort options, and examples to effectively manage large
    data sets in your API requests.
  keywords:
    - API filtering
    - result filtering
    - data sorting
    - API query parameters
    - filter operators
    - sort order
    - advanced filtering
    - sorting API results
    - data management
    - query filters
  robots: index
---
## Filtering

Prahsys APIs support flexible filtering options to narrow down results based on specific criteria.

### Basic Filtering

To filter results, use the `filter` parameter in your query string:

```bash /filter/
curl -X GET "https://api.prahsys.com/payments/n1/merchant/{merchantId}/payments?filter[status]=COMPLETED" \
-H "Authorization: Bearer $PRAHSYS_API_KEY"
```

### Multiple Filters

Apply multiple filters by adding additional filter parameters:

```bash /filter/
curl -X GET "https://api.prahsys.com/payments/n1/merchant/{merchantId}/payments?filter[status]=COMPLETED&filter[amount]=1000" \
-H "Authorization: Bearer $PRAHSYS_API_KEY"
```

### Multiple Values for a Single Filter

Use comma-separated values to match multiple possibilities:

```bash
curl -X GET "https://api.prahsys.com/payments/n1/merchant/{merchantId}/payments?filter[status]=COMPLETED,PENDING" \
-H "Authorization: Bearer $PRAHSYS_API_KEY"
```

This returns payments with status **COMPLETED** OR **PENDING**.

### Automatic Type Conversion

The query builder automatically maps certain values:

* `1`, `0`, `true`, and `false` are converted to `boolean` values
* Comma-separated lists are converted to arrays for IN queries

```shell Boolean conversion
curl -X GET "https://api.prahsys.com/payments/n1/merchant/{merchantId}/payments?filter[active]=true" \ -H "Authorization: Bearer $PRAHSYS_API_KEY" 
```
```shell Where In
curl -X GET "https://api.prahsys.com/payments/n1/merchant/{merchantId}/payments?filter[status]=COMPLETED,PENDING,PROCESSING" \ -H "Authorization: Bearer $PRAHSYS_API_KEY" 
```

### Filtering Example

```bash title="Advanced filtering" /filter[amount]=>1000/ /filter[status]=COMPLETED,PENDING/
curl -X GET "https://api.prahsys.com/payments/n1/merchant/{merchantId}/payments?filter[amount]=>1000&filter[status]=COMPLETED,PENDING" \
-H "Authorization: Bearer $PRAHSYS_API_KEY"
```

> Refer to specific API endpoint documentation to see which fields support filtering. Not all fields are filterable on every endpoint.

## Sorting

Control the order of returned results using the `sort` parameter.

### Basic Sorting

All sorting defaults to ascending order.

```shell Basic sort
curl -X GET "https://api.prahsys.com/payments/n1/merchant/{merchantId}/payments?sort=createdAt" \
-H "Authorization: Bearer $PRAHSYS_API_KEY"
```

### Descending Order

Prefix the field name with a minus sign (**`-`**) to sort in descending order:

```bash Descending sort
curl -X GET "https://api.prahsys.com/payments/n1/merchant/{merchantId}/payments?sort=-createdAt" \
-H "Authorization: Bearer $PRAHSYS_API_KEY"
```

### Multiple Sort Fields

Sort by multiple fields using a comma-separated list:

```bash Multiple sort fields
curl -X GET "https://api.prahsys.com/payments/n1/merchant/{merchantId}/payments?sort=-createdAt,status" \ -H "Authorization: Bearer $PRAHSYS_API_KEY"
```

This example sorts payments by creation date (newest first), and then by status (alphabetically) for records with the same creation date.

### Sort Direction

* **Ascending order**: Use the field name without a prefix (`sort=createdAt`)
* **Descending order**: Add a minus sign before the field name (`sort=-createdAt`)

> Refer to specific API endpoint documentation to see which fields support sorting. Not all fields are sortable on every endpoint.

## Combining Filtering and Sorting

You can combine filtering and sorting in a single request:

```bash title="Combined request" /filter[status]=COMPLETED/ /sort=-amount/
curl -X GET "https://api.prahsys.com/payments/n1/merchant/{merchantId}/payments?filter[status]=COMPLETED&sort=-amount" \
-H "Authorization: Bearer $PRAHSYS_API_KEY"
```

This request:

1. Includes only payments with a status of "**COMPLETED**"
2. Sorts results by amount in descending order (highest first)

## Implementation Examples

<br />

```javascript Filtering & Sorting
// Fetch completed payments, sorted by amount (highest first)
async function fetchPayments() {
  const response = await fetch(
    "https://api.prahsys.com/payments/n1/merchant/{merchantId}/payments?filter[status]=COMPLETED&sort=-amount",
    {
      method: "GET",
      headers: {
        "Authorization": `Bearer ${apiKey}`,
        "Content-Type": "application/json"
      }
    }
  );

  const data = await response.json();
  return data.data;
}
```

## Best Practices

* **Use specific filters** to reduce the dataset size
* **Combine multiple filters** for more precise results
* **Sort by indexed fields** for better performance on large datasets
* **Use filtering before sorting** to improve query efficiency

For additional information on specific filterable or sortable fields, refer to the documentation for each API endpoint.