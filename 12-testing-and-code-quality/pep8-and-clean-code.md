# PEP 8 and Clean Code - Revision Notes ~ sm

> **In one line:** **PEP 8** is Python's official **style guide**: agreed rules for layout, naming and formatting so that all Python code looks familiar. **Clean code** goes further: writing code that other people (and you in six months) can **read, understand and change easily**. Tools such as **black**, **ruff**, **flake8** and **pycodestyle** check and fix style automatically.

## Table of Contents
- [Why Style Matters](#why-style-matters)
- [Layout: Indentation, Line Length, Blank Lines](#layout-indentation-line-length-blank-lines)
- [Imports](#imports)
- [Whitespace](#whitespace)
- [Naming Conventions](#naming-conventions)
- [Comments and Docstrings](#comments-and-docstrings)
- [Programming Recommendations](#programming-recommendations)
- [Clean Code Principles](#clean-code-principles)
- [Refactoring Examples](#refactoring-examples)
- [Tools That Check and Fix Style](#tools-that-check-and-fix-style)
- [The Zen of Python](#the-zen-of-python)
- [Checklist](#checklist)
- [Common Mistakes](#common-mistakes)

---

## Why Style Matters

Code is **read far more often than it is written**. Consistent style means:
- you spot real problems instead of fighting odd formatting,
- teams can read each other's code,
- editors and tools can format it automatically,
- fewer silly bugs (a wrong indentation or a variable name `l` that looks like `1`).

PEP 8 itself says: *"A style guide is about consistency. Consistency with this style guide is important. Consistency within a project is more important."* Follow the project's existing style first, PEP 8 second.

---

## Layout: Indentation, Line Length, Blank Lines

**Indentation: 4 spaces per level.** Never mix tabs and spaces (Python 3 rejects the mix with `TabError`).

```python
def check(age):
    if age >= 18:
        return "adult"
    return "minor"

print(check(20))
# Output: adult
```

**Line length: 79 characters** for code, **72** for comments and docstrings (PEP 8). Many teams agree on a longer limit (black and ruff default to **88**). Pick one and keep it.

**Breaking long lines**: use the brackets you already have. Python continues a line automatically inside `()`, `[]` and `{}`. Avoid the backslash `\` where possible.

```text
# aligned with the opening bracket
total = calculate_price(base_price, tax_rate,
                        discount, shipping)

# hanging indent: nothing after the opening bracket, extra indent on the next lines
total = calculate_price(
    base_price,
    tax_rate,
    discount,
    shipping,
)

# long condition: wrap it in brackets
if (age >= 18
        and has_id
        and not banned):
    allow()
```
A **trailing comma** after the last item of a multi-line list or call keeps changes tidy (one line per change in version control).

**Breaking before or after an operator?** PEP 8 recommends breaking **before** the operator in new code, so the operators line up:
```text
income = (salary
          + bonus
          - tax)
```
**Blank lines**

| Where | How many |
|---|---|
| Around top-level functions and classes | **2** |
| Between methods inside a class | **1** |
| Inside a function, to separate steps | Sparingly, **1** |

**Other layout rules**
- One statement per line (no `a = 1; b = 2`, no `if x: return`).
- Files end with **one** newline. Use UTF-8.
- Imports go at the **top** of the file.

---

## Imports

```text
import os
import sys
from pathlib import Path

import requests          # third-party

from myapp import utils  # your own project
```
| Rule | Detail |
|---|---|
| **One import per line** | `import os` and `import sys`, not `import os, sys` |
| `from x import a, b` is fine | Several names from one module on one line |
| **Three groups**, in this order, with a blank line between | 1. standard library, 2. third-party packages, 3. your own modules |
| Alphabetical within a group | Easier to find |
| Prefer **absolute** imports | `from app.utils import helper` |
| **Avoid `from x import *`** | You cannot tell where a name came from, and names can clash |
| Imports at the **top** | After the module docstring, before the code |

Unused imports are clutter: remove them (tools find them for you).

---

## Whitespace

**Avoid extra spaces** inside brackets and before commas or colons:

| Wrong | Right |
|---|---|
| `spam( ham[ 1 ], { eggs: 2 } )` | `spam(ham[1], {eggs: 2})` |
| `x = (1 , 2)` | `x = (1, 2)` |
| `if x == 4 : print(x)` | `if x == 4:` (new line) `print(x)` |
| `d ['key']`, `f (x)` | `d['key']`, `f(x)` |

**One space around operators**, none for keyword arguments:

| Wrong | Right |
|---|---|
| `x=1`, `y = x*2+1` | `x = 1`, `y = x * 2 + 1` |
| `def f(a, b = 2):` | `def f(a, b=2):` |
| `f(a = 1)` | `f(a=1)` |
| `def f(a: int = 2) -> int:` (with type hints, spaces around `=` **are** right) | |

When operators have different priority, spacing can show it: `c = (a+b) * (a-b)` and `x = x*2 - 1` are fine. Do not add spaces **only** to line things up in columns.

**Trailing whitespace** at the end of a line is an error. Your editor can remove it automatically.

---

## Naming Conventions

| What | Style | Example |
|---|---|---|
| Variables, functions, methods | `snake_case` | `total_price`, `get_name()` |
| Constants | `UPPER_SNAKE_CASE` | `MAX_RETRIES = 3` |
| Classes | `CapWords` (also called PascalCase) | `ShoppingCart` |
| Exceptions | `CapWords` ending in `Error` | `InsufficientFundsError` |
| Modules (files) | short `lowercase`, underscores allowed | `file_utils.py` |
| Packages (folders) | short `lowercase`, preferably no underscores | `mypackage` |
| "Internal use" name | one leading underscore | `_helper`, `self._cache` |
| Name-mangled attribute | two leading underscores | `self.__secret` |
| Avoid a keyword clash | one trailing underscore | `class_`, `type_` |
| Special ("dunder") methods | two underscores both sides, only the ones Python defines | `__init__`, `__str__` |
| Type variables | `CapWords`, short | `T` |

**Never use `l` (lowercase L), `O` or `I` as single-letter names**: they look like `1` and `0`.

**Names should be descriptive**
```python
# bad
def f(x, y):
    return x * y / 100

# good
def percentage_of(amount, percent):
    return amount * percent / 100

print(percentage_of(200, 15))
# Output: 30.0
```
| Guideline | Example |
|---|---|
| Say **what it is**, not how it is stored | `students`, not `student_list_array` |
| Functions are **verbs**: they do something | `calculate_tax`, `send_email`, `is_valid` |
| Booleans read like a question | `is_empty`, `has_permission`, `can_retry` |
| Variables and classes are **nouns** | `invoice`, `Customer` |
| Be consistent | Do not mix `get_user`, `fetch_customer`, `retrieve_account` for the same idea |
| Single letters only for tiny scopes | `i`, `j` in a loop, `x`, `y` for coordinates, `e` for an exception |
| Do not shadow built-ins | Avoid `list`, `str`, `sum`, `max`, `id`, `input` as names |
| Avoid abbreviations nobody knows | `customer_count`, not `cust_cnt` |

---

## Comments and Docstrings

**Comments explain WHY, not WHAT.** The code already shows what it does.

```text
# bad: repeats the code
x = x + 1      # add 1 to x

# good: gives the reason
retries += 1   # the server sometimes drops the first request
```
| Rule | Detail |
|---|---|
| Block comments | Start with `# `, complete sentences, indented like the code |
| Inline comments | At least **two spaces** after the code, then `# text`. Use rarely |
| Keep comments **up to date** | A wrong comment is worse than none |
| Do not comment out old code for later | Delete it; version control (git) remembers |
| `TODO:` and `FIXME:` | Fine for marked work, with a short note |

**Docstrings** (PEP 257) describe a **module, function, class or method**. They are written in **triple double quotes** as the first statement, and are available through `help()` and `__doc__`.

```python
def area(width, height):
    """Return the area of a rectangle."""
    return width * height

print(area.__doc__)
# Output: Return the area of a rectangle.
```
A **multi-line** docstring has a one-line summary, a blank line, then the details:
```python
def withdraw(balance, amount):
    """Return the balance after taking out `amount`.

    Args:
        balance: Money currently in the account.
        amount: Money to take out. Must not be more than the balance.

    Returns:
        The new balance.

    Raises:
        ValueError: If `amount` is negative or more than the balance.
    """
    if amount < 0 or amount > balance:
        raise ValueError("invalid amount")
    return balance - amount

print(withdraw(100, 30))
# Output: 70
```
The `Args / Returns / Raises` layout is the **Google style**. Other common styles are **NumPy style** and **reStructuredText (Sphinx)**. Choose one and use it everywhere in the project.

| Docstring rule | Detail |
|---|---|
| Summary line | A command ("Return ...", "Calculate ..."), ends with a full stop, fits on one line |
| Document **what** it does, the **arguments**, the **result** and **errors raised** | Not how it works inside |
| Classes | Describe the class; document public methods |
| Modules | A docstring at the top describing the file |

Type hints (see the Type Hints note) replace the **types** in docstrings: write `def area(width: float, height: float) -> float:` and describe only the meaning.

---

## Programming Recommendations

These are from the PEP 8 section "Programming Recommendations".

| Recommendation | Instead of | Write |
|---|---|---|
| Compare to `None` with `is` | `if x == None:` | `if x is None:` |
| Test emptiness directly | `if len(items) == 0:` | `if not items:` |
| Do not compare booleans to `True` / `False` | `if done == True:` | `if done:` |
| Use `is not`, `not in` | `if not x is None:`, `if not a in b:` | `if x is not None:`, `if a not in b:` |
| Use `isinstance` | `if type(x) == int:` | `if isinstance(x, int):` |
| Use `startswith` / `endswith` | `if s[:3] == "abc":` | `if s.startswith("abc"):` |
| Use a `def`, not an assigned lambda | `square = lambda n: n * n` | `def square(n): return n * n` (on separate lines) |
| Catch specific exceptions | `except:` | `except ValueError:` |
| Keep the `try` body small | Many lines in `try` | Only the line that can fail |
| Use `with` for files and locks | `f = open(...)` ... `f.close()` | `with open(...) as f:` |
| Be consistent with `return` | Some paths return a value, some fall off the end | Every path returns a value (or none do) |
| Do not compare using `==` for singletons | `x == None` | `x is None` |
| Use `enumerate` | `for i in range(len(items)):` | `for i, item in enumerate(items):` |
| Use `"".join()` to build strings in a loop | `s += piece` repeatedly | `"".join(pieces)` |

```python
items = []
name = "Riya"

if not items:
    print("no items")
if name.startswith("Ri"):
    print("starts with Ri")
if isinstance(name, str) and name is not None:
    print("a string")
# Output: no items
# Output: starts with Ri
# Output: a string
```

---

## Clean Code Principles

| Principle | Meaning |
|---|---|
| **Readability first** | Write for the next reader, not to be clever |
| **KISS** | *Keep It Simple*: choose the simplest solution that works |
| **DRY** | *Don't Repeat Yourself*: one piece of logic, in one place |
| **YAGNI** | *You Aren't Gonna Need It*: do not build features "just in case" |
| **Single responsibility** | A function (or class) does **one** thing, and its name says what |
| **Small functions** | Aim for a function that fits on one screen and has few parameters |
| **No magic numbers** | Give meaning to numbers with named constants |
| **Early return (guard clauses)** | Handle the unusual cases first, so the main code is not deeply nested |
| **Avoid deep nesting** | More than 3 levels is a sign to split or return early |
| **Avoid global state** | Pass values in, return values out |
| **Explicit is better than implicit** | Clear beats short |
| **Prefer the standard library** | `Counter`, `defaultdict`, `pathlib` already solve common jobs |
| **Fail loudly** | Raise a clear exception instead of continuing with bad data |
| **Test it** | Code you can test is usually cleaner (see Unit Testing) |

---

## Refactoring Examples

**Refactoring** = improving the structure of code **without changing what it does**. Each example below shows the same behaviour before and after.

**1. Names and the `range(len())` habit**
```python
# before
def f(l):
    r = []
    for i in range(len(l)):
        if l[i] % 2 == 0:
            r.append(l[i] * l[i])
    return r

# after
def squares_of_evens(numbers):
    return [n * n for n in numbers if n % 2 == 0]

data = [1, 2, 3, 4]
print(f(data), squares_of_evens(data))
# Output: [4, 16] [4, 16]
```

**2. Magic numbers become constants**
```python
# before
def price_with_tax(price):
    return price * 1.18

# after
GST_RATE = 0.18

def price_with_tax(price):
    return price * (1 + GST_RATE)

print(round(price_with_tax(100), 2))
# Output: 118.0
```

**3. Guard clauses instead of nested `if`**
```python
# before
def discount(age, is_member):
    if age is not None:
        if age >= 0:
            if is_member:
                return 0.2
            else:
                return 0.0
        else:
            return None
    else:
        return None

# after
def discount(age, is_member):
    if age is None or age < 0:
        return None
    return 0.2 if is_member else 0.0

print(discount(30, True), discount(30, False), discount(-1, True), discount(None, True))
# Output: 0.2 0.0 None None
```

**4. Return the condition directly**
```python
# before
def is_adult(age):
    if age >= 18:
        return True
    else:
        return False

# after
def is_adult(age):
    return age >= 18

print(is_adult(20), is_adult(10))
# Output: True False
```

**5. One function, one job**
```python
# before: reads, calculates and prints in one place
def report(marks):
    total = 0
    for m in marks:
        total += m
    avg = total / len(marks)
    if avg >= 40:
        print("average", avg, "pass")
    else:
        print("average", avg, "fail")

# after: separate the calculation from the printing
def average(marks):
    return sum(marks) / len(marks)

def result(avg, pass_mark=40):
    return "pass" if avg >= pass_mark else "fail"

def report(marks):
    avg = average(marks)
    print("average", avg, result(avg))

report([50, 60, 70])
report([10, 20])
# Output: average 60.0 pass
# Output: average 15.0 fail
```
The small functions `average` and `result` can now be **tested** and **reused** on their own.

**6. Remove repetition (DRY)**
```python
# before
def describe_circle(r):
    return "area=" + str(round(3.14159 * r * r, 2)) + " perimeter=" + str(round(2 * 3.14159 * r, 2))

# after
import math

def describe_circle(radius):
    area = math.pi * radius ** 2
    perimeter = 2 * math.pi * radius
    return f"area={area:.2f} perimeter={perimeter:.2f}"

print(describe_circle(2))
# Output: area=12.57 perimeter=12.57
```
(Using `math.pi` is also more accurate than typing `3.14159`.)

**7. Use the right tool: `Counter`, `enumerate`, `zip`, `with`**
```python
# before
words = ["a", "b", "a", "c", "a"]
counts = {}
for w in words:
    if w in counts:
        counts[w] = counts[w] + 1
    else:
        counts[w] = 1

# after
from collections import Counter
counts2 = Counter(words)

print(counts == dict(counts2))
# Output: True
```

**8. Comments that repeat the code vs. code that explains itself**
```python
# before
def check(a):
    # check if a is greater than 17 and less than 65
    if a > 17 and a < 65:
        return True
    return False

# after
MIN_WORKING_AGE = 18
RETIREMENT_AGE = 65

def is_working_age(age):
    return MIN_WORKING_AGE <= age < RETIREMENT_AGE

print(check(30), is_working_age(30), is_working_age(70))
# Output: True True False
```
The after-version needs no comment; the names say it, and the chained comparison `18 <= age < 65` reads like maths.

---

## Tools That Check and Fix Style

Do not rely on remembering all the rules: let tools do it.

| Tool | Does | Install |
|---|---|---|
| **pycodestyle** | Checks code against PEP 8 | `python -m pip install pycodestyle` |
| **pyflakes** | Finds unused imports and variables, undefined names | `python -m pip install pyflakes` |
| **flake8** | Runs pycodestyle + pyflakes (+ complexity check) in one command | `python -m pip install flake8` |
| **pylint** | A stricter checker with a score and design hints | `python -m pip install pylint` |
| **black** | An **automatic formatter**: rewrites your file in one consistent style | `python -m pip install black` |
| **ruff** | A very fast linter **and** formatter that replaces flake8, isort and more | `python -m pip install ruff` |
| **isort** | Sorts imports (ruff can do it too) | `python -m pip install isort` |
| **mypy** / **pyright** | Check type hints | `python -m pip install mypy` |

A **linter** reports problems; a **formatter** fixes the layout for you.

**Example.** This file, `messy.py`, works but breaks many rules:
```text
import os, sys
from math import *

def CalcTotal( items,tax = 0.18 ):
    l = []
    for i in range(len(items)):
        l.append(items[i]*(1+tax))
    if l == None:
        return 0
    x=sum(l)
    return x
class shoppingCart:
    def __init__(self):
        self.Items=[]
    def add(self,item): self.Items.append(item)
square = lambda n: n*n
try:
    pass
except:
    pass
```
`python -m pycodestyle messy.py` reports:
```text
messy.py:1:10: E401 multiple imports on one line
messy.py:4:1: E302 expected 2 blank lines, found 1
messy.py:4:15: E201 whitespace after '('
messy.py:4:21: E231 missing whitespace after ','
messy.py:4:25: E251 unexpected spaces around keyword / parameter equals
messy.py:4:27: E251 unexpected spaces around keyword / parameter equals
messy.py:4:32: E202 whitespace before ')'
messy.py:5:5: E741 ambiguous variable name 'l'
messy.py:8:10: E711 comparison to None should be 'if cond is None:'
messy.py:10:6: E225 missing whitespace around operator
messy.py:12:1: E302 expected 2 blank lines, found 0
messy.py:14:19: E225 missing whitespace around operator
messy.py:15:5: E301 expected 1 blank line, found 0
messy.py:15:17: E231 missing whitespace after ','
messy.py:16:1: E305 expected 2 blank lines after class or function definition, found 0
messy.py:16:1: E731 do not assign a lambda expression, use a def
messy.py:19:1: E722 do not use bare 'except'
```
Each line is `file:line:column: CODE description`. `python -m pyflakes messy.py` adds what style checks cannot see:
```text
messy.py:1:1: 'os' imported but unused
messy.py:1:1: 'sys' imported but unused
messy.py:2:1: 'from math import *' used; unable to detect undefined names
messy.py:2:1: 'math.*' imported but unused
```
`python -m black messy.py` then **rewrites the layout** (a `--diff` shows the changes without saving):
```text
-def CalcTotal( items,tax = 0.18 ):
+
+
+def CalcTotal(items, tax=0.18):
     l = []
     for i in range(len(items)):
-        l.append(items[i]*(1+tax))
+        l.append(items[i] * (1 + tax))
...
-    x=sum(l)
+    x = sum(l)
```
A formatter fixes **layout** only. It does not rename `CalcTotal`, `l` or `shoppingCart`, remove the unused imports, change `== None` to `is None`, or replace the bare `except`. Those need you (or `ruff check --fix`, which can remove unused imports).

**Common pycodestyle and pyflakes codes**

| Code | Meaning |
|---|---|
| `E101`, `W191` | Mixed tabs and spaces / tab indentation |
| `E111` | Indentation is not a multiple of four |
| `E201`, `E202` | Space after `(` / before `)` |
| `E225` | Missing space around an operator |
| `E231` | Missing space after `,` |
| `E251` | Spaces around `=` in a keyword argument |
| `E301`, `E302`, `E303`, `E305` | Wrong number of blank lines |
| `E401` | Several imports on one line |
| `E501` | Line too long |
| `E701` | Several statements on one line (colon) |
| `E711`, `E712` | Comparison to `None` / `True` with `==` |
| `E713` | Use `not in` |
| `E722` | Bare `except` |
| `E731` | Lambda assigned to a name |
| `E741` | Ambiguous variable name (`l`, `O`, `I`) |
| `W291`, `W293` | Trailing whitespace |
| `W292`, `W391` | Missing newline at end / blank line at end |
| `F401` | Imported but unused |
| `F841` | Local variable assigned but never used |
| `F811` | Redefinition of an unused name |
| `F821` | Undefined name |

**Settings** go in `pyproject.toml`, for example:
```text
[tool.black]
line-length = 88

[tool.ruff]
line-length = 88
```
**Make it automatic**: run the formatter on save in your editor, and run the checkers in a **pre-commit** hook or in your project's automated checks, so style problems never reach the main branch.

The usual workflow: **format** (black or `ruff format`), **lint** (ruff or flake8), **type-check** (mypy), **test** (pytest).

---

## The Zen of Python

Type `import this` in Python to see **PEP 20**, the guiding ideas. A few of them:

| Line | Meaning for your code |
|---|---|
| Beautiful is better than ugly | Care about how the code looks |
| Explicit is better than implicit | Do not hide what happens |
| Simple is better than complex | Prefer the plain solution |
| Flat is better than nested | Avoid deep indentation |
| Readability counts | The main goal |
| Errors should never pass silently | No `except: pass` |
| In the face of ambiguity, refuse the temptation to guess | Validate input |
| There should be one-- and preferably only one --obvious way to do it | Use the standard idioms |
| If the implementation is hard to explain, it's a bad idea | Simplify |

---

## Checklist

Before you share code, ask:

- Does a tool such as `black` or `ruff format` leave it unchanged?
- Does the linter report nothing (or only things I decided to accept)?
- Does every function, class and module have a short docstring?
- Are the names clear enough that most comments are unnecessary?
- Does each function do **one** thing and fit on a screen?
- Are there no magic numbers, no copy-pasted blocks and no deep nesting?
- Are errors handled with specific exceptions, and no bare `except`?
- Are there tests for the main behaviour and the edge cases?
- Did I remove debug prints, commented-out code and unused imports?

---

## Common Mistakes

| Mistake | Why it is a problem | Fix |
|---|---|---|
| Mixing tabs and spaces | `TabError`, invisible differences | 4 spaces; set the editor to insert spaces |
| `from module import *` | Hidden names and clashes | Import what you need |
| Single-letter or vague names (`l`, `data2`, `temp`) | Unreadable | Descriptive names |
| Naming a variable like a built-in (`list`, `sum`, `max`) | Hides the built-in; later calls break | Pick another name |
| A class named in `snake_case` or a function in `CamelCase` | Misleads readers | `ClassName`, `function_name` |
| Comments that repeat the code | Noise, goes out of date | Explain **why**, or rename things |
| Comments out of date | Misleads | Update or delete them |
| Bare `except:` | Hides every bug | `except SomeError:` |
| Very long functions with several jobs | Hard to test and change | Split into small functions |
| Copy-pasting code | A fix must be made in many places | Put it in one function |
| Deep nesting | Hard to follow | Guard clauses, extract functions |
| Magic numbers and strings | Meaning is hidden | Named constants |
| Lines of 150+ characters | Unreadable on small screens and in diffs | Break them, within brackets |
| Formatting by hand | Time lost, inconsistent | Use black or ruff |
| Debating style for hours | Wasted time | Agree on one formatter and use it |
| Treating PEP 8 as law | Sometimes readability needs an exception | Follow the project's rules first, PEP 8 second |

---

- [Back to section index](./README.md)
