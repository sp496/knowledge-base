# SQL notes

## Join behaviour

### Left Join

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
-
retrive everyting from the left table irrespective of the contitions specified on teh left table in the ON clause.
The ON clause determines what will be retrieved from the right table.
so in the below example it would be, retrive the rows of table2 when `table1.id1 = table2.id2`


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

