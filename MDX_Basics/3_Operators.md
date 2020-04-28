# OPERATORS IN MDX

There are mainly, 3 types of operators used in MDX :

1. Comma (`,`)
1. Colon (`:`)
1. Minus (`-`)

### COMMA OPERATOR

Two members are always separated with a comma operator.

***Example :***

In the below code, we have selected two *countries* from the *Sales Territory* attribute separated by comma operator.

```mdx
SELECT
[Measures].[Sales Amount]
ON COLUMNS,
{
	[Sales Territory].[Sales Territory Country].&[Australia],
	[Sales Territory].[Sales Territory Country].&[Germany]
}
ON ROWS

FROM
[Adventure Works]
```
### COLON OPERATOR

If we have to select a range of information from the same attribute then, instead of writing each one of them using a comma separator, we can use the colon separator as follows :

```mdx
SELECT
[Measures].[Sales Amount]
ON COLUMNS,
{
	[Sales Territory].[Sales Territory Country].&[Australia]:
	[Sales Territory].[Sales Territory Country].&[Germany]
}
ON ROWS

FROM
[Adventure Works]
```
### MINUS OPERATOR
---
If we have to select a range of information except some specific members then we can use the hyphen operator before those specific memebers to omit them out from the result as follows :

```mdx
SELECT
[Measures].[Sales Amount]
ON COLUMNS,
{
	[Sales Territory].[Sales Territory Country].&[Australia]:
	[Sales Territory].[Sales Territory Country].&[Germany]-
	[Sales Territory].[Sales Territory Country].&[France]-
	[Sales Territory].[Sales Territory Country].&[Canada]
}
ON ROWS

FROM
[Adventure Works]
```
or,

we can put the members to exclude in a set and deduct them from the range.

```mdx
SELECT
[Measures].[Sales Amount]
ON COLUMNS,

[Sales Territory].[Sales Territory Country].&[Australia]:
[Sales Territory].[Sales Territory Country].&[Germany]-
{
	[Sales Territory].[Sales Territory Country].&[France],
	[Sales Territory].[Sales Territory Country].&[Canada]
}
ON ROWS

FROM
[Adventure Works]
```
#### EXCLUDING ELEMENTS WITHOUT MINUS OPERATOR

Another way to exclude some specific members from the output without using the minus operator is :

```mdx
SELECT
[Measures].[Sales Amount]
ON COLUMNS,
{
	[Sales Territory].[Sales Territory Country].&[Australia]:
	[Sales Territory].[Sales Territory Country].&[France],

	[Sales Territory].[Sales Territory Country].&[United Kingdom]:
	[Sales Territory].[Sales Territory Country].&[United States]
}
ON ROWS

FROM
[Adventure Works]
```
By using the above code, we can exclude the range of countries falling between *France* and *United Kingdom* in the cube.
