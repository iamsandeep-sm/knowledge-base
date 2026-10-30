# Advanced Python Practice

Related notes: [Iterators](../../10-advanced-python/iterators.md), [Generators](../../10-advanced-python/generators.md), [Closures](../../10-advanced-python/closures.md), [Decorators](../../10-advanced-python/decorators.md), [Context Managers](../../10-advanced-python/context-managers.md), [Type Hints](../../10-advanced-python/type-hints.md)

## Table of Contents
- [Practice Questions](#practice-questions)
- [Practice Answers](#practice-answers)

---

## Practice Questions

1. What is the difference between an iterable and an iterator? Is a list an iterator?
2. Use `iter()` and `next()` on `[10, 20]` by hand. What happens on the third `next()`?
3. Write the class `Countdown(n)` that counts down from `n` to `1` when used in a `for` loop.
4. Why can you loop over a generator only once? Show it.
5. Write a generator `squares(n)` that yields the squares of `0` to `n - 1`.
6. Write an endless generator `naturals()` and take the first five values with `itertools.islice`.
7. Rewrite this with `yield from`:
    ```python
    def both(a, b):
        for x in a:
            yield x
        for y in b:
            yield y
    ```
8. What is the difference between `[x for x in range(3)]` and `(x for x in range(3))`?
9. Write a generator `chunks(items, size)` that yields lists of `size` items.
10. What is a closure? Write `make_adder(n)` that returns a function adding `n`.
11. Write `make_counter()` that remembers a count between calls. Why is `nonlocal` needed?
12. What does this print and why? How do you fix it?
    ```python
    funcs = [lambda: i for i in range(3)]
    print([f() for f in funcs])
    ```
13. Write a decorator `shout` that makes the result of a function upper case.
14. Why should a decorator use `@functools.wraps`? Show what is lost without it.
15. Write a decorator `count_calls` that stores the number of calls in `wrapper.calls`.
16. Write a decorator with an argument, `@repeat(3)`, that runs a function three times.
17. In what order are stacked decorators applied? Show it with `@bold` and `@italic`.
18. What does `__exit__` returning `True` do?
19. Write a context manager `Timer` as a **class** with `__enter__` and `__exit__`.
20. Rewrite the same idea with `@contextmanager`. Why do we use `try` / `finally` around `yield`?
21. Use `contextlib.suppress` to delete a file that may not exist.
22. Add type hints to `def average(nums): return sum(nums) / len(nums)`.
23. Does Python stop you from calling `add(a: int, b: int)` with strings? What tool would warn you?
24. Write a function hint for `first_or_none(items)` that returns the first item, or `None` for an empty list.

---

## Practice Answers

Try each question yourself first, then compare.

**Answer 1**

An **iterable** can be looped over and gives you a fresh iterator (it has `__iter__`). An **iterator** hands out items one by one with `next()` (it has `__next__`) and is used up after one pass. A list is an **iterable**, not an iterator.
```python
nums = [1, 2]
print(iter(nums) is nums)
print(hasattr(nums, "__next__"))
# Output: False
# Output: False
```

**Answer 2**
```python
it = iter([10, 20])
print(next(it))
print(next(it))
next(it)
# Output: 10
# Output: 20
# Error: StopIteration
```
The third `next()` raises `StopIteration`, which is how a `for` loop knows to stop.

**Answer 3**
```python
class Countdown:
    def __init__(self, n):
        self.n = n

    def __iter__(self):
        return self

    def __next__(self):
        if self.n <= 0:
            raise StopIteration
        value = self.n
        self.n -= 1
        return value

for x in Countdown(3):
    print(x)
# Output: 3
# Output: 2
# Output: 1
```

**Answer 4**

A generator is an iterator: each value is produced once and then gone.
```python
g = (x for x in range(3))
print(list(g))
print(list(g))
# Output: [0, 1, 2]
# Output: []
```
To loop again, create a new generator.

**Answer 5**
```python
def squares(n):
    for i in range(n):
        yield i * i

print(list(squares(5)))
# Output: [0, 1, 4, 9, 16]
```

**Answer 6**
```python
from itertools import islice

def naturals():
    n = 1
    while True:
        yield n
        n += 1

print(list(islice(naturals(), 5)))
# Output: [1, 2, 3, 4, 5]
```
Never call `list()` on an endless generator.

**Answer 7**
```python
def both(a, b):
    yield from a
    yield from b

print(list(both([1, 2], "ab")))
# Output: [1, 2, 'a', 'b']
```

**Answer 8**

The square brackets build the **whole list** in memory now. The round brackets make a **generator** that produces one value at a time, only when asked, and can be used once.
```python
print([x for x in range(3)])
g = (x for x in range(3))
print(next(g))
# Output: [0, 1, 2]
# Output: 0
```

**Answer 9**
```python
def chunks(items, size):
    for i in range(0, len(items), size):
        yield items[i:i + size]

print(list(chunks([1, 2, 3, 4, 5], 2)))
# Output: [[1, 2], [3, 4], [5]]
```

**Answer 10**

A **closure** is an inner function that remembers variables of the outer function after the outer function has finished.
```python
def make_adder(n):
    def add(x):
        return x + n
    return add

add5 = make_adder(5)
print(add5(10))
# Output: 15
```

**Answer 11**
```python
def make_counter():
    count = 0
    def counter():
        nonlocal count
        count += 1
        return count
    return counter

c = make_counter()
print(c(), c(), c())
# Output: 1 2 3
```
`count += 1` assigns to `count`, so without `nonlocal` Python treats it as a new local variable and raises `UnboundLocalError`.

**Answer 12**
```python
funcs = [lambda: i for i in range(3)]
print([f() for f in funcs])
# Output: [2, 2, 2]
```
All the lambdas share the **same variable** `i`, which is `2` at the end (late binding). Fix with a default argument:
```python
funcs = [lambda i=i: i for i in range(3)]
print([f() for f in funcs])
# Output: [0, 1, 2]
```

**Answer 13**
```python
from functools import wraps

def shout(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs).upper()
    return wrapper

@shout
def hello(name):
    return "hello " + name

print(hello("riya"))
# Output: HELLO RIYA
```

**Answer 14**

`wraps` copies the original name and docstring onto the wrapper.
```python
from functools import wraps

def plain(func):
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

def good(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

@plain
def a():
    """Doc A."""

@good
def b():
    """Doc B."""

print(a.__name__, a.__doc__)
print(b.__name__, b.__doc__)
# Output: wrapper None
# Output: b Doc B.
```

**Answer 15**
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

hi()
hi()
print(hi.calls)
# Output: 2
```

**Answer 16**
```python
from functools import wraps

def repeat(times):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
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
Three levels: the arguments, the function, the call.

**Answer 17**

From the **bottom up**: the decorator nearest the function is applied first.
```python
def bold(func):
    return lambda: "<b>" + func() + "</b>"

def italic(func):
    return lambda: "<i>" + func() + "</i>"

@bold
@italic
def text():
    return "hi"

print(text())
# Output: <b><i>hi</i></b>
```
It is `text = bold(italic(text))`.

**Answer 18**

It **swallows** the exception: the program continues after the `with` block as if nothing happened. `False` or `None` lets the exception continue upwards.
```python
class Quiet:
    def __enter__(self):
        return self

    def __exit__(self, exc_type, exc, tb):
        return True

with Quiet():
    1 / 0
print("still running")
# Output: still running
```

**Answer 19**
```python
import time

class Timer:
    def __enter__(self):
        self.start = time.perf_counter()
        return self

    def __exit__(self, exc_type, exc, tb):
        self.seconds = time.perf_counter() - self.start
        return False

with Timer() as t:
    sum(range(100_000))
print(t.seconds >= 0)
# Output: True
```

**Answer 20**
```python
import time
from contextlib import contextmanager

@contextmanager
def timer():
    result = {}
    start = time.perf_counter()
    try:
        yield result
    finally:
        result["seconds"] = time.perf_counter() - start

with timer() as t:
    sum(range(100_000))
print(t["seconds"] >= 0)
# Output: True
```
If the block raises an error, the exception is thrown into the generator at the `yield`. Without `try` / `finally` the clean-up lines would be skipped.

**Answer 21**
```python
import contextlib
import os

with contextlib.suppress(FileNotFoundError):
    os.remove("missing.txt")
print("done")
# Output: done
```

**Answer 22**
```python
def average(nums: list[float]) -> float:
    return sum(nums) / len(nums)

print(average([1.0, 2.0, 6.0]))
# Output: 3.0
```
`list[float]` needs Python 3.9 or newer.

**Answer 23**

No. Python **ignores type hints at run time**.
```python
def add(a: int, b: int) -> int:
    return a + b

print(add("x", "y"))
# Output: xy
```
A type checker such as **mypy** or **pyright** (or an editor like VS Code or PyCharm) warns you **before** you run the program.

**Answer 24**
```python
def first_or_none(items: list[int]) -> int | None:
    return items[0] if items else None

print(first_or_none([4, 5]), first_or_none([]))
# Output: 4 None
```
`int | None` needs Python 3.10 or newer; older code writes `Optional[int]`.

---

- [Back to practice index](../README.md)
