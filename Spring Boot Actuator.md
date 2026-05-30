#java #spring 
+ Needs `spring-boot-starter-actuator` dependency.
+ Includes additional features to monitor and manage application to make it production-ready.
+ It provides a number of inbuilt endpoints, which are autoconfigured when available.
+ **Default mapping** for endpoints is after `/actuator` prefix Ex: `/actuator/health`
+ By **default**, all endpoints except `shutdown` are **unrestricted**.
# In-built Endpoints

| Endpoint    | Function                                                              |
| ----------- | --------------------------------------------------------------------- |
| beans       | Complete list of all beans in application                             |
| caches      | Exposes available caches                                              |
| env         | Exposes properties from `ConfigurableEnvironment`                     |
| flyway      | List of all flyway migrations applied                                 |
| health      | Shows health related info                                             |
| info        | Application info. **Default**: empty                                  |
| metrics     | Shows metrics info for app                                            |
| loggers     | List all loggers and allows modification of logging level at runtime  |
| threadDump  | Thread dump of all threads in app                                     |
| heapDump    | Heap dump of app (Diagnose memory related issues)                     |
| auditevents | Exposes audit events info, like authentication (Can customize events) |
| shutdown    | Shuts down app gracefullt (**Only works for jar**)                    |

# Enabling access
+ By default, only `/health` is exposed.
+ Endpoint enabling/disabling properties written to `.properties` or `.yaml` file.
+ To enable/disable a particular endpoint use `management.endpoint.<id>.enabled` property
```
management.endpoint.shutdown.enabled=true
```
+ To enable `include` /disable `exclude` a list of endpoints:
```
// Expose list
management.endpoints.web.exposure.include=health,shutdown
// Expose all
management.endpoints.web.exposure.include=*
// Dispose list
management.endpoints.web.exposure.exclude=beans,cache
//Dispose all
management.endpoints.web.exposure.exclude=*
```
***NOTE***: `exclude` has higher precedence that `include` i.e. if a endpoint is listed under both include and exclude properties, it will be excluded since exclude > include.
# Controlling access
+ Access control properties are written to .`properties` or .`yaml` file
+ For permitted access use `management.endpoint.<id>.access` property
```
management.endpoint.shutdown.access=unrestricted
```