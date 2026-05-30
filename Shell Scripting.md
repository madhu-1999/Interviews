#bash #scripting
# Prerequisite
[[Core Bash]]
# A Simple Script
```shell title=hello.sh
#!/bin/bash
# Text to the right of a '#' is treated as a comment
echo "Hello World"
```
```run-shell
./Assets/hello.sh
```

The first line of the shell script file begins with a "sha-bang" `#!` which is not read as a comment, followed by the full path where the shell interpreter is located. 
This path, tells the operating system that this file is a set of commands to be fed into the interpreter indicated. Here, the script gives the path to the bash shell `/bin/bash` .
	Note that if the path given at the `#!` is incorrect, then an error message e.g. "Command not found.", may be the result of the script execution.
# Passing Arguments
Arguments can be passed to the script when it is executed, by writing them as a space-delimited list following the script file name.
`$1` references first argument, `$2` references second argument etc...
==`$0` references script name==.

```shell title=my_shopping.sh
#!/bin/bash
echo "File name is "$0 # holds the current script
echo $3 # $3 holds banana
Data=$5
echo "A $Data costs just $6."
echo $# # Prints number of arguments passed
```
```run-shell
./Assets/my_shopping.sh apple 5 banana 8 "Fruit Basket" 15
```
## Special variables
- `$0` - The filename of the current script.
- `$n` - The Nth argument passed to script was invoked or function was called.
- `$#` - The number of argument passed to script or function.
- `$@` - All arguments passed to script or function.
- `$*` - All arguments passed to script or function.
- ==`$?` - The exit status of the last command executed.==
- `$$` - The process ID of the current shell. For shell scripts, this is the process ID under which they are executing.
- `$!` - The process number of the last background command.
# Arithmetic in Scripts
When you want to retrieve the output of a arithmetic expression, wrap it in` $(())`
```run-shell
A=3
B=$((100 * $A + 5)) # 305
echo $B
```

When you simply want to perform an arithmetic operation wrap it in `(())`
```shell
A=4
((A + 1))
echo $A # 5
```
Supported operators are +, -, \*, /, %, ** (exponentiation)
# Basic String Operations
## String Length
To find string length
```run-shell
STRING="this is a a string"
echo ${#STRING}
```

## Substring extraction
Extract substring of length `$LEN` from `$STRING` starting after position `$POS`. Note that first position is 0.
```run-shell
STRING="this is a string"
POS=1
LEN=3
echo ${STRING:$POS:$LEN}   # his
# Omitting $LEN -> till end of string
echo ${STRING:$POS}
```

## Index of first occurrence of substring
Find the index in $STRING of the first $SUBSTRING that matches.
```run-shell
STRING="this is a string"
SUBSTRING="is"

# Prefix contains the part of STRING that precedes first occurrence of SUBSTRING
prefix=${STRING%%"$SUBSTRING"*}
# Length of prefix = index of first occurrence of substring
echo ${#prefix}
```

## Substring replacement
Replace first occurrence of substring with replacement
```run-shell
STRING="to be or not to be"
# Replace be with eat
echo ${STRING[@]/be/eat}        # to eat or not to be
```

Replace all occurrences of substring
```run-shell
STRING="to be or not to be"
echo ${STRING[@]//be/eat}        # to eat or not to eat
```

Delete all occurrences of substring (replace with empty string)
```run-shell
STRING="to be or not to be"
echo ${STRING[@]//not/}        # to be or to be
```

Replace occurrence of substring if at the beginning of $STRING
```run-shell
STRING="to be or not to be"
echo ${STRING[@]/#to be/eat now}    # eat now or not to be
```

Replace occurrence of substring if at the end of $STRING
```run-shell
STRING="to be or not to be"
echo ${STRING[@]/%be/eat}        # to be or not to eat
```

Replace occurrence of substring with shell command output
```run-shell
STRING="to be or not to be"
echo ${STRING[@]/%be/be on $(date +%Y-%m-%d)}    # to be or not to be on 2012-06-14
```
# If statements
```shell
NAME="John"
if [[ "$NAME" = "John" ]] then
  echo "True - my name is indeed John"
fi
```

If-else
```shell
NAME="Bill"
if [[ "$NAME" = "John" ]] then
  echo "True - my name is indeed John"
else
  echo "False"
  echo "You must have mistaken me for $NAME"
fi
```

If-else if
```bash
NAME="George"
if [[ "$NAME" = "John" ]] then
  echo "John Lennon"
elif [["$NAME" = "George" ]] then
  echo "George Harrison"
else
  echo "This leaves us with Paul and Ringo"
fi
```
## Numeric comparisons
```shell
comparison    Evaluated to true when
$a -lt $b    $a < $b
$a -gt $b    $a > $b
$a -le $b    $a <= $b
$a -ge $b    $a >= $b
$a -eq $b    $a is equal to $b
$a -ne $b    $a is not equal to $b
```
## String comparisons
```shell
comparison    Evaluated to true when
"$a" = "$b"     $a is the same as $b
"$a" == "$b"    $a is the same as $b
"$a" != "$b"    $a is different from $b
-z "$a"         $a is empty
-n "$a"         $a is not empty
```
## Logical Operators
### **AND (&&) operator**
```shell
if [[ $VAR_A[0] -eq 1 && ($VAR_B = "bee" || $VAR_T = "tee") ]] then
    command...
fi
```
### **OR ( || ) operator
```shell
# Check if the name is either "John" OR "George"
NAME="George"

if [[ "$NAME" == "John" || "$NAME" == "George" ]] then
    echo "The name is a member of the Beatles."
fi
```
## CASE (Switch equivalent)
The double semicolons (;;) indicate next case block is coming.
The 1) indicates the case value (`$mycase`) needs to match.
```bash
mycase=1
case $mycase in
    1) echo "You selected bash";;
    2) echo "You selected perl";;
    3) echo "You selected phyton";;
    4) echo "You selected c++";;
    5) exit
esac
```

## File and Directory Tests

| Operator | Function                                                             | Example          |
| -------- | -------------------------------------------------------------------- | ---------------- |
| `-e`     | Returns true if the file or directory **exists**.                    | `if [[ -e $a ]]` |
| `-f`     | Returns true if the file exists and is a **regular file**            | `if [[ -f $a ]]` |
| `-d`     | Returns true if the file exists and is a **directory**.              | `if [[ -d $a ]]` |
| `-s`     | Returns true if the file exists and has a **size greater than zero** | `if [[ -s $a ]]` |
|          |                                                                      |                  |
## Permission Tests
| Operator | Function                                   | Example          |
| -------- | ------------------------------------------ | ---------------- |
| `-r`     | Returns true if the file is **readable**.  | `if [[ -r $a ]]` |
| `-x`     | Returns true if the file is **executable** | `if [[ -x $a ]]` |
| `-w`     | Returns true if the file is **writable**   | `if [[ -w $a ]]` |
# For loop
```shell
for (( i=0; i < 20; i++ ))
do
	echo $i
	# Redirect output to a file
	echo $i > file${i}.txt
done
```

Looping through values returned by a command output
```shell
for f in $(ls *txt)
do
	echo "File $f contains $(cat $f)"
done
```
# While loop
```shell
n=0
while [[ ! -e newfile ]] # while `newfile` does not exist
do
	((n++)) # Arithmetic expr
	echo "Iteration $n"
	# If content of file = 15
	if [[ $(cat file${n}.txt == "15")]]
	then
		touch newfile # create `newfile`
	fi
done
```
# Until loop
The until construct tests for a condition, and if false, executes commands. It keeps looping as long as the condition is false (opposite of while construct).
```shell
until [[ $COUNT -gt 5]]
do 
echo "Value of COUNT: $COUNT"
COUNT=$((COUNT+1))
done
```
# Break and continue
```shell
# Prints 0 1 2 3 4
COUNT=0
while [[ $COUNT -ge 0]]
do
	echo $COUNT
	COUNT=$((COUNT + 1))
	if [[ $COUNT -ge 5]] then
		break
	fi
done

# Prints 1 3 5 7 9
COUNT=0
while [[COUNT -ge 0]]
do
	COUNT=$((COUNT + 1))
	if [[ $(($COUNT % 2)) == 0 ]] then
		continue
	fi
	echo $COUNT
done
```
# Process Substitution
```shell
# Redirect stdout to file `aout`
ls a > aout
# Redirect stdout to file `bout`
ls b > bout
# Print diff b/w files `aout` and `bout`
diff aout bout
```

That works, and there’s nothing wrong with it, but typing all that out and cleaning up the files is a bit cumbersome. There’s a much neater way to do this:
```shell
diff <(ls a) <(ls b)
```
The `<()` operator is similar to the `$()` [[Core Bash#**Substitution**| command substitution operator]] .
The `<()` substitutes the _file containing the output of the command_.