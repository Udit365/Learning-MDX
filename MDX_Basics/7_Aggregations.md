# AGGREGATONS


## `SUM()` FUNCTION

The `SUM()` functio is simply used as an aggregator to calculate the summation of a measure over a certain attribute.

For example, we can use the `SUM()` function to calculate the grand total of reseller sales or, grand total of reseller sales for all the countires under "*North America*" group etc.

The syntax of the `SUM()` function in MDX is :
```MDX
SUM( Set_expression [,Numeric_Exression] )</br>
```
In the above syntax :

- `[,Numeric_Exression]` is optional.
- If a numeric expression is specified, then it is evaluated across the set and then summed.
- If a numeric expression is not specified, then the set is evaluated based on the other elements of the `SELECT` statement.

Let's demonstrate the `SUM()` with an example :

*Show the reseller sales by country and their grand total as well :*

```mdx
WITH MEMBER [Measures].[Total Sales] AS

SUM
(
	[Dim Sales Territory].[Sales Territory Country].[Sales Territory Country],
	[Measures].[Sales Amount]
)

SELECT
{
	[Measures].[Sales Amount],
	[Measures].[Total Sales]
} ON COLUMNS,

[Dim Sales Territory].[Sales Territory Country].[Sales Territory Country] ON ROWS

FROM
[AdventureWorksDW]
```

In the above code, we will get two columns

1. `Sales Amount`
1. `Total Sales`

`Total Sales` is a query scoped calculated measure that sums up the sales amount for all the countries.

Therefore, we will have the same number in the `Total Sales` coulumn against each country name, which is nothing but the `Sales Amount` of all the countires combined.

***Practice Query :***</br>
	*Write a MDX query that will show top 5 countries based on their contribution to the `Sales Amount`*

***Solution :***

```mdx
WITH MEMBER [Measures].[Total Sales] AS

SUM
(
	[Dim Sales Territory].[Sales Territory Country].[Sales Territory Country],
	[Measures].[Sales Amount]
)

MEMBER [Measures].[Contibution %] AS
[Measures].[Sales Amount]/[Measures].[Total Sales], FORMAT_STRING ='Percent'

SET [Top5Countries] AS

TOPCOUNT
(
	[Dim Sales Territory].[Sales Territory Country].[Sales Territory Country],
	5,
	[Measures].[Contibution %]
)

SELECT
[Measures].[Contibution %] ON COLUMNS,
[Top5Countries] ON ROWS

FROM
[AdventureWorksDW]
```
### `SUM()` function without numeric expression

From the syntax of `SUM()` function, it is clear that the `[,Numeric_Exression]` is optionl and when we don't provide a `[,Numeric_Exression]` then, the `SUM()` function just aggregates the given `Set_expression`.

For example, if we create a calculated member using the `SUM()` with  `Set_expression` as "*Sales Territory Countries*" then, we can pass the calculated member in row level and it will show us the aggregator value of the measure taken in the column (it may be *sales amount* or, *product cost* etc.) for all the countries combined.

***Sample Query:***</br>
Create a calculated member that shows the sum of United States, Australia and Canada combined for reseller sales amount and total product cost :

***Solution:***

```mdx
WITH MEMBER [Dim Sales Territory].[Sales Territory Country].[TotalSales US,Can,Aus] AS
SUM
(
	{
		[Dim Sales Territory].[Sales Territory Country].&[Australia],
		[Dim Sales Territory].[Sales Territory Country].&[Canada],
		[Dim Sales Territory].[Sales Territory Country].&[United States]
	}
)

SELECT
{
	[Measures].[Sales Amount],
	[Measures].[Total Product Cost]
} ON COLUMNS,

{
	[Dim Sales Territory].[Sales Territory Country].&[Australia],
	[Dim Sales Territory].[Sales Territory Country].&[Canada],
	[Dim Sales Territory].[Sales Territory Country].&[United States],
	[Dim Sales Territory].[Sales Territory Country].[TotalSales US,Can,Aus]
} ON ROWS

FROM
[AdventureWorksDW]
```

If we wish to make this calculated member permanent then, we can just copy the `SUM()` argument and paste it in the "*expression*" section in visual studio interface for cube calculation, as shown in the below image :






We must give special attention to the "*Parent Hierarchy*" section because, now the calculated member should be placed under `[Dim Sales Territory].[Sales Territory Country]` and not in the `Measures`.


## `.MEMBERS` and `.ALLMEMBERS`

Both `.MEMBERS` and `.ALLMEMBERS` executes all the members/elements present in the attribute along with the grand total but, `.ALLMEMBERS` provides the calculated members related to the attribute whereas, `.MEMBERS` only provides only the members and their grand total.

The following example shows a clear distinction between `.MEMBERS` and `.ALLMEMBERS` :

#### `.MEMBERS` Example

```mdx
SELECT
[Measures].[Sales Amount] ON COLUMNS,
[Dim Sales Territory].[Sales Territory Country].MEMBERS ON ROWS

FROM
[AdventureWorksDW]
```
#### `.ALLMEMBERS` Example

```MDX
SELECT
[Measures].[Sales Amount] ON COLUMNS,
[Dim Sales Territory].[Sales Territory Country].ALLMEMBERS ON ROWS

FROM
[AdventureWorksDW]
```
