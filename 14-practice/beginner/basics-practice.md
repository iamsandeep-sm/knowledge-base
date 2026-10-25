# Basics Practice

Related notes: [Basics notes](../../01-basics/README.md)

## Practice Questions

1. Print your name, your age and your city on three separate lines, then all on **one** line.
2. Which of these variable names are invalid, and why? `my_age`, `2nd_place`, `total-marks`, `_count`, `class`, `First Name`.
3. Store your name, age, height and whether you are a student in four variables. Print each value with its type.
4. Swap the values of two variables `a = 5` and `b = 9` without a third variable.
5. What is the output of `print(7 / 2)`, `print(7 // 2)`, `print(7 % 2)`, `print(2 ** 5)`?
6. What does `print(2 + 3 * 4)` print? Change it to print `20` using brackets.
7. Ask the user for two numbers and print their sum. Why does the program print `45` for `4` and `5` if you forget `int()`?
8. Convert `"3.99"` to an integer. Why does `int("3.99")` fail, and what is the correct way?
9. Ask for a user's name and age, and print `Riya will be 20 next year.` (use an f-string).
10. Ask for the radius of a circle and print its area with **2 decimal places**.
11. Which of these are mutable: `"hello"`, `[1, 2]`, `(1, 2)`, `{"a": 1}`, `5`, `{1, 2}`?
12. What is the output? `print(bool(""))`, `print(bool("0"))`, `print(bool([]))`, `print(True + True)`.
13. Ask for a temperature in Celsius and print it in Fahrenheit with 1 decimal place.
14. Print `A-B-C` using one `print()` call with the items `"A"`, `"B"`, `"C"`.

---

## Practice Answers

Try each question yourself first, then compare.

**Answer 1**
```python
name = "Karan"
age = 19
city = "Pune"
print(name)
print(age)
print(city)
print(name, age, city)
# Output: Karan
# Output: 19
# Output: Pune
# Output: Karan 19 Pune
```

**Answer 2**

| Name | Valid? | Reason |
|---|---|---|
| `my_age` | Valid | |
| `2nd_place` | **Invalid** | Starts with a digit |
| `total-marks` | **Invalid** | `-` is not allowed (it means minus) |
| `_count` | Valid | Starting with `_` is allowed |
| `class` | **Invalid** | It is a Python keyword |
| `First Name` | **Invalid** | Contains a space |

**Answer 3**
```python
name = "Karan"
age = 19
height = 5.9
is_student = True
print(name, type(name))
print(age, type(age))
print(height, type(height))
print(is_student, type(is_student))
# Output: Karan <class 'str'>
# Output: 19 <class 'int'>
# Output: 5.9 <class 'float'>
# Output: True <class 'bool'>
```

**Answer 4**
```python
a = 5
b = 9
a, b = b, a
print(a, b)
# Output: 9 5
```

**Answer 5**
```python
print(7 / 2)
print(7 // 2)
print(7 % 2)
print(2 ** 5)
# Output: 3.5
# Output: 3
# Output: 1
# Output: 32
```

**Answer 6**
```python
print(2 + 3 * 4)
print((2 + 3) * 4)
# Output: 14
# Output: 20
```
Multiplication happens before addition, so `3 * 4` is done first.

**Answer 7**
```python
a = int(input("First: "))
b = int(input("Second: "))
print("Sum:", a + b)
# User types: 4
# User types: 5
# Output: First: 4
# Output: Second: 5
# Output: Sum: 9
```
Without `int()`, `input()` returns text, and `"4" + "5"` **joins** the two texts into `"45"`.

**Answer 8**

`int("3.99")` fails because the text `"3.99"` is not a whole number. Convert to `float` first.
```python
print(int(float("3.99")))
# Output: 3
```
`int()` cuts the decimals; it does not round.

**Answer 9**
```python
name = input("Name: ")
age = int(input("Age: "))
print(f"{name} will be {age + 1} next year.")
# User types: Riya
# User types: 19
# Output: Name: Riya
# Output: Age: 19
# Output: Riya will be 20 next year.
```

**Answer 10**
```python
import math

radius = float(input("Radius: "))
print(f"Area = {math.pi * radius ** 2:.2f}")
# User types: 3
# Output: Radius: 3
# Output: Area = 28.27
```

**Answer 11**

Mutable: `[1, 2]` (list), `{"a": 1}` (dict), `{1, 2}` (set).
Immutable: `"hello"` (str), `(1, 2)` (tuple), `5` (int).

**Answer 12**
```python
print(bool(""))
print(bool("0"))
print(bool([]))
print(True + True)
# Output: False
# Output: True
# Output: False
# Output: 2
```
`"0"` is a non-empty string, so it is `True`. Only the **number** `0` is false.

**Answer 13**
```python
c = float(input("Celsius: "))
f = c * 9 / 5 + 32
print(f"{f:.1f}")
# User types: 37
# Output: Celsius: 37
# Output: 98.6
```

**Answer 14**
```python
print("A", "B", "C", sep="-")
# Output: A-B-C
```

---

- [Back to practice index](../README.md)
