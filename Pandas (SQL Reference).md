---
tags:
  - "#python"
  - "#data-science"
  - "#de"
  - "#pandas"
---
# Prerequisite
[SQL_Basics](SQL_Basics.md)
# Creating DataFrame
```python
import pandas as pd
# Option 1
data = [
    ['0', 'Y', 'N'], 
    ['1', 'Y', 'Y'], 
    ['2', 'N', 'Y'], 
    ['3', 'Y', 'Y'], 
    ['4', 'N', 'N']
]
products = pd.DataFrame(data, columns=['product_id', 'low_fats', 'recyclable'])\
    .astype({'product_id':'int64', 'low_fats':'category', 'recyclable':'category'})

# Option 2
products_alt = pd.DataFrame(
    {
        'product_id' : ['0', '1', '2', '3', '4'],
        'low_fats' : ['Y', 'Y', 'N', 'Y', 'N'],
        'recyclable': ['N', 'Y', 'Y', 'Y', 'N']
    }
).astype({'product_id':'int64', 'low_fats':'category', 'recyclable':'category'})
```


|       | product_id | low_fats | recyclable |
| ----- | ---------- | -------- | ---------- |
| **0** | 0          | Y        | N          |
| **1** | 1          | Y        | Y          |
| **2** | 2          | N        | Y          |
| **3** | 3          | Y        | Y          |
| **4** | 4          | N        | N          |
>[!important] 
>All operations unless stated otherwise are __NOT__ inplace. 
>Use kwarg `inplace=True` to perform the operation inplace.

# `LIMIT` Clause

```python
# First 5 rows
products.head()

# First n rows
products.head(2)
```

|     | product_id | low_fats | recyclable |
| :-- | ---------- | -------- | ---------- |
| **0**   | 0          | Y        | N          |
| **1**   | 1          | Y        | Y          |
```python
# Last 5 rows (default)
products.tail()

# Last n rows
products.tail(2)
```

|     | product_id | low_fats | recyclable |
| --- | ---------- | -------- | ---------- |
| **3**   | 3          | Y        | Y          |
| **4**   | 4          | N        | N          |

# `SELECT` Clause
## Select all columns
  ```sql
  SELECT * FROM products;
  ```

  ```python
  df
  ```
## Select + Limit + Offset
  ```sql
  SELECT * FROM products LIMIT 3 OFFSET 2;
  ```

  ```python
  df[2:6] # df[2:(2 + 3)]
  ```
  
## Select one column
```sql
SELECT low_fats FROM products;
```

```python
df['low_fats']
# or
df.low_fats
```

|     | low_fats |
| --- | -------- |
| **0**   | Y        |
| **1**   | Y        |
| **2**   | N        |
| **3**   | Y        |
| **4**   | N        |
```python
# axis = 1 -> subset using column names
# filter() filters based on index values or column names, not content!
df.filter(['low_fats'], axis = 1)
```
## Select multiple columns
```sql
SELECT product_id, low_fats FROM products;
```

```python
df['product_id', 'low_fats']('product_id',%20'low_fats')
```

|       | product_id | low_fats |
| ----- | ---------- | -------- |
| **0** | 0          | Y        |
| **1** | 1          | Y        |
| **2** | 2          | N        |
| **3** | 3          | Y        |
| **4** | 4          | N        |
```python
# axis = 1 -> subset using column names
# filter() filters based on index values or column names, not content!
df.filter(['product_id','low_fats'], axis = 1)
```
# Label Based Indexing (.loc\[ \])
It allows us to access rows and columns by their index labels.
	In this example `Name` is the index of the dataframe.

| **Name**          | **Age** | **College**       | **Salary** |
| ----------------- | ------- | ----------------- | ---------- |
| **Avery Bradley** | 25.0    | Texas             | 7730337.0  |
| **Jae Crowder**   | 25.0    | Marquette         | 6796117.0  |
| **John Holland**  | 27.0    | Boston University | NaN        |
| **R.J. Hunter**   | 22.0    | Georgia State     | 1148640.0  |
| **Jonas Jerebko** | 29.0    | NaN               | 5000000.0  |
## Select single row
```python
df.loc['Avery Bradley']
```

| **Name**          | **Age** | **College** | **Salary** |
| ----------------- | ------- | ----------- | ---------- |
| **Avery Bradley** | 25.0    | Texas       | 7730337.0  |
>[!hint] Alternative: filter()
>```python
>df.filter(["Avery Bradley"], axis=0)
>```
## Select multiple rows
```python
df.loc["Avery Bradley", "R.J. Hunter"]("Avery%20Bradley",%20"R.J.%20Hunter")
```

| **Name**          | **Age** | **College**   | **Salary** |
| ----------------- | ------- | ------------- | ---------- |
| **Avery Bradley** | 25.0    | Texas         | 7730337.0  |
| **R.J. Hunter**   | 22.0    | Georgia State | 1148640.0  |
>[!hint] Alternative: filter()
>```python
>df.filter(["Avery Bradley", "R.J. Hunter"], axis=0)
>```
## Select Specific Rows and Columns
```python
df.loc["Avery Bradley", "R.J. Hunter"]("Avery%20Bradley",%20"R.J.%20Hunter")
```

| **Name**          | **Age** | **College**   |
| ----------------- | ------- | ------------- |
| **Avery Bradley** | 25.0    | Texas         |
| **R.J. Hunter**   | 22.0    | Georgia State |
## Select All Rows and Specific Columns
```python
df.loc[:, ['Age', 'College']]
```

| **Name**          | **Age** | **College**       |
| ----------------- | ------- | ----------------- |
| **Avery Bradley** | 25.0    | Texas             |
| **Jae Crowder**   | 25.0    | Marquette         |
| **John Holland**  | 27.0    | Boston University |
| **R.J. Hunter**   | 22.0    | Georgia State     |
| **Jonas Jerebko** | 29.0    | NaN               |
# Position Based Indexing (.iloc\[ \])
It allows us to access rows and columns by their integer positions.
	`Name` is index of the dataframe

| **Name**          | **Age** | **College**       | **Salary** |
| ----------------- | ------- | ----------------- | ---------- |
| **Avery Bradley** | 25.0    | Texas             | 7730337.0  |
| **Jae Crowder**   | 25.0    | Marquette         | 6796117.0  |
| **John Holland**  | 27.0    | Boston University | NaN        |
| **R.J. Hunter**   | 22.0    | Georgia State     | 1148640.0  |
| **Jonas Jerebko** | 29.0    | NaN               | 5000000.0  |
## Select Single Row
```python
df.iloc[3]
```

| **Name**          | **Age** | **College**   | **Salary** |
| ----------------- | ------- | ------------- | ---------- |
| **R.J. Hunter**   | 22.0    | Georgia State | 1148640.0  |
## Select multiple rows
```python
df.iloc[2, 4](2,%204)
```

| **Name**          | **Age** | **College**       | **Salary** |
| ----------------- | ------- | ----------------- | ---------- |
| **John Holland**  | 27.0    | Boston University | NaN        |
| **Jonas Jerebko** | 29.0    | NaN               | 5000000.0  |
## Select specific rows and columns
```python
df.iloc[2, 4](2,%204)
```
Notice the 2nd column `Salary` appears first.

| **Name**          | **Salary** | **Age** |
| ----------------- | ---------- | ------- |
| **John Holland**  | NaN        | 27.0    |
| **Jonas Jerebko** | 5000000.0  | 29.0    |
## Select all rows and specific columns
```python
df.iloc[:, [2, 0]]
```

| **Name**          | **Salary** | **Age** |
| ----------------- | ---------- | ------- |
| **Avery Bradley** | 7730337.0  | 25.0    |
| **Jae Crowder**   | 6796117.0  | 25.0    |
| **John Holland**  | NaN        | 27.0    |
| **R.J. Hunter**   | 1148640.0  | 22.0    |
| **Jonas Jerebko** | 5000000.0  | 29.0    |
# `WHERE` clause

## Boolean Indexing
It allows you to **filter rows based on conditions**.
	The condition evaluates to __True__ or __False__ for each row.
	The rows where the condition is __True__ are selected, while the rest are ignored.

| Operator | Meaning            |
| -------- | ------------------ |
| <        | Less than          |
| >        | More than          |
| <=       | Less than equal to |
| >=       | More than equal to |
| ==       | Equal to           |
| !=       | Not equal to       |

`World` Table:

|       | **name**    | **continent** | **area** | **population** | **gdp**      |
| ----- | ----------- | ------------- | -------- | -------------- | ------------ |
| **0** | Afghanistan | Asia          | 652230   | 25500100       | 20343000000  |
| **1** | Albania     | Europe        | 28748    | 2831741        | 12960000000  |
| **2** | Algeria     | Africa        | 2381741  | 37100000       | 188681000000 |
| **3** | Andorra     | Europe        | 468      | 78115          | 3712000000   |
| **4** | Angola      | Africa        | 1246700  | 20609294       | 100990000000 |
### __Filter on one condition__
```sql
SELECT *
FROM world
WHERE area > 65000;
```

```python
df[df['area'] > 65000]
# or 
df.loc[df.area > 65000]
```

|       | **name**    | **continent** | **area** | **population** | **gdp**      |
| ----- | ----------- | ------------- | -------- | -------------- | ------------ |
| **0** | Afghanistan | Asia          | 652230   | 25500100       | 20343000000  |
| **2** | Algeria     | Africa        | 2381741  | 37100000       | 188681000000 |
| **4** | Angola      | Africa        | 1246700  | 20609294       | 100990000000 |
### __Filtering with multiple conditions__

| Operator | Meaning |
| -------- | ------- |
| &        | AND     |
| \|       | OR      |
| ~        | NOT     |
| ^        | XOR     |
>[!warning] Multiple conditions must be enclosed in parentheses!
>Ex: `(df['A'] > 2) & (df['B'] < 3)`.

```sql
SELECT name, population, area
FROM World
WHERE area >= 3000000
   OR population >= 25000000;
```

```python
df[ (df['area'] >= 3000000) | (df['population'] >= 25000000) ]
	['name', 'population', 'area']('name',%20'population',%20'area')
# or
df.loc[(df['area'] >= 3000000) | (df['population'] >= 25000000), ['name', 'population', 'area']]
```

|     | name        | population | area    |
| --- | ----------- | ---------- | ------- |
| **0**   | Afghanistan | 25500100   | 652230  |
| **2**   | Algeria     | 37100000   | 2381741 |
## `df.query` method
Filter rows in a DataFrame using a string expression. 
Instead of juggling multiple `&` (and), `|` (or), and parentheses, you can just write your condition like plain English.
It uses `numexpr` under the hood, making it ==more memory-efficient for very large datasets==.

>[!tip] You can also query on index labels in the same way as columns


|       | First Name | Age | City    |
| ----- | ---------- | --- | ------- |
| **0** | Alice      | 25  | NY      |
| **1** | Bob        | 30  | LA      |
| **2** | Charlie    | 35  | NY      |
| **3** | David      | 40  | Chicago |
### __Filter on one condition__
```python
df.query('Age > 30')
```

|     | First Name | Age | City    |
| --- | ---------- | --- | ------- |
| 2   | Charlie    | 35  | NY      |
| 3   | David      | 40  | Chicago |
### __Filter on multiple conditions__ (`and`**,** `or`**,** `not`)
```python
df.query('Age > 25 and City == "NY"')
```
Note the double quotes around NY, they signify that NY is a string literal.
	
|       | First Name | Age | City |
| ----- | ---------- | --- | ---- |
| __2__ | Charlie    | 35  |      |
### __Filter using variable__
```python
city_name = 'LA'
df.query('City == @city_name')
```

|       | First Name | Age | City |
| ----- | ---------- | --- | ---- |
| __1__ | Bob        | 30  | LA   |
   > [!Warning] if column name has space in it, it must be surrounded by backticks!
   > ```python
   > df.query('`First Name` == "Alice"')
   > ```
# `IN` operator
The `isin()` method  is used to __check (not filter)__ whether each element in a `DataFrame` or `Series` is contained in a specified set of values.
	It returns a **boolean mask** (==a structure of the same shape== with `True` or `False` values)
>[!tip] `isin()` is significantly faster and more readable than using multiple "OR" (`|`) conditions.

|     | City     | Sales |
| --- | -------- | ----- |
| **0**   | New York | 100   |
| **1**   | London   | 200   |
| **2**   | Paris    | 150   |
| **3**   | Tokyo    | 300   |
##  __Filter on one column__
```sql
SELECT * 
FROM sales
WHERE city IN ('London', 'Tokyo');
```

```python
# Boolean indexing
sales[sales['City'].isin(['London', 'Tokyo'])]

# query
sales.query('City in ("London", "Tokyo")')
```

|     | City   | Sales |
| --- | ------ | ----- |
| **1**   | London | 200   |
| **3**   | Tokyo  | 300   |
>[!info]- Detailed explanation 
> ```python
> sales['City'].isin(['London', 'Tokyo']
> ```
> returns the Series mask
>
>|  | City  | 
| ----- | ----- | 
| **0** | False | 
| **1** | True  | 
| **2** | False | 
| **3** | True  | 
|which does Boolean indexing inside `sales[...]` to filter out rows where _city_ is `False`.

## __Filter on multiple columns__
```sql
SELECT *
FROM sales
WHERE city IN ('London', 'Paris') OR sales IN (100, 150);
```

```python
filter_dict = {'City': ['Paris', 'London'], 'Sales': [100, 150]}
# boolean indexing
sales[sales.isin(filter_dict).any(axis=1)]
# query
sales.query('City in @filter_dict["City"] or Sales in @filter_dict["Sales"]')
```

|     | City     | Sales |
| --- | -------- | ----- |
| **0**   | New York | 100   |
| **1**   | London   | 200   |
| **2**   | Paris    | 150   |
>[!info]- Detailed Explanation
>```python
>sales.isin(filter_dict)
>```
>returns the following mask:
>
>| |City|Sales|
|---|---|---|
|__0__|False|True|
|__1__|True|False|
|__2__|True|True|
|__3__|False|False|
> Notice that with this mask, it is unclear how we should filter rows. Should we keep a row if both conditions are true? if one condition is true?
> This is where `.any(axis=1)` comes in. It checks if ==any condition is true== on row level. if it is, it returns `True` else `False` i.e. it squashes the mask down into a Series mask.
> 
>|  |   | 
| ----- | ----- | 
| **0** | True | 
| **1** | True  | 
| **2** | True | 
| **3** | False  | 
> which `sales[...]` uses to perform boolean indexing.

```sql
SELECT *
FROM sales
WHERE city IN ('London', 'Paris') AND sales IN (100, 150);
```

```python
filter_dict = {'City': ['Paris', 'London'], 'Sales': [100, 150]}
# boolean indexing
sales[sales.isin(filter_dict).all(axis=1)]

# query
sales.query('City in @filter_dict["City"] and Sales in @filter_dict["Sales"]')
```

|       | City  | Sales |
| ----- | ----- | ----- |
| __2__ | Paris | 150   |
## `NOT IN` operator

```sql
SELECT * 
FROM sales
WHERE city NOT IN ('London', 'Tokyo')
```

```python
# boolean indexing
sales[~sales['City'].isin(['London', 'Tokyo'])]

# query
sales.query('City not in ("London", "Tokyo")')
```

# `LIKE` operator
|     | email             | status        |
| --- | ----------------- | ------------- |
| **0**   | Alice@gmail.com   | Active_User   |
| **1**   | bob@yahoo.com     | Inactive_User |
| **2**   | CHARLIE@gmail.com | active_admin  |
| **3**   | david@outlook.com | Pending       |
| **4**   | Eve@GMAIL.com     | Archived_2023 |
## `%word%` pattern

```sql
SELECT *
FROM email_status
WHERE email LIKE '%gmail%';
```

```python
# Case sensitive
email_status[email_status.email.str.contains('gmail')]
# Case insensitive
email_status[email_status.email.str.contains('gmail', case=False)]
```

|     | email             | status       |
| --- | ----------------- | ------------ |
| **0**   | Alice@gmail.com   | Active_User  |
| **2**   | CHARLIE@gmail.com | active_admin |
```python
# Case sensitive
email_status.query('email.str.contains("gmail")')
# Case insensitive
email_status.query('email.str.contains("gmail", case=False)')
```
## `word%` pattern

```sql
SELECT *
FROM email_status
WHERE email LIKE 'CH%'
```

```python
# case sensitive
email_status[email_status.email.str.startswith('CH')]

# case insensitive
email_status[email_status.email.str.contains('^ch', case=False)]
```

|       | email             | status       |
| ----- | ----------------- | ------------ |
| __2__ | CHARLIE@gmail.com | active_admin |
```python
# Case sensitive
email_status.query('email.str.startswith("CH")')
# Case insensitive
email_status.query('email.str.contains("^ch", case=False)')
```
## `%word` pattern

```sql
SELECT *
FROM email_status
WHERE status LIKE '%ser'
```

```python
# case sensitive
email_status[email_status.status.str.endswith('ser')]
# case insensitive
email_status[email_status.status.str.contains('SeR$', case=False)]
```

|     | email           | status        |
| --- | --------------- | ------------- |
| **0**   | Alice@gmail.com | Active_User   |
| **1**   | bob@yahoo.com   | Inactive_User |
```python
# Case sensitive
email_status.query('email.str.endswith("ser")')
# Case insensitive
email_status.query('email.str.contains("SeR$", case=False)')
```
## Regex matching
```sql
SELECT *
FROM email_status
WHERE email LIKE 'A%' OR email LIKE 'C%';
```

```python
pattern = '^(A|C)'
email_status[email_status['email'].str.contains(pattern)]
```

|     | email             | status       |
| --- | ----------------- | ------------ |
| **0**   | Alice@gmail.com   | Active_User  |
| **2**   | CHARLIE@gmail.com | active_admin |
```python
# Case sensitive
email_status.query('email.str.contains(@pattern)')
```
# `IS NULL` operator
|     |         | name              | email | age |
| --- | ------- | ----------------- | ----- | --- |
| **0**   | Alice   | alice@gmail.com   | 25.0  |     |
| **1**   | Bob     | NaN               | 30.0  |     |
| **2**   | Charlie | charlie@yahoo.com | NaN   |     |
| **3**   | David   | None              | 22.0  |     |
| **4**   | Eve     |                   | 28.0  |     |
```sql
SELECT *
FROM person
WHERE email IS NULL
```

 `isnull()` is an alias for `isna()` . They are the same function.
```python
person[person['email'].isnull()]
# or
person[person['email'].isna()]
```

|     | name  | email | age  |
| --- | ----- | ----- | ---- |
| **1**   | Bob   | NaN   | 30.0 |
| **3**   | David | None  | 22.0 |
>[!success] Catches `None`, `pd.NaT`, `np.NaN` -- Applies to `isnull()`/`notnull()`

>[!missing] Does not catch empty strings and infinfity `inf`

```sql
SELECT *
FROM person
WHERE age IS NOT NULL
```

`notnull()` is an alias for `notna()`. They are the same function.
```python
person[person['age'].notnull()]
# or
person[person['age'].notna()]
```

|     | name  | email           | age  |
| --- | ----- | --------------- | ---- |
| **0**   | Alice | alice@gmail.com | 25.0 |
| **1**   | Bob   | NaN             | 30.0 |
| **3**   | David | None            | 22.0 |
| **4**   | Eve   |                 | 28.0 |
# `DISTINCT` / `DISTINCT ON`
|       | name    | date       | visit_id |
| ----- | ------- | ---------- | -------- |
| **0** | Alice   | 2023-01-01 | 101      |
| **1** | Bob     | 2023-01-01 | 102      |
| **2** | Alice   | 2023-01-01 | 103      |
| **3** | Bob     | 2023-01-02 | 104      |
| **4** | Charlie | 2023-01-02 | 105      |
| __5__ | Diana   | 2023-01-03 | 106      |
##  Single Column
```sql
SELECT DISTINCT date
FROM visits;
```

```python
# Returns a numpy  array
# Works on Series only
visits['date'].unique()
# O/P:
# array(['2023-01-01', '2023-01-02'], dtype=object)
```

```python
# Returns a Series
# keep = 'First' is default
# Keeps first duplicate value and deletes others
visits['date'].drop_duplicates() 
```

Notice that Rows 0, 1, 2 have same value and rows 3, 4 have the same value. We keep the first duplicate value (0, 3) and discard the rest

|       | date       |
| ----- | ---------- |
| __0__ | 2023-01-01 |
| __3__ | 2023-01-02 |
```python
# Keeps last duplicate value and deletes others
visits['date'].drop_duplicates(keep='last')
```
Notice that Rows 0, 1, 2 have same value and rows 3, 4 have the same value. We keep the last duplicate value (2, 4) and discard the rest

|       | date       |
| ----- | ---------- |
| __2__ | 2023-01-01 |
| __4__ | 2023-01-02 |
```sql
SELECT date
FROM visits
GROUP BY date
HAVING COUNT(*) = 1;
```

```python
# Drop all duplicate values
visits['date'].drop_duplicates(keep=False)
```

|       | date       |
| ----- | ---------- |
| __5__ | 2023-01-03 |
## Multiple columns
```sql
SELECT DISTINCT ON (name, date)
FROM visits;
```

```python
# keep = 'first'
# Keep first duplicate
visits['name', 'date']('name',%20'date').drop_duplicates()
```
Rows 0 and 2 have repeating values for the entire subset (name, date). Here, we keep the first duplicate (row 0)

|     | name    | date       |
| --- | ------- | ---------- |
| **0**   | Alice   | 2023-01-01 |
| **1**   | Bob     | 2023-01-01 |
| **3**   | Bob     | 2023-01-02 |
| **4**   | Charlie | 2023-01-02 |
| **5**   | Diana   | 2023-01-03 |
```python
# keep = 'last'
# Keep last duplicate
visits['name', 'date']('name',%20'date').drop_duplicates(keep='last')
```
Rows 0 and 2 have repeating values for the entire subset (name, date). Here, we keep the last duplicate (row 2)

|     | name    | date       |
| --- | ------- | ---------- |
| 1   | Bob     | 2023-01-01 |
| 2   | Alice   | 2023-01-01 |
| 3   | Bob     | 2023-01-02 |
| 4   | Charlie | 2023-01-02 |
| 5   | Diana   | 2023-01-03 |
```sql
SELECT name, date
FROM visits
GROUP BY name, date
HAVING COUNT(*) > 1;
```

```python
# Drop all duplicates
visits['name', 'date']('name',%20'date').drop_duplicates(keep=False)
```

|     | name    | date       |
| --- | ------- | ---------- |
| **1**   | Bob     | 2023-01-01 |
| **3**   | Bob     | 2023-01-02 |
| **4**   | Charlie | 2023-01-02 |
| **5**   | Diana   | 2023-01-03 |
## Important Note
>[!warning] Notice that the results only contain the subset of columns that it was called on.
>What if we want to find the first visitor every day:
>```sql
>SELECT DISTINCT date, visit_id
>FROM visits;
>```
>
>We can use alternative syntax for `drop_duplicates`:
>```python
>visits.drop_duplicates(subset=['date'])['date', 'visit_id']('date',%20'visit_id')
>```
>In the __previous syntax__, we first selected a subset and then performed the operation, which limits the projected data to that subset.
>
>In the __alternative syntax__, `drop_duplicates` internally works on the subset but we still retain access to the entire dataframe.

## Count of unique values
```sql
SELECT 
    COUNT(DISTINCT name)
    COUNT(DISTINCT date)
FROM visits;
```

`nunique()` gives the count of unique values across:
	columns -> (__axis=0__)
	rows (__axis=1__)
	
>[!WARNING] `nunique()` does not count `Nan` values by default. Set kwarg `dropna=False` to count `Nan`.

```python
# Using with Series
visits['date'].nunique()
# O/P:
# 3 --- Returns a scalar value

# Using with DataFrame
# axis = 0 (default) -> unique count across columns
visits['name', 'date']('name',%20'date').nunique() # Returns a Series
```
Here _name_ and _date_ are __index__ values of the Series.

|      |     |
| ---- | --- |
| **name** | 3   |
| **date** | 4   |
`value_counts()` gives a more detailed breakdown of counts on all or a subset of columns.
>[!warning] `value_counts()` does not count `Nan` values by default. Set kwarg `dropna=False` to count `Nan`.

```sql
SELECT COUNT(*) 
FROM (
    SELECT DISTINCT name, date 
    FROM visits
);
```

```python
# Returns a Series
visits['name', 'date']('name',%20'date').value_counts()
```
Here _name_ and _date_ are both __index__ for the Series.

| name    | date       |     |
| ------- | ---------- | --- |
| **Alice**   | **2023-01-01** | 2   |
| **Bob**     | **2023-01-01** | 1   |
|         | **2023-01-02** | 1   |
| **Charlie** | **2023-01-02** | 1   |
| **Diana**   | **2023-01-03** | 1   |
# `AS` clause
|     | total_bill | tip  | sex    | smoker | day | time   | size |
| --- | ---------- | ---- | ------ | ------ | --- | ------ | ---- |
| **0**   | 16.99      | 1.01 | Female | No     | Sun | Dinner | 2    |
| **1**   | 10.34      | 1.66 | Male   | No     | Sun | Dinner | 3    |
| **2**   | 21.01      | 3.50 | Male   | No     | Sun | Dinner | 3    |
| **3**   | 23.68      | 3.31 | Male   | No     | Sun | Dinner | 2    |
| **4**   | 24.59      | 3.61 | Female | No     | Sun | Dinner | 4    |
## Calculated field
```sql
SELECT tip/total_bill AS tip_rate
FROM tips;
```

`assign()` returns the dataframe + new column as a new dataframe. It is slower due to copying overhead.
`df[col] = value` modifies the dataframe to include new column.
```python
# .assign()
tips.assign(tip_rate=tips.tip / tips.total_bill)

# df[col] = value
tips['tip_rate'] = tips['tip'] / tips['total_bill']
```

|     | total_bill | tip  | sex    | smoker | day | time   | size | tip_rate |
| --- | ---------- | ---- | ------ | ------ | --- | ------ | ---- | -------- |
| **0**   | 16.99      | 1.01 | Female | No     | Sun | Dinner | 2    | 0.059447 |
| **1**   | 10.34      | 1.66 | Male   | No     | Sun | Dinner | 3    | 0.160542 |
| **2**   | 21.01      | 3.50 | Male   | No     | Sun | Dinner | 3    | 0.166587 |
| **3**   | 23.68      | 3.31 | Male   | No     | Sun | Dinner | 2    | 0.139780 |
| **4**   | 24.59      | 3.61 | Female | No     | Sun | Dinner | 4    | 0.146808 |
## Rename column
```sql
SELECT tip, sex as gender
FROM tips;
```

```python
tips['tip', 'sex']('tip',%20'sex').rename(columns = {'sex' : 'gender'})
```

|     | tip  | gender |
| --- | ---- | ------ |
| **0**   | 1.01 | Female |
| **1**   | 1.66 | Male   |
| **2**   | 3.50 | Male   |
| **3**   | 3.31 | Male   |
| **4**   | 3.61 | Female |
# `ORDER BY` clause
|       | product  | price  |
| ----- | -------- | ------ |
| **0** | Laptop   | 1200.0 |
| **1** | Mouse    | NaN    |
| **2** | Monitor  | 300.0  |
| **3** | Keyboard | 100.0  |
| **4** | Webcam   | NaN    |
| __5__ | Laptop   | 300.0  |
## Ascending Order
```sql
SELECT  *
FROM products
ORDER BY price;
```

```python
products.sort_values(by='price')
```
Notice that by default __NULLS are last__.

|       | product  | price  |
| ----- | -------- | ------ |
| **3** | Keyboard | 100.0  |
| **2** | Monitor  | 300.0  |
| __5__ | Laptop   | 300.0  |
| **0** | Laptop   | 1200.0 |
| **1** | Mouse    | NaN    |
| **4** | Webcam   | NaN    |
## Descending Order
```sql
SELECT  *
FROM products
ORDER BY price DESC;
```

```python
products.sort_values(by='price', ascending=False)
```
Notice that by default __NULLS are last__.

|       | product  | price  |
| ----- | -------- | ------ |
| **0** | Laptop   | 1200.0 |
| **2** | Monitor  | 300.0  |
| __5__ | Laptop   | 300.0  |
| **3** | Keyboard | 100.0  |
| **1** | Mouse    | NaN    |
| **4** | Webcam   | NaN    |
## On multiple columns
```sql
SELECT *
FROM products
ORDER BY product ASC, price DESC;
```

```python
products.sort_values(by=['product', 'price'], ascending=[True, False])
```

|     | product  | price  |
| --- | -------- | ------ |
| **3**   | Keyboard | 100.0  |
| **0**   | Laptop   | 1200.0 |
| **5**   | Laptop   | 300.0  |
| **2**   | Monitor  | 300.0  |
| **1**   | Mouse    | NaN    |
| **4**   | Webcam   | NaN    |
## NULLS FIRST 
Default is `NULLS LAST`.
Set kwarg `na_position='first'`.
```sql
SELECT *
FROM products
ORDER BY price ASC NULLS FIRST;
```

```python
products.sort_values(by='price', ascending=True, na_position='first')
```
## Custom sort
```sql
SELECT *
FROM products
ORDER BY LOWER(product)
```

```python
'''
Note the use of .str here. `product` has a dtype of `object` so we
must first convert to `str` to use the string function `lower()`
'''
products.sort_values(by='product', key = lambda x: x.str.lower())
```

|     | product  | price  |
| --- | -------- | ------ |
| **3**   | Keyboard | 100.0  |
| **0**   | Laptop   | 1200.0 |
| **5**   | Laptop   | 300.0  |
| **2**   | Monitor  | 300.0  |
| **1**   | Mouse    | NaN    |
| **4**   | Webcam   | NaN    |
## Sort by index
Here _city_ and _year_ are part of the __index__. _city_ is level 0 and _year_ is level 1

| city        | year | sales |
| ----------- | ---- | ----- |
| **New York**    | **2023** | 100   |
| **Los Angeles** | **2022** | 200   |
| **New York**    | **2022** | 150   |
| **Chicago**     | **NaN**  | 50    |
| **Los Angeles** | **2023** | 300   |
### __Sort by a Specific Level__
```python
sales.sort_index(level=1) # sort by year
```

| **city**        | **year** | sales |
| ----------- | ---- | ----- |
| **Los Angeles** | **2022** | 200   |
| **New York**    | **2022** | 150   |
| **Los Angeles** | **2023** | 300   |
| **New York**    | **2023** | 100   |
| **Chicago**     | **NaN**  | 50    |
### __Sort Different Levels in Different Orders__
```python
sales.sort_index(level=['city', 'year'], ascending=[True, False])
```

| city            | year     | sales |
| --------------- | -------- | ----- |
| **Chicago**     | **NaN**  | 50    |
| **Los Angeles** | **2023** | 300   |
|                 | **2022** | 200   |
| **New York**    | **2023** | 100   |
|                 | **2022** | 150   |
### `NULLS FIRST`

```python
sales.sort_index(level='year', na_position='first')
```

| city        | year | sales |
| ----------- | ---- | ----- |
| **Chicago**     | **NaN**  | 50    |
| **Los Angeles** | **2022** | 200   |
| **New York**    | **2022** | 150   |
| **Los Angeles** | **2023** | 300   |
| **New York**    | **2023** | 100   |
# `GROUP BY` clause

| Function      | Purpose                                            | SQL equivalent                              |
| ------------- | -------------------------------------------------- | ------------------------------------------- |
| sum()         | Total sum of values                                | SUM()                                       |
| mean()        | Arithmetic mean / avg                              | AVG()                                       |
| min() / max() | Identifies smallest and largest values             | MIN() / MAX()                               |
| count()       | Count of __non null__ observations                 | COUNT()                                     |
| size()        | Count of __all__ observations (including __NULL__) | COUNT( \* )                                 |
| nunique()     | Count of unique __non-null__ values                | COUNT( DISTINCT)                            |
| std() / var() | Standard deviation / variance                      | STDDEV() / VARIANCE()                       |
| quantile()    | Percentile (0 - 100) / Quantile (0 - 1)            | PERCENTILE_DISC() / PERCENTILE_CONT()       |
| median()      | Middle value of the dataset                        | PERCENTILE_DISC(0.5) / PERCENTILE_CONT(0.5) |

|       | Department | Employee | Salary | Project_Count | Region |
| ----- | ---------- | -------- | ------ | ------------- | ------ |
| **0** | Sales      | Alice    | 50000  | 2             | North  |
| **1** | Sales      | Bob      | 54000  | 3             | South  |
| **2** | IT         | Charlie  | 70000  | 5             | North  |
| **3** | IT         | David    | 82000  | 4             | North  |
| **4** | HR         | Eve      | 60000  | 2             | South  |
| **5** | Sales      | Frank    | 52000  | 3             | South  |
| **6** | IT         | Grace    | 75000  | 6             | North  |

>[!info]- `groupby()` under the hood
>It follows a ****split-apply-combine**** process:
>- ****Split**** the data into groups ( Lazy evaluation)
>- ****Apply**** some calculation like sum, average etc.
>- ****Combine**** the results into a new table
## Single column aggregate
```sql
SELECT department, AVG(salary)
FROM employees
GROUP BY department
ORDER BY department -- `groupby() sorts by key (default)`
```

```python
employees.groupby('Department')['Salary'].mean() # Returns Series

employees.groupby('Department')['Salary'].agg('mean') # Returns Series

employees.groupby('Department').agg({'Salary':'mean'}) # Returns DataFrame
```

| Department | Salary       |
| ---------- | ------------ |
| **HR**     | 60000.000000 |
| **IT**     | 75666.666667 |
| **Sales**  | 52000.000000 |
>[!tip] If you want the grouping key to be a column, not an index:
>```python
>employees.groupby('Department')['Salary'].mean().reset_index()
>```
## Multiple Grouping Columns
```sql
SELECT department, AVG(salary)
FROM employees
GROUP BY department, region
ORDER BY department, region
```

```python
employees.groupby(['Department', 'Region'])['Salary'].mean()

employees.groupby(['Department', 'Region'])['Salary'].agg('mean')

employees.groupby(['Department', 'Region']).agg({'Salary': 'mean'})
```

|                |            | Salary       |
| -------------- | ---------- | ------------ |
| **Department** | **Region** |              |
| **HR**             | **South**      | 60000.000000 |
| **IT**             | **North**      | 75666.666667 |
| **Sales**          | **North**      | 50000.000000 |
|                | **South**      | 53000.000000 |
## Multiple aggregations on single column
```python
employees.groupby(['Department', 'Region'])['Salary'].agg(['mean', 'size', 'min'])
```

|            |        | mean         | size | min   |
| ---------- | ------ | ------------ | ---- | ----- |
| **Department** | **Region** |              |      |       |
| **HR**         | **South**  | 60000.000000 | 1    | 60000 |
| **IT**         | **North**  | 75666.666667 | 3    | 70000 |
| **Sales**      | **North**  | 50000.000000 | 1    | 50000 |
|            | **South**  | 53000.000000 | 2    | 52000 |
## Per column aggregation
```python
employees.groupby(['Department', 'Region']).agg({'Salary': 'mean', 'Project_Count': 'size'})
```

|            |        | Salary       | Project_Count |
| ---------- | ------ | ------------ | ------------- |
| **Department** | **Region** |              |               |
| **HR**         | **South**  | 60000.000000 | 1             |
| **IT**         | **North**  | 75666.666667 | 3             |
| **Sales**      | **North**  | 50000.000000 | 1             |
|            | **South**  | 53000.000000 | 2             |
## Custom aggregation methods
```python
# Salary difference
employees.groupby('Department')['Salary'].apply(lambda x: x.max() - x.min())
```


| Department | Salary |
| ---------- | ------ |
| **HR**         | 0      |
| **IT**         | 12000  |
| **Sales**      | 4000   |
## `HAVING` clause

```python
# filter() returns boolean mask so getting all the rows is easier.
# In SQL we would have to do a cte join for the same result
employees.groupby('Department').filter(lambda x: x['Salary'].sum() > 150000)
```

|       | Department | Employee | Salary | Project_Count | Region |
| ----- | ---------- | -------- | ------ | ------------- | ------ |
| **0** | Sales      | Alice    | 50000  | 2             | North  |
| **1** | Sales      | Bob      | 54000  | 3             | South  |
| **2** | IT         | Charlie  | 70000  | 5             | North  |
| **3** | IT         | David    | 82000  | 4             | North  |
| **5** | Sales      | Frank    | 52000  | 3             | South  |
| **6** | IT         | Grace    | 75000  | 6             | North  |
# Joins
## Comparison of functions used
`pd.merge()` 
	It allows joins on both columns (default) and indexes.
	Flexible but can be __slower on large data__
	==Limited to joining two objects at a time==
`join()`
	Join on indexes (default)
	Optimized and __faster for index-based joins__
	==Join multiple DataFrames at once==.
## Join on Columns
### Inner Join
<center><b>Orders</b></center>

|       | order_id | date       | amount |
| ----- | -------- | ---------- | ------ |
| **0** | 101      | 2023-01-01 | 250    |
| **1** | 102      | 2023-01-02 | 450    |
| **2** | 103      | 2023-01-03 | 100    |
| **3** | 104      | 2023-01-04 | 300    |
<center><b>Payments</b></center>

|       | transaction_id | date       | method |
| ----- | -------------- | ---------- | ------ |
| **0** | 101            | 2023-01-10 | Credit |
| **1** | 102            | 2023-01-11 | Cash   |
| **2** | 103            | 2023-01-12 | Credit |
| **3** | 105            | 2023-01-15 | PayPal |

```sql
SELECT *
FROM orders o
JOIN payments p
	ON o.order_id = p.transaction_id;
```

```python
pd.merge(
    orders, 
    payments, 
    left_on='order_id',      # Key in orders
    right_on='transaction_id', # Key in payments
    how='inner',             # Type of join
    suffixes=('_order', '_pay') # Rename conflicting 'date' columns
)
```

|     | order_id | date_order | amount | transaction_id | date_pay   | method |
| --- | -------- | ---------- | ------ | -------------- | ---------- | ------ |
| **0**   | 101      | 2023-01-01 | 250    | 101            | 2023-01-10 | Credit |
| **1**   | 102      | 2023-01-02 | 450    | 102            | 2023-01-11 | Cash   |
| **2**   | 103      | 2023-01-03 | 100    | 103            | 2023-01-12 | Credit |
>[!tip]- Cleaning up after `merge()`
>In above example, notice that the join column repeats twice (one for payments, one for orders). 
>Usually, you'll want to [drop](#`DELETE`%20clause) one immediately to keep the data tidy.
>```python
># Drop the redundant transaction_id column
merged_df = merged_df.drop(columns=['transaction_id'])
>```

>[!faq]- What happens if we don't provide suffixes?
>date_order $\rightarrow$ date_x
>date_pay $\rightarrow$ date_y
>
>|       | order_id | date_x     | amount | transaction_id | date_y     | method |
| ----- | -------- | ---------- | ------ | -------------- | ---------- | ------ |
| **0** | 101      | 2023-01-01 | 250    | 101            | 2023-01-10 | Credit |
| **1** | 102      | 2023-01-02 | 450    | 102            | 2023-01-11 | Cash   |
| **2** | 103      | 2023-01-03 | 100    | 103            | 2023-01-12 | Credit |

>[!info]- Join on same column name / `USING`
>Assume join key is `id` in both dataframes
>```python
>pd.merge(
orders, 
payments, 
on='id',      # Key in orders and payments
how='inner',             # Type of join
suffixes=('_order', '_pay') # Rename conflicting 'date' columns
)
>```

^1166de

### Left Join
<center><b>Customers</b></center>

|       | customer_id | name    |
| ----- | ----------- | ------- |
| **0** | 1           | Alice   |
| **1** | 2           | Bob     |
| **2** | 3           | Charlie |

^1b4929

<center><b>Orders</b></center>

|     | order_id | cust_id | amount |
| --- | -------- | ------- | ------ |
| **0**   | 101      | 1       | 50     |
| **1**   | 102      | 2       | 150    |
| **2**   | 103      | 4       | 200    |
```sql
SELECT *
FROM customers c
LEFT JOIN orders o
	ON c.customer_id = o.cust_id;
```

```python
pd.merge(
    customers, 
    orders, 
    left_on='customer_id', 
    right_on='cust_id', 
    how='left'
)
```

|       | customer_id | name    | order_id | cust_id | amount |
| ----- | ----------- | ------- | -------- | ------- | ------ |
| **0** | 1           | Alice   | 101.0    | 1.0     | 50.0   |
| **1** | 2           | Bob     | 102.0    | 2.0     | 150.0  |
| **2** | 3           | Charlie | NaN      | NaN     | NaN    |
### Right Join
[](.md#^1b4929|Sample%20Data)
```sql
SELECT *
FROM customers c
RIGHT JOIN orders o
	ON c.customer_id = o.cust_id;
```

```python
pd.merge(
    customers, 
    orders, 
    left_on='customer_id', 
    right_on='cust_id', 
    how='right'
)
```

|       | customer_id | name  | order_id | cust_id | amount |
| ----- | ----------- | ----- | -------- | ------- | ------ |
| **0** | 1.0         | Alice | 101      | 1       | 50     |
| **1** | 2.0         | Bob   | 102      | 2       | 150    |
| **2** | NaN         | NaN   | 103      | 4       | 200    |
### Outer Join
[](.md#^1b4929|Sample%20Data)
```sql
SELECT *
FROM customers c
OUTER JOIN orders o
	ON c.customer_id = o.cust_id
```

```python
pd.merge(
    customers, 
    orders, 
    left_on='customer_id', 
    right_on='cust_id', 
    how='outer', 
    indicator=True
)
```

Setting `indicator=True` results in addition of a new column `_merge` which indicates if the row is present in both the tables (`both`), only in the left table (`left_only`) or only in the right table (`right_only`).

|     | customer_id | name    | order_id | cust_id | amount | _merge     |
| --- | ----------- | ------- | -------- | ------- | ------ | ---------- |
| **0**   | 1.0         | Alice   | 101.0    | 1.0     | 50.0   | both       |
| **1**   | 2.0         | Bob     | 102.0    | 2.0     | 150.0  | both       |
| **2**   | 3.0         | Charlie | NaN      | NaN     | NaN    | left_only  |
| **3**   | NaN         | NaN     | 103.0    | 4.0     | 200.0  | right_only |
### Left Anti Join
[](.md#^1b4929|Sample%20Data)
```sql
SELECT *
FROM customers c
OUTER JOIN orders o
	ON c.customer_id = o.cust_id
WHERE o.cust_id IS NULL;
```

```python
## 1. Outer join with indicator
temp = pd.merge(
    customers, 
    orders, 
    left_on='customer_id', 
    right_on='cust_id', 
    how='outer', 
    indicator=True
)
## 2. Filter for 'left_only' and drop indicator
temp[temp['_merge'] == 'left_only'].drop(columns=['_merge'])
```

|       | customer_id | name    | order_id | cust_id | amount |
| ----- | ----------- | ------- | -------- | ------- | ------ |
| __2__ | 3.0         | Charlie | NaN      | NaN     | NaN    |
### Right Anti Join
[](.md#^1b4929|Sample%20Data)
```sql
SELECT *
FROM customers c
OUTER JOIN orders o
	ON c.customer_id = o.cust_id
WHERE c.customer_id IS NULL;
```

```python
## 1. Outer join with indicator
temp = pd.merge(
    customers, 
    orders, 
    left_on='customer_id', 
    right_on='cust_id', 
    how='outer', 
    indicator=True
)
## 2. Filter for 'right_only' and drop indicator
temp[temp['_merge'] == 'right_only'].drop(columns=['_merge'])
```

|       | customer_id | name | order_id | cust_id | amount |
| ----- | ----------- | ---- | -------- | ------- | ------ |
| __3__ | NaN         | NaN  | 103.0    | 4.0     | 200.0  |
### Full Anti Join
[](.md#^1b4929|Sample%20Data)
```sql
SELECT *
FROM customers c
OUTER JOIN orders o
	ON c.customer_id = o.cust_id
WHERE c.customer_id IS NULL OR o.cust_id IS NULL;
```

```python
## 1. Outer join with indicator
temp = pd.merge(
    customers, 
    orders, 
    left_on='customer_id', 
    right_on='cust_id', 
    how='outer', 
    indicator=True
)
## 2. Filter for 'right_only' or 'left_only' and drop indicator
temp[temp['_merge'].isin(['left_only', 'right_only'])]\
	.drop(columns=['_merge'])
```

|     | customer_id | name    | order_id | cust_id | amount |
| --- | ----------- | ------- | -------- | ------- | ------ |
| **2**   | 3.0         | Charlie | NaN      | NaN     | NaN    |
| **3**   | NaN         | NaN     | 103.0    | 4.0     | 200.0  |
### Self Join
Same as [](.md#^1166de|%20inner%20join%20same%20key).
### Cross Join
<center><b>Colors</b></center>

|     | color |
| --- | ----- |
| **0**   | Red   |
| **1**   | Blue  |
<center><b>Sizes</b></center>

|     | size |
| --- | ---- |
| **0**   | S    |
| **1**   | L    |
```sql
SELECT *
FROM colors
CROSS JOIN sizes
```

```python
## how='cross' starting pandas 1.2.0
pd.merge(colors, sizes, how='cross')
```

|     | color | size |
| --- | ----- | ---- |
| **0**   | Red   | S    |
| **1**   | Red   | L    |
| **2**   | Blue  | S    |
| **3**   | Blue  | L    |
### Natural Join
```sql
SELECT *
FROM tbl1
NATURAL JOIN tbl2;
```

Omitting `on` parameter allows us to mimic natural join.
```python
pd.merge(tbl1, tbl2)
```
## Join on indexes
>[!missing]- Partial index matches
>Index to index joins are always on the entire index of both df's. 
### Index join (Same index label)
>[!warning] `join()` assumes same index label at same level.
>Let df1 index = ['city' , 'year'] then df2 index must also be ['city', 'year']
<center><b>df_a</b> </center>

|     | val_a |
| --- | ----- |
| **K1**  | 1     |
| **K2**  | 2     |
<center><b>df_b</b> </center>

|     | val_a | val_b |
| --- | ----- | ----- |
| **K1**  | 3     | 10    |
| **K3**  | 4     | 20    |
```python
# K2 will be NaN in result because it's missing in df_b (Left Join by default)
df_a.join(df_b, lsuffix='_left', rsuffix='_right')
```

`val_a` is a common column name so we __must__ provide either `lsuffix` or `rsuffix` to distinguish between them.

|     | val_a_left | val_a_right | val_b |
| --- | ---------- | ----------- | ----- |
| **K1**  | 1          | 3.0         | 10.0  |
| **K2**  | 2          | NaN         | NaN   |
>[!faq]- What happens if we don't provide a suffix?
>`ValueError` is thrown, since conflicting column names exist!

>[!summary]- Supported joins for `join()`
>**how** = {‘left’, ‘right’, ‘outer’, ‘inner’, ‘cross’, ‘left_anti’, ‘right_anti’}

>[!hint]- Alternative: merge()
>Not recommended for large dataframes since `join()` is faster.
>```python
>pd.merge(
df_a,
df_b,
how = "left",
left_index = True,
right_index = True,
suffixes = ['_left' , '_right']
)
>```
### Index Join (Different index labels)
<center><b>df_mi_same</b> </center>

| city | year | sales |
| ---- | ---- | ----- |
| **NY**   | **2023** | 500   |
| **LA**   | **2023** | 600   |
<center><b>df_mi_diff</b> </center>

| location | date | temp |
| -------- | ---- | ---- |
| **NY**       | **2023** | 75   |
| **SF**       | **2024** | 68   |
```python
# Levels are matched by position (0 with 0, 1 with 1) 
# `on` param -> provide index or col names for caller dataframe
	# Here caller is `df_mi_same`
df_mi_same.join(df_mi_diff, on=['city', 'year'])
```

| city   | year     | sales | temp |
| ------ | -------- | ----- | ---- |
| **NY** | **2023** | 500   | 75.0 |
| **LA** | **2023** | 600   | NaN  |
>[!hint] Alternative: merge()
>For `merge()`, one of the two dataframes must be merging on the index and the other on columns. Here, we are assuming, `df_mi_diff` is joining on the index and `df_mi_same` is using columns in the join.
>
>```python
>pd.merge (
df_mi_same,
df_mi_diff,
how='left',
left_on = ['city', 'year'],
right_index = True
)
>```
### Mixed Join (Index-Column)
[](.md#Index%20join%20(Same%20index%20label)|Other%20data) 
<center><b>df_c</b> </center>

|     | id_col | val_c |
| --- | ------ | ----- |
| **0**   | K1     | 100   |
| **1**   | K2     | 200   |
```python
# Links df_c['id_col'] to df_a.index
# `on` param -> provide index or col names for caller dataframe
	# Here caller is `df_c`
df_c.join(df_a, on='id_col')
```

|     | id_col | val_c | val_a |
| --- | ------ | ----- | ----- |
| **0**   | K1     | 100   | 1     |
| **1**   | K2     | 200   | 2     |
>[!hint] Alternative: merge()
>```python
>pd.merge (
df_c,
df_a,
how='left',
left_on = 'id_col',
right_index = True
)
>```
### Multi join (3+ df)
[](.md#Index%20join%20(Same%20index%20label)|Other%20data) 
<center><b>df_d</b> </center>

|     | val_d |
| --- | ----- |
| **K1**  | 99    |
| **K2**  | 88    |
```python
# Joins A, B (renamed), and D all on their indices in one go
df_a.join([df_b.add_suffix('_b'), df_d])
```

|     | val_a | val_a_b | val_b_b | val_d |
| --- | ----- | ------- | ------- | ----- |
| **K1**  | 1.0   | 3.0     | 10.0    | 99.0  |
| **K2**  | 2.0   | NaN     | NaN     | 88.0  |
## Non-equi joins
For smaller datasets, the most common approach is to create a Cartesian product of both DataFrames and then filter the results based on your inequality condition.
```python
# Example: Join df_events with df_ranges where event_date is between start and end
df_merged = pd.merge(df_events, df_ranges, how='cross')
result = df_merged.query('start_date <= event_date <= end_date')
```

For larger datasets, use a third party library like `pyjanitor`.
# Set operations
## `UNION ALL`/ `UNION`
<center><b>staff</b> </center>

|     | ID  | Name  |
| --- | --- | ----- |
| **0**   | 1   | Alice |
| **1**   | 2   | Bob   |
<center><b>hires</b> </center>
|     | ID  | Name    |
| --- | --- | ------- |
| **0**   | 2   | Bob     |
| **1**   | 3   | Charlie |
```sql
SELECT *
FROM staff
UNION ALL
SELECT *
FROM hires;
```

>[!warning]- Column names must match in both df's for `UNION` equivalency
>If column names differ, the values in non-matching rows are filled with `NaN`. 
>
>|       | customer_id | name    | order_id | cust_id | amount |
| ----- | ----------- | ------- | -------- | ------- | ------ |
| __0__ | 1.0         | Alice   | NaN      | NaN     | NaN    |
| __1__ | 2.0         | Bob     | NaN      | NaN     | NaN    |
| __2__ | 3.0         | Charlie | NaN      | NaN     | NaN    |
| __3__ | NaN         | NaN     | 101.0    | 1.0     | 50.0   |
| __4__ | NaN         | NaN     | 102.0    | 2.0     | 150.0  |
| __5__ | NaN         | NaN     | 103.0    | 4.0     | 200.0  |
> However, note that this is __NOT__ a outer join!

```python
pd.concat([staff, hires], axis=0, ignore_index = True)
# or
pd.concat([staff, hires], axis=0).reset_index().drop(columns=['index'])
```

|       | ID  | Name    |
| ----- | --- | ------- |
| **0** | 1   | Alice   |
| **1** | 2   | Bob     |
| **2** | 2   | Bob     |
| **3** | 3   | Charlie |
>[!faq]- What happens if we set `ignore_index = False`?
>The index duplicates since both df's are stacked vertically (i.e. mapped by column names)
>| |ID|Name|
|---|---|---|
|__0__|1|Alice|
|__1__|2|Bob|
|__0__|2|Bob|
|__1__|3|Charlie|

```sql
SELECT *
FROM staff
UNION
SELECT *
FROM hires;
```

```python
pd.concat([staff, hires], axis=0, ignore_index = True)\
	.drop_duplicates()
# or
pd.concat([staff, hires], axis=0)\
	.reset_index()\
	.drop(columns=['index'])\
	.drop_duplicates()
```

|       | ID  | Name    |
| ----- | --- | ------- |
| **0** | 1   | Alice   |
| **1** | 2   | Bob     |
| **3** | 3   | Charlie |
>[!info]- pd.concat([df1, df2], axis = 1)
>Horizontally stack df's (i.e map using index values)
>```python
>pd.concat([staff, hires], axis=1)
>```
>The output makes no sense, but observe how the stacking happens.
>
>| |ID|Name|ID|Name|
|---|---|---|---|---|
|__0__|1|Alice|2|Bob|
|__1__|2|Bob|3|Charlie|
## `INTERSECT`
Same as [#Inner Join](#Inner%20Join).
## `EXCEPT`
Same as [#Left Anti Join](#Left%20Anti%20Join)
# UPDATE clause
## `where()` method
It keeps the original data when the condition is **True** and replaces it when the condition is **False**. i.e. the opposite of what a `UPDATE` statement does.

|     | **Product_ID** | **Category** | **Stock** | **Price** |
| --- | -------------- | ------------ | --------- | --------- |
| 0   | 101            | Electronics  | 15        | 200       |
| 1   | 102            | Furniture    | 0         | 150       |
| 2   | 103            | Electronics  | -3        | 400       |
| 3   | 104            | Clothing     | 50        | 25        |
| 4   | 105            | Furniture    | -1        | 60        |
```sql
UPDATE inventory 
SET Stock = 0 
WHERE Stock < 0;
```

The `other` param specifies value to replace with when condition is __False__. If not specified, it is by default `NaN`.
```python
df_simple = df.copy()
# Condition: Keep if Stock is greater than or equal to 0
df_simple['Stock'] = df_simple['Stock'].where(df_simple['Stock'] >= 0, other=0)
```

|     | **Product_ID** | **Category** | **Stock** | **Price** |
| --- | -------------- | ------------ | --------- | --------- |
| 0   | 101            | Electronics  | 15        | 200       |
| 1   | 102            | Furniture    | 0         | 150       |
| 2   | 103            | Electronics  | 0         | 400       |
| 3   | 104            | Clothing     | 50        | 25        |
| 4   | 105            | Furniture    | 0         | 60        |
>[!important] Difference between `where()` and `loc[]` 

| Feature           | df.loc[]                                              | df.where()                                      |
| ----------------- | ----------------------------------------------------- | ----------------------------------------------- |
| Output Shape      | **Reduces rows/columns** to only match the condition. | **Maintains original shape**.                   |
| Non-matching data | Excluded entirely from the output.                    | Replaced with `NaN` or a custom fallback value. |

## `mask()` method
It replaces values if condition evaluates to __True__, and preserves data if condition evaluates to __False__. i.e. same as a `UPDATE` statement.

|     | **Product_ID** | **Category** | **Stock** | **Price** |
| --- | -------------- | ------------ | --------- | --------- |
| 0   | 101            | Electronics  | 15        | 200       |
| 1   | 102            | Furniture    | 0         | 150       |
| 2   | 103            | Electronics  | -3        | 400       |
| 3   | 104            | Clothing     | 50        | 25        |
| 4   | 105            | Furniture    | -1        | 60        |
```sql
UPDATE inventory 
SET Stock = 0 
WHERE Stock < 0;
```

The `other` param specifies value to replace with when condition is __True__. If not specified, it is by default `NaN`.
```python
df_mask = df.copy()

# Condition: Update if stock < 0
df_mask['Stock'] = df_mask['Stock'].mask(df_mask['Stock'] < 0, other=0)
```

|     | **Product_ID** | **Category** | **Stock** | **Price** |
| --- | -------------- | ------------ | --------- | --------- |
| 0   | 101            | Electronics  | 15        | 200       |
| 1   | 102            | Furniture    | 0         | 150       |
| 2   | 103            | Electronics  | 0         | 400       |
| 3   | 104            | Clothing     | 50        | 25        |
| 4   | 105            | Furniture    | 0         | 60        |
# `DELETE` clause
## Delete cols
```python
df = df.drop(columns=["column_to_delete"])
# or
df = df.drop(["column_to_delete"], axis=1)
```
## Delete rows
```python
# By index labels
# Drop rows with index labels 0 and 1
df = df.drop([0, 1])

# Boolean indexing
df = df[df["status"] != "inactive"] # Opposite of DELETE statement
df = df.query('status != "inactive"')
```
# Separate article
window functions
datetime functions
string functions
pivoting and unpivoting (case)
binning data 
casting