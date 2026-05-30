+ An [[Interface]] with only **one abstract method**. 
+ Also called Single Abstract Method (***SAM***).
+ ***@FunctionalInterface*** annotation can be used at the top of the interface, optionally. 
+ ***@FunctionalInterface*** restricts us and throws compilation error if we try to add more than 1 abstract method.
+ They can have multiple ***static*** and ***default*** methods.
```table-of-contents
title: Index
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
# Ways to implement Functional Interface
## **Implements** keyword
```java
@FunctionalInterface
public interface Bird {
	void canFly(String val);
}

public class Eagle implements Bird {
	@Override
	void canFly(String val){//code}
} 

public class Main {
	public static void main(String args[]) {
		Eagle eagle = new Eagle();
		eagle.canFly("vertical");	
	}
}
```

## Anonymous class
```java
@FunctionalInterface
public interface Bird {
	void canFly(String val);
}

public class Main {
	public static void main(String args[]) {
		Bird eagle = new Bird() {
			@Override
			void canFly(String val){//some code}
		};
		eagle.canFly("vertical");
	}
}
```

## Lambda
```java
@FunctionalInterface
public interface Bird {
	void canFly(String val);
}

public class Main {
	public static void main(String args[]) {
		Bird eagle = (String val) -> {
			//some code
		};
		eagle.canFly("vertical");
	}
}
```

# Built in Java Functional Interfaces
1. Runnable: Contains ***run()*** method
2. Comparable: Contains ***compareTo()*** method.
3. ActionListener: Contains ***actionPerformed()*** method
4. Callable: Contains ***call()*** method.

# Types of Functional Interfaces in Java
## Consumer
+ Represents an operation that accepts an input but returns no result.
```java
@FunctionalInterface
public interface Consumer<T> {
	void accept(T t);
	default Consumer<T> andThen(Consumer<? super T> after);
}

```
+ accept() method
```java
public class Main {
	public static void main(String args[]){
		Consumer<Integer> consumer = (Integer val) -> {
			// some code
		};
		consumer.accept(11);
	}
}
```
+ andThen(): accepts a parameter ***after***,which is the Consumer to be applied after current one, and returns a composed Consumer that applies current Consumer, followed by the after one.
```java
public class Main {
	public static void main(String[] args) {
		Consumer<String> printConsumer1 
		= (String val) -> System.out.println("Hello "+val);

		Consumer<String> printConsumer2 
		= (String val) -> System.out.println("Day 1");

		printConsumer1.andThen(printConsumer2).accept("Alice");
	}
}
```
***Note***: andThen() is before accept() because accept() is a terminal function that does not return a value. andThen() returns a Consumer object to chain consumers together. 
## Predicate
+ Represents function that accept one argument and returns a boolean.
```java
@FunctionalInterface
public interface Predicate<T> {
	boolean test(T t);
	default Predicate<T> or(Predicate<T> other);
	default Predicate<T> negate();
	default Predicate<T> and(Predicate<T> other);
	static Predictae<T> isEqual(Object targetRef);
}
```
+ test() method: Accepts one argument and returns true if argument matches predicate, else false
```java
public class Main {
	public static void main(String[] args) {
		Predicate<Integer> isGreater 
		= (Integer a) -> { return a>18; };

		System.out.println(isGreater.test(32)); // Output:true
	}
}
```
+ or() method: Accepts a predicate that will be logically-ORed with this predicate and returns a composed predicate.
```java
public class Main {
	public static void main(String[] args) {
		Predicate<Integer> isGreater 
		= (Integer a) -> { return a>18; };

		Predicate<Integer> isLesser 
		= (Integer a) -> {return a<5;};

		System.out.println(isGreater.or(isLesser).test(32));//true
	}
}
```
+ negate() method: Returns a predicate that is logical negation of this predicate.
```java
public class Main {
	public static void main(String[] args) {
		Predicate<Integer> isGreater 
		= (Integer a) -> { return a>18; };

		System.out.println(isGreater.negate().test(32));//false
	}
}
```
***Note***: can be used without chaining on its own also.
+ and() method: Accepts a predicate that will be logically-ANDed with this predicate and returns a composed predicate.
```java
public class Main {
	public static void main(String[] args) {
		Predicate<Integer> isGreater 
		= (Integer a) -> { return a>18; };

		Predicate<Integer> isLesser 
		= (Integer a) -> {return a<35;};

		System.out.println(isGreater.and(isLesser).test(32));//true
	}
}
```
+ isEqual() method: Returns a predicate that tests if two are equal according to Object.equals(Object, Object).
```java
public class Main {
    public static void main(String[] args) {
        Predicate<String> isHello = Predicate.isEqual("Hello");

        System.out.println(isHello.test("Hello")); // Output: true
        System.out.println(isHello.test("World")); // Output: false
    }
}
```
## Supplier
+ Represent the supplier of the result, accepts no parameter but returns a result
```java
@FunctionalInterface
public interface Supplier<T> {
	T get();
}

public class Main {
	public static void main(String args[]){
		Supplier<Integer> supplier = () -> {
			// some code
		};
		int x = supplier.get();
	}
}

```
## Function
+ Represents a function that accepts an argument and returns a result.
```java
@FunctionalInterface
public interface Function<T, R> {
	R apply (T t);
	default <V> Function<T, V> andThen(Function<? super R, ? extends V> after);
	default <V> Function<V, R> compose(Function<? super V, ? extends T> before);
	static <T> Function<T, T> identity()
}
```
+ apply() method takes one argument and returns a value.
```java
public class Main {
	public static void main(String[] args) {
		Function<Integer, Integer> addItself 
		= (Integer a) -> { return a+a; };

		System.out.println(addItself.apply(3));
	}
}
```
+ andThen() method: accepts a parameter ***after*** to be applied after current one, and returns a composed function.
```java
public class Main {
	public static void main(String[] args) {
		Function<Integer, Integer> add 
		= (Integer a) -> { return a+a; };

		Function<Integer, Integer> multiply2 
		= (Integer a) -> { return a*a; };

		System.out.println(add.andThen(multiply2).apply(3));
	}
}
```
***Note***: andThen() is before accept() because apply() is a terminal function. andThen() returns a Function object to chain functions together. 
+ compose() method: accepts a parameter ***before*** that is applied first and then current one, and returns a composed function.
```java
public class Main {
	public static void main(String[] args) {
	Function<Integer, Double> halve 
	= (Integer a) -> { return a/2.0; };

	Function<Double, Integer> multiplyItself 
	= (Double a) -> { return (int) (a*a);};

	System.out.println(halve.compose(multiplyItself).apply(3.0));
	}
}
```
***Note***: compose() is before apply() because apply() is a terminal function. compose() returns a Function object to chain functions together. 
+ identity() method: Returns a function that returns its only argument.
```java
public class Main {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

        // Using Function.identity() in map()
        List<String> result = names.stream()
                                   .map(Function.identity()) // Returns the same element
                                   .collect(Collectors.toList());

        System.out.println(result); // Output: [Alice, Bob, Charlie]
    }
}
```

## BiConsumer
+ Represents a function that accepts ***two*** arguments and returns no value.
```java
@FunctionalInterface
public class BiConsumer {
	void accept(T t, U u);
	default BiConsumer<T, U> andThen(BiConsumer<? super T, ? super U> after);
}
```
+ accept() method
```java
public class Main {
    public static void main(String[] args) {
        BiConsumer<String, Integer> printDetails = (name, age) -> 
            System.out.println(name + " is " + age + " years old.");

        printDetails.accept("Alice", 25);
    }
}
```
+ andThen() method: Accepts a parameter ***after*** which is applied after current one and returns a composed BiConsumer.
```java
public class Main {
    public static void main(String[] args) {
        BiConsumer<String, Integer> greet = (name, age) -> 
            System.out.println("Hello " + name + ", Age: " + age);

        BiConsumer<String, Integer> birthday = (name, age) -> 
            System.out.println("Happy Birthday, " + name + "! You are now " + (age + 1));

        BiConsumer<String, Integer> combined = greet.andThen(birthday);

        combined.accept("Alice", 25);
    }
}
```
## BiPredicate
 Represents function that accept ***two*** arguments and returns a boolean.
```java
@FunctionalInterface
public interface BiPredicate<T, V> {
	boolean test(T t, V v);
	default BiPredicate<T, V> or(BiPredicate<? super T, ? super V>  other);
	default BiPredicate<T, V> negate();
 	default BiPredicate<T> and(BiPredicate<? super T, ? super V> other);
}
```
+ test() method: Accepts two arguments and returns true if argument matches predicate, else false
```java
public class Main {
    public static void main(String[] args) {
        BiPredicate<String, String> isSameLength = (s1, s2) -> s1.length() == s2.length();

        System.out.println(isSameLength.test("hello", "world"));  // true
    }
}
```
+ or() method: Accepts a predicate that will be logically-ORed with this predicate and returns a composed predicate.
```java
public class Main {
    public static void main(String[] args) {
        BiPredicate<Integer, Integer> isEven = (a, b) -> a % 2 == 0 || b % 2 == 0;
        BiPredicate<Integer, Integer> isEqual = (a, b) -> a.equals(b);

        System.out.println(isEven.or(isEqual).test(4, 9)); // true (4 is even) 
    }
}
```
+ negate() method: Returns a predicate that is logical negation of this predicate.
```java
public class Main {
    public static void main(String[] args) {
        BiPredicate<String, String> isEqual = (s1, s2) -> s1.equals(s2);
        System.out.println(isEqual.negate().test("Java", "Java"));  // false
    }
}

```
***Note***: can be used without chaining on its own also.
+ and() method: Accepts a predicate that will be logically-ANDed with this predicate and returns a composed predicate.
```java
public class Main {
    public static void main(String[] args) {
        BiPredicate<Integer, Integer> isPositive = (a, b) -> a > 0 && b > 0;
        BiPredicate<Integer, Integer> isEqual = (a, b) -> a.equals(b);
       System.out.println(isPositive.and(isEqual).test(5, 5));   // true      
    }
}
```
## BiFunction
Represents a function that accepts two argument and returns a result.
```java
@FunctionalInterface
public interface BiFunction<T, U, R> {
	R apply (T t, U u);
	default <V> BiFunction<T, U, V> andThen(Function<? super R, ? extends V> after);
}
```
+ apply() method takes two arguments and returns a value.
```java
public class Main {
    public static void main(String args[])
    {
        BiFunction<Integer, Integer, Integer> add 
        = (a, b) -> a + b;
        
        System.out.println("Sum = " + add.apply(2, 3));
    }
}
```
+ andThen() method: accepts a parameter ***after*** to be applied after current one, and returns a composed function.
```java
public class Main {
    public static void main(String args[])
    { 
        BiFunction<Integer, Integer, Integer> composite1 
        = (a, b) -> a + b;

        composite1 = composite1.andThen(a -> 2 * a);

        System.out.println("Composite1 = " + composite1.apply(2, 3));
    }
}
```
***Note***: andThen() is before accept() because apply() is a terminal function. andThen() returns a Function object to chain functions together. 
## UnaryOperator
+ Special case of Function functional interface which accepts an argument and returns a value, where **both input and result have same type**.
```java
@FunctionalInterface
public interface UnaryOperator<T, T> extends Function<T, R> {
	R apply (T t);
	default <V> Function<T, V> andThen(Function<? super R, ? extends V> after);
	default <V> Function<V, R> compose(Function<? super V, ? extends T> before);
	static UnaryOperator<T, T> identity();
}
```

## BinaryOperator
+ Special case of BiFunction functional interface which accepts two arguments and returns a value, where **both input and result have same type**.
```java
@FunctionalInterface
public interface BinaryOperator<T, T, T> extends BiFunction<T, U, R> {
	R apply (T t, U u);
	default <V> BiFunction<T, U, V> andThen(Function<? super R, ? extends V> after);
	static <T> BinaryOperator<T> minBy(Comparator<? super T> comparator)
	static <T> BinaryOperator<T> maxBy(Comparator<? super T> comparator)
}
```
+ minby() method: Accepts one parameter which is object of Comparator class and returns a BinaryOperator which returns the minimum of two objects passed into the comparator.
```java
public class Main {
	public static void main(String args[]) {
		BinaryOperator<Integer> op = BinaryOperator.minBy(
(a, b) -> (a > b) ? 1 : ((a == b) ? 0 : -1));

		System.out.println(op.apply(98, 11));
	}
}
```
+ maxBy() method: Accepts one parameter which is object of Comparator class and returns a BinaryOperator which returns the max of two objects passed into the comparator
```java
public class Main {
	public static void main(String args[]) {
		BinaryOperator<Integer> op = BinaryOperator.maxBy(
(a, b) -> (a > b) ? 1 : ((a == b) ? 0 : -1));

		System.out.println(op.apply(98, 11));
	}
}
```

# Method references
+ Shorthand way to refer to method without calling them explicitly.
+ Four kinds:
	+ Reference to static method
		+  `ClassName::methodName`  Ex: String:: valueOf
	+ Reference to instance method of particular object
		+ `objectName :: methodName` Ex: myString:: toUpperCase
	+ Reference to constructor
		+ `ClassName:: new` 
		+ Can call both no-arg and parameteized constructors. The type that gets called depends on functional interface used.
		+ Supplier -> No arg, Function -> One arg, BiFunction-> Two args
	+ Reference to instance method of arbitrary object of a particular type
		+ `ClassName:: methodName`  Ex: String :: startsWith
		```java
public class Main {
	public static void main(String[] args) {
		// reference to an arbitrary object of the String type
		BiPredicate<String, String> evaluation = String::startsWith;
		boolean result = evaluation.test("hello world", "he");
		System.out.println("Result #1: " + result);
	}
}
```
