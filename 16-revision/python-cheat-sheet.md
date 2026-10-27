# Python Cheat Sheet

## Table of Contents
- [Getting Started](#getting-started)
- [Basics](#basics)
- [Conditionals](#conditionals)
- [Loops](#loops)
- [Lists](#lists)
- [Tuples](#tuples)
- [Dictionaries](#dictionaries)
- [Sets](#sets)
- [Functions](#functions)
- [Comprehensions](#comprehensions)
- [Modules and Packages](#modules-and-packages)
- [Exception Handling](#exception-handling)
- [File Handling](#file-handling)
- [Object-Oriented Programming](#object-oriented-programming)

---

## Getting Started

```python
# Check the version (terminal)
#   python --version       Windows
#   python3 --version      macOS / Linux

# Run
#   python hello.py        run a script
#   python                 start the >>> shell   (exit with exit())
#   python -c "print(1)"   run one line of code
#   python -m pip --version  run a module
#   python -i file.py      run, then stay in the shell
#   Ctrl + C               stop a running program

print("Hello, World!")      # first program
name = input("Name: ")      # always returns text
print(f"Hi, {name}!")

if name:                    # colon + 4-space indent
    print("has name")

total = (1 +
         2)                 # continue a line inside brackets

import keyword
keyword.kwlist              # the 35 keywords
```

**Key points:** Python is case-sensitive. A block is shown by indentation (4 spaces). `#` starts a comment. Do not name your file after a standard module.

---

## Basics

```python
# comment
print("text", 10, sep=" ", end="\n")      # output

name = "Karan"                  # str
age = 19                        # int
price = 9.99                    # float
ok = True                       # bool
nothing = None                  # NoneType

type(age)                       # check the type
isinstance(age, int)            # True / False

text = input("Enter: ")         # ALWAYS a string
n = int(input("Number: "))      # whole number
x = float(input("Decimal: "))   # decimal number

int("42")                       # str to int       -> 42
float("3.5")                    # str to float     -> 3.5
str(10)                         # number to str    -> "10"
bool(0)                         # to bool          -> False

print(f"{name} is {age}")       # f-string
print(f"{price:.2f}")           # 2 decimal places
```

| Operators | |
|---|---|
| Arithmetic | `+  -  *  /  //  %  **` |
| Assignment | `=  +=  -=  *=  /=  //=  %=  **=` |
| Comparison | `==  !=  >  <  >=  <=` |
| Logical | `and  or  not` |
| Membership | `in  not in` |
| Identity | `is  is not` |

### Key Takeaways
- A **variable** is a name that refers to a value. `=` assigns; you do not declare types.
- Variable names: letters, digits and `_` only, cannot start with a digit, no spaces, not a keyword, **case-sensitive**.
- Main types: `int`, `float`, `str`, `bool`, `list`, `tuple`, `set`, `dict`, `NoneType`.
- Check a type with `type()` or `isinstance()`.
- **Immutable** (`int`, `float`, `str`, `bool`, `tuple`) cannot be changed in place; **mutable** (`list`, `dict`, `set`) can.
- `/` always gives a float, `//` gives the whole-number part, `%` gives the remainder, `**` is power.
- `input()` **always returns a string**. Use `int()` or `float()` to get a number.
- `int(3.9)` gives `3` (it cuts, it does not round); `int("3.5")` is an error.
- Text and numbers cannot be joined with `+`. Use `str()` or an f-string.
- Use **f-strings** for output: `f"{value:.2f}"`.
- `0.1 + 0.2` is not exactly `0.3` in any language; round when printing.
- `True` and `False` start with a capital letter; empty values (`0`, `""`, `[]`, `None`) are falsy.

---

## Conditionals

```python
# if
if condition:
    ...

# if / else
if condition:
    ...
else:
    ...

# if / elif / else
if cond1:
    ...
elif cond2:
    ...
else:
    ...

# nested if
if cond1:
    if cond2:
        ...

# shorthand if (one line)
if cond: statement

# ternary (shorthand if-else)
value = a if cond else b

# placeholder
if cond:
    pass

# match case (Python 3.10+)
match value:
    case 1:
        ...
    case _:
        ...
```

| Operators | |
|---|---|
| Comparison | `==  !=  >  <  >=  <=` |
| Logical | `and`  `or`  `not` |
| Membership | `in`  `not in` |
| Identity | `is`  `is not` |
| Chained | `a < b < c` |

### Key Takeaways
- A condition produces `True` or `False`; the block runs only when it is `True`.
- Every `if`, `elif` and `else` line ends with a **colon**, and its block is **indented**.
- In an `if / elif / else` chain **only the first true block runs**; `else` catches the rest and has no condition.
- Order `elif` conditions from **most specific to least specific**.
- `=` assigns, `==` compares.
- `and` needs both true, `or` needs one true, `not` reverses; precedence is `not`, then `and`, then `or`.
- `x == 1 or 2` is a classic bug; write `x == 1 or x == 2` or `x in (1, 2)`.
- Empty values (`0`, `""`, `[]`, `None`) are **falsy**; use `if not items:`.
- Use `is None` to test for `None`.
- `pass` is a do-nothing placeholder for empty blocks.
- The ternary form `a if cond else b` is for **simple** choices only.
- Use `match / case` (3.10+) when comparing one value against many options.

---

## Loops

```python
# for loop
for item in iterable:
    ...

# range
range(5)            # 0 1 2 3 4
range(1, 6)         # 1 2 3 4 5
range(0, 10, 2)     # 0 2 4 6 8
range(5, 0, -1)     # 5 4 3 2 1

# while loop
while condition:
    ...                 # update something so the condition can become False

# nested loop
for i in range(3):
    for j in range(3):
        ...

# control statements (they only work inside a loop)
for x in items:
    if stop_condition:
        break               # stop the whole loop
    if skip_condition:
        continue            # skip to the next round
    pass                    # do nothing (placeholder)

# loop else (runs only if the loop did NOT hit break)
for x in items:
    if x == target:
        break
else:
    print("not found")

# helpers
enumerate(items)             # (index, item) pairs
enumerate(items, start=1)    # count from 1
zip(a, b)                    # go through two lists together
reversed(items)              # backwards
sorted(items)                # in order
```

### Key Takeaways
- A loop **repeats** code. Use `for` for "each item" or "N times", and `while` for "until a condition changes".
- `for` and `while` lines **end with a colon**, and the body is **indented**.
- `range(stop)` starts at **0**, and the **stop value is never included**.
- A `while` loop needs a **start value**, a **condition**, and a **change inside the loop**. Without the change you get an infinite loop (stop it with Ctrl + C).
- **Nested loops:** the inner loop runs **fully** for every round of the outer loop. Total rounds = outer x inner.
- `break` stops the **whole loop**, `continue` skips only the **current round**, `pass` does nothing.
- `break` in a nested loop stops only the **inner** loop.
- Loop `else` runs only when the loop finished **without `break`**.
- Create totals and counters **before** the loop, and print the result **after** it.
- Do not add or remove items of a list while looping over it; loop over a copy or build a new list.
- `enumerate()` gives the index and the item, and `zip()` walks through two lists together.

---

## Lists

```python
lst = [10, 20, 30, 40, 50]

lst[0]            # first item
lst[-1]           # last item
lst[1:4]          # [20, 30, 40]  slice
lst[::-1]         # reversed copy
lst[1] = 99       # change one item
lst[1:3] = [7]    # change a range

lst.append(60)        # add at the end
lst.insert(0, 5)      # add at an index
lst.extend([1, 2])    # add many items

lst.pop()             # remove and return last
lst.pop(0)            # remove and return at index
lst.remove(40)        # remove first match by value
del lst[0]            # remove by index
lst.clear()           # empty the list

lst = [3, 1, 2]
lst.sort()            # in place, returns None
sorted(lst)           # new sorted list
lst.reverse()         # in place
lst.copy()            # shallow copy

len(lst)              # number of items
min(lst)              # smallest
max(lst)              # largest
sum(lst)              # total
2 in lst              # membership test
lst.index(2)          # position of first 2
lst.count(2)          # how many 2s
[x * 2 for x in lst if x > 1]    # comprehension
```

### Key Takeaways
- List = **ordered + mutable + allows duplicates**, written with `[ ]`.
- Indexes start at **0**; negative indexes count from the end.
- Slicing `[start:end:step]` **excludes** `end` and never raises `IndexError`.
- `append()` adds **one** item, `extend()` adds **each item** of an iterable, `insert()` adds at a position.
- `pop()` removes **by index** (and returns it), `remove()` removes **by value** (first match only), `del` removes by index or slice.
- `sort()` changes the list and returns `None`; `sorted()` returns a **new** list.
- `b = a` does not copy; `copy()` is **shallow**, use `copy.deepcopy()` for nested lists.
- Never change the size of a list while looping over it; build a new list instead.
- `[[0] * 3] * 3` repeats the **same** inner list; use a comprehension.
- List comprehension: `[expression for item in iterable if condition]`.

---

## Tuples

```python
t = (10, 20, 30, 40, 50)

t[0]          # 10          first item
t[-1]         # 50          last item
t[1:4]        # (20,30,40)  slice
t[::-1]       # reversed
len(t)        # 5
20 in t       # True
t.count(10)   # 1
t.index(30)   # 2
t + (60,)     # concatenate
t * 2         # repeat
a, *b = t     # unpack with star
tuple(sorted(t))   # sorted tuple
```

###  Key Takeaways
- Tuple = **ordered + immutable + allows duplicates**.
- The **comma** makes a tuple; single item needs `(x,)`.
- To "change" a tuple: **tuple → list → edit → tuple**.
- Only **two** methods: `count()` and `index()`.
- Unpacking with `*` collects extra items into a **list**.
- Use tuples for **fixed data**, lists for **changing data**.

---

## Dictionaries

```python
d = {'name': 'Karan', 'age': 19}

d['name']                 # access (KeyError if missing)
d.get('x', 'default')     # safe access
d['city'] = 'Pune'        # add or change
d.update({'age': 20})     # add or change many
d.setdefault('x', 0)      # add only if missing
d.pop('city')             # remove key, returns value
d.popitem()               # remove last inserted pair
del d['age']              # remove key
d.clear()                 # empty the dict
d.keys()                  # view of all keys
d.values()                # view of all values
d.items()                 # view of (key, value) pairs
'name' in d               # key check
len(d)                    # number of pairs
d.copy()                  # shallow copy
d | other                 # merge (3.9+)
{k: v for k, v in d.items()}    # comprehension
```

### Key Takeaways
- Dictionary = **key:value pairs**, **ordered (3.7+)**, **mutable**, **unique hashable keys**.
- Access by **key**, never by position. Use `get()` to avoid `KeyError`.
- `in` checks **keys**; use `.values()` to check values.
- `update()` changes an existing key or adds a new one.
- `pop(key)` returns the value; `popitem()` removes the **last inserted** pair.
- Never change the **size** of a dictionary while looping over it.
- `copy()` is **shallow**; use `copy.deepcopy()` when values are nested.
- `{}` is an empty **dictionary**; an empty set is `set()`.

---

## Sets

```python
s = {1, 2, 3}

s.add(4)                    # add one item
s.update([5, 6])            # add many items
s.remove(1)                 # remove (KeyError if missing)
s.discard(100)              # remove (no error if missing)
s.pop()                     # remove an arbitrary item
s.clear()                   # empty the set
del s                       # delete the set entirely

# Each line: operator form, then the equivalent method
A | B       # same as A.union(B)                  -> everything
A & B       # same as A.intersection(B)           -> common items
A - B       # same as A.difference(B)             -> only in A
A ^ B       # same as A.symmetric_difference(B)   -> not common

A.isdisjoint(B)             # nothing in common?
A.issubset(B)               # A inside B?   (A <= B)
A.issuperset(B)             # A contains B? (A >= B)

len(A)                      # number of items
x in A                      # membership test
A.copy()                    # independent copy
set()                       # empty set
frozenset(A)                # immutable version
```

### Key Takeaways
- Set = **unordered + unique + mutable**, items must be **hashable**.
- Empty set → `set()`, **not** `{}`.
- `remove()` → error if missing; `discard()` → silent.
- `pop()` removes an **arbitrary** item.
- Methods with `_update` **change the original**; others **return a new set**.
- `difference` is **order-sensitive**; union, intersection and symmetric difference are not.
- Use sets for **uniqueness**, **fast lookup**, and **Venn-diagram style operations**.

---

## Functions

```python
def greet(name, greeting="Hello"):     # parameter with a default
    """Say hello."""                   # docstring
    return f"{greeting}, {name}!"      # return a value

greet("Riya")                          # positional
greet(name="Riya", greeting="Hi")      # keyword

def total(*nums, **options): ...       # *args -> tuple, **kwargs -> dict
def f(a, /, b, *, c): ...              # a positional-only, c keyword-only

nums = [1, 2]
f(*nums)                               # unpack a list into arguments
f(**{"b": 1})                          # unpack a dict into keywords

def pair():
    return 1, 2                        # returns a tuple
x, y = pair()

counter = 0
def inc():
    global counter                     # change a global
    counter += 1

def outer():
    n = 0
    def inner():
        nonlocal n                     # change an enclosing variable
        n += 1
    return inner

square = lambda n: n * n               # lambda: one expression
sorted(data, key=lambda s: s[1])       # common use

def fact(n):                           # recursion
    return 1 if n == 0 else n * fact(n - 1)
```

**Scope order (LEGB):** Local, Enclosing, Global, Built-in.

**Built-ins:** `len` `min` `max` `sum` `abs` `round` `sorted` `reversed` `enumerate` `zip` `map` `filter` `any` `all` `isinstance` `range`.

**Key points:** no `return` means `None`. Default values are created once, so never use `[]` or `{}` as a default. Assigning to a name inside a function makes it local. Recursion needs a base case (limit is about 1000 calls).

---

## Comprehensions

```python
[x * 2 for x in nums]                   # list
[x for x in nums if x > 0]              # filter (if at the END)
[x if x > 0 else 0 for x in nums]       # if/else (at the START)
[(x, y) for x in a for y in b]          # nested loops
[n for row in matrix for n in row]      # flatten
[[0] * 3 for _ in range(3)]             # 2D grid

{x ** 2 for x in nums}                  # set (unique items)
{k: v for k, v in d.items() if v > 0}   # dict
{v: k for k, v in d.items()}            # swap keys and values

(x * 2 for x in nums)                   # generator (lazy, one use)
sum(x * x for x in nums)                # no extra brackets needed
tuple(x for x in nums)                  # there is no tuple comprehension
next(gen, "default")                    # one value at a time
```

**Key points:** a list comprehension builds the whole list; a generator makes one item at a time and can be used once. The loop variable does not leak out of a comprehension. If it is hard to read, use a normal loop.

---

---

## Modules and Packages

```python
import math                          # use as math.sqrt(4)
from math import sqrt, pi            # use as sqrt(4)
import math as m                     # alias
dir(math)                            # names inside a module
help(math.sqrt)                      # documentation

import sys
sys.path                             # folders Python searches

# mymath.py
def add(a, b):
    return a + b

if __name__ == "__main__":           # runs only when run directly
    print(add(2, 3))

# packages: folder + __init__.py
from shop.cart import total          # absolute import
from .prices import with_tax         # relative import (inside a package)
```

```
python mymath.py                     # run a file
python -m shop.cart                  # run a module inside a package

python -m pip --version
python -m pip install name           # latest
python -m pip install name==1.2.3    # exact version
python -m pip install -U name        # upgrade
python -m pip uninstall name
python -m pip list
python -m pip show name
python -m pip freeze > requirements.txt
python -m pip install -r requirements.txt

python -m venv .venv                 # create
.venv\Scripts\activate              # activate on Windows
source .venv/bin/activate            # activate on macOS / Linux
deactivate                           # leave
```

**Key points:** `__name__` is `"__main__"` when a file is run directly and the module name when imported. A module loads only once. Search order: loaded modules, built-ins, `sys.path`. Use `python -m pip` so pip matches your Python. One virtual environment per project; keep `.venv/` out of Git.

---

## Exception Handling

```python
try:
    x = int(input("Number: "))
    result = 10 / x
except ValueError:                   # specific first
    print("not a number")
except ZeroDivisionError as e:       # as e -> the exception object
    print("error:", e)
except (TypeError, KeyError):        # several types: use a tuple
    print("type or key problem")
except Exception as e:               # last resort
    print(type(e).__name__, e)
else:                                # only if NO exception
    print("result", result)
finally:                             # ALWAYS runs
    print("done")

raise ValueError("bad value")        # raise your own
raise                                # re-raise (inside except)
raise RuntimeError("x") from e       # chain with the cause

class MyError(Exception):            # custom exception
    pass

assert x > 0, "x must be positive"   # development check only
```

| Exception | Typical cause |
|---|---|
| `ValueError` | Right type, bad value: `int("abc")` |
| `TypeError` | Wrong type: `"a" + 1` |
| `ZeroDivisionError` | `1 / 0` |
| `IndexError` / `KeyError` | Bad index / missing key |
| `NameError` | Name not defined |
| `AttributeError` | No such attribute or method |
| `FileNotFoundError` | File does not exist |
| `ModuleNotFoundError` | Module not found |
| `RecursionError` | Recursion too deep |

**Key points:** syntax errors stop the file before it runs; exceptions happen while it runs. Only the first matching `except` runs. Catch specific exceptions and keep the `try` block small. `finally` runs even with `return`. A bare `except:` also catches `Ctrl + C`.

---

---

## File Handling

```python
# read
with open("notes.txt", encoding="utf-8") as f:   # "r" is the default
    text = f.read()                  # whole file as one string
    # f.readline()                   # one line
    # f.readlines()                  # list of lines (with \n)
    # f.read().splitlines()          # list of lines (no \n)
    # for line in f: ...             # line by line (best for big files)
    # f.seek(0)                      # back to the start

# write
with open("out.txt", "w", encoding="utf-8") as f:    # "w" erases the file
    f.write("text\n")                # no newline added for you
    f.writelines(["a\n", "b\n"])
    print("text", file=f)            # print adds the newline

with open("out.txt", "a", encoding="utf-8") as f:    # append at the end
    f.write("more\n")

# modes: r read | w write (erase) | a append | x new only | b binary | + read+write
```

```python
import os
os.path.exists(p); os.path.isfile(p); os.path.getsize(p)
os.remove(p); os.rename(a, b); os.listdir(folder)
os.makedirs(folder, exist_ok=True); os.path.join("data", "a.txt")
```

```python
# CSV: always newline=""
import csv
with open("p.csv", "w", newline="", encoding="utf-8") as f:
    w = csv.writer(f); w.writerow(["name", "age"]); w.writerows(rows)
with open("p.csv", newline="", encoding="utf-8") as f:
    for row in csv.reader(f): ...          # lists of strings
    # csv.DictReader(f) -> dicts;  csv.DictWriter(f, fieldnames=[...])

# JSON
import json
text = json.dumps(data, indent=4)          # Python -> string
data = json.loads(text)                    # string -> Python
with open("d.json", "w", encoding="utf-8") as f:
    json.dump(data, f, indent=4)           # Python -> file
with open("d.json", encoding="utf-8") as f:
    data = json.load(f)                    # file -> Python
```

| JSON | Python |
|---|---|
| object / array | dict / list |
| string / number | str / int, float |
| true / false / null | True / False / None |

**Key points:** use `with` so the file always closes. `"w"` erases the file. `write()` needs a string and does not add `\n`. Always give `encoding="utf-8"`. CSV values are strings. JSON turns tuples into lists and keys into strings. Catch `FileNotFoundError` for missing files.

---

---

## Object-Oriented Programming

```python
class Student:
    school = "Green Valley"                  # class attribute (shared)
    count = 0

    def __init__(self, name, marks=0):       # constructor
        self.name = name                     # instance attributes
        self.marks = marks
        Student.count += 1

    def grade(self):                         # instance method
        return "Pass" if self.marks >= 40 else "Fail"

    @classmethod
    def from_string(cls, text):              # alternative constructor
        name, marks = text.split("-")
        return cls(name, int(marks))

    @staticmethod
    def is_valid(marks):                     # needs neither self nor cls
        return 0 <= marks <= 100

    @property
    def result(self):                        # read like an attribute
        return f"{self.name}: {self.grade()}"

    def __str__(self):  return self.name                 # print(obj)
    def __repr__(self): return f"Student({self.name!r})" # debugging

s = Student("Riya", 85)
```

```python
class Dog(Animal):                           # inheritance
    def __init__(self, name, breed):
        super().__init__(name)               # run the parent's __init__
        self.breed = breed
    def sound(self):                         # overriding
        return "Woof"

isinstance(d, Animal); issubclass(Dog, Animal); Dog.__mro__

self._x      # protected (convention)       self.__x   # name-mangled (_Class__x)

@property
def balance(self): return self._balance
@balance.setter
def balance(self, v): self._balance = v

from abc import ABC, abstractmethod          # abstraction
class Shape(ABC):
    @abstractmethod
    def area(self): ...
```

| Dunder | Triggered by | Dunder | Triggered by |
|---|---|---|---|
| `__init__` | `Cls(...)` | `__add__` | `a + b` |
| `__str__` | `print(x)`, `str(x)` | `__eq__` | `a == b` |
| `__repr__` | shell, lists, `repr(x)` | `__lt__` | `a < b`, `sorted()` |
| `__len__` | `len(x)` | `__getitem__` | `x[i]` |
| `__iter__` / `__next__` | `for ... in x` | `__call__` | `x(...)` |
| `__enter__` / `__exit__` | `with x:` | `__hash__` | `set`, dict keys |

**Key points:** a class is the blueprint, an object is the thing built from it. `self` is the object itself. Instance attributes belong to one object, class attributes are shared. A child calls `super().__init__()`. Python has no real private data (`__x` is only mangled). Polymorphism: same method name, different behaviour (duck typing). Abstract classes cannot be instantiated. Use inheritance for "is a" and composition for "has a".

---

---

- [Back to section index](./README.md)
