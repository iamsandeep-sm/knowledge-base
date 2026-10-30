# Iterators - Revision Notes ~ sm

> **In one line:** An **iterable** is anything you can loop over (list, string, dictionary, file ...). An **iterator** is the object that actually **hands out the items one at a time**. Every `for` loop gets an iterator with `iter()` and keeps asking it for the next item with `next()` until it raises `StopIteration`.

## Table of Contents
- [Iterable vs Iterator](#iterable-vs-iterator)
- [iter() and next()](#iter-and-next)
- [What a for Loop Really Does](#what-a-for-loop-really-does)
- [Iterators are One-Time](#iterators-are-one-time)
- [The Iterator Protocol](#the-iterator-protocol)
- [Building Your Own Iterator](#building-your-own-iterator)
- [A Separate Iterator for an Iterable Class](#a-separate-iterator-for-an-iterable-class)
- [Iterators You Already Use](#iterators-you-already-use)
- [iter() with a Sentinel](#iter-with-a-sentinel)
- [Lazy Evaluation and Memory](#lazy-evaluation-and-memory)
- [Handy Tools: itertools and islice](#handy-tools-itertools-and-islice)
- [Unpacking and Other Uses of Iteration](#unpacking-and-other-uses-of-iteration)
- [Common Mistakes](#common-mistakes)

---

## Iterable vs Iterator

| | Iterable | Iterator |
|---|---|---|
| Meaning | Can be looped over | Produces the items **one by one** and remembers where it is |
| Has `__iter__()` | Yes | Yes (returns itself) |
| Has `__next__()` | Not necessarily | **Yes** |
| Examples | `list`, `tuple`, `str`, `dict`, `set`, `range`, file | `iter([1, 2])`, `enumerate(...)`, `zip(...)`, file objects, generators |
| Can be used again | Yes (each loop starts fresh) | **No**, it is used up once |

Every iterator is iterable, but **not every iterable is an iterator**. A list is iterable, yet it is not an iterator:

```python
nums = [10, 20, 30]
next(nums)
# Error:  TypeError: 'list' object is not an iterator
```

---

## iter() and next()

`iter(iterable)` returns a new iterator. `next(iterator)` returns the next item.

```python
nums = [10, 20, 30]
it = iter(nums)

print(next(it))
print(next(it))
print(next(it))
# Output: 10
# Output: 20
# Output: 30
```
One more `next(it)` has nothing left to give, so it raises **`StopIteration`**:
```python
it = iter([1])
next(it)
next(it)
# Error:  StopIteration
```
`next(it, default)` returns the default instead of raising:
```python
it = iter([1])
print(next(it, "done"))
print(next(it, "done"))
# Output: 1
# Output: done
```

---

## What a for Loop Really Does

```python
for x in [10, 20, 30]:
    print(x)
```
is the same as:

```python
it = iter([10, 20, 30])
while True:
    try:
        x = next(it)
    except StopIteration:
        break
    print(x)
# Output: 10
# Output: 20
# Output: 30
```
| Step | What happens |
|---|---|
| 1 | `iter()` is called on the thing after `in` |
| 2 | `next()` is called again and again, and the value goes into `x` |
| 3 | When `StopIteration` is raised, the loop ends quietly |

---

## Iterators are One-Time

```python
it = iter([1, 2, 3])
print(list(it))
print(list(it))
# Output: [1, 2, 3]
# Output: []
```
The second `list(it)` is empty because the iterator is already used up. An **iterable** such as a list can be looped over as often as you like because every loop calls `iter()` and gets a **new** iterator.

An iterator returns **itself** from `iter()`:
```python
it = iter([1, 2])
print(iter(it) is it)
# Output: True
```

---

## The Iterator Protocol

The rules an object follows to work in a `for` loop:

| Method | Job |
|---|---|
| `__iter__(self)` | Return an **iterator** (for an iterator, return `self`) |
| `__next__(self)` | Return the **next item**, or raise `StopIteration` when there are none |

Objects that follow these rules work with `for`, `list()`, `sum()`, `in`, unpacking and every other tool that loops.

---

## Building Your Own Iterator

```python
class Countdown:
    def __init__(self, start):
        self.current = start

    def __iter__(self):
        return self

    def __next__(self):
        if self.current <= 0:
            raise StopIteration
        value = self.current
        self.current -= 1
        return value

for n in Countdown(3):
    print(n)
print(sum(Countdown(4)))
# Output: 3
# Output: 2
# Output: 1
# Output: 10
```
The object stores its **position** in `self.current`. Each `__next__` call moves it forward.

An iterator can also be **endless**, because items are produced only when asked:
```python
class Naturals:
    def __init__(self):
        self.n = 0

    def __iter__(self):
        return self

    def __next__(self):
        self.n += 1
        return self.n

it = Naturals()
print(next(it), next(it), next(it))
# Output: 1 2 3
```
Never loop over an endless iterator without a `break`.

---

## A Separate Iterator for an Iterable Class

If your class is an **iterable** (a collection), it should create a **fresh iterator** each time, so it can be looped over many times.

```python
class Playlist:
    def __init__(self, songs):
        self.songs = songs

    def __iter__(self):
        return iter(self.songs)            # a new iterator every time

p = Playlist(["a", "b", "c"])
print(list(p))
print(list(p))
# Output: ['a', 'b', 'c']
# Output: ['a', 'b', 'c']
```
If `__iter__` returned `self` and kept its position inside the class, the second loop would start where the first stopped. (Writing `__iter__` as a **generator** is even shorter, see the Generators note.)

---

## Iterators You Already Use

| Function | Returns an iterator of |
|---|---|
| `enumerate(x)` | `(index, item)` pairs |
| `zip(a, b)` | tuples of items, side by side |
| `map(f, x)` | `f(item)` for every item |
| `filter(f, x)` | items where `f(item)` is true |
| `reversed(x)` | items from the end |
| `open(file)` | the lines of a file |
| `range(n)` | is **iterable** (not an iterator), and can be looped over again and again |
| generator expressions `(x for ...)` | computed items |

```python
e = enumerate("ab")
print(next(e))
print(list(e))
# Output: (0, 'a')
# Output: [(1, 'b')]
```
```python
m = map(str.upper, ["a", "b"])
print(iter(m) is m)
print(list(m))
# Output: True
# Output: ['A', 'B']
```

---

## iter() with a Sentinel

`iter(function, sentinel)` calls the function again and again and stops when it returns the **sentinel** value.

```python
data = iter([3, 2, 1, 0, 5])
values = []
for n in iter(lambda: next(data), 0):
    values.append(n)
print(values)
# Output: [3, 2, 1]
```
A classic use is reading a file in chunks: `iter(lambda: f.read(1024), "")`.

---

## Lazy Evaluation and Memory

An iterator produces values **only when asked**, so it needs very little memory, however many items there are.

```python
nums = range(10_000_000)        # does not store ten million numbers
print(sum(nums))
# Output: 49999995000000
```
A file object reads **one line at a time**, so you can process a file larger than your memory with `for line in f:`.

---

## Handy Tools: itertools and islice

`itertools.islice(iterator, stop)` takes the first few items of any iterator, even an endless one. The `itertools` module (Standard Library section) has many more tools.

```python
from itertools import islice, count

print(list(islice(count(1), 5)))            # first 5 of an endless counter
print(list(islice("abcdef", 2, 4)))
# Output: [1, 2, 3, 4, 5]
# Output: ['c', 'd']
```
Normal slicing `x[:5]` does not work on iterators (they have no indexes).

---

## Unpacking and Other Uses of Iteration

Anything iterable can be unpacked, and many functions accept any iterable.

```python
a, b, c = iter("xyz")
print(a, b, c)

first, *rest = range(4)
print(first, rest)

print(max(iter([3, 9, 2])), "m" in iter("name"))
# Output: x y z
# Output: 0 [1, 2, 3]
# Output: 9 True
```

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| `next(my_list)` | `TypeError: 'list' object is not an iterator` | `next(iter(my_list))` |
| Looping twice over an iterator | The second loop is empty | Recreate it, or store the items in a list |
| `__next__` that never raises `StopIteration` | An endless loop | Raise `StopIteration` at the end |
| `__iter__` that does not return an iterator | `TypeError: iter() returned non-iterator` | Return `self` or `iter(...)` |
| Keeping the position in an iterable class and returning `self` | The second loop is empty | Return a new iterator from `__iter__` |
| `len(iterator)` or `iterator[0]` | `TypeError` | Convert to a list first (if it is finite) |
| `list(endless_iterator)` | Never finishes and eats memory | Use `islice` |
| Using `return value` where `yield` was meant (generators) | The function ends at once | See the Generators note |

---

- [Back to section index](./README.md)
