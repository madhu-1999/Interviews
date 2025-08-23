#java #spring
+ Instance of a class managed by the Spring container (think IOC).
+ @Component indicates that class is a bean.
+ @Controller, @Service, @Repository, @RestController are specialized forms of @Component i.e. they internally use @Component annotation, so they also indicate that the class is a bean.

# @Bean vs @Component
+ @Component is used for classes that we define and DI is done through either constructor or @Autowired.
+ @Bean is used to define bean in config classes. Bean that it defines is typically a third-party class or non-Spring class that the **Spring container has no control over**.
+ Using @Bean gives us the freedom to customize the params of the bean being defined.
```java
@Configuration
public class AppConfig{
	@Bean
	public MyBean() {
		return new MyBean();
	}
}
```
+ @Bean and Config classes are also used when we want to use a class that is in a different package. For example, look at the directory structure. The application is started by running `QuickstartApplication.java` class, which is in `com.example.quickstart` package. The `Invoice` class is in a different package `com.example.beanExample`.
![[Screenshot 2025-03-14 at 3.01.17 PM.png]]
Lets look at the `QuickstartApplication` class, @SpringBootApplication annotation can only find beans in the same package i.e. all beans in `com.example.quickstart` . 
```java
@SpringBootApplication
public class QuickstartApplication {
	public static void main(String[] args) {
		SpringApplication.run(QuickstartApplication.class, args);
	}
}
```
If we have a `User` class that depends on the `Invoice` class in `com.example.beanExample`, **@SpringBootApplication is unable to get an instance of it as it is in a different package**, so on app startup, `UnsatisfiedDependencyException` is thrown.
To fix this, we create a `AppConfig` class that defines the bean of Invoice class (in this context, it is a third-party class, even though it is part of same project !) which the Spring Container can use by creating a context for the `AppConfig` class. The `AppConfig` class **must be in same package** as `QuickstartApplication`.
```java
@Configuration
public class AppConfig {
    @Bean 
    public Invoice getInvoice() {
        return new Invoice();
    }
}

@SpringBootApplication
public class QuickstartApplication {

	public static void main(String[] args) {
		AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
		SpringApplication.run(QuickstartApplication.class, args);
	}
}
```
`AnnotationConfigApplicationContext` is used to read the beans/ properties defined in `AppConfig` class.

# Bean lifecycle
![](https://miro.medium.com/v2/resize:fit:1306/1*5MShKzJV3ClbCRHHWxIWIw.png)
+ Every bean created by IOC container has two methods which can be either overriden directly or we can create custom method with same method signature and annotate as given below:
	+ `public void init()` (Custom method annotation @PostConstruct)
	+ `public void destroy()` (Custom method annotation @PreDestroy)
+ `init()` called after bean is constructed and dependencies injected but before object is requested.
+ `destroy()` is called before the destruction of a bean.
+ 3 ways to configure custom init, destroy methods:
	+ Annotation based: Explained above
	+ XML based: <bean-id="StudentDAO" class="StudentData.StudentDAO" init-method=(enter custom method name) destroy-method= (enter custom method name)>
	+ Interface-based: `InitializingBean` and `DisposableBean` interfaces implemented and `afterPropertiesSet()` (eq.to init()) and `destroy()` methods overriden. 
# Bean Scopes
## Singleton
+ `@Scope("singleton")` 
+ Default scope of a bean
+ Only one instance in the container. Same instance returned each time it is requested.
## Prototype
+  `@Scope("prototype")`
+ Multiple instances in the container. New instance returned each time it is requested.
## Web Aware Scopes
### Request
+  `@Scope("request")`
+ a new object is instantiated once per web request
### Session
+  `@Scope("session")`
+ a new object is instantiated once per web session
### Application/ Global Session
+  `@Scope("application")`
+ a new object is instantiated once per web servlet
### WebSocket
+ `@Scope("websocket")`
+ a new object is instantiated once per websocket
# Further reading
+ [[Spring Boot IOC container|IOC container]] - Clarify @SpringBootApplication & how it performs DI, ApplicationContext