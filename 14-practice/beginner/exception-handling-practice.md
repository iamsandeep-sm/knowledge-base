# Exception Handling Practice

Related notes: [Syntax Errors](../../07-exception-handling/syntax-errors.md), [try / except](../../07-exception-handling/try-except.md), [else and finally](../../07-exception-handling/else-and-finally.md), [Raising Exceptions](../../07-exception-handling/raising-exceptions.md)

## Table of Contents
- [Practice Questions](#practice-questions)
- [Practice Answers](#practice-answers)

---

## Practice Questions

1. What is the difference between a syntax error and an exception?
2. Name the exception raised by each: `int("abc")`, `10 / 0`, `[1, 2][5]`, `{"a": 1}["b"]`, `"a" + 1`, `undefined_name`.
3. Write code that prints `Cannot divide by zero` instead of crashing when dividing `10` by `0`.
4. Convert the text `"abc"` to an integer and print the exception message using `as e`.
5. Write a function `to_int(text)` that returns the number, or `None` if the text is not a valid number.
6. Handle `ValueError` and `ZeroDivisionError` differently in a function `inverse(text)` that returns `1 / int(text)`.
7. Catch both `ValueError` and `TypeError` with a single `except`.
8. Why is this order a problem?
   ```python
   try:
       [1, 2, 3][10]
   except LookupError:
       print("lookup")
   except IndexError:
       print("index")
   ```
9. Why is a bare `except:` a bad idea?
10. Ask the user for a whole number again and again until the input is valid.
11. What does the `else` block of a `try` do? Show an example.
12. What does `finally` do? When does it run?
13. Predict the output:
    ```python
    def f():
        try:
            return "try"
        finally:
            print("finally")
    print(f())
    ```
14. Raise a `ValueError` with a message when `age` is negative.
15. Write a function `set_age(age)` that raises `TypeError` for a non-integer and `ValueError` for an age outside 0-120.
16. What does a bare `raise` do inside an `except` block?
17. What does `raise NewError(...) from e` do?
18. Create a custom exception `InsufficientFundsError` and raise it from a `withdraw(balance, amount)` function.
19. What is the difference between `assert` and `raise`? Why not use `assert` to validate user input?
20. Read a file safely: print a message if the file does not exist, and always print `done` at the end.

---

## Practice Answers

Try each question yourself first, then compare.

**Answer 1**

A **syntax error** is broken Python grammar. It is found before the program runs, so nothing runs. An **exception** happens while the program is running (the code is valid but something fails), and it can be caught with `try` / `except`.

**Answer 2**

| Code | Exception |
|---|---|
| `int("abc")` | `ValueError` |
| `10 / 0` | `ZeroDivisionError` |
| `[1, 2][5]` | `IndexError` |
| `{"a": 1}["b"]` | `KeyError` |
| `"a" + 1` | `TypeError` |
| `undefined_name` | `NameError` |

**Answer 3**
```python
try:
    print(10 / 0)
except ZeroDivisionError:
    print("Cannot divide by zero")
# Output: Cannot divide by zero
```

**Answer 4**
```python
try:
    int("abc")
except ValueError as e:
    print(e)
# Output: invalid literal for int() with base 10: 'abc'
```

**Answer 5**
```python
def to_int(text):
    try:
        return int(text)
    except ValueError:
        return None

print(to_int("42"))
print(to_int("abc"))
# Output: 42
# Output: None
```

**Answer 6**
```python
def inverse(text):
    try:
        return 1 / int(text)
    except ValueError:
        return "not a number"
    except ZeroDivisionError:
        return "zero has no inverse"

print(inverse("4"))
print(inverse("x"))
print(inverse("0"))
# Output: 0.25
# Output: not a number
# Output: zero has no inverse
```

**Answer 7**
```python
try:
    int(None)
except (ValueError, TypeError) as e:
    print(type(e).__name__)
# Output: TypeError
```

**Answer 8**

`IndexError` is a child of `LookupError`, so the first `except LookupError` catches it and the `IndexError` block can never run. Put the **specific** exception first:
```python
try:
    [1, 2, 3][10]
except IndexError:
    print("index")
except LookupError:
    print("lookup")
# Output: index
```

**Answer 9**

A bare `except:` catches **everything**, including `KeyboardInterrupt` (`Ctrl + C`) and `SystemExit`, and hides real bugs. Catch specific exceptions, or at most `except Exception`.

**Answer 10**
```python
while True:
    try:
        number = int(input("Enter a whole number: "))
        break
    except ValueError:
        print("Invalid, try again.")
print("You entered", number)
```
(`break` is reached only when `int()` succeeds.)

**Answer 11**

`else` runs only when the `try` block finished **without** an exception. It keeps the `try` block small.
```python
try:
    n = int("7")
except ValueError:
    print("bad")
else:
    print("ok", n)
# Output: ok 7
```

**Answer 12**

`finally` runs **always**: after success, after a handled exception, and even when an exception is not handled (before it moves on). It is used for clean-up.
```python
try:
    print("work")
finally:
    print("clean-up")
# Output: work
# Output: clean-up
```

**Answer 13**
```python
def f():
    try:
        return "try"
    finally:
        print("finally")
print(f())
# Output: finally
# Output: try
```
`finally` runs before the function returns.

**Answer 14**
```python
age = -5
try:
    if age < 0:
        raise ValueError("age cannot be negative")
except ValueError as e:
    print(e)
# Output: age cannot be negative
```

**Answer 15**
```python
def set_age(age):
    if not isinstance(age, int):
        raise TypeError("age must be a whole number")
    if age < 0 or age > 120:
        raise ValueError("age must be between 0 and 120")
    return age

print(set_age(30))
try:
    set_age(200)
except ValueError as e:
    print(e)
# Output: 30
# Output: age must be between 0 and 120
```

**Answer 16**

It **re-raises the same exception** that is being handled, so you can log it (or do some clean-up) and still let it continue upwards.

**Answer 17**

It raises a **different** exception while recording the original as its cause (`__cause__`). The traceback shows both errors: "The above exception was the direct cause of the following exception".

**Answer 18**
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

**Answer 19**

`assert` checks something that should **never** be false while developing and raises `AssertionError`. `raise` signals a real error condition on purpose. Asserts are **removed** when Python runs with `-O`, so a check on user input would silently disappear. Use `if` + `raise` for input validation.

**Answer 20**
```python
try:
    with open("missing.txt") as f:
        print(f.read())
except FileNotFoundError:
    print("file not found")
finally:
    print("done")
# Output: file not found
# Output: done
```

---

- [Back to practice index](../README.md)
