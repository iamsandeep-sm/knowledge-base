# Built-in Functions - Revision Notes ~ sm

> **In one line:** **Built-in functions** are functions that Python gives you ready to use, with **no import needed**, such as `print()`, `len()`, `range()`, `sum()` and `sorted()`.

## Table of Contents
- [What are Built-in Functions?](#what-are-built-in-functions)
- [Input, Output and Type](#input-output-and-type)
- [Number Functions](#number-functions)
- [Functions for Collections](#functions-for-collections)
- [Sorting and Reversing](#sorting-and-reversing)
- [enumerate and zip](#enumerate-and-zip)
- [any and all](#any-and-all)
- [map and filter](#map-and-filter)
- [Characters and Number Systems](#characters-and-number-systems)
- [Inspecting Objects](#inspecting-objects)
- [Getting Help](#getting-help)
- [Do Not Shadow Built-ins](#do-not-shadow-built-ins)

---

## What are Built-in Functions?

They are always available because they live in the **built-in scope** (the "B" of LEGB in the Scope note). You can list them:

```python
import builtins
print("len" in dir(builtins))
# Output: True
```
This note covers the ones a beginner uses most. The full list is in the official Python documentation, "Built-in Functions".

---

## Input, Output and Type

| Function | What it does | Example | Result |
|---|---|---|---|
| `print(...)` | Shows values | `print("a", "b")` | `a b` |
| `input(prompt)` | Reads text typed by the user (always `str`) | `input("Name: ")` | the text |
| `type(x)` | Gives the type of `x` | `type(5)` | `<class 'int'>` |
| `isinstance(x, T)` | Is `x` of type `T`? | `isinstance(5, int)` | `True` |
| `int(x)`, `float(x)`, `str(x)`, `bool(x)` | Convert the type | `int("42")` | `42` |
| `list(x)`, `tuple(x)`, `set(x)`, `dict(...)` | Make a collection | `list("abc")` | `['a', 'b', 'c']` |

```python
print(isinstance(5, int))
print(isinstance("5", int))
print(int("42") + 1)
print(float("2.5"))
print(str(10) + "!")
print(bool(0), bool("hi"))
# Output: True
# Output: False
# Output: 43
# Output: 2.5
# Output: 10!
# Output: False True
```

---

## Number Functions

| Function | What it does |
|---|---|
| `abs(x)` | Absolute value (distance from 0) |
| `round(x, n)` | Round to `n` decimal places |
| `pow(a, b)` | `a` to the power `b` (same as `a ** b`) |
| `divmod(a, b)` | Gives `(a // b, a % b)` together |
| `min(...)`, `max(...)` | Smallest and largest |
| `sum(iterable)` | Total of the items |

```python
print(abs(-7))
print(round(3.14159, 2))
print(pow(2, 10))
print(divmod(17, 5))
print(min(4, 9, 2), max(4, 9, 2))
print(sum([1, 2, 3, 4]))
# Output: 7
# Output: 3.14
# Output: 1024
# Output: (3, 2)
# Output: 2 9
# Output: 10
```

> `round()` rounds exactly halves to the **nearest even** number: `round(2.5)` is `2` and `round(3.5)` is `4`. It also gives an `int` when you leave out `n`.

---

## Functions for Collections

| Function | What it does | Works on |
|---|---|---|
| `len(x)` | Number of items | str, list, tuple, dict, set, range |
| `min(x)`, `max(x)` | Smallest / largest item | any iterable of comparable items |
| `sum(x)` | Total | iterable of numbers |
| `range(start, stop, step)` | Sequence of whole numbers (stop is **not** included) | for loops |

```python
nums = [4, 9, 2]
print(len(nums), min(nums), max(nums), sum(nums))
print(len("hello"))
print(list(range(1, 10, 3)))
# Output: 3 2 9 15
# Output: 5
# Output: [1, 4, 7]
```
`min` and `max` also accept a `key` and, for empty input, a `default`:
```python
print(max([], default=0))
print(max(["bb", "a", "ccc"], key=len))
# Output: 0
# Output: ccc
```

---

## Sorting and Reversing

| Function | Returns | Changes the original? |
|---|---|---|
| `sorted(x)` | A **new sorted list** | No |
| `reversed(x)` | A reverse **iterator** (wrap in `list()`) | No |

```python
nums = [3, 1, 2]
print(sorted(nums))
print(sorted(nums, reverse=True))
print(list(reversed(nums)))
print(nums)
# Output: [1, 2, 3]
# Output: [3, 2, 1]
# Output: [2, 1, 3]
# Output: [3, 1, 2]
```
`list.sort()` is the method version: it sorts the list **in place** and returns `None`.

---

## enumerate and zip

**`enumerate`** gives `(index, item)` pairs. Use it instead of `range(len(...))`.
```python
for i, fruit in enumerate(["apple", "kiwi"], start=1):
    print(i, fruit)
# Output: 1 apple
# Output: 2 kiwi
```

**`zip`** walks through several iterables **together**, and stops at the shortest.
```python
names = ["Riya", "Amit"]
marks = [90, 80, 70]
for n, m in zip(names, marks):
    print(n, m)
print(dict(zip(names, marks)))
# Output: Riya 90
# Output: Amit 80
# Output: {'Riya': 90, 'Amit': 80}
```
`zip(..., strict=True)` (Python 3.10+) raises `ValueError` when the lengths differ.

---

## any and all

| Function | `True` when |
|---|---|
| `any(iterable)` | **At least one** item is true |
| `all(iterable)` | **Every** item is true (and `True` for an empty iterable) |

```python
marks = [45, 60, 38]
print(any(m < 40 for m in marks))
print(all(m >= 40 for m in marks))
# Output: True
# Output: False
```

---

## map and filter

```python
nums = ["1", "2", "3"]
print(list(map(int, nums)))
print(list(filter(str.isdigit, ["12", "ab", "7"])))
# Output: [1, 2, 3]
# Output: ['12', '7']
```
Both return iterators, so use `list()` to see the items. A comprehension is often easier to read: `[int(n) for n in nums]`.

---

## Characters and Number Systems

```python
print(ord("A"))
print(chr(97))
print(bin(10), oct(8), hex(255))
print(int("1010", 2))
# Output: 65
# Output: a
# Output: 0b1010 0o10 0xff
# Output: 10
```
`ord` gives the Unicode number of a character; `chr` does the opposite.

---

## Inspecting Objects

| Function | What it does |
|---|---|
| `id(x)` | A number that identifies the object (its identity) |
| `dir(x)` | Lists the names (methods and attributes) of an object |
| `callable(x)` | `True` if `x` can be called like a function |
| `hash(x)` | Hash value of a hashable object |
| `vars(x)` | The `__dict__` of an object |

```python
print(callable(len), callable(5))
print("append" in dir([]))
# Output: True False
# Output: True
```

---

## Getting Help

```python
help(len)          # documentation of len
print(len.__doc__) # the docstring only
```
In the interactive shell `help(print)` is a quick way to see a function's parameters. Press `q` to leave the help screen.

---

## Do Not Shadow Built-ins

If you use a built-in name for your own variable, the built-in stops working in that scope.

```python
list = [1, 2, 3]            # hides the list() function
print(list("abc"))
# Error:  TypeError: 'list' object is not callable
```
Common victims: `list`, `str`, `sum`, `max`, `min`, `len`, `input`, `type`, `id`. Choose names such as `numbers`, `total`, `maximum`. (See the Scope note.)

---

- [Back to section index](./README.md)
