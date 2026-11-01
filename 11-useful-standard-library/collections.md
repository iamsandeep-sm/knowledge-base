# collections - Revision Notes ~ sm

> **In one line:** The **`collections`** module adds **specialised container types** that improve on the built-in list, dict and tuple for common jobs: **`Counter`** (counting), **`defaultdict`** (dictionaries with automatic defaults), **`deque`** (fast queues), **`namedtuple`** (tuples with named fields), plus `OrderedDict` and `ChainMap`.

## Table of Contents
- [Overview](#overview)
- [Counter](#counter)
- [defaultdict](#defaultdict)
- [deque](#deque)
- [namedtuple](#namedtuple)
- [OrderedDict](#ordereddict)
- [ChainMap](#chainmap)
- [Choosing the Right Container](#choosing-the-right-container)
- [Practical Examples](#practical-examples)
- [Common Mistakes](#common-mistakes)

---

## Overview

```python
from collections import Counter, defaultdict, deque, namedtuple, OrderedDict, ChainMap
```
| Type | One-line purpose |
|---|---|
| `Counter` | A dictionary that **counts** things |
| `defaultdict` | A dictionary that creates a **default value** for a missing key |
| `deque` | A **double-ended queue**: fast add and remove at both ends |
| `namedtuple` | A tuple whose items also have **names** |
| `OrderedDict` | A dictionary with order-aware extras (rarely needed now) |
| `ChainMap` | Several dictionaries searched **as one** |

(The abstract base classes in `collections.abc`, such as `Iterable` and `Mapping`, are used for type hints and `isinstance` checks. See the Type Hints note.)

---

## Counter

A `Counter` counts how many times each item appears.

```python
from collections import Counter

letters = Counter("banana")
print(letters)
print(letters["a"])
print(letters["z"])
# Output: Counter({'a': 3, 'n': 2, 'b': 1})
# Output: 3
# Output: 0
```
A missing item counts as `0` (no `KeyError`, and the item is not added).

**Create it from any iterable, a dictionary or keywords**
```python
from collections import Counter

print(Counter(["red", "blue", "red", "red"]))
print(Counter({"a": 2, "b": 1}))
print(Counter(a=3, b=1))
# Output: Counter({'red': 3, 'blue': 1})
# Output: Counter({'a': 2, 'b': 1})
# Output: Counter({'a': 3, 'b': 1})
```

**Most common items**
```python
from collections import Counter

words = "the cat and the hat and the bat".split()
counts = Counter(words)
print(counts.most_common(2))
print(counts.most_common())
# Output: [('the', 3), ('and', 2)]
# Output: [('the', 3), ('and', 2), ('cat', 1), ('hat', 1), ('bat', 1)]
```
`most_common(n)` returns the `n` biggest as `(item, count)` pairs, largest first; without `n` it returns all. Items with equal counts keep the order in which they were first seen.

**Updating and subtracting**
```python
from collections import Counter

c = Counter("aab")
c.update("abc")
print(c)
c.subtract("aa")
print(c)
# Output: Counter({'a': 3, 'b': 2, 'c': 1})
# Output: Counter({'b': 2, 'a': 1, 'c': 1})
```
(A `Counter` prints its items from the biggest count to the smallest.) `update()` **adds** counts (unlike `dict.update`, which replaces). `subtract()` can go below zero.

**Arithmetic between counters**
```python
from collections import Counter

a = Counter(x=3, y=1)
b = Counter(x=1, y=2)

print(a + b)
print(a - b)
print(a & b)
print(a | b)
# Output: Counter({'x': 4, 'y': 3})
# Output: Counter({'x': 2})
# Output: Counter({'x': 1, 'y': 1})
# Output: Counter({'x': 3, 'y': 2})
```
| Operator | Meaning |
|---|---|
| `a + b` | Add counts |
| `a - b` | Subtract, **dropping zero and negative** results |
| `a & b` | Minimum of each count (intersection) |
| `a \| b` | Maximum of each count (union) |

**Other useful methods**
```python
from collections import Counter

c = Counter("banana")
print(sorted(c.elements()))
print(c.total())
print(list(c))
# Output: ['a', 'a', 'a', 'b', 'n', 'n']
# Output: 6
# Output: ['b', 'a', 'n']
```
`elements()` repeats each item by its count. `total()` (Python 3.10+) is the sum of all counts. A `Counter` is a dictionary, so `list(c)` gives its keys in the order they were first added.

**Anagram check with Counter**
```python
from collections import Counter

def is_anagram(a, b):
    return Counter(a.lower()) == Counter(b.lower())

print(is_anagram("Listen", "Silent"), is_anagram("abc", "abd"))
# Output: True False
```

---

## defaultdict

A normal dictionary raises `KeyError` for a missing key. A `defaultdict` calls a **factory function** to create a default value instead.

```python
from collections import defaultdict

d = defaultdict(int)
for ch in "hello":
    d[ch] += 1
print(dict(d))
# Output: {'h': 1, 'e': 1, 'l': 2, 'o': 1}
```
`defaultdict(int)` creates `0` for a new key (because `int()` is `0`), so `d[ch] += 1` just works.

| Factory | Default value | Typical use |
|---|---|---|
| `int` | `0` | Counting |
| `list` | `[]` | Grouping items |
| `set` | `set()` | Grouping unique items |
| `str` | `""` | Joining text |
| `lambda: "N/A"` | `"N/A"` | Any custom default |
| `dict` | `{}` | Two-level dictionaries |

**Grouping**
```python
from collections import defaultdict

words = ["apple", "avocado", "banana", "blueberry", "cherry"]
by_letter = defaultdict(list)
for word in words:
    by_letter[word[0]].append(word)

print(dict(by_letter))
# Output: {'a': ['apple', 'avocado'], 'b': ['banana', 'blueberry'], 'c': ['cherry']}
```
Without `defaultdict` you would write `if key not in d: d[key] = []` first.

**A custom default**
```python
from collections import defaultdict

phone = defaultdict(lambda: "unknown")
phone["Riya"] = "98765"
print(phone["Riya"], phone["Amit"])
print(sorted(phone))
# Output: 98765 unknown
# Output: ['Amit', 'Riya']
```
**Reading a missing key adds it.** `phone["Amit"]` created an entry. To look something up without adding it, use `d.get(key)` or `key in d`:
```python
from collections import defaultdict

d = defaultdict(list)
print("x" in d)
print(d.get("x"))
print(len(d))
d["x"]
print(len(d))
# Output: False
# Output: None
# Output: 0
# Output: 1
```
Assigning `d.default_factory = None` turns it back into an ordinary strict dictionary.

---

## deque

A `deque` (say "deck") is a **double-ended queue**. Adding or removing at **either end** is fast (O(1)). A plain list is fast only at the right end: `list.pop(0)` and `list.insert(0, x)` move every item (O(n)).

```python
from collections import deque

q = deque([1, 2, 3])
q.append(4)
q.appendleft(0)
print(q)
print(q.pop())
print(q.popleft())
print(q)
# Output: deque([0, 1, 2, 3, 4])
# Output: 4
# Output: 0
# Output: deque([1, 2, 3])
```
| Method | Does |
|---|---|
| `append(x)` / `appendleft(x)` | Add at the right / left |
| `pop()` / `popleft()` | Remove and return from the right / left |
| `extend(iterable)` / `extendleft(iterable)` | Add many (`extendleft` reverses the order) |
| `rotate(n)` | Rotate right by `n` (negative: left) |
| `clear()`, `count(x)`, `remove(x)`, `reverse()` | Like a list |

```python
from collections import deque

d = deque([1, 2, 3, 4])
d.rotate(1)
print(d)
d.rotate(-2)
print(d)

e = deque([1, 2, 3])
e.extendleft([10, 20])
print(e)
# Output: deque([4, 1, 2, 3])
# Output: deque([2, 3, 4, 1])
# Output: deque([20, 10, 1, 2, 3])
```

**`maxlen`: a window that keeps only the newest items**
```python
from collections import deque

last3 = deque(maxlen=3)
for n in range(1, 6):
    last3.append(n)
    print(list(last3))
# Output: [1]
# Output: [1, 2]
# Output: [1, 2, 3]
# Output: [2, 3, 4]
# Output: [3, 4, 5]
```
When full, adding on one side **drops** an item from the other side. Perfect for "the last N lines", a moving average, or history.

**Queue (first in, first out)**
```python
from collections import deque

line = deque()
line.append("Riya")
line.append("Amit")
line.append("Zoya")
print(line.popleft())
print(line.popleft())
print(list(line))
# Output: Riya
# Output: Amit
# Output: ['Zoya']
```
**Stack (last in, first out)** works with a plain list (`append` and `pop`) or a deque.

`popleft()` and `pop()` on an empty deque raise `IndexError`. Indexing the middle (`d[500]`) is O(n) for a deque, so use a list when you need fast random access.

---

## namedtuple

A `namedtuple` is a tuple whose positions also have **names**, making code self-explanatory. It is still **immutable**.

```python
from collections import namedtuple

Point = namedtuple("Point", ["x", "y"])
p = Point(3, 4)

print(p)
print(p.x, p.y)
print(p[0], p[1])
x, y = p
print(x + y)
# Output: Point(x=3, y=4)
# Output: 3 4
# Output: 3 4
# Output: 7
```
You can also write the field names as one string: `namedtuple("Point", "x y")`.

```python
from collections import namedtuple

Student = namedtuple("Student", "name marks grade", defaults=["B"])
s = Student("Riya", 82)
print(s)
print(s._replace(marks=90))
print(s._asdict())
print(Student._fields)
# Output: Student(name='Riya', marks=82, grade='B')
# Output: Student(name='Riya', marks=90, grade='B')
# Output: {'name': 'Riya', 'marks': 82, 'grade': 'B'}
# Output: ('name', 'marks', 'grade')
```
| Feature | Meaning |
|---|---|
| `_replace(**changes)` | A **new** tuple with some fields changed (the original is not modified) |
| `_asdict()` | Convert to a dictionary |
| `_fields` | The field names |
| `_make(iterable)` | Build one from a list |
| `defaults=[...]` | Defaults for the **last** fields |

Trying to assign `p.x = 10` raises `AttributeError`, because it is immutable.

**namedtuple, dataclass or class?** Use `namedtuple` for small, fixed, read-only records that should still behave like tuples (unpacking, as dictionary keys). Use a `@dataclass` (see the Constructors note) when you want mutable fields, methods and type hints. Both can also be written with type hints: `typing.NamedTuple`.
```python
from typing import NamedTuple

class Item(NamedTuple):
    name: str
    price: float = 0.0

print(Item("pen", 5.5))
# Output: Item(name='pen', price=5.5)
```

---

## OrderedDict

Since Python 3.7 an ordinary `dict` also **remembers insertion order**, so `OrderedDict` is needed less. It still has two differences:

```python
from collections import OrderedDict

a = OrderedDict([("x", 1), ("y", 2)])
b = OrderedDict([("y", 2), ("x", 1)])
print(a == b)
print(dict(a) == dict(b))

a.move_to_end("x")
print(list(a))
a.move_to_end("y", last=False)
print(list(a))
# Output: False
# Output: True
# Output: ['y', 'x']
# Output: ['y', 'x']
```
1. Two `OrderedDict`s are equal only if the **order is the same** too (plain dictionaries ignore order when comparing).
2. `move_to_end(key)` moves an item to the end (or to the start with `last=False`). That makes it good for a simple **LRU cache** ("least recently used").

```python
from collections import OrderedDict

class LRU:
    def __init__(self, size):
        self.size = size
        self.data = OrderedDict()

    def get(self, key):
        if key not in self.data:
            return None
        self.data.move_to_end(key)
        return self.data[key]

    def put(self, key, value):
        self.data[key] = value
        self.data.move_to_end(key)
        if len(self.data) > self.size:
            self.data.popitem(last=False)

cache = LRU(2)
cache.put("a", 1)
cache.put("b", 2)
cache.get("a")
cache.put("c", 3)
print(list(cache.data))
# Output: ['a', 'c']
```
`b` was the least recently used, so it was dropped. (For functions, `functools.lru_cache` does this for you.)

---

## ChainMap

A `ChainMap` links several dictionaries so they act as **one**. A lookup searches the dictionaries **in order** and returns the first match.

```python
from collections import ChainMap

defaults = {"color": "blue", "size": "M"}
user = {"color": "red"}
settings = ChainMap(user, defaults)

print(settings["color"])
print(settings["size"])
print(dict(settings))
# Output: red
# Output: M
# Output: {'color': 'red', 'size': 'M'}
```
The user's choice wins; anything not set falls back to the defaults. **Writing** goes only to the **first** dictionary:
```python
from collections import ChainMap

defaults = {"color": "blue"}
user = {}
settings = ChainMap(user, defaults)
settings["color"] = "green"
print(user)
print(defaults)
# Output: {'color': 'green'}
# Output: {'color': 'blue'}
```
It never copies the data, so it reflects later changes of the underlying dictionaries. Good for layered settings (command line, then environment, then defaults) and for variable scopes.

---

## Choosing the Right Container

| I need to... | Use |
|---|---|
| Count occurrences | `Counter` |
| Group items by a key | `defaultdict(list)` |
| Have a default value for missing keys | `defaultdict(factory)` |
| A fast queue, or "the last N items" | `deque` (`maxlen=`) |
| A small read-only record with named fields | `namedtuple` |
| Reorder dictionary items or build an LRU cache | `OrderedDict` |
| Layer settings from several dictionaries | `ChainMap` |
| Everything else | plain `list`, `dict`, `set`, `tuple` |

---

## Practical Examples

**Word frequency**
```python
from collections import Counter

text = "to be or not to be that is the question to be"
top = Counter(text.split()).most_common(3)
print(top)
# Output: [('to', 3), ('be', 3), ('or', 1)]
```

**Group students by grade**
```python
from collections import defaultdict

marks = {"Riya": 91, "Amit": 78, "Zoya": 85, "Kabir": 72}
groups = defaultdict(list)
for name, mark in marks.items():
    grade = "A" if mark >= 85 else "B"
    groups[grade].append(name)
print(dict(groups))
# Output: {'A': ['Riya', 'Zoya'], 'B': ['Amit', 'Kabir']}
```

**Moving average of the last 3 values**
```python
from collections import deque

window = deque(maxlen=3)
for value in [10, 20, 30, 40, 50]:
    window.append(value)
    print(sum(window) / len(window))
# Output: 10.0
# Output: 15.0
# Output: 20.0
# Output: 30.0
# Output: 40.0
```

**Breadth-first search with a deque**
```python
from collections import deque

graph = {"A": ["B", "C"], "B": ["D"], "C": ["D"], "D": []}

def bfs(start):
    seen = {start}
    queue = deque([start])
    order = []
    while queue:
        node = queue.popleft()
        order.append(node)
        for nxt in graph[node]:
            if nxt not in seen:
                seen.add(nxt)
                queue.append(nxt)
    return order

print(bfs("A"))
# Output: ['A', 'B', 'C', 'D']
```

**Two-level counting**
```python
from collections import defaultdict

sales = [("north", "pen", 5), ("north", "pen", 3), ("south", "ink", 2)]
totals = defaultdict(lambda: defaultdict(int))
for region, product, qty in sales:
    totals[region][product] += qty
print({r: dict(p) for r, p in totals.items()})
# Output: {'north': {'pen': 8}, 'south': {'ink': 2}}
```

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| `defaultdict(list())` or `defaultdict([])` | `TypeError: first argument must be callable` | Pass the **function**: `defaultdict(list)` |
| Reading a missing `defaultdict` key to "check" it | The key gets created | `key in d` or `d.get(key)` |
| Expecting `Counter.update(x)` to replace like `dict.update` | It **adds** to the counts | Assign `c[key] = value` to replace |
| `a - b` on Counters expecting negative counts | Zero and negative results are dropped | `a.subtract(b)` keeps negatives |
| `list.pop(0)` in a loop for a queue | Slow for big lists (O(n) each) | `deque.popleft()` |
| Indexing the middle of a big `deque` | Slow (O(n)) | Use a list for random access |
| Changing a `namedtuple` field: `p.x = 5` | `AttributeError` | `p._replace(x=5)` (creates a new one) |
| Forgetting that `namedtuple(...)` returns a **class** | `Point` is the type, `Point(1, 2)` the object | Keep the same name for both |
| `deque(maxlen=3)` and expecting an error when full | The oldest item is silently dropped | Intended behaviour; check `len` if it matters |
| Using `OrderedDict` for simple ordering | Not needed since Python 3.7 | A normal `dict` |
| Naming your file `collections.py` | Hides the real module | Rename your file |

---

- [Back to section index](./README.md)
