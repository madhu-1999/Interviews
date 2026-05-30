#spring 
# Default Exception Handling
![spring mvc](https://cdn.jsdelivr.net/gh/springcloud-community/image-bucket/2022/02/08/438c2291278c40e3aae81cf11ff31d0c.png)
+ The `HandlerExceptionResolver` interface is responsible for resolving any exceptions generated at runtime that are not explicitly handled.
+ The `DefaultErrorAttributes` class is used to save exception attributes.
```json
//Example DefaultErrorAttributes object
{
	"timestamp":"2024-10-09T16:22:24",
	"status": 500,
	"error": "Internal Server Error",
	"path": "/api/get-user"
}
```
By default, a 500-Internal Server Error will be thrown. Spring creates a ResponseEntity object and wraps DefaultErrorAttributes object in it to return.
+ If we want a **custom status and error msg** but exception should be **handled by spring**, then:
```java
class CustomException extends RuntimeException {
	HttpStatus status;
	String message;
	public CustomException(HttpStatus status, String message){
		this.status = status;
		this.message = message;
	}
	public Httpstatus getStatus() {
		return status;
	}
	@Override
	//Will be returned when e.getMessage() is called by default.
	public String getMessage() {
		return message;
	}
}
```
+ `HandlerExceptionResolverComposite` is a child interface of `HandlerExceptionResolver` that is invoked upon encountering a runtime exception.
![[Screenshot 2025-03-24 at 3.07.17 PM.png]]
# ExceptionHandlerExceptionResolver
+ Responsible for handling ***@ExceptionHandler*** and ***@ControllerAdvice***.
+ Controller level exception handling with **@ExceptionHandler**:
```java
@RestController
@RequestMapping(value="/api")
public class RestController {

	@GetMapping(value="/get-user")
	public ResponseEntity<?> getUser() {
		//business logic
		throw new CustomException(HttpStatus.BAD_REQUEST, "User id missing");
	}

	@ExceptionHandler(CustomException.class)
	public ResponseEntity<String> handleException(CustomException ex){
		return new ResponseEntity<>(ex.getStatus(), ex.getMessage());
	}
}
```
Anytime a `CustomException` is thrown in `RestController` class, the `handleException()` method will handle it
+ Multiple handlers can be written for different exceptions.
+ One handler can be used for multiple exceptions. `handleException()` parameter is of type `Exception` since it handles multiple classes.
```java
@ExceptionHandler({CustomException.class, IllegalArgumentException.class})
public ResponseEntity<String> handleException(Exception ex){
	return new ResponseEntity<>(ex.getStatus(), ex.getMessage());
}
```
+ Instead of returning ResponseEntity, we can let Spring handle it:
```java
@ExceptionHandler(CustomException.class)
	public void handleException(HttpServletResponse response, CustomException ex) throws IOException{
		response.sendError(HttpStatus.BAD_REQUEST.value(), ex.getMessage());
	}
```
+ Global Exception Handling with ***@ControllerAdvice*** and ***@ExceptionHandler***
```java
@ControllerAdvice
public class GlobalExceptionHandler {

	@ExceptionHandler(CustomException.class)
	public ResponseEntity<String> handleException(CustomException ex){
		return new ResponseEntity<>(ex.getStatus(), ex.getMessage());
	}
}

```
It is applicable to all controllers in the application. Can narrow down scope to same package, annotations etc..
# ResponseStatusExceptionResolver
+ Handles uncaught exception for exception annotated with ***@ResponseStatus***.
```java
@ResponseStatus(HttpStatus..BAD_REQUEST)
class CustomException extends RuntimeException {
	HttpStatus status;
	String message;
	public CustomException(String message){
		super(message);
	}
}

@RestController
@RequestMapping(value="/api")
public class RestController {

	@GetMapping(value="/get-user")
	public ResponseEntity<?> getUser() {
		//business logic
		throw new CustomException("User id missing");
	}
}
// defaulterrorattributes object with 400-user id is missing.
```
# DefaultHandlerExceptionResolver
+ Handles Spring related exceptions like *MethodNotFound*, *NoResourceFound*.