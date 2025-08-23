#database 
+ Process to **reduce** or **eliminate** data redundancy.
+ Data redundancy **increases database size** and leads to **inconsistencies in CRUD** operations.

```table-of-contents
title: Index
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
# Anomalies that Normalization aims to eliminate
## Insert Anomaly

This occurs when inserting data is not possible because data is missing or incomplete.
Ex: Worker Table

| Worker_id | Worker_name | Worker_dept | Worker_address |
| --------- | ----------- | ----------- | -------------- |
| 65        | Ramesh      | ECT001      | Jaipur         |
| 65        | Ramesh      | ECT002      | Jaipur         |
| 73        | Amit        | ECT002      | Delhi          |
| 76        | Vikas       | ECT501      | Pune           |
| 76        | Vikas       | ECT502      | Pune           |
| 79        | Rajesh      | ECT669      | Mumbai         |
If we insert a record with empty `Worker_dept` then the missing data creates a anomaly, since a worker *MUST* belong to a department.
## Update Anomaly

This occurs when updating a record can lead to inconsistent data in other records or other tables in the database.
Ex:

| Worker_id | Worker_name | Worker_dept | Worker_address |
| --------- | ----------- | ----------- | -------------- |
| 65        | Ramesh      | ECT001      | Jaipur         |
| 65        | Ramesh      | ECT002      | Jaipur         |
| 73        | Amit        | ECT002      | Delhi          |
| 76        | Vikas       | ECT501      | Pune           |
| 76        | Vikas       | ECT502      | Pune           |
| 79        | Rajesh      | ECT669      | Mumbai         |
If we change `Worker_Address` of ***Ramesh*** in one record, we have inconsistent data in the other records of ***Ramesh***.
## Delete Anomaly

This occurs when we delete data that leads to unintentional data loss.
Ex:

| Worker_id | Worker_name | Worker_dept | Worker_address |
| --------- | ----------- | ----------- | -------------- |
| 65        | Ramesh      | ECT001      | Jaipur         |
| 65        | Ramesh      | ECT002      | Jaipur         |
| 73        | Amit        | ECT002      | Delhi          |
| 76        | Vikas       | ECT501      | Pune           |
| 76        | Vikas       | ECT502      | Pune           |
| 79        | Rajesh      | ECT669      | Mumbai         |
If we delete `Worker_dept = ECT669` , then it leads to unintentional data loss of worker ***Rajesh*** since he is only employee in the department.

# Normal Forms

## 1NF

A table is in 1NF if it has **no multi-valued attributes** i.e. each cell has a single value.
A multi-valued attribute/column contains multiple values in a cell.
Ex: Storing multiple phone numbers in single cell separated by commas.

## 2NF

+ A table is in 2NF if it is 1NF and all non-prime attributes are fully functionally dependent[^1] on the prime attribute.
+ If a partial dependency exists, we remove the partially dependent attribute(s) from the relation by placing them in a new relation along with a copy of their determinant.
Ex: `{STUD_NO, COURSE_NO}` is the candidate key/ prime attribute and `COURSE_FEE` is the non-prime attribute. COURSE_NO $\rightarrow$ COURSE_FEE exists since COURSE_NO is enough to determine COURSE_FEE. The non-prime attribute is dependent on a subset of the prime attribute so a partial dependency exists.
![courses_table3](https://media.geeksforgeeks.org/wp-content/uploads/20250108151428253795/courses_table3.webp)
To convert to 2NF, we separate the non-prime attribute (COURSE_FEE) along with copy of its determinant (COURSE_NO) into a separate table.
![courses_table5](https://media.geeksforgeeks.org/wp-content/uploads/20250108151615641852/courses_table5.webp)
![courses_table4](https://media.geeksforgeeks.org/wp-content/uploads/20250108151633723773/courses_table4.webp)

## 3NF
+ A relation is in 3NF if it is in 2NF and no non-prime attribute is transitively dependent on the primary key. i.e. atleast one of the following conditions hold for every non-trivial FD[^2] X$\rightarrow$Y:
	+ X is a super key[^3]
	+ Y is a prime attribute( each element of Y is part of some candidate key).
+ Ex: FD = {CAND_NO $\rightarrow$ CAND_NAME, CAND_NO $\rightarrow$CAND_STATE, CAND_STATE $\rightarrow$ CAND_COUNTRY, CAND_NO $\rightarrow$ CAND_AGE}![candidate](https://media.geeksforgeeks.org/wp-content/uploads/20250108155404622708/candidate.webp)
Here, CAND_NO $\rightarrow$CAND_STATE,  CAND_STATE $\rightarrow$ CAND_COUNTRY thus, CAND_NO $\rightarrow$ CAND_COUNTRY. A non-prime attribute *CAND_COUNTRY* is transitively dependent on the primary key *CAND_NO*. 
+ Thus, we separate the non-prime dependent along with a copy of its non-prime determinant into a separate table.
![courses_table_7](https://media.geeksforgeeks.org/wp-content/uploads/20250108160049760531/courses_table_7.webp)
![courses_table_8](https://media.geeksforgeeks.org/wp-content/uploads/20250108160118089197/courses_table_8.webp)

## BCNF
A relation should be in 3NF and X should be super key for every non-trivial FD X$\rightarrow$Y in the relation.
Ex: FD = { (student, Teacher) $\rightarrow$ subject, (student, subject) $\rightarrow$ Teacher, Teacher $\rightarrow$ subject }![courses_table_9](https://media.geeksforgeeks.org/wp-content/uploads/20250108163223207532/courses_table_9.webp)
Candidate keys = {(student, teacher) and (student, subject)}. Table is not in BCNF since in teacher$\rightarrow$subject, teacher is not a super key.

## 4NF
A relation is in 4NF if it is in BCNF and there are no multi-valued dependency[^4].

## 5NF
A relation is in 5NF if it is in 4NF and there is no join dependency i.e. lossless decomposition[^5].

# Advantages
+ Eliminates data redundancy
+ Ensures each table stores relevant data only, improving data integrity
+ Simplifies data updation so we need to update only one place, rather than multiple.
+ Enhances querying using various criteria as data is stored in smaller, more specific tables.
+ Ensures data consistency across database.

# Disadvantages
+ Increases performance overhead due to additional joins.
+ Loss of data context due to being split up into smaller, more specific tables.
+ Increases complexity of database design if not done correctly.

[^1]: [[Functional Dependencies#Full FD]]

[^2]: [[Functional Dependencies#Non-Trivial FD]]

[^3]: [[Database Keys#Super Key]]

[^4]: [[Functional Dependencies#Multivalued FD]]

[^5]: [[Functional Dependencies#Lossless join decomposition]]
