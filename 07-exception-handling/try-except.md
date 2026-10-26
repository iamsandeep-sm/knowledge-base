# try / except - Revision Notes ~ sm

> **In one line:** Put risky code in a **`try`** block. If an exception occurs, Python jumps to the matching **`except`** block instead of crashing, so your program can recover and continue.

## Table of Contents
- [Basic Syntax](#basic-syntax)
- [How It Runs (Dry Run)](#how-it-runs-dry-run)
- [Catching a Specific Exception](#catching-a-specific-exception)
- [Getting the Exception Object (as e)](#getting-the-exception-object-as-e)
- [Multiple except Blocks](#multiple-except-blocks)
- [One except for Several Types](#one-except-for-several-types)
- [Order of except Blocks](#order-of-except-blocks)
- [Catching Exception (and the Bare except)](#catching-exception-and-the-bare-except)
- [Ask Again Until the Input is Valid](#ask-again-until-the-input-is-valid)
- [Handling Errors from Functions](#handling-errors-from-functions)
- [Nested try](#nested-try)
- [EAFP vs LBYL](#eafp-vs-lbyl)
- [Best Practices](#best-practices)
- [Common Mistakes](#common-mistakes)

---

## Basic Syntax

```python
try:
    # code that might fail
except SomeError:
    # what to do if it fails
```

```python
try:
    result = 10 / 0
    print("This line is skipped")
except ZeroDivisionError:
    print("You cannot divide by zero")

print("Program continues")
# Output: You cannot divide by zero
# Output: Program continues
```
The program did not crash. Both `try` and `except` need a colon and an **indented** block.

---

## How It Runs (Dry Run)

| Situation | What Python does |
|---|---|
| No exception in `try` | Runs the whole `try` block, **skips** every `except` |
| Exception in `try`, and an `except` matches | **Stops** the `try` block at that line, runs the matching `except`, then continues after the whole statement |
| Exception in `try`, no `except` matches | The exception continues upwards; if nothing handles it, the program stops with a traceback |

Lines of the `try` block **after** the failing line never run.

```python
try:
    print("1")
    print(1 / 0)
    print("3")
except ZeroDivisionError:
    print("error")
# Output: 1
# Output: error
```

---

## Catching a Specific Exception

Name the exception you **expect**.

```python
try:
    number = int("abc")
except ValueError:
    print("That is not a number")
# Output: That is not a number
```

---

## Getting the Exception Object (as e)

`as e` stores the exception in a variable so you can read its message.

```python
try:
    int("abc")
except ValueError as e:
    print("Error:", e)
    print(type(e).__name__)
# Output: Error: invalid literal for int() with base 10: 'abc'
# Output: ValueError
```
`e` exists only inside the `except` block (Python deletes it at the end of the block).

| Way to see the error | Result for `1 / 0` |
|---|---|
| `str(e)` or `print(e)` | `division by zero` |
| `repr(e)` | `ZeroDivisionError('division by zero')` |
| `type(e).__name__` | `ZeroDivisionError` |
| `e.args` | `('division by zero',)` |

To print the full traceback while still handling the error, use `traceback.print_exc()` (in the `traceback` module) or `logging.exception(...)`.

---

## Multiple except Blocks

Different errors can need different responses.

```python
def convert(text):
    try:
        return 10 / int(text)
    except ValueError:
        return "not a number"
    except ZeroDivisionError:
        return "zero is not allowed"

print(convert("5"))
print(convert("abc"))
print(convert("0"))
# Output: 2.0
# Output: not a number
# Output: zero is not allowed
```
Python checks the `except` blocks **from top to bottom** and runs the **first** one that matches. Only one `except` block runs.

---

## One except for Several Types

Put the types in a **tuple** (with brackets):

```python
try:
    value = int("abc")
except (ValueError, TypeError) as e:
    print("Bad input:", e)
# Output: Bad input: invalid literal for int() with base 10: 'abc'
```

---

## Order of except Blocks

A parent class catches its children too, so put the **specific** exception **before** the **general** one.

```python
try:
    [1, 2, 3][10]
except IndexError:
    print("index problem")          # runs: specific first
except LookupError:
    print("general lookup problem")
# Output: index problem
```
If `LookupError` came first, it would catch the `IndexError` and the more specific block would never run. (`IndexError` and `KeyError` both belong to `LookupError`, see the family tree in the Syntax Errors note.)

---

## Catching Exception (and the Bare except)

`except Exception` catches almost every ordinary error. Use it only as a last resort, for example at the top of a program to log the problem.

```python
try:
    risky = 1 / 0
except Exception as e:
    print("Something went wrong:", type(e).__name__)
# Output: Something went wrong: ZeroDivisionError
```

A **bare** `except:` (no type) catches *everything*, even `KeyboardInterrupt` (`Ctrl + C`) and `SystemExit`, so the user cannot stop your program.

```python
try:
    ...
except:            # avoid: hides all errors, even Ctrl + C
    pass
```
Prefer specific exceptions. If you must catch broadly, write `except Exception`.

> Never write `except: pass` (silently ignoring errors). Bugs then become impossible to find.

---

## Ask Again Until the Input is Valid

A very common pattern: loop until the user gives valid input.

```python
while True:
    try:
        age = int(input("Enter your age: "))
        break                         # valid: leave the loop
    except ValueError:
        print("Please type a whole number.")

print("Your age is", age)
# User types: abc
# User types: 19
# Output: Enter your age: abc
# Output: Please type a whole number.
# Output: Enter your age: 19
# Output: Your age is 19
```
`break` is placed **inside** `try`, after the conversion succeeded. If `int()` fails, `break` is never reached and the loop repeats.

---

## Handling Errors from Functions

An exception travels **up** through the functions that called the failing one, until something handles it. You can handle it where it makes sense.

```python
def divide(a, b):
    return a / b

def safe_divide(a, b):
    try:
        return divide(a, b)
    except ZeroDivisionError:
        return None

print(safe_divide(10, 2))
print(safe_divide(10, 0))
# Output: 5.0
# Output: None
```

---

## Nested try

A `try` can be inside another `try` or inside an `except`.

```python
try:
    try:
        1 / 0
    except ValueError:
        print("inner")           # does not match
except ZeroDivisionError:
    print("outer caught it")
# Output: outer caught it
```
The inner block did not match, so the exception moved outwards. Do not nest more than necessary.

---

## EAFP vs LBYL

Two styles of dealing with possible errors:

| Style | Meaning | Example |
|---|---|---|
| **LBYL** | **L**ook **B**efore **Y**ou **L**eap: check first | `if key in d: value = d[key]` |
| **EAFP** | **E**asier to **A**sk **F**orgiveness than **P**ermission: just try | `try: value = d[key]` / `except KeyError: ...` |

```python
d = {"a": 1}

# LBYL
if "b" in d:
    print(d["b"])
else:
    print("missing")

# EAFP
try:
    print(d["b"])
except KeyError:
    print("missing")
# Output: missing
# Output: missing
```
Python programmers often prefer EAFP, because the check and the use happen together (for example, a file might be deleted between checking that it exists and opening it). For simple cases `d.get("b")` is shorter still.

---

## Best Practices

- Catch **specific** exceptions you know how to handle.
- Keep the `try` block **small**: only the line(s) that can fail.
- Do not hide errors: at least print or log them.
- Use `as e` when the message helps the user or the developer.
- Use `try` for **unexpected or external** problems (files, network, user input). Use a normal `if` for things you can easily check.
- Do not use exceptions as ordinary control flow for common cases.

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Bare `except:` | Hides every error, even `Ctrl + C` | `except ValueError:` or `except Exception:` |
| `except: pass` | Errors disappear silently | Handle, print or log them |
| General `except` before a specific one | The specific block never runs | Specific first |
| Large `try` block with many lines | Hard to know what failed | Wrap only the risky line |
| `except ValueError, TypeError:` (no brackets) | `SyntaxError: multiple exception types must be parenthesized` in Python 3.13 and older (Python 3.14 allows it only when there is no `as`, but brackets work in every version) | `except (ValueError, TypeError):` |
| Using `e` after the `except` block | `NameError` | Store what you need before the block ends |
| Forgetting that later `try` lines are skipped | Variables are never assigned | Assign defaults first or use `else` (next note) |

---

- [Back to section index](./README.md)
