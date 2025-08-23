#java #spring
+ Part of core Spring framework
+ Responsible for lifecycle of beans, managing thier dependencies and injects them (DI) where needed. 
+ ***Note***: It is possible for an instance of class to be managed by the developer manually, so it is possible that not all beans are managed by Spring.
+ There are 2 types of IOC containers:
	+ BeanFactory: basic
	+ ApplicationContext: advanced
# BeanFactory vs ApplicationContext
## BeanFactory
+ It is an interface.
+ Basic container with DI capabilities.
+ Lazy initialization of beans i.e. created only when requested
+ Limited functionality
+ Manual Bean Post-processor registration
## ApplicationContext (Preferred)
+ It is an interface that extends BeanFactory
+ Advanced container with extra functionality along with DI capability
+ Eager initialization of beans i.e. created on app startup. Annotate with @Lazy if lazy initialization needed for a particular bean.
+ Extra features like Spring AOP support, resource loading, event publication.
+ Automatic bean post processor registration

# @ SpringBootApplication
Below is a basic spring boot main class.
```java
@SpringBootApplication
public class QuickstartApplication {
	public static void main(String[] args) {
		SpringApplication.run(QuickstartApplication.class, args);
	}

}
```
+ @SpringBootApplication used only ***ONCE*** in an application (or package module) on top of the class contained main method.
+ Creates the IOC container(ApplicationContext), finds and auto-configures all beans in the same package.
+ Combination of 3 annotations:
	+ @EnableAutoConfiguration
	+ @ComponentScan
	+ @SpringBootConfiguration
## @SpringBootConfiguration
+ Internally uses @Configuration i.e. class where bean definitions are stored so IOC can load it.
+ Responsible for creating the internal `ApplicationContext` . 
## @ComponentScan
+ Scans base package and sub-packages to find all the beans which need to be auto-configured.
+ Base package is the package in which main class exists.
## @EnableAutoConfiguration
+ Auto configures beans into internal `ApplicationContext`. i.e auto generates bean definitions.
+ Only beans in same package/classpath as main class (annotated with @SpringBootApplication) are auto-configured.
# Further reading:
+ [[Core Principles]] -> What is IOC/DI?
