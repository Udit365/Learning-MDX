# FILTERING IN MDX

There are certain clauses in MDX, using which we can filter our result set. They are :

1. `HAVING` Clause
1. `WHERE` Clause
1. `FILTER` Clause

### `HAVING` & `WHERE` CLAUSE


Although both of these clauses are used to filter the data but,some basic differences between the `HAVING` and `WHERE` clauses are as follows :

|HAVING|WHERE|
|----|----|
|Filter is applied on the result set |Filter is diectly applied on the cube</br>along with the MDX code|
|MDX code executed first to generate</br>the result set and then `HAVING` caluse</br>is applied|The resultset is filtered with the `WHERE`</br> clause during execution of MDX code|
|As the `HAVING` clause is applied on the result set,</br> therefore, only conditions related to the attributes</br> present is rows or, columns can be provided|We can't use the attributes used in rows/columns</br> into the `WHERE` condition because, `WHERE` acts like</br> an axis and in MDX same attribute can't be used in multiple axis.|

#### `HAVING` CLAUSE

In the below example, the `HAVNG` clause is applied on the rows section and hence, the query will check for the `TRUE` condition row by row and after that it will execute only those rows that satisfies the `HAVING` condition.

```mdx
SELECT
[Measures].[Sales Amount]
ON COLUMNS,
[Sales Territory].[Sales Territory Country].[Sales Territory Country]
HAVING
[Sales Territory].[Sales Territory Country] = [Sales Territory].[Sales Territory Country].&[Canada]
ON ROWS

FROM
[Adventure Works]
```

we can use `OR` operator to specify multiple filter conditions, as follows :

```mdx
SELECT
[Measures].[Sales Amount]
ON COLUMNS,
[Sales Territory].[Sales Territory Country].[Sales Territory Country]
HAVING
[Sales Territory].[Sales Territory Country] = [Sales Territory].[Sales Territory Country].&[Canada]
OR
[Sales Territory].[Sales Territory Country] = [Sales Territory].[Sales Territory Country].&[Germany]
ON ROWS

FROM
[Adventure Works]
```
If we put `TRUE` with `HAVING` clause then, each record of the result set will be executed :

```mdx
SELECT
[Measures].[Sales Amount]
ON COLUMNS,
[Sales Territory].[Sales Territory Country].[Sales Territory Country]
HAVING TRUE ON ROWS

FROM
[Adventure Works]
```

similarly, if we put `FALSE` with `HAVING` clause then, no record from the result set will be executed :

```mdx
SELECT
[Measures].[Sales Amount]
ON COLUMNS,
[Sales Territory].[Sales Territory Country].[Sales Territory Country]
HAVING FALSE ON ROWS

FROM
[Adventure Works]
```

Such behaviour of `HAVING` clause shows that it works row by row on the result set checking for the given condition and then executes only those rows that satisfied the given condition.

##### MORE QUERIES
---

1. *Display the countries having `Sales Amount` greater than 5 million*

```mdx
SELECT
[Measures].[Sales Amount]
ON COLUMNS,
[Sales Territory].[Sales Territory Country].[Sales Territory Country]
HAVING
[Measures].[Sales Amount]>5000000
ON ROWS

FROM
[Adventure Works]
```
2. *Display the countries having `Sales Amount` between 1 to 5 million*

```mdx
SELECT
[Measures].[Sales Amount]
ON COLUMNS,
[Sales Territory].[Sales Territory Country].[Sales Territory Country]
HAVING
[Measures].[Sales Amount]>1000000 AND [Measures].[Sales Amount]<5000000
ON ROWS

FROM
[Adventure Works]
```
##### `HAVING` CLAUSE WITH `INSTR()`
---

Suppose, we want to view the records for the countries whose name starts with "U".

Then, to do so, we have to use a function named `INSTR()` as follows :

```mdx
SELECT
[Measures].[Sales Amount]
ON COLUMNS,
[Sales Territory].[Sales Territory Country].[Sales Territory Country]
HAVING
INSTR([Sales Territory].[Sales Territory Country].CURRENTMEMBER.PROPERTIES('MEMBER_CAPTION'),'U')=1
ON ROWS

FROM
[Adventure Works]
```
In the above code, the LHS side (`INSTR()` function) of the `HAVING` clause will provide `1` if the condition will be satisfied which will then be equated with the `1` in RHS to provide the desired rows.

The `INSTR()` function takes two argument :

1. In the first argument it chooses the first letter of the current row memeber
1. In the second argument, a condition is provided with which the `INSTR()` function compares the output from the first argument,i.e., the first letter of the current member.

The output of this function is either `1` or, `0`.

So, if we want all the countries whose name doesn't starts with "U" then, we just have to put `0` in the RHS side of the `HAVING` clause.

#### `WHERE` CLAUSE


Let's say we want to see the `Sales Amount` of all the countries only for the orders that are shipped in `2012` then, we can't use `HAVING` clause because, the filtering condition is outside of the attributes used in rows/columns.

In such situations, we have to use the `WHERE` condition as follows :

```mdx
SELECT
[Measures].[Sales Amount]
ON COLUMNS,
[Sales Territory].[Sales Territory Country].[Sales Territory Country]
ON ROWS

FROM
[Adventure Works]
WHERE
[Ship Date].[Calendar Year].&[2012]
```
##### MORE QUERIES
---

1. *Find the `Sales Amount` by country for the years except `2012`*

```mdx
SELECT
[Measures].[Sales Amount]
ON COLUMNS,
[Sales Territory].[Sales Territory Country].[Sales Territory Country]
ON ROWS

FROM
[Adventure Works]
WHERE
[Ship Date].[Calendar Year].[Calendar Year]-
[Ship Date].[Calendar Year].&[2012]
```
##### `WHERE` AS AN AXIS
---

The `WHERE` clause can also be used as an axis.

For example :

If wE want to see *Sales Amount* by *Year* (in columns) and *Country* (in rows) then, we can provide the *Sales Amount* in the `WHERE` clause as well :

```mdx
SELECT
NON EMPTY
[Ship Date].[Calendar Year].[Calendar Year]
ON COLUMNS,

NON EMPTY
[Sales Territory].[Sales Territory Country].[Sales Territory Country]
ON ROWS

FROM
[Adventure Works]
WHERE
[Measures].[Sales Amount]
```
The `NON EMPTY` keyword will remove the empty rows/columns from the result set.

***NOTES :***
> We can't use the attributes used in rows/columns into the `WHERE` condition because, `WHERE` acts like an axis and in MDX same attribute can't be used in multiple axis.

#### `FILTER` CLAUSE

- `Filter` clause works exactly like the `HAVING` clause.
- `HAVING` clause was introduced in SSAS 2005 and before that, people were using the `FILTER` clause only.
- The syntax of `FILTER` clause is complex as compared to the `HAVING` clause.

The syntax for `FILTER` clause is :
>FILTER( «Set», «Search Condition» )

***Examples :***

*1. To show "Sales Amount" for Canada using FILTER clause*

```MDX
SELECT [Measures].[Sales Amount] ON COLUMNS,

FILTER
(
[Dim Sales Territory].[Sales Territory Country].[Sales Territory Country],
[Dim Sales Territory].[Sales Territory Country].CURRENTMEMBER.NAME = 'Canada'
) ON ROWS

FROM
[AdventureWorksDW]
```

*2. To show "Sales Amount" for Canada, United States using FILTER clause*

```MDX
SELECT [Measures].[Sales Amount] ON COLUMNS,

FILTER
(
[Dim Sales Territory].[Sales Territory Country].[Sales Territory Country],

[Dim Sales Territory].[Sales Territory Country].CURRENTMEMBER.NAME = 'Canada'
OR
[Dim Sales Territory].[Sales Territory Country].CURRENTMEMBER.NAME = 'United States'
) ON ROWS

FROM
[AdventureWorksDW]
```

*3. Display the "Sales Amount" for countries whose name starts with letter "U" using FILTER Clause*

```MDX
SELECT [Measures].[Sales Amount] ON COLUMNS,

FILTER
(
[Dim Sales Territory].[Sales Territory Country].[Sales Territory Country],
INSTR([Dim Sales Territory].[Sales Territory Country].CURRENTMEMBER.PROPERTIES('MEMBER_CAPTION'),'U') = 1
) ON ROWS

FROM
[AdventureWorksDW]
```
