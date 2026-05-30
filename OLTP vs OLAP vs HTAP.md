#database #DE
# OLTP
Data processing system used to handle a **high volume of short, *concurrent* transactions** in real-time, like banking or e-commerce transactions.
# OLAP
Data processing system used to handle **complex queries or multidimensional analysis** on large volumes of data for the purpose of analysis.

>[!tldr]
>OLTP -> Application oriented storage
>OLAP -> Analysis oriented storage

# Why can OLTP databases not be used for analytical purposes?

**Aggregation** queries (using `SUM`, `AVG`, `GROUP BY`) require massive table scans, which would cause latency spike.
Since OLTP is row-based, **aggregation** queries read the entire row even though we need only one or two columns in a table. This uses up memory unnecessarily.

Lets say, I want to ___find the top 5 bestselling products last year among customers in the age range of 25-39 in California State___
This is a very complex query that would involve __multiple joins__, since the relevant data is spread across multiple tables (Customers, Products, Orders etc). This can become even more complex if we consider that the company has online sales as well as in store sales, the data for which would be stored separately. 

As seen above analytical, queries are expensive to run due to their complexity. Using a OLTP database for this would mean **degraded performance** for users while the query is running since both are analytical and operational queries would **fight for resources**, such as disk I/O, memory and CPU. 
Furthermore, maintaining a large amount of historical data would **slow down inserts and updates** due to the massive index overhead, which defeats the purpose of using a OLTP database, which is optimized for fast inserts and updates.
# Why can OLAP databases not be used for operational purposes?
1. **Inefficient for Frequent Inserts** — OLAP databases are optimized for fast read operations, particularly in a [[Row wise vs Column vs Hybrid Data Storage formats|columnar]] format. Writing operations, especially frequent inserts, are slow since these systems are not designed for row-level transactions.
2. **Lack of ACID Compliance** — OLAP databases do not fully support ACID (Atomicity, Consistency, Isolation, Durability) properties, making them unreliable for transactional data.
3. **High Computational Costs** — Frequent inserts and updates in OLAP databases require extensive computing power, leading to higher operational costs.
4. **Missing Transactional Features** — Unlike OLTP databases, OLAP systems lack essential features like rollback mechanisms, foreign key constraints for data integrity, and row-level locking to prevent data conflicts.
5. **Update Anomalies** — Instead of modifying existing records, OLAP databases often create duplicate versions, complicating data consistency.

[[Designing Data-Intensive Applications, 2nd Edition-9781098119058.pdf#page=21&selection=11,51,11,52&color=note|Designing Data-Intensive Applications, 2nd Edition-9781098119058, p.21]]

| Property           | OLTP                                                            | OLAP                                                        |
| ------------------ | --------------------------------------------------------------- | ----------------------------------------------------------- |
| Read pattern <br>  | Fetch records by key                                            | Aggregate over large number of records                      |
| Write pattern      | Create, update, and delete individual records                   | Bulk import (ETL) or event stream                           |
| Human user example | End user of web/mobile application                              | Internal analyst, for decision support                      |
| Data storage       | Row based storage                                               | Column based storage                                        |
| Type of queries    | Fixed, predefined by application                                | Arbitrary, ad-hoc exploration by analysts                   |
| Query volume       | Lots of small queries                                           | Few queries, each is complex                                |
| Data represents    | Latest state of data (current point in time)                    | History of events that happened over time                   |
| Dataset size       | Gigabytes to terabytes                                          | Terabytes to petabytes                                      |
| Method Used        | Uses a data warehouse.                                          | Uses a standard DBMS                                        |
| Data structure     | Normalized to avoid data redundancy and ensure data consistency | Denormalized to improve query performance by reducing joins |
| Concurrency        | Can handle large number of concurrent users                     | Does not support large number of concurrent users           |
| Optimization       | For writes and updates in huge volumes                          | For fast retrieval of complex queries and aggregations      |
| Tools              | MySQL, MongoDB                                                  | BigQuery, Snowflake, Redshift                               |

> [!PDF|255, 208, 0] [[Designing Data-Intensive Applications, 2nd Edition-9781098119058.pdf#page=24&annotation=37943R|Designing Data-Intensive Applications, 2nd Edition-9781098119058, p.24]]
> > Systems designed for this type of use, known as product analytics or realtime analytics, include Pinot, Druid, and ClickHouse [6]. Such systems ingest data in real time and are optimized for low-latency query responses. In contrast, traditional OLAP systems typically ingest data in batches and are optimized for high-throughput query processing.

# HTAP (Hybrid Transaction and Analytical Processing)
## Advantages
HTAP creates a **simpler architecture**, because OLAP + OLTP + ETL, is replaced by a single database. It **eliminates data redundancy** since HTAP becomes single source of truth.
Securing one database is easier and **data can be fresh**, as soon as data comes in for processing, it’s also available for analytics.
## Challenges
Balancing both OLTP and OLAP can lead to **performance degradation** of one over the other at scale (consider resource contention).
To guarantee performance, there is a **tradeoff with data freshness** (depends on implementation of HTAP database).
	Another important factor is deciding when to merge OLTP data in OLAP system to maintain data freshness. Immediately merging keeps data freshness high but comes with performance degradation due to merging overhead. Merging on demand keeps throughput high, but compromises data freshness.
Some HTAP databases store data in both row-wise(OLTP) and column-wise (OLAP) formats which leads to **storage overhead** since same data is stored twice.
**Query optimizer** must decide and plan query execution against row-store or column-store, based on which is more beneficial. This is challenging.


# References:
1. [Hybrid Transactional/Analytical Processing: A Survey](https://humming80.github.io/papers/sigmod-htaptut.pdf)
2. [HTAP Databases: A Survey](https://arxiv.org/pdf/2404.15670)
3. [HTAP still a dream a decade later](https://medium.com/@danthelion/htap-still-the-dream-a-decade-later-9d168f07c759)
