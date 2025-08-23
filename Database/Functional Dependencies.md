```table-of-contents
title: Index
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
#database 
If value of column **A uniquely identifies** the value of column **B** then **B is functionally dependent on A**. (A $\rightarrow$B). Ex: Stud_Id $\rightarrow$ Stud_name

If X$\rightarrow$Y and X$\rightarrow$Z then X$\rightarrow$Y,Z.  X is **determinant**, Y,Z are **dependents**.
# Types of FD's

## Trivial FD

> A$\rightarrow$B is a trivial FD, if B $\subset$ A

Ex: ABC $\rightarrow$ AB, ABC $\rightarrow$ A, ABC $\rightarrow$ ABC

## Non-Trivial FD
> A$\rightarrow$B is a non-trivial FD, if B $\subsetneq$ A (B is not a subset of A)

## Semi Non-Trivial FD
>  A$\rightarrow$B is a semi non-trivial FD, if **a part of B** is subset of A, **but not all of it**.

Ex: AB $\rightarrow$ BD is semi non-trivial FD because AB$\rightarrow$B is trivial but  AB$\rightarrow$D is non-trivial.

## Multivalued FD
> A$\rightarrow$BC is a multivalued FD, if set of dependents have no FD i.e. **B and C are independent** of each other.

+ Conditions for multivalued FD: For all pairs of tuples t1,t2 in relation R such that `t1[a] = t2[a]` there exists t3, t4 in R such that,
```
t1[a] = t2[a] = t3[a] = t4[a]
t1[b] = t3[b]; t2[b] = t4[b]
t1 = t4; t2=t3
```
where a, b are are attributes in R and t1=t4 etc.. implies all remaining attributes are same.
## Transitive FD
> If A $\rightarrow$ B, B $\rightarrow$ C then A $\rightarrow$ C

## Full FD
> If A $\rightarrow$ B then removing **any** attribute from A, will **break** the dependency i.e. B is **not dependent** on a subset of A.

Ex: FD = {AB $\rightarrow$ C} , the AB $\rightarrow$ C is Full FD since A $\rightarrow$ C or B $\rightarrow$ C does not exist.

## Partial FD
> If A $\rightarrow$ B then removing some attribute from A, might **not break** the dependency i.e. B is **dependent** on a subset of A.

Ex: FD = {AB $\rightarrow$ C, A->C} , the AB $\rightarrow$ C is partial FD since A $\rightarrow$ C exists(removing B).

# Dependency Preserving Decomposition

>Suppose R is a relational schema and F is the set of functional dependencies on R. If R is decomposed into relations R1, R2, ………….…… Rn , each holding functional dependencies F1, F2, …….……… Fn respectively.
>Now this decomposition will be considered as dependency preserving decomposition if and only if-  Closure(F) = Closure(F') where F' = F1 U F2 ...... U Fn.

# Lossless join decomposition
>Lossless join decomposition is a decomposition of a relation R into relations R1, and R2 such that if we perform a natural join of relation R1 and R2, it will return the original relation R.

+ Only ****1NF,2NF,3NF,**** and ****BCNF**** are valid for lossless join decomposition.
+ Employee (Employee_Id, Ename, Salary, Department_Id, Dname) can be decomposed using **lossless decomposition** as, 
	+ Employee_desc (Employee_Id, Ename, Salary, Department_Id)   
	+ Department_desc (Department_Id, Dname)
+ and for **lossy decomposition** as.
	+ Employee_desc (Employee_Id, Ename, Salary)   
	+ Department_desc (Department_Id, Dname)