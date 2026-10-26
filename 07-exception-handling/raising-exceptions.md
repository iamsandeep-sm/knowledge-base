# Raising Exceptions - Revision Notes ~ sm

> **In one line:** You can **raise** an exception yourself with `raise`, to say "this input or situation is not acceptable". You can also create your own exception types by writing a class that inherits from `Exception`.

## Table of Contents
- [The raise Statement](#the-raise-statement)
- [Choosing the Right Exception](#choosing-the-right-exception)
- [Validating Input](#validating-input)
- [Catching What You Raise](#catching-what-you-raise)
- [Re-raising with a Bare raise](#re-raising-with-a-bare-raise)
- [Chaining with raise from](#chaining-with-raise-from)
- [Custom Exceptions](#custom-exceptions)
- [assert](#assert)
- [Adding Notes (Python 3.11+)](#adding-notes-python-311)
- [Raise or Return an Error Value?](#raise-or-return-an-error-value)
- [Common Mistakes](#common-mistakes)

---

## The raise Statement

```python
raise ExceptionType("message")
```

```python
age = -5
if age < 0:
    raise ValueError("age cannot be negative")
# Error:  ValueError: age cannot be negative
```
When `raise` runs, the current code stops at once and Python looks for an `except` that matches, exactly as for built-in errors. If nothing handles it, the program stops with a traceback.

You can also raise the **class** without brackets (`raise ValueError`). Python creates an empty exception for you. A message is more helpful, so write one.

---

## Choosing the Right Exception

Use the built-in type that describes the problem.

| Problem | Raise |
|---|---|
| Right type, but unacceptable value (`-5` as an age) | `ValueError` |
| Wrong type (`"abc"` where a number is required) | `TypeError` |
| Missing key | `KeyError` |
| Index not valid | `IndexError` |
| A feature not written yet | `NotImplementedError` |
| Something unexpected in the program state | `RuntimeError` |

---

## Validating Input

```python
def set_age(age):
    if not isinstance(age, int):
        raise TypeError("age must be a whole number")
    if age < 0 or age > 120:
        raise ValueError("age must be between 0 and 120")
    return age

print(set_age(25))
# Output: 25
```
`set_age(-3)` raises `ValueError: age must be between 0 and 120`, and `set_age("x")` raises `TypeError: age must be a whole number`.

The function checks its inputs **first** and stops immediately when something is wrong. That is much better than letting wrong data travel deeper into the program.

---

## Catching What You Raise

```python
def set_age(age):
    if age < 0:
        raise ValueError("age cannot be negative")
    return age

try:
    set_age(-1)
except ValueError as e:
    print("Problem:", e)
# Output: Problem: age cannot be negative
```
The function **reports** the problem; the caller **decides** what to do (show a message, ask again, stop).

---

## Re-raising with a Bare raise

Inside an `except` block, a plain `raise` sends the **same exception** onward. Use it to do something (such as logging) and still let the error continue.

```python
def process():
    try:
        1 / 0
    except ZeroDivisionError:
        print("logging the problem")
        raise

try:
    process()
except ZeroDivisionError:
    print("handled at the top")
# Output: logging the problem
# Output: handled at the top
```
A bare `raise` outside of an `except` block gives `RuntimeError: No active exception to reraise`.

---

## Chaining with raise from

When you catch one error and raise a **different** one, use `from` to keep the original as the cause.

```python
def load_age(text):
    try:
        return int(text)
    except ValueError as e:
        raise RuntimeError("config is broken") from e

try:
    load_age("x")
except RuntimeError as e:
    print(e)
    print(repr(e.__cause__))
# Output: config is broken
# Output: ValueError("invalid literal for int() with base 10: 'x'")
```
If it is not caught, the traceback shows both errors:
```
ValueError: invalid literal for int() with base 10: 'x'

The above exception was the direct cause of the following exception:

RuntimeError: config is broken
```
`raise NewError(...) from None` hides the original error from the traceback (use it only when the original is just noise).

---

## Custom Exceptions

Create your own exception by making a class that inherits from `Exception`. It gives the error a **meaningful name** for your program.

```python
class InsufficientFundsError(Exception):
    pass

def withdraw(balance, amount):
    if amount > balance:
        raise InsufficientFundsError(f"balance {balance}, requested {amount}")
    return balance - amount

try:
    withdraw(100, 150)
except InsufficientFundsError as e:
    print("Error:", e)
# Output: Error: balance 100, requested 150
```
- By convention the name **ends with `Error`**.
- Inherit from `Exception`, not from `BaseException`.
- You can make a **family**: one base class for your project, and children for specific problems, so callers can catch all of them with the parent.

```python
class ShopError(Exception):
    """Base class for shop errors."""

class OutOfStockError(ShopError):
    pass

class PaymentError(ShopError):
    pass

try:
    raise OutOfStockError("pen")
except ShopError as e:
    print(type(e).__name__, e)
# Output: OutOfStockError pen
```
A custom exception can also carry extra data:
```python
class InvalidAgeError(Exception):
    def __init__(self, age):
        super().__init__(f"invalid age: {age}")
        self.age = age

try:
    raise InvalidAgeError(-4)
except InvalidAgeError as e:
    print(e, e.age)
# Output: invalid age: -4 -4
```
(`__init__` and `super()` belong to the Object-Oriented Programming section.)

---

## assert

`assert condition, message` raises an `AssertionError` if the condition is `False`. It is a quick check for things that should **never** be false while you develop.

```python
x = 5
assert x > 0, "x must be positive"      # fine, nothing happens

try:
    assert x < 0, "x must be negative"
except AssertionError as e:
    print("assert failed:", e)
# Output: assert failed: x must be negative
```
Do not use `assert` to check user input. Asserts are **removed** when Python runs with the `-O` option, so the check would disappear. Use `if` and `raise` for input validation.

---

## Adding Notes (Python 3.11+)

`add_note()` attaches extra text to an exception. It shows in the traceback.

```python
e = ValueError("bad value")
e.add_note("while reading the config file")
print(e.__notes__)
# Output: ['while reading the config file']
```

---

## Raise or Return an Error Value?

| | Raise an exception | Return `None` / a special value |
|---|---|---|
| Caller can ignore it? | No: the program stops unless handled | Yes: easy to forget to check |
| Best for | Real errors and invalid input | Normal "no result" cases (a search finds nothing) |

Rule of thumb: if the problem means "this should not have happened", raise. If "nothing found" is a normal outcome, return `None`.

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| `raise "error text"` | `TypeError: exceptions must derive from BaseException` | Raise an exception: `raise ValueError("error text")` |
| Bare `raise` outside `except` | `RuntimeError: No active exception to reraise` | Raise a specific exception |
| Raising a new error inside `except` without `from` | The traceback says "During handling of the above exception, another exception occurred", which is confusing | `raise NewError(...) from e` |
| Custom exception that does not inherit `Exception` | Cannot be raised or caught properly | `class MyError(Exception)` |
| Using `assert` for user input | Skipped with `python -O` | Use `if` and `raise` |
| Vague message `raise ValueError("error")` | Hard to debug | Say what was wrong and the bad value |

---

- [Back to section index](./README.md)
