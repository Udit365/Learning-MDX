# `SUM()` FUNCTION
---
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
