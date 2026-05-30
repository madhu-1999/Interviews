#java #spring #aop 
+ AOP stands for Aspect Oriented Programming.
# AOP and OOP
+ AOP complements OOP 
+ OOP $\rightarrow$ class, AOP $\rightarrow$ aspect
+ OOP $\rightarrow$ modularizes business logic, AOP $\rightarrow$ modularizes cross-cutting concerns i.e. functionality apart from business logic (ex: logging, transaction management).
# Terminology
## Aspect
+ Modularization of a cross-cutting concern i.e. non-business logic functionality (ex: logging, transactions).
+ Defined as a class, annotated with ***@Aspect***. To ensure Spring Boot, picks up on it, also annotate class with ***@Component***.
+ **A aspect cannot be a target for another aspect**.
## Join Point
+ A point during execution of a program, such as method execution or exception handling, before or after which the aspect is applied.
## Advice
+ Action taken by an aspect at a join point.
```java
@Aspect
public class BeforeExample {
	// pointcut expression used inside annotation
	@Before("execution(* com.xyz.dao.*.*(..))") 
	public void doAccessCheck() {
		// ...
	}
}
```
+ Types:
	+  Before: 
		+ Runs before join point but cannot prevent execution flow from leading to join point (except if exception is thrown).
		+ ***@Before*** annotation
	+ After returning: 
		+ Runs after join point if method completes normally i.e. no exception is thrown
		+ ***@AfterReturning*** annotation
	+ After throwing: 
		+ Runs after join point if exception is thrown
		+ ***@AfterThrowing*** annotation
	+ After (finally) advice: 
		+ Runs after join point regardless of exception thrown or normal completion.
		+ ***@After*** annotation
	+ Around: 
		+ Can perform custom behavior before and after method invocation. It can choose to proceed to join point or return its own value/throw a exception.
		+ ***@Around*** annotation
+ Modeled as an interceptor in Spring
## Pointcut
+ Predicate that is used to match join points i.e. based on the predicate, advice is executed for a matching join point.
+ In Spring Boot, can be specified as part of advice annotation or using ***@Pointcut*** to create a named pointcut.
+ Pointcut designators:
	+ execution: 
		+ for matching method execution join point
		+ `execution(* transfer(...))` matches any method named transfer in any package with any arguments
	+ within:
		+ Limits matching of join points within a certain type ex: class, package
		+ `within(com.example.Service.ProductService)` matches all methods in ProductService class
	+ @annotation:
		+ Limits matching to join points having a certain annotation
		+ `@annotation(org.springframework.stereotype.PostMapping)` matches all methods annotated with @PostMapping.
	+ target:
		+ Limits matching to join points where target object is an instance of given type
		+ `target(com.example.Service.ProductService)` matches all methods invoked by object of class ProductService.
	+ @target:
		+ Limits matching to join points where class of target object is annotated with a given type
		+ `@target(org.springframework.stereotype.Service)` matches all methods invoked by objects whose class is annotated with @Service
## Target object
+ An object being advised by multiple aspects.
+ Spring AOP uses runtime proxies, so this object is also a proxy.
+ Whenever a method is called on proxy object, it first delegates to any interceptors (advice) on called method, before calling actual method through actual object. 
## AOP Proxy
+ An object created by Spring AOP to execute advices.
# Enabling AspectJ
+ Configuration class must be annotated with ***@EnableAspectJAutoProxy***.
+ In Spring Boot ***@SpringBootApplication***, ***@EnableAutoConfiguration*** handles the configuration internally.