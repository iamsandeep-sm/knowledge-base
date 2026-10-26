# Packages - Revision Notes ~ sm

> **In one line:** A **package** is a **folder of modules**. It lets you organise a big project into groups, and you import from it with dots: `from shop.cart import total`. A normal package folder contains a file named `__init__.py`.

## Table of Contents
- [Module vs Package](#module-vs-package)
- [Creating a Package](#creating-a-package)
- [Importing from a Package](#importing-from-a-package)
- [The __init__.py File](#the-__init__py-file)
- [Absolute and Relative Imports](#absolute-and-relative-imports)
- [Running Modules Inside a Package](#running-modules-inside-a-package)
- [Sub-packages](#sub-packages)
- [__all__ in a Package](#__all__-in-a-package)
- [Namespace Packages](#namespace-packages)
- [A Typical Project Layout](#a-typical-project-layout)
- [Common Mistakes](#common-mistakes)

---

## Module vs Package

| | Module | Package |
|---|---|---|
| What it is | One `.py` file | A **folder** that contains modules |
| Import | `import mymath` | `import shop.prices` |
| Special file | None | `__init__.py` |

---

## Creating a Package

Folder layout:
```
project/
├── app.py
└── shop/
    ├── __init__.py
    ├── prices.py
    └── cart.py
```

```python
# file: shop/__init__.py
print("shop package loaded")
```
```python
# file: shop/prices.py
def with_tax(p, rate=0.18):
    return round(p * (1 + rate), 2)
```
```python
# file: shop/cart.py
from .prices import with_tax

def total(items):
    return sum(with_tax(p) for p in items)
```

---

## Importing from a Package

```python
# file: app.py
from shop import prices
from shop.cart import total
import shop.prices

print(prices.with_tax(100))
print(total([100, 50]))
print(shop.prices.with_tax(10))
```
Run from the `project` folder with `python app.py`:
```
shop package loaded
118.0
177.0
11.8
```

| Statement | How to use the name |
|---|---|
| `import shop.prices` | `shop.prices.with_tax(10)` (full path every time) |
| `from shop import prices` | `prices.with_tax(10)` |
| `from shop.prices import with_tax` | `with_tax(10)` |
| `from shop.prices import with_tax as tax` | `tax(10)` |

Notice that `shop package loaded` printed **once**, the first time anything from `shop` was imported.

---

## The __init__.py File

`__init__.py` runs when the package is first imported, and it **marks the folder as a regular package**. It can be:

- **Empty.** This is fine and very common.
- **A place to shorten imports** for the user of your package:
```python
# file: shop/__init__.py
from .cart import total
from .prices import with_tax
```
Now users can write `from shop import total` instead of `from shop.cart import total`.

- **A place for package-level variables**, like `__version__ = "1.0"`.

Keep it small. Heavy code in `__init__.py` makes imports slow.

---

## Absolute and Relative Imports

Inside a package, a module can import its neighbours in two ways.

| Type | Example (inside `shop/cart.py`) | Meaning |
|---|---|---|
| **Absolute** | `from shop.prices import with_tax` | Full path from the top package |
| **Relative** | `from .prices import with_tax` | Starting from the current package |

| Dots | Meaning |
|---|---|
| `.` | This package (same folder) |
| `..` | The parent package (one folder up) |

```python
from . import prices            # a module in the same package
from .prices import with_tax    # a name from it
from ..utils import helper      # from a sibling of the parent
```
- Relative imports work **only inside packages**.
- Absolute imports are clearer and are the style recommended by PEP 8. Relative imports are fine for large packages with long names.

---

## Running Modules Inside a Package

A module that uses relative imports must **not** be run as a plain script.

```
python shop/cart.py
```
```
ImportError: attempted relative import with no known parent package
```
Run it **as part of the package**, from the `project` folder, with `-m` and dots instead of slashes:
```
python -m shop.cart
```
```
shop package loaded
177.0
```
(That output assumes `shop/cart.py` ends with `if __name__ == "__main__": print(total([100, 50]))`.)

---

## Sub-packages

Packages can contain packages. Each folder gets its own `__init__.py`.

```
shop/
├── __init__.py
├── prices.py
└── payment/
    ├── __init__.py
    └── card.py
```
```python
from shop.payment.card import pay
```

---

## __all__ in a Package

In `__init__.py`, `__all__` lists the module names that `from shop import *` should import.
```python
# file: shop/__init__.py
__all__ = ["prices", "cart"]
```
As with modules, avoid `import *` in real code.

---

## Namespace Packages

A folder **without** `__init__.py` can still be imported. Python treats it as a **namespace package** (a package that can even be spread over several folders).

```
ns/
└── m.py
```
```python
import ns.m
print(ns.m.x)
```
It works, but for your own projects **always add `__init__.py`**: it makes the intention clear, and tools and test runners behave more predictably.

---

## A Typical Project Layout

```
my_project/
├── README.md
├── requirements.txt
├── .gitignore
├── main.py
└── myapp/
    ├── __init__.py
    ├── models.py
    ├── utils.py
    └── services/
        ├── __init__.py
        └── email.py
```
- `main.py` is the starting point and imports from `myapp`.
- Run it from `my_project/` so that Python can find `myapp`.
- Third-party libraries are listed in `requirements.txt` and installed inside a virtual environment (see the pip and Virtual Environments notes).

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Running a file with relative imports directly | `ImportError: attempted relative import with no known parent package` | `python -m package.module` from the project folder |
| Running a command from the wrong folder | `ModuleNotFoundError: No module named 'shop'` | `cd` to the folder that contains `shop/` |
| Folder name with a hyphen (`my-shop/`) | It cannot be imported | Use `my_shop` |
| Forgetting `__init__.py` | Often still works (namespace package), but behaves less predictably | Add an empty `__init__.py` |
| A module and a package with the same name | One hides the other | Use unique names |
| Slashes in `-m`: `python -m shop/cart` | Error | Use dots: `python -m shop.cart` |

---

- [Back to section index](./README.md)
