# Object-Oriented Programming Practice

Related notes: [Classes and Objects](../../09-object-oriented-programming/classes-and-objects.md), [Constructors](../../09-object-oriented-programming/constructors.md), [Instance and Class Attributes](../../09-object-oriented-programming/instance-and-class-attributes.md), [Inheritance](../../09-object-oriented-programming/inheritance.md), [Encapsulation](../../09-object-oriented-programming/encapsulation.md), [Polymorphism](../../09-object-oriented-programming/polymorphism.md), [Abstraction](../../09-object-oriented-programming/abstraction.md), [Special Methods](../../09-object-oriented-programming/special-methods.md)

## Table of Contents
- [Practice Questions](#practice-questions)
- [Practice Answers](#practice-answers)

---

## Practice Questions

1. What is the difference between a class and an object? Give a real-life example.
2. Create a class `Dog` with a method `bark()` that prints `Woof!`. Make an object and call the method.
3. What is `self`? Why is it the first parameter of every instance method?
4. Create a class `Student` with a constructor that takes `name` and `marks`, and a method `show()` that prints both.
5. Add a method `grade()` to `Student` that returns `"Pass"` for marks of 40 or more, otherwise `"Fail"`.
6. Make the `Student` constructor raise `ValueError` if the marks are not between 0 and 100.
7. Give `Account` a default balance of `0`. What happens with `Account("Riya")` and `Account("Riya", 500)`?
8. What is wrong with `def __init__(self, items=[])`? Fix it.
9. Create a class `Circle` with a class attribute `pi = 3.14159` and an instance attribute `radius`. Add an `area()` method.
10. Count how many `Student` objects have been created using a class attribute.
11. What does this print and why?
    ```python
    class A:
        x = 1
    a = A()
    b = A()
    a.x = 5
    print(a.x, b.x, A.x)
    ```
12. What are the differences between an instance method, a class method and a static method?
13. Write a class method `from_string("Riya-20")` that returns a `Person` object.
14. Create the class `Animal` with `name` and `eat()`, and a class `Dog(Animal)` with an extra method `bark()`.
15. Override `sound()` in `Dog` and `Cat` (children of `Animal`) and call it for a list of animals.
16. Why does `super().__init__(name)` matter in a child's `__init__`? What happens if you leave it out?
17. Use `isinstance` and `issubclass` to check a `Dog` against `Animal`.
18. Make `Account.balance` a property that rejects negative values.
19. What does a name with two leading underscores (`__x`) do in a class? Can it still be reached from outside?
20. Write a function `speak_all(things)` that calls `.speak()` on any objects. Test it with two unrelated classes. What is this style called?
21. Create an abstract class `Shape` with an abstract method `area()`, and a concrete `Square`. What happens if you try `Shape()`?
22. Add `__str__` and `__repr__` to `Point`, and explain the difference.
23. Add `__add__` and `__eq__` to a `Vector` class.
24. Make `sorted(students)` work for `Student` objects by their marks.
25. Make a `Playlist` class so that `len(p)` and `for song in p` both work.

---

## Practice Answers

Try each question yourself first, then compare.

**Answer 1**

A **class** is a blueprint; an **object** is a real thing built from it. Example: the plan of a house is the class, each actual house built from the plan is an object. The class `Dog` is the idea, `tommy = Dog()` is one dog.

**Answer 2**
```python
class Dog:
    def bark(self):
        print("Woof!")

d = Dog()
d.bark()
# Output: Woof!
```

**Answer 3**

`self` is the **object the method was called on**. Python passes it automatically as the first argument, so `d.bark()` runs `Dog.bark(d)`. Through `self` the method can read and change that object's data (`self.name`).

**Answer 4**
```python
class Student:
    def __init__(self, name, marks):
        self.name = name
        self.marks = marks

    def show(self):
        print(self.name, self.marks)

Student("Riya", 85).show()
# Output: Riya 85
```

**Answer 5**
```python
class Student:
    def __init__(self, name, marks):
        self.name = name
        self.marks = marks

    def grade(self):
        return "Pass" if self.marks >= 40 else "Fail"

print(Student("Riya", 85).grade(), Student("Amit", 30).grade())
# Output: Pass Fail
```

**Answer 6**
```python
class Student:
    def __init__(self, name, marks):
        if not 0 <= marks <= 100:
            raise ValueError("marks must be between 0 and 100")
        self.name = name
        self.marks = marks

try:
    Student("Riya", 150)
except ValueError as e:
    print(e)
# Output: marks must be between 0 and 100
```

**Answer 7**
```python
class Account:
    def __init__(self, owner, balance=0):
        self.owner = owner
        self.balance = balance

print(Account("Riya").balance)
print(Account("Riya", 500).balance)
# Output: 0
# Output: 500
```

**Answer 8**

The default list is created **once** and shared by every object. Use `None`:
```python
class Team:
    def __init__(self, items=None):
        self.items = [] if items is None else items

a = Team()
b = Team()
a.items.append("x")
print(b.items)
# Output: []
```

**Answer 9**
```python
class Circle:
    pi = 3.14159

    def __init__(self, radius):
        self.radius = radius

    def area(self):
        return Circle.pi * self.radius ** 2

print(round(Circle(2).area(), 2))
# Output: 12.57
```

**Answer 10**
```python
class Student:
    count = 0

    def __init__(self, name):
        self.name = name
        Student.count += 1

Student("Riya")
Student("Amit")
print(Student.count)
# Output: 2
```

**Answer 11**
```python
class A:
    x = 1
a = A()
b = A()
a.x = 5
print(a.x, b.x, A.x)
# Output: 5 1 1
```
`a.x = 5` creates a new **instance** attribute on `a` only. The class attribute and `b` are unchanged.

**Answer 12**

| Kind | First parameter | Use |
|---|---|---|
| Instance method | `self` (the object) | Work with one object's data |
| Class method (`@classmethod`) | `cls` (the class) | Work with class data, alternative constructors |
| Static method (`@staticmethod`) | none | A helper that needs neither |

**Answer 13**
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

**Answer 14**
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

**Answer 15**
```python
class Animal:
    def sound(self):
        return "..."

class Dog(Animal):
    def sound(self):
        return "Woof"

class Cat(Animal):
    def sound(self):
        return "Meow"

for a in [Dog(), Cat(), Animal()]:
    print(a.sound())
# Output: Woof
# Output: Meow
# Output: ...
```

**Answer 16**

`super().__init__(name)` runs the **parent's** constructor, so the parent's attributes are created. If a child defines its own `__init__` and does not call it, the parent's `__init__` is skipped and those attributes never exist (`AttributeError` when you use them).
```python
class Animal:
    def __init__(self, name):
        self.name = name

class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name)
        self.breed = breed

d = Dog("Tommy", "Labrador")
print(d.name, d.breed)
# Output: Tommy Labrador
```

**Answer 17**
```python
class Animal: pass
class Dog(Animal): pass

d = Dog()
print(isinstance(d, Animal))
print(issubclass(Dog, Animal))
print(issubclass(Animal, Dog))
# Output: True
# Output: True
# Output: False
```

**Answer 18**
```python
class Account:
    def __init__(self, balance):
        self.balance = balance

    @property
    def balance(self):
        return self._balance

    @balance.setter
    def balance(self, value):
        if value < 0:
            raise ValueError("balance cannot be negative")
        self._balance = value

a = Account(100)
a.balance = 250
print(a.balance)
try:
    a.balance = -1
except ValueError as e:
    print(e)
# Output: 250
# Output: balance cannot be negative
```

**Answer 19**

Python **mangles** the name to `_ClassName__x`, so `obj.__x` from outside raises `AttributeError`. It can still be reached as `obj._ClassName__x`, so it protects against accidents and name clashes, not against determined access.
```python
class A:
    def __init__(self):
        self.__x = 1

a = A()
print(a._A__x)
# Output: 1
```

**Answer 20**
```python
class Dog:
    def speak(self):
        return "Woof"

class Robot:
    def speak(self):
        return "Beep"

def speak_all(things):
    for t in things:
        print(t.speak())

speak_all([Dog(), Robot()])
# Output: Woof
# Output: Beep
```
This is **duck typing**: the function does not check the type, only that the object has `speak()`.

**Answer 21**
```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

class Square(Shape):
    def __init__(self, side):
        self.side = side

    def area(self):
        return self.side ** 2

print(Square(3).area())
try:
    Shape()
except TypeError as e:
    print(e)
# Output: 9
# Output: Can't instantiate abstract class Shape without an implementation for abstract method 'area'
```

**Answer 22**
```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __str__(self):
        return f"({self.x}, {self.y})"

    def __repr__(self):
        return f"Point({self.x}, {self.y})"

p = Point(1, 2)
print(p)
print([p])
# Output: (1, 2)
# Output: [Point(1, 2)]
```
`__str__` is the friendly text for users (`print`, `str`, f-strings). `__repr__` is the exact text for developers (the shell, inside lists, debugging).

**Answer 23**
```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __add__(self, other):
        return Vector(self.x + other.x, self.y + other.y)

    def __eq__(self, other):
        return (self.x, self.y) == (other.x, other.y)

    def __repr__(self):
        return f"Vector({self.x}, {self.y})"

print(Vector(1, 2) + Vector(3, 4))
print(Vector(1, 2) == Vector(1, 2))
# Output: Vector(4, 6)
# Output: True
```

**Answer 24**
```python
class Student:
    def __init__(self, name, marks):
        self.name = name
        self.marks = marks

    def __lt__(self, other):
        return self.marks < other.marks

    def __repr__(self):
        return f"{self.name}({self.marks})"

print(sorted([Student("Riya", 85), Student("Amit", 70), Student("Sam", 92)]))
# Output: [Amit(70), Riya(85), Sam(92)]
```
`sorted()` only needs `__lt__`.

**Answer 25**
```python
class Playlist:
    def __init__(self, songs):
        self.songs = songs

    def __len__(self):
        return len(self.songs)

    def __iter__(self):
        return iter(self.songs)

p = Playlist(["a", "b", "c"])
print(len(p))
for song in p:
    print(song)
# Output: 3
# Output: a
# Output: b
# Output: c
```

---

- [Back to practice index](../README.md)
