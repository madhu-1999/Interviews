+ It is used to indicate that a variable, method or class ***CANNOT*** be modified.
+ Use of final keyword can sometimes improve performance. The compiler can optimize efficiently since value is fixed.
+ Can help secure a part of the program, from malicious overwriting.
## Variable
+ The value of a final variable cannot be modified. i.e. it must be initialized at the time of declaration or through a constructor.
+ If a class has multiple constructors, the uninitialized final variable  must be initialized in all constructors of the class, otherwise a compiler error is thrown.
+ If final variable is static, instead of constructor, it can be initialized through a static block.
+ If final variable is a reference, the reference itself cannot be changed. The object being referred to, however can be modified i.e. add/delete.
+ If a final variable is initialized inside a block/method, its value cannot be modified within the scope of the block/method. i.e. if it was declared globally, value can be modified in global scope.
## Class
+ A final class cannot be inherited/extended.
+ Uses of final class:
	+ Prevent inheritance. Ex: Wrapper classes
	+ Create a Immutable class Ex: String class
## Method
+ Final methods cannot be overridden.
+ Constructor ***CANNOT*** be final (static or abstract) since constructors of a class are not inherited.