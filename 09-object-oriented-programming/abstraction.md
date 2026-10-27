# Abstraction - Revision Notes ~ sm

> **In one line:** **Abstraction** means **showing only what an object does and hiding how it does it**. In Python you build **abstract classes** with the `abc` module: they define the methods every child **must** write, but cannot be used to create objects themselves.

## Table of Contents
- [What is Abstraction?](#what-is-abstraction)
- [Abstraction in Daily Life and in Python](#abstraction-in-daily-life-and-in-python)
- [The Problem: Forgotten Methods](#the-problem-forgotten-methods)
- [Abstract Classes with abc](#abstract-classes-with-abc)
- [You Cannot Create an Object of an Abstract Class](#you-cannot-create-an-object-of-an-abstract-class)
- [Children Must Implement Every Abstract Method](#children-must-implement-every-abstract-method)
- [Concrete Methods in an Abstract Class](#concrete-methods-in-an-abstract-class)
- [Abstract Properties](#abstract-properties)
- [A Complete Example](#a-complete-example)
- [Abstract Class vs Interface](#abstract-class-vs-interface)
- [NotImplementedError Alternative](#notimplementederror-alternative)
- [Abstraction vs Encapsulation](#abstraction-vs-encapsulation)
- [Common Mistakes](#common-mistakes)

---

## What is Abstraction?

When you drive a car you use the steering wheel, pedals and gear lever. You do not need to know how the engine burns fuel. The car **hides the complexity** and offers a **simple interface**. That is abstraction.

| Level | Example |
|---|---|
| **What** (interface) | `car.start()`, `payment.pay(100)` |
| **How** (hidden details) | Fuel injection, card network calls |

Every function already works like this: you call `len(x)` without knowing how it counts.

---

## Abstraction in Daily Life and in Python

In OOP, abstraction appears in two ways:

1. **Methods hide the steps.** `account.withdraw(500)` hides checking the balance, updating it and recording the transaction.
2. **Abstract classes define a contract.** "Every payment method must have a `pay()` method", without saying how each one pays.

The second one needs the `abc` module.

---

## The Problem: Forgotten Methods

```python
class Shape:
    def area(self):
        pass

class Circle(Shape):
    def __init__(self, r):
        self.r = r
    # forgot to write area() !

c = Circle(2)
print(c.area())
# Output: None
```
Python happily runs and silently gives `None`. A bug like this can travel far. We want Python to **refuse** to build an incomplete `Circle`.

---

## Abstract Classes with abc

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass
```

| Piece | Meaning |
|---|---|
| `ABC` | "Abstract Base Class". Inherit from it to make an abstract class |
| `@abstractmethod` | Marks a method that every **child must override**. Its body is usually just `pass` or `...` |

---

## You Cannot Create an Object of an Abstract Class

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

s = Shape()
# Error:  TypeError: Can't instantiate abstract class Shape without an implementation for abstract method 'area'
```
An abstract class is only a **template**. It describes what children must provide.

---

## Children Must Implement Every Abstract Method

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

    @abstractmethod
    def perimeter(self):
        pass

class Square(Shape):
    def __init__(self, side):
        self.side = side

    def area(self):
        return self.side ** 2

sq = Square(3)
# Error:  TypeError: Can't instantiate abstract class Square without an implementation for abstract method 'perimeter'
```
`Square` forgot `perimeter`, so Python **refuses to create** it. When the child implements all abstract methods it becomes a **concrete class**:

```python
class Square(Shape):
    def __init__(self, side):
        self.side = side

    def area(self):
        return self.side ** 2

    def perimeter(self):
        return 4 * self.side

sq = Square(3)
print(sq.area(), sq.perimeter())
# Output: 9 12
```
This catches the mistake **when the object is created**, not later in the middle of the program.

---

## Concrete Methods in an Abstract Class

An abstract class may also have **normal methods** and attributes, which the children inherit.

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    def __init__(self, name):
        self.name = name

    @abstractmethod
    def area(self):
        pass

    def describe(self):                       # normal method
        return f"{self.name} with area {self.area()}"

class Square(Shape):
    def __init__(self, side):
        super().__init__("Square")
        self.side = side

    def area(self):
        return self.side ** 2

print(Square(4).describe())
# Output: Square with area 16
```
`describe()` is written once in the parent and calls `area()`, which each child defines. This is **abstraction and polymorphism working together**.

---

## Abstract Properties

Properties can be abstract too. Put `@property` **above** `@abstractmethod`.

```python
from abc import ABC, abstractmethod

class Vehicle(ABC):
    @property
    @abstractmethod
    def wheels(self):
        pass

class Bike(Vehicle):
    @property
    def wheels(self):
        return 2

print(Bike().wheels)
# Output: 2
```

---

## A Complete Example

```python
from abc import ABC, abstractmethod

class Payment(ABC):
    @abstractmethod
    def pay(self, amount):
        pass

class UPI(Payment):
    def pay(self, amount):
        return f"Paid {amount} using UPI"

class Card(Payment):
    def pay(self, amount):
        return f"Paid {amount} using Card"

def checkout(method, amount):
    print(method.pay(amount))      # does not care HOW it is paid

checkout(UPI(), 500)
checkout(Card(), 1200)
# Output: Paid 500 using UPI
# Output: Paid 1200 using Card
```
`checkout` knows only the **interface** (`pay`). If a new `Cash(Payment)` class is added, `checkout` needs no change.

---

## Abstract Class vs Interface

Some languages have an `interface` keyword (a class with only method names). Python has none. A common way is an **abstract class with only abstract methods**, which acts as an interface.

| | Abstract class | Interface (in Python) |
|---|---|---|
| Abstract methods | Some | All |
| Normal methods and data | Allowed | Usually none |
| Object creation | Not allowed | Not allowed |

---

## NotImplementedError Alternative

A simpler (older) approach: the parent method raises `NotImplementedError`.

```python
class Shape:
    def area(self):
        raise NotImplementedError("Subclasses must write area()")

class Circle(Shape):
    pass

try:
    Circle().area()
except NotImplementedError as e:
    print("Error:", e)
# Output: Error: Subclasses must write area()
```
It works, but the error only appears **when the method is called**. With `ABC`, it appears **when the object is created**, which is earlier and safer.

---

## Abstraction vs Encapsulation

| | Abstraction | Encapsulation |
|---|---|---|
| Focus | **Design**: what the object offers | **Protection**: who may access the data |
| Hides | Complexity / implementation | Internal data |
| Python tools | `ABC`, `@abstractmethod`, clear method names | `_x`, `__x`, `@property` |

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Forgetting to inherit from `ABC` | `@abstractmethod` is **not enforced** | `class Shape(ABC):` |
| Trying `Shape()` | `TypeError: Can't instantiate abstract class ...` | Create an object of a concrete child |
| Child misses an abstract method | `TypeError: Can't instantiate abstract class ... without an implementation for abstract method ...` | Implement all abstract methods |
| Different method name in the child (`areas`) | Counts as missing | Same name as the abstract method |
| `@property` below `@abstractmethod` | Not treated as an abstract property | `@property` first, `@abstractmethod` underneath |
| Putting too much code in the abstract methods | Misleading | Keep them as `pass` / `...`, or a docstring |
| Forgetting `from abc import ABC, abstractmethod` | `NameError` | Import both |

---

- [Back to section index](./README.md)
