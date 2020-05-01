# CALCULATED MEMBERS & NAMED SETS
---

## CALCULATED MEMBERS
---

Calculated members are nothing but the calculations applied over the members of the cube.

The calculated members can't be displayed as an axis but, its the calculated results that shows up on cells (as coordinates).

For example, we can create members like "*Profit*", "*Profit percentage*","*Product count*" etc. which are nothing but the measures created on the members.

We need to perform the following steps to create a calculated member in Visual Studio :


Steps in Visual studio and others..


***Example Query :***

*Show profit by countries (Use calculated member "Profit")*

***Solution :***

```MDX
SELECT [Measures].[Profit] ON COLUMNS,

[Dim Sales Territory].[Sales Territory Country].[Sales Territory Country] ON ROWS

FROM
[AdventureWorksDW]
```

To verify whether the calculated members are performing correctly, we can execute the following query

```MDX
SELECT
{
	[Measures].[Sales Amount],
	[Measures].[Total Product Cost],
	[Measures].[Profit]
} ON COLUMNS,

[Dim Sales Territory].[Sales Territory Country].[Sales Territory Country] ON ROWS

FROM
[AdventureWorksDW]
```

- The calculated member is completely stored within the cube and can be modified as per the requirement.




### QUERY SCOPED CALCULATED MEMBER
---

Query scoped calculated members are defined and used along with the MDX query

For example :

In the below example, `[Measures].[InternetProfit]` is a calculated member which is defined and executed along with rest of the MDX query

```mdx
WITH MEMBER [Measures].[InternetProfit] AS
[Measures].[Sales Amount - Fact Internet Sales] - [Measures].[Total Product Cost - Fact Internet Sales]

SELECT
[Measures].[InternetProfit] ON COLUMNS,
[Dim Sales Territory].[Sales Territory Country].[Sales Territory Country] ON ROWS

FROM
[AdventureWorksDW]
```

### SESSION SCOPED CALCULATED MEMBER
---

Session scoped calculated members are created first and then can be used in any number of queries of the current session.

>***Session :***
    As long as we are performing operations in the same MDX script file, we are inside a session and upon creating a new MDX query script, we jump into another session.

To create a session scoped calculated member, we have to follow the simillar syntax as below and execute it.

```mdx
CREATE MEMBER [AdventureWorksDW].[Measures].[SessionInternetProfit] AS
[Measures].[Sales Amount - Fact Internet Sales] - [Measures].[Total Product Cost - Fact Internet Sales]
```
>***Notes :*** *Cube name must be provided before defining the session scoped calculated member*

After creating the above session scoped calculated member, we can simply comment it out so that, we don't lose the calculated member name and its formula and then, we can query the cube any number of time within the same session by using the created session scoped calculated member.

An example of using the session scoped calculated member is :

```mdx
SELECT
[Measures].[SessionInternetProfit] ON COLUMNS,
[Dim Sales Territory].[Sales Territory Group].[Sales Territory Group] ON ROWS

FROM
[AdventureWorksDW]
```
> ***Notes :***</br>
In query scoped calculation, the `WITH MEMBER` statement is executed with the `SELECT` statement whereas, in the session scoped calculation the `CREATE MEMBER`statement is executed first to create the calculated member for the session and then, the `SELECT` statement gets executed.



### CREATING MUTIPLE CALCULATED MEMBER AT ONCE
---
We can also create mutiple calculated members (query scoped/session scoped) at once, as follows :

```mdx
WITH MEMBER [Measures].[InternetProfit] AS
[Measures].[Sales Amount - Fact Internet Sales]- [Measures].[Total Product Cost - Fact Internet Sales]

MEMBER [Measures].[ProfitPct] AS
[Measures].[InternetProfit]/[Measures].[Sales Amount - Fact Internet Sales], FORMAT_STRING = 'Percent'

SELECT
{
	[Measures].[InternetProfit],
	[Measures].[ProfitPct]
} ON COLUMNS,

[Dim Sales Territory].[Sales Territory Country].[Sales Territory Country] ON ROWS

FROM
[AdventureWorksDW]
```

In the above formula, we have created 2 query scoped calculated members and for the 2nd calculated member, we don't have to write `WITH` before `MEMBER`.

Similarly, we needed to show the "*Profit*"" in percentages and therefore, we have assigned `Percent` for the `FORMAT_STRING` argument.

## NAMED SET
---

Named sets can be understood as the alias that help developers by reducing the burden of writing repetative queries.

Named sets are created when we have to show the members on either of the axis.

For example, a named set can be created that finds out the "*Top 5 countries by Sales Amount*".


Steps in Visual studio and others..

### QUERY SCOPED NAMED SET
---

Query scoped named set are defined and used along with the MDX query

For example :

In the below example, `[Year2012Onwards]` is a named set which is defined and executed along with rest of the MDX query

```mdx
WITH SET [Year2010Onwards] AS
[Order Date].[Calendar Year].&[2010] : [Order Date].[Calendar Year].&[2014]

SELECT [Measures].[Sales Amount] ON COLUMNS,
[Year2010Onwards] ON ROWS

FROM
[AdventureWorksDW]
```
### SESSION SCOPED NAMED SET
---

Session scoped named set are created first and then can be used in any number of queries of the current session.

>***Session :***
    As long as we are performing operations in the same MDX script file, we are inside a session and upon creating a new MDX query script, we jump into another session.

To create a session scoped named set, we have to follow the simillar syntax as below and execute it.

```mdx
CREATE SET [AdventureWorksDW].[Session2010Onwards] AS
[Order Date].[Calendar Year].&[2010] : [Order Date].[Calendar Year].&[2014]
```
***Notes :*** *Cube name must be provided before defining the session scoped named set*

After creating the above session scoped named set, we can simply comment it out so that, we don't lose the named set name and its formula and then, we can query the cube any number of time within the same session by using the created session scoped named set.

An example of using the session scoped calculated member is :

```mdx
SELECT [Measures].[Sales Amount] ON COLUMNS,
[Session2010Onwards] ON ROWS

FROM
[AdventureWorksDW]
```

### Static & Dynamic Named Sets

A static named set executes the same result irrespective of the filter condition, whereas a dynamic named set alters the executions on the basis of filter condition.

All the query scoped named sets are dynamic by nature but, all the session scoped named sets are static.

For example :

Let's say, we created a named set to get the Top-5 countries by reseller sales amount and to do so, we created a query scoped and a session scoped named set as follows -:

```mdx
-- Query_Scoped

WITH SET [QS_Top5Countries] AS

TOPCOUNT
(
	[Dim Sales Territory].[Sales Territory Country].[Sales Territory Country],
	5,
	[Measures].[Sales Amount]
)

-- Session_Scoped

CREATE SET [AdventureWorksDW].[SS_Top5Countries] AS

TOPCOUNT
(
	[Dim Sales Territory].[Sales Territory Country].[Sales Territory Country],
	5,
	[Measures].[Sales Amount]
)

```

Now, if we execute a query with a `WHERE` clause for the "*Order Year = 2012*" then, the query scoped named set will execute the Top-5 countries on the basis of *2012 reseller sales amount*, whereas, the session scoped named set will execute the Top-5 countries without taking the filter provided into account.

To create a dynamic session scoped named set, we have to write the named set as follows -:

```mdx
CREATE DYNAMIC SET [AdventureWorksDW].[SS_Top5Countries] AS

TOPCOUNT
(
	[Dim Sales Territory].[Sales Territory Country].[Sales Territory Country],
	5,
	[Measures].[Sales Amount]
)
```
### Dropping Session Scoped Named Sets

To delete/drop a session scoped named set, we have to pass `DROP SET` command followed by the `[Cube Name].[Session Scoped Named Set Name]`

```mdx
DROP SET [AdventureWorksDW].[SS_Top5Countries]
```

Similarly, while creating a permanent named set (using visual studio platform) on cube, we have the option to choose whether we need it to be static or, dynamic; as shown in the below image :


## USING CALCULATED MEMBERS & NAMED SET TOGATHER
---
We can define and calculated members and named set togather as follows :

*Show the reseller profit for the order year 2012 and onwards :*

```MDX
WITH SET [Year2012Onwards] AS
[Order Date].[Calendar Year].&[2012] : [Order Date].[Calendar Year].&[2014]

MEMBER [Measures].[Profit] AS
[Measures].[Sales Amount]-[Measures].[Total Product Cost], FORMAT_STRING = 'Currency'

SELECT
[Measures].[Profit] ON COLUMNS,
[Year2012Onwards] ON ROWS

FROM
[AdventureWorksDW]
```
Wheather we define multiple named sets or, calculated members or, both; we always use the `WITH` clause just once as showed in the above example.
