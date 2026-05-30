#java 

| Aspect     | Error                                                                                                                                                                                                 | Exception                                                                                                                                                                |
| ---------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Definition | An Error indicates a serious problem that a reasonable application should not try to catch.                                                                                                           | Exception indicates conditions that a reasonable application might try to catch                                                                                          |
| Cause      | Caused by issues with the JVM or hardware.                                                                                                                                                            | Caused by conditions in the program such as invalid input or logic errors.                                                                                               |
| Examples   | [OutOfMemoryError](https://www.geeksforgeeks.org/understanding-outofmemoryerror-exception-java/)<br><br>[StackOverFlowError](https://www.geeksforgeeks.org/stackoverflowerror-in-java-with-examples/) | [IOException](https://www.geeksforgeeks.org/handle-an-ioexception-in-java/)<br><br>[NullPointerException](https://www.geeksforgeeks.org/null-pointer-exception-in-java/) |
# Exception Hierarchy
![Exception Hierarchy in Java](https://media.geeksforgeeks.org/wp-content/uploads/20241218154434430661/Exception-Handling-768.png)
## Checked Exception
+ Compile time exceptions
+ Ex: ClassNotFoundException, IOException, SQLException
+ Custom checked exception extends `Execption` class.
## Unchecked Exception
+ Runtime exceptions
+ Ex: NullPointerException, ArithmeticException, ArrayIndexOutOfBoundsException
+ Custom unchecked exception extends `RuntimeException` class

# JVM Exception Handling
1. The run-time system searches the call stack for an Exception handler
2. It starts searching from the method where the exception occurred and proceeds backward through the call stack.
3. If a handler is found, the exception is passed to it.
4. If no handler is found, the default exception handler terminates the program and prints the stack trace.![Flow of class stack for exceptions in Java](https://media.geeksforgeeks.org/wp-content/uploads/20230714113633/Exceptions-in-Java2-768.png)
# Exception Handling
+ try-catch block
+ try-multiple catch block
+ try-catch-finally block