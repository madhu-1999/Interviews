#java #spring

# @Controller
+ Marks class as a controller i.e. route
+ By default, it assumes name of view (.jsp file) is being returned.
+ If we want to return any other type of data annotate with @ResponseBody on endpoint
```java
@Controller
public class HelloWorldController {

    @GetMapping(path = "/hello")
    @ResponseBody
    public String helloWorld(){
        return "Hello World!";
    }   
}
```

# @RestController
+ Combines @Controller and @ResponseBody together.
```java
@RestController
public class HelloWorldController {

    @GetMapping(path = "/hello")
    public String helloWorld(){
        return "Hello World!";
    }   
}
```

# HTTP method annotations

|                                                                 |                                 |
| --------------------------------------------------------------- | ------------------------------- |
| @RequestMapping(path = "/hello", method = RequestMethod.GET)    | @GetMapping(path = "/hello")    |
| @RequestMapping(path = "/hello", method = RequestMethod.POST)   | @PostMapping(path = "/hello")   |
| @RequestMapping(path = "/hello", method = RequestMethod.PUT)    | @PutMapping(path = "/hello")    |
| @RequestMapping(path = "/hello", method = RequestMethod.DELETE) | @DeleteMapping(path = "/hello") |
| @RequestMapping(path = "/hello", method = RequestMethod.PATCH)  | @PatchMapping(path = "/hello")  |
## When to use @RequestMapping?
+ Can annotate class and methods while @GetMapping etc can only annotate methods.
+ Use @GetMapping etc **on method level** to keep code clean, concise, readable
+  Use @RequestMapping **on class level** to provide base path for all apis in that controller.
```java
@RestController
@RequestMapping(path = "/api/v1")
public class HelloWorldController {

    @GetMapping(path = "/hello") // endpt: /api/v1/hello
    public String helloWorld(){
        return "Hello World!";
    }

    @GetMapping(path = "bye") // endpt: /api/v1/bye
    public String byeWorld(){
        return "Bye World!";
    }    
}
```

# Parameter Annotations
## @RequestParam
+ Binds query parameters to method parameter if exists, else method parameter will be null.
+ if method parameter is of primitive/ wrapper/ String/ enum type, value will be typecast to required type (as long as it is valid value). If invalid 400 Bad Request is thrown.
+ If no value is passed, since primitive types cannot be null, `MissingServletRequestParameterException` will be thrown only if no `defaultValue` is provided. 
+ QueryParam can be made required using `required=true`. If absent, `MissingServletRequestParameterException` is thrown. 
+ ***CANNOT*** directly map to objects. Use @ModelAttribute for form submissions.
```java
@RestController
@RequestMapping(path = "/api/v1")
public class HelloWorldController {

    @GetMapping(path = "/hello")
    // endpt: /api/v1/hello?name=Madhu or /api/v1/hello?name=
    public String helloWorld(@RequestParam(name = "name", required = false, defaultValue = "") String name) {
		
        return "Hello " + name + " !";
    }
}
```
## @RequestBody
+ Binds json/xml payload in request body to method parameter, which is typically a object.
+ DTO (POJO) of request payload is created and actual request payload is mapped to DTO object.
```java
@RestController
@RequestMapping(path = "/api/v1")
public class HelloWorldController {

    @GetMapping(path = "/hello")
    public String helloWorld(@RequestBody Data data) {
		
        return "Hello " + data.name + " !";
    }
}
```
## @PathVariable
+ Maps path param to method param.
```java
@RestController
@RequestMapping(path = "/api/v1")
public class HelloWorldController {

    @GetMapping(path = "/hello/{name}")
    // endpt: /api/v1/hello/Madhu
    public String helloWorld(@PathVariable(value = "name") String name) {

        return "Hello " + name + " !";
    }
}
```

# ResponseEntity
+ Represents entire HTTP response i.e. header, status, response body etc
+ It is a class ***NOT*** annotation.
+ Need to specify type of Response in return type of method like `ReponseEntity<T>` 
```java
@RestController
@RequestMapping(path = "/api/v1")
public class HelloWorldController {

    @GetMapping(path = "/hello")
    public ResponseEntity<String> helloWorld() {

        return ResponseEntity.status(HttpStatus.OK).body("Hello World!");
    }
}
```
