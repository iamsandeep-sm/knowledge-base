# Instance and Class Attributes - Revision Notes ~ sm

> **In one line:** An **instance attribute** belongs to **one object** (set with `self.x`), while a **class attribute** belongs to the **class** and is **shared** by all its objects. Methods come in three kinds too: **instance methods** (use `self`), **class methods** (`@classmethod`, use `cls`) and **static methods** (`@staticmethod`, use neither).

## Table of Contents
- [Two Kinds of Attributes](#two-kinds-of-attributes)
- [Instance Attributes](#instance-attributes)
- [Class Attributes](#class-attributes)
- [Instance vs Class: Side by Side](#instance-vs-class-side-by-side)
- [How Python Finds an Attribute](#how-python-finds-an-attribute)
- [Assigning Through an Object Does Not Change the Class](#assigning-through-an-object-does-not-change-the-class)
- [Changing a Class Attribute](#changing-a-class-attribute)
- [Counting Objects](#counting-objects)
- [The Mutable Class Attribute Trap](#the-mutable-class-attribute-trap)
- [Looking Inside: __dict__](#looking-inside-__dict__)
- [Three Kinds of Methods](#three-kinds-of-methods)
- [Class Methods](#class-methods)
- [Static Methods](#static-methods)
- [Which One to Use](#which-one-to-use)
- [Common Mistakes](#common-mistakes)

---

## Two Kinds of Attributes

Imagine a school.
- Every **student** has their own name and marks. This differs for each student: **instance attributes**.
- The **school name** is the same for every student. It belongs to the school, not to a single student: a **class attribute**.

---

## Instance Attributes

Created with `self.something` (normally inside `__init__`). Every object has its **own copy**.

```python
class Student:
    def __init__(self, name):
        self.name = name

a = Student("Riya")
b = Student("Amit")
a.name = "Zoya"

print(a.name, b.name)
# Output: Zoya Amit
```

---

## Class Attributes

Created **directly in the class body**, outside any method. There is **one copy**, shared by the class and all its objects.

```python
class Student:
    school = "Green Valley"          # class attribute

    def __init__(self, name):
        self.name = name             # instance attribute

a = Student("Riya")
b = Student("Amit")

print(a.school, b.school)
print(Student.school)
# Output: Green Valley Green Valley
# Output: Green Valley
```
Access it through an object (`a.school`) or, better, through the **class** (`Student.school`), which makes it clear it is shared.

---

## Instance vs Class: Side by Side

| | Instance attribute | Class attribute |
|---|---|---|
| Defined | In a method with `self.x = ...` (usually `__init__`) | In the class body: `x = ...` |
| Belongs to | One object | The class (shared) |
| Copies | One per object | One in total |
| Access | `obj.x` | `Class.x` (or `obj.x`) |
| Use for | Data that differs between objects (name, balance) | Constants and shared values (school name, tax rate, counters) |

---

## How Python Finds an Attribute

For `obj.x`, Python looks **first in the object**, and if it is not there, **then in the class** (and then in the parent classes, see Inheritance).

```python
class Student:
    school = "Green Valley"

a = Student()
print(a.school)         # not in a, found in the class
print("school" in vars(a))
# Output: Green Valley
# Output: False
```
`vars(a)` shows only the object's **own** attributes, which is empty here.

---

## Assigning Through an Object Does Not Change the Class

Writing `obj.x = value` always creates or changes an **instance** attribute, even if a class attribute with that name exists. It then **hides** the class one for that object only.

```python
class Student:
    school = "Green Valley"

a = Student()
b = Student()
a.school = "Sunrise"           # creates an instance attribute on a only

print(a.school)
print(b.school)
print(Student.school)
# Output: Sunrise
# Output: Green Valley
# Output: Green Valley
```

---

## Changing a Class Attribute

To change the shared value for everybody, assign through the **class**.

```python
class Student:
    school = "Green Valley"

a = Student()
b = Student()
Student.school = "Sunrise"

print(a.school, b.school)
# Output: Sunrise Sunrise
```
(Objects that already hid the attribute with their own copy keep their own value.)

---

## Counting Objects

A classic use of a class attribute: keep a counter shared by all objects. Change it with `ClassName.counter`, **not** `self.counter += 1`.

```python
class Student:
    count = 0

    def __init__(self, name):
        self.name = name
        Student.count += 1

Student("Riya")
Student("Amit")
Student("Sam")
print(Student.count)
# Output: 3
```
If you wrote `self.count += 1` instead, Python would read the class value, add one, and store the result as a **new instance attribute**, leaving the class counter at `0`.

```python
class Wrong:
    count = 0
    def __init__(self):
        self.count += 1          # creates an instance attribute

Wrong()
Wrong()
print(Wrong.count)
# Output: 0
```

---

## The Mutable Class Attribute Trap

A **list or dictionary** defined as a class attribute is shared by **all** objects. Changing it through one object changes it for every object.

```python
class Cart:
    items = []                   # BAD: shared by all carts

    def add(self, item):
        self.items.append(item)

a = Cart()
b = Cart()
a.add("pen")
print(b.items)
# Output: ['pen']
```
Per-object lists belong in `__init__`:
```python
class Cart:
    def __init__(self):
        self.items = []          # GOOD: each cart has its own list

    def add(self, item):
        self.items.append(item)

a = Cart()
b = Cart()
a.add("pen")
print(b.items)
# Output: []
```

---

## Looking Inside: __dict__

Both the object and the class keep their attributes in a dictionary.

```python
class Student:
    school = "Green Valley"

    def __init__(self, name):
        self.name = name

s = Student("Riya")
print(s.__dict__)
print("school" in Student.__dict__)
# Output: {'name': 'Riya'}
# Output: True
```
The object's `__dict__` holds only the instance attributes. The class attribute lives in the class's `__dict__`.

---

## Three Kinds of Methods

| Kind | Decorator | First parameter | Can use |
|---|---|---|---|
| **Instance method** | none | `self` (the object) | Instance and class data |
| **Class method** | `@classmethod` | `cls` (the class) | Class data only |
| **Static method** | `@staticmethod` | none | Neither (a plain function in the class) |

```python
class Circle:
    pi = 3.14159

    def __init__(self, r):
        self.r = r

    def area(self):                      # instance method
        return Circle.pi * self.r ** 2

    @classmethod
    def unit(cls):                       # class method
        return cls(1)

    @staticmethod
    def is_valid_radius(r):              # static method
        return r > 0

c = Circle(2)
print(round(c.area(), 2))
print(Circle.unit().r)
print(Circle.is_valid_radius(-5))
# Output: 12.57
# Output: 1
# Output: False
```

---

## Class Methods

`@classmethod` receives the **class** as `cls`, not an object. Two common uses:

**1. Alternative constructors** (create objects from different input)
```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    @classmethod
    def from_string(cls, text):
        name, age = text.split("-")
        return cls(name, int(age))

p = Person.from_string("Riya-20")
print(p.name, p.age)
# Output: Riya 20
```
Using `cls(...)` instead of `Person(...)` means child classes automatically create objects of **their own** type.

**2. Working with class attributes**
```python
class Student:
    school = "Green Valley"

    @classmethod
    def change_school(cls, new_name):
        cls.school = new_name

Student.change_school("Sunrise")
print(Student.school)
# Output: Sunrise
```

---

## Static Methods

`@staticmethod` is an ordinary function placed inside the class because it **belongs to the topic** of the class. It gets neither `self` nor `cls`.

```python
class MathTools:
    @staticmethod
    def is_even(n):
        return n % 2 == 0

print(MathTools.is_even(10))
print(MathTools().is_even(7))
# Output: True
# Output: False
```
If a method uses neither the object nor the class, make it a static method (or even a plain function outside the class).

---

## Which One to Use

| Question | Answer |
|---|---|
| Does it need the object's own data? | Instance method (`self`) |
| Does it work with the class itself or create objects of the class? | Class method (`cls`) |
| Does it need neither? | Static method |
| Is the value different for every object? | Instance attribute |
| Is the value the same for all objects? | Class attribute |

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| `self.count += 1` for a shared counter | Makes a new instance attribute; the class counter stays | `ClassName.count += 1` |
| Mutable class attribute (`items = []`) | Shared by all objects | Create it in `__init__` |
| `a.school = "X"` expecting all to change | Only `a` changes | `Student.school = "X"` |
| Using `school` without `self.` or `Student.` inside a method | `NameError` | `self.school` or `Student.school` |
| Forgetting `cls` or `self` in the method | `TypeError ... positional arguments` | Add the first parameter |
| Forgetting `@classmethod` / `@staticmethod` | Python passes the object as the first argument | Add the decorator |
| Making everything a class attribute | All objects share one value | Instance data goes in `__init__` |

---

- [Back to section index](./README.md)
