
+ Subclass implements parent class method.
+ The overriden method **must have same method signature**( name and parameters)
+ Final methods **cannot** be overriden.
+ The return type of overriden method must be a subtype of the original return type.

```java 
class Parent {
    Parent getObject() {
        return new Parent();
    }
}

class Child extends Parent {
    @Override
    Child getObject() { // Covariant return type (Child is a subclass of Parent)
        return new Child();
    }
}

public class Test {
    public static void main(String[] args) {
        Parent obj1 = new Parent();
        System.out.println(obj1.getObject()); // Output: Parent@<hashcode>

        Child obj2 = new Child();
        System.out.println(obj2.getObject()); // Output: Child@<hashcode>

        Parent obj3 = new Child();
        System.out.println(obj3.getObject()); // Output: Child@<hashcode>
    }
}

```

+ The overriden method can have a **different access specifier** than the original if it gives **more access**.
+ **Runtime polymorphism** i.e method reference is resolved at runtime.
+ Method is **invoked** based on **object** type.
	+ Ex: If method **reference** is of **superclass** type and **object** type is **subclass**, then **overriden method of subclass is called.**
	```java
public class Test {
    public static void main(String[] args) {
        Parent obj1 = new Parent();
        obj1.display();  // Output: method in Parent

        Child obj2 = new Child();
        obj2.display();  // Output: Overriden method in Child

        Parent obj3 = new Child();
        obj3.display();  // Output: Overriden method in Child
    }
}
```

# Method Hiding vs Method Overriding

+ If subclass implements static method with same signature as superclass static method, the **superclass method is hidden *NOT* overriden.**
+ Method resolution is **compile time** as opposed to runtime for method overriding.
+ Method is **invoked** based on **reference type**.
	+ Ex: If method **reference** is of **superclass** type and **object** is of **subclass** type, then static method of **superclass is called.**
	```java
public class Test {
    public static void main(String[] args) {
        Parent obj1 = new Parent();
        obj1.display();  // Output: Static method in Parent

        Child obj2 = new Child();
        obj2.display();  // Output: Static method in Child

        Parent obj3 = new Child();
        obj3.display();  // Output: Static method in Parent (method hiding)
    }
}
```