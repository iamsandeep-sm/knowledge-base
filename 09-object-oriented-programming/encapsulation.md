# Encapsulation - Revision Notes ~ sm

> **In one line:** **Encapsulation** means **bundling data with the methods that work on it, and controlling access to that data**, so it cannot be changed carelessly from outside. In Python this is done with naming conventions (`_name`, `__name`) and **properties** (`@property`).

## Table of Contents
- [What is Encapsulation?](#what-is-encapsulation)
- [The Problem: Unprotected Data](#the-problem-unprotected-data)
- [Public, Protected and Private](#public-protected-and-private)
- [Protected: One Underscore](#protected-one-underscore)
- [Private: Two Underscores (Name Mangling)](#private-two-underscores-name-mangling)
- [Getter and Setter Methods](#getter-and-setter-methods)
- [Properties: the Pythonic Way](#properties-the-pythonic-way)
- [Read-only Properties](#read-only-properties)
- [Computed Properties](#computed-properties)
- [Deleting with a Property](#deleting-with-a-property)
- [Encapsulation vs Abstraction](#encapsulation-vs-abstraction)
- [Best Practices](#best-practices)
- [Common Mistakes](#common-mistakes)

---

## What is Encapsulation?

Think of a **capsule** or an **ATM**. You press buttons (the public interface) and the machine controls what happens to the money inside. You cannot reach in and take cash yourself.

Encapsulation has two parts:
1. **Bundling:** keep data and methods together in a class.
2. **Protecting:** let outside code use the data **only through safe methods**, which can check the values.

---

## The Problem: Unprotected Data

```python
class Account:
    def __init__(self, balance):
        self.balance = balance

acc = Account(100)
acc.balance = -999999       # nothing stops this
print(acc.balance)
# Output: -999999
```
Anyone can put the object into an invalid state. Encapsulation prevents that.

---

## Public, Protected and Private

Many languages have `public`, `protected` and `private` keywords. **Python has no such keywords.** It uses **naming conventions** instead:

| Name | Level | Meaning | Enforced by Python? |
|---|---|---|---|
| `name` | **Public** | Anyone may use it | - |
| `_name` | **Protected** | "Internal, please do not touch from outside" | **No**, a convention only |
| `__name` | **Private** | Hidden by **name mangling** | Partly (the name is changed) |

Python's philosophy: *"We are all consenting adults."* It trusts you to follow the convention.

---

## Protected: One Underscore

A single leading underscore is a **signal** to other programmers: "this is an internal detail".

```python
class Account:
    def __init__(self, balance):
        self._balance = balance      # internal

    def get_balance(self):
        return self._balance

acc = Account(100)
print(acc.get_balance())
print(acc._balance)                  # works, but you were warned
# Output: 100
# Output: 100
```
It is still reachable. The underscore is purely a message. Child classes may use `_name` attributes freely.

---

## Private: Two Underscores (Name Mangling)

A name that starts with **two** underscores (and does not end with two) is **mangled**: Python renames it to `_ClassName__name`.

```python
class Account:
    def __init__(self, balance):
        self.__balance = balance

    def get_balance(self):
        return self.__balance

acc = Account(100)
print(acc.get_balance())
print(acc.__dict__)
# Output: 100
# Output: {'_Account__balance': 100}
```
```python
acc = Account(100)
acc.__balance
# Error:  AttributeError: 'Account' object has no attribute '__balance'
```
Outside the class the plain name `__balance` does not exist, which protects it from **accidental** use. But it is **not secure**: `acc._Account__balance` still reaches it. Name mangling mainly prevents **name clashes between a parent and a child class** that happen to use the same attribute name.

---

## Getter and Setter Methods

The traditional approach (as in Java): private data plus `get_` and `set_` methods that can **validate**.

```python
class Account:
    def __init__(self, balance):
        self.__balance = balance

    def get_balance(self):
        return self.__balance

    def set_balance(self, value):
        if value < 0:
            raise ValueError("balance cannot be negative")
        self.__balance = value

acc = Account(100)
acc.set_balance(250)
print(acc.get_balance())
# Output: 250
```
This works, but writing `acc.set_balance(250)` instead of `acc.balance = 250` is clumsy. Python has a nicer way.

---

## Properties: the Pythonic Way

`@property` lets you call methods using **plain attribute syntax**, while still running your validation code.

```python
class Account:
    def __init__(self, balance):
        self.balance = balance          # goes through the setter below

    @property
    def balance(self):                  # getter
        return self._balance

    @balance.setter
    def balance(self, value):           # setter
        if value < 0:
            raise ValueError("balance cannot be negative")
        self._balance = value

acc = Account(100)
acc.balance = 250                       # runs the setter
print(acc.balance)                      # runs the getter

try:
    acc.balance = -5
except ValueError as e:
    print("Error:", e)
# Output: 250
# Output: Error: balance cannot be negative
```

| Piece | Job |
|---|---|
| `@property` above `def balance(self)` | Defines the **getter**: runs when you **read** `acc.balance` |
| `@balance.setter` above a method of the **same name** | Defines the **setter**: runs when you **assign** `acc.balance = ...` |
| `self._balance` | The real storage. A **different name** (with `_`) is needed, otherwise the property would call itself forever |

Because `__init__` also uses `self.balance = balance`, even the **starting value** is validated.

Users of the class write simple `acc.balance` and never need to know that checking happens behind the scenes. You can also start with a plain attribute and **add a property later** without changing any code that uses the class. That is why Python programmers do not write getters and setters "just in case".

---

## Read-only Properties

A property with **only a getter** cannot be assigned.

```python
class Circle:
    def __init__(self, radius):
        self.radius = radius

    @property
    def diameter(self):
        return self.radius * 2

c = Circle(5)
print(c.diameter)
c.diameter = 20
# Output: 10
# Error:  AttributeError: property 'diameter' of 'Circle' object has no setter
```
(The wording of this message is slightly different in older Python versions.)

---

## Computed Properties

A property can **calculate** a value each time, so it never goes out of date. Compare with an attribute set in `__init__`, which is calculated only once.

```python
class Rectangle:
    def __init__(self, width, height):
        self.width = width
        self.height = height

    @property
    def area(self):
        return self.width * self.height

r = Rectangle(3, 4)
print(r.area)
r.width = 10
print(r.area)
# Output: 12
# Output: 40
```
`area` always reflects the current `width` and `height`.

---

## Deleting with a Property

A `@x.deleter` method runs on `del obj.x`. It is rarely needed.

```python
class Box:
    def __init__(self):
        self._items = [1, 2]

    @property
    def items(self):
        return self._items

    @items.deleter
    def items(self):
        self._items = []

b = Box()
del b.items
print(b.items)
# Output: []
```

---

## Encapsulation vs Abstraction

They are often confused.

| | Encapsulation | Abstraction |
|---|---|---|
| Idea | **Protect** data and control access | **Hide complexity**, show only what is needed |
| Question | "Who may touch this data?" | "What does this do, without how?" |
| Python tools | `_x`, `__x`, `@property` | `abc` module, abstract classes |
| Example | Balance can only change through `deposit()` | You call `car.start()` without knowing how the engine works |

---

## Best Practices

- Start with **public** attributes. Add `@property` only when you need validation or a computed value.
- Use `_name` for things that are internal to the class.
- Use `__name` only when you really need to avoid clashes in subclasses.
- Change data through **methods** that keep the object valid (`deposit`, `withdraw`) rather than letting outside code set raw numbers.
- Validate in the **setter** (or in `__init__`), and raise `ValueError` / `TypeError` for bad values.
- Never rely on `__private` for security. It is not a security feature.

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Storing in the same name as the property (`self.balance = value` inside the setter) | `RecursionError` (the setter calls itself) | Store in `self._balance` |
| `@balance.setter` placed before `@property` is defined | `NameError` | Define the getter first |
| Different names for getter and setter methods | The setter does not attach to the property | Use the **same name** |
| Reading `obj.__x` from outside | `AttributeError` (name mangling) | Provide a getter or property |
| Assigning to a read-only property | `AttributeError: property 'x' of 'C' object has no setter` | Add a setter, or do not assign |
| Believing `__x` is truly private | `obj._Class__x` still works | It is a convention, not security |
| Writing getters and setters for every attribute | Extra code with no benefit | Use plain attributes until you need a property |

---

- [Back to section index](./README.md)
