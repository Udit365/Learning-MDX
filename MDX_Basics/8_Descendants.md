# DESCENDANTS() FUNCTION
---
- `DESCENDANTS()` deals with hierarchies that are created in the cube.
- Displays the descendants of a member based on the level of hierarchy.

Let's assume that we have a `Geography` Hierarchy as follows :

> Level-1 : Country
Level-2 : State
Level-3 : City
Level-4 : Postal Code


The syntax of `DESCENDANTS()`  function is :

>DESCENDANTS( «Member»[, «Level»[, «Desc_flags»]] )


***In the above syntax :***

`«Member»` : The base level metrics upon which the function will work upon. Let's say `Level-1`
`«Level»` : A lower level metrics. So, it can be `Level-2` or, `Level-3` or, `Level-4`.
`<<Desc_Flags>>` : There are many description flags used to modify the results of `DESCENDANTS()` function.

## `<<Desc_Flags>>`

Deascription flags are responsible for modifying the final result.

The various ways through which description flags modify the results are :

1. `SELF`: Returns the level information mentioned in `«Level»` argument. Let's say, its **Level-3**

2. `AFTER`:Returns all the level information that are present below **Level-3** ,i.e., **Level-4** and further, if present.

3. `BEFORE`: Returns all the level information that are present above **Level-3** ,i.e., **Level-2** and **Level-1**.

4. `BEFORE_AND_AFTER`: Returns all the levels except **Level-3**

5. `SELF_AND_AFTER`: Returns **Level-3** and all the level information that are present below it.

6. `SELF_AND_BEFORE`: Returns **Level-3** and all the level information that are present above it,i.e. upto the **Level-1**

7. `SELF_BEFORE_AFTER`: Returns information for all the levels, i.e., from **Level-1** to **Level-4**

8. `LEAVES` : Returns the same result as `SELF` when the `«Level»` argument is defined otherwise, it will give the granular level information for the `«Member»` argument (In this case it is **Level-4**)



Let's understand this more clearly with an example :

```mdx
SELECT
{} ON COLUMNS,

DESCENDANTS
(
	[Geography].[Geography].[Country].&[Germany],
	[Geography].[Geography].[City],
	SELF
)
ON ROWS

FROM
[Adventure Works]
```
The above code will display all the city names of `Germany`.

There is another type of syntax used for `DESCENDANTS()` Function, i.e., 

>DESCENDANTS( «Member»,«Distance»[, «Desc_flags»] )

If we use this syntax then, rather than giving the name of the level, we just give the level number.

Here `<<Member>>` is considered to be the **Level-0**.

So, if we have a `Geography` Hierarchy as follows :

> Level-1 : Country
Level-2 : State
Level-3 : City
Level-4 : Postal Code

then, if we provide *Country* as the `<<Member>>` then, its becomes **Level-0** and hence if we want *City* level information then we have to provide `3` as the <<Distance>> argument and `SELF` as the <<Desc_flags>>.

Similarly, if we choose *City* to be the <<Member>> then, it becomes **Level-0** and *Postal Code* becomes **Level-1**.

The below example shows, how to get all the*Postal Code* information using this alternate syntax of `DESCENDANTS()`.

```mdx
SELECT
{} ON COLUMNS,

DESCENDANTS
(
	[Geography].[Geography].[Country].&[Germany],
	3,
	SELF
)
ON ROWS

FROM
[Adventure Works]
```
If someone gives some bigger number in `<<Distance>>` argument then, the result will be the last level information available.