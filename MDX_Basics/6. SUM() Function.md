# `SUM()` FUNCTION
---
The syntax of the `SUM()` function in MDX is :

> SUM( Set_expression [,Numeric_Exression] )
	- `[,Numeric_Exression]` is optional.
	- If a numeric expression is specified, then it is evaluated across the set and then summed.
	- If a numeric expression is not specified, then the set is evaluated based on the other elements of the `SELECT` statement.

Let's demonstrate the `SUM()` with an example :

```mdx
WITH MEMBER [Measures].[Total Sales] AS
SUM
(
	[Sales Territory].[Sales Territory Country].[Sales Territory Country],
	[Measures].[Sales Amount]
)

SELECT
{
	[Measures].[Sales Amount],
	[Measures].[Total Sales]
}
ON COLUMNS,
[Sales Territory].[Sales Territory Country].[Sales Territory Country]
ON ROWS

FROM
[Adventure Works]
```

In the above code, we will get two columns 

1. `Sales Amount`
1. `Total Sales`

`Total Sales` is a query scoped calculated measure that sums up the sales amount for all the countries.
Therefore, we will have the same number in the `Total Sales` coulumn against each country name, which is nothing but the `Sales Amount` of all the countires combined.

> ***Query :***
	*Write a MDX query that will show top 5 countries based on their contribution to the `Sales Amount`*

```mdx
WITH MEMBER [Measures].[Total Sales] AS
SUM
(
	[Sales Territory].[Sales Territory Country].[Sales Territory Country],
	[Measures].[Sales Amount]
)

MEMBER [Measures].[Contribution %] AS
[Measures].[Sales Amount]/[Measures].[Total Sales],
FORMAT_STRING = 'Percent'

SET [Top5Country] AS
TOPCOUNT
(
	[Sales Territory].[Sales Territory Country].[Sales Territory Country],
	5,
	[Measures].[Contribution %]
)
SELECT
{
	[Measures].[Sales Amount],
	[Measures].[Contribution %]
}
ON COLUMNS,
[Top5Country]
ON ROWS

FROM
[Adventure Works]
```
