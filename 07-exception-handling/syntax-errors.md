# Syntax Errors and Exceptions - Revision Notes ~ sm

> **In one line:** Python has two kinds of errors. A **syntax error** means the code is written wrongly, so Python refuses to run it at all. An **exception** happens **while the program runs**, and it can be **caught and handled** with `try` / `except`.

## Table of Contents
- [Two Kinds of Errors](#two-kinds-of-errors)
- [Syntax Errors](#syntax-errors)
- [Exceptions](#exceptions)
- [Reading a Traceback](#reading-a-traceback)
- [Common Built-in Exceptions](#common-built-in-exceptions)
- [The Exception Family Tree](#the-exception-family-tree)
- [Why Handle Exceptions?](#why-handle-exceptions)
- [Logic Errors](#logic-errors)

---

## Two Kinds of Errors

| | Syntax error | Exception (runtime error) |
|---|---|---|
| **When** | Before the program starts (while Python reads the file) | While the program is running |
| **Cause** | Broken grammar of Python | Code is valid, but something goes wrong (bad value, missing file ...) |
| **Output before it** | None. The program never starts | Earlier lines already ran |
| **Can `try/except` catch it?** | Not in the same file | Yes |
| **Example** | `if x > 3` (no colon) | `10 / 0` |

(The Python Syntax note in Getting Started has more about writing correct syntax.)

---

## Syntax Errors

```python
print("This line never runs")
if True
    print("x")
```
```
  File "demo.py", line 2
    if True
           ^
SyntaxError: expected ':'
```
Python points to the place with `^` and names the problem. Notice that the first `print` did **not** run, because the whole file is checked first.

| Message | Usual cause |
|---|---|
| `SyntaxError: expected ':'` | Missing colon after `if`, `for`, `def` ... |
| `SyntaxError: '(' was never closed` | Missing closing bracket |
| `SyntaxError: unterminated string literal` | Missing closing quote |
| `SyntaxError: invalid syntax` | Another broken rule |
| `IndentationError: expected an indented block` | Wrong or missing indentation |
| `TabError` | Tabs and spaces mixed |

> The error is sometimes reported on the line **after** the real mistake (for example a missing bracket). If the marked line looks fine, check the line above it.

Fix a syntax error by correcting the code. There is nothing to handle at run time. A `SyntaxError` is only catchable when the code is compiled while the program runs, for example with `eval()` or `exec()`:

```python
try:
    eval("1 +")
except SyntaxError as e:
    print("caught:", e.msg)
# Output: caught: invalid syntax
```

---

## Exceptions

When valid code meets a problem at run time, Python **raises an exception**. If nothing handles it, the program stops and prints a traceback.

```python
print("start")
print(10 / 0)
print("end")
# Output: start
# Error:  ZeroDivisionError: division by zero
```
`start` was printed, then the program stopped. `end` never ran.

Exceptions are **objects**. Each has a **type** (its name, like `ZeroDivisionError`) and a **message**.

---

## Reading a Traceback

```python
def divide(a, b):
    return a / b

def calculate():
    return divide(10, 0)

calculate()
```
```
Traceback (most recent call last):
  File "demo.py", line 7, in <module>
    calculate()
  File "demo.py", line 5, in calculate
    return divide(10, 0)
  File "demo.py", line 2, in divide
    return a / b
ZeroDivisionError: division by zero
```
Read it **from the bottom**:
1. **Last line:** the exception type and message.
2. The line just above it is where it happened.
3. Going up shows the chain of calls that led there (the oldest call is at the top).

---

## Common Built-in Exceptions

| Exception | When it happens | Example | Message |
|---|---|---|---|
| `ZeroDivisionError` | Dividing by zero | `1 / 0` | `division by zero` |
| `ValueError` | Right type, wrong value | `int("abc")` | `invalid literal for int() with base 10: 'abc'` |
| `TypeError` | Wrong type for an operation | `"a" + 1` | `can only concatenate str (not "int") to str` |
| `NameError` | Name does not exist | `print(x)` | `name 'x' is not defined` |
| `IndexError` | List index out of range | `[1][5]` | `list index out of range` |
| `KeyError` | Dictionary key missing | `{"a": 1}["b"]` | `'b'` |
| `AttributeError` | Object has no such attribute or method | `(1, 2).append(3)` | `'tuple' object has no attribute 'append'` |
| `FileNotFoundError` | File does not exist | `open("nofile.txt")` | `[Errno 2] No such file or directory: 'nofile.txt'` |
| `ModuleNotFoundError` | Module cannot be found | `import nothing_here` | `No module named 'nothing_here'` |
| `RecursionError` | Recursion too deep | a function calling itself forever | `maximum recursion depth exceeded` |
| `KeyboardInterrupt` | User presses `Ctrl + C` | | |
| `StopIteration` | `next()` on an exhausted iterator | `next(iter([]))` | |

---

## The Exception Family Tree

Exceptions are organised in a **hierarchy**. A parent class matches all its children, which matters when you write `except` (see the try / except note).

```
BaseException
├── KeyboardInterrupt
├── SystemExit
└── Exception                    <- almost every error you handle
    ├── ArithmeticError
    │   └── ZeroDivisionError
    ├── LookupError
    │   ├── IndexError
    │   └── KeyError
    ├── ValueError
    ├── TypeError
    ├── NameError
    │   └── UnboundLocalError
    ├── AttributeError
    ├── ImportError
    │   └── ModuleNotFoundError
    ├── OSError
    │   ├── FileNotFoundError
    │   └── PermissionError
    ├── RuntimeError
    │   └── RecursionError
    ├── StopIteration
    ├── AssertionError
    └── SyntaxError
        └── IndentationError
            └── TabError
```
(A shortened tree; Python has more.) You can check any relationship yourself:
```python
print(issubclass(ZeroDivisionError, ArithmeticError))
print(issubclass(KeyError, LookupError))
print(issubclass(KeyboardInterrupt, Exception))
# Output: True
# Output: True
# Output: False
```
`KeyboardInterrupt` and `SystemExit` are **not** under `Exception`, so catching `Exception` will not stop the user from pressing `Ctrl + C`.

---

## Why Handle Exceptions?

Without handling, any bad input crashes the program:

```python
age = int(input("Age: "))        # user types "abc"
# Error:  ValueError: invalid literal for int() with base 10: 'abc'
```
With handling, the program can show a friendly message, ask again, or clean up. See the next notes: try / except, else and finally, and Raising Exceptions.

---

## Logic Errors

A third type needs no error message: a **logic error**. The program runs without any exception, but gives the **wrong result**.

```python
def average(a, b):
    return a + b / 2        # should be (a + b) / 2

print(average(10, 20))
# Output: 20.0
```
The answer should be `15.0`. Python cannot know your intention, so find these by testing, printing values, and reading the code carefully.

---

- [Back to section index](./README.md)
