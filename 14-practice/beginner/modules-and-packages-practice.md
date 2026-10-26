# Modules and Packages Practice

Related notes: [Importing Modules](../../06-modules-and-packages/importing-modules.md), [Creating Modules](../../06-modules-and-packages/creating-modules.md), [Packages](../../06-modules-and-packages/packages.md), [pip](../../06-modules-and-packages/pip.md), [Virtual Environments](../../06-modules-and-packages/virtual-environments.md)

## Table of Contents
- [Practice Questions](#practice-questions)
- [Practice Answers](#practice-answers)

---

## Practice Questions

1. What is the difference between a module and a package?
2. Import the `math` module and print the square root of 49 in three different ways (`import`, `from ... import`, `import ... as`).
3. Why is `from math import *` discouraged?
4. Use `dir()` to check whether the `math` module has a function called `ceil`.
5. Create a module `greetings.py` with a function `hello(name)`. Import it from `main.py` and call it.
6. What does `if __name__ == "__main__":` do? What is `__name__` when a file is imported?
7. You import the same module twice. How many times does its code run?
8. You created a file `random.py` and `import random` now fails to find `randint`. Why? Fix it.
9. What does `ModuleNotFoundError: No module named 'xyz'` mean? Give two possible causes.
10. Where does Python look for a module you import? How can you print the folders?
11. What is the purpose of `__init__.py`?
12. Given the package below, write the import that gives you `total` from `cart.py`.
    ```
    shop/
    ├── __init__.py
    ├── prices.py
    └── cart.py
    ```
13. In `cart.py` write a relative import of `with_tax` from `prices.py`.
14. Why does `python shop/cart.py` fail with a relative import, and how do you run it correctly?
15. Write the command to check pip's version and which Python it belongs to.
16. Write the commands to install `six` version `1.16.0`, list the installed packages, and uninstall `six`.
17. Why is `python -m pip install ...` safer than `pip install ...`?
18. What is `requirements.txt`? Write the two commands to create it and to install from it.
19. Create a virtual environment named `.venv` and activate it on Windows and on macOS / Linux. How do you know it is active?
20. Why should the `.venv` folder not be copied or put in Git?

---

## Practice Answers

Try each question yourself first, then compare.

**Answer 1**

A **module** is one `.py` file. A **package** is a **folder** of modules (normally with an `__init__.py` file).

**Answer 2**
```python
import math
from math import sqrt
import math as m

print(math.sqrt(49))
print(sqrt(49))
print(m.sqrt(49))
# Output: 7.0
# Output: 7.0
# Output: 7.0
```

**Answer 3**

It imports every public name into your file. You cannot tell where a name came from, and it can silently overwrite your own names or names from other imports.

**Answer 4**
```python
import math
print("ceil" in dir(math))
# Output: True
```

**Answer 5**
```python
# file: greetings.py
def hello(name):
    return f"Hello, {name}!"
```
```python
# file: main.py   (same folder as greetings.py)
import greetings

print(greetings.hello("Riya"))
```
Run `python main.py`:
```
Hello, Riya!
```

**Answer 6**

The code under `if __name__ == "__main__":` runs **only when the file is run directly** (`python file.py`), not when it is imported. When a file is imported, `__name__` is the module's name (for example `"greetings"`); when it is run directly, `__name__` is `"__main__"`.

**Answer 7**

**Once.** The first `import` runs the module and stores it; later imports reuse the stored copy.

**Answer 8**

Your own file comes first in `sys.path`, so `import random` loads **your** `random.py` instead of the standard one. Python reports `AttributeError: module 'random' has no attribute 'randint'`. Rename your file (for example to `dice.py`) and delete the `__pycache__` folder.

**Answer 9**

Python cannot find a module with that name. Causes: the name is misspelled; the package is not installed (`python -m pip install xyz`); it was installed for a different Python; or the file is not in a folder that Python searches.

**Answer 10**

In `sys.modules` (already loaded), then built-in modules, then the folders in `sys.path` (the script's folder, `PYTHONPATH`, the standard library, `site-packages`).
```python
import sys
for folder in sys.path:
    print(folder)
```

**Answer 11**

It marks the folder as a regular **package**, and its code runs the first time the package is imported. It can be empty, or it can shorten imports and set package-level variables.

**Answer 12**
```python
from shop.cart import total
```

**Answer 13**
```python
# inside shop/cart.py
from .prices import with_tax
```

**Answer 14**

Relative imports need Python to know the **package** the file belongs to, and running it as a script hides that (`ImportError: attempted relative import with no known parent package`). Run it as a module from the folder that contains `shop/`:
```
python -m shop.cart
```

**Answer 15**
```
python -m pip --version
```
(`python3` on macOS and Linux.) The text in brackets shows the Python it belongs to.

**Answer 16**
```
python -m pip install six==1.16.0
python -m pip list
python -m pip uninstall six
```

**Answer 17**

`python -m pip` uses the pip that belongs to **that exact `python`**, so the package is installed where that Python will look for it. A plain `pip` may belong to a different Python.

**Answer 18**

`requirements.txt` is a text file that lists the packages (and versions) a project needs.
```
python -m pip freeze > requirements.txt
python -m pip install -r requirements.txt
```

**Answer 19**
```
python -m venv .venv
.venv\Scripts\activate          # Windows (Command Prompt)
.venv\Scripts\Activate.ps1      # Windows (PowerShell)
source .venv/bin/activate       # macOS / Linux
```
When active, the prompt starts with `(.venv)`, and `which python` (`where python` on Windows) shows a path inside `.venv`.

**Answer 20**

It contains paths specific to your computer, can be very large, and can be rebuilt at any time. Share `requirements.txt` instead and add `.venv/` to `.gitignore`.

---

- [Back to practice index](../README.md)
