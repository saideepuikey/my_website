# Stop using CTEs just to filter window functions

If you have ever wrapped a window-function query in a CTE just to filter the final result, you are not alone. It is a common pattern when you want the top row per group, the latest record, or the highest value in a category.

But there is a cleaner approach.

## Meet QUALIFY

QUALIFY is a SQL feature that lets you filter the output of window functions directly. It works like a companion to WHERE and HAVING, but instead of filtering rows or grouped data, it filters the result of functions such as ROW_NUMBER(), RANK(), DENSE_RANK(), and SUM() over a window.

That means you can skip the extra subquery or CTE in many cases.

### Example

Here is the traditional approach:

```sql
WITH RankedData AS (
    SELECT *,
           ROW_NUMBER() OVER (
               PARTITION BY department
               ORDER BY salary DESC
           ) AS rn
    FROM employees
)
SELECT *
FROM RankedData
WHERE rn = 1;
```

The same logic becomes much simpler with QUALIFY:

```sql
SELECT *
FROM employees
QUALIFY ROW_NUMBER() OVER (
    PARTITION BY department
    ORDER BY salary DESC
) = 1;
```

## Why it is useful

QUALIFY can make your SQL easier to read and maintain because it removes unnecessary nesting. Some of the biggest benefits are:

- Fewer layers of logic
- Less boilerplate code
- Easier debugging and review
- Cleaner intent when the filter is directly tied to a window function

## One important limitation

QUALIFY is not part of standard ANSI SQL. It is supported natively in platforms such as Snowflake, BigQuery, and Teradata. If you are using PostgreSQL or SQL Server, you will still need to rely on the traditional CTE or subquery pattern.

## Final thought

If you are already using window functions in your data pipelines, QUALIFY is worth exploring. It can reduce clutter, make your SQL more expressive, and help your team focus on the business logic instead of the wrapper code.

#SQL #DataEngineering #Analytics #Databases #Snowflake #BigQuery
