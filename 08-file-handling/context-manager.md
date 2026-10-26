# Context Managers (the with Statement) - Revision Notes ~ sm

> **In one line:** A **context manager** sets something up when a block starts and **cleans it up automatically** when the block ends, even if an error happens. The **`with`** statement is how you use one, and `with open(...) as f:` is the standard way to work with files.

## Table of Contents
- [The Problem with open() and close()](#the-problem-with-open-and-close)
- [The with Statement](#the-with-statement)
- [How with Works](#how-with-works)
- [with Closes the File Even After an Error](#with-closes-the-file-even-after-an-error)
- [Several Files in One with](#several-files-in-one-with)
- [with Handles More Than Files](#with-handles-more-than-files)
- [Catching Errors Around with](#catching-errors-around-with)
- [Writing Your Own Context Manager (Class)](#writing-your-own-context-manager-class)
- [Writing One with contextlib](#writing-one-with-contextlib)
- [Common Mistakes](#common-mistakes)

---

## The Problem with open() and close()

```python
f = open("data.txt", "w", encoding="utf-8")
f.write("hello")
f.close()               # easy to forget
```
If an error happens **between** `open` and `close`, the `close()` line never runs and the file stays open (and data may be lost).

The long, safe way uses `try` / `finally`:
```python
f = open("data.txt", "w", encoding="utf-8")
try:
    f.write("hello")
finally:
    f.close()           # always runs
```
That works but is wordy. `with` does the same in less code.

---

## The with Statement

```python
with open("data.txt", "w", encoding="utf-8") as f:
    f.write("hello")

print(f.closed)
# Output: True
```

| Part | Meaning |
|---|---|
| `with` | Start using a context manager |
| `open(...)` | The context manager (it creates the file object) |
| `as f` | The name for what the context manager gives you |
| indented block | The code that uses it |
| after the block | Clean-up happens automatically (the file is closed) |

---

## How with Works

A context manager is an object with two special methods:

| Method | Runs | Job |
|---|---|---|
| `__enter__()` | When the `with` block **starts** | Set up, and return the value for `as` |
| `__exit__()` | When the block **ends**, for any reason | Clean up (for a file: close it) |

`with` guarantees that `__exit__` is called, whether the block finishes normally, uses `return` or `break`, or raises an exception. It is the same guarantee as `try` / `finally`, packaged neatly.

---

## with Closes the File Even After an Error

```python
try:
    with open("data.txt", "w", encoding="utf-8") as f:
        f.write("start")
        1 / 0                      # error inside the block
except ZeroDivisionError:
    print("error happened")

print(f.closed)
# Output: error happened
# Output: True
```
The file was closed even though the block ended with an error, and the text written before the error is saved.

---

## Several Files in One with

Separate them with commas. They are closed in the **reverse** order.

```python
with open("a.txt", "w", encoding="utf-8") as f:
    f.write("one\ntwo\n")

with open("a.txt", encoding="utf-8") as src, open("b.txt", "w", encoding="utf-8") as dst:
    for line in src:
        dst.write(line.upper())

with open("b.txt", encoding="utf-8") as f:
    print(f.read().splitlines())
# Output: ['ONE', 'TWO']
```
For a long list, put the files in brackets (allowed in Python 3.10 and newer):
```python
with (
    open("a.txt", encoding="utf-8") as src,
    open("b.txt", "w", encoding="utf-8") as dst,
):
    dst.write(src.read())
```

---

## with Handles More Than Files

Many things in Python are context managers:

| Used for | Example |
|---|---|
| Files | `with open("f.txt") as f:` |
| Locks (threads) | `with lock:` |
| Database connections | `with sqlite3.connect("db.sqlite3") as conn:` (commits or rolls back the transaction; read the library docs, it does not close the connection) |
| Temporary folders | `with tempfile.TemporaryDirectory() as d:` |
| Hiding specific errors | `with contextlib.suppress(FileNotFoundError):` |
| Timing and testing tools | many libraries |

```python
import contextlib
import os

with contextlib.suppress(FileNotFoundError):
    os.remove("does_not_exist.txt")     # no error, ignored
print("continued")
# Output: continued
```

---

## Catching Errors Around with

`with` closes the file, but it does **not** handle the error. Put `try` / `except` around it for a friendly message.

```python
try:
    with open("missing.txt", encoding="utf-8") as f:
        print(f.read())
except FileNotFoundError:
    print("Sorry, that file does not exist")
# Output: Sorry, that file does not exist
```

---

## Writing Your Own Context Manager (Class)

Add `__enter__` and `__exit__` to a class. (Classes are explained in the Object-Oriented Programming section.)

```python
class Timer:
    def __enter__(self):
        print("start")
        return self

    def __exit__(self, exc_type, exc_value, traceback):
        print("stop")
        return False        # False: do not hide an exception

with Timer() as t:
    print("working")
# Output: start
# Output: working
# Output: stop
```
`__exit__` receives the exception details (or `None` values when there was no error). Returning `True` would **swallow** the exception, so normally return `False` (or nothing).

---

## Writing One with contextlib

The `@contextmanager` decorator turns a **generator function** with one `yield` into a context manager. Code before `yield` is the set-up, code after it is the clean-up.

```python
from contextlib import contextmanager

@contextmanager
def opened(name):
    print("opening", name)
    try:
        yield name.upper()
    finally:
        print("closing", name)

with opened("data") as value:
    print("using", value)
# Output: opening data
# Output: using DATA
# Output: closing data
```
The `try` / `finally` around `yield` makes sure the clean-up runs even if the block raises an error. Decorators and generators are covered in the Advanced Python section.

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Using the file after the `with` block | `ValueError: I/O operation on closed file.` | Do all the work inside the block |
| Forgetting the colon after `with ...` | `SyntaxError: expected ':'` | Add `:` |
| Opening the file with `open()` and never closing it | Data may not be saved; the file stays locked | Use `with` |
| Expecting `with` to handle the error | The exception still propagates | Wrap in `try` / `except` |
| `with open("f.txt") as f` followed by no block | `IndentationError` | Indent the body |
| Returning `True` from `__exit__` by accident | Exceptions are silently swallowed | Return `False` or nothing |
| Using `as` for something that does not return a value you need | Not an error | `as` is optional: `with lock:` |

---

- [Back to section index](./README.md)
