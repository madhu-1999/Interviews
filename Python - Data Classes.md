---
tags:
  - "#python"
---
# Prerequisite
[[Python - Classes and Objects]]
# Basics 
It is a built-in [[Decorator Pattern|decorator]] that automatically generates boilerplate code for `.__init__()` , `.__repr__()` and `.__eq__()` for classes that primarily store data.
```run-python
from dataclasses import dataclass

@dataclass
class Exercise:
   name: str
   reps: int
   sets: int
   weight: float

ex1 = Exercise("Bench press", 10, 3, 52.5)
# O/P: Exercise(name='Bench press', reps=10, sets=3, weight=52.5)
print(repr(ex1))

ex2 = Exercise("Bench press", 10, 3, 52.5)
# `.__eq__()` implemented for equality of objects. (All parameters must have the same value)
print(ex1 == ex2) # O/P: True
```

Since it is a regular class with some predefined functionality, we can add [[Python - Functions|functions]] to it.
>[!warning] Type hints are ___required___ when defining a dataclass, but the types are ___NOT___ enforced!
## Adding default values
```run-python
from dataclasses import dataclass, field

@dataclass
class Exercise:
   name: str = field(default="Push-up")
   reps: int = field(default=10)
   sets: int = field(default=3)
   weight: float = field(default=0)

ex5 = Exercise()
print(ex5)
# O/P: Exercise(name='Push-up', reps=10, sets=3, weight=0)
```

>[!warning] Non-default fields cannot follow default fields!
>Below code will throw a `TypeError`:
>
>```run-python
>@dataclass
class Exercise:
   name: str = "Push-ups"
   reps: int = 10
   sets: int = 3
   weight: float  # NOT ALLOWED
ex5 = Exercise()
print(ex5)
>```

## Adding _mutable_ default values
Dataclasses ___DO NOT___ allow mutable default values. Below code will throw a `ValueError`:
```run-python
from dataclasses import dataclass, field
from typing import List

@dataclass
class Exercise:
   name: str = field(default="Push-up")
   reps: int = field(default=10)
   sets: int = field(default=3)
   weight: float = field(default=0)

@dataclass
class WorkoutSession:
   exercises: List[Exercise] = field(default=[])
   duration_minutes: int
   
hiit_monday = WorkoutSession("25-02-2024")
```

>[!faq] Why are mutable default values not supported?
>This is because, all instances of the class will end up sharing the same mutable instance (here, list). A new list is ___NOT___ created for every object instance.

Instead, we can use the `default_factory` parameter of the `field` object to specify a custom / mutable default type.
```run-python
from dataclasses import dataclass, field
from typing import List

@dataclass
class Exercise:
   name: str = field(default="Push-up")
   reps: int = field(default=10)
   sets: int = field(default=3)
   weight: float = field(default=0)

@dataclass
class WorkoutSession:
   exercises: List[Exercise] = field(default_factory=list)
   duration_minutes: int = 5

hiit_monday = WorkoutSession()
print(hiit_monday)
```

We can also specify a custom / lambda function as the parameter.
```run-python
from dataclasses import dataclass, field
from typing import List


@dataclass
class Exercise:
   name: str = field(default="Push-up")
   reps: int = field(default=10)
   sets: int = field(default=3)
   weight: float = field(default=0)

def create_warmup():
   return [
       Exercise("Jumping jacks", 30, 1),
       Exercise("Squat lunges", 10, 2),
       Exercise("High jumps", 20, 1),
   ]

@dataclass
class WorkoutSession:
   exercises: List[Exercise] = field(default_factory=create_warmup)
   duration_minutes: int = 5

hiit_monday = WorkoutSession()
print(hiit_monday)
```
# Comparing instances
Equality comparison is built in through the default `.__eq__()` implementation. But what about other comparisons like <mark>>, >=, <=, <</mark> ?
We set the parameter `order=True` for the `@dataclass` decorator:
```run-python
from dataclasses import dataclass

@dataclass(order=True)
class PlayingCard:
    rank: str
    suit: str

    def __str__(self):
        return f'{self.suit}{self.rank}'
        
queen_of_hearts = PlayingCard('Q', '♡')
ace_of_spades = PlayingCard('A', '♠')

# O/P: False
print(ace_of_spades > queen_of_hearts)

```

>[!faq] On what basis, does `@dataclass` perform comparisons?
Dataclasses compares objects as [[Python - Tuples#Comparing tuples|tuples]] of all its fields in the order that they are specified in the class: `('A', '♠') > ('Q', '♡')` . 
>Here, `A < Q` so, it returns False.
>
## Customizing comparisons
### Excluding fields from comparison
We can set the parameter `compare=False` for a field that we want to exclude.
```run-python
from dataclasses import dataclass, field

@dataclass(order=True)
class User:
    user_id: int
    name: str = field(compare=False)  # Ignored in comparisons
    age: int

# Compared strictly by user_id, then age
u1 = User(user_id=1, name="Alice", age=25)
u2 = User(user_id=1, name="Bob", age=30)

print(u1 > u2)  # O/P: False 
```
### Custom sort key
If you want sorting behaviors based on a derived attribute or a field that isn't defined first in the class structure, use a dedicated sorting field paired with `.__post_init__()` 

```run-python
from dataclasses import dataclass, field

RANKS = '2 3 4 5 6 7 8 9 10 J Q K A'.split()
SUITS = '♣ ♢ ♡ ♠'.split()

@dataclass(order=True)
class PlayingCard:
    sort_index: int = field(init=False, repr=False)
    rank: str
    suit: str

    def __post_init__(self):
        self.sort_index = (RANKS.index(self.rank) * len(SUITS)
                           + SUITS.index(self.suit))

    def __str__(self):
        return f'{self.suit}{self.rank}'
        
queen_of_hearts = PlayingCard('Q', '♡')
ace_of_spades = PlayingCard('A', '♠')

# O/P: True
print(ace_of_spades > queen_of_hearts)
```

Here, the `sort_index` attribute, represents our custom sort key. It is placed first so that, when tuple based ordering happens, we compare based on its value first.
We set `field(init=False, repr=False)` so that its value can be calculated after the values of `rank` and `suit` are set.
To set the value of `sort_index`, we use the `.__post_init__()` [[Python - Magic Methods|dunder method]] , which executes after `.__init__()` method.
### Override dunder methods
If you don't want to use tuple based comparison at all, you can override the required dunder methods manually to provide custom comparison behavior.
>[!warning] `@dataclass` decorator parameter must be set to `order=False`, if overriding methods, else a `TypeError` will be raised.

```run-python
from dataclasses import dataclass
from functools import total_ordering

@total_ordering # Binds all comparison dunder methods with similar logic
@dataclass
class Product:
    sku: str
    price: float

    def __eq__(self, other):
        if not isinstance(other, Product):
            return NotImplemented
        return self.sku == other.sku

    def __lt__(self, other):
        if not isinstance(other, Product):
            return NotImplemented
        return self.price < other.price

p1 = Product(sku='SKU101', price=29.99)
p2 = Product(sku='SKU001', price=49.99)

print(p1 == p2) # O/P: False
# Works, even though .__ge__() not implemented
print(p1 >= p2) # O/P: False
```
# Immutable data class
To make a data class immutable, set the parameter `frozen=True` for the `@dataclass` decorator:
```python
from dataclasses import dataclass
from typing import List

@dataclass(frozen=True)
class ImmutableCard:
    rank: str
    suit: str
```

>[!warning] The _contents_ of mutable attributes can change, but, the object instance of the mutable attribute cannot change!

```run-python
from dataclasses import dataclass
from typing import List

@dataclass(frozen=True)
class ImmutableCard:
    rank: str
    suit: str

@dataclass(frozen=True)
class ImmutableDeck:
    cards: List[ImmutableCard]
    
queen_of_hearts = ImmutableCard('Q', '♡')
ace_of_spades = ImmutableCard('A', '♠')
deck = ImmutableDeck([queen_of_hearts, ace_of_spades])
print(deck)
# O/P: ImmutableDeck(cards=[ImmutableCard(rank='Q', suit='♡'), ImmutableCard(rank='A', suit='♠')])

# We can change the contents of the deck
deck.cards[0] = ImmutableCard('7', '♢')
print(deck)
# O/P: ImmutableDeck(cards=[ImmutableCard(rank='7', suit='♢'), ImmutableCard(rank='A', suit='♠')])

# We cannot change the deck itself
# This will throw a dataclasses.FrozenInstanceError
deck.cards = [queen_of_hearts, ace_of_spades]
```
# [[Python - Inheritance|Inheritance]]
We can subclass data classes, in the same way as regular classes:
```python
from dataclasses import dataclass

@dataclass
class Position:
    name: str
    lon: float
    lat: float

@dataclass
class Capital(Position):
    country: str
```

>[!warning] If any fields in the base class have default values, then _all_ fields in the subclass must have default values!

```run-python
from dataclasses import dataclass

@dataclass
class Position:
    name: str
    lon: float = 0.0
    lat: float = 0.0

@dataclass
class Capital(Position):
    country: str  # Does NOT work
```

This is because, Python will try to write an `.__init__()` method with the following signature, which is not valid Python:
```python
def __init__(name: str, lon: float = 0.0, lat: float = 0.0, country: str):
    ...
```
# When to use
- [u]  **For DAOs:** Use `@dataclass` when passing objects around within your application or testing suite.
- [u] **To eliminate boilerplate code:** It automatically generates [dunder methods] like `__init__()`, `__repr__()` (for clean printing), and `__eq__()` (for comparing objects by value)
- [u] **When you need immutability:** You can pass `frozen=True` to the decorator to create a read-only object that can also be used as a dictionary key or added to a set. 
# When NOT to use
+ [d] **Heavy business logic:** If your class relies heavily on complex methods, internal encapsulation, or dynamically modified behaviors, use a standard Python class.
+ [d] **Complex initialization requirements:** If your class constructor needs to establish database connections or run heavy external operations, a traditional `__init__` method is cleaner. 
+ [d] **Strict runtime data validation:** Data classes accept type hints but **do not enforce them at runtime**. If you are parsing untrusted data (like raw JSON from a web request), use a library like Pydantic or [attrs](https://www.attrs.org/) instead.
# References
[Python Data classes - Datacamp](https://www.datacamp.com/tutorial/python-data-classes)
[Python Data classes - Real Python](https://realpython.com/python-data-classes/)