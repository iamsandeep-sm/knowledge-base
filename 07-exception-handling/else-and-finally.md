# else and finally - Revision Notes ~ sm

> **In one line:** A `try` statement can also have an **`else`** block (runs only if **no exception** happened) and a **`finally`** block (runs **always**, whatever happened). `finally` is used for clean-up work such as closing files.

## Table of Contents
- [Full Structure](#full-structure)
- [The else Block](#the-else-block)
- [The finally Block](#the-finally-block)
- [All Four Together](#all-four-together)
- [When Each Block Runs](#when-each-block-runs)
- [finally Runs Even with return](#finally-runs-even-with-return)
- [try with finally Only](#try-with-finally-only)
- [Clean-up Example](#clean-up-example)
- [finally with break and continue](#finally-with-break-and-continue)
- [Common Mistakes](#common-mistakes)

---

## Full Structure

```python
try:
    ...          # code that might fail
except SomeError:
    ...          # runs if that error happened
else:
    ...          # runs if NO error happened
finally:
    ...          # runs ALWAYS
```
Order matters: `try`, then `except` blocks, then `else`, then `finally`. An `else` is allowed only if there is at least one `except`.

---

## The else Block

`else` runs **only when the `try` block finished without an exception**.

```python
try:
    number = int("42")
except ValueError:
    print("Not a number")
else:
    print("Converted:", number)
# Output: Converted: 42
```
With bad input the `except` runs and the `else` is skipped:
```python
try:
    number = int("abc")
except ValueError:
    print("Not a number")
else:
    print("Converted:", number)
# Output: Not a number
```

### Why not just put that code inside try?
Code in `else` is **not protected** by the `except` blocks. This keeps `try` small and prevents catching an error you did not mean to catch.

```python
try:
    value = int("5")           # only the risky line
except ValueError:
    print("bad number")
else:
    print(10 / (value - 5))    # this ZeroDivisionError is NOT hidden by the except above
# Error:  ZeroDivisionError: division by zero
```
If the `print` line were inside `try`, a different problem could be mistaken for a bad number.

---

## The finally Block

`finally` runs **no matter what**: after success, after a handled exception, and even when an exception is **not** handled.

```python
try:
    print("working")
finally:
    print("clean-up")
# Output: working
# Output: clean-up
```

```python
try:
    1 / 0
except ZeroDivisionError:
    print("caught")
finally:
    print("clean-up")
# Output: caught
# Output: clean-up
```
When an exception is **not** caught, `finally` still runs first, and then the exception continues upwards:
```python
try:
    try:
        1 / 0
    finally:
        print("cleanup")
except ZeroDivisionError:
    print("outer")
# Output: cleanup
# Output: outer
```

---

## All Four Together

```python
def divide(a, b):
    try:
        result = a / b
    except ZeroDivisionError:
        print("cannot divide by zero")
        return None
    else:
        print("division worked")
        return result
    finally:
        print("done")

print(divide(10, 2))
print(divide(1, 0))
# Output: division worked
# Output: done
# Output: 5.0
# Output: cannot divide by zero
# Output: done
# Output: None
```
Notice `done` is printed **before** the returned value reaches `print`.

---

## When Each Block Runs

| Situation | try | except | else | finally |
|---|---|---|---|---|
| No exception | runs fully | skipped | **runs** | **runs** |
| Exception, handled by an `except` | runs until the error | **runs** | skipped | **runs** |
| Exception, no matching `except` | runs until the error | skipped | skipped | **runs**, then the exception goes on |

---

## finally Runs Even with return

Even if `try` has a `return`, `finally` runs **before** the function actually returns.

```python
def test():
    try:
        return "from try"
    finally:
        print("finally runs first")

print(test())
# Output: finally runs first
# Output: from try
```
Avoid putting `return` (or `break` / `continue`) **inside** `finally`: it can override the real result and silently **swallow an exception**, which makes bugs very hard to find.

---

## try with finally Only

`except` is optional. A `try ... finally` does not handle the error; it only guarantees the clean-up, and the exception still travels on.

```python
try:
    print("opening")
    1 / 0
finally:
    print("closing")
# Output: opening
# Output: closing
# Error:  ZeroDivisionError: division by zero
```

---

## Clean-up Example

`finally` is meant for releasing things: closing files or connections, unlocking, resetting state.

```python
f = None
try:
    f = open("data.txt")
    print(f.read())
except FileNotFoundError:
    print("file not found")
finally:
    if f is not None:
        f.close()
    print("finished")
```
If `open` fails, `f` is still `None`, so the `if` avoids calling `close()` on nothing.

> For files there is a shorter and safer way: the **`with` statement**, which closes the file automatically even if an error happens. It is covered in the File Handling section (Context Manager note). Use `with` for files and `finally` for other clean-up.

---

## finally with break and continue

```python
for i in range(3):
    try:
        if i == 1:
            continue
        print("body", i)
    finally:
        print("finally", i)
# Output: body 0
# Output: finally 0
# Output: finally 1
# Output: body 2
# Output: finally 2
```
`finally` runs on every pass, even when `continue` skips the rest of the body.

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| `else` without `except` | `SyntaxError` | `else` needs at least one `except` |
| `else` before `except` | `SyntaxError` | Order: `try`, `except`, `else`, `finally` |
| Putting everything in `try` | Hides unrelated errors | Keep `try` small, move the rest to `else` |
| `return` inside `finally` | Replaces the real return value and can hide exceptions | Do not return from `finally` |
| Using a variable in `finally` that `try` never created | `NameError` / `UnboundLocalError` | Give it a starting value (`f = None`) first |
| Expecting `finally` to stop the exception | It only runs; the exception continues | Add an `except` to handle it |

---

- [Back to section index](./README.md)
