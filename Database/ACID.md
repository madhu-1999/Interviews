#database 
ACID ensures data consistency, reliability, integrity during database transactions.

![atomicity](https://media.geeksforgeeks.org/wp-content/uploads/20250109151305931125/atomicity.webp)
# Atomicity
The entire transaction happens all at once or not at all.
If T1 completes but T2 fails, transaction T should be rollbacked, otherwise database will be inconsistent.
# Consistency
Database must be consistent before and after a transaction
Before : 500 + 200 = 700
After: 400 + 300 = 700
If T1 completes but T2 fails, database will be inconsistent.
# Isolation
Ensures multiple transactions can occur simultaneously without affecting each other. i.e. each transaction must be isolated from other transactions.
# Durability
It ensures that once changes are committed after transaction ends, it is done to non-volatile memory so that data remains unaffected even if system failure occurs.

# Transaction Anomalies
## Dirty Read

Consider two transactions T1 and T2. Let value of A=100 at the start. Transaction T1 reads value of A, decrements it by 50 and writes it to volatile memory (since T1 is not committed yet), following principle of atomicity. 

Then Transaction T2 reads this changed value of A = 50, decrements by 10 and writes to volatile memory. T2 commits i.e. A=40 becomes permanent. However, T1 fails and its changes are removed from volatile memory, but not non-volatile memory. Thus we have incorrect value of A in non-volatile memory.

| T1      | T2      |
| ------- | ------- |
| R(A)    |         |
| A:=A-50 |         |
| W(A)    |         |
|         | R(A)    |
|         | A:=A-10 |
|         | W(A)    |
| Failed  | Commit  |
Reading in a value before it has been committed is called ***Dirty Read***. 
**Values in non-volatile memory should always be used to read in transactions.**
## Non-repeatable Read

Consider two transactions T1 and T2. Let value of A=100 at the start. Transaction T1 and T2 both read value of A = 100. T1 makes change and writes A=50 to volatile memory. T2 reads in this A=50 again.

However, value of R(A) id different now. A=100 on first read operation and A=50 on second, this is called ***non-repeatable read***.
**Values in non-volatile memory should always be used to read in transactions.**

| T1      | T2     |
| ------- | ------ |
| R(A)    | R(A)   |
| A:=A-50 | ..     |
| W(A)    | ..     |
| ..      | R(A)   |
| ..      | ..     |
| commit; | ..     |
|         | Commit |
## Phantom Read

Consider two transactions T1 and T2. Let value of A=100 at the start. Transaction T1 reads and then eventually deletes A. T2 tries to read A which now does not exist. This is called ***phantom read***.
**Values in non-volatile memory should always be used to read in transactions.**

| T1      | T2     |
| ------- | ------ |
| R(A)    | ..     |
| ....    | ..     |
| D(A)    | ..     |
| ..      | R(A)   |
| ..      | ..     |
| commit; | ..     |
|         | Commit |
# Isolation Levels
+ Used to manage how isolated concurrent transactions are from each other.
+ Used to prevent transaction anomalies.

|                  | Dirty Read | Non-repeatable Read | Phantom Read |
| ---------------- | ---------- | ------------------- | ------------ |
| Read Uncommitted | **YES**    | **YES**             | **YES**      |
| Read Committed   | *NO*       | **YES**             | **YES**      |
| Repeatable Read  | *NO*       | *NO*                | **YES**      |
| Serializable     | *NO*       | *NO*                | *NO*         |
## Read Uncommitted
+ Lowest Isolation level
+ Transactions can see uncommitted changes from other transactions
## Read Committed
+ Data is read only after transaction that modifies value commits. 
+ If T1 is doing W(A) and T2 is doing R(A), T2 can only do R(A) after T1 commits its changes.
## Repeatable Read
+ If T2 is ***reading*** value of A, then T1 cannot ***write*** to A until T2 completes i.e. T2 cannot see any modifications made to A during its transaction.
+ T2 can, however modify A, whose changes are visible to T1 when it reads A i.e. ***dirty read*** on side of T1.
## Serializable
+ T2 must wait until T1 finishes i.e. serial execution of transactions.
+ Transactions are executed in a way that **produces the same result as if they were executed sequentially** (one after another), even if they run concurrently.