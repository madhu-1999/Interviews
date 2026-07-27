#coa
# Integers
They are represented by a fixed size of bits. For example, `byte` is typically 8 bits,
`short` is 16 bits, `int` is 32 bits and `long` is 64 bits.
## Unsigned Integers (+ve values only)
>[!important] Range: $0$ to $2^n-1$ for a _n_ bit representation

Value in base 10 is represented on disk as its equivalent value in base 2. 
Ex: $(32)_{10} = (100000)_2$
## Signed Integers (-ve and +ve values)
The industry standard is to represent signed integers using the [2's complement](https://builtin.com/articles/twos-complement)
 method.
>[!important] Range: $-2^{n-1}$ to $2^{n-1}-1$

The MSB represents the sign of the integer ($1 \implies \text{-ve, } 0 \implies \text{+ve}$ ).
### Method One
To take the two’s complement of a number, simply complement (or invert) each bit and then add one.
>[!example]
>With 4-bit representation, the number 6 in binary is `0110`. To take the two’s complement, complement each bit, which becomes `1001`, and add 1: `1010` (represents -6).
### Method Two
Another way to take the two’s complement: starting from the right, keep the first `1` and all bits to its right unchanged; invert all bits to the left.

>[!example] Example
The number 10 in binary is `01010`. The bit on the right-hand side is `0`, so we leave that. The next bit to the left is `1`, so each bit after that going toward the left we complement.
So, we end with `10110`.
# Floating point numbers
Floating point numbers (`float` (32 bits) and `double` (64 bits)) are represented using the [IEEE 754 Standard](https://www.youtube.com/watch?v=TaDrBnRS0_Q).
# Strings
They are first serialized into bytes using an encoding scheme such as UTF-8.
In UTF-8 standard English characters take up 1 byte , while emojis and other international characters can take up to 4 bytes
## Fixed Length Strings
If a string is represented using a fixed number of characters (`char[]` or `CHAR` in SQL), then, if the given string requires less bytes, we pad it with zeros to get teh fixed size representation.
## Variable Length Strings
( Think `string` or `VARCHAR` ).
### Null Terminated String
String end is symbolized by the null `\0` terminating character.
>[!example]
>String "hello" would be represented as \x68\x65\x6C\x6C\x6F\0, where **\x68\x65\x6C\x6C\x6F** is the UTF-8 encoded string and \0 implies end of the string.

>[!success]- Advantages
>- **Unlimited Length**: Length is bounded by available memory only.
>- __Less memory overhead__: Requires exactly 1 byte to represent null terminating character `\0`.

>[!failure]- Disadvantages
>- __Length Discovery__: $O(n)$, since we need to traverse entire string to reach null terminating character.
>- **Binary Safety**: Unsafe, cannot represent nulls between other text.
>- **Overflow risk**: Since length is unknown, buffer overflow can occur if `\0` is missing or string length >> buffer size.
### Pascal String (Recommended)
Prepend text with its length in bytes. 
>[!example] 
>String "hello" would be represented as 5\x68\x65\x6C\x6C\x6F, where 5 is the length of bytes (1 byte per character) and **\x68\x65\x6C\x6C\x6F** is the UTF-8 encoded string.

>[!success]- Advantages
>- **Length Discovery**: $O(1)$ since length of string is stored in prefix.
>- **Binary Safety**: Safe, can contain any data including nulls.
>- **Low overflow risk**: Buffer sizes known ahead of time (length prefix).

>[!missing]- Disadvantages
>- __Length Limitation__: Limited by the amount of bytes allotted for length prefix. (1 byte $\implies$ max length of 255 characters).
>- **Memory overhead**: Length prefix adds 1-8 bytes of overhead to a string.

# Boolean
Technically, a boolean can be represented using 1 bit but most programming languages use 8 bits (1 byte) to represent a boolean. 
This is because, computer memory is byte addressable. The CPU likes to fetch data in chunks — bytes, words (4 bytes), or double words (8 bytes). 
To read a single bit, we would have to fetch the byte and use bit masking to filter out the required bit. This is faster to do if the boolean just claims the whole byte for itself.[^1].

Now, a system might have multiple boolean (properties on a object / boolean value columns in a table), say for example a user might have `isBetaTester`, `hasDarkTheme`, `emailVerified`, `notificationsEnabled`, `marketingOptIn`, etc.
If you have millions of users or thousands of entities, there would be a lot of storage space wasted on representing boolean values separately (1 bool = 1 byte). 
To save space, we can use [[Parquet#Bit Packing|bit packing]] to group multiple boolean values together. (1 bool = 1 bit).[^2]
# Enum
To efficiently represent, ==a combination of enum values==, we use _flag enums_.
Each enum constant, is represented by a bit (power of 2), so a combination of enum values is represented by bit packing boolean values ($1 \implies \text{enum value exists }, 0 \implies \text{enum value doesn't exist}$).

```run-python
from enum import IntFlag, auto

# auto() auto-assigns consecutive power of 2 values
class UserRoles(IntFlag):
    NONE = 0
    ADMIN = auto() # 1
    EDITOR = auto() # 2
    VIEWER = auto() # 4

# Add roles
user_role = UserRoles.ADMIN | UserRoles.EDITOR
print(user_role)  # Output: 3(1 + 2)

# Check if a flag is set
is_admin = user_role & UserRoles.ADMIN != 0
print(f"Is Admin: {is_admin}")  # Output: True

# Remove a role
user_role &= ~ UserRoles.ADMIN
print(user_role)  # Output: 2
```

# Footnotes
[^1]: [A Boolean is a bit so why does it take a byte?](https://medium.com/@srsoumyax11/a-boolean-is-a-bit-so-why-does-it-take-a-byte-d0e6c4a808eb)

[^2]: [Bit packing booleans](https://twin.sh/articles/54/bit-fields-squishing-booleans-using-bitwise-operations)
