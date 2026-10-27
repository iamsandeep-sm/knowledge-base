# Special Methods (Dunder Methods) - Revision Notes ~ sm

> **In one line:** **Special methods** have names with **double underscores on both sides** (`__init__`, `__str__`, `__add__` ...), called **dunder** methods. You rarely call them yourself: **Python calls them for you** when you use `print()`, `+`, `==`, `len()`, `for` and so on. Writing them makes your own objects behave like built-in ones.

## Table of Contents
- [What are Dunder Methods?](#what-are-dunder-methods)
- [Overview Table](#overview-table)
- [__str__ and __repr__](#__str__-and-__repr__)
- [__len__](#__len__)
- [Comparison: __eq__, __lt__ and Others](#comparison-__eq__-__lt__-and-others)
- [__eq__ and Hashing](#__eq__-and-hashing)
- [Arithmetic: __add__ and Friends](#arithmetic-__add__-and-friends)
- [Reflected Operators: __radd__](#reflected-operators-__radd__)
- [In-place Operators: __iadd__](#in-place-operators-__iadd__)
- [__bool__](#__bool__)
- [Indexing: __getitem__, __setitem__, __contains__](#indexing-__getitem__-__setitem__-__contains__)
- [Iteration: __iter__ and __next__](#iteration-__iter__-and-__next__)
- [__call__: Objects That Act Like Functions](#__call__-objects-that-act-like-functions)
- [Context Manager: __enter__ and __exit__](#context-manager-__enter__-and-__exit__)
- [A Complete Example](#a-complete-example)
- [Common Mistakes](#common-mistakes)

---

## What are Dunder Methods?

"Dunder" = **d**ouble **under**score. You have already met one: `__init__`.

```python
class Box:
    def __init__(self, items):
        self.items = items

    def __len__(self):
        return len(self.items)

b = Box([1, 2, 3])
print(len(b))
# Output: 3
```
You wrote `len(b)`, and Python called `b.__len__()` behind the scenes. Without `__len__`, `len(b)` would fail with `TypeError: object of type 'Box' has no len()`.

> Write `len(b)`, not `b.__len__()`. Only define the dunder methods; let Python call them. Do not invent your own dunder names.

---

## Overview Table

| You write | Python calls | Used for |
|---|---|---|
| `Box(...)` | `__init__` | Setting up a new object |
| `print(obj)`, `str(obj)` | `__str__` | A readable text for users |
| `repr(obj)`, the shell, `print([obj])` | `__repr__` | An exact text for developers |
| `len(obj)` | `__len__` | Length |
| `a == b` | `__eq__` | Equality |
| `a < b`, `a <= b`, `a > b`, `a >= b` | `__lt__`, `__le__`, `__gt__`, `__ge__` | Ordering and sorting |
| `a + b`, `a - b`, `a * b`, `a / b` | `__add__`, `__sub__`, `__mul__`, `__truediv__` | Arithmetic |
| `bool(obj)`, `if obj:` | `__bool__` (then `__len__`) | Truth value |
| `obj[key]` | `__getitem__` | Indexing |
| `obj[key] = v` | `__setitem__` | Assigning by index |
| `x in obj` | `__contains__` | Membership |
| `for x in obj` | `__iter__`, `__next__` | Looping |
| `obj(...)` | `__call__` | Calling the object |
| `with obj:` | `__enter__`, `__exit__` | Context manager |
| `hash(obj)` | `__hash__` | Use as dict key or set item |

---

## __str__ and __repr__

By default an object prints as `<__main__.Point object at 0x...>`. Two methods give it a proper text.

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __str__(self):
        return f"({self.x}, {self.y})"

    def __repr__(self):
        return f"Point({self.x}, {self.y})"

p = Point(3, 4)
print(p)
print(str(p))
print(repr(p))
print([p, p])
print(f"{p} and {p!r}")
# Output: (3, 4)
# Output: (3, 4)
# Output: Point(3, 4)
# Output: [Point(3, 4), Point(3, 4)]
# Output: (3, 4) and Point(3, 4)
```

| | `__str__` | `__repr__` |
|---|---|---|
| For | **Users**: friendly and readable | **Developers**: exact and unambiguous |
| Used by | `print()`, `str()`, f-strings | `repr()`, the shell, **inside lists**, `!r`, debugging |
| Goal | Easy to read | Ideally shows how to rebuild the object |
| If missing | Python uses `__repr__` | Python uses `<... object at 0x...>` |

Rule of thumb: **always write `__repr__`** (it also gives you a decent `__str__`), and add `__str__` when you need a friendlier text. Both must **return a string**.

---

## __len__

```python
class Playlist:
    def __init__(self, songs):
        self.songs = songs

    def __len__(self):
        return len(self.songs)

p = Playlist(["a", "b", "c"])
print(len(p))
# Output: 3
```

---

## Comparison: __eq__, __lt__ and Others

By default two different objects are never equal (they are compared by **identity**). Define `__eq__` to compare by **value**.

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __eq__(self, other):
        if not isinstance(other, Point):
            return NotImplemented
        return (self.x, self.y) == (other.x, other.y)

print(Point(1, 2) == Point(1, 2))
print(Point(1, 2) == Point(3, 4))
print(Point(1, 2) == "text")
# Output: True
# Output: False
# Output: False
```
Returning `NotImplemented` (not an error) tells Python "I do not know how to compare with this type", so it tries the other side and finally answers `False`.

**Ordering** needs `__lt__` (less than) to use `<` and `sorted()`.

```python
class Student:
    def __init__(self, name, marks):
        self.name = name
        self.marks = marks

    def __lt__(self, other):
        return self.marks < other.marks

    def __repr__(self):
        return f"{self.name}({self.marks})"

students = [Student("Riya", 85), Student("Amit", 70), Student("Sam", 92)]
print(sorted(students))
print(max(students))
# Output: [Amit(70), Riya(85), Sam(92)]
# Output: Sam(92)
```
`sorted()`, `min()` and `max()` only need `__lt__`. To get all of `<`, `<=`, `>`, `>=` from just `__eq__` and one ordering method, use `@functools.total_ordering`:

```python
from functools import total_ordering

@total_ordering
class Version:
    def __init__(self, n):
        self.n = n

    def __eq__(self, other):
        return self.n == other.n

    def __lt__(self, other):
        return self.n < other.n

print(Version(1) <= Version(2), Version(3) > Version(2))
# Output: True True
```

---

## __eq__ and Hashing

If you define `__eq__` **without** `__hash__`, Python sets `__hash__` to `None`, and the object **cannot be used in a set or as a dictionary key**.

```python
class Point:
    def __init__(self, x, y):
        self.x, self.y = x, y

    def __eq__(self, other):
        return (self.x, self.y) == (other.x, other.y)

{Point(1, 2)}
# Error:  TypeError: unhashable type: 'Point'
```
Objects that are equal must have the **same hash**. Add `__hash__` based on the same values:
```python
class Point:
    def __init__(self, x, y):
        self.x, self.y = x, y

    def __eq__(self, other):
        return (self.x, self.y) == (other.x, other.y)

    def __hash__(self):
        return hash((self.x, self.y))

print(len({Point(1, 2), Point(1, 2), Point(3, 4)}))
# Output: 2
```
(Only do this for objects that you do not change after creating them.)

---

## Arithmetic: __add__ and Friends

```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __add__(self, other):
        return Vector(self.x + other.x, self.y + other.y)

    def __sub__(self, other):
        return Vector(self.x - other.x, self.y - other.y)

    def __mul__(self, k):                # vector * number
        return Vector(self.x * k, self.y * k)

    def __repr__(self):
        return f"Vector({self.x}, {self.y})"

a = Vector(1, 2)
b = Vector(3, 4)
print(a + b)
print(b - a)
print(a * 3)
# Output: Vector(4, 6)
# Output: Vector(2, 2)
# Output: Vector(3, 6)
```

| Operator | Method | Operator | Method |
|---|---|---|---|
| `+` | `__add__` | `//` | `__floordiv__` |
| `-` | `__sub__` | `%` | `__mod__` |
| `*` | `__mul__` | `**` | `__pow__` |
| `/` | `__truediv__` | `-x` | `__neg__` |

If a method meets a type it does not support, return `NotImplemented` and Python will raise a clean `TypeError`.

---

## Reflected Operators: __radd__

`a + b` first calls `a.__add__(b)`. If `a` does not know `b`, Python tries `b.__radd__(a)`. This matters for `sum()`, which starts with the number `0`.

```python
class Money:
    def __init__(self, amount):
        self.amount = amount

    def __add__(self, other):
        if isinstance(other, Money):
            return Money(self.amount + other.amount)
        return Money(self.amount + other)

    __radd__ = __add__                   # makes  5 + Money(3)  and  sum(...)  work

    def __repr__(self):
        return f"Money({self.amount})"

print(Money(5) + 3)
print(3 + Money(5))
print(sum([Money(1), Money(2), Money(3)]))
# Output: Money(8)
# Output: Money(8)
# Output: Money(6)
```
Without `__radd__`, `3 + Money(5)` gives `TypeError: unsupported operand type(s) for +: 'int' and 'Money'`.

---

## In-place Operators: __iadd__

`a += b` uses `__iadd__` if it exists (to change the object itself), otherwise it falls back to `a = a + b`.

```python
class Counter:
    def __init__(self):
        self.n = 0

    def __iadd__(self, k):
        self.n += k
        return self                      # must return the object

c = Counter()
c += 5
c += 2
print(c.n)
# Output: 7
```

---

## __bool__

Decides what `if obj:` means. If `__bool__` is missing, Python uses `__len__` (zero means false), and otherwise every object is true.

```python
class Basket:
    def __init__(self, items):
        self.items = items

    def __len__(self):
        return len(self.items)

print(bool(Basket([])), bool(Basket(["pen"])))
# Output: False True
```

---

## Indexing: __getitem__, __setitem__, __contains__

```python
class Deck:
    def __init__(self):
        self.cards = ["A", "K", "Q"]

    def __getitem__(self, index):
        return self.cards[index]

    def __setitem__(self, index, value):
        self.cards[index] = value

    def __contains__(self, card):
        return card in self.cards

d = Deck()
print(d[0], d[-1])
d[1] = "J"
print("J" in d, "K" in d)
for card in d:                           # works because of __getitem__
    print(card, end=" ")
# Output: A Q
# Output: True False
# Output: A J Q
```
An object with `__getitem__` also supports slicing and a `for` loop (Python tries `0, 1, 2 ...` until `IndexError`). Dictionary-like classes receive the **key** in `__getitem__`.

---

## Iteration: __iter__ and __next__

Make an object work in a `for` loop by returning an **iterator**: an object with `__next__` that raises `StopIteration` when finished.

```python
class Countdown:
    def __init__(self, start):
        self.current = start

    def __iter__(self):
        return self

    def __next__(self):
        if self.current <= 0:
            raise StopIteration
        value = self.current
        self.current -= 1
        return value

for n in Countdown(3):
    print(n)
print(list(Countdown(3)))
# Output: 3
# Output: 2
# Output: 1
# Output: [3, 2, 1]
```
(Iterators and generators, a shorter way to write this, are covered in the Advanced Python section.)

---

## __call__: Objects That Act Like Functions

```python
class Multiplier:
    def __init__(self, factor):
        self.factor = factor

    def __call__(self, x):
        return x * self.factor

triple = Multiplier(3)
print(triple(10))
print(callable(triple))
# Output: 30
# Output: True
```
`triple(10)` runs `triple.__call__(10)`. This is useful for objects that remember some state (like `factor`).

---

## Context Manager: __enter__ and __exit__

These two methods make `with obj:` work (see the Context Managers note).

```python
class Section:
    def __enter__(self):
        print("enter")
        return self

    def __exit__(self, exc_type, exc, tb):
        print("exit")

with Section():
    print("inside")
# Output: enter
# Output: inside
# Output: exit
```

---

## A Complete Example

```python
class Fraction:
    def __init__(self, num, den):
        self.num = num
        self.den = den

    def __add__(self, other):
        return Fraction(self.num * other.den + other.num * self.den,
                        self.den * other.den)

    def __eq__(self, other):
        return self.num * other.den == other.num * self.den

    def __str__(self):
        return f"{self.num}/{self.den}"

    def __repr__(self):
        return f"Fraction({self.num}, {self.den})"

half = Fraction(1, 2)
third = Fraction(1, 3)
print(half + third)
print(Fraction(1, 2) == Fraction(2, 4))
print([half, third])
# Output: 5/6
# Output: True
# Output: [Fraction(1, 2), Fraction(1, 3)]
```
(Python's standard library has a ready-made `fractions.Fraction`; this is only a practice class.)

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Misspelling the name: `__str`, `_str_`, `__string__` | Never called | Exactly two underscores on both sides |
| `__str__` that prints instead of returning | `TypeError: __str__ returned non-string (type NoneType)` | `return` a string |
| Calling `obj.__len__()` directly | Works but is bad style | `len(obj)` |
| `__eq__` without `__hash__` | Object is unhashable (cannot be put in a set or used as a dict key) | Also define `__hash__` |
| `__add__` with no `return` | Result is `None` | Return a new object |
| No `__radd__` and using `sum()` | `TypeError` with `int + object` | Add `__radd__` |
| `__iadd__` without `return self` | The variable becomes `None` | End with `return self` |
| Comparing with an unexpected type inside `__eq__` | `AttributeError` | Check `isinstance`, return `NotImplemented` |
| Only `__lt__` defined, then using `>=` | `TypeError` | Add the other methods or `@total_ordering` |

---

- [Back to section index](./README.md)
