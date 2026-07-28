#bash 
# What is Bash? 

Bash is a shell program.
>  A shell program is typically an **executable binary** that takes commands that you type and (once you hit return), translates those commands into (ultimately) system calls to the Operating System API.

# Globbing
It is a simple pattern matching method, used to match files/directories in the shell with commands like `ls` , `cp` and `mv`.
## Matching any string  \[ \* \]
```run-shell
ls *  
```

```run-shell
ls Assets/*.pdf
```
## Matching a single character \[ ? \]
```run-shell
# Lists contents of directory AW? and files matching AW? in pwd.
ls AW?
```
## Matches any character in given set
\[abd\] - matches any character from a,b or d
```run-shell
# Lists content of directories starting with B or C
ls [BC]*
```

\[ a - d \] - matches any character from a, b, c or d
```run-shell
# List contents of directories starting with I, J, K
ls [I-K]*
```
# Dotfiles
They are like normal files, except their name begins with a dot ( . )
```shell
touch .adotfile
mkdir .adotfolder
```

```run-shell
# Dotfiles don't show up
ls
```

Even when using [ * ](#Matching%20any%20string%20[%20*%20) wild-card, dotfiles do not show up.
```run-shell
# Dotfiles don't show up
ls *
```

>[!info] Dotfiles are hidden files and cannot be seen in standard `ls` output.

To see all dotfiles in current folder
```run-shell
echo .*
```

To see only hidden files and contents of hidden folders:
```run-shell
ls .*
```

To see all files in current folder including hidden files:
```run-shell
ls -a
```

# Quoting
## Single Quotes
Everything inside is treated like a literal.
```run-shell
# This will print the contents of '' to standard output
H=123
echo '$H'
```

## Double Quotes
Expands variable enclosed in double quotes
```run-shell
H=123
echo "$H"
```

>[!warning] Quotes and Globbing Wildcards
>When a wildcard \[  \], \*, ? is enclosed in single quotes or double quotes, it stops becoming a wildcard
>```shell
># Lists files matching * character
>ls '*'
>ls "*"
>```
>
>```run-shell
># Echoes the character *
>echo '*'
>echo "*"
>```

# Variables
## Defining variables
Variables are ==capitalized== by convention.
```run-shell
# Define a variable
MYSTRING=astring
# Print its value. (Note the use of $ to identify this is a variable)
echo $MYSTRING
```

>[!warning] 
>When defining a variable, there must be no space between variable name, equals sign and the value.
>```run-shell
># This gives an error
>A =234
>```
>
>```run-shell
>#This is correct
>A=234
>echo $A
>```
## Variables and Quoting
Refer [#Quoting](#Quoting) for basic variable behavior with quotes.
### **String Variables with more than one word**
```run-shell
# This throws an error
MYSENTENCE=A sentence 

# This is correct
MYSENTENCE="A sentence" 
echo $MYSENTENCE

MYSENTENCE='A sentence'
echo $MYSENTENCE
```

### **Embedding variables**
```run-shell
MYSTRING=astring
# Value of MYSTRING is embedded here
MYSENTENCE="A sentence with $MYSTRING in it" 
echo $MYSENTENCE 

# Single quotes treats contents as literal. This does not embed the value of MYSTRING
MYSENTENCE='A sentence with $MYSTRING in it' 
echo $MYSENTENCE
```

We can encapsulate variables in `${}` to remove ambiguity
```run-shell
MyFirstLetters=ABC
echo "The first 10 letters in the alphabet are: ${MyFirstLetters}DEFGHIJ"

# Without the ${} bash will consider MyFirstLettersDEFGHIJ to be the variable name
echo "The first 10 letters in the alphabet are: $MyFirstLettersDEFGHIJ"
```
### **Substitution**
Variables can be assigned with the value of a command output. This is referred to as _substitution_.

Substitution can be done by encapsulating the command with backticks or `$()`.
```run-shell
FILELIST=$(ls Ba*)
echo $FILELIST
```

# Arrays
An array is initialized by assign space-delimited values enclosed in `( )`
```run-shell
my_array=(apple banana "Fruit Basket" orange)

# If no index is given, prints value of first index
echo $my_array
# Printing value at given index
echo ${my_array[1]}
# Printing value at index which doesn't exist
echo ${my_array[4]}

#Setting value at a given index
my_array[4]=apricot
echo ${my_array[4]}
```

The total number of elements in an array is referenced by `${#arrayname[@]}`.
```run-shell
my_array=(apple banana "Fruit Basket" orange)
echo ${#my_array[@]}

#Printing last element
echo ${my_array[ ${#my_array[@]} - 1]}
```

# Functions
## Defining
```run-shell
function myfunc {
	echo "Hello world"
}

myfunc
```
## Arguments
First argument is denoted by `$1` , second by `$2` and so on.
Space separated arguments are passed after specifying function name.
```shell
# Here `echo` is the function name.
# `Hi` is the first argument and `Bye` is the second
echo Hi Bye
```

```run-shell
function myfunc {
	echo $1
	echo $2
}

echo "Running myfunc \"Hello World\" "
# `"Hello World"` is one argument since it is wrapped in quotes.
myfunc "Hello World"

echo "Running myfunc Hello World..."
# Hello and World are two different arguments
myfunc Hello World
```
## Variable scope
Bash functions have no special scope. Variables outside are visible to it.
```run-shell
function myfunc {
	echo $myvar
}

# Variable myvar doesn't exist
myfunc

# Setting value of myvar
myvar="hi from outside the function"
myfunc
```

We can declare a variable to be local using `local` keyword:
```run-shell
function myfunc {
	local myvar="hi from inside the function"
	echo $myvar
}
# Prints value of local variable myvar
myfunc

# Prints nothing since local variable myvar is not accessible outside the function
echo $myvar

# Cannot use local to define value outside a function
local myvar="Will this work?"
myfunc
```

## Builtin functions
Builtins are commands that come ‘built in’ to the bash shell program.
Ex: `cd`, `ls` etc...
Because they are "built-in," the shell executes them directly without needing to start an external process.

>[!faq] What happens if we define a function with the same name as a builtin function?
> The custom function hides the builtin function. We can use the `builtin` builtin function to call the builtin version of the function.
> > If it is a builtin, it will execute; if not, the shell will throw an error.

```run-shell
function cd() { 
	echo 'No!' 
} 

echo "Own function: $(cd /tmp)"
echo "Builtin function: $(builtin cd /tmp)" 
echo "cd-: $(cd -)" 

unset -f cd
echo "After unsetting function..."
echo "cd tmp: $(cd /tmp)"
echo "cd -: $(cd -)"
```

## Programs
These are separate executable files stored on your computer's filesystem. Commonly-used examples of these are programs such as `grep`, `sed`, `vi`, and so on.

When you run a program, the shell must locate the file (usually via the `PATH` variable) and start a new process to run it.
>[!faq] How to find where a program is stored?
>Run the `which` command to determine where the file is on your filesystem.

```run-shell
# returns file path since `grep` is a program
which grep
# returns nothing since builtin is not a program
which builtin
# returns nothing since doesnotexist doesn't exist
which doesnotexist
```

>[!faq] How do you tell whether a command is a builtin or a separate binary?
>1. Check whether it’s a builtin by running `builtin <command>` 
>2. Use the builtin `type <command>` to understand how shell interprets the command.

```run-shell
function myfunc {
	echo "Hello world"
}
# Returns path sicne `grep` is a program
type grep
# Tells you `cd` is a builtin
type cd
# Tells you `myfunc` is a function
type myfunc
```

# Alias
Aliases are strings that the shell takes and translates to whatever that string is aliased to.
```run-shell
# Set alias
alias ll=$(ls -a)
# Run alias cmd to use alias value
alias
# Running `ll` is same as running `ls -a` 
ll
#Unset alias to stop using it
unalias ll
# Throws error since no such command exists
ll
```
# Redirects
| Operator | Name            | Description                                                                                | Example                         |
| -------- | --------------- | ------------------------------------------------------------------------------------------ | ------------------------------- |
| **`>`**  | Redirect stdout | Redirects standard output to a file, overwriting the file's contents if it already exists. | `echo "hello" > file.txt`       |
| **`>>`** | Append stdout   | Appends standard output to a file; creates the file if it doesn't exist.                   | `echo "world" >> file.txt`      |
| **`<`**  | Redirect stdin  | Redirects standard input for a command from a file.                                        | `wc -l < file.txt`              |
| **`2>`** | Redirect stderr | Redirects standard error to a file.                                                        | `ls non_existent 2> errors.txt` |
| **`&>`** | Redirect all    | Redirects both stdout and stderr to a file (Bash shorthand).                               | `ls &> all_output.txt`          |
**Discarding output:** To run a command and hide all its output (both stdout and stderr), redirect them to `/dev/null`, which is a special "null" device that discards data written to it.
```shell
command > /dev/null &>
```

**Separating output and error:** You can send standard output and standard error to different files.
```shell
command > output.txt 2> errors.txt
```
# Pipes
A pipe passes _stdout_ of _command1_ as _stdin_ to _command2_.
```shell
command1 | command2
```

By default pipelines redirects only the standard output, if you want to include the standard error you need to use the form `|&` which is a short hand for `2>&1 |`.
```shell
command1 |& command2
```

Imagine you want to see only the first 3 results
```run-shell
ls | head -n 3
```

Or you want to only see files with _Network_ in its name:
```run-shell
ls | grep *Network*
```

Use the `tee` command to redirect output to a file while also printing it to the screen.
```shell
# overwrites text in `logfile.txt`
command &> | tee logfile.txt
#appends text to `logfile.txt`
command &> | tee logfile.txt -a
```
# References
[Learn Bash the hard way](https://www.softouch.on.ca/kb/data/Learn%20Bash%20the%20Hard%20Way.pdf)
[Learn Shell](https://www.learnshell.org/)

