#java 
+ Java is partially **object oriented** since it has primitive data types.
# Primitive
| Default values             | Primitive type | Size              | Datatype Usage        |
| -------------------------- | -------------- | ----------------- | --------------------- |
| false                      | `boolean`      | 1-bit             | true and false values |
| 0                          | `byte`         | 1 byte (8 bits)   | numerical values      |
| \u0000                     | `char`         | 2 bytes (16 bits) | 1 character           |
| 0                          | `short`        | 2 bytes (16 bits) | numerical values      |
| 0                          | `int`          | 4 bytes (32 bits) | numerical values      |
| 0.0 7 digits of precision  | `float`        | 4 bytes (32 bits) | floating point value  |
| 0                          | `long`         | 8 bytes (64 bits) | numerical values      |
| 0.0 15 digits of precision | `double`       | 8 bytes (64 bits) | floating point value  |

# Reference
+ Class(includes Enum), Interfaces(includes Annotation), Arrays
+ Default value is `null`
+ Holds address of memory location where value is actually stored (think pointer).
+ Equality of value using `equals()` , equality of reference `==`.
```java
Example a = new Example(10);
Example b  = new Example(10);
System.out.println(a == b) //false since different objects are created in heap so both point a and b point to diff memory locations.
System.out.println(a.equals(b)) //true since same value 10 is passed.
```
## Strings
+ **Immutable** (Not to be confused with **final**)
```java
/* "hello" string in string pool created. s points to "hello" string in string pool*/
String s = "hello"; 
/* "hi" string in string pool created. s points to "hi" string in string pool*/
s = "hi";
// s is a reference that points to actual string. string itself cannot be modified.
```
+ All **string literals** are stored in **String Pool** which is part of heap memory in JVM. 
```java
String s1 = "hello" // No such string in string pool, create new
String s2 = "hello" // Already in string pool, point to same ref
System.out.println(s1==s2) // true since they point to same reference
```
+ If a string object is created using **new** keyword, it is **not placed in string pool**.
```java
String s1 = "hello" // No such string in string pool, create new
String s2 = new String("hello") // New object created in heap
System.out.println(s1==s2) // false since they point to diff reference
```
