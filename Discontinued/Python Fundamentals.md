# Introduction

###### Random Notes

There is no way to clear python's idle shell without some external module!

I set up environment path variables to allow python to be used in the command line

Use Ctrl + Z and enter to escape the python shell

## Python Overview, Part 1

Python is a strongly types language in the sense that every object in the language has a definite type, and there's generally no way to circumvent that type. At the same time, Python is dynamically typed, meaning that there's no type checking of your code prior to running it. This is in contrast to statically typed languages like C++ or Java where a compiler does a lot of type checking for you, rejecting programs which misuse objects.

Python is an interpreted language. This is a bit of a myth statement technically because Python is normally compiled into a form of byte code before it's executed. However, this compilation happens invisibly, and the experience of using Python is one of immediately executing code without a noticeabl compilation phase. This lack of interruption between editing and running is one of the great joys of working with Python.

# Getting Started With Python 3

## Installing Python 3 on Windows

Install both Python2 and Python3 (make sure to check the option `Add python.exe to Path`). In the terminal, typing `python` still defaults to python 2. Fortunately, in `cmder` you can specify which python version you want to run with an alias.

```
alias python2="/c/Python27/python.exe"
alias python3="/c/Python34/python.exe"
```

For some reason this only works when placed in a `.bashrc` file and need to run `source .bashrc` beforehand every time you open `cmder`. Need to find a better solution. For the time being, just navigate to `C:\Python34` and run `python` from there.

## The Read-Eval-Print-Loop or REPL

The python command line environment is a Read-Eval-Print-Loop. Python will read whatever input we type in, evaluate it, print the result, and then loop back to the beginning.

You can use the special underscore variable `_` to refer to the most recently printed value, and can even use the underscore in an expression.

Example: `_ * 2`

Note that the underscore doesn't have any special behavior in Python scripts or programs.

## Significant Whitespace in Python

If you type `for i in range(5):` + `Enter` into the Python interpreter, the cursor will be brought to a new line with `...`. Need to add 4 spaces or hit tab to enter the next line or you will get an `IndentationError`.

To terminate a block, need to enter a blank line into the REPL.

###### Significant Whitespace Rules
- Prefer four spaces
- Never mix spaces and tabs
- Be consistent on consecutive lines
- Only deviate to improve readability

## Python Culture and the Zen of Python

Can access the zen of python by entering `import this` into the REPL.

## Importing From the Python Standard Library

Python comes with an extensive standard library, an aspect of Python often referred to as "batteries included". The standard library is structured as modules, which you gain access to using the `import` keyword.

```
import math
math.sqrt(81) # 9.0
help(math) # press q to esacpe
help(math.factorial)
math.factorial(3) # 6
from math import factorial
factorial(5) # 120
from math import factorial as fac # Use the "as" keyword sparingly
n = 5
k = 3
fac(n) / (fac(k) * fac(n-k)) # 10.0
fac(100) # some giant number
len(str(fac(100))) # 158, the number of digits in 100 factorial
```

`/` is the floating point division operator
`//` is the integer division operator

```
fac(n) // (fac(k) * fac(n-k)) # 10
```

## Scalar Types: `int`, `float`, `None`, and `bool`

```
0x12 # returns 18
int(3.5) # returns 3
int(-3.5) # returns -3
int("496") # returns 496
int("10000", 3) # returns 81, or 10000 in base 3
```

Python has a special null value called `None`. `None` is frequently used to represent the absence of a value.

```
A = None
a is None # True
```

bools are True and False (capitalized)

```
bool(0) # False
bool(42) # True
bool(-1) # True
bool(0.0) # False
bool(0.2) # True
bool([]) # False
bool([1, 2, 3]) # True
bool("") # False
bool("Spam") # True
bool("False") # True
```

## Relational Operators

`if` statements don't have to have brackets around their expressions!

```
if True:
	print("It's true!")
if bool("eggs"):
	print("Good job")
if "eggs":
	print("Awesome job")
```

`elif` used for else if

## `while` Loops

```
response = input() # input() is a built-in python function
```

# Strings and Collections

## Strings

- Strings in Python are immutable
- You can use single quotes or double quotes for strings so long as you are consistent (this has the advantage of not having to escape single quotes in a double quoted string)

## Strings (Continued)

Can use three single or double quotes for multi-line comments

Sometimes, particularly when dealing with strings such as Windows file system paths or regular expressions, the requirement to double up on backslashes can be ugly and error prone. Python comes to the rescue with its raw strings.

```
path = r'C:\Users\person\Documents'
print(path) # r'C:\\Users\\person\\Documents
```

Raw strings don't support escape sequences and are very much "what you see is what you get".

***

Can access individual characters in a string using array notation

```
s = 'parrot'
s[4] # 'o'
```

Can use the `help` function on strings: `help(str)`, `help(str.capitalize)`, etc

## Lists

Unlike strings, lists are mutable in so far as the elements within them can be replaced or removed, and new elements can be inserted or appended. Lists are the workhorse of Python data structures.

```
[1, 9, 8]
a = ["apple", "orange", "pear"]
a[1] # 'orange'
a[1] = 7
a # ['apple', 7, 'pear']

b = []
b.append(1.618)
b # [1.618]

list("words") # ['w', 'o', 'r', 'd', 's']
c = ['bear',
	'giraffe',
    'elephant',
    'caterpillar',]

```

Notice that you're allowed to put an ending comma in a list

## Dictionaries

Dictionaries are completely fundamental to the way the Python language works and are very widely used.

Dictionaries are very similar to object literals in javascript, except the key can be types other than string, and if it a string, you still have to wrap them in quotes.

```
d = {3 : "bill", 4: "bob"}
d = {"name" : "bill", "occupation" : "programmer"}
```

Note that `d.name` doesn't work, have to use array notation `d['name']` in order to retrieve the key's value.

Be aware that the entries in a dictionary can't be relied upon to be stored in any particular order.

Can create dictionaries using empty curly braces: `e = {}`.

## For-Loops

###### `for .. in` loop

```
cities = ["London", "New York", "Paris", "Oslo", "Ottawa"]
for city in cities:
	print(city)
```

You can also retrieve both keys and values of a dictionary you iterate over.

```
colors = {"john": "hopkins", "joe": "theplumber", "jane": "doe"}
for color in colors:
	print(color, colors[color])
```

## Putting it All Together

Example of retrieving a text file from the web and putting every word of the text file into a python list:

```
from urllib.request import urlopen

with urlopen("http://sixty-north.com/c/t.txt") as story:
	story_words = []
	for line in story:
		line_words = line.decode("utf-8").split()
		for word in line_words:
			story_words.append(word)
```

If you forget the call to `decode("utf-8")`, every element of the list will be prefixed with b.

# Modularity

## Creating, Running, and Importing a Module

One possible setup to making Python programs is to use a text editor like Sublime to write your Python code, then run the `py` file in the terminal.

To run a python file directly from command line, navigate to the directory containing the file and then type in:
`python filename.py`

From the REPL, you instead enter `import <filename>` (note the extension isn't included).

## Defining Functions and Returning Values

Function syntax

```
def square(x):
	return x * x
square(5) # 25
```

Functions don't have to have `return` statements (ex/ just printing stuff to console)

`return` by itself returns `None`

## Distinguishing Between Module Import and Module Execution

When you define your own modules, you can use the same import syntax with them as you would for any other python module.

Example: Write a module `words.py` containing a function `fetch_words()`:

```
from urllib.request import urlopen

def  fetch_words():
	with urlopen("http://sixty-north.com/c/t.txt") as story:
		story_words = []
		for line in story:
			line_words = line.decode("utf-8").split()
			for word in line_words:
				story_words.append(word)

	for word in story_words:
		print(word)
```

Then import it as follows:


```
import words
words.fetch_words()
```

or

```
from words import fetch_words
fetch_words()
```

There is a difference between importing our module and executing it in the REPL. For the latter option, you would add the following to the end of `words.py`:

```
if __name__ == '__main__':
	fetch_words()
```

Then in the terminal run `python3 words.py`

## The Python Execution Model

We're sometimes asked about the differences between Python modules, Python scripts and Python programs. Any `py` file constitutes a Python module. But as we've seen, modules can be written for convenient import, convenient execution, or using the `if __name__ == '__main__'` idiom, or both.

Author strongly recommends making even simple scripts importable since it eases development and testing so much if you can access your code from the Python REPL. Likewise, even modues which are only ever meant to be imported in production settings benefit from having executable test code. For this reason, nearly all modules we create have this form of defining one or more importable functions with a postscript to facilitate execution.

## Main Functions and Command Line Arguments

Rewrite `words.py` as follows:

```
from urllib.request import urlopen

def  fetch_words():
	with urlopen("http://sixty-north.com/c/t.txt") as story:
		story_words = []
		for line in story:
			line_words = line.decode("utf-8").split()
			for word in line_words:
				story_words.append(word)
	return story_words

def print_words(story_words):
	for word in story_words:
		print(word)

def main():
	words = fetch_words()
	print_words(words)

if __name__ == '__main__':
	main()
```

Here's two new ways to perform imports:

```
from words import (fetch_words, print_words) # imports multiple functions from the module

from words import * # recommended only for casual use of the REPL in order to avoid namespace clashes
```

Our `print_words` function doesn't necessarily have to take `fetch_words` as input.

```
print_words([1,2,3]) # 1 2 3
print_words("hello") # h e l l o
```

Access to command line arguments in Python is through an attribute of the `sys` module called `argv`, which is a list of strings. To use it, we must `import sys` at the top of our program, then retrieve values from it via `sys.argv[index]`.

Note that since our module name is the first command line argument passed to python, we need to use `sys.argv[1]` to grab the actual command line argument we wish to add.

Example: For `python3 words.py http://somewebsite.com`, `sys.argv[1]` would refer to the url.

In order to have access to the `sys.argv` variable when importing our module, need to do the following:

```
def main(url):
	words = fetch_words()
	print_items(words)

if __name__ == '__main__':
	main(sys.argv[1])
```

Can now do the following:

```
from words import *
main("http://sixty-north.com/c/t.txt")
```

## Sparse Is Better Than Dense

It is a python convention to place **two** blank lines between functions

## Documenting Your Code Using Docstrings

Any documentation you put in between triple quotes immediately after the function definition can be accessed via the help(<function>) command.

Example:

```
def fetch_words(url):
	"""Fetch a list of words from a URL.

	Args:
		url: The URL of a UTF-8 text document.

	Returns:
		A list of strings containing the words
	"""
```

Then in python terminal: `help(fetch_words)`

To put help documentation for the module itself, simply put a triple quotes comment at the very beginning of the module (even before the imports).

## The Whole Shebang

The shebang allows the program loader to identify which interpreter should be used to run the program. Shebangs have an additional purpose of conveniently documenting at the top of a file whether the Python code they're in is Python 2 or Python 3.

Include `#!/usr/bin/env python3` at the beginning of your module.

This shebang is meant for UNIX systems but supposedly works on Windows as well because of a program called PyLauncher.

With the shebang included, running `words.py <url>` will be sufficient to run your script in Python 3, even if you have Python 2 installed.

# Objects

## Introduction

Every object you create has a unique ID. Example:

```
a = 496
id(a) # returns something like 4298472624
```

More common to use the `is` keyword (`a is b`)

## Argument Passing

Primitives are passed by value and objects are passed by reference, just as you'd expect.

## Function Arguments in Detail

A function argument can be made optional by providing default values. Example:

```
def banner(message, border='-'):
	line = border * len(message)
	print(line)
	print(message)
	print(line)
```

`banner('Hello')` outputs:

```
-----
Hello
-----
```

Whereas `banner('Hello', '*')` outputs:

```
*****
Hello
*****
```

You can even change the order in which you pass arguments by explicitly specifying the argument keywords. Example:

`banner(border='#', message='Greetings')` outputs:

```
#########
Greetings
#########
```

Even though by default, `message` is specified before `border`.

There is an important gotcha when using mutable collections as argument defaults. Here's an example:

```
import time
time.ctime() # 'Mon Jul 29 21:22:10 2013'
def show_default(arg=time.ctime()):
	print(arg)
show_default() # 'Mon Jul 29 21:22:27 2013'
show_default() # Shows same time as above
```

This is because default argument expressions are evaluated only once when the `def` statement is executed.

Here's another example:

```
def add_spam(menu=[]):
	menu.append("spam")
	return menu

add_spam() # returns ["spam"]
add_spam() # returns ["spam", "spam"]
```

This happens because the default list created by the default argument is used exactly once, when the def statement is executed.

The solution to this is to always use immutable objects such as integers or strings for default values. In this case, we will use the immutable `None` object:

```
def add_spam(menu=None):
	if menu is None:
		menu = []
	menu.append('spam')
	return menu
```

## Python's Type System

Python has a strong, dynamic type system.

In a dynamic type system object types are only resolved at runtime. Example:

```
def add(a, b):
	return a + b
```

Nowhere in this definition is there any mention of types.

```
add(5, 7)
add(3.1, 2.4)
add("news", "paper")
```

In a strong type system there is no implicit type conversion. Example:

```
add("The answer is", 42) # produces an error
```

The only exception to this is boolean conversion for conditional statements

## Everything Is An Object

```
import words
type(words) # <class 'module'>
dir(words) # ['__builtins__', '__cached__', ...]
type(words.fetch_words) # <class 'function'>
dir(words.fetch_words) # ['__annotations__', '__call__', etc]
words.fetch_words.__name__ # 'fetch_words'
words.fetch_words.__doc__ # Shows your docstring
```

## Summary

- Use `len()` to measure the length of a string
- You can multiply a string by an integer
 - Produces a new string with multiple copies of the operand
 - This is called the "repetition" operation

Example:

```
"s" * 5 # 'sssss'
name = "john"
name * 2 # 'johnjohn'
```

# Collections

## Tuple

Tuples in Python are immutable sequences of arbitrary objects. Once created, the objects within them cannot be replaced or removed, and new elements cannot be added.

```
t = ("Norway", 4.953, 3)
t[0] # 'Norway'
t[2] # 3
len(t) # 3

for item in t:
	print(item) # Norway 4.954 3

t + (8, 4) # ('Norway', 4.953, 3, 8, 4)
t * 3 # ('Norway', 4.963, 3, 'Norway', 4.963, 3, 'Norway', 4.963, 3)

h = (391)
h # 391
type(h) # <class 'int'>

k = (391,)
k # (391,)
type(k) # <class 'tuple'>

e = ()
e # ()
type(e) # <class 'tuple'>

p = 1, 1, 1, 4, 6, 19
p # (1, 1, 1, 4, 6, 19)
type(p) # <class 'tuple'>
```

Returning multiple values as a tuple is often used in conjunction with a wonderful feature of Python called tuple unpacking. Tuple unpacking is a destructuring operation, which allows us to unpack data structures into named references. Example:

```
def minmax(items):
	return min(items), max(items)

lower, upper = minmax([83, 33, 84, 32, 85, 31, 86])
lower # 31
upper # 86
```

Another example:

```
a = 'jelly'
b = 'bean'
a, b = b, a
a # 'bean'
b = 'jelly'
```

Should you need to create a tuple from an existing collection object such as a list, you can use the tuple constructor:

```
tuple([561, 1105, 1729, 2465]) # (561, 1105, 1729, 2465)
tuple("Name") # ('N', 'a', 'm', 'e')
```

As with most collection types in Python, we can test for containment using the `in` operator:

```
5 in (3, 5, 17, 257, 65537) # True
5 not in (3, 5, 167, 257, 65537) # False
```

## String

```
name = '--'.join(['my', 'name', 'is', 'bob']) # 'my--name--is--bob'
name.split('--') # ['my', 'name', 'is', 'bob']
```

A convenient way to concatenate string is to call `join` on an empty string:

```
''.join(['high', 'way', 'man']) # 'highwayman'
```

Another very useful string method is `partition()`, which divides a string into three sections, the part before the separator, the separator itself, and the part after the separator.

```
"unforgetable".partition("forget") # ('un', 'forget', 'able')
departure, separator, arrival = "London:Edinburg".partition(':')
departure # 'London'
separator # ':'
arrival # 'Edinburg'
```

Often we're not interested in capturing the separator value, so you might see the underscore variable name used. This is not treated in a special way by the python language, but there's an unwritten convention that the underscore variable is for unused or dummy values. This convention is supported by many Python aware development tools, which will supress unused variable warnings for underscore.

```
origin, _, destination = "Seattle-Boston".partition('-')
origin # 'Seattle'
_ # '-'
destination # 'Boston'
```

One of the most interesting and frequently used string methods is format.

```
"The age of {0} is {1}".format('Jim', 32) # 'The age of Jim is 32'

"Reticulating spline {} of {}".format(4, 23) # 'Reticulating spline 4 of 23'

"Current position {latitude} {longitude}".format(latitude="60N", longitude="5E") # 'Current position 60N 5E'

import math
"Math constants: pi={m.pi}, e={m.e}".format(m=math) # 'Math constants: pi=3.141592653589793, e=2.718281828459045'

"Math constants: pi={m.pi:.3f}, e={m.e:.3f}".format(m=math) # 'Math constants: pi=3.142, e=2.718'
```

## Range

```
range(5) # range(0, 5)
for i in range(5):
	print(i) # 0 1 2 3 4

list(range(5, 10)) # [5, 6, 7, 8, 9]
list(range(0, 10, 2)) # [0, 2, 4, 6, 8]
```

Here's an example of unpythonic code:

```
s = [0, 1, 4, 6, 13]
for i in range(len(s)):
	print(s[i]) # 0 1 4 6 13
```

Here's a better way:

```
for v in s:
	print(v) # 0 1 4 6 13
```

If for some reason you need a counter, you should use the built-in `enumerate()` function, which returns an iterable series of pairs, each pair being a tuple:

```
t = [6, 372, 8862, 148800, 2096886]
for p in enumerate(t):
	print(p) # (0, 6), (1, 372), (2, 8862), (3, 148800), (4, 2096886)
```

We can improve this even further by using tuple unpacking and avoiding having to deal directly with the tuple.

```
for i, v in enumerate(t):
	print("i = {}, v = {}".format(i, v))
```

Because of the strong iteration primitives built into Python, ranges aren't widely used in modern Python code.

# List

```
s = "show how to index into sequences".split()
s # ['show', 'how', 'to', 'index', 'into', 'sequences']
s[-2] # 'into'
s[1:4] # ['how', 'to', 'index']
s[1:1] # []
s[1:-1] # ['how', 'to', 'index', 'into'] -> this slice removes only the first and last elements
s[3:] # ['index', 'into', 'sequences']
s[:3] # ['show', 'how', 'to']
```

Note: `s[:x] + s[x:] == s`

```
full_slice = s[:]
full_slice # ['show', 'how', 'to', 'index', 'into', 'sequences']
full_slice is s # False
full_slice == s # True
```

It's important to understand that although we have a new list object, which can be independently modified, the elements within it are references to the same objects referred to by the original list. In the event that these objects are both mutable and modified as opposed to replaced, the change will be seen in both lists.

Example:

```
s = ["john", [1, 2, 3], "doe"]
s2 = s[:]
s[2].append(4) # modify the mutable inner list
s # ["john", [1, 2, 3, 4], "doe"]
s2 # ["john", [1, 2, 3, 4], "doe"] # both lists are affected
```

We teach this full slice list copying idiom because you're likely to see it in the wild, and it's not immediately obvious what it does. You should be aware that there are other more readable ways of copying a list such as the `copy()` method or even a simple call to the `list()` constructor passing the list to be copied.

```
u = s.copy()
u # ['show', 'how', 'to', 'index', 'into', 'sequences']
v = list(s)
v # ['show', 'how', 'to', 'index', 'into', 'sequences']
```

Author's preference is the 3rd form using the list constructor since it has the advantage of working with any iterable series as the source and not just lists.

You must be aware, however, that all of these techniques perform a shallow copy. That is, they create a new list containing the same object references as the source list, but don't copy the referred to objects.

## Shallow Copies

```
a = [[1, 2], [3, 4]]
b = a[:]
a is b # False
a == b # True
a[0] is b[0] # True
a[1] is b[1] # True
```

If a deep copy were performed, `a[0] is b[0]` would return `False`.

## List Repetition

```
c = [21, 37]
d = c * 3
d # [21, 37, 21, 37, 21, 37]

[0] * 9 # [0, 0, 0, 0, 0, 0, 0, 0, 0]
```

Unfortunately list repetition is shallow:

```
s = [[1]] * 3
s # [[1], [1], [1]]
s[0].append(2)
s # [[1, 2], [1, 2], [1, 2]]
```

## More on List

```
w = "the quick brown fox jumps over the lazy dog".split()
w # ['the', 'quick', 'brown', 'fox', 'jumps', 'over', 'the', 'lazy', 'dog']
i = w.index('fox')
i # 3
w[i] # 'fox'
```

Can count occurrences of a word:

```
w.count('the') # 2
```

Can delete elements using `del`:

```
del w[1]
w # ['the', 'brown', 'fox', 'jumps', 'over', 'the', 'lazy', 'dog']
w.remove('the') # ['brown', 'fox', 'jumps', 'over', 'the', 'lazy', 'dog'] -> Notice the 2nd 'the' is still there
```

To insert a value into the list at a specified index:

```
a = "I accidentally the whole universe".split()
a.insert(2, "destroyed")
a # ['I', 'accidentally', 'destroyed', 'the', 'whole', 'universe']
' '.join(a) # 'I accidentally destroyed the whole universe'
```

## Growing Lists

```
m = [2, 1, 3]
n = [4, 7, 11]
k = m + n
k # [2, 1, 3, 4, 7, 11]
k += [18, 29, 47]
k # [2, 1, 3, 4, 7, 11, 18, 29, 47]
k.extend([76, 129, 199])
k # [2, 1, 3, 4, 7, 11, 18, 29, 47, 76, 129, 199]
```

## Reversing and Sorting Lists

```
g = [1, 5, 2, 8, 9, 7, 3]
g.reverse()
g # [3, 7, 9, 8, 2, 5, 1]
g.sort()
g # [1, 2, 3, 5, 7, 8, 9]
g.sort(reverse=True)
g # [9, 8, 7, 5, 3, 2, 1]
```

An example for sorting strings:

```
h = 'not perplexing do handwriting family where I illegibly know doctors'.split()
h # ['not', 'perplexing', 'do', 'handwriting', 'family', 'where', 'I', 'illegibly', 'know', 'doctors']
h.sort(key=len)
h # ['I', 'do', 'not', 'know', 'where', 'family', 'doctors', 'illegibly', 'perplexing', 'handwriting']
' '.join(h) # 'I do not know where family doctors illegibly perplexing handwriting'
```

To avoid side-effects, use `sorted` instead:

```
x = [4, 9, 2, 1]
y = sorted(x)
y # [1, 2, 4, 9]
x # [4, 9, 2, 1]

p = [9, 3, 1, 0]
q = reversed(p)
q # <list_reverseiterator object at 0x02679B70>
list(q) # [0, 1, 3, 9]
```

## Dictionaries

String keys must be immutable

You should never rely on the order of items in the dictionary. It's essentially random and may even vary between different runs of the same program.

There's also a named constructor, `dict()`, which can convert other types to the dictionaries. We can use the constructor to copy from an iterable series of key-value pairs stored in tuples:

```
names_and_ages = [ ('Alice', 32), ('Bob', 48), ('Charlie', 28), ('Daniel', 33) ]
d = dict(names_and_ages)
d # {'Bob': 48, 'Alice': 32, 'Daniel': 33, 'Charlie': 28}
```

So long as the keys are legitimate Python identifiers, it's even possible to create a dictionary directly from keyword arguments passed to `dict`:

```
phonetic = dict(a='alfa', b='bravo', c='charlie', d='delta', e='echo', f='foxtrot')
phonetic # {'c': 'charlie', 'f': 'foxtrot', 'd': 'delta', 'b': 'bravo', 'e': 'echo', 'a': 'alfa'}
```

As with lists, dictionary copying is shallow by default, copying only the references to the key and value objects, not the value objects themselves.

There are two means of copying a dictionary of which we most commonly see the second. The first technique is to use the `copy()` method. The second means of copying is simply to pass an existing dictionary to the `dict` constructor.

```
a = {'val': 1, 'otherVal': 2}
b = dict(a)
a # {'val': 1, 'otherVal': 2}
b #{'val': 1, 'otherVal': 2}
```

If you need to extend a dictionary with definitions from another dictionary, you can use the `update()` method:

```
a = {'val': 1}
b = {'param' : 2}
a.update(b)
a # {'val': 1, 'param': 2}
```

Iteration:

```
colors = dict(aquamarine='#7FFFD4', burlywood='#DEB887', chartreuse='#7FFFF00', cornflower='#6495ED')
for key in colors:
	print("{key} => {value}".format(key=key, value=colors[key]))
```

Result:

```
burlywood => #DEB887
chartreuse => #7FFFF00
aquamarine => #7FFFD4
cornflower => #6495ED
```

If we want to iterate over only the values, we can use the `values()` dictionary method:

```
for value in colors.values():
	print(value)
```

There's also a `keys()` method, which provides a view onto the keys of the dictionary, although it's not often used because default iteration of dictionaries is by key.

```
for key in colors.keys()
	print(key)
```

Often we want to iterate over the keys and values in tandem. Each key-vale pair in a dictionary is called an item, and we can get ahold of an iterable view of the items using the `items()` dictionary method.

```
for key, value in colors.items():
	print("{key} => {value}".format(key=key, value=value))
```

The membership test for dictionaries use the `in` and `not in` operators and only work for the keys.

```
d = {'a': 1, 'b': 2, 'c': 3}
'a' in d # True
'z' in d # False
```

A nice way to print dictionaries is by using the `pprint` library, as follows:

```
from pprint import pprint as pp
```

For some reason this didn't work any differently for me compared to a regular `print` statement.

## Set

The set data type is an unordered collection of unique elements. The collection is mutable in so far as elements can be added and removed from the set, but each element must itself be immutable. Sets have a literal form very similar to dictionaries:

```
p = {1, 2, 3, 4}
p # {1, 2, 3, 4}
type(p) # <class 'set'>
d = {}
type(d) # <class 'dict'>
e = set()
e # set()
```

The `set()` constructor can create a set from any iterable series such as a list. Duplicates are discarded. In fact, a common use of sets is to efficiently remove duplicate items from a series of objects.

```
s = set([2, 2, 3, 5, 8, 3])
s # {8, 2, 3, 5}
```

Naturally sets are iterable, although the order is arbitrary:

```
for x in {1, 2, 4, 8, 16, 32}:
	print(x) # 32 1 2 4 8 16
```

Membership is a fundamental operation for sets, and as with the other collection types is performed using the `in` and `not in` operators.

```
q = {2, 9, 6, 4}
3 in q # False
3 not in q # True
```

To add a single element to the set, simply use the add method.

```
k = {81, 108}
k.add(54)
k # {81, 108, 54}
k.add(81)
k # {81, 108, 54} -> adding an element that already exists has no effect
```

Multiple elements can be added in one go from any iterable series including another set using the update method.

```
k.update([37, 128, 97])
k # {128, 97, 37, 108, 81, 54}
```

Two methods are provided for removing elements from sets. The first, `remove`, requires that the element to be removed is present in the set; otherwise, a `KeyError` is given.

```
k # {128, 97, 37, 108, 81, 54}
k.remove(97)
k # {128, 37, 108, 81, 54}
```

The second method, `discard`, is less fussy an simply has no effect if the item is not a member of the set.

As with the other built-in collections, `set` sports a `copy()` method, which performs a shallow copy of the set, copying references, but not the objects they refer to.

Perhaps the most useful aspect of the set type is a group of powerful set algebra operations which are provided. These allow us to easily compute set unions, set differences, and set intersections and to evaluate whether two sets have subset, superset, or disjoint relations.

```
blue_eyes = {'Olivia', 'Harry', 'Lily', 'Jack', 'Amelia'}
blond_hair = {'Harry', 'Jack', 'Amelia', 'Mia', 'Joshua'}
smell_hcn = {'Harry', 'Amelia'}
taste_pct = {'Harry', 'Lily', 'Amelia', 'Lola'}
o_blood = {'Mia', 'Joshua', 'Lily', 'Olivia'}
b_blood = {'Amelia', 'Jack'}
a_blood = {'Harry'}
ab_blood = {'Joshua', 'Lola'}

blue_eyes.union(blond_hair) # {'Jack', 'Lily', 'Olivia', 'Joshua', 'Mia', 'Amelia', 'Harry'}
blue_eyes.union(blond_hair) == blond_hair.union(blue_eyes) # True

blue_eyes.intersection(blond_hair) # {'Jack', 'Harry', 'Amelia'}
blue_eyes.intersection(blond_hair) == blond_hair.intersection(blue_eyes) # True

blond_hair.difference(blue_eyes) # {'Joshua', 'Mia'}
blond_hair.difference(blue_eyes) == blue_eyes.difference(blond_hair) # False
```

If you want to determine which people have exclusively blond hair or blue eyes, but not both, we can use the `symmetric_difference` method.

```
blond_hair.symmetric_difference(blue_eyes) # {'Lily', 'Olivia', 'Joshua', 'Mia'}
```

Other methods:
- `issubset`
- `issuperset`
- `isdisjoint`

## Collection Protocols

In Python, a protocol is a set of operations or methods that a type must support if it is to implement that protocol.

Most of the collection types we've looked at support the container, size, and iterable protocols. Many of them are also sequences.

- The container protocol requires that membership testing using the `in` and `not in` operators must be supported.
- The size protocol requires that the number of elements in a collection can be determined by passing the collection to the built-in `len` function
- Iterables provide a means of yielding elements one-by-one as they're requested. One important property of iterables is that they can be used with `for` loops.
- The sequence protocol requires that items can be retrieved using square brackets with an integer index, that items can be searched for with the `index` method, that items can be counted with the `count` method, and that a reversed copy of the sequence can be produced with the reversed built-in function

# Handling Exceptions

## Exceptions and Control Flow

Exceptions are better tested in modules than in the REPL, so create a file called `exceptional.py`.

You need to restart the REPL whenever you make changes to a module! It's not enough just to re-import it.

## Handling Exceptions

Each `try` block can have multiple corresponding `except` blocks, which intercept exceptions of different types.

```
'''A module for demonstrating exceptions'''

def convert(s):
    '''Convert to an integer.'''
    try:
        x = int(s)
        print("Conversion succeeded! x = ", x)
    except ValueError:
        print("Conversion failed!")
        x = -1
    except TypeError:
        print("Conversion failed!")
        x = -1
    return x
```

And in the REPL:

```
convert(5) # Conversion succeeded! x =  5
convert("hedgehog") # Conversion failed! -1 -> ValueError
convert([1, 2, 3]) # Conversion failed! -1 -> TypeError
```

Essentially, we're relying on Python's built-in `ValueError` and `TypeError` exceptions to be thrown when passing in invalid values, and we're catching those exceptions so we can handle them however we want.

For a more concise handler, could write the following:

```
except (ValueError, TypeError):
	print("Conversion failed!")
```

## Exceptions as APIs

Exceptions form an important aspect of the API of a function. Callers of a function need to know which exceptions to expect under various conditions so that they can ensure appropriate exception handlers are in place.

```
def sqrt(x):
	'''Compute square roots using the method of Heron of Alexandria.

	Args:
		x: The number for which the square root is to be computed.

	Returns:
		The square root of x.

	Raises:
		ValueError: If x is negative.
	'''

	if x < 0:
		raise ValueError("Cannot compute square root "
			"of negative number {}".format(x))

	guess = x
	i = 0
	while guess * guess != x and i < 20:
		guess = (guess + x / guess) / 2.0
		i += 1
	return guess

import sys

def main():
	try:
		print(sqrt(9))
		print(sqrt(2))
		print(sqrt(-1))
		print("This is never printed")
	except ValueError as e:
		print(e, file=sys.stderr)

	print("Program execution continues normally here.")

if __name__ == '__main__':
	main()
```

Only confusing part is `print(e, file=sys.stderr)`

## Exceptions, APIs, and Protocols

There are a handful of common exception types in Python, and usually when you need to raise an exception in your own code, one of the built-in types is a good choice. Much more rarely you'll need to define new exception types. Often if you're deciding what exceptions your code should raise, you should look for similar cases in existing code. The more your code follows existing patterns, the easier it will be for people to integrate and understand.

Common exception types:

`IndexError` is raised when an integer index is out of range.

`ValueError` is raised when the object is of the right type, but contains an inappropriate value. Example: `int("jim")`.

`KeyError` is raised when a look-up in a mapping fails. Example:

```
codes = dict(gb=44, us=1, no=47, fr=33, es=34)
codes['de'] # Throws the exception
```

## Do Not Guard Against Type Errors

We tend to not protect against TypeErrors in Python. To do so runs against the grain of dynamic typing in Python and limits the reuse potential of the code that we write.

## EAFP vs LBYL

Look Before You Leap
It's Easer to Ask Forgiveness than Permission

Python is strongly in favor of EAFP because it puts primary logic for the happy path in its most readable form, with deviations from the normal flow handled separately rather than interspersed with the main flow. Example:

LBYL version:

```
import os

p = '/path/to/datafile.dat'

if os.path.exists(p):
	process_file(p)
else:
	print('No such file as {}'.format(p))
```

Problems:
- We're only checking for existence. What if the file exists, but contains garbage? What if the path refers to a directory instead of a file? According to LBYL, we should add preemptive tests for these too.
- A more subtle problem is that there is a race condition here. It's possible for the file to be deleted, for example by another process, between the existence check and the `process_file` call, a classic atomicity issue.

EAFP version:

```
p = '/path/to/datafile.dat'

try:
	process_file(f)
except OSError as e:
	print('Could not process file because {}'\
    	.format(str(e)))
```

Here we simply attempt the operation without checks in advance, but we have an exception handler in place to deal with any problems. We don't even need to know in a lot of detail exactly what might go wrong. Here we catch `OSError`, which covers all manner of conditions such as file not found and using directories where files are expected.

Without exceptions, that is using error codes instead, you are forced to include error handling directly in the main flow of the logic. Since exceptions interrupt the main flow, they allow you to handle exceptional cases non-locally.

## Resource Cleanup with Finally

```
import os

def make_at(path, dir_name):
	original_path = os.getcwd()
    os.chdir(path)
    os.mkdir(dir_name) # If this fails...
    os.chdir(original_path) # ... then this won't happen!
```

In the above, should the call to `os.mkdir` fail for some reason, the current working directory of the Python process won't be restored to its original value, and the `make_at` function will have had an unintended side effect. To fix this, we'd like the function to restore the original current working directory under all circumstances. We can achieve this with a `try...finally` block:

```
import os

def make_at(path, dir_name):
	original_path = os.getcwd()
    try:
    	os.chdir(path)
    	os.mkdir(dir_name)
    except OSError as e:
    	print(e, file=sys.stderr)
        raise # Re-raise the exception after printing the error
    finally:
    	os.chdir(original_path)
```

## Summary

- `raise` without an argument re-raises the current exception.
- We tend to not routinely check for TypeErrors. To do so would negate the flexibility afforded to us by Python's dynamic type system.
- A function's exception form part of its API, and as such they should be documented properly.
- Prefer to use built-in exception types when possible

# Iterables

## Introduction

Comprehensions in Python are a concise syntax for describing lists, sets, or dictionaries in a declarative or functional style. This shorthand is readable and expressive, meaning that comprehensions are very effective at communicating intent to human readers. Some comprehensions almost read like natural language, making them nicely self-documenting.

```
words = "Why sometimes I have believed as many as six impossible things before breakfast".split()
[len(word) for word in words] # [3, 9, 1, 4, 8, 2, 4, 2, 3, 10, 6, 6, 9]
```

The general form of list comprehensions is "expression of item for item in iterable".

```
[expr(item) for item in iterable]
```

The longform for the example given earlier would be:

```
lengths = []
for word in words:
	lengths.append(len(word))
```

Notice that the source object over which we iterate doesn't need to be a list. It can be any iterable object such as a tuple.

## Set Comprehensions

```
from math import factorial
{len(str(factorial(x))) for x in range(20)} # {1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 13, 14, 15, 16, 18}
```

## Dictionary Comprehensions

```
{ key_expr:value_expr for item in iterable }
```

Example of interchanging the keys and values:

```
country_to_capital = {'UK': 'London', 'Morocco': 'Rabat', 'Sweden': 'Stockholm'}
capital_to_country = {capital: country for country, capital in country_to_capital.items()}
capital_to_country # {'Rabat': 'Morocco', 'London': 'UK', 'Stockholm': 'Sweden'}
```

Another example:

```
words = ["hi", "hello", "foxtrot", "hotel"]
{word[0]: word for word in words} # {'h': 'hotel', 'f': 'foxtrot'}
```

Note that since we're using a set, the `'h': 'hi'` entry is being overwritten by the `'h': 'hotel'` one.

Remember that there's no limit to the complexity of the expression you can use in any of the comprehensions, but for the sake of your fellow programmers you should avoid going overboard and extract complet expressions into separate functions to preserve readability. The following is close to the limit of being reasonable for a dictionary comprehension:

```
import os
import glob
file_sizes = {os.path.realpath(p): os.stat(p).st_size for p in glob.glob('*.py')}
pp(file_sizes)
# {'/Users/pyfund/examples/exceptional.py': 400,
# '/Users/pyfund/examples/keypress.py': 778,
# '/Users/pyfund/examples/scopes.py': 133,
# '/Users/pyfund/examples/words.py': 1185}
```

## Filtering Predicates

You can add an optional filtering clause to your comprehensions:

```
[ expr(item) for item in iterable if predicate(item) ]
```

Example:

```
numbers = [1, 2, 3, 4, 5, 6]
[x for x in numbers if x % 2 == 0] # [2, 4, 6]
```

Notice the `x for x` construct. This is because we're not applying any transformation to the filtered values.

There's nothing stopping us from combining a filtering predicate with a transforming expression.

```
[x*x for x in numbers if x % 2 == 0] # [4, 16, 36]
{x*x: x for x in numbers if x % 2 == 0} # {16: 4, 36: 6, 4: 2}
```

## Moment of Zen

Comprehensions are often more readable than the alternative; however, it's possible to overuse comprehensions. Sometimes a long or complex comprehension may be less readable than the equivalent `for` loop.

Above all, your comprehensions should ideally be purely functional. That is, they should have no side effects. If you need to create side effects such as printing to the console during iteration, use another construct such as a `for` loop instead.

## Stateful Generator Functions

[Currently here]