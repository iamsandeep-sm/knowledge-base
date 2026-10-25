# Functions Practice

Related notes: [Defining Functions](../../04-functions/defining-functions.md), [Parameters and Arguments](../../04-functions/parameters-and-arguments.md), [Return Statement](../../04-functions/return-statement.md), [Scope](../../04-functions/scope.md), [Lambda Functions](../../04-functions/lambda-functions.md), [Recursion](../../04-functions/recursion.md), [Built-in Functions](../../04-functions/built-in-functions.md)

## Table of Contents
- [Practice Questions](#practice-questions)
- [Practice Answers](#practice-answers)

---

## Practice Questions

1. Write a function `greet(name)` that prints `Hello, <name>!`. Call it twice.
2. Write a function `square(n)` that **returns** the square. Print `square(7)`.
3. What is the difference between a parameter and an argument?
4. What does this print and why?
   ```python
   def f():
       print("hi")
   x = f()
   print(x)
   ```
5. Write `power(base, exp=2)` with a default value. Call it as `power(5)` and `power(2, 10)`.
6. Write `min_max(numbers)` that returns both the smallest and the largest value. Unpack the result into two variables.
7. Write `total(*numbers)` that returns the sum of any number of arguments.
8. What is wrong with this function? Fix it.
   ```python
   def add_item(item, items=[]):
       items.append(item)
       return items
   ```
9. Predict the output:
   ```python
   x = 10
   def change():
       x = 20
   change()
   print(x)
   ```
10. Write a function with a counter stored as a global variable and use `global` to increase it.
11. Write `is_even(n)` that returns `True` or `False`.
12. Write `grade(marks)` using early returns: `Invalid` for marks outside 0-100, `Pass` for 40 or more, otherwise `Fail`.
13. Sort `[("Riya", 82), ("Amit", 91), ("Sam", 67)]` by marks (high to low) using a lambda.
14. Write a recursive function `factorial(n)`.
15. Write a recursive function that returns the sum of the digits of a number (`sum_digits(1234)` gives `10`).
16. What happens when a recursive function has no base case?
17. Using built-in functions only, print the length, the largest and the sum of `[4, 9, 2, 7]`.
18. Use `zip` and `enumerate` to print `1 Riya 90` and `2 Amit 80` from two lists.
19. Why does `sum = 100` followed by `sum([1, 2])` fail?
20. Write a function `describe(**info)` that prints each `key = value` pair.

---

## Practice Answers

Try each question yourself first, then compare.

**Answer 1**
```python
def greet(name):
    print(f"Hello, {name}!")

greet("Riya")
greet("Amit")
# Output: Hello, Riya!
# Output: Hello, Amit!
```

**Answer 2**
```python
def square(n):
    return n * n

print(square(7))
# Output: 49
```

**Answer 3**

A **parameter** is the name in the definition (`def f(a)`: `a`). An **argument** is the value given in the call (`f(5)`: `5`).

**Answer 4**
```python
def f():
    print("hi")
x = f()
print(x)
# Output: hi
# Output: None
```
`f` has no `return`, so it returns `None`, which is stored in `x`.

**Answer 5**
```python
def power(base, exp=2):
    return base ** exp

print(power(5))
print(power(2, 10))
# Output: 25
# Output: 1024
```

**Answer 6**
```python
def min_max(numbers):
    return min(numbers), max(numbers)

low, high = min_max([4, 9, 2])
print(low, high)
# Output: 2 9
```

**Answer 7**
```python
def total(*numbers):
    return sum(numbers)

print(total(1, 2, 3))
print(total())
# Output: 6
# Output: 0
```

**Answer 8**

The default list `[]` is created once and **shared** by every call, so items pile up. Use `None` as the default:
```python
def add_item(item, items=None):
    if items is None:
        items = []
    items.append(item)
    return items

print(add_item("a"))
print(add_item("b"))
# Output: ['a']
# Output: ['b']
```

**Answer 9**
```python
x = 10
def change():
    x = 20
change()
print(x)
# Output: 10
```
The `x = 20` inside the function creates a separate **local** `x`. The global `x` is unchanged.

**Answer 10**
```python
count = 0

def increase():
    global count
    count += 1

increase()
increase()
print(count)
# Output: 2
```

**Answer 11**
```python
def is_even(n):
    return n % 2 == 0

print(is_even(10), is_even(7))
# Output: True False
```

**Answer 12**
```python
def grade(marks):
    if marks < 0 or marks > 100:
        return "Invalid"
    if marks >= 40:
        return "Pass"
    return "Fail"

print(grade(75), grade(20), grade(150))
# Output: Pass Fail Invalid
```

**Answer 13**
```python
students = [("Riya", 82), ("Amit", 91), ("Sam", 67)]
print(sorted(students, key=lambda s: s[1], reverse=True))
# Output: [('Amit', 91), ('Riya', 82), ('Sam', 67)]
```

**Answer 14**
```python
def factorial(n):
    if n == 0:
        return 1
    return n * factorial(n - 1)

print(factorial(5))
# Output: 120
```

**Answer 15**
```python
def sum_digits(n):
    if n < 10:
        return n
    return n % 10 + sum_digits(n // 10)

print(sum_digits(1234))
# Output: 10
```

**Answer 16**

It keeps calling itself until Python stops it with `RecursionError: maximum recursion depth exceeded` (the limit is usually 1000 calls).

**Answer 17**
```python
nums = [4, 9, 2, 7]
print(len(nums), max(nums), sum(nums))
# Output: 4 9 22
```

**Answer 18**
```python
names = ["Riya", "Amit"]
marks = [90, 80]
for i, (n, m) in enumerate(zip(names, marks), start=1):
    print(i, n, m)
# Output: 1 Riya 90
# Output: 2 Amit 80
```

**Answer 19**

`sum = 100` makes the name `sum` point to the number `100`, hiding the built-in function. Then `sum([1, 2])` tries to call an `int`: `TypeError: 'int' object is not callable`. Use another variable name.

**Answer 20**
```python
def describe(**info):
    for key, value in info.items():
        print(key, "=", value)

describe(name="Riya", age=20)
# Output: name = Riya
# Output: age = 20
```

---

- [Back to practice index](../README.md)
