# SORTING & ORDERING

## ORDER()

This is used to sort the set based on a measure value.

For example :

If we want to sort the countries based on the `Sales Amount` in ascending or, descending order then, we can use the order set.

The syntax for doing so is :

> ORDER (set/attribute to sort, sort by measure, asc/desc)

Let's see how we can sort the countries in descending order of `Sales Amount`

```mdx
SELECT
[Measures].[Sales Amount] ON COLUMNS,

ORDER
(
	[Dim Sales Territory].[Sales Territory Country].[Sales Territory Country],
	[Measures].[Sales Amount],
	DESC
) ON ROWS

FROM
[AdventureWorksDW]
```
Similarly, to sort the countries by ascending order of reseller sales amount :

```MDX
SELECT
[Measures].[Sales Amount] ON COLUMNS,

ORDER
(
	[Dim Sales Territory].[Sales Territory Country].[Sales Territory Country],
	[Measures].[Sales Amount],
	ASC
) ON ROWS

FROM
[AdventureWorksDW]
```

### `ORDER()` With `HEAD()`
---

To see only the top 5 countries based on `Sales Amount` we can use the `HEAD()` function with `ORDER()`, as follows :

```mdx
SELECT
[Measures].[Sales Amount] ON COLUMNS,

HEAD
(
	ORDER
	(
		[Dim Sales Territory].[Sales Territory Country].[Sales Territory Country],
		[Measures].[Sales Amount],
		DESC
	), 5
) ON ROWS

FROM
[AdventureWorksDW]
```
### `ORDER()` With `TAIL()`
---
To see only the bottom 5 countries based on `Sales Amount` we can use the `TAIL()` function with `ORDER()`, as follows :

```MDX
SELECT
[Measures].[Sales Amount] ON COLUMNS,

TAIL
(
	ORDER
	(
		[Dim Sales Territory].[Sales Territory Country].[Sales Territory Country],
		[Measures].[Sales Amount],
		DESC
	), 5
) ON ROWS

FROM
[AdventureWorksDW]
```

## TOPCOUNT() & BOTTOMCOUNT()
---

Instead of using `HEAD()` and `ORDER()` togather to get the top 5 counties by a measure, we can use a single function, called `TOPCOUNT()`.

The syntax of the function is as follows :

> TOPCOUNT(set/attribute to sort, n , sort by measure)

Let's see how we can get top 3 countries by a measure using `TOPCOUNT()` :

```mdx
SELECT
[Measures].[Sales Amount] ON COLUMNS,

TOPCOUNT
(
	[Dim Sales Territory].[Sales Territory Country].[Sales Territory Country],
	3,
	[Measures].[Sales Amount]
) ON ROWS

FROM
[AdventureWorksDW]
```
Similarly, we can use the `BOTTOMCOUNT()` function to get bottom 3 countries as follows :

```MDX
SELECT
[Measures].[Sales Amount] ON COLUMNS,

BOTTOMCOUNT
(
	[Dim Sales Territory].[Sales Territory Country].[Sales Territory Country],
	3,
	[Measures].[Sales Amount]
) ON ROWS

FROM
[AdventureWorksDW]
```

We can also optimize (reducing code complexity and making it more readable) the code by, using the `TOPCOUNT()` function in a named set as follows :

```mdx
WITH SET [Top3Countries] AS
TOPCOUNT
(
	[Dim Sales Territory].[Sales Territory Country].[Sales Territory Country],
	3,
	[Measures].[Sales Amount]
)

SELECT
[Measures].[Sales Amount] ON COLUMNS,
[Top3Countries] ON ROWS

FROM
[AdventureWorksDW]
```
