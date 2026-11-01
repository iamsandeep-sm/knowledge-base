# Assertions - Revision Notes ~ sm

> **In one line:** An **`assert`** statement checks that a condition you **believe is always true** really is true. If it is false, Python raises **`AssertionError`** and stops. Assertions are for **catching your own bugs** while developing and testing, not for checking user input.

## Table of Contents
- [Syntax](#syntax)
- [What Happens When It Fails](#what-happens-when-it-fails)
- [Adding a Message](#adding-a-message)
- [Where to Use assert](#where-to-use-assert)
- [assert vs Exceptions](#assert-vs-exceptions)
- [Assertions Can Be Switched Off](#assertions-can-be-switched-off)
- [Comparing Floats and Other Special Cases](#comparing-floats-and-other-special-cases)
- [Assertions in Tests](#assertions-in-tests)
- [Pitfalls](#pitfalls)
- [Practical Examples](#practical-examples)
- [Common Mistakes](#common-mistakes)

---

## Syntax

```text
assert condition
assert condition, "message shown if it fails"
```
It means: "I **assert** that this is true. If I am wrong, stop right here."

```python
x = 10
assert x > 0
assert x % 2 == 0, "x must be even"
print("all assertions passed")
# Output: all assertions passed
```
When the condition is true, **nothing happens** and the program continues.

---

## What Happens When It Fails

```python
x = 5
assert x > 10
# Error: AssertionError
```
The program stops with `AssertionError`, and the traceback shows the exact line. Like any exception it can be caught, but you normally **let it stop the program** because it signals a bug.

`assert condition, message` is the same as:
```text
if not condition:
    raise AssertionError(message)
```
(except that it can be switched off, see below).

---

## Adding a Message

The message after the comma is attached to the error. It makes the failure much easier to understand.

```python
age = -3
try:
    assert age >= 0, f"age cannot be negative, got {age}"
except AssertionError as error:
    print("Failed:", error)
# Output: Failed: age cannot be negative, got -3
```
The message is evaluated **only if the assertion fails**, so building it costs nothing normally. Without a message `str(error)` is empty:
```python
try:
    assert False
except AssertionError as error:
    print(repr(error))
# Output: AssertionError()
```
Write messages that say **what was expected and what was found**.

---

## Where to Use assert

Use `assert` to state things that **must be true if your code is correct**.

| Use | Example |
|---|---|
| **Precondition**: what a function expects from its caller | `assert len(items) > 0` |
| **Postcondition**: what a function guarantees | `assert result >= 0` |
| **Invariant**: something that stays true in a loop or class | `assert total == sum(parts)` |
| **Unreachable code**: "this branch cannot happen" | `assert False, "unreachable"` |
| **Sanity check** while developing | `assert isinstance(x, int)` |
| **Tests** | `assert add(2, 3) == 5` |

```python
def average(numbers):
    assert len(numbers) > 0, "average() needs at least one number"
    result = sum(numbers) / len(numbers)
    assert min(numbers) <= result <= max(numbers)
    return result

print(average([2, 4, 9]))
# Output: 5.0
```
```python
def describe(n):
    if n < 0:
        return "negative"
    elif n == 0:
        return "zero"
    elif n > 0:
        return "positive"
    assert False, "unreachable: every number is covered above"

print(describe(7))
# Output: positive
```

---

## assert vs Exceptions

This is the most important rule:

> Use **`raise`** (with `ValueError`, `TypeError`, ...) to reject **bad data from the outside** (users, files, networks). Use **`assert`** to find **mistakes in your own code**.

| | `assert` | `if ... raise` |
|---|---|---|
| Purpose | Finds **programmer** errors | Handles **bad input and expected problems** |
| Can be disabled | **Yes**, with `python -O` | No |
| Error type | Always `AssertionError` | Any, chosen by you |
| Good for | Internal checks, tests | Validating user input, public functions |
| Meant to be caught? | Rarely | Yes |

```python
# Wrong: user input checked with assert (the check vanishes under python -O)
#     age = int(text)
#     assert age >= 0, "age must not be negative"

# Right: validation with an exception
def set_age(age):
    if age < 0:
        raise ValueError("age must not be negative")
    return age

try:
    set_age(-5)
except ValueError as error:
    print(error)
# Output: age must not be negative
```
The caller can catch the `ValueError` and ask the user again. An `AssertionError` says "the programmer made a mistake", which is not what a typing error by a user is.

---

## Assertions Can Be Switched Off

Running Python with the **`-O`** ("optimise") flag **removes every `assert` statement**. The special constant `__debug__` is `True` normally and `False` under `-O`.

```python
print(__debug__)
# Output: True
```
Proof, by starting a second Python with `-O`:
```python
import subprocess
import sys

code = "assert False, 'boom'\nprint('assert was skipped')"
result = subprocess.run([sys.executable, "-O", "-c", code], capture_output=True, text=True)
print(result.stdout.strip())
# Output: assert was skipped
```
So:
- **Never put required work inside an `assert`.** It would silently vanish. `assert do_something()` is a bug if `do_something()` has side effects.
- **Never use `assert` for security or input checks.**
- Many tools use `-O` in production, so your program must work correctly without the assertions.

```python
# Wrong: the remove() disappears under -O
items = [1, 2, 3]
assert items.remove(2) is None

# Right: do the work first, then check the result
items = [1, 2, 3]
items.remove(2)
assert 2 not in items
print(items)
# Output: [1, 3]
```

---

## Comparing Floats and Other Special Cases

Floating-point numbers are not exact, so a plain `==` can fail.

```python
import math

assert 0.1 + 0.2 != 0.3                       # surprising, but true
assert math.isclose(0.1 + 0.2, 0.3)           # the right way
assert abs((0.1 + 0.2) - 0.3) < 1e-9          # also fine
print("ok")
# Output: ok
```
Other checks:
```python
data = {"name": "Riya", "age": 20}
names = ["Riya", "Amit"]

assert "name" in data
assert isinstance(data["age"], int)
assert all(isinstance(n, str) for n in names)
assert len(set(names)) == len(names), "duplicate names"
assert data.get("email") is None
print("ok")
# Output: ok
```
Use `is None` and `is not None` for `None`, and `==` for values.

---

## Assertions in Tests

The simplest tests are just functions full of `assert`. The test tool **pytest** (see the Unit Testing note) was built around this: on a failure it shows the values on both sides.

```python
def add(a, b):
    return a + b

def test_add():
    assert add(2, 3) == 5
    assert add(-1, 1) == 0
    assert add(0, 0) == 0

test_add()
print("test passed")
# Output: test passed
```
Inside `unittest` you use methods such as `self.assertEqual(a, b)` instead, which give a clearer message than a bare `assert` (also see the Unit Testing note).

A tiny hand-made "test runner":
```python
def add(a, b):
    return a + b

cases = [((2, 3), 5), ((0, 0), 0), ((-1, -1), -2)]
for args, expected in cases:
    assert add(*args) == expected, f"add{args} gave {add(*args)}, expected {expected}"
print(f"{len(cases)} cases passed")
# Output: 3 cases passed
```

---

## Pitfalls

**1. A tuple is always true.** Putting brackets around the condition **and** the message makes a tuple, and a non-empty tuple is always true, so the assertion can **never fail**.

```python
assert (1 == 2, "this can never fail")      # no error!  (a SyntaxWarning is shown)
print("still running")
# Output: still running
```
Python warns you: `SyntaxWarning: assertion is always true, perhaps remove parentheses?`. Write it without brackets: `assert 1 == 2, "message"`.

**2. `assert` is a statement, not a function.** `assert(x > 0)` happens to work because the brackets are just grouping, but `assert(x > 0, "message")` is the tuple trap above.

**3. Side effects.** Anything inside an `assert` that changes something (calls a function that modifies data, reads input) will **not run** under `-O`.

**4. Catching `AssertionError` everywhere.** A bare `except Exception` hides assertion failures, along with the bugs they report.

**5. Vague conditions.** `assert result` does not say what you expected. Be specific: `assert result > 0`.

---

## Practical Examples

**Check a function's result**
```python
def percentage(part, whole):
    assert whole != 0, "whole must not be zero"
    result = part / whole * 100
    assert 0 <= result <= 100, f"percentage out of range: {result}"
    return result

print(percentage(45, 60))
# Output: 75.0
```

**Check that a list is sorted after your own sort**
```python
def my_sort(items):
    result = sorted(items)
    assert all(a <= b for a, b in zip(result, result[1:])), "not sorted"
    assert len(result) == len(items), "items were lost"
    return result

print(my_sort([3, 1, 2]))
# Output: [1, 2, 3]
```

**Class invariant**
```python
class Account:
    def __init__(self, balance=0):
        self.balance = balance
        self._check()

    def _check(self):
        assert self.balance >= 0, "balance must never be negative"

    def deposit(self, amount):
        if amount <= 0:
            raise ValueError("deposit must be positive")
        self.balance += amount
        self._check()

account = Account(100)
account.deposit(50)
print(account.balance)
# Output: 150
```
Notice the mixture: **`raise ValueError`** for the bad argument from the caller, **`assert`** for the internal rule that should never break.

**Show the failing values**
```python
def divide(a, b):
    return a / b

a, b = 10, 4
result = divide(a, b)
try:
    assert result == 3, f"divide({a}, {b}) returned {result}"
except AssertionError as error:
    print(error)
# Output: divide(10, 4) returned 2.5
```

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Validating user input with `assert` | Checks vanish with `python -O`; wrong error type | `if ...: raise ValueError(...)` |
| `assert (condition, "message")` | Always true: never fails | `assert condition, "message"` |
| Putting required work inside `assert` | Skipped under `-O` | Do the work first, assert on the result |
| No message on a hard-to-read assertion | Failure is mysterious | Add what was expected and what was found |
| Catching `AssertionError` to "fix" it | Hides a real bug | Fix the code that broke the rule |
| Using `==` to compare floats | Spurious failures | `math.isclose(a, b)` |
| Using `assert x == None` | Works but is poor style | `assert x is None` |
| Expecting `assert` to protect against a crash in production | May not even run | Use real exceptions and handling |
| Forgetting that `assert` stops at the first failure | Later checks never run | Test tools (pytest) report each test separately |
| Using `assert` for conditions that can really happen | Program stops unexpectedly | Handle the case with `if` / `raise` |

---

- [Back to section index](./README.md)
