# Polymorphism - Revision Notes ~ sm

> **In one line:** **Polymorphism** means "**many forms**": the **same method call or operator does different things depending on the object**. You write code that says `shape.area()` and each kind of shape works out its own answer.

## Table of Contents
- [What is Polymorphism?](#what-is-polymorphism)
- [Built-in Examples You Already Use](#built-in-examples-you-already-use)
- [Polymorphism with Inheritance (Overriding)](#polymorphism-with-inheritance-overriding)
- [Why It is Useful](#why-it-is-useful)
- [Duck Typing](#duck-typing)
- [Polymorphism Without Inheritance](#polymorphism-without-inheritance)
- [When an Object Does Not Fit](#when-an-object-does-not-fit)
- [Operator Polymorphism](#operator-polymorphism)
- [Polymorphic Functions](#polymorphic-functions)
- [Overloading Does Not Exist](#overloading-does-not-exist)
- [Common Mistakes](#common-mistakes)

---

## What is Polymorphism?

A **remote control** has a "power" button. Pressing it turns a TV on, a fan on or an AC on: **same button, different result**, depending on the device.

In code: one **interface** (a method name), many **implementations**.

---

## Built-in Examples You Already Use

```python
print(len("hello"))
print(len([1, 2, 3]))
print(len({"a": 1, "b": 2}))

print(3 + 4)
print("3" + "4")
print([1] + [2])
# Output: 5
# Output: 3
# Output: 2
# Output: 7
# Output: 34
# Output: [1, 2]
```
- `len()` works on strings, lists and dictionaries.
- `+` adds numbers, joins strings and joins lists.

---

## Polymorphism with Inheritance (Overriding)

Different child classes provide **their own version** of a method with the same name (see Inheritance).

```python
class Shape:
    def area(self):
        return 0

class Rectangle(Shape):
    def __init__(self, w, h):
        self.w, self.h = w, h

    def area(self):
        return self.w * self.h

class Circle(Shape):
    def __init__(self, r):
        self.r = r

    def area(self):
        return round(3.14159 * self.r ** 2, 2)

shapes = [Rectangle(3, 4), Circle(1), Rectangle(2, 5)]
for s in shapes:
    print(type(s).__name__, s.area())
# Output: Rectangle 12
# Output: Circle 3.14
# Output: Rectangle 10
```
The loop does not care which kind of shape it has. It just calls `area()`, and **each object responds in its own way**.

---

## Why It is Useful

Without polymorphism you need `if` / `elif` for every type:

```python
def total_area(shapes):
    total = 0
    for s in shapes:
        if isinstance(s, Rectangle):
            total += s.w * s.h
        elif isinstance(s, Circle):
            total += 3.14159 * s.r ** 2
        # and one more elif for every new shape...
    return total
```
With polymorphism the function never changes when you add a new shape:

```python
class Square(Shape):
    def __init__(self, side):
        self.side = side

    def area(self):
        return self.side ** 2

def total_area(shapes):
    return sum(s.area() for s in shapes)

print(total_area([Rectangle(3, 4), Square(5)]))
# Output: 37
```
Adding `Square` required **no change** to `total_area`. Code is easy to extend and short.

---

## Duck Typing

> "If it walks like a duck and quacks like a duck, it is a duck."

Python does not check the **type** of an object, only whether it has the **method** you call. This is **duck typing**.

```python
class Duck:
    def speak(self):
        return "Quack"

class Dog:
    def speak(self):
        return "Woof"

class Robot:
    def speak(self):
        return "Beep"

def make_it_speak(thing):
    print(thing.speak())

for obj in (Duck(), Dog(), Robot()):
    make_it_speak(obj)
# Output: Quack
# Output: Woof
# Output: Beep
```

---

## Polymorphism Without Inheritance

In the example above `Duck`, `Dog` and `Robot` have **no common parent**. They only share a method name. In Python, inheritance is **not required** for polymorphism (unlike in languages such as Java or C++). Inheritance is useful when classes also share **code**.

---

## When an Object Does Not Fit

Duck typing fails at **run time** if the object lacks the method.

```python
class Rock:
    pass

def make_it_speak(thing):
    print(thing.speak())

make_it_speak(Rock())
# Error:  AttributeError: 'Rock' object has no attribute 'speak'
```
You can check first, or handle the error:
```python
class Rock:
    pass

thing = Rock()
if hasattr(thing, "speak"):
    print(thing.speak())
else:
    print("cannot speak")

try:
    thing.speak()
except AttributeError:
    print("cannot speak")
# Output: cannot speak
# Output: cannot speak
```
Python prefers `try` / `except` (EAFP, see the try / except note). To **force** every child to provide a method, use an abstract class (see the Abstraction note).

---

## Operator Polymorphism

With special methods (see the Special Methods note) your own classes can support operators like `+`, `==` and `len()`.

```python
class Money:
    def __init__(self, amount):
        self.amount = amount

    def __add__(self, other):
        return Money(self.amount + other.amount)

    def __str__(self):
        return f"{self.amount} INR"

print(Money(50) + Money(25))
# Output: 75 INR
```
`+` now means "add two Money objects", just as it means different things for numbers, strings and lists.

---

## Polymorphic Functions

Many functions work with **any** objects that support the operations they need.

```python
def double(x):
    return x * 2

print(double(5))
print(double("ab"))
print(double([1, 2]))
# Output: 10
# Output: abab
# Output: [1, 2, 1, 2]
```
`*` behaves differently for numbers, strings and lists, so `double` is polymorphic without any extra code.

---

## Overloading Does Not Exist

Some languages allow **method overloading**: several methods with the same name but different parameters. **Python does not.** If you define the same name twice, the **second replaces the first**.

```python
class Calc:
    def add(self, a, b):
        return a + b

    def add(self, a, b, c):          # replaces the first add
        return a + b + c

print(Calc().add(1, 2, 3))
# Output: 6
```
`Calc().add(1, 2)` would now fail with `TypeError: Calc.add() missing 1 required positional argument: 'c'`. Instead use **default arguments** or `*args`:

```python
class Calc:
    def add(self, *numbers):
        return sum(numbers)

print(Calc().add(1, 2))
print(Calc().add(1, 2, 3))
# Output: 3
# Output: 6
```

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Using a different method name in each class (`bark`, `meow`) | The shared code cannot call them uniformly | Give them the **same name** (`speak`) |
| Different parameters in the overriding method | `TypeError` when called through the parent's interface | Keep the same parameters |
| Checking `type(x) == ...` in long `if` chains | Code breaks when a new class is added | Call the method and let the object decide |
| Passing an object without the method | `AttributeError` | Provide the method, check with `hasattr`, or use an abstract base class |
| Expecting method overloading | The later definition replaces the earlier | Default arguments or `*args` |
| Forgetting that `+` on mixed types fails (`"a" + 1`) | `TypeError` | Convert, or define `__add__` / `__radd__` |

---

- [Back to section index](./README.md)
