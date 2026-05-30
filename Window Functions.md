#sql #postgres 

# Sample Tables

<u>Table</u>: Products

| **product_id** | **product_name**   | **price** | **group_id** |
| -------------- | ------------------ | --------- | ------------ |
| 1              | Microsoft Lumia    | 200.00    | 1            |
| 2              | HTC One            | 400.00    | 1            |
| 3              | Nexus              | 500.00    | 1            |
| 4              | iPhone             | 900.00    | 1            |
| 5              | HP Elite           | 1200.00   | 2            |
| 6              | Lenovo Thinkpad    | 700.00    | 2            |
| 7              | Sony VAIO          | 700.00    | 2            |
| 8              | Dell Vostro        | 800.00    | 2            |
| 9              | iPad               | 700.00    | 3            |
| 10             | Kindle Fire        | 150.00    | 3            |
| 11             | Samsung Galaxy Tab | 200.00    | 3            |
<u>Table</u>: Product_Groups

| **group_id** | **group_name** |
| ------------ | -------------- |
| 1            | Smartphone     |
| 2            | Laptop         |
| 3            | Tablet         |
<u>Table:</u> Sales Stats
| **name**       | **year** | **amount** |
| -------------- | -------- | ---------- |
| John Doe       | 2018     | 120000.00  |
| Jane Doe       | 2018     | 110000.00  |
| Jack Daniel    | 2018     | 150000.00  |
| Yin Yang       | 2018     | 30000.00   |
| Stephane Heady | 2018     | 200000.00  |
| John Doe       | 2019     | 150000.00  |
| Jane Doe       | 2019     | 130000.00  |
| Jack Daniel    | 2019     | 180000.00  |
| Yin Yang       | 2019     | 25000.00   |
| Stephane Heady | 2019     | 270000.00  |
# Aggregate Functions vs Window Functions
An aggregate function **aggregates data from a set of rows into a single row**.

**Scenario**: Calculate the average price of all products ***per product_group*** in the `products` table.
```sql
SELECT 
	group_name, 
	AVG (price) 
FROM products 
INNER JOIN product_groups USING (group_id) GROUP BY group_name;
```

O/P:

| **group_name** | **avg** |
| -------------- | ------- |
| Smartphone     | 500.00  |
| Tablet         | 350.00  |
| Laptop         | 850.00  |

A window function **aggregates data but does not change the number of rows**.

**Scenario**:Calculate the average price of all products ***per product_group*** in the `products` table.
```sql
SELECT 
	product_name, 
	price, 
	group_name, 
	AVG (price) 
OVER ( PARTITION BY group_name ) 
FROM products 
INNER JOIN product_groups USING (group_id);
```

O/P:
![[Pasted image 20260302112234.png]]
`AVG()` function works as a _window function_ that operates on a set of rows specified by the `OVER` clause. Each set of rows is called a window.
The `PARTITION BY` distributes the rows of the result set into groups and the `AVG()` function is applied to each group to return the average price for each.
## Syntax
```sql
SELECT <column_1>, <column_2>,
  <window_function> OVER (
    PARTITION BY <...>
    ORDER BY <...>
    <window_frame>) <window_column_alias>
FROM <table_name>;
```

The `OVER` clause is ***mandatory*** to define a window frame.

The `PARTITION BY` clause divides rows into multiple groups or partitions to which the window function is applied. 
	It is optional. If you skip the `PARTITION BY` clause, the window function will treat the whole result set as a single partition.

The `ORDER BY` clause specifies the order of rows in each partition to which the window function is applied.
	It uses the `NULLS FIRST` or `NULLS LAST` option to specify whether nullable values should be first or last in the result set. The default is `NULLS LAST` option.

The `window_frame` subclause defines the upper and lower bounds of a window frame. It is optional.
	`ROWS`: defines the number of rows that precede and follow the current row.
	`RANGE`: defines the range of rows based on their value compared to the current row.
	The default `window_frame` is start of partition to current row.
	For more details, read [[#Window Frame]] 

# Ranking Functions
## ROW_NUMBER
+ It assigns a sequential number to each row in each partition.
+ If rows have the same value, they all will get consecutive rankings i.e. if two rows have the same value, `ROW_NUMBER()` would place them at 5th and 6th.
+ Can't predict which row with same value is ranked higher.
+ Does not require `ORDER BY` clause.

**Scenario**: Imagine you are managing an inventory database for a tech retailer. Your manager wants to see a list of all products, but they want them organized by their category (Smartphone, Laptop, Tablet).
Within each category, they want the products listed from the **cheapest to the most expensive**.

The `ROW_NUMBER()` window function is perfect here because it allows you to generate those sequential IDs without needing to manually count or alter the underlying data.

```sql
SELECT 
	product_name, 
	group_name, 
	price, 
	ROW_NUMBER () 
OVER ( PARTITION BY group_name ORDER BY price ) 
FROM products INNER JOIN product_groups USING (group_id);
```

![[Pasted image 20260302115348.png]]
## RANK
+ It assigns a rank to each row in the same window. 
+ If two rows have the same value, they have the same rank with next rank skipped.
+ Requires a `ORDER BY` clause.

**Scenario**: Imagine you are conducting a pricing audit for your electronics store. You want to identify which products are the "best value" (cheapest) in each category. However, unlike a simple list, you want to acknowledge when products have the **exact same price** by giving them the same rank.

```sql
SELECT 
	product_name, 
	group_name,
	price, 
	RANK () 
OVER ( PARTITION BY group_name ORDER BY price ) 
FROM products INNER JOIN product_groups USING (group_id);
```

![[Pasted image 20260302120034.png]]
## DENSE_RANK
+ It assigns a rank to each row in the same window. 
+ If two rows have the same value, they have the same rank but next rank is not skipped.
+ Requires a `ORDER BY` clause.

```sql
SELECT 
	product_name, 
	group_name, 
	price, 
	DENSE_RANK () 
OVER ( PARTITION BY group_name ORDER BY price ) 
FROM products INNER JOIN product_groups USING (group_id);
```

![[Pasted image 20260302120544.png]]
# Analytic Functions
## FIRST_VALUE
+ Returns the value in the first row of the window frame.
+ Used for comparison between current value and first value.

**Scenario**: Imagine you are a category manager who needs to generate a report showing how each product's price compares to the **cheapest item** in its respective category.

```sql
SELECT 
	product_name, 
	group_name, 
	price, 
	FIRST_VALUE (price) 
OVER ( 
		PARTITION BY group_name ORDER BY price 
	) AS lowest_price_per_group 
FROM products INNER JOIN product_groups USING (group_id);
```

![[Pasted image 20260302122805.png]]
## LAST_VALUE
+ Returns the value in the last row of the window frame.
+ Used for comparison between current value and last value.

**Scenario**: Imagine you are preparing a "Premium Range" report. You want to list every product and, right next to its price, display the **most expensive** product's price from that same category.

```sql
SELECT 
	product_name, 
	group_name, 
	price, 
	LAST_VALUE (price) 
OVER ( 
		PARTITION BY group_name 
		ORDER BY price 
		RANGE BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING 
	) AS highest_price_per_group 
FROM products INNER JOIN product_groups USING (group_id);
```

Note the use of `RANGE BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING`This is done so that we can get the value of the last row in the _partition_.
Remember that default window frame is from _start of the partition_ to _current row_.
![[Pasted image 20260302123808.png]]
If we do not specify the `RANGE` , we get this output:
![[Pasted image 20260302124407.png]] ^10e0c8
## LAG
+ Returns the value in the _nth_ preceding row.
+ Since the first row has no preceding row, the value is `NULL`.
```sql
-- Syntax
LAG (expression [,offset] [,default]) over_clause;
```

- `expression` – a column or expression to compute the returned value.
-  `offset` – the number of rows preceding ( `LAG`) the current row. It defaults to 1.
- `default` – the default returned value if the `offset` goes beyond the scope of the window. The `default` is `NULL`.

**Scenario**
Imagine you are a financial analyst reviewing the company's hardware pricing strategy. You want to see how the price of each product compares to the product listed **just before it** in the same category.

```sql
SELECT 
	product_name, 
	group_name, 
	price, 
	LAG (price, 1) OVER ( PARTITION BY group_name ORDER BY price ) AS prev_price, 
	price - LAG (price, 1) OVER ( PARTITION BY group_name ORDER BY price ) AS cur_prev_diff 
FROM products INNER JOIN product_groups USING (group_id);
```

![[Pasted image 20260302130401.png]]
## LEAD
+ Returns the value in the _nth_ following row.
+ Since the last row has no following row, the value is `NULL`.
```sql
-- Syntax
LEAD (expression [,offset] [,default]) over_clause;
```

- `expression` – a column or expression to compute the returned value.
-  `offset` – the number of rows following ( `LEAD`) the current row. It defaults to 1.
- `default` – the default returned value if the `offset` goes beyond the scope of the window. The `default` is `NULL`.

**Scenario**: Imagine you are a sales strategist looking to encourage customers to "upset" their purchase. You want to show each product alongside the price of the **next most expensive item** in that same category.
```sql
SELECT 
	product_name, 
	group_name, 
	price, 
	LEAD (price, 1) OVER ( PARTITION BY group_name ORDER BY price ) AS next_price, 
	price - LEAD (price, 1) OVER ( PARTITION BY group_name ORDER BY price ) AS cur_next_diff 
FROM products INNER JOIN product_groups USING (group_id);
```
![[Pasted image 20260302140852.png]]
## NTH_VALUE
+ Returns the value from the nth row in an ordered partition of a result set.
```sql
--Syntax
NTH_VALUE(expression, offset) 
OVER ( [PARTITION BY partition_expression] 
		[ ORDER BY sort_expression [ASC | DESC] 
		frame_clause 
	)
```

+ `expression` is the target column or expression on which the `NTH_VALUE()` function operates.
+ `offset` is a positive integer (greater than zero) that determines the row number relative to the first row in the ___window___ against which the expression evaluates.

**Scenario**: Imagine you are a product researcher wanting to identify the **"Premium Standard"**—the second most expensive item—in each category.

```sql
SELECT 
	product_id, 
	product_name, 
	price, 
	NTH_VALUE(product_name, 2) OVER( 
		PARTITION BY group_id 
		ORDER BY price DESC 
		RANGE BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING ) 
FROM products;
```

To understand why we used `RANGE BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING` refer [[#^10e0c8|this]] 
![[Pasted image 20260302142246.png]]
## NTILE
+  Divides ordered rows in the partition into a specified number of ranked groups as equal size as possible.
+ Each group is assigned a bucket number starting from 1.
```sql
--Syntax
NTILE(buckets) OVER ( [PARTITION BY partition_expression, ... ] [ORDER BY sort_expression [ASC | DESC], ...] )
```

`buckets` represents the number of ranked groups. It can be a number or an expression that evaluates to a positive integer value (greater than 0) for each partition.

**Scenario**: divide the sales team into three "performance buckets" (e.g., Low, Medium, and High performers) for each specific year.

```sql
SELECT 
	name, 
	amount, 
	NTILE(3) 
OVER( PARTITION BY year ORDER BY amount ) 
FROM sales_stats;
```
![[Pasted image 20260302154835.png]]
# Distribution Functions
## CUME_DIST
 + Returns the cumulative distribution of a value within a set of values. In other words, it returns the relative position of a value in a set of values.
 + Value between 0 and 1.
 + Returns the same cumulative distribution values for the same tie values.
 ```sql
 CUME_DIST() OVER ( [PARTITION BY partition_expression, ... ] ORDER BY sort_expression [ASC | DESC], ... )
 ```

**Scenario**: Imagine you are a HR manager preparing a performance review. You want to see the relative standing of each salesperson's revenue compared to their peers over the years.
```sql
SELECT 
	name, 
	year, 
	amount, 
	CUME_DIST() OVER ( PARTITION BY year ORDER BY amount ) 
FROM sales_stats;
```
![[Pasted image 20260302161153.png]]
## PERCENT_RANK
+ Evaluates the relative standing of a value within a set of values.
+ Value between 0 and 1.
+ **The first value always receives a value of zero**.
+ Tie values evaluate to the same cumulative distribution value.
```sql
--Syntax
PERCENT_RANK() OVER ( [PARTITION BY partition_expression, ... ] ORDER BY sort_expression [ASC | DESC], ... )
```

**Scenario**: Imagine you are a regional sales director performing a "relative performance" audit. While `CUME_DIST` tells you the percentage of people at or below a certain level, **`PERCENT_RANK()`** is often preferred for calculating **bonuses or percentile standings** because it always anchors the "bottom" performer at exactly **0**.

```sql
SELECT 
	name, 
	amount, 
	PERCENT_RANK() OVER ( PARTITION BY year ORDER BY amount ) FROM sales_stats;
```
![[Pasted image 20260302162802.png]]
# Using aggregate functions with OVER clause
+ `avg(expr)` - average value for rows within the window frame
- `count(expr)` - count of values for rows within the window frame
- `max(expr)` - maximum value within the window frame
- `min(expr)` - minimum value within the window frame
- `sum(expr)` - sum of values within the window frame

# Window Frame

+ `ROWS`: Defines the number of rows that precede and follow the current row.
+ `RANGE`: defines the range of rows based on their value compared to the current row.
```sql
ROWS/RANGE BETWEEN [start] AND [end]
```

`[start]` can take on 3 values:
	`UNBOUNDED PRECEDING`: Window starts at first row of partition
	`CURRENT ROW`: Window starts at current row.
	`n PRECEDING`: Window starts at nth row before the current row. 
	
`[end]` can take on 3 values:
	`UNBOUNDED FOLLOWING`: Window ends at last row of partition
	`CURRENT ROW`: Window ends at current row.
	`n FOLLOWING`: Window ends at nth row after the current row. 

<u>Sample data</u>: `daily_revenue`

| **sale_date** | **revenue** |
| ------------- | ----------- |
| 2026-01-01    | 1000.00     |
| 2026-01-02    | 2000.00     |
| 2026-01-03    | 2000.00     |
| 2026-01-04    | 3000.00     |
**Scenario**: Imagine you are a retail manager tracking daily sales. You want a **3-day moving average** to smooth out fluctuations.

```sql
SELECT 
    sale_date, 
    revenue,
    -- Strictly the last 3 rows
    AVG(revenue) OVER (
        ORDER BY revenue 
        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
    ) AS rows_moving_avg,=
    AVG(revenue) OVER (
        ORDER BY revenue 
        RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS range_running_avg
FROM daily_revenue;
```

![[Pasted image 20260302173923.png]]
>[!warning]
>- **`ROWS`** is "literal"—it adds one row at a time.  
>	- Use when physical count of records (ex: last 3 days) is more important
>- **`RANGE`** is "logical"—it treats tied values as a single peer group and adds them both to the total at the same time.
>	- Use when logical values of records is more important (ex: with date ranges, [[#PERCENT_RANK]] or [[#CUME_DIST]] calculation)

A more appropriate case for using `RANGE` is:
<u>Sample data</u>: `inventory_audit`

| **item_name** | **category** | **value** |
| ------------- | ------------ | --------- |
| Bolt          | Hardware     | 5.00      |
| Screw         | Hardware     | 5.00      |
| Drill Bit     | Hardware     | 15.00     |
| Power Drill   | Tools        | 150.00    |
**Scenario**: Find cumulative value of products which cost the same
```sql
SELECT 
    item_name, 
    value,
    -- Logical: Treats $5.00 items as one group
    SUM(value) OVER (
        ORDER BY value 
        RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS range_total,
    -- Physical: Adds items one-by-one
    SUM(value) OVER (
        ORDER BY value 
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS rows_total
FROM inventory_audit;
```

| **item_name** | **value** | **range_total** | **rows_total** |
| ------------- | --------- | --------------- | -------------- |
| Bolt          | 5.00      | **10.00**       | 5.00           |
| Screw         | 5.00      | **10.00**       | 10.00          |
| Drill Bit     | 15.00     | 25.00           | 25.00          |
| Power Drill   | 150.00    | 175.00          | 175.00         |
|               |           |                 |                |
