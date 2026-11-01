# Testing and Code Quality Practice

Related notes: [Assertions](../../12-testing-and-code-quality/assertions.md), [Unit Testing](../../12-testing-and-code-quality/unit-testing.md), [Debugging](../../12-testing-and-code-quality/debugging.md), [PEP 8 and Clean Code](../../12-testing-and-code-quality/pep8-and-clean-code.md)

## Table of Contents
- [Practice Questions](#practice-questions)
- [Practice Answers](#practice-answers)

---

## Practice Questions

1. Name the three kinds of errors. Which kind does Python **not** report, and why?
2. Write an `assert` that checks a list is not empty, with a message. Catch the failure and print the message.
3. This function validates user input with `assert`. Why is that wrong, and how do you fix it?
    ```python
    def set_age(age):
        assert age >= 0, "age must not be negative"
        return age
    ```
4. What happens to `assert` statements when Python runs with `-O`? Show it.
5. Why can `assert (x > 0, "x must be positive")` never fail?
6. How do you check in an `assert` that `0.1 + 0.2` equals `0.3`?
7. Write `is_palindrome(text)` and test it with plain `assert` statements, including an edge case.
8. Write a `unittest` test class for `square(n)` with two tests, and run it in code.
9. Test that `int("x")` raises `ValueError` using `unittest`.
10. Why use `setUp`? Write a test class where each test gets a fresh list.
11. In a `unittest` run, what do `.`, `F`, `E` and `s` mean? What is the difference between a FAIL and an ERROR?
12. Write a pytest test that checks `is_even` for several inputs with `parametrize`.
13. Test `roll()` (which uses `random.randint(1, 6)`) by replacing `randint` so it always returns `5`.
14. How would you test a function that calls `input()`?
15. Read this traceback. What went wrong, in which function and on which line?
    ```text
    Traceback (most recent call last):
      File "app.py", line 14, in <module>
        show(data)
      File "app.py", line 9, in show
        print(first_item(items))
      File "app.py", line 4, in first_item
        return items[0]
    IndexError: list index out of range
    ```
16. Print a variable's name and value in one `print`, with an f-string.
17. Show that `logging` prints only warnings and above by default, and then show debug messages too.
18. Log a caught exception together with its traceback.
19. What do the `pdb` commands `n`, `s`, `c`, `p x`, `l` and `q` do? How do you start the debugger from inside the code?
20. `average([])` crashes. Fix the function and write tests for it.
21. Measure how long `sum(range(1000))` takes using the standard library.
22. Rewrite this snippet to follow PEP 8 and give the names better meaning.
    ```python
    def F(l,x = 2):
        r=[]
        for i in range(len(l)):
            if l[i]>x: r.append(l[i])
        return r
    ```
23. Which naming style is used for a class, a function, a constant and a "private" attribute?
24. Rewrite this using guard clauses:
    ```python
    def grade(marks):
        if marks is not None:
            if marks >= 0:
                if marks >= 40:
                    return "pass"
                else:
                    return "fail"
            else:
                return "invalid"
        else:
            return "invalid"
    ```
25. Write a Google-style docstring for `withdraw(balance, amount)`.
26. Which tool checks style, which fixes layout automatically and which checks type hints?

---

## Practice Answers

Try each question yourself first, then compare.

**Answer 1**

| Kind | When | Example |
|---|---|---|
| Syntax error | Before the program runs | Missing colon |
| Runtime error (exception) | While running | `1 / 0`, `int("abc")` |
| Logic error | The program runs, but the answer is wrong | Using `>` instead of `>=` |

Python does **not** report logic errors: the code is valid, so it has nothing to complain about. Tests and careful checking find them.

**Answer 2**
```python
items = []
try:
    assert items, "the list must not be empty"
except AssertionError as error:
    print("Failed:", error)
# Output: Failed: the list must not be empty
```

**Answer 3**

The check is **input validation**, but `assert` is for finding the programmer's own mistakes and can be **switched off** with `python -O`. Raise a real exception:
```python
def set_age(age):
    if age < 0:
        raise ValueError("age must not be negative")
    return age

try:
    set_age(-1)
except ValueError as error:
    print(error)
# Output: age must not be negative
```

**Answer 4**

They are **removed**.
```python
import subprocess
import sys

code = "assert False, 'boom'\nprint('assert skipped')"
result = subprocess.run([sys.executable, "-O", "-c", code], capture_output=True, text=True)
print(result.stdout.strip())
# Output: assert skipped
```

**Answer 5**

The brackets turn the condition and the message into a **tuple**, `(False, "x must be positive")`, and a non-empty tuple is always true. Python warns with `SyntaxWarning: assertion is always true`. Write `assert x > 0, "x must be positive"`.
```python
x = -5
assert (x > 0, "x must be positive")
print("no error: the assertion is useless")
# Output: no error: the assertion is useless
```

**Answer 6**
```python
import math

assert math.isclose(0.1 + 0.2, 0.3)
print("ok")
# Output: ok
```
Never use `==` for floats.

**Answer 7**
```python
def is_palindrome(text):
    cleaned = [c.lower() for c in text if c.isalnum()]
    return cleaned == cleaned[::-1]

assert is_palindrome("level")
assert is_palindrome("Was it a car or a cat I saw?")
assert not is_palindrome("python")
assert is_palindrome("")            # edge case: empty text
assert is_palindrome("a")           # edge case: one character
print("all tests passed")
# Output: all tests passed
```

**Answer 8**
```python
import unittest

def square(n):
    return n * n

class TestSquare(unittest.TestCase):
    def test_positive(self):
        self.assertEqual(square(4), 16)

    def test_negative(self):
        self.assertEqual(square(-3), 9)

suite = unittest.defaultTestLoader.loadTestsFromTestCase(TestSquare)
result = unittest.TestResult()
suite.run(result)
print(result.testsRun, result.wasSuccessful())
# Output: 2 True
```
In a real project you put the class in `test_square.py` and run `python -m unittest`.

**Answer 9**
```python
import unittest

class TestConvert(unittest.TestCase):
    def test_bad_number(self):
        with self.assertRaises(ValueError):
            int("x")

result = unittest.TestResult()
unittest.defaultTestLoader.loadTestsFromTestCase(TestConvert).run(result)
print(result.wasSuccessful())
# Output: True
```

**Answer 10**

`setUp` runs **before every test**, so each test starts from the same fresh state and tests cannot affect each other.
```python
import unittest

class TestList(unittest.TestCase):
    def setUp(self):
        self.items = [1, 2, 3]

    def test_append(self):
        self.items.append(4)
        self.assertEqual(len(self.items), 4)

    def test_pop(self):
        self.items.pop()
        self.assertEqual(len(self.items), 2)

result = unittest.TestResult()
unittest.defaultTestLoader.loadTestsFromTestCase(TestList).run(result)
print(result.testsRun, result.wasSuccessful())
# Output: 2 True
```
Without `setUp`'s fresh list, a shared list would be changed by the first test.

**Answer 11**

| Symbol | Meaning |
|---|---|
| `.` | The test passed |
| `F` | **FAIL**: an assertion was false, so the code gave a wrong answer |
| `E` | **ERROR**: the test crashed with an exception |
| `s` | The test was skipped |

**Answer 12**
```text
import pytest

from calculator import is_even


@pytest.mark.parametrize("n, expected", [(2, True), (3, False), (0, True), (-4, True)])
def test_is_even(n, expected):
    assert is_even(n) is expected
```
Run it with `python -m pytest`. It is reported as four separate tests.

**Answer 13**
```python
import random
from unittest.mock import patch

def roll():
    return random.randint(1, 6)

with patch("random.randint", return_value=5):
    print(roll())
# Output: 5
```
Replacing the random part makes the test **predictable**.

**Answer 14**

Replace `input` with a fake using `patch`, so the test does not wait for a person.
```text
from unittest.mock import patch

def ask_name():
    return input("Name: ").strip().title()

with patch("builtins.input", return_value="  riya "):
    print(ask_name())
```
This prints `Riya`. In a test you would write `self.assertEqual(ask_name(), "Riya")` inside the `with`. Better design: keep the logic in a function that **takes the text as an argument** (`clean_name(text)`) and test that function directly.

**Answer 15**

An `IndexError: list index out of range` happened in the function `first_item`, at **line 4** (`return items[0]`), because `items` was **empty**. The call chain was: the main program (line 14) called `show`, which (line 9) called `first_item`. Read from the bottom. The real cause is that an **empty list** was passed in, so the fix is either a check in `first_item` or ensuring `data` is not empty.

**Answer 16**
```python
total = 42
print(f"{total=}")
# Output: total=42
```

**Answer 17**
```python
import logging
import sys

logging.basicConfig(stream=sys.stdout, format="%(levelname)s: %(message)s", force=True)
logging.debug("not shown")
logging.warning("shown")

logging.basicConfig(
    stream=sys.stdout,
    level=logging.DEBUG,
    format="%(levelname)s: %(message)s",
    force=True,
)
logging.debug("now shown")
# Output: WARNING: shown
# Output: DEBUG: now shown
```
The default threshold is `WARNING`. `force=True` lets you reconfigure.

**Answer 18**
```python
import io
import logging

buffer = io.StringIO()
log = logging.getLogger("practice")
log.addHandler(logging.StreamHandler(buffer))
log.propagate = False

try:
    int("abc")
except ValueError:
    log.exception("conversion failed")

lines = buffer.getvalue().splitlines()
print(lines[0])
print(lines[-1])
# Output: conversion failed
# Output: ValueError: invalid literal for int() with base 10: 'abc'
```
`log.exception()` logs at the ERROR level and adds the traceback. Call it only inside an `except` block.

**Answer 19**

| Command | Does |
|---|---|
| `n` | Run the current line, **skip over** function calls |
| `s` | Run the current line, **step into** a function |
| `c` | Continue to the next breakpoint |
| `p x` | Print the value of `x` |
| `l` | List the source around the current line |
| `q` | Quit the debugger |

Start the debugger from inside the code by writing `breakpoint()` where you want the program to stop. Or run the whole program under it: `python -m pdb script.py`.

**Answer 20**
```python
def average(numbers):
    if not numbers:
        raise ValueError("average() needs at least one number")
    return sum(numbers) / len(numbers)

assert average([2, 4, 6]) == 4
assert average([5]) == 5

try:
    average([])
except ValueError as error:
    print(error)
# Output: average() needs at least one number
```
Decide what the function should do for empty input (raise an error, or return `0` or `None`) and **test that decision**.

**Answer 21**
```python
import timeit

seconds = timeit.timeit("sum(range(1000))", number=1000)
print(seconds > 0)
# Output: True
```
`number=1000` runs the code 1000 times and returns the **total** seconds. For single measurements use `time.perf_counter()`.

**Answer 22**
```python
def filter_greater(numbers, limit=2):
    result = []
    for number in numbers:
        if number > limit:
            result.append(number)
    return result

print(filter_greater([1, 2, 3, 4]))
# Output: [3, 4]
```
Even shorter: `return [n for n in numbers if n > limit]`. Changes made: a descriptive `snake_case` function name (not `F`), no `l` as a name, spaces after commas and around operators, no spaces around `=` in the default value, one statement per line, and looping directly over the items instead of `range(len(...))`.

**Answer 23**

| Item | Style | Example |
|---|---|---|
| Class | `CapWords` | `ShoppingCart` |
| Function | `snake_case` | `calculate_total` |
| Constant | `UPPER_SNAKE_CASE` | `MAX_RETRIES` |
| "Private" attribute | One leading underscore | `_cache` |

**Answer 24**
```python
def grade(marks):
    if marks is None or marks < 0:
        return "invalid"
    return "pass" if marks >= 40 else "fail"

print(grade(55), grade(20), grade(-1), grade(None))
# Output: pass fail invalid invalid
```
Handle the unusual cases first and return early, so the main logic is not buried in nesting.

**Answer 25**
```python
def withdraw(balance, amount):
    """Return the balance after taking out `amount`.

    Args:
        balance: Money currently in the account.
        amount: Money to take out. Must not be more than the balance.

    Returns:
        The new balance.

    Raises:
        ValueError: If `amount` is negative or more than the balance.
    """
    if amount < 0 or amount > balance:
        raise ValueError("invalid amount")
    return balance - amount

print(withdraw(100, 30))
# Output: 70
```

**Answer 26**

| Job | Tool |
|---|---|
| Check style against PEP 8 | `pycodestyle`, `flake8`, `ruff check` |
| Find unused imports and undefined names | `pyflakes`, `flake8`, `ruff check` |
| Fix layout automatically | `black` or `ruff format` |
| Check type hints | `mypy` or `pyright` |

---

- [Back to practice index](../README.md)
