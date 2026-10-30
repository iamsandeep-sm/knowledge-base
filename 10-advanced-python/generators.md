# Generators - Revision Notes ~ sm

> **In one line:** A **generator** is a function that uses **`yield`** instead of `return`. Calling it does not run the code; it gives you an **iterator** that runs the function **one `yield` at a time**, pausing in between and remembering its place. It is the easiest way to write an iterator.

## Table of Contents
- [The Problem Generators Solve](#the-problem-generators-solve)
- [Your First Generator](#your-first-generator)
- [How It Runs (Dry Run)](#how-it-runs-dry-run)
- [yield vs return](#yield-vs-return)
- [Using a Generator](#using-a-generator)
- [Generators are One-Time](#generators-are-one-time)
- [Endless Generators](#endless-generators)
- [Practical Examples](#practical-examples)
- [Generator Pipelines](#generator-pipelines)
- [yield from](#yield-from)
- [A Generator for an Iterable Class](#a-generator-for-an-iterable-class)
- [send() and return Values](#send-and-return-values)
- [Generator Expression vs Generator Function](#generator-expression-vs-generator-function)
- [Generator vs List: Memory](#generator-vs-list-memory)
- [Common Mistakes](#common-mistakes)

---

## The Problem Generators Solve

A normal function builds the **whole result** before giving it back:

```python
def squares_list(n):
    result = []
    for i in range(n):
        result.append(i * i)
    return result
```
For a million items this builds a million-item list in memory first. A generator hands out **one value at a time** instead.

---

## Your First Generator

```python
def squares(n):
    for i in range(n):
        yield i * i

gen = squares(4)
print(type(gen))
print(list(gen))
# Output: <class 'generator'>
# Output: [0, 1, 4, 9]
```
- Any function that contains `yield` is a **generator function**.
- Calling it returns a **generator object** (an iterator). **No code runs yet.**
- The code runs only when values are requested, with `next()` or a `for` loop.

---

## How It Runs (Dry Run)

```python
def demo():
    print("start")
    yield 1
    print("middle")
    yield 2
    print("end")

g = demo()
print("created")
print(next(g))
print(next(g))
next(g)
# Output: created
# Output: start
# Output: 1
# Output: middle
# Output: 2
# Output: end
# Error:  StopIteration
```

| Step | What happens |
|---|---|
| `g = demo()` | A generator object is created. **Nothing runs** |
| 1st `next(g)` | Runs until the first `yield`: prints `start`, **pauses**, gives `1` |
| 2nd `next(g)` | **Continues** from that point: prints `middle`, pauses at `yield 2`, gives `2` |
| 3rd `next(g)` | Continues, prints `end`, the function finishes, and `StopIteration` is raised |

The local variables stay alive between pauses. That is the whole trick: **a function that can pause and resume**.

---

## yield vs return

| | `return` | `yield` |
|---|---|---|
| Effect | Ends the function | **Pauses** the function |
| Values given | One | Many, one per `yield` |
| Local variables | Lost | **Kept** until the next resume |
| Function called | Runs fully | Gives an iterator, runs step by step |

A bare `return` inside a generator simply **ends** it early.

```python
def until_negative(nums):
    for n in nums:
        if n < 0:
            return              # stop the generator
        yield n

print(list(until_negative([3, 5, -1, 7])))
# Output: [3, 5]
```

---

## Using a Generator

```python
def count_up(limit):
    n = 1
    while n <= limit:
        yield n
        n += 1

for x in count_up(3):
    print(x)
print(sum(count_up(5)))
print(list(count_up(3)))
# Output: 1
# Output: 2
# Output: 3
# Output: 15
# Output: [1, 2, 3]
```
A generator works anywhere an iterable does: `for`, `list()`, `sum()`, `max()`, `sorted()`, `in`, unpacking.

---

## Generators are One-Time

```python
g = count_up(3)
print(list(g))
print(list(g))
# Output: [1, 2, 3]
# Output: []
```
To start again, call the generator function again: `count_up(3)`.

---

## Endless Generators

A generator can run forever, because it produces values only when asked.

```python
def naturals():
    n = 1
    while True:
        yield n
        n += 1

from itertools import islice
print(list(islice(naturals(), 5)))

g = naturals()
print(next(g), next(g), next(g))
# Output: [1, 2, 3, 4, 5]
# Output: 1 2 3
```
Use `islice`, `next` or a `break` to take only what you need.

---

## Practical Examples

**Fibonacci numbers**
```python
def fib():
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b

from itertools import islice
print(list(islice(fib(), 10)))
# Output: [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```

**Read a big file line by line**
```python
def non_empty_lines(path):
    with open(path, encoding="utf-8") as f:
        for line in f:
            line = line.strip()
            if line:
                yield line

with open("sample.txt", "w", encoding="utf-8") as f:
    f.write("one\n\ntwo\n  \nthree\n")

print(list(non_empty_lines("sample.txt")))
# Output: ['one', 'two', 'three']
```
Only one line is in memory at a time, so a huge file is fine.

**Process data in chunks**
```python
def chunks(items, size):
    for i in range(0, len(items), size):
        yield items[i:i + size]

print(list(chunks([1, 2, 3, 4, 5], 2)))
# Output: [[1, 2], [3, 4], [5]]
```

---

## Generator Pipelines

Generators can be **chained**: each one takes the output of the previous one. Values flow through **one at a time**, with no big lists in between.

```python
def numbers():
    for n in range(1, 11):
        yield n

def evens(source):
    for n in source:
        if n % 2 == 0:
            yield n

def squared(source):
    for n in source:
        yield n * n

print(list(squared(evens(numbers()))))
# Output: [4, 16, 36, 64, 100]
```
This reads like an assembly line: numbers, keep the even ones, square them.

---

## yield from

`yield from iterable` yields every item of another iterable (or generator), saving a loop.

```python
def both(a, b):
    yield from a
    yield from b

print(list(both([1, 2], "xy")))
# Output: [1, 2, 'x', 'y']
```
It is also handy for recursion, such as flattening nested lists:

```python
def flatten(items):
    for item in items:
        if isinstance(item, list):
            yield from flatten(item)
        else:
            yield item

print(list(flatten([1, [2, [3, 4]], 5])))
# Output: [1, 2, 3, 4, 5]
```

---

## A Generator for an Iterable Class

Writing `__iter__` as a generator is the shortest way to make a class iterable (compare with the Iterators note).

```python
class Playlist:
    def __init__(self, songs):
        self.songs = songs

    def __iter__(self):
        for song in self.songs:
            yield song

p = Playlist(["a", "b"])
print(list(p))
print(list(p))
# Output: ['a', 'b']
# Output: ['a', 'b']
```
Each loop calls `__iter__`, which creates a **new** generator, so the object can be looped over many times.

---

## send() and return Values

`yield` is also an **expression**: `generator.send(value)` resumes the generator and makes `yield` produce that value inside it. (Advanced, and rarely needed.)

```python
def running_total():
    total = 0
    while True:
        number = yield total
        total += number

g = running_total()
next(g)                    # run to the first yield
print(g.send(10))
print(g.send(5))
# Output: 10
# Output: 15
```
A generator can also `return` a value. It becomes the `value` of the `StopIteration`:
```python
def with_result():
    yield 1
    return "finished"

g = with_result()
next(g)
try:
    next(g)
except StopIteration as e:
    print(e.value)
# Output: finished
```
`close()` stops a generator early.

---

## Generator Expression vs Generator Function

| | Generator expression | Generator function |
|---|---|---|
| Written as | `(x * x for x in nums)` | `def f(): ... yield ...` |
| Good for | A short one-line transformation | Longer logic, state, several `yield`s |

```python
nums = [1, 2, 3]
as_expression = (x * x for x in nums)

def squares(nums):
    for x in nums:
        yield x * x

print(list(as_expression) == list(squares(nums)))
# Output: True
```
Both create a generator. See the Generator Expressions note in the Comprehensions section.

---

## Generator vs List: Memory

```python
import sys

as_list = [n for n in range(100_000)]
as_gen = (n for n in range(100_000))
print(sys.getsizeof(as_list) > sys.getsizeof(as_gen))
# Output: True
```
| Use a list when | Use a generator when |
|---|---|
| You need indexing, `len()`, or to loop many times | You loop **once** |
| The data is small | The data is **huge or endless** |
| You need all the values at once | You want to start using values **immediately** |

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Expecting the function body to run when called | Nothing runs until `next()` or a loop | Iterate over the result |
| Using the generator twice | The second time it is empty | Call the function again |
| `return value` instead of `yield value` | A normal function, ends at once | Use `yield` |
| `len(gen)` or `gen[0]` | `TypeError` | Convert to a list (if finite) |
| `list(endless_generator())` | Never ends | `islice(...)` |
| Forgetting that `print(gen)` shows an object | `<generator object ...>` | `list(gen)` or loop |
| Calling `send()` before the first `next()` | `TypeError: can't send non-None value to a just-started generator` | `next(g)` first |
| Modifying a list while a generator is reading it | Surprising results | Do not change the data meanwhile |

```python
g = running_total()
g.send(5)
# Error:  TypeError: can't send non-None value to a just-started generator
```

---

- [Back to section index](./README.md)
