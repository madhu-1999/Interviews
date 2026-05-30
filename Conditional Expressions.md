#postgres #sql

# CASE
+ If/else expression
+ Returns _NULL_ if ELSE clause is omitted and no condition matches.
```sql
SELECT
	CASE WHEN condition THEN result
	[WHEN ...]
	[ELSE result]
	END
FROM table_name;
```

## To label categories of data
<u><b>Scenario</b></u> : __E-commerce Shipping Urgency__
Imagine you are running a logistics report. You have a `shipments` table, and you need to flag orders based on how long they've been sitting in the warehouse.
- If the order is over **5 days old**, it’s a `CRITICAL` delay.    
- If it's between **3 and 5 days**, it's a `WARNING`.   
- Otherwise, it's `ON TRACK`.

Sample Data: `shipments`

| **order_id** | **destination** | **days_in_transit** |
| ------------ | --------------- | ------------------- |
| 5001         | New York        | 6                   |
| 5002         | Los Angeles     | 4                   |
| 5003         | Chicago         | 1                   |
| 5004         | Miami           | 10                  |

```sql
SELECT 
	*,
	(CASE WHEN days_in_transit > 5 THEN 'CRITICAL'
	WHEN days_in_transit BETWEEN 3 AND 5 THEN 'WARNING'
	ELSE 'ON_TRACK'
	END) AS shipping_status
FROM shipments
ORDER BY days_in_transit DESC;
```

Output:

| **order_id** | **destination** | **days_in_transit** | **shipping_status** |
| ------------ | --------------- | ------------------- | ------------------- |
| 5004         | Miami           | 10                  | CRITICAL            |
| 5001         | New York        | 6                   | CRITICAL            |
| 5002         | Los Angeles     | 4                   | WARNING             |
| 5003         | Chicago         | 1                   | ON TRACK            |
## Pivot Data (Turn rows into columns)
<u><b>Scenario</b></u> : __Monthly Sales Report__
Imagine you have a simple list of sales transactions, and you want to see the total sales per `office_location` for Jan and Feb as **columns** rather than a long list of rows.

Table `sales_records`

| **office_location** | **sale_month** | **amount** |
| ------------------- | -------------- | ---------- |
| London              | Jan            | 5000       |
| London              | Feb            | 3000       |
| New York            | Jan            | 7000       |
| New York            | Feb            | 8500       |
| London              | Jan            | 2000       |

```sql
SELECT 
    office_location,
    SUM(CASE WHEN sale_month = 'Jan' THEN amount ELSE 0 END) AS january_sales,
    SUM(CASE WHEN sale_month = 'Feb' THEN amount ELSE 0 END) AS february_sales,
    SUM(amount) AS total_annual
FROM sales_records
GROUP BY office_location;
```

Output:

| **office_location** | **january_sales** | **february_sales** | **total_annual** |
| ------------------- | ----------------- | ------------------ | ---------------- |
| **London**          | 7000              | 3000               | 10000            |
| **New York**        | 7000              | 8500               | 15500            |
>[!warning]
>Note the difference when using `COUNT()` with `CASE`  as opposed to using `SUM()` below:
>
>The difference occurs because `COUNT()` only counts non-null values, while `SUM()` sums all non-null values.
> By specifying `ELSE 0` for `SUM()` , we ensure null values are considered as 0. 
>For `COUNT()` , count of null values is irrelevant, so we choose to ignore it.

<u><b>Scenario</b></u>: __Customer Support Ticket Audit__
Imagine you are managing a support team. You have a table of tickets, and you want to see how many were **Resolved**, **Pending**, or **Escalated** for each agent.

Table `support_tickets`:

|**agent_name**|**status**|**priority**|
|---|---|---|
|Sarah|Resolved|Low|
|Mike|Pending|High|
|Sarah|Escalated|High|
|Mike|Resolved|Medium|
|Sarah|Resolved|Medium|
|Sarah|Pending|Low|

```sql
SELECT 
    agent_name,
    COUNT(CASE WHEN status = 'Resolved' THEN 1 ELSE NULL END) AS resolved_count,
    COUNT(CASE WHEN status = 'Pending' THEN 1 ELSE NULL END) AS pending_count,
    COUNT(CASE WHEN status = 'Escalated' THEN 1 ELSE NULL END) AS escalated_count,
    COUNT(*) AS total_tickets
FROM support_tickets
GROUP BY agent_name;
```

Output:

| **agent_name** | **resolved_count ** | **pending_count ** | **escalated_count ** | **total_tickets** |
| -------------- | ------------------- | ------------------ | -------------------- | ----------------- |
| **Sarah**      | 2                   | 1                  | 1                    | 4                 |
| **Mike**       | 1                   | 1                  | 0                    | 2                 |
## Sorting in non-Alphabetical order
<u><b>Scenario</b></u>: __Project Management Priorities__
Imagine you have a table called `tasks`. If you sort by the `Priority` column alphabetically, **"High"** would come after **"Critical,"** but **"Low"** would incorrectly appear before **"Medium."**
We want to force this specific logical order:
1. **Critical** (Most Urgent)
2. **High**
3. **Medium**
4. **Low** (Least Urgent)

Table: `tasks`

| **TaskID** | **TaskName**     | **Priority** |
| ---------- | ---------------- | ------------ |
| 101        | Server Migration | Medium       |
| 102        | Fix Login Bug    | Critical     |
| 103        | Update Docs      | Low          |
| 104        | Security Patch   | High         |

```sql
SELECT TaskID, TaskName, Priority
FROM Tasks
ORDER BY 
    CASE Priority
        WHEN 'Critical' THEN 1
        WHEN 'High'     THEN 2
        WHEN 'Medium'   THEN 3
        WHEN 'Low'      THEN 4
        ELSE 5 -- Handles any unexpected values
    END ASC;
```

Output:

| **TaskID** | **TaskName**     | **Priority** | **(Hidden Sort Value)** |
| ---------- | ---------------- | ------------ | ----------------------- |
| 102        | Fix Login Bug    | **Critical** | _1_                     |
| 104        | Security Patch   | **High**     | _2_                     |
| 101        | Server Migration | **Medium**   | _3_                     |
| 103        | Update Docs      | **Low**      | _4_                     |
# COALESCE
+ Returns the first of its arguments that is **not null**
+ Null is returned only if all arguments are null.
+ The arguments must all be convertible to a common data type, which will be the type of the result
```sql
--This returns `description` if it is not null, otherwise `short_description` if it is not null, otherwise `(none)`.
SELECT COALESCE(description, short_description, '(none)') ...
```
## Default value for NULL
**Scenario**: You are building a profile page. Some users haven't filled out their "Bio" or "Middle Name." Instead of showing a blank space or the word "null," you want to show a friendly placeholder.

Table: `users`

| user_id | first_name | middle_name | last_name |     |
| :------ | :--------- | :---------- | :-------- | --- |
| 1       | Jane       | NULL        | Smith     |     |
| 2       | Bob        | Ray         | Jones     |     |

```sql
SELECT 
    first_name,
    COALESCE(middle_name, 'N/A') AS middle_name,
    last_name
FROM users;
```


| first_name | middle_name | last_name |
| ---------- | ----------- | --------- |
| Jane       | N/A         | Smith     |
| Bob        | Ray         | Jones     |

## Fetching by priority
**Scenario:** A CRM system stores multiple ways to reach a customer: `work_phone`, `cell_phone`, and `home_phone`. You want to pull the "best" available number, prioritizing the cell phone first, then work, then home.

| customer | cell_phone | work_phone | home_phone |     |
| :------- | :--------- | :--------- | :--------- | --- |
| Alice    | NULL       | 555-0102   | 555-0103   |     |
| Bryan    | 555-7777   | 555-8888   | NULL       |     |
| Chloe    | NULL       | NULL       | 555-9999   |     |

```sql
SELECT 
    customer,
    COALESCE(cell_phone, work_phone, home_phone, 'No Number Found') AS primary_contact
FROM customers;
```


| customer | primary_contact |
| -------- | --------------- |
| Alice    | 555-01012       |
| Bryan    | 555-7777        |
| Chloe    | 555-9999        |
## Handling Math with NULLs
**Scenario:** You are calculating total compensation (`salary` + `bonus`). In SQL, `5000 + NULL` results in `NULL`. To fix this, you treat a missing bonus as $0.

| employee | salary | bonus |     |
| :------- | :----- | :---- | --- |
| Sarah    | 90000  | 5000  |     |
| Dave     | 85000  | NULL  |     |
```sql
SELECT 
    employee,
    salary + COALESCE(bonus, 0) AS total_pay
FROM employees;
```


| employee | total_pay |
| -------- | --------- |
| Sarah    | 95000     |
| Dave     | 85000     |
# NULLIF
+  returns a null value if _`value1`_ equals _`value2`_; otherwise it returns _`value1`.
+ The two arguments must be of comparable types
```sql
NULLIF(value1, value2)
```

## Prevent Division by Zero
**Scenario:** You are calculating a success rate or ratio. If the denominator is `0`, SQL will throw an error and stop the entire query. By turning `0` into `NULL`, the result of the division becomes `NULL` instead of a crash.

| campaign    | clicks | impressions |     |
| :---------- | :----- | :---------- | --- |
| Summer_Sale | 50     | 1000        |     |
| New_Draft   | 0      | 0           |     |

```sql
SELECT 
    campaign,
    clicks / NULLIF(impressions, 0) AS click_through_rate
FROM ads;
```


| campaign    | click_through_rate |
| ----------- | ------------------ |
| Summer_Sale | 0.05               |
| New_Draft   | NULL               |
## Make Empty Strings NULL
**Scenario:** A web form sends empty strings (`''`) to your database instead of proper `NULL` values. This makes `IS NULL` checks fail. You want to treat those empty strings as true `NULLs`.

| user_id | phone_number |     |
| :------ | :----------- | --- |
| 1       | '555-1234'   |     |
| 2       | ''           |     |
```sql
SELECT 
    user_id,
    NULLIF(phone_number, '') AS cleaned_phone
FROM users;
```


| user_id | cleaned_phone |
| ------- | ------------- |
| 1       | 555-1234      |
| 2       | NULL          |
# GREATEST/LEAST
+ select the maximum or minimum value from a list of columns or expressions.
+ Unlike `MAX()/MIN()`, it works **horizontally** across a single row.
## Find max/min in a row
**Scenario:** You have a table that tracks different types of activity (last login, last post, last profile edit). You want to find the most recent time the user was active in _any_ capacity.

| user_id | last_login | last_post  | last_edit  |     |
| :------ | :--------- | :--------- | :--------- | --- |
| 1       | 2024-01-01 | 2024-02-15 | 2023-12-20 |     |
| 2       | 2024-03-10 | NULL       | 2024-03-12 |     |
```sql
SELECT 
    user_id,
    GREATEST(last_login, last_post, last_edit) AS most_recent_activity
FROM user_activity;
```


| user_id | most_recent_activity |
| ------- | -------------------- |
| 1       | 2024-02-15           |
| 2       | 2024-03-12           |

## Enforce floor/ceil value
**Scenario:** You are running a promotion where products are 50% off, but you have a rule that no product can ever be sold for less than $5.00 (the floor).

| product        | original_price | promo_price (50%) |     |
| :------------- | :------------- | :---------------- | --- |
| Premium Widget | 20.00          | 10.00             |     |
| Cheap Bolt     | 8.00           | 4.00              |     |
```sql
SELECT 
    product,
    GREATEST(original_price * 0.5, 5.00) AS final_price
FROM products;
```


| product        | final_price |
| -------------- | ----------- |
| Premium Widget | 10.00       |
| Cheap Bolt     | 5.00        |
