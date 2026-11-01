# Unit Testing - Revision Notes ~ sm

> **In one line:** A **unit test** is a small piece of code that **runs one part of your program (a function or method) with known input and checks the result**. Automated tests tell you in seconds whether a change broke something. Python ships with **`unittest`**, and **`pytest`** is the popular, simpler tool.

## Table of Contents
- [Why Test?](#why-test)
- [A First Test, by Hand](#a-first-test-by-hand)
- [unittest](#unittest)
- [Reading the Results](#reading-the-results)
- [Common Assert Methods](#common-assert-methods)
- [Testing Exceptions](#testing-exceptions)
- [setUp and tearDown](#setup-and-teardown)
- [subTest, skip and Other Decorators](#subtest-skip-and-other-decorators)
- [Running Tests](#running-tests)
- [pytest](#pytest)
- [Mocking: Replacing Parts of the Program](#mocking-replacing-parts-of-the-program)
- [doctest](#doctest)
- [What Makes a Good Test](#what-makes-a-good-test)
- [Test-Driven Development](#test-driven-development)
- [Coverage](#coverage)
- [Common Mistakes](#common-mistakes)

---

## Why Test?

| Benefit | Meaning |
|---|---|
| **Find bugs early** | Before the user does |
| **Safe changes** | After editing, run the tests: if they pass, you did not break anything (this protection is called a *regression* test) |
| **Documentation** | Tests show how a function is meant to be used |
| **Better design** | Code that is easy to test is usually small and clear |
| **Confidence** | You can refactor without fear |

The kinds of tests, from small to big:

| Kind | Tests |
|---|---|
| **Unit test** | One function or class in isolation (this note) |
| **Integration test** | Several parts working together (for example code plus a database) |
| **End-to-end test** | The whole program, as a user uses it |

---

## A First Test, by Hand

You already know how: call the function and compare with the answer you expect.

```python
def add(a, b):
    return a + b

assert add(2, 3) == 5
assert add(-1, 1) == 0
print("tests passed")
# Output: tests passed
```
This works, but it stops at the first failure, gives no summary and has no structure. Test frameworks fix that. The examples below use this small module saved as **`calculator.py`**:

```python
# file: calculator.py
def add(a, b):
    return a + b


def divide(a, b):
    if b == 0:
        raise ZeroDivisionError("cannot divide by zero")
    return a / b


def is_even(n):
    return n % 2 == 0
```

---

## unittest

`unittest` is part of the standard library (nothing to install). The recipe:

1. Create a file whose name starts with **`test_`**, for example `test_calculator.py`.
2. Make a **class** that inherits from `unittest.TestCase`.
3. Write **methods whose names start with `test`**. Each is one test.
4. Inside, call an `assert...` method.

```python
# file: test_calculator.py
import unittest

from calculator import add, divide, is_even


class TestCalculator(unittest.TestCase):
    def test_add_positive(self):
        self.assertEqual(add(2, 3), 5)

    def test_add_negative(self):
        self.assertEqual(add(-1, -1), -2)

    def test_divide(self):
        self.assertEqual(divide(10, 4), 2.5)

    def test_divide_by_zero(self):
        with self.assertRaises(ZeroDivisionError):
            divide(1, 0)

    def test_is_even(self):
        self.assertTrue(is_even(4))
        self.assertFalse(is_even(7))


if __name__ == "__main__":
    unittest.main()
```
Run it from the folder that holds both files:

```text
python -m unittest
```
```text
.....
----------------------------------------------------------------------
Ran 5 tests in 0.000s

OK
```
Each dot is a passed test. (The time differs on your computer.) Add `-v` for one line per test:
```text
python -m unittest -v
```
```text
test_add_negative (test_calculator.TestCalculator.test_add_negative) ... ok
test_add_positive (test_calculator.TestCalculator.test_add_positive) ... ok
test_divide (test_calculator.TestCalculator.test_divide) ... ok
test_divide_by_zero (test_calculator.TestCalculator.test_divide_by_zero) ... ok
test_is_even (test_calculator.TestCalculator.test_is_even) ... ok

----------------------------------------------------------------------
Ran 5 tests in 0.000s

OK
```
Tests run in **alphabetical order** of their names, and each test must work **independently** of the others.

---

## Reading the Results

Two things can go wrong, and `unittest` tells them apart.

| Symbol | Word | Meaning |
|---|---|---|
| `.` | ok | The test passed |
| `F` | **FAIL** | An assertion was false: the code gave a **wrong answer** |
| `E` | **ERROR** | The test crashed with an **exception** (a bug in the code or the test) |
| `s` | skipped | The test was skipped on purpose |

A failing run (one wrong expectation, one crash):
```text
EF
======================================================================
ERROR: test_error (test_fail.TestFail.test_error)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "test_fail.py", line 9, in test_error
    add(1)
    ~~~^^^
TypeError: add() missing 1 required positional argument: 'b'

======================================================================
FAIL: test_wrong (test_fail.TestFail.test_wrong)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "test_fail.py", line 6, in test_wrong
    self.assertEqual(add(2, 2), 5)
    ~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^
AssertionError: 4 != 5

----------------------------------------------------------------------
Ran 2 tests in 0.001s

FAILED (failures=1, errors=1)
```
Read it bottom to top: the last line is the summary, `AssertionError: 4 != 5` says what was found (`4`) and what was expected (`5`).

---

## Common Assert Methods

Use the specific method instead of a bare `assert`: the failure message is much clearer.

| Method | Checks |
|---|---|
| `assertEqual(a, b)` | `a == b` |
| `assertNotEqual(a, b)` | `a != b` |
| `assertTrue(x)` / `assertFalse(x)` | `bool(x)` is `True` / `False` |
| `assertIs(a, b)` / `assertIsNone(x)` | `a is b` / `x is None` |
| `assertIn(a, b)` / `assertNotIn(a, b)` | `a in b` |
| `assertIsInstance(a, T)` | `isinstance(a, T)` |
| `assertAlmostEqual(a, b, places=7)` | Equal to some decimal places (for **floats**) |
| `assertGreater(a, b)` / `assertLess(a, b)` | `a > b` / `a < b` |
| `assertListEqual`, `assertDictEqual`, `assertSetEqual` | Equal collections, with a clear diff |
| `assertRaises(Error)` | The code raises that exception |
| `assertRaisesRegex(Error, text)` | Raises it **and** the message matches |

Floats cannot be compared with `assertEqual`, because of rounding. Here a test class is run in code and the result object is inspected (normally you run tests from the terminal):
```python
import unittest

class TestFloat(unittest.TestCase):
    def test_almost(self):
        self.assertAlmostEqual(0.1 + 0.2, 0.3)

    def test_exact(self):
        self.assertEqual(0.1 + 0.2, 0.3)

suite = unittest.defaultTestLoader.loadTestsFromTestCase(TestFloat)
result = unittest.TestResult()
suite.run(result)
print(result.testsRun, len(result.failures))
print([test.id().split(".")[-1] for test, _ in result.failures])
# Output: 2 1
# Output: ['test_exact']
```
`test_almost` passes; `test_exact` fails with `0.30000000000000004 != 0.3`.

---

## Testing Exceptions

Use `assertRaises` as a **context manager** (`with`). The test passes only if the block raises the exception.

```python
import unittest

def divide(a, b):
    if b == 0:
        raise ZeroDivisionError("cannot divide by zero")
    return a / b

class TestDivide(unittest.TestCase):
    def test_zero(self):
        with self.assertRaises(ZeroDivisionError) as ctx:
            divide(1, 0)
        self.assertEqual(str(ctx.exception), "cannot divide by zero")

    def test_message(self):
        with self.assertRaisesRegex(ZeroDivisionError, "cannot"):
            divide(1, 0)

suite = unittest.defaultTestLoader.loadTestsFromTestCase(TestDivide)
result = unittest.TestResult()
suite.run(result)
print(result.testsRun, result.wasSuccessful())
# Output: 2 True
```
Always test **both** the normal path and the error path. Check the **type** of the exception, and the message when the message matters.

---

## setUp and tearDown

`setUp()` runs **before each test**, `tearDown()` **after each test**. Use them to give every test a fresh starting point.

```python
# file: account.py
class InsufficientFunds(Exception):
    pass


class Account:
    def __init__(self, owner, balance=0):
        self.owner = owner
        self.balance = balance

    def deposit(self, amount):
        if amount <= 0:
            raise ValueError("deposit must be positive")
        self.balance += amount

    def withdraw(self, amount):
        if amount > self.balance:
            raise InsufficientFunds("not enough money")
        self.balance -= amount
```
```python
# file: test_account.py
import unittest

from account import Account, InsufficientFunds


class TestAccount(unittest.TestCase):
    def setUp(self):
        self.account = Account("Riya", 100)

    def test_deposit(self):
        self.account.deposit(50)
        self.assertEqual(self.account.balance, 150)

    def test_withdraw(self):
        self.account.withdraw(30)
        self.assertEqual(self.account.balance, 70)

    def test_overdraw(self):
        with self.assertRaises(InsufficientFunds):
            self.account.withdraw(500)
        self.assertEqual(self.account.balance, 100)

    def test_bad_deposit_message(self):
        with self.assertRaisesRegex(ValueError, "positive"):
            self.account.deposit(-5)
```
Because `setUp` creates a **new** `Account` for each test, the deposit in one test does not affect another. Other hooks: `setUpClass` / `tearDownClass` (once per class, written with `@classmethod`), and `addCleanup(function)`.

---

## subTest, skip and Other Decorators

**`subTest`** runs one test with many inputs and reports **each failing input separately**:
```text
    def test_many_deposits(self):
        for amount in (1, 10, 100):
            with self.subTest(amount=amount):
                before = self.account.balance
                self.account.deposit(amount)
                self.assertEqual(self.account.balance, before + amount)
```
**Skipping and expected failures**
```text
    @unittest.skip("not written yet")
    def test_interest(self):
        pass
```
| Decorator | Meaning |
|---|---|
| `@unittest.skip("reason")` | Never run it |
| `@unittest.skipIf(condition, "reason")` | Skip when the condition is true |
| `@unittest.skipUnless(condition, "reason")` | Skip unless the condition is true |
| `@unittest.expectedFailure` | Known bug; counts as success while it still fails |

The run for `test_account.py` (verbose):
```text
test_bad_deposit_message (test_account.TestAccount.test_bad_deposit_message) ... ok
test_deposit (test_account.TestAccount.test_deposit) ... ok
test_interest (test_account.TestAccount.test_interest) ... skipped 'not written yet'
test_many_deposits (test_account.TestAccount.test_many_deposits) ... ok
test_overdraw (test_account.TestAccount.test_overdraw) ... ok
test_withdraw (test_account.TestAccount.test_withdraw) ... ok

----------------------------------------------------------------------
Ran 6 tests in 0.001s

OK (skipped=1)
```

---

## Running Tests

| Command | Does |
|---|---|
| `python -m unittest` | Finds and runs all tests (discovery) in the current folder |
| `python -m unittest -v` | Verbose: one line per test |
| `python -m unittest test_account` | Run one test **file** (module) |
| `python -m unittest test_account.TestAccount` | Run one test **class** |
| `python -m unittest test_account.TestAccount.test_deposit` | Run **one test** |
| `python -m unittest -k deposit` | Run tests whose name contains `deposit` |
| `python -m unittest discover -s tests` | Look for tests in the `tests` folder |
| `python -m unittest -f` | Stop at the first failure |

**Discovery rules**: files named `test*.py`, classes that inherit `TestCase`, methods named `test*`. The folder must be importable (for subfolders, add an `__init__.py`).

A typical project layout:
```text
project/
    calculator.py
    account.py
    tests/
        __init__.py
        test_calculator.py
        test_account.py
```
Run `python -m unittest discover -s tests -t .` from `project/`, or use `pytest`.

---

## pytest

**pytest** is a third-party tool (`python -m pip install pytest`) that most Python teams use. It runs `unittest` tests too, but needs much less code:

| | `unittest` | `pytest` |
|---|---|---|
| Test is | A method in a `TestCase` class | A **plain function** `def test_...():` |
| Checking | `self.assertEqual(a, b)` | plain **`assert a == b`** |
| Setup | `setUp` method | **fixtures** |
| Many inputs | `subTest` | `@pytest.mark.parametrize` |
| Failure message | Message from the method | Shows the **values** of the whole expression |

```python
# file: test_calc_pytest.py
import pytest

from calculator import add, divide, is_even


def test_add():
    assert add(2, 3) == 5


def test_divide_by_zero():
    with pytest.raises(ZeroDivisionError):
        divide(1, 0)


def test_divide_message():
    with pytest.raises(ZeroDivisionError, match="cannot divide"):
        divide(1, 0)


@pytest.mark.parametrize("n, expected", [(2, True), (3, False), (0, True), (-4, True)])
def test_is_even(n, expected):
    assert is_even(n) is expected


def test_float():
    assert 0.1 + 0.2 == pytest.approx(0.3)


@pytest.fixture
def numbers():
    return [3, 1, 2]


def test_sorted(numbers):
    assert sorted(numbers) == [1, 2, 3]


def test_file(tmp_path):
    f = tmp_path / "hello.txt"
    f.write_text("hi", encoding="utf-8")
    assert f.read_text(encoding="utf-8") == "hi"
```
```text
python -m pytest -v
```
```text
============================= test session starts ==============================
platform linux -- Python 3.13.17, pytest-9.2.0, pluggy-1.7.0 -- /usr/local/bin/python3
rootdir: .
collecting ... collected 10 items

test_calc_pytest.py::test_add PASSED                                     [ 10%]
test_calc_pytest.py::test_divide_by_zero PASSED                          [ 20%]
test_calc_pytest.py::test_divide_message PASSED                          [ 30%]
test_calc_pytest.py::test_is_even[2-True] PASSED                         [ 40%]
test_calc_pytest.py::test_is_even[3-False] PASSED                        [ 50%]
test_calc_pytest.py::test_is_even[0-True] PASSED                         [ 60%]
test_calc_pytest.py::test_is_even[-4-True] PASSED                        [ 70%]
test_calc_pytest.py::test_float PASSED                                   [ 80%]
test_calc_pytest.py::test_sorted PASSED                                  [ 90%]
test_calc_pytest.py::test_file PASSED                                    [100%]

============================== 10 passed in 0.03s ==============================
```
(Your platform line and versions will differ.)

**What pytest gives you**
- **`parametrize`**: one test function, many inputs: it ran as four separate tests above.
- **`pytest.raises`**: like `assertRaises`; `match=` checks the message.
- **`pytest.approx`**: compare floats.
- **Fixtures**: functions marked `@pytest.fixture` that **supply** data or resources; a test asks for one by writing its name as a parameter. `tmp_path` is a built-in fixture giving a fresh temporary folder.
- **Clear failures**: a failing plain `assert` shows both sides:
```text
============================= test session starts ==============================
collected 1 item

test_pyfail.py F                                                         [100%]

=================================== FAILURES ===================================
__________________________________ test_wrong __________________________________

    def test_wrong():
        result = add(2, 2)
>       assert result == 5
E       assert 4 == 5

test_pyfail.py:5: AssertionError
=========================== short test summary info ============================
FAILED test_pyfail.py::test_wrong - assert 4 == 5
============================== 1 failed in 0.02s ===============================
```
| Command | Does |
|---|---|
| `python -m pytest` | Run all tests (files `test_*.py` or `*_test.py`, functions `test_*`) |
| `python -m pytest -q` | Quiet output |
| `python -m pytest -v` | Verbose |
| `python -m pytest test_file.py::test_name` | One test |
| `python -m pytest -k "deposit"` | Tests whose name contains the word |
| `python -m pytest -x` | Stop at the first failure |
| `python -m pytest --lf` | Re-run only the tests that failed last time |

---

## Mocking: Replacing Parts of the Program

A unit test should not depend on **randomness, the clock, the network, a database or the user typing**. `unittest.mock` **replaces** those parts with controllable fakes.

**`Mock`**: a fake object that records how it was used.
```python
from unittest.mock import Mock

fake = Mock(return_value=42)
print(fake(1, 2))
print(fake.call_count)
fake.assert_called_once_with(1, 2)

sequence = Mock(side_effect=[1, 2, ValueError("boom")])
print(sequence(), sequence())
try:
    sequence()
except ValueError as error:
    print("raised", error)
# Output: 42
# Output: 1
# Output: 1 2
# Output: raised boom
```
- `return_value` is what the call returns.
- `side_effect` as a **list** returns the items one by one; as an **exception** it raises it.
- `assert_called_once_with(...)`, `assert_called()`, `call_count` and `call_args` check how it was called.

**`patch`**: temporarily replace a real name while a block runs (or on a decorated test).
```python
import random
from unittest.mock import patch

def roll():
    return random.randint(1, 6)

with patch("random.randint", return_value=4) as fake_randint:
    print(roll())
    print(fake_randint.call_args)
print(1 <= roll() <= 6)
# Output: 4
# Output: call(1, 6)
# Output: True
```
Inside the `with`, `random.randint` is the fake and always gives `4`, so the test is **predictable**. After the block the real function is back.

The golden rule: **patch the name where it is looked up (used)**, not where it is defined. If `app.py` does `from random import randint`, patch `"app.randint"`, not `"random.randint"`.

In a test:
```text
# inside a TestCase
    @patch("random.randint", return_value=6)
    def test_max_roll(self, fake_randint):
        self.assertEqual(roll(), 6)
```
The same idea replaces `input()` (`patch("builtins.input", return_value="Riya")`), `time.sleep`, or an HTTP call.

---

## doctest

`doctest` runs the **examples written inside docstrings** (lines that look like an interactive session) and checks the output. It keeps documentation honest.

```python
# file: shapes.py
def area(width, height):
    """Return the area of a rectangle.

    >>> area(3, 4)
    12
    >>> area(2.5, 2)
    5.0
    """
    return width * height
```
```text
python -m doctest shapes.py
```
Silence means every example passed (`-v` shows details). If the code is wrong (say it used `+` instead of `*`):
```text
**********************************************************************
File "shapes.py", line 4, in shapes.area
Failed example:
    area(3, 4)
Expected:
    12
Got:
    7
**********************************************************************
File "shapes.py", line 6, in shapes.area
Failed example:
    area(2.5, 2)
Expected:
    5.0
Got:
    4.5
**********************************************************************
1 item had failures:
   2 of   2 in shapes.area
***Test Failed*** 2 failures.
```
Good for simple examples; real test suites use `unittest` or `pytest`.

---

## What Makes a Good Test

**The three steps: Arrange, Act, Assert**
```python
def test_withdraw():
    account = Account("Riya", 100)      # Arrange: set things up
    account.withdraw(30)                # Act: do the one thing being tested
    assert account.balance == 70        # Assert: check the result
```
**Good tests are**
| Quality | Meaning |
|---|---|
| **Fast** | Milliseconds, so you run them often |
| **Independent** | No test relies on another or on the order |
| **Repeatable** | Same result every time (no real clock, randomness or network) |
| **Focused** | One idea per test |
| **Readable** | The name tells the story: `test_withdraw_more_than_balance_raises` |

**What to test**
- A **normal** case.
- **Edge cases**: empty input, zero, one item, the largest and smallest values, negative numbers.
- **Error cases**: bad input that should raise an exception.
- A **bug** you fixed: add a test that would have caught it.

Do **not** test Python itself (`len([1,2]) == 2`), and do not copy the code's logic into the test (a test that repeats the same calculation proves nothing).

```python
def is_even(n):
    return n % 2 == 0

# a good set of checks for is_even
for n, expected in [(0, True), (1, False), (2, True), (-3, False), (-4, True)]:
    assert is_even(n) is expected
print("edge cases passed")
# Output: edge cases passed
```

---

## Test-Driven Development

**TDD** reverses the order: write the test **first**, watch it **fail**, then write just enough code to make it **pass**, then tidy up.

| Step | Name | Meaning |
|---|---|---|
| 1 | **Red** | Write a test for something that does not work yet; it fails |
| 2 | **Green** | Write the simplest code that makes it pass |
| 3 | **Refactor** | Clean the code; the tests keep you safe |

Repeat in small steps. It forces you to think about what the function should do before you write it.

---

## Coverage

**Coverage** measures **which lines of your code the tests actually ran**. The popular tool is `coverage.py` (`python -m pip install coverage`), run with `coverage run -m pytest` and `coverage report`. Lines that no test reaches are lines nobody checks.

High coverage does **not** prove the tests are good (a test can run a line without checking anything), but **low** coverage shows where to add tests. Do not chase 100% for its own sake.

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Test file or method name does not start with `test` | The test is **never found or run** | `test_*.py`, `def test_...` |
| Test class does not inherit `unittest.TestCase` | `unittest` ignores it | `class TestX(unittest.TestCase):` |
| Forgetting `self.` before `assertEqual` | `NameError` | `self.assertEqual(...)` |
| Swapping expected and actual in `assertEqual(expected, actual)` | Confusing failure message | Convention: `assertEqual(actual, expected)` |
| Comparing floats with `assertEqual` | Fails because of rounding | `assertAlmostEqual`, `pytest.approx` |
| Tests that depend on each other or on the order | Pass alone, fail together | Use `setUp` / fixtures for a fresh start |
| Tests that use the real network, clock or randomness | Slow and unpredictable | Replace with `patch` / `Mock` |
| Several unrelated checks in one test | The first failure hides the rest, unclear cause | One idea per test; `subTest` / `parametrize` for many inputs |
| `with self.assertRaises(...)` containing extra lines after the raising call | Those lines never run | Keep the block to the single failing call |
| Patching the wrong name | The real function still runs | Patch where it is **used** |
| A test that cannot fail (`assert True`, or an `assert` with a tuple) | False confidence | Make the test fail once (change the code) to see it work |
| Testing only the happy path | Edge-case bugs slip through | Add empty, zero, negative and error cases |
| `ImportError` when running tests | Python cannot find your module | Run from the project folder; use `python -m unittest` or `python -m pytest` |
| Stale `.pyc` or wrong folder | Old code is tested | Run from the right folder; delete `__pycache__` if in doubt |
| Calling `unittest.main()` at the top level of a file you import | Tests run on import | Keep it under `if __name__ == "__main__":` |

---

- [Back to section index](./README.md)
