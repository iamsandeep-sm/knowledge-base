# Context Managers (Advanced) - Revision Notes ~ sm

> **In one line:** A **context manager** guarantees that **set-up and clean-up code run around a block**, even when an error happens. The basics (`with open(...)`, `__enter__`, `__exit__`) are in the File Handling section; this note goes deeper: how exceptions reach `__exit__`, `contextlib` tools, and useful patterns.

## Table of Contents
- [Quick Recap](#quick-recap)
- [What __exit__ Receives](#what-__exit__-receives)
- [Suppressing an Exception](#suppressing-an-exception)
- [The Value Given to as](#the-value-given-to-as)
- [@contextmanager](#contextmanager)
- [Exceptions Inside @contextmanager](#exceptions-inside-contextmanager)
- [Useful Patterns](#useful-patterns)
- [Ready-made Tools in contextlib](#ready-made-tools-in-contextlib)
- [Several Context Managers: ExitStack](#several-context-managers-exitstack)
- [Reusable and Reentrant](#reusable-and-reentrant)
- [Context Manager as a Decorator](#context-manager-as-a-decorator)
- [Async Context Managers](#async-context-managers)
- [Common Mistakes](#common-mistakes)

---

## Quick Recap

```python
with open("data.txt", "w", encoding="utf-8") as f:
    f.write("hi")
print(f.closed)
# Output: True
```
`with` calls `__enter__` when the block starts and **always** calls `__exit__` when it ends, whether the block finishes normally, uses `return` / `break`, or raises an error. The long form of what `with` does is a `try` / `finally`.

---

## What __exit__ Receives

`__exit__(self, exc_type, exc_value, traceback)` gets the details of any exception that happened in the block. If there was **no** exception, all three are `None`.

```python
class Watcher:
    def __enter__(self):
        print("enter")
        return self

    def __exit__(self, exc_type, exc_value, tb):
        print("exit:", exc_type, exc_value)
        return False                    # do not hide the exception

with Watcher():
    print("fine")

try:
    with Watcher():
        raise ValueError("boom")
except ValueError:
    print("caught outside")
# Output: enter
# Output: fine
# Output: exit: None None
# Output: enter
# Output: exit: <class 'ValueError'> boom
# Output: caught outside
```
| Return value of `__exit__` | Effect |
|---|---|
| `False` or `None` (nothing) | The exception **continues** upwards |
| `True` | The exception is **swallowed**; the program continues after the `with` block |

---

## Suppressing an Exception

Returning `True` hides the exception. Use it **only for exceptions you mean to ignore**, and check the type first.

```python
class Ignore:
    def __init__(self, *types):
        self.types = types

    def __enter__(self):
        return self

    def __exit__(self, exc_type, exc_value, tb):
        return exc_type is not None and issubclass(exc_type, self.types)

with Ignore(ZeroDivisionError):
    1 / 0
print("continued")

try:
    with Ignore(ZeroDivisionError):
        int("x")
except ValueError:
    print("ValueError was not ignored")
# Output: continued
# Output: ValueError was not ignored
```
The standard library already provides this as `contextlib.suppress`.

---

## The Value Given to as

`with obj as name:` stores whatever `__enter__` **returns** in `name`. It is **not** necessarily the context manager itself.

```python
class Resource:
    def __enter__(self):
        return "the handle"

    def __exit__(self, *args):
        pass

with Resource() as r:
    print(r)
# Output: the handle
```
`open()` returns the file object from `__enter__`, which is why `as f` gives you the file. Forgetting `return` in `__enter__` makes `as name` equal to `None`.

---

## @contextmanager

Writing a class with two methods is long. `contextlib.contextmanager` turns a **generator** with **one `yield`** into a context manager:

- code **before** `yield` is the set-up (`__enter__`)
- the **yielded value** goes to `as`
- code **after** `yield` is the clean-up (`__exit__`)

```python
from contextlib import contextmanager

@contextmanager
def timer(label):
    import time
    start = time.perf_counter()
    try:
        yield
    finally:
        print(f"{label} finished")

with timer("task"):
    total = sum(range(1000))
print(total)
# Output: task finished
# Output: 499500
```

```python
from contextlib import contextmanager

@contextmanager
def opened_upper(path):
    f = open(path, encoding="utf-8")
    try:
        yield f.read().upper()
    finally:
        f.close()

with open("sample.txt", "w", encoding="utf-8") as f:
    f.write("hello")

with opened_upper("sample.txt") as text:
    print(text)
# Output: HELLO
```

---

## Exceptions Inside @contextmanager

If the block raises an error, it is **thrown into the generator** at the `yield` line. Wrap the `yield` in `try` / `finally` so the clean-up always runs.

```python
from contextlib import contextmanager

@contextmanager
def guarded():
    print("set up")
    try:
        yield
    finally:
        print("clean up")

try:
    with guarded():
        print("working")
        raise RuntimeError("fail")
except RuntimeError:
    print("handled outside")
# Output: set up
# Output: working
# Output: clean up
# Output: handled outside
```
Without `try` / `finally`, the clean-up lines after `yield` would be **skipped** when an error occurs. To handle (swallow) the error inside the generator, catch it around the `yield`:

```python
from contextlib import contextmanager

@contextmanager
def quiet():
    try:
        yield
    except ZeroDivisionError:
        print("ignored")

with quiet():
    1 / 0
print("after")
# Output: ignored
# Output: after
```

---

## Useful Patterns

**Temporarily change a setting, then restore it**
```python
from contextlib import contextmanager
import os

@contextmanager
def working_directory(path):
    old = os.getcwd()
    os.chdir(path)
    try:
        yield
    finally:
        os.chdir(old)

before = os.getcwd()
with working_directory("/tmp"):
    print(os.getcwd())
print(os.getcwd() == before)
# Output: /tmp
# Output: True
```

**All-or-nothing changes (transaction)**
```python
from contextlib import contextmanager

@contextmanager
def transaction(data):
    backup = dict(data)
    try:
        yield data
    except Exception:
        data.clear()
        data.update(backup)             # roll back
        raise

account = {"balance": 100}
try:
    with transaction(account) as acc:
        acc["balance"] -= 500
        raise ValueError("insufficient funds")
except ValueError:
    pass
print(account)
# Output: {'balance': 100}
```

**Measuring time**
```python
import time
from contextlib import contextmanager

@contextmanager
def measure():
    result = {}
    start = time.perf_counter()
    try:
        yield result
    finally:
        result["seconds"] = time.perf_counter() - start

with measure() as r:
    sum(range(100_000))
print(r["seconds"] >= 0)
# Output: True
```

---

## Ready-made Tools in contextlib

| Tool | Does |
|---|---|
| `suppress(*exceptions)` | Ignore the listed exceptions |
| `redirect_stdout(target)` | Send `print()` output into an object (for example a `StringIO`) |
| `closing(obj)` | Call `obj.close()` at the end |
| `nullcontext(value)` | Does nothing; a stand-in when a context manager is optional |
| `ExitStack()` | Manage a **variable number** of context managers |
| `chdir(path)` | Temporarily change the folder (Python 3.11+) |

```python
import io
import contextlib

buffer = io.StringIO()
with contextlib.redirect_stdout(buffer):
    print("captured text")

print(repr(buffer.getvalue()))
# Output: 'captured text\n'
```
```python
import contextlib
import os

with contextlib.suppress(FileNotFoundError):
    os.remove("no_such_file.txt")
print("no error")
# Output: no error
```

---

## Several Context Managers: ExitStack

When you do not know in advance **how many** files or resources you will open, use `ExitStack`. Everything registered is cleaned up at the end, in reverse order.

```python
from contextlib import ExitStack

names = ["a.txt", "b.txt", "c.txt"]

with ExitStack() as stack:
    files = [stack.enter_context(open(n, "w", encoding="utf-8")) for n in names]
    for i, f in enumerate(files):
        f.write(f"file {i}")

print(all(f.closed for f in files))
# Output: True
```
For a fixed number you can simply write several in one `with`: `with open(a) as f, open(b) as g:`.

---

## Reusable and Reentrant

A context manager made from `@contextmanager` is **single-use**: the generator can run only once. Create a new one every time (call the function again).

```python
from contextlib import contextmanager

@contextmanager
def once():
    yield "value"

cm = once()
with cm as v:
    print(v)
# Output: value
```
Trying `with cm:` a **second** time fails with an error (its exact type and message depend on the Python version). The fix is to call `once()` again to get a fresh manager: `with once() as v:`.

A class-based manager can be reusable if `__enter__` re-creates its state each time. `suppress`, `redirect_stdout` and similar tools are reusable in simple cases, but check the documentation when you nest them.

---

## Context Manager as a Decorator

Managers made with `@contextmanager` can also be used as **decorators**: the clean-up code then runs around every call.

```python
from contextlib import contextmanager

@contextmanager
def announce():
    print("start")
    yield
    print("stop")

@announce()
def work():
    print("working")

work()
work()
# Output: start
# Output: working
# Output: stop
# Output: start
# Output: working
# Output: stop
```
(This works because they inherit from `ContextDecorator`. Each call re-creates the manager.)

---

## Async Context Managers

For asynchronous programs (`async` / `await`), the matching methods are `__aenter__` and `__aexit__`, used with `async with`. This is beyond these notes; the idea is exactly the same.

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| `__enter__` without `return` | `as name` is `None` | `return self` (or the value you want) |
| `__exit__` returns `True` by accident | Every exception is silently swallowed | Return `False` / nothing, unless you mean to suppress |
| `@contextmanager` without `try` / `finally` around `yield` | Clean-up is skipped when the block fails | Wrap `yield` in `try` / `finally` |
| Two `yield`s in a `@contextmanager` function | `RuntimeError: generator didn't stop` | Exactly one `yield` |
| Reusing the same `@contextmanager` object | An error (the exact type depends on the Python version) | Call the function again for a fresh one |
| `__exit__` with the wrong number of parameters | `TypeError` | `def __exit__(self, exc_type, exc, tb)` |
| Using the resource after the `with` block | It is closed or released | Use it inside the block |
| Forgetting the colon or indentation | `SyntaxError` / `IndentationError` | `with x as y:` and an indented body |

---

- [Back to section index](./README.md)
