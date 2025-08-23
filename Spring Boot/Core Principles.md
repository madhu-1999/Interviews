#java #spring 
# Dependency Injection (DI) / Inversion of Control (IOC)
Refer [[SOLID#**D**ependency Injection | Dependency Injection]] to understand in depth
+ Traditionally, an object creates its own dependencies (other objects it needs to function). This leads to **tight coupling** between objects, making it harder to test, maintain and reuse.
```java
class ReportGenerator {
    private DatabaseConnection dbConnection;

    public ReportGenerator() {
        this.dbConnection = new DatabaseConnection(); 
        // Creates its own dependency
    }

    public void generateReport() {
        // Use dbConnection to fetch data and generate report
    }
}
```
+ In **IOC**, a separate entity (IOC container) is responsible for providing (injecting) dependencies. This results in loose coupling between objects. 
+ ***@Autowired*** annotation is responsible for DI.
## Types of DI
### **Field Injection**
+ Dependency is set into the field of class directly.
+ Spring internally uses reflection to iterate over fields and resolve dependencies.
+ The container will set the field at some point after object is created i.e. `User` object created, `Order` object will be created at some point. This means that `User` object exists in an invalid/ partially initialized state. 
+ This makes it **prone to runtime errors**. Ex: `User user = new User()` implies `order=null` . If then, `process()` is called, `NullPointerException` is thrown since `order` is null.
+ Cannot be used for `final` dependencies, since they cannot be modified.
+ Makes testing harder, reflection is needed to inject dependency.
```java
@Component
public class User {
	@Autowired
	Order order;
	
	public User() {
        System.out.println("User initialized");
    }

    public void process() {
        order.process();
    }
}
```
### **Setter Injection**
+ DI using setter. @Autowired on setter.
+ DI done when setter called, so `User` object can be in invalid/ partially initialized state after creation. Makes it **prone to runtime errors**.
+ Dependency can be changed after object creation i.e. Order object can be OnlineOrder at one point in time and then can be changed to OfflineOrder instance.
+ This means dependencies ***CANNOT*** be final.
+ Hard to figure out (at a glance) which dependency are injected using setter.
```java
@Component
public class User {

    public Order order;

    public User() {
        System.out.println("User initialized");
    }

    @Autowired
    public void setOrder(Order order) {
        this.order = order;
    }
}
```
### **Constructor injection**
+ DI done at time of object creation. Object is always fully initialized.
+ No need for @Autowired, since Spring can automatically detect constructor with params and inject into it.
+ Can be used with `final` dependencies, since it initializes value of final dependency. `final` usage is not mandatory, but recommended if instance unlikely/should not change.
+ If more than one parametrized constructor, @Autowired should be used on preferred constructor. If no @Autowired, no-arg constructor is called.
+ If any dependency is missing, it will give **compilation error** ***NOT*** runtime error.
+ Hard to maintain if class has a lot of dependencies.
```java
@Component
public class User {

    public final Order order;

    public User(Order order) {
        this.order = order;
    }

}
```

## Common Issues
### **Circular Dependency**
If A depends on B, B also depends on A. In this case A cannot be fully instantiated until B is set as dependency but B requires A to be set as dependency.
***Resolution***:
+ (Preferred) Keep common code on which both are dependent in separate class to break the dependency i.e. Made new common class C, now A depends on C instead of B and B depends on C instead of A.
+ Use @Lazy annotation on @Autowired. 
	+ @Lazy implies bean will be created only when required i.e. no eager initialization, so on application startup, a proxy bean is generated. (To understand proxy refer,[[Spring AOP#AOP Proxy | Proxy]])
	+ Suppose B is labeled with @Lazy so when A is instantiated, proxy bean of B is set as dependency. Then when B is instantiated, A is available to be set as dependency.
+ Use setter/field injection for circular dependencies (since instance is in partially initialized state)
### **Unsatisfied Dependency**
If a class say `Order` has two child classes `OnlineOrder` and `OfflineOrder` . When we try to autowire an instance of order class, a issue arises, which instance should be injected, `OnlineOrder` or `OfflineOrder` ? Since it cannot be resolved at runtime a `NoUniqueBeanDefinitionException` exception is thrown.
***Resolution***:
+  Put @Primary annotation on the class which should be used by default. If `OnlineOrder` is annotate with @Primary, then at app startup, `Order` class instance is injected with instance of `OnlineOrder` .
+ @Qualifier annotation of every child class and on autowired instance. Instance of class identified by @Qualifier (here `OfflineOrder`) is injected into instance of `Order` class.
```java
@Component
@Qualifier("onlineOrder)
public class OnlineOrder {}

@Component
@Qualifier("offlineOrder)
public class OfflineOrder{}

@Component
public class User {
	@Autowired
	@Qualifier("offlineOrder)
	Order order;
}
```
# Aspect Oriented Programming (AOP)
Refer [[Spring AOP]]
