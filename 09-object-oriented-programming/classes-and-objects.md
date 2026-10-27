# Classes and Objects - Revision Notes ~ sm

> **In one line:** A **class** is a **blueprint** that describes what something has (data) and can do (functions). An **object** (also called an **instance**) is a real thing made from that blueprint. You create objects by calling the class like a function: `Dog("Tommy")`.

## Table of Contents
- [What is Object-Oriented Programming?](#what-is-object-oriented-programming)
- [Class vs Object](#class-vs-object)
- [Your First Class](#your-first-class)
- [Creating Objects](#creating-objects)
- [Attributes: the Data of an Object](#attributes-the-data-of-an-object)
- [Methods: the Actions of an Object](#methods-the-actions-of-an-object)
- [What is self?](#what-is-self)
- [Objects are Independent](#objects-are-independent)
- [Everything in Python is an Object](#everything-in-python-is-an-object)
- [Useful Built-in Functions for Objects](#useful-built-in-functions-for-objects)
- [Printing an Object](#printing-an-object)
- [A Complete Small Example](#a-complete-small-example)
- [Objects Share References](#objects-share-references)
- [Naming and Style](#naming-and-style)
- [Common Mistakes](#common-mistakes)

---

## What is Object-Oriented Programming?

So far we stored data in variables and wrote separate functions. In **object-oriented programming (OOP)** we **group the data and the functions that work on it together** into one unit: an object.

Think of a **student**. A student has a name, a roll number and marks (data), and can calculate a percentage or print a report card (actions). Instead of passing separate variables around, we keep them together.

| OOP idea | Meaning | Note |
|---|---|---|
| **Class / object** | Blueprint and the real things made from it | this note |
| **Constructor** | Sets up a new object | Constructors |
| **Inheritance** | A class reuses another class | Inheritance |
| **Encapsulation** | Protect and control the data | Encapsulation |
| **Polymorphism** | Same call, different behaviour | Polymorphism |
| **Abstraction** | Show *what*, hide *how* | Abstraction |

---

## Class vs Object

| | Class | Object (instance) |
|---|---|---|
| Idea | The **blueprint** or design | A **real thing** built from the design |
| Example | The plan of a house | One actual house |
| Example | The idea "Dog" | Tommy the dog, Bruno the dog |
| How many | One | As many as you like |
| Created with | the `class` keyword | calling the class: `Dog()` |

One class can produce many objects, each with its own data.

---

## Your First Class

```python
class Dog:
    pass
```
- `class` starts the definition, followed by the **name** and a colon.
- The indented block is the class **body**. `pass` means "empty for now".

Even this empty class can make objects.

---

## Creating Objects

```python
class Dog:
    pass

tommy = Dog()
bruno = Dog()

print(type(tommy))
print(tommy == bruno)
print(isinstance(tommy, Dog))
# Output: <class '__main__.Dog'>
# Output: False
# Output: True
```
`Dog()` creates a **new object** every time, so `tommy` and `bruno` are different objects. Making an object is called **instantiation**.

---

## Attributes: the Data of an Object

An **attribute** is a variable that belongs to an object. Write it as `object.attribute`.

```python
class Dog:
    pass

tommy = Dog()
tommy.name = "Tommy"
tommy.age = 3

print(tommy.name, tommy.age)
# Output: Tommy 3
```
Setting attributes one by one like this is tedious and easy to forget. The **constructor** (`__init__`, next note) sets them when the object is created, which is the normal way.

---

## Methods: the Actions of an Object

A **method** is a function defined **inside a class**. It works on the object's data.

```python
class Dog:
    def bark(self):
        print("Woof!")

    def introduce(self):
        print("I am", self.name)

tommy = Dog()
tommy.name = "Tommy"

tommy.bark()
tommy.introduce()
# Output: Woof!
# Output: I am Tommy
```
Call a method with a dot and brackets: `object.method()`.

---

## What is self?

Every method receives the **object itself** as its **first parameter**. By convention it is named **`self`**.

```python
class Counter:
    def __init__(self):
        self.value = 0

    def add(self, amount):
        self.value += amount

c = Counter()
c.add(5)
c.add(2)
print(c.value)
# Output: 7
```
When you write `c.add(5)`, Python quietly turns it into `Counter.add(c, 5)`: `c` becomes `self`, and `5` becomes `amount`. That is why a method with two parameters is called with one argument.

| Rule | Detail |
|---|---|
| `self` is the **first** parameter of every instance method | `def add(self, amount):` |
| You do **not** pass it yourself | `c.add(5)`, not `c.add(c, 5)` |
| It lets the method reach the object's data | `self.value` |
| Inside the method, data without `self.` is just a local variable | `value = 3` does not change the object |
| The name `self` is only a convention | Always use `self` |

---

## Objects are Independent

Each object keeps its **own** data.

```python
class Dog:
    def __init__(self, name):
        self.name = name

a = Dog("Tommy")
b = Dog("Bruno")
a.name = "Max"

print(a.name)
print(b.name)
# Output: Max
# Output: Bruno
```
Changing `a` does not affect `b`.

---

## Everything in Python is an Object

Numbers, strings, lists and functions are all objects, made from built-in classes.

```python
print(type(5))
print(type("hi"))
print(type([1, 2]))
print("hi".upper())
# Output: <class 'int'>
# Output: <class 'str'>
# Output: <class 'list'>
# Output: HI
```
`"hi".upper()` is a method call on a `str` object. You already use OOP without noticing: now you can write your own classes.

---

## Useful Built-in Functions for Objects

| Function | What it does | Example |
|---|---|---|
| `type(obj)` | The class of an object | `type(tommy)` |
| `isinstance(obj, Class)` | Is the object made from that class (or a child class)? | `isinstance(tommy, Dog)` |
| `hasattr(obj, "name")` | Does the attribute exist? | `hasattr(tommy, "age")` |
| `getattr(obj, "name", default)` | Read an attribute by its name (with a default) | `getattr(tommy, "age", 0)` |
| `setattr(obj, "name", value)` | Set an attribute by its name | `setattr(tommy, "age", 4)` |
| `vars(obj)` or `obj.__dict__` | A dictionary of the object's attributes | `vars(tommy)` |
| `dir(obj)` | All names the object has | `dir(tommy)` |
| `id(obj)` | A number that identifies the object | `id(tommy)` |

```python
class Dog:
    def __init__(self, name):
        self.name = name

d = Dog("Tommy")
print(hasattr(d, "name"), hasattr(d, "age"))
print(getattr(d, "age", "unknown"))
setattr(d, "age", 4)
print(vars(d))
# Output: True False
# Output: unknown
# Output: {'name': 'Tommy', 'age': 4}
```

---

## Printing an Object

By default, printing an object shows its class and memory address, which is not helpful.

```python
class Dog:
    pass

print(Dog())
# Output: <__main__.Dog object at 0x...>
```
(The address after `0x` is different every run.) Define the special method `__str__` to show something readable. This is explained in the Special Methods note.

---

## A Complete Small Example

```python
class BankAccount:
    def __init__(self, owner, balance=0):
        self.owner = owner
        self.balance = balance

    def deposit(self, amount):
        self.balance += amount

    def withdraw(self, amount):
        if amount > self.balance:
            print("Not enough money")
        else:
            self.balance -= amount

    def show(self):
        print(f"{self.owner}: {self.balance}")

acc = BankAccount("Riya", 100)
acc.deposit(50)
acc.withdraw(500)
acc.withdraw(30)
acc.show()
# Output: Not enough money
# Output: Riya: 120
```
The data (`owner`, `balance`) and the actions (`deposit`, `withdraw`, `show`) live together in one place.

---

## Objects Share References

A variable holds a **reference** to an object, so copying a variable does not copy the object.

```python
class Box:
    def __init__(self):
        self.items = []

a = Box()
b = a                 # same object, two names
b.items.append("pen")
print(a.items)
print(a is b)
# Output: ['pen']
# Output: True
```
To get an independent copy use the `copy` module (`copy.copy(a)` or `copy.deepcopy(a)`). The same idea was explained in the Mutability note.

---

## Naming and Style

| Item | Style | Example |
|---|---|---|
| Class name | `CapWords` (each word starts with a capital) | `BankAccount`, `Dog` |
| Method and attribute | `snake_case` | `get_balance`, `first_name` |
| Instance parameter | always `self` | `def show(self):` |

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Forgetting `self` in a method: `def bark():` | `TypeError: Dog.bark() takes 0 positional arguments but 1 was given` | `def bark(self):` |
| Calling a method on the class: `Dog.bark()` | `TypeError: Dog.bark() missing 1 required positional argument: 'self'` | Make an object first: `Dog().bark()` |
| Writing `name` instead of `self.name` inside a method | `NameError`, or only a local variable is changed | Use `self.name` |
| Forgetting the brackets: `tommy = Dog` | `tommy` is the class, not an object | `tommy = Dog()` |
| Reading an attribute that was never set | `AttributeError: 'Dog' object has no attribute 'nope'` | Set it in `__init__` |
| Passing arguments to a class that has no `__init__` | `TypeError: Dog() takes no arguments` | Add `__init__` |
| Thinking `b = a` makes a copy | Both names refer to one object | `copy.copy(a)` |

---

- [Back to section index](./README.md)
