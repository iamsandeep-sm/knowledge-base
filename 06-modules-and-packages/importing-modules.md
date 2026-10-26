# Importing Modules - Revision Notes ~ sm

> **In one line:** A **module** is a Python file (`.py`) that holds code you can reuse. The `import` statement loads a module so you can use its functions, classes and variables. Python comes with a big **standard library** of ready-made modules (`math`, `random`, `datetime` ...).

## Table of Contents
- [What is a Module?](#what-is-a-module)
- [import module](#import-module)
- [from module import name](#from-module-import-name)
- [import module as alias](#import-module-as-alias)
- [from module import *](#from-module-import-)
- [Which Style to Use](#which-style-to-use)
- [Exploring a Module](#exploring-a-module)
- [Where Python Looks for Modules](#where-python-looks-for-modules)
- [A Module is Loaded Only Once](#a-module-is-loaded-only-once)
- [Order of Imports (PEP 8)](#order-of-imports-pep-8)
- [Import Errors](#import-errors)
- [Do Not Name Your File After a Module](#do-not-name-your-file-after-a-module)

---

## What is a Module?

As programs grow, one file becomes too big. Code is split into several files, and each file is a **module**. Another file can then **import** it.

| Term | Meaning |
|---|---|
| **Module** | One `.py` file |
| **Package** | A folder of modules (see the Packages note) |
| **Standard library** | Modules that come with Python, no install needed |
| **Third-party package** | Modules written by others, installed with `pip` (see the pip note) |

---

## import module

```python
import math

print(math.sqrt(16))
print(math.pi)
# Output: 4.0
# Output: 3.141592653589793
```
- After `import math`, use the module's names with a dot: `math.sqrt`.
- The dot keeps names **organised**: `math.pi` cannot clash with your own variable `pi`.

---

## from module import name

Bring chosen names straight into your file, so no prefix is needed.

```python
from math import sqrt, pi

print(sqrt(25))
print(round(pi, 2))
# Output: 5.0
# Output: 3.14
```
Import several names with commas. Only those names are available (`math.floor` would still be unknown here).

---

## import module as alias

`as` gives a **shorter or clearer name**.

```python
import math as m
from math import factorial as fact

print(m.floor(3.9))
print(fact(5))
# Output: 3
# Output: 120
```
Some aliases are standard conventions in the community (for example `import numpy as np`, `import pandas as pd`). Use those exact aliases for such packages.

---

## from module import *

This imports **every public name** of a module into your file.

```python
from math import *

print(sqrt(16))
# Output: 4.0
```
Avoid it. You cannot tell where a name came from, and it can silently **overwrite** your own names or names from other imports. It is acceptable only in an interactive shell for quick tests.

---

## Which Style to Use

| Style | Use when | Example |
|---|---|---|
| `import module` | Default choice. Clear where each name comes from | `math.sqrt(4)` |
| `from module import name` | You use one or two names often | `sqrt(4)` |
| `import module as alias` | The module name is long | `import datetime as dt` |
| `from module import *` | Avoid | |

---

## Exploring a Module

```python
import math

print("sqrt" in dir(math))
print(math.sqrt.__doc__)
# Output: True
# Output: Return the square root of x.
```

| Tool | What it shows |
|---|---|
| `dir(module)` | All the names inside the module |
| `help(module)` | Documentation of the whole module (press `q` to quit) |
| `module.__name__` | The module's name |
| `module.__doc__` | The module's docstring |
| `module.__file__` | The file the module was loaded from (not available for modules built into Python) |

---

## Where Python Looks for Modules

When you write `import something`, Python searches in this order and uses the first match:

1. **`sys.modules`**: modules already imported (a cache).
2. **Built-in modules** that are part of the interpreter itself.
3. The folders in **`sys.path`**, in order:
   - the folder of the script you ran (so your own files next to it can be imported),
   - folders listed in the `PYTHONPATH` environment variable,
   - the standard library folders,
   - `site-packages` (where `pip` installs packages).

```python
import sys

print(type(sys.path))
print(len(sys.path) > 0)
# Output: <class 'list'>
# Output: True
```
`sys.path` is an ordinary list of folder names, so you can print it to see exactly where Python looks. If the module is not in any of these folders, the import fails.

---

## A Module is Loaded Only Once

The first `import` runs the module's code from top to bottom. Later imports reuse the stored copy.

If `mymath.py` contains `print("mymath loaded")`, then

```python
import mymath
import mymath
```
prints `mymath loaded` **only once**. To load it again while experimenting, use `importlib.reload(mymath)`.

```python
import importlib
import math

print(importlib.reload(math) is math)
# Output: True
```
If you edit a module while a program is running, the running program still uses the old copy until it is reloaded or restarted.

---

## Order of Imports (PEP 8)

Write imports at the **top** of the file, one per line, in three groups separated by a blank line:

```python
# 1. standard library
import os
import sys

# 2. third-party packages (installed with pip)
import requests

# 3. your own modules
import mymath
```
(`requests` is shown only to illustrate the group; it must be installed first.)

---

## Import Errors

| Error | Cause | Fix |
|---|---|---|
| `ModuleNotFoundError: No module named 'xyz'` | Module is not installed, or the name is misspelled, or the file is not on `sys.path` | Check the spelling, `pip install` it, or put the file next to your script |
| `ImportError: cannot import name 'nothing' from 'math'` | The module exists but has no such name (Python may add the file path in brackets) | Check the name with `dir(module)` |
| `AttributeError: module 'math' has no attribute 'nothing'` | `import math` worked, but the name is wrong | Same as above |

```python
import nothing_here
# Error:  ModuleNotFoundError: No module named 'nothing_here'
```

Notes:
- `ModuleNotFoundError` is a kind of `ImportError`.
- A common cause on a computer with several Pythons: the package was installed for **another** Python. Use `python -m pip install ...` so pip and python match (see the pip note).
- Two modules that import **each other** (circular imports) can fail with an `ImportError`. Move the shared code into a third module.

---

## Do Not Name Your File After a Module

Your own folder comes first in `sys.path`, so a file named `random.py` hides the real `random` module.

```
# you created random.py and wrote: import random
AttributeError: module 'random' has no attribute 'randint' (consider renaming '.../random.py' since it has the same name as the standard library module named 'random' ...)
```
Fix: rename your file (and delete the `__pycache__` folder next to it). Avoid names such as `random.py`, `math.py`, `string.py`, `test.py`, `json.py`.

---

- [Back to section index](./README.md)
