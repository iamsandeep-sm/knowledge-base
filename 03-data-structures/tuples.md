#  Python Tuples — Revision Notes       ~sm

> **TUPLE -** A tuple is an **ordered**, **immutable** (unchangeable) collection that can store multiple items — of any data type — in a single variable.

## Table of Contents
- [What is a Tuple?](#what-is-a-tuple)
- [Creating Tuples](#creating-tuples)
- [Tuple Indexes](#tuple-indexes)
- [Slicing — Range of Index](#slicing--range-of-index)
- [Checking for Items & Length](#checking-for-items--length)
- [Looping Through a Tuple](#looping-through-a-tuple)
- [Immutability & Manipulating Tuples](#immutability--manipulating-tuples)
- [Joining & Repeating Tuples](#joining--repeating-tuples)
- [Tuple Methods & Built-in Functions](#tuple-methods--built-in-functions)
- [Unpacking Tuples](#unpacking-tuples)
- [Tuple vs List](#tuple-vs-list)
- [Why Use Tuples?](#why-use-tuples)

---

## What is a Tuple?

| Property | Meaning |
|---|---|
| **Ordered** | Items keep a fixed position (index) |
| **Immutable** | Cannot add / remove / change items after creation |
| **Allows duplicates** | The same value can appear many times |
| **Heterogeneous** | Can mix data types (`int`, `str`, `float`, `list`, ...) |
| **Indexed** | Access items by position, starting at `0` |
| **Hashable*** | Can be used as a dictionary key (*if all items inside are hashable too) |

> **Remember:** It is the **comma** that creates a tuple, **not the parentheses**.

```python
t = 1, 2, 3          # still a tuple (no brackets!)
print(type(t))       # <class 'tuple'>
```

---

## Creating Tuples

### Basic creation
```python
tuple1 = (1, 2, 2, 3, 5, 4, 6)
tuple2 = ("Red", "Green", "Blue")
details = ("Abhijeet", 18, "FYBScIT", 9.8)   # mixed data types

print(tuple1)
print(tuple2)
print(details)
```

### Empty tuple
```python
empty = ()
print(type(empty))     # <class 'tuple'>
```

### Single-item tuple — the trailing comma trap
```python
a = ("hello")      #  NOT a tuple → it's a str
b = ("hello",)     #  tuple with one item

print(type(a))     # <class 'str'>
print(type(b))     # <class 'tuple'>
```
> A single item **must** have a trailing comma.

### Using the `tuple()` constructor
```python
t1 = tuple([1, 2, 3])        # from a list   → (1, 2, 3)
t2 = tuple("Python")         # from a string → ('P', 'y', 't', 'h', 'o', 'n')
t3 = tuple(range(1, 6))      # from range    → (1, 2, 3, 4, 5)
```

### Tuple without parentheses (packing)
```python
point = 10, 20, 30
print(point)         # (10, 20, 30)
```

### Nested tuples
```python
student = ("Abhijeet", (85, 90, 78))
print(student[1][0])   # 85
```

---

## Tuple Indexes

Each item has a unique **index**. Python supports both **positive** (left → right) and **negative** (right → left) indexing.

```python
country = ("Spain", "Italy", "India", "England", "Germany")
#            [0]      [1]      [2]       [3]        [4]     ← positive
#           [-5]      [-4]     [-3]      [-2]       [-1]    ← negative

print(country[0])      # Spain
print(country[2])      # India
print(country[-1])     # Germany  (last item)
print(country[-5])     # Spain    (first item)
```

| Index | `0` | `1` | `2` | `3` | `4` |
|---|---|---|---|---|---|
| **Value** | Spain | Italy | India | England | Germany |
| **Negative** | `-5` | `-4` | `-3` | `-2` | `-1` |

> ! Accessing an index that doesn't exist raises `IndexError: tuple index out of range`.

---

## Slicing — Range of Index

This topic has its own note: [Slicing](./slicing.md).

---

## Checking for Items & Length

### Membership test with `in` / `not in`
```python
country = ("Spain", "Italy", "India", "England", "Germany")

if "Germany" in country:
    print("Germany is present.")
else:
    print("Germany is absent.")

print("France" not in country)   # True
```

### Length with `len()`
```python
print(len(country))   # 5
```

---

## Looping Through a Tuple

```python
country = ("Spain", "Italy", "India")

# 1) Directly over items
for c in country:
    print(c)

# 2) Using index
for i in range(len(country)):
    print(i, country[i])

# 3) enumerate() — index + value together (most Pythonic)
for i, c in enumerate(country):
    print(i, c)

# 4) while loop
i = 0
while i < len(country):
    print(country[i])
    i += 1
```

---

## Immutability & Manipulating Tuples

Tuples are **immutable** — you cannot add, remove, or change items directly.

```python
t = (1, 2, 3)
t[0] = 99        #  TypeError: 'tuple' object does not support item assignment
```

###  Workaround: convert → modify → convert back
```
tuple  →  list()  →  modify  →  tuple()
```

```python
countries = ("Spain", "Italy", "India", "England", "Germany")

temp = list(countries)       # 1. convert to list
temp.append("Russia")        # 2. add item
temp.pop(3)                  #    remove item at index 3 ("England")
temp[2] = "Finland"          #    change item at index 2

countries = tuple(temp)      # 3. convert back to tuple
print(countries)
# ('Spain', 'Italy', 'Finland', 'Germany', 'Russia')
```

>  This doesn't really *modify* the old tuple — it creates a **brand-new tuple** and re-assigns the variable.

###  Special case: mutable objects *inside* a tuple
The tuple's **structure** is fixed, but if an item is itself mutable (like a list), **that item can still change**.

```python
t = (1, 2, [10, 20])
t[2].append(30)
print(t)           # (1, 2, [10, 20, 30])  ← allowed!

# t[2] = [99]      #  still not allowed (can't replace the item itself)
```

### Deleting a tuple
You can't delete single items, but you can delete the **whole tuple**:
```python
del countries
# print(countries)   #  NameError: name 'countries' is not defined
```

---

## Joining & Repeating Tuples

### Concatenate with `+`
```python
a = (1, 2, 3)
b = ("x", "y")
print(a + b)       # (1, 2, 3, 'x', 'y')
```

### Repeat with `*`
```python
print(("Hi",) * 3)   # ('Hi', 'Hi', 'Hi')
print((0, 1) * 2)    # (0, 1, 0, 1)
```

---

## Tuple Methods & Built-in Functions

Tuples have only **2 methods** (because they're immutable):

| Method | Description | Example |
|---|---|---|
| `count(x)` | Number of times `x` appears | `(1,2,2,3).count(2)` → `2` |
| `index(x)` | Index of the **first** occurrence of `x` | `(1,2,2,3).index(2)` → `1` |

> `index()` raises `ValueError` if the item isn't found.

### Useful built-in functions

| Function | Purpose | Example |
|---|---|---|
| `len(t)` | Number of items | `len((1,2,3))` → `3` |
| `max(t)` | Largest item | `max((4, 9, 2))` → `9` |
| `min(t)` | Smallest item | `min((4, 9, 2))` → `2` |
| `sum(t)` | Sum of numeric items | `sum((4, 9, 2))` → `15` |
| `sorted(t)` | Returns a **sorted list** (not a tuple!) | `sorted((3,1,2))` → `[1, 2, 3]` |
| `tuple(x)` | Converts iterable to tuple | `tuple([1,2])` → `(1, 2)` |

```python
nums = (5, 2, 8, 1)
sorted_nums = tuple(sorted(nums))   # wrap in tuple() to get a tuple back
print(sorted_nums)                  # (1, 2, 5, 8)
```

>  Tuples have **no** `.sort()`, `.append()`, `.remove()`, `.pop()`, `.insert()` or `.reverse()`.

---

## Unpacking Tuples

This topic has its own note: [Unpacking](./unpacking.md).

---

## Tuple vs List

| Feature | **Tuple** | **List** |
|---|---|---|
| Syntax | `( )` | `[ ]` |
| Mutable? |  No |  Yes |
| Methods | 2 (`count`, `index`) | Many (`append`, `pop`, `sort`, ...) |
| Speed | Slightly faster | Slightly slower |
| Memory | Uses less | Uses more |
| Dictionary key / set item |  Possible (if hashable) |  Not possible |
| Best for | Fixed data (coordinates, dates, config) | Data that changes |

---

## Why Use Tuples?

-  **Data safety** — protects data that must not change (e.g., days of the week, RGB colors).
-  **Faster & lighter** than lists.
-  **Hashable** — can be dictionary keys or set members.
-  **Multiple return values** from functions.
-  **Signals intent** — tells other programmers "this data is fixed".

```python
# Tuple as a dictionary key
locations = {(28.61, 77.20): "Delhi", (19.07, 72.87): "Mumbai"}
print(locations[(28.61, 77.20)])    # Delhi
```

---

- [Back to section index](./README.md)
