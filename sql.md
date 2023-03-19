# SQL notes

## Left join conditions

In a LEFT JOIN, you can apply a condition on the right side column by including that condition in the ON clause of the
JOIN statement. The ON clause is used to specify the conditions that must be satisfied for a row to be included in the
result set of the JOIN operation.

Here's an example of a LEFT JOIN with a condition on the right side column:

```sql
SELECT
    *
FROM
    table1
LEFT JOIN
    table2
    ON
        table1.column1 = table2.column1
        AND
        table2.column2 = 'some_value'
```

In this example, the condition table2.column2 = 'some_value' is applied on the right side column column2. This condition
is included in the ON clause along with the condition that specifies the join condition between table1 and table2.

Note that if the condition applies only to the right side table, it's equivalent to a WHERE clause. So, you could also
write:

```sql
SELECT
    *
FROM
    table1
LEFT JOIN 
    table2
    ON
        table1.column1 = table2.column1
WHERE
    table2.column2 = 'some_value'
```

This query would give you the same result as the previous one, but it's not the same as the LEFT JOIN with a condition
in the ON clause. The main difference is that in the latter case, all rows from table1 are returned, even if there is no
match in table2. The rows from table2 that don't satisfy the condition are included in the result set with NULL values
for the columns of table2. In the former case, rows from table2 that don't satisfy the condition are not included in the
result set at all.
