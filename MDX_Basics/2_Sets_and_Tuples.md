# SETS & TUPLES

Some of the key differences between sets and tuples are :

|SETS|TUPLES|
|----|----|
|Same attribute|Different attribute|
|Enclosed withing `{}`|Enclosed withing `()`|
|Performs **Union** opration|Performs **Crossjoin** operation|

### TUPLES

In the below example, *Country* and *Calender Year* belong to different attribute and hence treated as a tuple.

```mdx
SELECT
[Measures].[Sales Amount] ON COLUMNS,
(
	[Geography].[Country].&[Australia],
	[Ship Date].[Calendar Year].[Calendar Year]
) ON ROWS
FROM
[Adventure Works]
```
Similarly, we can also make sets for the column headers as well :
```mdx
SELECT
(
	[Ship Date].[Calendar Year].[Calendar Year],
	[Measures].[Sales Amount]
)
ON COLUMNS,
[Sales Territory].[Sales Territory Country].[Sales Territory Country]
ON ROWS

FROM
[Adventure Works]
```

### SETS

In the below example, both *Canada* and *Australia* belong to the same attribute, i.e., `[Geography].[Country]` and hence treated as a set.
```mdx
SELECT
[Measures].[Sales Amount] ON COLUMNS,
{
	[Geography].[Country].&[Australia],
	[Geography].[Country].&[Germany]
} ON ROWS
FROM
[Adventure Works]
```
Similarly, we can also make sets for the column headers as well :

```mdx
SELECT
{
	[Measures].[Sales Amount],
	[Measures].[Internet Sales Amount]
}
ON COLUMNS,
[Sales Territory].[Sales Territory Country].[Sales Territory Country]
ON ROWS

FROM
[Adventure Works]
```

### MIXING SETS & TUPLES


In the below example; *Canada*, *Germany* and *United Kingdom* belong to the `[Sales Territory].[Sales Territory Country]` attribute and hence treated as a set, whereas, *Europe* belongs to a different attribute, i.e., `[Sales Territory].[Sales Territory Group]` and hence, we have to treat the combination of 3 countries and *Europe* as a tuple.

```mdx
SELECT
[Measures].[Sales Amount] ON COLUMNS,
(
	{
		[Sales Territory].[Sales Territory Country].&[Canada],
		[Sales Territory].[Sales Territory Country].&[Germany],
		[Sales Territory].[Sales Territory Country].&[United Kingdom]
	},
	[Sales Territory].[Sales Territory Group].&[Europe]
)
ON ROWS

FROM
[Adventure Works]
```

**Union** operation happened between the set of countries and then, we have **crossjoin** between the set of countries and *Europe*.

Because, **crossjoin** occur within the members of a set, therefore, in the result, we will only get a two rows, as follows :

| | |Sales Amount|
|--|--|--|
|Germany|Europe|$4,878,300.38|
|United Kingdom|Europe|$7,670,721.04|

we will not get *Canada* because there is no combination exists between *Europe* and *Canada* as, *Canada* belongs to *North America* group.

### REMOVING NULL/BLANK VALUES

For removing the null values from rows/columns, we need to enclose the sets/tuples within `NONEMPTY()`.

For example :

```mdx
SELECT
NONEMPTY
(
	(
		[Ship Date].[Calendar Year].[Calendar Year],
		[Measures].[Sales Amount]
	)
)
ON COLUMNS,

NONEMPTY
(
[Sales Territory].[Sales Territory Country].[Sales Territory Country]
)
ON ROWS

FROM
[Adventure Works]
```
### USING EMPTY SET


If we just want to see the list of countries then, we can pass an empty set, i.e., `{}` in columns as follows :

```mdx
SELECT
{}
ON COLUMNS,
[Sales Territory].[Sales Territory Country].[Sales Territory Country]
ON ROWS

FROM
[Adventure Works]
```
If we put `()` instead of `{}` then, we will get an error because, empty tuple is not allowed in MDX.

>**Notes :**
    1. *Empty set is allowed but, empty tuple is not allowed*
    2. *If there is no comma separated values in rows/columns, i.e., we have a single object then, we it will work perfectly even if we treat it as a set (enclosed within curly braces) or, tuple (enclosed within normal braces) or, left it without enclosng it within any kind of braces.*

### Handling Hierarchies


In a geography hierarchy both, *Group* and *Country* belongs to the different levels of same attribute hierarchy and therefore, we have to put them togather as a set :

```mdx
SELECT
[Measures].[Sales Amount]
ON COLUMNS,
{
	[Sales Territory].[Sales Territory].[Group],
	[Sales Territory].[Sales Territory].[Country]
}
ON ROWS

FROM
[Adventure Works]
```
Upon executing the above code, we will first get the *Sales Amount* by *Groups* and below that *Sales Amount* by `Countries`.

This is because of **union** operation that took place within a set.
