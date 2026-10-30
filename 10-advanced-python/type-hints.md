# Type Hints - Revision Notes ~ sm

> **In one line:** **Type hints** (also called type annotations) are labels you write in your code to say **what type a variable, parameter or return value should have**, such as `def add(a: int, b: int) -> int:`. Python **does not enforce them when the program runs**; they help you, your editor and tools like **mypy** find mistakes early.

## Table of Contents
- [Why Type Hints?](#why-type-hints)
- [Hints Do Not Change How the Program Runs](#hints-do-not-change-how-the-program-runs)
- [Function Hints](#function-hints)
- [Variable Hints](#variable-hints)
- [Collections](#collections)
- [Optional Values and Unions](#optional-values-and-unions)
- [Any](#any)
- [Callable](#callable)
- [Type Aliases](#type-aliases)
- [Hints for Your Own Classes](#hints-for-your-own-classes)
- [Literal, Final and TypedDict](#literal-final-and-typeddict)
- [Generics and TypeVar](#generics-and-typevar)
- [Looking at Annotations at Run Time](#looking-at-annotations-at-run-time)
- [Checking with a Tool (mypy)](#checking-with-a-tool-mypy)
- [Forward References and Python Versions](#forward-references-and-python-versions)
- [Best Practices](#best-practices)
- [Common Mistakes](#common-mistakes)

---

## Why Type Hints?

Python decides types while running, so this mistake is found only when the line runs:

```python
def double(n):
    return n * 2

print(double("ab"))      # no error, but is "abab" what you wanted?
# Output: abab
```
Type hints make the **intention** clear:

| Benefit | Meaning |
|---|---|
| **Documentation** | The signature tells you what goes in and comes out |
| **Editor help** | Better auto-complete, and warnings while you type |
| **Bug finding** | A type checker finds wrong calls **before** you run the program |
| **Easier teamwork** | Others understand your functions faster |

---

## Hints Do Not Change How the Program Runs

```python
def add(a: int, b: int) -> int:
    return a + b

print(add(2, 3))
print(add("x", "y"))      # wrong type, but Python does not stop it
# Output: 5
# Output: xy
```
Python **ignores** the hints at run time. A checker such as mypy would warn about the second call, but the program still runs. If you must reject bad values while running, use `isinstance` and raise `TypeError`.

---

## Function Hints

Syntax: `parameter: type` and `-> return_type`.

```python
def greet(name: str, times: int = 1) -> str:
    return ("Hello, " + name + "! ") * times

print(greet("Riya", 2))
# Output: Hello, Riya! Hello, Riya!
```

| Part | Meaning |
|---|---|
| `name: str` | `name` should be a string |
| `times: int = 1` | an `int`, with default `1` (spaces around `=` when there is a hint) |
| `-> str` | the function returns a string |
| `-> None` | the function returns nothing |

```python
def show(items: list) -> None:
    for item in items:
        print(item)
```

---

## Variable Hints

```python
age: int = 20
name: str = "Riya"
price: float = 9.99
active: bool = True
nothing: None = None
```
A hint without a value only **declares** the name (it does not create the variable):
```python
count: int
print("count" in globals())
# Output: False
```
For simple assignments the type is usually **inferred**, so `age = 20` needs no hint. Add hints when they add clarity.

---

## Collections

Since Python **3.9** you can use the built-in names with square brackets.

```python
scores: list[int] = [90, 85]
names: set[str] = {"Riya", "Amit"}
point: tuple[int, int] = (3, 4)
marks: dict[str, int] = {"maths": 90}
```

| Hint | Meaning |
|---|---|
| `list[int]` | list of ints |
| `dict[str, float]` | keys are `str`, values are `float` |
| `set[str]` | set of strings |
| `tuple[int, str]` | a tuple with exactly two items: an `int` then a `str` |
| `tuple[int, ...]` | a tuple of any length, all `int` |
| `list[list[int]]` | list of lists of ints |

```python
def average(nums: list[float]) -> float:
    return sum(nums) / len(nums)

print(average([1.0, 2.0, 6.0]))
# Output: 3.0
```
Older code uses `from typing import List, Dict, Tuple`. Those still work but are no longer needed.

For **inputs** that can be any list-like or iterable thing, hint with the general types from `collections.abc`: `Iterable[int]`, `Sequence[str]`, `Mapping[str, int]`.

```python
from collections.abc import Iterable

def total(nums: Iterable[int]) -> int:
    return sum(nums)

print(total([1, 2, 3]), total(range(4)), total((5, 5)))
# Output: 6 6 10
```

---

## Optional Values and Unions

A value that can be **one of several types** uses `|` (Python **3.10+**).

```python
def to_number(text: str) -> int | None:
    return int(text) if text.isdigit() else None

print(to_number("42"))
print(to_number("abc"))
# Output: 42
# Output: None
```

| Hint | Meaning |
|---|---|
| `int \| str` | an `int` **or** a `str` |
| `str \| None` | a string or `None` (an "optional" value) |
| `Optional[str]` | the older way to write `str \| None` |
| `Union[int, str]` | the older way to write `int \| str` |

`Optional` means "may be `None`". It does **not** mean "the argument can be left out". A parameter that can be omitted needs a **default value**:

```python
def hello(name: str | None = None) -> str:
    return "Hello, " + (name or "stranger")

print(hello())
print(hello("Riya"))
# Output: Hello, stranger
# Output: Hello, Riya
```

---

## Any

`Any` switches type checking **off** for a value: anything is allowed.

```python
from typing import Any

def show(value: Any) -> None:
    print(value)
```
Use it sparingly. A more precise hint finds more bugs.

---

## Callable

Functions can be passed around, so there is a hint for them: `Callable[[argument types], return type]`.

```python
from collections.abc import Callable

def apply(func: Callable[[int, int], int], a: int, b: int) -> int:
    return func(a, b)

print(apply(lambda x, y: x + y, 2, 3))
# Output: 5
```
`Callable[[int, int], int]` means "a function that takes two ints and returns an int".

---

## Type Aliases

Give a long hint a **short name**.

```python
Point = tuple[float, float]

def distance(a: Point, b: Point) -> float:
    return ((a[0] - b[0]) ** 2 + (a[1] - b[1]) ** 2) ** 0.5

print(distance((0, 0), (3, 4)))
# Output: 5.0
```
Python 3.12 added a dedicated statement: `type Point = tuple[float, float]`.

---

## Hints for Your Own Classes

A class name is a type.

```python
class Dog:
    def __init__(self, name: str) -> None:
        self.name = name

    def rename(self, new_name: str) -> "Dog":
        self.name = new_name
        return self

def adopt(pets: list[Dog]) -> Dog:
    return pets[0]

print(adopt([Dog("Tommy")]).name)
# Output: Tommy
```
Inside the class itself the class name is not yet defined, so older Python versions need it **in quotes** (`"Dog"`), a **forward reference** (see below).

A `@dataclass` uses hints to define its fields:
```python
from dataclasses import dataclass

@dataclass
class Student:
    name: str
    marks: int = 0

print(Student("Riya", 90))
# Output: Student(name='Riya', marks=90)
```

---

## Literal, Final and TypedDict

```python
from typing import Literal, Final, TypedDict

def set_mode(mode: Literal["r", "w", "a"]) -> None:   # only these exact values
    print("mode", mode)

MAX_USERS: Final = 100                                  # should never be reassigned

class Movie(TypedDict):                                 # a dict with fixed keys
    title: str
    year: int

m: Movie = {"title": "Dune", "year": 2021}
set_mode("r")
print(m["title"], MAX_USERS)
# Output: mode r
# Output: Dune 100
```
All three are only **checked by tools**. `TypedDict` objects are ordinary dictionaries at run time.

---

## Generics and TypeVar

A function that works with **any** type but keeps the types consistent uses a **type variable**.

```python
from typing import TypeVar

T = TypeVar("T")

def first(items: list[T]) -> T:
    return items[0]

print(first([10, 20]))
print(first(["a", "b"]))
# Output: 10
# Output: a
```
For `first([10, 20])` a checker knows the result is an `int`; for strings, a `str`. Python **3.12** has a shorter syntax: `def first[T](items: list[T]) -> T:`.

---

## Looking at Annotations at Run Time

Hints are stored on the function or class. Tools such as dataclasses and validation libraries read them.

```python
def add(a: int, b: int = 0) -> int:
    return a + b

print(add.__annotations__)
# Output: {'a': <class 'int'>, 'b': <class 'int'>, 'return': <class 'int'>}
```
```python
import typing

print(typing.get_type_hints(add)["return"])
# Output: <class 'int'>
```
(`typing.get_type_hints` or, in newer Python, `annotationlib.get_annotations` are the recommended ways to read them.)

---

## Checking with a Tool (mypy)

A **type checker** reads your hints and reports wrong usage **without running** the program. The common ones are **mypy** and **pyright**.

```
python -m pip install mypy
python -m mypy program.py
```
For this program:
```python
def add(a: int, b: int) -> int:
    return a + b

add("x", "y")
```
mypy reports an error such as:
```
error: Argument 1 to "add" has incompatible type "str"; expected "int"
```
The wording varies between versions. Many editors (VS Code with Pylance, PyCharm) show the same warnings as you type.

---

## Forward References and Python Versions

A **forward reference** is a hint for a name that is not defined yet (for example a class mentioning itself).

| Python | What to do |
|---|---|
| Up to 3.13 | Put the name **in quotes** (`"Dog"`), or add `from __future__ import annotations` at the top of the file |
| **3.14 and newer** | Annotations are evaluated **lazily** ("deferred"), so quotes are no longer needed |

```python
class Node:
    def __init__(self, value: int, next: "Node | None" = None) -> None:
        self.value = value
        self.next = next

n = Node(1, Node(2))
print(n.next.value)
# Output: 2
```
The quotes work in **every** version, so they are the safe choice if you are not sure which Python you will use.

---

## Best Practices

- Hint the **parameters and return values** of functions first. That is where it pays off most.
- Use the **most general** type for inputs (`Iterable`, `Sequence`, `Mapping`) and a **specific** type for outputs (`list[int]`).
- Avoid `Any` where you can.
- Use `X | None` for values that may be missing, and check for `None` before use.
- Do not mix hints and reality: wrong hints are worse than none.
- Be consistent in a project, and run a checker (mypy or pyright) in your editor or in automated tests.
- Hints are **not** validation. Check input yourself where it matters.

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Expecting Python to reject a wrong type | It does not; hints are ignored at run time | Use `isinstance` and `raise TypeError`, or a type checker |
| `list[int]` in Python 3.8 | `TypeError: 'type' object is not subscriptable` | Use Python 3.9+ or `typing.List[int]` |
| `int \| None` in Python 3.9 | `TypeError: unsupported operand type(s) for \|` | Python 3.10+ or `Optional[int]` |
| Treating `Optional` as "may be left out" | A required argument stays required | Give a default: `x: int \| None = None` |
| Using `list` for a list of known items | Checker cannot see the item type | `list[int]` |
| `def f(x: int = None)` | Hint says `int` but the default is `None` | `x: int \| None = None` |
| Class used in its own hints without quotes (Python up to 3.13) | `NameError` | `"ClassName"` or `from __future__ import annotations` |
| Forgetting `-> None` on functions that return nothing | Not an error, but unclear | Add `-> None` |
| Writing hints and never running a checker | Wrong hints go unnoticed | Run mypy or pyright |

---

- [Back to section index](./README.md)
