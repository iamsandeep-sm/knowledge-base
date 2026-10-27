# Inheritance - Revision Notes ~ sm

> **In one line:** **Inheritance** lets a new class (the **child** or **subclass**) **reuse** everything from an existing class (the **parent** or **base class**) and then add or change things. Write it as `class Child(Parent):`.

## Table of Contents
- [Why Inheritance?](#why-inheritance)
- [Basic Syntax](#basic-syntax)
- [Adding New Things in the Child](#adding-new-things-in-the-child)
- [Overriding a Method](#overriding-a-method)
- [super()](#super)
- [How Python Looks Up a Method](#how-python-looks-up-a-method)
- [isinstance() and issubclass()](#isinstance-and-issubclass)
- [Types of Inheritance](#types-of-inheritance)
- [Multiple Inheritance and MRO](#multiple-inheritance-and-mro)
- [The Diamond Problem and super()](#the-diamond-problem-and-super)
- [Everything Inherits from object](#everything-inherits-from-object)
- [is-a vs has-a (Composition)](#is-a-vs-has-a-composition)
- [Inheriting from Built-in Types and Exceptions](#inheriting-from-built-in-types-and-exceptions)
- [Common Mistakes](#common-mistakes)

---

## Why Inheritance?

A **Dog**, a **Cat** and a **Cow** are all **animals**. They share a name, can eat and can sleep. Without inheritance we would copy that code into every class. With inheritance we write it **once** in an `Animal` class, and each animal class reuses it.

| Benefit | Meaning |
|---|---|
| **Reuse** | Write common code once |
| **Organisation** | Mirrors real-world "is a" relationships (a Dog **is an** Animal) |
| **Easy change** | Fix the parent, and every child gets the fix |
| **Extension** | Children add or change behaviour |

---

## Basic Syntax

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def eat(self):
        print(self.name, "is eating")

class Dog(Animal):          # Dog inherits from Animal
    pass

d = Dog("Tommy")
d.eat()
print(d.name)
# Output: Tommy is eating
# Output: Tommy
```
`Dog` has no code, yet it already has `__init__`, `name` and `eat()` because it inherited them.

| Term | Other names |
|---|---|
| `Animal` | parent class, base class, superclass |
| `Dog` | child class, derived class, subclass |

---

## Adding New Things in the Child

The child can have extra methods that the parent does not have.

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def eat(self):
        print(self.name, "is eating")

class Dog(Animal):
    def bark(self):
        print(self.name, "says Woof!")

d = Dog("Tommy")
d.eat()
d.bark()
# Output: Tommy is eating
# Output: Tommy says Woof!
```
The reverse does not work: an `Animal` object has no `bark()`.

---

## Overriding a Method

If the child defines a method **with the same name** as the parent's, the child's version is used. This is **method overriding**.

```python
class Animal:
    def sound(self):
        print("Some sound")

class Dog(Animal):
    def sound(self):
        print("Woof")

class Cat(Animal):
    def sound(self):
        print("Meow")

class Fish(Animal):
    pass

Dog().sound()
Cat().sound()
Fish().sound()
# Output: Woof
# Output: Meow
# Output: Some sound
```
`Fish` does not override, so it uses the parent's version.

---

## super()

`super()` gives access to the **parent's** version of a method. It is most often used to run the parent's `__init__`, so the child does not repeat that code.

```python
class Animal:
    def __init__(self, name):
        self.name = name

class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name)         # let Animal set the name
        self.breed = breed

d = Dog("Tommy", "Labrador")
print(d.name, d.breed)
# Output: Tommy Labrador
```
**Important:** if the child defines its own `__init__` and does **not** call `super().__init__(...)`, the parent's `__init__` is **skipped**, and the parent's attributes are never created.

```python
class Animal:
    def __init__(self, name):
        self.name = name

class Dog(Animal):
    def __init__(self, breed):
        self.breed = breed             # forgot super().__init__

d = Dog("Labrador")
print(d.name)
# Error:  AttributeError: 'Dog' object has no attribute 'name'
```

`super()` also extends a normal method instead of replacing it:
```python
class Animal:
    def describe(self):
        return "I am an animal"

class Dog(Animal):
    def describe(self):
        return super().describe() + " and a dog"

print(Dog().describe())
# Output: I am an animal and a dog
```

---

## How Python Looks Up a Method

For `obj.method()`, Python searches the object's class first, then the parent, then the grandparent, and so on, and uses the **first match**.

```python
class A:
    def hello(self):
        return "A"

class B(A):
    pass

class C(B):
    def hello(self):
        return "C"

print(B().hello())
print(C().hello())
# Output: A
# Output: C
```
`B` has no `hello`, so Python goes up to `A`. `C` has its own and uses it.

---

## isinstance() and issubclass()

```python
class Animal: pass
class Dog(Animal): pass

d = Dog()
print(isinstance(d, Dog))
print(isinstance(d, Animal))        # a Dog is also an Animal
print(isinstance(Animal(), Dog))    # an Animal is not necessarily a Dog
print(issubclass(Dog, Animal))
print(type(d) == Animal)
# Output: True
# Output: True
# Output: False
# Output: True
# Output: False
```

| Function | Question it answers |
|---|---|
| `isinstance(obj, Class)` | Is this object of that class **or any child** of it? |
| `issubclass(Child, Parent)` | Is this class derived from that class? |
| `type(obj) == Class` | Is it **exactly** that class (children do not count)? |

Prefer `isinstance` over `type(...) ==`.

---

## Types of Inheritance

| Type | Shape | Example |
|---|---|---|
| **Single** | One parent | `class B(A)` |
| **Multilevel** | A chain | `class C(B)` where `class B(A)` |
| **Hierarchical** | Many children, one parent | `class B(A)`, `class C(A)` |
| **Multiple** | Many parents | `class C(A, B)` |

```python
class Animal:
    def eat(self):
        return "eating"

class Dog(Animal):          # single
    pass

class Puppy(Dog):           # multilevel: Animal -> Dog -> Puppy
    pass

print(Puppy().eat())
# Output: eating
```

---

## Multiple Inheritance and MRO

Python allows a class to have **several parents**.

```python
class Flyer:
    def move(self):
        return "fly"

class Swimmer:
    def move(self):
        return "swim"

class Duck(Flyer, Swimmer):
    pass

print(Duck().move())
# Output: fly
```
Both parents have `move()`. Python picks the one from the **first parent listed**. The exact search order is the **MRO** (Method Resolution Order), which you can print:

```python
print([c.__name__ for c in Duck.__mro__])
# Output: ['Duck', 'Flyer', 'Swimmer', 'object']
```
`Duck.mro()` gives the same list. The search always goes: the class itself, its parents left to right (without visiting a class before all of its children), and finally `object`.

---

## The Diamond Problem and super()

When two parents share a **common grandparent**, you get a diamond shape.

```python
class A:
    def hello(self):
        print("A")

class B(A):
    def hello(self):
        print("B")
        super().hello()

class C(A):
    def hello(self):
        print("C")
        super().hello()

class D(B, C):
    def hello(self):
        print("D")
        super().hello()

D().hello()
print([c.__name__ for c in D.__mro__])
# Output: D
# Output: B
# Output: C
# Output: A
# Output: ['D', 'B', 'C', 'A', 'object']
```
`super()` does **not** simply mean "the parent". It means "the **next class in the MRO**". That is why `B`'s `super()` leads to `C` here, and `A` runs only **once**. Multiple inheritance is powerful but confusing, so beginners should prefer single inheritance and composition.

---

## Everything Inherits from object

Every class inherits from the built-in class `object`, even when you do not write it.

```python
class A:
    pass

print(A.__bases__)
print(isinstance(A(), object))
print(isinstance(5, object))
# Output: (<class 'object'>,)
# Output: True
# Output: True
```
`object` is where default behaviour such as `__init__`, `__str__` and `__eq__` comes from, and where you can override it.

---

## is-a vs has-a (Composition)

Use inheritance only for an **"is a"** relationship. For a **"has a"** relationship, make an object a **part** of another object. This is called **composition**.

| Relationship | Use | Example |
|---|---|---|
| A Dog **is an** Animal | Inheritance | `class Dog(Animal)` |
| A Car **has an** Engine | Composition | `self.engine = Engine()` |

```python
class Engine:
    def start(self):
        return "engine started"

class Car:
    def __init__(self):
        self.engine = Engine()          # a Car HAS an Engine

    def start(self):
        return self.engine.start()

print(Car().start())
# Output: engine started
```
Making `Car` inherit from `Engine` would be wrong, because a car is not an engine. Many designs prefer composition because it is simpler and more flexible.

---

## Inheriting from Built-in Types and Exceptions

You can extend built-in classes.

```python
class MyList(list):
    def second(self):
        return self[1]

m = MyList([10, 20, 30])
m.append(40)
print(m.second(), len(m))
# Output: 20 4
```
Custom exceptions (see the Raising Exceptions note) are also inheritance: `class MyError(Exception): pass`.

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Child `__init__` without `super().__init__(...)` | Parent's attributes are missing (`AttributeError`) | Call `super().__init__(...)` |
| Wrong arguments to `super().__init__` | `TypeError` | Match the parent's parameters |
| Using inheritance for "has a" | Illogical and rigid design | Use composition |
| Writing `class Dog(animal)` (a variable) | `NameError` | Spell the class name exactly |
| Defining the child before the parent | `NameError` | Define the parent first |
| Deep inheritance chains | Hard to follow | Keep hierarchies shallow |
| `type(x) == Parent` for checking | Children are rejected | `isinstance(x, Parent)` |
| Confusing `super()` with "my parent" in multiple inheritance | Unexpected call order | Check `Class.__mro__` |

---

- [Back to section index](./README.md)
