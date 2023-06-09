# SQL notes

## Join behaviour

### Left Join

Philosophy of a left join:

When you are doing a left join and in the ON clause you are putting conditions on the right table then you are basically
telling the query optimizer that instead of retrieving all teh rows from the right table retrieve only the rows that
match teh condition

- Based on the conditions defined in the ON clause determine whether to retrieve and if yes, what rows to retrieve from
  the right table.
- Retrieve everyting from the left table irrespective of the conditions specified on the left table in the ON clause. If
  a row in the left table does not match the conditions specifed on the left table in the ON clause then return null
  values for the right table for those rows.
- Conditions in the WHERE clause are applied after the join is completed following the above principles

Consider two tables table1 and table2

```text
table1
+----+---------+
| id1 | value1 |
+----+---------+
| 1  |    a    |
| 2  |    b    |
| 3  |    c    | 
+----+---------+

table2
+----+---------+
| id2 | value2 |
+----+---------+
| 1  |    x    |
| 2  |    y    |
| 3  |    z    | 
+----+---------+

table3
+----+---------+
| id3 | value3 |
+----+---------+
| 4  |    p    |
| 5  |    q    |
| 6  |    r    | 
+----+---------+

table4
+----+---------+
| id4 | value4 |
+----+---------+
| 3  |    o    |
| 4  |    p    |
| 5  |    q    | 
+----+---------+


```

Template for creating tables like above

```sql
CREATE TABLE tablen (
    idn INT PRIMARY KEY,
    valuen VARCHAR(50) NOT NULL
);

INSERT INTO tablen (idn, valuen) VALUES (1, 'a'), (2, 'b'), (3, 'c');
```

```sql
SELECT
    *
FROM
    table1
LEFT JOIN
    table2
    ON
        table1.id1 = table2.id2;
```

```text
 id1 | value1 | id2 | value2 
-----+--------+-----+--------
   1 | a      |   1 | x
   2 | b      |   2 | y
   3 | c      |   3 | z
```

-

```sql
SELECT
    *
FROM
    table1
LEFT JOIN
    table3
    ON
        table1.id1 = table3.id3;
```

```text
 id1 | value1 | id3 | value3 
-----+--------+-----+--------
   1 | a      |     | 
   2 | b      |     | 
   3 | c      |     | 
```

-

```sql
SELECT
    *
FROM
    table1
LEFT JOIN
    table4
    ON
        table1.id1 = table4.id4;
```

```text
 id1 | value1 | id4 | value4 
-----+--------+-----+--------
   1 | a      |     | 
   2 | b      |     | 
   3 | c      |   3 | o

```

-

retrive the rows of table2 when `table1.id1 = 4`

```sql
SELECT
    *
FROM
    table1
LEFT JOIN
    table2
    ON
        table1.id1 = 4;
```

```text
 id1 | value1 | id2 | value2 
-----+--------+-----+--------
   1 | a      |     | 
   2 | b      |     | 
   3 | c      |     | 
```

-

retrive the rows of table2 when `table1.id1 = 1`

```sql
SELECT
    *
FROM
    table1
LEFT JOIN
    table2
    ON
        table1.id1 = 1;
```

```text
 id1 | value1 | id2 | value2 
-----+--------+-----+--------
   1 | a      |   1 | x
   1 | a      |   2 | y
   1 | a      |   3 | z
   2 | b      |     | 
   3 | c      |     | 
```

Retrieve subset of the rows of table2 where `table2.id2 = 1` when `table1.id1 = 1`

```sql
SELECT
    *
FROM
    table1
LEFT JOIN
    table2
    ON
        table1.id1 = 1
    AND
        table2.id2 = 1;
```

```text
 id1 | value1 | id2 | value2 
-----+--------+-----+--------
   1 | a      |   1 | x
   2 | b      |     | 
   3 | c      |     | 
```

-

Retrieve subset of the rows of table2 where `table2.id2 = 2` when `table1.id1 = 1`

```sql
SELECT
    *
FROM
    table1
LEFT JOIN
    table2
    ON
        table1.id1 = 1
    AND
        table2.id2 = 2;
```

```text
 id1 | value1 | id2 | value2 
-----+--------+-----+--------
   1 | a      |   2 | y
   2 | b      |     | 
   3 | c      |     | 
```

No condition on table1 is quivalent of saying all rows from table1 match thee condition. We further filter this saing we
dont want all the rows from table2, just where the id is 1.

```sql
SELECT
    *
FROM
    table1
LEFT JOIN
    table2
    ON
        table2.id2 = 1;
```

```text
 id1 | value1 | id2 | value2 
-----+--------+-----+--------
   1 | a      |   1 | x
   2 | b      |   1 | x
   3 | c      |   1 | x
```

```sql
SELECT
    *
FROM
    table1
LEFT JOIN
    table2
    ON
        table1.id1 = 1
    OR
        table1.id1 = 2
    OR
        table1.id1 = 3
    ;
```

```text
 id1 | value1 | id2 | value2 
-----+--------+-----+--------
   1 | a      |   1 | x
   1 | a      |   2 | y
   1 | a      |   3 | z
   2 | b      |   1 | x
   2 | b      |   2 | y
   2 | b      |   3 | z
   3 | c      |   1 | x
   3 | c      |   2 | y
   3 | c      |   3 | z

```

### ON or WHERE

**In sql, Is there a performance benefit we get when we specify the additional conditions of the join in the ON clause
instead of a where clause?**

Yes, there can be a performance benefit when you specify additional conditions of a join in the ON clause instead of the
WHERE clause.

When you specify conditions in the WHERE clause, the database engine first performs the join operation and then applies
the filter conditions to the result set. This can result in the database engine having to perform the filter operation
on a larger result set, which can lead to slower performance.

On the other hand, when you specify conditions in the ON clause, the database engine applies the conditions as part of
the join operation. This can result in the database engine having to process fewer rows, which can lead to faster
performance.

Additionally, specifying conditions in the ON clause can also affect the results of the query. When you specify
conditions in the WHERE clause, it filters the result set after the join has been performed. In contrast, specifying
conditions in the ON clause can affect which rows are selected for the join operation in the first place.

Therefore, it is generally recommended to specify conditions in the ON clause whenever possible, as it can improve
performance and produce more accurate results.

**But I have heard that some database engines use information from the where clause to optimize the join**

Yes, that's correct. Some database engines can use information from the WHERE clause to optimize the join, especially
when the WHERE clause contains conditions that can narrow down the result set before the join is performed.

For example, if the WHERE clause contains a condition that filters out a large portion of the rows in one of the tables
being joined, the database engine can use this information to perform a more efficient join operation. In this case, it
may be more efficient to perform a nested loop join or a hash join rather than a more expensive sort-merge join.

However, even in cases where the WHERE clause contains conditions that can be used to optimize the join, it is still
generally better to include the join conditions in the ON clause whenever possible. This is because including the join
conditions in the ON clause makes the query more self-contained and easier to understand. It also ensures that the join
conditions are always applied consistently, regardless of the presence or absence of additional filter conditions in the
WHERE clause.




## left semi join

Yes, that's correct. One of the differences between a leftsemi join and a regular left join is that a leftsemi join does not duplicate rows from the left DataFrame that have multiple matching rows in the right DataFrame.

For example, consider the following code:

makefile
Copy code
from pyspark.sql.functions import col

```python
# Create two sample DataFrames with duplicates
df1 = spark.createDataFrame([(1, "A"), (2, "B"), (3, "C"), (3, "D")], ["id", "value"])
df2 = spark.createDataFrame([(2, "X"), (3, "Y"), (4, "Z"), (4, "W")], ["id", "value"])

# Perform a left join and show the result
left_join_df = df1.join(df2, on="id", how="left")
left_join_df.show()
```

The output of this code will be:

```text
+---+-----+-----+
| id|value|value|
+---+-----+-----+
|  1|    A| null|
|  2|    B|    X|
|  3|    C|    Y|
|  3|    C|    Y|
|  3|    D|    Y|
|  3|    D|    Y|
+---+-----+-----+
```

As you can see, the resulting DataFrame (left_join_df) includes duplicates in the rows where the left DataFrame (df1) has multiple matching rows in the right DataFrame (df2).

Now consider the following code using a leftsemi join:

```python
# Create two sample DataFrames with duplicates
leftsemi_join_df = df1.join(df2, on="id", how="leftsemi")
leftsemi_join_df.show()
```

The output of this code will be:

```text
+---+-----+
| id|value|
+---+-----+
|  2|    B|
|  3|    C|
+---+-----+
```
As you can see, the resulting DataFrame (leftsemi_join_df) does not include any duplicates, even though the left DataFrame (df1) has multiple matching rows in the right DataFrame (df2).


## difference between left outer join and left join

Sure, here's an example that illustrates the difference between "left join" and "left outer join" when there are unmatched records in the left DataFrame:

```text
df1:

+----+-------+
| id | value |
+----+-------+
| 1  |  100  |
| 4  |  400  |
+----+-------+

df2:
+----+-------+
| id | value |
+----+-------+
| 1  |  100  |
| 4  |  400  |
+----+-------+
```

If we do a "left join" on "id":

```python
df_join = df1.join(df2, on=["id"], how="left")
```

The result will be:

```text
+----+-------+------+
| id | value |value |
+----+-------+------+
| 1  |  10   | 100  |
| 2  |  20   | null |
| 3  |  30   | null |
+----+-------+------+
```

As you can see, all the records from the left DataFrame are returned, and matching records from the right DataFrame are also included. For the unmatched records from the left DataFrame (id=2 and id=3), null values are added.

If we do a "left outer join" on "id":

```python
df_join = df1.join(df2, on=["id"], how="left_outer")
```
The result will be:

```text
+----+-------+------+
| id | value |value |
+----+-------+------+
| 1  |  10   | 100  |
| 2  |  20   | null |
| 3  |  30   | null |
| 4  |  null | 400  |
+----+-------+------+
```

As you can see, "left outer join" returns all the records from the left DataFrame and matching records from the right DataFrame, as well as all the records from the left DataFrame that do not have a matching record in the right DataFrame. In this case, the record from the right DataFrame with id=4 is included, along with a null value for the "value" column from the left DataFrame.