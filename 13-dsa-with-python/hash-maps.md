# Hash Maps - Revision Notes ~ sm

> **In one line:** A **hash map** stores **key → value** pairs and finds any key in **about one step (`O(1)` on average)**, no matter how many pairs it holds, by turning the key into a number (its **hash**) that says where to look. In Python the hash map is the **`dict`**, and the same idea powers the **`set`**.

## Table of Contents
- [The Problem Hash Maps Solve](#the-problem-hash-maps-solve)
- [How a Hash Map Works](#how-a-hash-map-works)
- [Collisions](#collisions)
- [Building a Tiny Hash Map](#building-a-tiny-hash-map)
- [Hash Maps in Python: dict and set](#hash-maps-in-python-dict-and-set)
- [What Can Be a Key? Hashability](#what-can-be-a-key-hashability)
- [Your Own Classes as Keys](#your-own-classes-as-keys)
- [Cost of Operations](#cost-of-operations)
- [Pattern: Counting](#pattern-counting)
- [Pattern: Remember What You Have Seen](#pattern-remember-what-you-have-seen)
- [Pattern: Group by a Key](#pattern-group-by-a-key)
- [Pattern: Prefix Sums with a Dictionary](#pattern-prefix-sums-with-a-dictionary)
- [Pattern: Sets for Fast Membership](#pattern-sets-for-fast-membership)
- [Pattern: Caching (Memoization)](#pattern-caching-memoization)
- [Common Mistakes](#common-mistakes)

---

## The Problem Hash Maps Solve

You have 1,000,000 students and want the marks of "Riya".
- In a **list of pairs** you scan until you find her: up to 1,000,000 steps (`O(n)`).
- In a **sorted list** you can binary search: about 20 steps (`O(log n)`), but inserting keeps costing `O(n)`.
- In a **hash map** you compute where Riya's entry lives and go straight there: about **1 step**, and inserting is also about 1 step.

```python
marks = {"Riya": 90, "Amit": 75, "Zoya": 82}
print(marks["Riya"])
print("Amit" in marks, "Kabir" in marks)
# Output: 90
# Output: True False
```

---

## How a Hash Map Works

Think of a **school locker corridor**. Each student's name is turned into a locker number by a rule. To store or fetch something you apply the same rule and open exactly that locker, without checking the others.

1. A **hash function** turns the key into a whole number (the **hash**): `hash("Riya")` gives some big integer.
2. The number is reduced to a **slot (bucket) index**, for example `hash % number_of_slots`.
3. The pair is stored in that slot. To look it up, repeat steps 1 and 2 and look in that slot.

```text
key "Riya"  --hash-->  big number  --% 8-->  slot 5
                                              [0] [1] [2] [3] [4] [5: ("Riya", 90)] [6] [7]
```
A **good hash function** is fast, always gives the **same hash for the same key**, and **spreads keys evenly** over the slots.

**Resizing.** When the table gets too full (the **load factor**, items divided by slots, becomes high) it is rebuilt with more slots, and every key is placed again. That costs `O(n)`, but it happens rarely, so adding an item is **amortized `O(1)`**, like `list.append`.

---

## Collisions

Different keys can land in the **same slot**. That is called a **collision**: unavoidable, because there are far more possible keys than slots.

Two common ways to handle it:

| Method | Idea |
|---|---|
| **Separate chaining** | Each slot holds a small **list** of the pairs that landed there. Look through that short list. |
| **Open addressing** | If the slot is taken, **probe** other slots by a fixed rule until a free one (or the key) is found. **CPython's `dict` and `set` use open addressing.** |

With a good hash function and resizing, the chains or probe sequences stay very short, so operations remain `O(1)` **on average**. If many keys collide (a bad hash function, or deliberately chosen keys), operations degrade to `O(n)` **in the worst case**.

---

## Building a Tiny Hash Map

Writing one yourself makes the idea concrete. This one uses separate chaining and a simple hash function for strings.

```python
class HashMap:
    def __init__(self, size=8):
        self.size = size
        self.buckets = [[] for _ in range(size)]      # each bucket is a list of [key, value]
        self.count = 0

    def _index(self, key):
        return sum(ord(c) for c in str(key)) % self.size

    def put(self, key, value):
        bucket = self.buckets[self._index(key)]
        for pair in bucket:
            if pair[0] == key:
                pair[1] = value                      # key exists: replace the value
                return
        bucket.append([key, value])
        self.count += 1

    def get(self, key, default=None):
        for k, v in self.buckets[self._index(key)]:
            if k == key:
                return v
        return default

    def remove(self, key):
        bucket = self.buckets[self._index(key)]
        for i, (k, v) in enumerate(bucket):
            if k == key:
                del bucket[i]
                self.count -= 1
                return True
        return False

m = HashMap()
m.put("Riya", 90)
m.put("Amit", 75)
m.put("Riya", 95)                  # update
print(m.get("Riya"), m.get("Amit"), m.get("Zoya", "missing"))
print(m.count)
print(m.remove("Amit"), m.remove("Amit"), m.count)
# Output: 95 75 missing
# Output: 2
# Output: True False 1
```
**A collision, on purpose:** the words `"ab"` and `"ba"` contain the same letters, so this simple hash function gives them the **same** bucket.
```python
m = HashMap()
print(m._index("ab"), m._index("ba"))
m.put("ab", 1)
m.put("ba", 2)
bucket = m.buckets[m._index("ab")]
print(bucket)
print(m.get("ab"), m.get("ba"))
# Output: 3 3
# Output: [['ab', 1], ['ba', 2]]
# Output: 1 2
```
Both pairs live in one bucket, and the lookup compares the **actual keys** to pick the right one. (A real implementation also resizes when `count / size` grows, which this teaching version skips.)

---

## Hash Maps in Python: dict and set

| Type | Holds | Created with |
|---|---|---|
| `dict` | key → value pairs | `{"a": 1}`, `dict(a=1)` |
| `set` | unique keys, **no** values | `{1, 2, 3}`, `set()` |
| `frozenset` | an unchangeable set (itself hashable) | `frozenset([1, 2])` |

```python
d = {"a": 1}
d["b"] = 2                    # insert
d["a"] = 10                   # update
print(d.get("z"), d.get("z", 0))
print(d.pop("b"))
print(list(d.items()))
print(d.setdefault("c", []), d)
# Output: None 0
# Output: 2
# Output: [('a', 10)]
# Output: [] {'a': 10, 'c': []}
```
- A `dict` **remembers insertion order** (guaranteed since Python 3.7). That is a property of Python's dictionary, **not** of hash maps in general.
- `d[key]` raises `KeyError` if missing; `d.get(key, default)` does not.
- `{}` is an **empty dictionary**; an empty set is `set()`.
- Iterating gives keys; use `.items()`, `.values()`, `.keys()`.
- You must **not add or remove keys while looping** over a dict (`RuntimeError: dictionary changed size during iteration`). Loop over `list(d)` instead.

---

## What Can Be a Key? Hashability

A key must be **hashable**: it needs a hash that **never changes** while it is in the map, and equal keys must have equal hashes. In practice that means **immutable** objects.

| Hashable (can be keys / set items) | Not hashable |
|---|---|
| `int`, `float`, `bool`, `str`, `bytes` | `list` |
| `tuple` **if all its items are hashable** | `dict` |
| `frozenset` | `set` |
| `None`, functions, classes | a `tuple` containing a list |

```python
print(hash((1, 2)) == hash((1, 2)))
print(hash(1) == hash(1.0) == hash(True))
# Output: True
# Output: True
```
```python
{[1, 2]: "list as a key"}
# Error: TypeError: unhashable type: 'list'
```
```python
{(1, [2]): "tuple holding a list"}
# Error: TypeError: unhashable type: 'list'
```
Why mutable objects are banned: if you changed a key after storing it, its hash would change and the map could never find it again.

Since `1 == 1.0 == True` and they have the same hash, they are **one key**:
```python
print({1: "a", 1.0: "b", True: "c"})
# Output: {1: 'c'}
```
**Two cautions**
- The hash of a **string differs between runs** of Python (it is randomised on purpose for security), so never store `hash(text)` or depend on its value, and never depend on the order of a `set` of strings.
- Use a **tuple** when you need a multi-part key, such as coordinates:
```python
visited = {(0, 0), (0, 1)}
print((0, 1) in visited, (5, 5) in visited)

distance = {("A", "B"): 5, ("B", "C"): 3}
print(distance[("B", "C")])
# Output: True False
# Output: 3
```

---

## Your Own Classes as Keys

By default, objects are hashed by **identity**, so two objects with the same data are different keys. To compare by value, define `__eq__` **and** `__hash__` using the same fields. (If you define `__eq__` without `__hash__`, Python sets `__hash__` to `None` and the class becomes unhashable.)

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __eq__(self, other):
        return isinstance(other, Point) and (self.x, self.y) == (other.x, other.y)

    def __hash__(self):
        return hash((self.x, self.y))

points = {Point(1, 2), Point(1, 2), Point(3, 4)}
print(len(points))
print(Point(3, 4) in points)
# Output: 2
# Output: True
```
An easier way is a **frozen dataclass**, which writes `__eq__` and `__hash__` for you:
```python
from dataclasses import dataclass

@dataclass(frozen=True)
class Cell:
    row: int
    col: int

print(len({Cell(0, 1), Cell(0, 1), Cell(2, 2)}))
# Output: 2
```
The fields used for `__hash__` must **not change** while the object is in a dict or set.

---

## Cost of Operations

| Operation | Average | Worst |
|---|---|---|
| Insert / update `d[k] = v` | `O(1)` | `O(n)` |
| Lookup `d[k]`, `k in d` | `O(1)` | `O(n)` |
| Delete `del d[k]` | `O(1)` | `O(n)` |
| Loop over everything | `O(n)` | `O(n)` |
| Set union, intersection | `O(len(a) + len(b))` about | |
| `x in list` (for comparison) | `O(n)` | `O(n)` |

The price: **extra memory** (a hash table needs spare slots) and **no useful ordering by key** (you cannot ask for "the smallest key" cheaply). If you need ordered keys, sort, use `heapq`, or a balanced tree (see the Trees note).

---

## Pattern: Counting

How many times does each item appear? One pass, `O(n)`.

```python
def count_items(items):
    counts = {}
    for item in items:
        counts[item] = counts.get(item, 0) + 1
    return counts

print(count_items("banana"))
# Output: {'b': 1, 'a': 3, 'n': 2}
```
`collections.Counter` does this for you (see the Collections note):
```python
from collections import Counter

counts = Counter("banana")
print(counts.most_common(1), counts["z"])
# Output: [('a', 3)] 0
```
Uses: word frequencies, anagram checks, "first unique character", "most frequent element", "majority element".

**Is an item a majority (more than half)?**
```python
from collections import Counter

nums = [3, 2, 3, 3, 1, 3]
value, count = Counter(nums).most_common(1)[0]
print(value, count > len(nums) // 2)
# Output: 3 True
```

---

## Pattern: Remember What You Have Seen

Instead of searching the earlier items again (`O(n)` each), **store them** in a set or dictionary and check in `O(1)`. This turns many `O(n²)` brute-force solutions into `O(n)`.

**Two Sum**: find two numbers that add up to a target
```python
def two_sum(nums, target):
    seen = {}                            # value -> index
    for i, n in enumerate(nums):
        need = target - n
        if need in seen:
            return seen[need], i
        seen[n] = i
    return None

print(two_sum([2, 7, 11, 15], 9))
print(two_sum([3, 2, 4], 6))
print(two_sum([1, 2], 10))
# Output: (0, 1)
# Output: (1, 2)
# Output: None
```
For each number ask "have I already seen the partner I need?". `O(n)` time, `O(n)` space (the brute force is `O(n²)` time, `O(1)` space). Store the number **after** checking, so an item is not paired with itself.

**First repeated item**
```python
def first_repeat(items):
    seen = set()
    for item in items:
        if item in seen:
            return item
        seen.add(item)
    return None

print(first_repeat([4, 2, 7, 2, 4]), first_repeat([1, 2, 3]))
# Output: 2 None
```
**Isomorphic strings** (a consistent one-to-one letter mapping)
```python
def isomorphic(a, b):
    if len(a) != len(b):
        return False
    forward, backward = {}, {}
    for x, y in zip(a, b):
        if forward.setdefault(x, y) != y or backward.setdefault(y, x) != x:
            return False
    return True

print(isomorphic("egg", "add"), isomorphic("foo", "bar"), isomorphic("badc", "baba"))
# Output: True False False
```
**Detect a cycle while walking** (a "visited" set): used for linked lists (see the Linked Lists note) and graphs.

---

## Pattern: Group by a Key

Choose a **key that is the same for items that belong together**, and collect them under it.

```python
from collections import defaultdict

def group_anagrams(words):
    groups = defaultdict(list)
    for word in words:
        groups["".join(sorted(word))].append(word)
    return list(groups.values())

print(group_anagrams(["eat", "tea", "tan", "ate", "nat", "bat"]))
# Output: [['eat', 'tea', 'ate'], ['tan', 'nat'], ['bat']]
```
Other keys: first letter, word length, `n % 10`, the date of an event, a tuple of counts.

```python
from collections import defaultdict

by_length = defaultdict(list)
for word in ["to", "be", "or", "not", "to", "be"]:
    by_length[len(word)].append(word)
print(dict(by_length))
# Output: {2: ['to', 'be', 'or', 'to', 'be'], 3: ['not']}
```

---

## Pattern: Prefix Sums with a Dictionary

**How many contiguous sub-lists add up to `k`?** (numbers may be negative, so a sliding window does not work).

Let `prefix` be the running sum. A sub-list that ends here and sums to `k` exists for every **earlier** prefix equal to `prefix - k`. Keep a dictionary of how many times each prefix sum has occurred.

```python
def subarray_sum(nums, k):
    counts = {0: 1}              # the empty prefix
    prefix = 0
    total = 0
    for n in nums:
        prefix += n
        total += counts.get(prefix - k, 0)
        counts[prefix] = counts.get(prefix, 0) + 1
    return total

print(subarray_sum([1, 1, 1], 2))
print(subarray_sum([1, 2, 3], 3))
print(subarray_sum([1, -1, 0], 0))
# Output: 2
# Output: 2
# Output: 3
```
`O(n)` time and space. For `[1, 2, 3]` and `k = 3` the sub-lists are `[1, 2]` and `[3]`. For `[1, -1, 0]` and `k = 0` they are `[1, -1]`, `[0]` and `[1, -1, 0]`.

---

## Pattern: Sets for Fast Membership

Turning a list into a set makes every `in` test `O(1)`.

```python
allowed = {"red", "green", "blue"}           # build once
requests = ["red", "pink", "blue"]
print([color for color in requests if color in allowed])
# Output: ['red', 'blue']
```
**Set operations**
```python
a = [1, 2, 3, 4]
b = [3, 4, 5]
print(sorted(set(a) & set(b)))      # in both
print(sorted(set(a) | set(b)))      # in either
print(sorted(set(a) - set(b)))      # only in a
print(sorted(set(a) ^ set(b)))      # in exactly one
# Output: [3, 4]
# Output: [1, 2, 3, 4, 5]
# Output: [1, 2]
# Output: [1, 2, 5]
```
**Longest run of consecutive numbers** in `O(n)`, without sorting: start counting only from a number whose predecessor is **not** in the set.
```python
def longest_consecutive(nums):
    values = set(nums)
    best = 0
    for n in values:
        if n - 1 not in values:              # n starts a run
            length = 1
            while n + length in values:
                length += 1
            best = max(best, length)
    return best

print(longest_consecutive([100, 4, 200, 1, 3, 2]))
# Output: 4
```
The run `1, 2, 3, 4` has length 4. Each number is visited a constant number of times overall, so it is `O(n)`.

**Remove duplicates, keeping the order**
```python
print(list(dict.fromkeys([3, 1, 3, 2, 1])))
# Output: [3, 1, 2]
```

---

## Pattern: Caching (Memoization)

Store the result of a calculation under its **input**, so you never calculate the same thing twice.

```python
cache = {}

def fib(n):
    if n in cache:
        return cache[n]
    result = n if n < 2 else fib(n - 1) + fib(n - 2)
    cache[n] = result
    return result

print(fib(50))
# Output: 12586269025
```
The naive recursive version makes billions of calls for `n = 50`; with the dictionary it makes about 100. `functools.cache` does the same for you with one decorator line. An **LRU cache** (least recently used) is a dictionary with a size limit: `collections.OrderedDict` or `functools.lru_cache`.

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Using a `list` or `dict` as a key | `TypeError: unhashable type` | Use a `tuple` or `frozenset` |
| `d[key]` for a key that may be missing | `KeyError` | `d.get(key, default)`, `defaultdict`, or `key in d` |
| Adding or removing keys while looping | `RuntimeError` | Loop over `list(d)` or build a new dict |
| `dict.fromkeys(keys, [])` | Every key shares **one** list | `{k: [] for k in keys}` |
| `{}` for an empty set | It is a dictionary | `set()` |
| Relying on `hash("text")` or set order of strings | Changes between runs | Never depend on them; sort if you need order |
| Mutating an object after using it as a key (custom class) | Lookups fail silently | Use immutable fields or a frozen dataclass |
| Defining `__eq__` without `__hash__` | Objects become unhashable | Define both, from the same fields |
| Using `x in list` in a loop | `O(n²)` | Make a `set` first |
| Expecting O(1) always | Worst case `O(n)` with many collisions | A good hash and normal keys make this rare |
| Forgetting that `1`, `1.0` and `True` are the same key | Entries overwrite each other | Keep key types consistent |
| Treating a dict as sorted | It keeps **insertion** order, not key order | `sorted(d)` or `sorted(d.items())` |
| Using a hash map when order or range queries matter | No cheap "next larger key" | Sorted list with `bisect`, or a tree |
| Checking `if d[key]:` to test presence | `KeyError`, or wrong when the value is `0` | `if key in d:` |

---

- [Back to section index](./README.md)
