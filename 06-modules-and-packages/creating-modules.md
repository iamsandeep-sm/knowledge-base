# Creating Modules - Revision Notes ~ sm

> **In one line:** **Any `.py` file is already a module.** To create one, save your functions and variables in a file and `import` it from another file in the same folder. The line `if __name__ == "__main__":` lets one file work both as a program and as a module.

## Table of Contents
- [Your First Module](#your-first-module)
- [Using the Module](#using-the-module)
- [Code Runs When Imported](#code-runs-when-imported)
- [What is __name__?](#what-is-__name__)
- [The if __name__ == "__main__" Block](#the-if-__name__--__main__-block)
- [Running a Module with -m](#running-a-module-with--m)
- [__all__](#__all__)
- [The __pycache__ Folder](#the-__pycache__-folder)
- [Module Docstring and Naming](#module-docstring-and-naming)
- [Common Mistakes](#common-mistakes)

---

## Your First Module

Create a file `mymath.py`:

```python
# file: mymath.py
"""Small maths helpers."""

PI = 3.14159

def add(a, b):
    return a + b

def area(r):
    return PI * r * r
```
That is a complete module. It holds one variable (`PI`) and two functions.

---

## Using the Module

Create `main.py` **in the same folder**:

```python
# file: main.py
import mymath
from mymath import add as plus

print(plus(1, 2))
print(mymath.area(2))
print(mymath.__name__)
print(mymath.__doc__)
```
Run it with `python main.py`:
```
3
12.56636
mymath
Small maths helpers.
```
The module name is the file name **without** `.py`.

---

## Code Runs When Imported

Python runs the **whole module** the first time it is imported. Anything at the top level (like a `print`) executes at that moment.

```python
# file: mymath.py  (with one extra line)
print("mymath loaded, __name__ =", __name__)
```
```
mymath loaded, __name__ = mymath
```
That is usually not what you want from a module that only offers functions. Tests and demo code should be placed inside the `__main__` block (below).

---

## What is __name__?

Every module has a built-in variable `__name__`:

| How the file is used | Value of `__name__` |
|---|---|
| Run directly: `python mymath.py` | `"__main__"` |
| Imported by another file | The module name: `"mymath"` |

Running `python mymath.py` with the `print` line above shows:
```
mymath loaded, __name__ = __main__
```

---

## The if __name__ == "__main__" Block

Code under this `if` runs **only when the file is run directly**, not when it is imported.

```python
# file: mymath.py
"""Small maths helpers."""

PI = 3.14159

def add(a, b):
    return a + b

def area(r):
    return PI * r * r

if __name__ == "__main__":
    print("running directly:", add(2, 3))
```

| Command | Output |
|---|---|
| `python mymath.py` | `running directly: 5` |
| `import mymath` from another file | (nothing printed) |

This is the standard layout of a Python script: put the work inside a `main()` function and call it from this block.

```python
# file: app.py
def main():
    print("Program started")

if __name__ == "__main__":
    main()
```

---

## Running a Module with -m

`python -m modulename` runs a module **by name**, searching `sys.path` like an import does.

```
python -m mymath
```
```
running directly: 5
```
Here `__name__` is also `"__main__"`. The `-m` form matters for modules inside packages (see the Packages note) and for tools such as `python -m pip` and `python -m venv`.

---

## __all__

`__all__` is an optional list of the names that `from module import *` should bring in.

```python
# file: shapes.py
__all__ = ["circle_area"]

def circle_area(r):
    return 3.14159 * r * r

def _helper():          # starts with _ : meant for internal use
    pass
```
- Names that start with an underscore (`_helper`) are **not** imported by `*`, even without `__all__`. It is a convention for "private".
- `__all__` affects only `import *`. You can still write `from shapes import _helper` explicitly.

---

## The __pycache__ Folder

When a module is imported, Python saves a compiled copy (`.pyc`) in a folder named `__pycache__` so that the next import is faster.
- It is created automatically next to your module.
- It is safe to delete. It will be created again.
- Do not put it in version control (add `__pycache__/` to `.gitignore`).
- The script you run directly is **not** cached, only the modules it imports.

---

## Module Docstring and Naming

- Start the file with a **docstring** that says what the module is for. It becomes `module.__doc__`.
- Use short, **lowercase** names, with underscores if needed: `file_utils.py`.
- The name must be a valid identifier: `my-tools.py` and `2fast.py` cannot be imported with a plain `import`.
- Do not use the name of a standard module (`random.py`, `math.py`), because it would hide it (see the Importing Modules note).

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Saving the module in another folder | `ModuleNotFoundError` | Keep it next to the file that imports it (or in a package) |
| Writing `import mymath.py` | `ModuleNotFoundError: No module named 'mymath.py'; 'mymath' is not a package` (the `.py` is not part of the name) | `import mymath` |
| Test code at the top level of a module | It runs on every import | Put it inside `if __name__ == "__main__":` |
| Two modules that import each other | `ImportError` (circular import) | Move shared code to a third module |
| Editing a module and not seeing the change | The program still uses the old import | Restart the program (or `importlib.reload`) |
| `from mymath import *` expecting private names | Names starting with `_` are skipped | Import them by name |

---

- [Back to section index](./README.md)
