#database #indexing #rdbms 
# Overview of how table data is stored
Table data + index data + metadata is stored in data files which is a collection of data pages.
 ![](mx-img-t1qhb1ziqtw32p13tp6apu1z-pt4m46_50s.jpg|SQL%20Indexes%20(Visually%20Explained)%20|%20Clustered%20vs%20Nonclustered%20|%20#SQL%20Course%2035%20-%2004:46|0)
  [04:46](https://www.youtube.com/watch?v=BxAj3bl00-o&t=287#t=04:46.50) 
 Each data page (of fixed size 8kb) stores
 1. Header (of fixed size) 
	 In image below 1:150 denotes file id (=1) and page id (=150).
 2. some rows in of the table (no of rows is variable, depending on the size of a row)
 3. offset footer which contains ptr to each row in the page for quick access.
![](mx-img-t1qhb1ziqtw32p13tp6apu1z-pt6m38_54s.jpg|SQL%20Indexes%20(Visually%20Explained)%20|%20Clustered%20vs%20Nonclustered%20|%20#SQL%20Course%2035%20-%2006:38|0)
[06:38](https://www.youtube.com/watch?v=BxAj3bl00-o&t=399#t=06:38.54) 
# How is data stored when there is no primary key and no indexes
Rows are stored in the same order as they are entered into the table, which could be ____random__.
Searching on such a table would require a __FULL TABLE SCAN__.

 ![](mx-img-t1qhb1ziqtw32p13tp6apu1z-pt8m7_02s.jpg|SQL%20Indexes%20(Visually%20Explained)%20|%20Clustered%20vs%20Nonclustered%20|%20#SQL%20Course%2035%20-%2008:07|0)
  [08:07](https://www.youtube.com/watch?v=BxAj3bl00-o&t=487#t=08:07.02) 
# Clustered / Primary Index
It defines the __physical__ order in which rows of a table are stored.
Since, data can be stored on disk in only one way, ==a table can have only one clustered index.==
Data in a clustered index is __stored in the sorted order__ of the selected key.
>[!info]-
>In __MySQL/MariaDB__: Primary key = clustered index
>
>In __Postgres__: Primary key = unique non-clustered index.
>> We can cluster on any key using `CLUSTER` command. But we need to periodically rerun `CLUSTER` command to cluster newer data.

Data is arranged in a [B-tree](B-tree). (Note that there are other options for arranging data, B- tree is the most widely used for general-purposes). 

 ![](mx-img-t1qhb1ziqtw32p13tp6apu1z-pt14m26_50s.jpg|SQL%20Indexes%20(Visually%20Explained)%20|%20Clustered%20vs%20Nonclustered%20|%20#SQL%20Course%2035%20-%2014:26|0) [14:26](https://www.youtube.com/watch?v=BxAj3bl00-o&t=866#t=14:26.50) 
 The image above shows a clustered index. CRUD ops are $O(log n)$.  
	 The leaf nodes point to the actual data pages. (not a pointer !)
	 Since the data is stored in sorted order so intermediate/root node(s) store pointers to sub-ranges of data.
#  Non-clustered / Secondary index
If you perform a query on a column that is _not_ the primary key, the database uses a **Non-Clustered Index**.
There are two ways to store non-clustered indexes:[^1] 
## Index references data pointers directly
>[!tip] Best for read-heavy workloads

This is a separate B-Tree that ==stores the indexed value and a pointer to go find the actual row== in the main clustered table. i.e. it requires extra space.
	Notice that data in the index structure is sorted by the column that the non clustered index is created on, but data that is physically in the data page remains in its original order.
	![Database Indexes-1780129755288](Assets/Database%20Indexes-1780129755288.webp)

![Database Indexes-1780129584084](Assets/Database%20Indexes-1780129584084.webp)


The leaf node stores a pointer to the actual row in the format *file_id : page_id : offset*.

>[!success]- Advantage
>By referencing data directly, we can reduce the number of disk seeks making reads faster.

>[!failure]- Disadvantage
>Whenever a data record gets updated or moved (during defragmentation), the database has to hunt down _every single non-clustered index_ you've created and update all of their pointers to the new disk location. This makes writes and maintenance incredibly expensive.

## Using primary index as indirection
>[!tip] Best for Write-heavy workloads

Instead of the secondary index pointing directly to a specific physical location on your hard drive (a file offset), it points to the **Primary Key**. The database then has to do two separate lookups to give you the data:
1. **First Lookup:** It searches the secondary index to find the primary key associated with your query.
2. **Second Lookup:** It takes that primary key and searches the primary index to find the actual data record.
![Database Indexes-1780165645456](Assets/Database%20Indexes-1780165645456.webp) 
[](Documents/Database%20Internals.pdf#page=51&selection=0,96,0,97&color=yellow|Src:%20Database%20Internals,%20p.51)
>[!success]- Advantage
>By pointing to the primary key instead, the physical location of the row can change as much as it wants (on updates, moving due to defragmentation) . As long as the primary key stays the same, the secondary indexes never need to be updated. This makes **writes faster.**

>[!missing]- Disadvantage
>While reading, we perform an extra disk seek since leaf nodes of secondary index point to primary key, and we need to perform a primary key lookup to get actual data record. This makes **reads slower.**
>(Think, traverse 2 B-tree indexes vs 1 B-tree index + 1 direct page-fetch) 
# Clustered Index vs Non-Clustered Index

| Feature               | Clustered Index                                                              | Non-Clustered Index                                                         |
| :-------------------- | :--------------------------------------------------------------------------- | :-------------------------------------------------------------------------- |
| **Physical Order**    | Sorts and stores data rows                                                   | Does not sort data rows                                                     |
| **Number per Table**  | Only one                                                                     | Many                                                                        |
| **Leaf Level**        | Contains data pages                                                          | Contains pointers (Row ID/Key)                                              |
| **Read Performance**  | Faster (usually)                                                             | Slower (extra step to data page)                                            |
| **Space Requirement** | Low (part of table)                                                          | High (separate structure)                                                   |
| __Write Performance__ | Slower (due to potential reordering of data)                                 | Faster (no change in physical location of data)                             |
| __Use case__          | Unique columns, Infrequenly modified column, improve range query performance | Columns frequently used in joins or search conditions, exact match queries. |
|                       |                                                                              |                                                                             |
# Rowstore Index
![](https://youtu.be/k9DpO91W76o?list=PLNcg_FV9n7qZY_2eAtUzEUulNjTJREhQe)
They store table data in data pages in rows (as described in [#Overview of how table data is stored](#Overview%20of%20how%20table%20data%20is%20stored)).
```sql
CREATE NONCLUSTERED INDEX IX_Customers_Country ON Customers(Country);

CREATE CLUSTERED INDEX IX_Customers_ID ON Customers(ID);
```
# Columnstore Index
They store table data in data pages in columns.
Check [Parquet](Parquet.md) for detailed explanation of how row grouping, segmentation and compression works.
 ![](mx-img-m2tk73y1gsv8vcb4ag8xwtmq-pt6m19_29s.jpg|SQL%20Columnstore%20Index%20(Visually%20Explained)%20|%20Columnstore%20vs%20Rowstore%20|%20#SQL%20Course%2036%20-%2006:19|0) [06:19](https://www.youtube.com/watch?v=k9DpO91W76o&t=379#t=06:19.29) 
The compressed segments are stored in LOB (Large Object) Pages.
	The Header  (of fixed size) will store *file_id : page_id*.
	Then we have a Segment Header which stores the _segment_id_ (id of column segment), the _rowgroup_id_ and _dictionary_id_ for the compression mapping.
	Lastly it stores the compressed data stream.

 ![](mx-img-m2tk73y1gsv8vcb4ag8xwtmq-pt6m0_29s-1w3hlvb.jpg|SQL%20Columnstore%20Index%20(Visually%20Explained)%20|%20Columnstore%20vs%20Rowstore%20|%20#SQL%20Course%2036%20-%2006:00|0) [06:00](https://www.youtube.com/watch?v=k9DpO91W76o&t=360#t=06:00.29) 
## How does search work?
Each Row group stores column - level stats for each column in the row (min/max value ranges).
So when a query comes asking for a column aggregation, the db will check row group stats to identify if the data ranges match (if `WHERE` or `HAVING` clause is specifed). If they don't we can skip that rowgroup (saving time).
If they match, the db scans the segments for specified columns only, in the row group to find all the column values that match its conditions.
	Each row group would store info about which segments store data chunks for a particular column.
Then using the dictionary reference, it can decompress and aggregate column values.
>[!tip] The row group skipping works better if data is ordered, that way we won't have too many segments with overlapping ranges.
>>[!note] Data is not ordered by default in columnstore indexes.
>>We can force a order while creating the index as:
>>```sql
>>CREATE CLUSTERED COLUMNSTORE INDEX IX_Sales ON Sales ORDER (TransactionDate, CustomerID);
>>```
## Clustered and Non-Clustered Columnstore Indexes
>[!warning] Data is unsorted by default

For a clustered columnstore index, the original heap/row store will be deleted and the table data is represented as seen in [Parquet](Parquet.md).

For a non clustered index, the LOB pages are stored as a separate structure, in addition to the rowstore or heap format.

```sql
CREATE NONCLUSTERED COLUMNSTORE INDEX IX_Customers_Country ON Customers(Country);

CREATE CLUSTERED COLUMNSTORE INDEX IX_Customers_ID ON Customers; -- Cannot specify a column since it must be done for all columns for a valid representation.
```
# Unique Index
Enforce uniqueness of column value.
Writes are slower (due to index overhead), but reads are faster.
```sql
CREATE UNIQUE INDEX  uniq_primary_email ON emails(user_id);
```
# Filtered/ Partial Index
>[!warning] Only allowed for non clustered index and rowstore

Create index on a subset of data by specifying `WHERE` condition.
- **Reduced Index Size**: The index only stores entries for rows that meet the condition, saving significant disk space.
- **Faster Maintenance**: PostgreSQL only updates the index when a row that satisfies the condition is inserted or updated.
- **Improved Query Performance**: Small, specialized indexes are often faster to scan than a full-table index
**Use Case**: 
	You query more often on _active_ users, and infrequently on _inactive_ users. Filtered index will speed up queries at lower storage cost (_inactive__ >> _active_).
```sql
CREATE INDEX index_name 
ON table_name (column_name) 
WHERE condition;
```
# When to use which index?
 ![](mx-img-e3nticad5hogrghcdp1tbv7n-pt3m20_52s.jpg|How%20to%20Choose%20the%20Right%20Index%20(Visually%20Explained)%20|%20#SQL%20Course%2038%20-%2003:20|0) [03:20](https://www.youtube.com/watch?v=1lHNw365ldc&t=201#t=03:20.52) 
# How does DB choose which index to use?
# Fragmentation
# Execution Plans
![](https://youtu.be/O7AzUDogXsw?list=PLNcg_FV9n7qZY_2eAtUzEUulNjTJREhQe)
Estimate vs Actual
Table scan vs index scan vs index seek
# Index Data Structures
[B-tree](B-tree)
[Hash Index](Hash%20Index.md)

serialize 
# Footnotes
[^1]: [](Documents/Database%20Internals.pdf#page=50&annotation=4170R|Database%20Internals,%20p.50)
