+ Stands for **RE**presentational **S**tate **T**ransfer.
+ It is an **architectural style** that defines a set of constraints used to create web services.
+ **REST API** is a simple, flexible way to access web services without any processing using HTTP methods (typically).
```table-of-contents
title: Index
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
# REST Constraints
+ Requires a **Client-Server** architecture. 
+ REST API calls are **stateless** .i.e. the calls contain all the information needed to fulfill the request successfully. ***NOTE***: HTTP methods are used since HTTP is a stateless protocol.
+ Since it is stateless, this increases overhead for server processing, thus **using cache** storage of data is recommended. 
+ Treats server objects as resources that can be created or destroyed.
+ Server responses can be of any type: HTML, JSON (typically), XML etc..
# Terminology
## Idempotent
It means that repeating the same call **does not change the result**. Resource on server may/may not change.
## Resource
/api/students is a resource
## Endpoint
GET:/api/students is an endpoint
## Route
Methods that wait for a HTTP request to be routed to a specific endpoint, which then execute to fulfill the request.
## URI
Address of resource. Ex: http://api.example.com/api/students
Best Practices:
+ Use forward slashes (/) to indicate hierarchy
+ Do not use trailing slashes
+ Use hyphens *NOT* underscores to improve readability. Ex: http://api.example.com/device-management
+ Use lowercase letters
+ Do not use file extensions
+ Use a query component to filter URI Ex: http://api.example.com/device-management?region=USA
# HTTP Methods
## GET
+ Used to read a resource from the server.
+ It does not have a body (Guideline, not enforced).
+ Ex: GET:/api/students [To get all students]
+ It is **idempotent**.
## POST
+ Used to create resource on server.
+ Ex: POST:/api/students {"name" : "Raj}
+ It is **not idempotent**.
## PUT
+ Used to create/ update resource on server.
+ Primarily used to update resource. Can be used to create (preferred, POST).
+ Used to create when PUT request is for a non-existent resource id. Id must be in request body as well as path.
+ In update, it **replaces entire resource**.
+ Ex: PUT:/api/students/1 {"name": "Rohan"}
+ It is **idempotent**.
## PATCH
+ Used to update a resource on the server.
+ PATCH is used when we want to **update a few attributes**, and not entire resource.
+ Ex: PATCH:/api/students/1 {"name": "Rohan"}
+ It is **idempotent**.
## DELETE
+ Used to delete a resource on the server.
+ ex: DELETE:/api/students/1
+ It is **idempotent**.
