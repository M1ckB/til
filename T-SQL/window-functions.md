# Window Functions

## Introduction

Windows functions are based on the idea of *windowing* which allow various calculations over a set of rows, a *window*, and return a single result. They help express calculations intuitively and efficiently.

They are mainly analytical functions and can help solve different querying tasks, e.g.

- Paging
- De-duplicating data
- Returning top *n* rows per group
- Computing running totals
- Identifying gaps and islands
- Sorting hierarchies
- Computing recency

Window functions are *supported* and *evaluated* as part of either the `SELECT` clause or the `ORDER BY` clause.

## Elements of Window Functions

The specification of a window functions happens in the function's `OVER` clause. The `OVER` clause has multiple elements:

- Partitioning
- Ordering
- Framing

An example of a window function that uses all elements is shown below:

```sql
SELECT
    CustomerId,
    OrderDate,
    OrderId,
    Amount
    SUM(Amount)
        OVER(
            /* Partitioning */
            PARTITION BY CustomerId 
            /* Ordering */
            ORDER BY OrderDate, OrderId
            /* Framing */
            ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
        ) AS RunningAmount
FROM Sales.Orders;
```

### Window Partitioning

The optional partitioning element is implemented using the `PARTITION BY` clause and is supported by all window functions.

The clause restricts the window of the current calculation to only those rows from the result set of the query that have the same values in the partitioning columns as in the current row. If the clause is not specified, the window is not restricted.

### Window Ordering

The window ordering defines the ordering, if relevant, for the calculation within the partition.

The element has different meaning depending on which type function is used with it. Using ranking functions, e.g. `RANK` and `ROW_NUMBER`, ordering is intuitive. Using aggregate functions, e.g. `SUM` and `COUNT`, the ordering gives meaning to the framing options.

### Window Framing

Window framing is, essentially, another filter that further restricts the rows in a window partition. It works as two end points in the current row's partition based on the given ordering, framing the rows that a calculation will apply to.

It is applicable to aggregate functions as well as some of the offset functions: `FIRST_VALUE` and `LAST_VALUE`.

The framing specification includes `ROWS` and `RANGE` that defines the starting and ending point of a frame:

- The `ROWS` option indicate the points in the frame as an offset in terms of the number of rows with respect to the current row, based on the window ordering
- The `RANGE` option defines the offsets in terms of the difference between the ordering value of the frame point and the current row's ordering value.

## Read More

Read more in Itzitk Ben-Gan's book *T-SQL Window Functions*.
