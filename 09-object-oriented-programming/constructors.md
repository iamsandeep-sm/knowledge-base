# Constructors - Revision Notes ~ sm

> **In one line:** A **constructor** is the special method **`__init__`** that Python runs **automatically** when you create an object. It sets up the object's starting data, so every new object begins in a proper state.

## Table of Contents
- [The Problem __init__ Solves](#the-problem-__init__-solves)
- [The __init__ Method](#the-__init__-method)
- [How Object Creation Runs (Dry Run)](#how-object-creation-runs-dry-run)
- [Default Values](#default-values)
- [Keyword Arguments](#keyword-arguments)
- [Validating Input in __init__](#validating-input-in-__init__)
- [Attributes Computed from Arguments](#attributes-computed-from-arguments)
- [Do Not Share Mutable Defaults](#do-not-share-mutable-defaults)
- [Only One __init__: Alternative Constructors](#only-one-__init__-alternative-constructors)
- [__init__ vs __new__](#__init__-vs-__new__)
- [Quick Shortcut: dataclass](#quick-shortcut-dataclass)
- [Destructor __del__](#destructor-__del__)
- [Common Mistakes](#common-mistakes)

---

## The Problem __init__ Solves

Without a constructor you must set every attribute by hand, and may forget one.

```python
class Student:
    pass

s = Student()
s.name = "Riya"
# forgot s.marks !
```
A constructor makes sure the attributes are created **together with** the object.

---

## The __init__ Method

```python
class Student:
    def __init__(self, name, marks):
        self.name = name
        self.marks = marks

s1 = Student("Riya", 85)
s2 = Student("Amit", 70)

print(s1.name, s1.marks)
print(s2.name, s2.marks)
# Output: Riya 85
# Output: Amit 70
```

| Part | Meaning |
|---|---|
| `__init__` | The constructor. The two underscores on each side make it a **special (dunder) method** |
| `self` | The new object being built |
| `name`, `marks` | Ordinary parameters, filled with the values you pass to `Student(...)` |
| `self.name = name` | Create an **attribute** on the object and store the value in it |

`self.name` is the object's attribute; `name` is just the parameter. They may have different names, but using the same name is common.

---

## How Object Creation Runs (Dry Run)

`s1 = Student("Riya", 85)`

| Step | What Python does |
|---|---|
| 1 | Creates a new, empty `Student` object |
| 2 | Calls `Student.__init__(new_object, "Riya", 85)` (the new object becomes `self`) |
| 3 | `__init__` stores `name` and `marks` on the object |
| 4 | The finished object is stored in `s1` |

You never call `__init__` yourself: `Student(...)` does it.

---

## Default Values

Parameters of `__init__` can have defaults, just like any function.

```python
class Account:
    def __init__(self, owner, balance=0, currency="INR"):
        self.owner = owner
        self.balance = balance
        self.currency = currency

a = Account("Riya")
b = Account("Amit", 500)
c = Account("Sam", currency="USD")

print(a.balance, a.currency)
print(b.balance)
print(c.currency)
# Output: 0 INR
# Output: 500
# Output: USD
```

---

## Keyword Arguments

You can pass the arguments by name, in any order.

```python
class Student:
    def __init__(self, name, marks):
        self.name = name
        self.marks = marks

s = Student(marks=90, name="Zoya")
print(s.name, s.marks)
# Output: Zoya 90
```
Passing too few or too many values is an error:
```python
Student("Riya")
# Error:  TypeError: Student.__init__() missing 1 required positional argument: 'marks'
```

---

## Validating Input in __init__

The constructor is the best place to **reject bad data** so that no invalid object can exist. Raise an exception (see the Raising Exceptions note).

```python
class Student:
    def __init__(self, name, marks):
        if not name:
            raise ValueError("name cannot be empty")
        if not 0 <= marks <= 100:
            raise ValueError("marks must be between 0 and 100")
        self.name = name
        self.marks = marks

try:
    Student("Riya", 150)
except ValueError as e:
    print("Error:", e)
# Output: Error: marks must be between 0 and 100
```

---

## Attributes Computed from Arguments

An attribute can be calculated from the arguments, or be fixed for every object.

```python
class Rectangle:
    def __init__(self, width, height):
        self.width = width
        self.height = height
        self.area = width * height      # computed
        self.sides = 4                  # same for every object

r = Rectangle(3, 4)
print(r.area, r.sides)
# Output: 12 4
```
Caution: `self.area` is calculated **once**. If `width` changes later, `area` is not updated. For values that must always stay correct, use a `@property` (see the Encapsulation note).

---

## Do Not Share Mutable Defaults

A default value like `[]` is created **once** and **shared** by all objects. This is the same trap as in functions (see the Parameters and Arguments note).

```python
class Team:
    def __init__(self, name, members=[]):        # BAD
        self.name = name
        self.members = members

a = Team("A")
b = Team("B")
a.members.append("Riya")
print(b.members)
# Output: ['Riya']
```
Use `None` and create a new list inside:
```python
class Team:
    def __init__(self, name, members=None):      # GOOD
        self.name = name
        self.members = [] if members is None else members

a = Team("A")
b = Team("B")
a.members.append("Riya")
print(b.members)
# Output: []
```

---

## Only One __init__: Alternative Constructors

A class has **one** `__init__`. If you define it twice, the second silently replaces the first. Python does not have constructor overloading, so use **default arguments**, or write an **alternative constructor** with a `@classmethod` (explained in the Instance and Class Attributes note).

```python
class Point:
    def __init__(self, x=0, y=0):
        self.x = x
        self.y = y

    @classmethod
    def from_text(cls, text):               # alternative constructor
        x, y = text.split(",")
        return cls(int(x), int(y))

print(Point().x)
print(Point(3, 4).y)
p = Point.from_text("5,6")
print(p.x, p.y)
# Output: 0
# Output: 4
# Output: 5 6
```

---

## __init__ vs __new__

| | `__new__` | `__init__` |
|---|---|---|
| Job | **Creates** the empty object | **Sets up** the object that already exists |
| Runs | First | Second |
| Needed? | Almost never | Almost always |

Beginners only ever write `__init__`. `__new__` is used for special cases such as immutable types and singletons.

`__init__` must return `None`. Returning anything else is an error:
```python
class A:
    def __init__(self):
        return 5

A()
# Error:  TypeError: __init__() should return None, not 'int'
```

---

## Quick Shortcut: dataclass

When a class mostly stores data, `@dataclass` writes `__init__` (and a readable printout) for you.

```python
from dataclasses import dataclass

@dataclass
class Point:
    x: int
    y: int = 0

p = Point(3)
print(p)
print(p == Point(3, 0))
# Output: Point(x=3, y=0)
# Output: True
```
The lines `x: int` are **annotations** (type hints); `dataclass` uses them to find the fields. Python does not check the types. It is a time-saver once you understand `__init__`.

---

## Destructor __del__

`__del__` is called when an object is about to be destroyed. It is **rarely needed**, and exactly *when* it runs is not guaranteed. For clean-up, use a `with` statement and a context manager instead (see the Context Managers note).

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Misspelling: `__int__`, `_init_`, `__innit__` | It is not the constructor, so it never runs automatically | `__init__` with two underscores on **both** sides |
| Forgetting `self` as the first parameter | `TypeError: ... takes 2 positional arguments but 3 were given` | `def __init__(self, ...)` |
| `name = name` instead of `self.name = name` | The data is not stored on the object | `self.name = name` |
| Returning a value from `__init__` | `TypeError: __init__() should return None` | Do not return anything |
| Two `__init__` methods | The second silently replaces the first | One `__init__` with defaults, or `@classmethod` |
| Mutable default (`members=[]`) | All objects share one list | `members=None` |
| Calling `Student()` without the required arguments | `TypeError: ... missing 1 required positional argument` | Pass them, or give defaults |
| Expecting a computed attribute to update | It was calculated once | Use a `@property` |

---

- [Back to section index](./README.md)
