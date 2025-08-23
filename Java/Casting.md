+ For casting to work, both datatypes must be compatible with each other.
# Typecasting for primitive types
+ Two types:
	+ Widening
		+ casting to higher data type
		+ byte$\rightarrow$short$\rightarrow$int$\rightarrow$long$\rightarrow$float$\rightarrow$double
		+ Implicit type casting Ex: `float s = a/2.0` where a is int.
	+ Narrowing
		+ casting to lower data type
		+ double$\rightarrow$float$\rightarrow$long$\rightarrow$int$\rightarrow$short$\rightarrow$byte
		+ Explicit type casting Ex: `int a = (int) s/2` where s is float.
# Typecasting between Wrapper and primitive
## Autoboxing
+ Primitive to wrapper conversion.
+ Implicit
## Unboxing
+ Wrapper to primitive conversion
+ Implicit (use .xxValue() for explicit Ex: `a.intValue()`
# Typecasting for non-primitive types
## [[Java Inheritance#Upcasting| Upcasting]]
## [[Java Inheritance#Downcasting| Downcasting]]
