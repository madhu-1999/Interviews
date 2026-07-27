#python 

A module can define functions, classes and variables.
	A module is only __loaded once per interpreter session__
````tabs
tab: mod.py
```python 
s = "If Comrade Napoleon says it, it must be right."
a = [100, 200, 300]

def foo(arg):
    print(f'arg = {arg}')

class Foo:
    pass
```

tab: main.py
```python
import mod

print(mod.s)
print(mod.a)
print(mod.foo(['quux', 'corge', 'grault']))
print(mod.Foo())
# O/P:
# If Comrade Napoleon says it, it must be right.
# [100, 200, 300]
# arg = ['quux', 'corge', 'grault']
# <mod.Foo object at 0x03C181F0>
```
````
# Module Search Path
When we write `import mod`, the interpreter searches for mod.py as follows:
+ The directory from which the input script was run or the **current directory** if the interpreter is being run interactively
- The list of directories contained in the [`PYTHONPATH`](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONPATH) environment variable, if it is set.

>[!tip] The resulting search path is accessible in the Python variable `sys.path`.
# Running as a script vs Importing as a module
Any `.py` file that contains a **module** is essentially also a Python **script**,
```python title:mod.py
s = "If Comrade Napoleon says it, it must be right."
a = [100, 200, 300]

def foo(arg):
    print(f'arg = {arg}')

class Foo:
    pass

print(s)
print(a)
foo('quux')
x = Foo()
print(x)
```
However, importing `mod` also generates output
```python
>>> import mod
If Comrade Napoleon says it, it must be right.
[100, 200, 300]
arg = quux
<mod.Foo object at 0x0169AD50>
```

When a `.py` file is imported as a module, Python sets the special **dunder** variable [`__name__`](https://realpython.com/python-main-function/) to the name of the module. However, if a file is run as a standalone script, `__name__` is (creatively) set to the string `'__main__'`.
```python
s = "If Comrade Napoleon says it, it must be right."
a = [100, 200, 300]

def foo(arg):
    print(f'arg = {arg}')

class Foo:
    pass

if (__name__ == '__main__'):
    print('Executing as standalone script')
    print(s)
    print(a)
    foo('quux')
    x = Foo()
    print(x)
```
# References:
[Python Modules](https://realpython.com/python-modules-packages/)