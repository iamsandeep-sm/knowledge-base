# Getting Started Practice

Related notes: [Python Installation](../../00-getting-started/python-installation.md), [Running Python](../../00-getting-started/running-python.md), [First Program](../../00-getting-started/first-program.md), [Python Syntax](../../00-getting-started/python-syntax.md)

## Table of Contents
- [Practice Questions](#practice-questions)
- [Practice Answers](#practice-answers)

---

## Practice Questions

1. Which command checks the installed Python version on Windows, and which one on macOS or Linux?
2. What is the difference between `python`, `python3` and `py`? Which one do you use on your computer?
3. Why should you install Python 3 and not Python 2?
4. What does the `>>>` prompt mean? How do you exit it on Windows and on macOS / Linux?
5. Write a program `hello.py` that prints `Hello, World!`. Write the terminal command that runs it.
6. Why does typing `python hello.py` at the `>>>` prompt give a `SyntaxError`? What should you do instead?
7. Print your name and your city on two separate lines, then on **one** line separated by ` | `.
8. Predict the output: `print(3 + 4)`, `print("3 + 4")`, `print("3", "+", "4")`.
9. Find and fix the error in each line: `Print("Hi")`, `print("Hi)`, `print "Hi"`.
10. What error does this code give? Fix it.
    ```python
    if True:
    print("x")
    ```
11. Write a program that asks for the user's name and prints `Welcome, <name>!`.
12. What does `python -c "print(2 + 3)"` do? What is `python -m pip --version` used for?
13. You save a file as `random.py` and write `import random` in it. What goes wrong, and how do you fix it?
14. Write the same long calculation on two lines in two different ways.
15. How many keywords does Python have? Show how to print them.
16. What is the difference between a syntax error and a runtime error? Give one example of each.

---

## Practice Answers

Try each question yourself first, then compare.

**Answer 1**
```
python --version      # Windows (py --version also works)
python3 --version     # macOS and Linux
```
Example output: `Python 3.15.1` (your version number will differ).

**Answer 2**

All three start the Python interpreter, but the name that exists depends on the system.

| Command | Where it is used |
|---|---|
| `python` | Windows (install manager). On macOS / Linux it may not exist, or may mean an old version |
| `python3` | macOS and Linux |
| `py` | Windows launcher. Useful when several versions are installed (`py -V:3.15`) |

**Answer 3**

Python 2 has not been supported since 1 January 2020, and it is not fully compatible with Python 3. For example, `print "Hi"` works in Python 2 but is an error in Python 3.

**Answer 4**

`>>>` is the prompt of **interactive mode**: Python is ready to run one line of code at a time. To exit, type `exit()` or `quit()`, or press `Ctrl + Z` then Enter (Windows) or `Ctrl + D` (macOS / Linux).

**Answer 5**
```python
# hello.py
print("Hello, World!")
# Output: Hello, World!
```
Run it from the folder that contains the file:
```
python hello.py
```
(On macOS and Linux: `python3 hello.py`.)

**Answer 6**

`python hello.py` is a **terminal command**, but the `>>>` prompt expects **Python code**, so Python reads `python hello.py` as code and fails with `SyntaxError: invalid syntax`. Exit the shell with `exit()` and type the command at the normal terminal prompt.

**Answer 7**
```python
name = "Karan"
city = "Pune"
print(name)
print(city)
print(name, city, sep=" | ")
# Output: Karan
# Output: Pune
# Output: Karan | Pune
```

**Answer 8**
```python
print(3 + 4)
print("3 + 4")
print("3", "+", "4")
# Output: 7
# Output: 3 + 4
# Output: 3 + 4
```
The first line calculates. The second is text in quotes, so it is printed as it is. The third prints three separate texts with one space between them.

**Answer 9**

| Wrong | Problem | Fixed |
|---|---|---|
| `Print("Hi")` | `NameError`, Python is case-sensitive | `print("Hi")` |
| `print("Hi)` | `SyntaxError: unterminated string literal`, the quote is not closed | `print("Hi")` |
| `print "Hi"` | `SyntaxError: Missing parentheses in call to 'print'` (Python 2 style) | `print("Hi")` |

**Answer 10**

`IndentationError: expected an indented block after 'if' statement on line 1`. The line after a colon must be indented (4 spaces).
```python
if True:
    print("x")
# Output: x
```

**Answer 11**
```python
name = input("What is your name? ")
print(f"Welcome, {name}!")
# User types: Riya
# Output: What is your name? Riya
# Output: Welcome, Riya!
```

**Answer 12**

- `python -c "print(2 + 3)"` runs the code inside the quotes and exits. Output: `5`.
- `python -m pip --version` runs the `pip` module with **this** Python. It shows the pip version and which Python it belongs to, so it checks that pip is installed.

**Answer 13**

Python finds **your** `random.py` first instead of the standard `random` module, so `random.randint(...)` fails with an error like `AttributeError: module 'random' has no attribute 'randint'` (Python even suggests renaming the file). Fix: rename your file to something else (for example `dice.py`) and delete the `__pycache__` folder if one was created.

**Answer 14**
```python
# 1. Inside brackets
total = (1 + 2 +
         3)
print(total)
# Output: 6

# 2. With a backslash at the end of the line
total = 1 + 2 + \
        3
print(total)
# Output: 6
```

**Answer 15**

Python has **35** keywords.
```python
import keyword
print(len(keyword.kwlist))
print(keyword.kwlist)
# Output: 35
```
(Python also has soft keywords `_`, `case`, `match` and `type`, shown by `keyword.softkwlist`.)

**Answer 16**

| | Syntax error | Runtime error |
|---|---|---|
| Found | **Before** the program runs, so nothing is printed | **While** the program runs, so earlier lines already ran |
| Example | `if x > 3` (missing colon): `SyntaxError: expected ':'` | `print(10 / 0)`: `ZeroDivisionError: division by zero` |

```python
print("start")
print(10 / 0)
# Output: start
# Error:  ZeroDivisionError: division by zero
```

---

- [Back to practice index](../README.md)
