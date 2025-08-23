#java
+ Means to achieve abstraction.
+ Declared using **abstract** keyword:
```java
public abstract class A{}
```

+ It can contain **abstract** and **non-abstract methods**, thus it provides only **partial** abstraction.
+ **Cannot** be instantiated.
+ Can have **constructors**, which can be used by subclasses to initialize common fields.
+ Methods declared as **final**, cannot be abstract.
+ If a class has a abstract method, it **must** be declared abstract.
+ It can have **static** methods.
+ A class can be abstract without having a abstract method.