#java 
```table-of-contents
title: Index
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
![](https://miro.medium.com/v2/resize:fit:1400/1*lLBwoFv6RAZ15vRuK6yFQg.png)

| Aspect              | JDK                                                 | JRE                                                 | JVM                                                                           |
| ------------------- | --------------------------------------------------- | --------------------------------------------------- | ----------------------------------------------------------------------------- |
| Purpose             | Used to develop Java applications                   | Used to run Java applications                       | Responsible for running Java code                                             |
| Platform Dependency | Platform-dependent                                  | Platform-dependent                                  | Platform-Independent                                                          |
| Includes            | It includes development tools like (compiler) + JRE | It includes libraries to run Java application + JVM | It runs the java byte code and make java application to work on any platform. |
| Use Case            | Writing and compiling Java code                     | Running a Java application on a system              | Convert bytecode into native machine code                                     |
# Flow of execution
## JDK
+ Write code in java source file (.java)
+ **javac** compiles it into .class file which is **bytecode**.
## JRE
+ **Class loader** loads .class bytecode file into memory.
+ **Bytecode verifier** verifies that the bytecode is correct and does security checks.
## JVM
+ Converts bytecode to machine code and executes it using interpreter.
# JVM
![Class Loader Sub System](https://media.geeksforgeeks.org/wp-content/uploads/20240903150527/Class-Loader-660.webp)
## Class loaders
+ Performs majorly 3 functions:
	+ Loading
	+ Linking
	+ Initialization
There are 3 types of class loaders
### **Bootstrap Class loader**
Loads core Java classes from JAVA_HOME/lib. It is implemented in native code and not a Java object.
### **Extension Class loader**
Loads classes from JAVA_HOME/jre/lib/ext directory or any direectory specified by`java.ext.dirs` env var. It is implemented in Java.
### **Application Class loader**
Loads classes from application classpath specified by `java.class.path` env var. It is implemented in Java.
## Memory Areas
### **Method area**
+ Class name, immediate parent name, static variables, methods etc stored for classes.
+ Only one per JVM, **shared resource**.
### **Heap Area**
+ All non-primitive data type instances (objects, enum, wrapper class, string) stored here
+ Only one per JVM, **shared resource**.
### **Stack area**
+ All stack frames stored here.
+ For every thread, one stack frame is created. **Not a shared resource**.
+ A stack frame stores all method calls, local variables, references to objects in heap area. Destroyed when scope ends i.e. thread is destroyed.
### **PC Registers**
+ Stores address of current instruction being executed by a thread.
+ Each thread gets its own PC registers, **not a shared resource**.
### **Native method stack**
+ Each thread has its own, **not a shared resource**.
+ Stores native method info
## Execution Engine
+ Responsible for executing bytecode.
+ Three major parts:
	+ ### Interpreter
		+ Executes bytecode line by line.
		+ Disadvantage: For repeated method calls, need to interpret same code again and again.
	+ ### Just-In-Time Compiler (JIT)
		+ Compiles bytecode to native machine code
		+ When interpreter sees repeated method calls, JIT supplies native code to interpreter, makes execution faster.
	+ ### [[Garbage Collection|Garbage Collector]] 
			+ Destroys unreferenced objects from heap
## Java Native Interface (JNI)
+ Interface that allows Java to call native method libraries (C/C++) and be called by native methods.
## Native Method Libraries
+ Libraries required for executing native methods. Written in (C/C++)