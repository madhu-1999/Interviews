# Design Approaches
https://www.geeksforgeeks.org/dbms/data-warehouse-design-approaches/
>Q. ETL vs ELT for data warehousing


### 1. The Traditional Flow: ETL

In the traditional model (common before cloud computing), the warehouse was a specialized, expensive resource. We didn't want to "waste" its processing power on cleaning or joining messy raw data.

- **Process:** Data was extracted from sources, sent to a separate **staging server** (an ETL engine like Informatica or Talend), transformed there, and only then loaded into the warehouse.
    
- **Why?** To keep the warehouse "clean" and optimized only for answering business questions.
    
---

### 2. The Modern Shift: ELT

With the rise of modern cloud data warehouses (like BigQuery, Snowflake, or Teradata), the warehouse itself became incredibly powerful and scalable. This led to a shift from ETL to **ELT**.

- **Process:**
    
    1. **Extract:** Pull raw data from the source.
        
    2. **Load:** Dump the raw data directly into the warehouse (often into a "Landing" or "Bronze" schema).
        
    3. **Transform:** Use the warehouse’s own compute power to clean, join, and model the data using **SQL**.
        
- **Why?** It is much faster to move raw data first and then use the warehouse’s massive parallel processing ($MPP$) to handle the heavy lifting.
    

---

### 3. Why the Warehouse "Supports" the Process

When we say a warehouse supports these processes, it’s because of specific features designed to make the "Transform" and "Load" stages easier:

- **External Tables:** Warehouses can "read" data sitting in cloud storage (like S3 or GCS) without actually importing it yet. This allows you to run SQL transformations on raw files.
    
- **Staging Areas:** They provide built-in temporary storage areas to hold data while it's being checked for errors before the final "Load."
    
- **Built-in Integration:** Many modern warehouses have native connectors to pull data directly from SaaS apps (like Salesforce or Google Ads), effectively handling the **Extraction** and **Loading** steps within the warehouse UI.
    
- **Stored Procedures & Task Scheduling:** They allow you to write complex scripts and schedule them to run at 2:00 AM, automating the transformation of raw data into "Gold" (production-ready) tables.
    

---

### Summary: The "Before vs. During" Reality

| **Aspect**                       | **Traditional ETL**              | **Modern ELT (Warehouse-Centric)**    |
| -------------------------------- | -------------------------------- | ------------------------------------- |
| **Where Transformation Happens** | On a separate ETL server.        | **Inside** the Data Warehouse.        |
| **When it happens**              | Before loading.                  | After loading (but before final use). |
| **Warehouse Role**               | Passive recipient of clean data. | Active processor of raw data.         |
| **Primary Tool**                 | GUI-based tools (Informatica).   | SQL-based tools (dBT, Stored Procs).  |

**In short:** While the initial "Extract" and "Load" start outside the warehouse, the most difficult part—the **Transformation**—is now increasingly handled _by_ the warehouse because it is faster and more scalable than a separate middleman server.

>Q. Is the data stored in a data warehouse read-only?

In theory, data in a data warehouse is considered **non-volatile**, meaning it is primarily **read-only**. Once a record is written, it isn't supposed to change. However, in modern practice, data _is_ modified, but usually for very specific structural or legal reasons rather than daily operational updates.

---

## 1. The "Read-Only" Philosophy

The goal of a data warehouse is to provide a "Single Source of Truth." If you could freely change last month's sales numbers today, your reports would lose integrity.

- **Consistency:** Analysts need to know that if they run a report for Q1 2024 today and again in six months, the numbers will match.
    
- **Audit Trails:** Warehouses often store historical snapshots. Instead of overwriting an old address, the warehouse adds a new row with a timestamp.
    

---

>Q. In what cases, might one modify data in a data warehouse?
## 2. Why Would One Modify Data?

Despite the "read-only" ideal, there are several critical scenarios where data in a warehouse must be modified:

### A. Correcting Data Quality Issues

If a bug in the source system (like a website) accidentally doubled the tax on every transaction for a week, the "raw" data in the warehouse is wrong.

- **The Fix:** Data engineers will perform a **data restatement**. They update the records to reflect the true financial reality so that executive dashboards aren't based on "garbage" data.
    

### B. Handling "Slowly Changing Dimensions" (SCD)

Dimensions (like Customer Name or Store Location) change over time.

- **The Scenario:** A customer moves from New York to California.
    
- **The Modification:** In an **SCD Type 1** model, the warehouse simply overwrites "New York" with "California." While this loses history, it keeps the database clean and is a literal modification of warehouse data.
    

### C. Legal Compliance (GDPR/CCPA)

This is the most common reason for modern data modification.

- **"The Right to be Forgotten":** If a customer requests that their personal data be deleted, the warehouse team must physically go into the `Customer_Dim` table and either **delete** the row or **anonymize** (mask) the names and emails.
    

### D. Late-Arriving Data

Sometimes data from a remote branch office doesn't arrive until three days after the month ends.

- **The Modification:** The warehouse must "upsert" (update or insert) these late records into the historical fact tables to ensure the monthly totals are eventually accurate.
    

---

## 3. How Modification Happens (The "Audit-Safe" Way)

When engineers modify data, they rarely just use a simple `UPDATE` command. Instead, they use strategies to maintain the warehouse's integrity:

- **Soft Deletes:** Instead of deleting a row, they add a column `is_deleted = TRUE`.
    
- **Versioning:** They keep the old record but mark it as "Inactive" and insert a new "Active" record.
    
- **Immutable Logs:** Some modern warehouses (like BigQuery or Snowflake) keep a log of every change made, allowing you to "time travel" back to see what the data looked like before the modification.

---

**Summary Table**

|**Feature**|**Operational DB (Source)**|**Data Warehouse**|
|---|---|---|
|**Primary Action**|Read/Write (CRUD)|Read-Mostly|
|**Data Volatility**|High (constant changes)|Low (historical/stable)|
|**Modification Reason**|Business transactions|Correction, Compliance, or Evolution|

Are you curious about how specific tools like Snowflake or BigQuery handle these updates, or are you looking at this from a data governance perspective?
>Q. How do data marts create data silos that store redundant or duplicate data?

Data marts often contain redundant data because they are designed as decentralized subsets of information tailored to specific departments. When multiple departments need the same information, they frequently replicate it within their own separate "silos" rather than sharing a single source.

Here is a breakdown of how this happens and a practical example:

### Why Redundancy Occurs

- **Decentralized Extraction:** Different departments often pull the same raw data from the same source systems (like an ERP or CRM) into their own individual marts.
    
- **Siloed Processing:** Because each team applies its own business logic, cleaning rules, and transformations, the same piece of data ends up living in multiple places, often with slight variations.
    
- **Performance Optimization:** Data marts prioritize speed for a specific group. To keep queries fast, they may store local copies of "global" data (like customer names) to avoid complex joins with a central warehouse.
    

---

### The "Single Customer" Example

Imagine a retail company that has two separate data marts: one for **Marketing** and one for **Customer Support**.

|**Data Mart**|**Use Case**|**Redundant Data Stored**|
|---|---|---|
|**Marketing Mart**|Used to track campaign performance and email open rates.|Customer Name, Email Address, Mailing Address, Purchase History.|
|**Support Mart**|Used to track ticket resolution times and return status.|Customer Name, Email Address, Phone Number, Purchase History.|

**The Redundancy Issue:**

1. **Duplicate Storage:** The `Customer Name`, `Email`, and `Purchase History` are stored in two different databases. The company is paying for that storage twice.
    
2. **Data Drift:** If a customer updates their email address via a Support ticket, the **Support Mart** is updated immediately. However, the **Marketing Mart** might still have the old email address.
    
3. **Inconsistency:** When the CEO asks, "How much did Customer X spend last year?", Marketing might report **$500** (using their logic) while Support reports **$450** (perhaps excluding returned items).
    

### Business and IT Implications

As noted in [Teradata's architecture insights](https://www.teradata.com/insights/data-architecture/data-warehouses-vs-data-marts-infographic), these silos create **"data drift"**—where data changes unexpectedly across different marts, breaking processes and making it difficult to maintain a "single source of truth."

While a [data mart](https://www.teradata.com/insights/data-architecture/what-is-a-data-mart) is easier and faster to set up for a single team, the redundancy it introduces often leads to higher long-term costs and governance challenges compared to an integrated [data warehouse](https://www.teradata.com/insights/data-architecture/data-warehouses-vs-data-marts-infographic).

>Q. What considerations are there apart from faster performance that can influence creation of a data mart over keeping data in a data warehouse.

Deciding to split a [data mart](https://www.teradata.com/insights/data-architecture/what-is-a-data-mart) from a unified [data warehouse](https://www.teradata.com/insights/data-architecture/data-warehouses-vs-data-marts-infographic) is a classic trade-off between **centralized control** and **departmental agility**. While performance is a common driver, it isn’t the only factor.

---

### 1. Considerations Before Breaking Off a Data Mart

Beyond speed, you should evaluate these factors to ensure the "separation" doesn't lead to "fragmentation":

- **Data Governance & Security:** Can the sensitive data required by a specific function (e.g., HR or Finance) be adequately protected within the warehouse? If a department requires hyper-restrictive access that is difficult to manage at a row-level in the warehouse, a physically separate mart might be safer.
    
- **Logical vs. Physical Separation:** Can you achieve your goals with a "Virtual Data Mart" (views or schemas) instead of moving the data? Moving data creates **redundancy and "data drift,"** where values unexpectedly change between systems.
    
- **Total Cost of Ownership (TCO):** A separate mart requires its own compute, storage, and—most importantly—ETL (Extract, Transform, Load) pipelines. You must weigh the performance gain against the cost of maintaining duplicate infrastructure.
    
- **Data Freshness:** Moving data to a mart adds another "hop" in the pipeline. If the function requires real-time data, the latency of loading the mart might negate the performance benefits of the queries themselves.
    

---
>Q. What optimizations or design choices can help improve performance so that we can avoid creating data marts?
### 2. Optimizations to Keep Data in the Warehouse

Before moving data out, modern [data platforms](https://www.google.com/search?q=https://www.teradata.com/insights/data-architecture/connected-multi-cloud-data-platform) offer several ways to boost performance while maintaining a "single source of truth":

- **Materialized Views:** These are pre-computed results stored on disk. They provide the speed of a separate table (like a mart) but are managed automatically by the warehouse engine.
    
- **Indexing and Clustering:** Use **Clustering Keys** or **Z-Ordering** to physically group related data on disk. For example, if a department always filters by `Region`, clustering by that column can drastically reduce the data scanned.
    
- **Compute Isolation (Virtual Warehouses):** Modern cloud warehouses allow you to spin up a dedicated "compute cluster" for a specific department. This ensures that a heavy Finance report doesn't slow down the Marketing dashboard, even though they are querying the same central data.
    
- **Result Caching:** If users run the same queries repeatedly, the warehouse can serve the results from a cache in milliseconds without re-scanning the tables.
    

---
>Q. What are some other scenarios, apart from faster performance that can require creation of a data mart?
### 3. Scenarios That _Require_ a Separate Data Mart

Sometimes, keeping everything in one warehouse is genuinely impractical. These scenarios often justify a physical break:

- **Highly Specialized Tools:** Some legacy or niche analytics tools require a specific database engine (e.g., an OLAP cube or a graph database) that the central warehouse doesn't support.
    
- **Extreme Low-Latency (Sub-Second):** If an application needs sub-second response times for thousands of concurrent users (like a customer-facing portal), a specialized, high-concurrency data mart (or "Data Serving Layer") is often necessary.
    
- **Sandbox Environments:** Data scientists often need a "sandbox" to store temporary tables, experimental features, and messy intermediate data. A separate mart keeps this "noise" out of the clean, governed production warehouse.
    
- **Geographic Sovereignty:** If certain data must stay within a specific country for legal reasons (GDPR/CCPA), a regional data mart may be the only way to comply while still feeding a global warehouse with anonymized summaries.
    

---

### Summary Table

|**Choice**|**Best For...**|**Major Risk**|
|---|---|---|
|**Unified Warehouse**|Integrity, governance, single source of truth.|Resource contention between teams.|
|**Virtual Mart (Views)**|Rapid setup, zero data redundancy.|Can be slow if underlying tables are massive.|
|**Physical Data Mart**|Maximum performance, specialized use cases.|**Data drift** and high maintenance costs.|

# Data Modeling Approaches
https://youtu.be/gRE3E7VUzRU?list=PLy4OcwImJzBLWhUqduwLs30YHiDpX4Xp7
1. [[Database Normalization]] (Snowflake schema) 
2. Denormalized Model (Star schema)
3. One Big Table
4. Data Vault
## Star Schema
https://www.owox.com/blog/articles/star-schema-explained
### __Fact Table__
At the center of a star schema, a fact table stores numerical data reflecting business performance. It contains measurable values (facts) and **foreign keys** that link to **dimension tables**.
The **granularity** of a fact table determines the level of detail stored, such as daily or monthly sales figures.
Since fact tables typically have **a high number of rows but fewer columns**, they ensure efficient data storage and retrieval.
### __Dimension Table__
Dimension tables store **descriptive information** that provides context to the numerical data in a fact table.
Each dimension table has a primary key that links to the fact table’s foreign key, enabling data grouping and filtering. Unlike fact tables, dimension tables are **not highly normalized** to avoid complex joins, ensuring **faster query execution**.
### __Attributes__
Attributes are **descriptive columns** in a dimension table that provide detailed information about an entity.
- In the **Product Dimension Table**, **Product Category** and **Brand** classify products.
__Attribute hierarchies__ organize data into different levels, allowing users to drill down into detailed data or roll up to view higher-level summaries.
- A **Time Hierarchy** in the **Time Dimension Table** follows this structure:  
    **Year → Quarter → Month → Day**, allowing analysis at various time scales.
- A **Location Hierarchy** in the **Store Dimension Table** follows this structure:  
    **Region → State → City**, enabling geographic trend analysis

Notice `Region` data is duplicated in `customer_dim` and `store_dim` .
![[Pasted image 20260414161307.png]]

>Q. How to design a star schema?

Let’s use a scenario for a **Global Coffee Shop Chain (e.g., "Star-Beans")** that wants to analyze its daily transactions.

---

## 1. Why Star Schema is the Best Choice

For a coffee chain with millions of rows of data, the star schema is superior to a flat file or a highly normalized "Snowflake" schema for three reasons:

- **Query Speed:** By denormalizing data into a "star," the database performs fewer "joins." Instead of searching through ten connected tables to find a customer’s city, it’s all in one `Customer` table.
    
- **Human Readability:** It mimics business questions. A manager doesn't ask about "normalized primary key relations"; they ask for "Sales by Region by Month." The star schema maps directly to these dimensions.
    
- **Aggregations:** It is optimized for math. Finding the total revenue for all Lattes sold in April 2026 is computationally "cheaper" in this format.

---

## 2. A Human-Intuitive Guide to Building Your Star

Developing a schema shouldn't start with code; it starts with a "mental sort." Here is how you decide what goes where.

### Step 1: Identify the "Verb" (The Fact)

Think of the central event you are tracking. This becomes your **Fact Table**.

- **The Rule:** If it’s a quantity that can be added, averaged, or counted, it’s a Fact.
    
- **Coffee Example:** The "Sale" or "Transaction" is the event.
    
- **Data Included:** `Quantity_Sold`, `Unit_Price`, `Tax_Amount`, `Discount_Value`.
    
- **Data Excluded:** The Coffee's Name or the Barista's Name. (Why? Because those are descriptions, not math. Including them here would make the table massive and redundant).
    

### Step 2: Identify the "Adjectives" (The Dimensions)

Think of how you want to filter or "slice" that verb. These become your **Dimension Tables**.

- **The Rule:** If it’s a word used to categorize the fact (Who, What, Where, When), it’s a Dimension.
    
- **Coffee Example:**
    
    - **Who:** `Customer_Dim` (Loyalty level, Age group).
        
    - **What:** `Product_Dim` (Drink type, Size, Roast level).
        
    - **Where:** `Store_Dim` (City, Manager, Seating capacity).
        
    - **When:** `Date_Dim` (Day of week, Fiscal quarter, Holiday status).
        

### Step 3: The "So What?" Test (Filtering Data)

When deciding whether to include a specific piece of data, ask: **"Will a manager ever filter a report by this?"**

- **Keep it:** "Was this an app order or in-store?" (Useful for strategy).
    
- **Discard it:** "What was the internal warehouse batch number of the milk used?" (Useful for supply chain, but clutters a Sales Star Schema).

### Step 4: Connect with "Keys"

Finally, you place a **Foreign Key** in the Fact table that points to the **Primary Key** of each Dimension.

|**Fact Table (Sales_Fact)**|**Dimension Table (Product_Dim)**|
|---|---|
|`Product_Key` (Connector)|`Product_Key` (ID)|
|`Quantity` (Math)|`Product_Name` (Label)|
|`Total_Price` (Math)|`Category` (Label)|

---

### Summary Table: Fact vs. Dimension

|**Feature**|**Fact Table**|**Dimension Table**|
|---|---|---|
|**Content**|Quantitative (Numbers)|Qualitative (Labels/Text)|
|**Shape**|Very tall (Millions of rows)|Wide (Many descriptive columns)|
|**Action**|Summarized (Sum, Avg)|Filtered (Group by, Where)|
## Snowflake Schema 
https://www.owox.com/blog/articles/snowflake-schema-data-modeling
![[Pasted image 20260414171218.png]]
__The Golden Rule__
- **Denormalize** attributes you use in **90% of queries**.Keep them in the main table to avoid joins.
- **Normalize** attributes used in **<10% of queries**. Move these to sub-dimensions to keep your main "search path" lean and fast.

>Q. How to design a snowflake schema?

To illustrate a **Snowflake Schema**, let’s use the scenario of a **Global Electronics Retailer** (e.g., "TechWorld").

In this model, we prioritize **query speed** while maintaining a logical structure that saves storage by reducing redundancy.

---

## 1. The Use Case: Global Electronics Retail

We are tracking **Sales Transactions**. Our primary goal is to analyze revenue across different product hierarchies and geographic locations.

### Frequent Queries (Must be Fast)
- _"What was the total revenue for 'Laptops' in the 'North America' region last month?"_
- _"Which 'Gaming' brands are trending in 'New York' stores?"_

### Less Frequent Queries (Can be Slower)
- _"What is the average shelf-life of products from a specific supplier in the 'Vietnam' factory?"_ 
- _"List all stores located in cities with a population over 1 million."_

---

## 2. Step-by-Step Snowflake Design

### Step 1: Define the Central Fact Table

We start with the **`Sales_Fact`** table. This stays "lean" to ensure fast scanning of millions of rows.

- **Columns:** `Sale_ID`, `Date_Key`, `Product_Key`, `Store_Key`, `Quantity`, `Total_Price`.
- **Optimization:** We keep only **keys** and **measures**. This makes the table "narrow," allowing the database to read more rows per second.

### Step 2: Designing the Product Hierarchy (Normalizing)

In a Star Schema, the `Product` table would contain every detail. In a **Snowflake**, we branch it out.

1. **`Dim_Product`**: Contains `Product_Name`, `SKU`, and `Brand_Key`.
2. **`Dim_Brand`**: Contains `Brand_Name` and `Manufacturer_Country`.
3. **`Dim_Category`**: Contains `Category_Name` (e.g., Laptops, Audio).

- **Decision (Normalize):** We normalize `Category` and `Brand` out of the main `Product` table.
    
- **Why:** Since "Laptops" or "Sony" might repeat 50,000 times, storing them as a 4-byte `Key` instead of a 20-character `String` saves space. For **query speed**, this supports "Columnar Compression"—the database can skip over the `Category` table entirely if the query doesn't ask for it.
    
### Step 3: Designing the Geography Hierarchy (Denormalizing for Speed)

1. **`Dim_Store`**: Contains `Store_Name`, `Address`, `City`, `State`, and `Region`.
    

- **Decision (Denormalize):** Unlike the product table, we keep `City`, `State`, and `Region` inside the **same** table.
    
- **Why:** Because our **Frequent Queries** constantly ask for "Sales by Region." If we "Snowflaked" this into `Dim_City` -> `Dim_State` -> `Dim_Region`, the database would have to perform **3 extra joins** for every single report. By denormalizing geography, we trade a tiny bit of storage for a massive jump in query speed.
    

---

## 3. Summary of Normalization Choices

| **Table**            | **Strategy**    | **Why it supports Query Speed**                                                                                                                                                  |
| -------------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Product Category** | **Normalize**   | Frequent queries often filter by a _specific_ product. Keeping the `Product` table thin allows the database to find those specific SKUs faster before joining the category name. |
| **Store Geography**  | **Denormalize** | Since "Region" and "State" are almost always queried together, keeping them in one table prevents "Join Explosion," which is the #1 killer of speed in large warehouses.         |
| **Supplier Details** | **Normalize**   | Since these are **Less Frequent Queries**, we move them to a distant sub-dimension. This keeps the primary `Product` dimension clean for the high-traffic sales reports.         |

---

## 4. The Resulting Structure

The [Snowflake Schema](https://www.owox.com/blog/articles/snowflake-schema-data-modeling) creates a hierarchy where the most important "adjectives" are close to the Fact table, and the "technical" or "rarely used" details are branched out further.

- **Fact_Sales**
    - → **Dim_Date** (Flat/Denormalized)
    - → **Dim_Store** (Flat/Denormalized for Region/City speed)
    - → **Dim_Product** (Normalized)
        - → **Dim_Brand**
        - → **Dim_Category**

By [normalizing dimension tables](https://www.google.com/search?q=https://www.owox.com/blog/articles/snowflake-schema-data-modeling%23Normalization) like Product, you ensure that any update to a category name happens in **one place**, maintaining [data integrity](https://www.google.com/search?q=https://www.owox.com/blog/articles/snowflake-schema-data-modeling%23Ensuring-Data-Integrity-With-Structured-Data) across all your reports.

>Q. How does Snowflake Schema help improve query speed, despite normalizing data?

Technically, **yes**, normalizing a dimension table (turning a Star into a Snowflake) can reduce query speed because the database has to perform more **joins**.

However, in modern cloud data warehouses, we normalize certain dimensions because the "speed penalty" of a join is often smaller than the "speed penalty" of scanning a massive, bloated table.

Here is the breakdown of why and when we normalize `Product_Dim` despite the join cost.

---

### 1. The "Thin Table" Advantage (Columnar Storage)

Modern warehouses use **Columnar Storage**. They don't read a whole row; they only read the specific columns you ask for.

- **Star Schema (Denormalized):** If your `Product_Dim` has 200 columns (color, weight, manufacturer details, etc.), the table becomes "fat." Even if you only want `Product_Name`, the database engine has to manage a much larger metadata overhead for that massive table.
    
- **Snowflake Schema (Normalized):** By moving "rarely used" data (like `Manufacturer_Details`) to a separate table, the primary `Product_Dim` stays "thin." This allows the database to cache the table in memory more easily, often making the search for a specific product **faster** than in a bloated Star table.

### 2. Update Speed vs. Read Speed

If you have 1 million products and the name of a "Category" changes (e.g., "Electronics" becomes "Digital Hardware"):

- **Star Schema:** You have to update **hundreds of thousands of rows** in the `Product_Dim`. This locks the table and slows down every analyst trying to run a report.
- **Snowflake Schema:** You update **one row** in the `Category_Dim`. The change is instant, and the `Product_Dim` is never touched.

### 3. When Normalizing actually _Helps_ Speed
Normalization can improve performance through a feature called **Join Pruning**.

If a user runs a query for "Total Sales by Date," a smart optimizer in a [Snowflake Schema](https://www.owox.com/blog/articles/snowflake-schema-data-modeling#Advantages-of-Using-a-Snowflake-Schema) will look at the query, see that `Category_Dim` isn't needed, and **completely ignore those tables**.

In a Star Schema, all that category data is "baked in" to the table you are searching, so the database has to deal with that extra data weight even if you don't use it.

---

### Summary: The "Speed" Verdict

|**Action**|**Impact on Speed**|**Why?**|
|---|---|---|
|**Adding Joins**|**Decreases**|More CPU work to link tables.|
|**Thinning Tables**|**Increases**|Less data to scan; better memory/cache usage.|
|**Data Integrity**|**Increases**|Prevents "fan-out" errors (double counting) which can happen in messy denormalized tables.|
## Slowly changing dimensions(SCD)
https://www.datacamp.com/tutorial/mastering-slowly-changing-dimensions-scd