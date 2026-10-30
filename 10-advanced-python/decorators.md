# Decorators - Revision Notes ~ sm

> **In one line:** A **decorator** is a function that **takes a function, adds behaviour around it, and returns the new version**. You apply it by writing `@decorator_name` on the line above a function definition, without changing the function's own code.

## Table of Contents
- [Ideas You Need First](#ideas-you-need-first)
- [A Decorator Step by Step](#a-decorator-step-by-step)
- [The @ Syntax](#the--syntax)
- [Functions with Arguments and Return Values](#functions-with-arguments-and-return-values)
- [functools.wraps](#functoolswraps)
- [A Template to Copy](#a-template-to-copy)
- [Practical Decorators](#practical-decorators)
- [Decorators with Arguments](#decorators-with-arguments)
- [Stacking Decorators](#stacking-decorators)
- [Decorating Methods](#decorating-methods)
- [Class-based Decorators](#class-based-decorators)
- [Built-in Decorators](#built-in-decorators)
- [Common Mistakes](#common-mistakes)

---

## Ideas You Need First

Decorators rest on three facts you already know:

| Fact | Example |
|---|---|
| Functions are **objects**: they can be stored and passed around | `f = print`, `apply(len, "abc")` |
| A function can **return** a function | `def outer(): ... return inner` |
| An inner function **remembers** outer variables (closure) | See the Closures note |

```python
def apply(func, value):
    return func(value)

print(apply(len, "hello"))
print(apply(str.upper, "hello"))
# Output: 5
# Output: HELLO
```

---

## A Decorator Step by Step

Goal: print a message before and after any function runs, **without editing the function**.

```python
def my_decorator(func):
    def wrapper():
        print("before")
        func()
        print("after")
    return wrapper

def say_hello():
    print("Hello!")

say_hello = my_decorator(say_hello)      # replace the name with the wrapped version
say_hello()
# Output: before
# Output: Hello!
# Output: after
```
| Step | What happens |
|---|---|
| 1 | `my_decorator(say_hello)` receives the original function as `func` |
| 2 | It defines `wrapper`, which calls `func()` between two prints |
| 3 | It returns `wrapper`, which is stored back into the name `say_hello` |
| 4 | Calling `say_hello()` now runs `wrapper()` |

---

## The @ Syntax

`@decorator` above a function is a shortcut for `function = decorator(function)`.

```python
def my_decorator(func):
    def wrapper():
        print("before")
        func()
        print("after")
    return wrapper

@my_decorator
def say_hello():
    print("Hello!")

say_hello()
# Output: before
# Output: Hello!
# Output: after
```
The `@` line runs **once, when the function is defined**. The wrapper runs **every time** the function is called.

---

## Functions with Arguments and Return Values

A general decorator must accept **any arguments** and **return the result**: use `*args` and `**kwargs`.

```python
def log_call(func):
    def wrapper(*args, **kwargs):
        print("calling", func.__name__, args, kwargs)
        result = func(*args, **kwargs)
        print("returned", result)
        return result                       # do not forget this
    return wrapper

@log_call
def add(a, b=0):
    return a + b

print(add(2, b=3))
# Output: calling add (2,) {'b': 3}
# Output: returned 5
# Output: 5
```
If `wrapper` forgot `return result`, `add(2, b=3)` would give `None`.

---

## functools.wraps

A decorated function is really the `wrapper`, so it loses its own name and docstring.

```python
def log_call(func):
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

@log_call
def add(a, b):
    """Add two numbers."""
    return a + b

print(add.__name__)
print(add.__doc__)
# Output: wrapper
# Output: None
```
Fix it with `functools.wraps`, which copies the name, docstring and other details to the wrapper:

```python
from functools import wraps

def log_call(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

@log_call
def add(a, b):
    """Add two numbers."""
    return a + b

print(add.__name__)
print(add.__doc__)
# Output: add
# Output: Add two numbers.
```
**Always use `@wraps(func)`** inside decorators. It keeps `help()`, debugging and tracebacks readable.

---

## A Template to Copy

```python
from functools import wraps

def decorator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        # code BEFORE the call
        result = func(*args, **kwargs)
        # code AFTER the call
        return result
    return wrapper
```

---

## Practical Decorators

**Timing a function**
```python
import time
from functools import wraps

def timer(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        result = func(*args, **kwargs)
        elapsed = time.perf_counter() - start
        print(f"{func.__name__} took {elapsed:.4f}s")
        return result
    return wrapper

@timer
def slow_sum(n):
    return sum(range(n))

total = slow_sum(1_000_000)
print(total)
# Output: 499999500000
```
(The "took ..." line also prints; its number differs every run.)

**Checking input**
```python
from functools import wraps

def positive_only(func):
    @wraps(func)
    def wrapper(n):
        if n < 0:
            raise ValueError("n must be positive")
        return func(n)
    return wrapper

@positive_only
def root(n):
    return n ** 0.5

print(root(16))
try:
    root(-4)
except ValueError as e:
    print(e)
# Output: 4.0
# Output: n must be positive
```

**Counting calls**
```python
from functools import wraps

def count_calls(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        wrapper.calls += 1
        return func(*args, **kwargs)
    wrapper.calls = 0
    return wrapper

@count_calls
def hi():
    return "hi"

hi(); hi(); hi()
print(hi.calls)
# Output: 3
```

**Caching results (remembering answers)**
```python
from functools import wraps

def memoize(func):
    cache = {}
    @wraps(func)
    def wrapper(n):
        if n not in cache:
            cache[n] = func(n)
        return cache[n]
    return wrapper

@memoize
def fib(n):
    return n if n < 2 else fib(n - 1) + fib(n - 2)

print(fib(60))
# Output: 1548008755920
```
Python already has this ready-made: `functools.cache` (3.9+) and `functools.lru_cache`.

---

## Decorators with Arguments

To write `@repeat(3)`, you need **one more level**: a function that takes the arguments and **returns** the real decorator.

```python
from functools import wraps

def repeat(times):                      # 1. takes the decorator's arguments
    def decorator(func):                # 2. the real decorator
        @wraps(func)
        def wrapper(*args, **kwargs):   # 3. the wrapper
            result = None
            for _ in range(times):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator

@repeat(3)
def hello():
    print("hello")

hello()
# Output: hello
# Output: hello
# Output: hello
```
`@repeat(3)` first **calls** `repeat(3)`, which returns `decorator`; then `decorator` is applied to the function. Three nested functions: arguments, function, call.

A decorator that **retries** on failure:
```python
from functools import wraps

def retry(attempts):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            for attempt in range(1, attempts + 1):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    print(f"attempt {attempt} failed: {e}")
            raise RuntimeError("all attempts failed")
        return wrapper
    return decorator

calls = {"n": 0}

@retry(3)
def flaky():
    calls["n"] += 1
    if calls["n"] < 3:
        raise ConnectionError("no connection")
    return "ok"

print(flaky())
# Output: attempt 1 failed: no connection
# Output: attempt 2 failed: no connection
# Output: ok
```

---

## Stacking Decorators

Several decorators can be stacked. They are applied **from the bottom up**, so the one nearest the function wraps it first.

```python
def bold(func):
    def wrapper():
        return "<b>" + func() + "</b>"
    return wrapper

def italic(func):
    def wrapper():
        return "<i>" + func() + "</i>"
    return wrapper

@bold
@italic
def text():
    return "hi"

print(text())
# Output: <b><i>hi</i></b>
```
`@bold @italic def text` means `text = bold(italic(text))`.

---

## Decorating Methods

Because the wrapper uses `*args`, the `self` argument passes straight through.

```python
from functools import wraps

def log(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print("calling", func.__name__)
        return func(*args, **kwargs)
    return wrapper

class Calc:
    @log
    def add(self, a, b):
        return a + b

print(Calc().add(2, 3))
# Output: calling add
# Output: 5
```

---

## Class-based Decorators

A class with `__call__` can act as a decorator. This is handy when the decorator needs to **keep state**.

```python
class CountCalls:
    def __init__(self, func):
        self.func = func
        self.calls = 0

    def __call__(self, *args, **kwargs):
        self.calls += 1
        return self.func(*args, **kwargs)

@CountCalls
def hello():
    return "hi"

hello()
hello()
print(hello.calls)
# Output: 2
```
(Decorators can also be applied to **classes**: `@dataclass` is a class decorator.)

---

## Built-in Decorators

| Decorator | Purpose | Note |
|---|---|---|
| `@property` | Method read like an attribute | Encapsulation |
| `@name.setter` | Setter of a property | Encapsulation |
| `@classmethod` | Method that receives the class | Instance and Class Attributes |
| `@staticmethod` | Method without `self` | Instance and Class Attributes |
| `@abstractmethod` | Child must override | Abstraction |
| `@functools.wraps(f)` | Keep the name and docstring | this note |
| `@functools.cache` | Remember results of a function | Recursion |
| `@functools.lru_cache(maxsize=n)` | Remember the most recent `n` results | |
| `@dataclasses.dataclass` | Generate `__init__`, `__repr__` ... | Constructors |
| `@contextlib.contextmanager` | Make a context manager from a generator | Context Managers |

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| `wrapper` does not `return` the result | The decorated function returns `None` | `return func(*args, **kwargs)` |
| Wrapper without `*args, **kwargs` | `TypeError` when called with arguments | Accept and pass on all arguments |
| Forgetting `@wraps(func)` | `__name__` and `__doc__` are lost | Add `@wraps(func)` |
| `return wrapper()` instead of `return wrapper` | The wrapper runs immediately | Return the function itself |
| `@repeat` without brackets when it needs arguments | The function becomes the argument `times` | `@repeat(3)` |
| Using a decorator before it is defined | `NameError` | Define the decorator first |
| Expecting the stacked order top-down | Applied bottom-up | Remember `@a @b f` = `a(b(f))` |
| Decorating a recursive function and calling the original name inside | Recursion goes through the wrapper (good for `cache`, surprising for logging) | Be aware of it |

---

- [Back to section index](./README.md)
