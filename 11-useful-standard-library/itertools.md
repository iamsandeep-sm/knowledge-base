# itertools - Revision Notes ~ sm

> **In one line:** **`itertools`** is a toolbox of fast, **memory-friendly iterator builders**: endless counters, chaining and slicing iterables, running totals, grouping, and every **combination, permutation or product** of items. Most functions return **iterators** (lazy, one-time-use); wrap them in `list()` to see the values.

## Table of Contents
- [Overview](#overview)
- [Endless Iterators: count, cycle, repeat](#endless-iterators-count-cycle-repeat)
- [Joining and Slicing: chain, islice](#joining-and-slicing-chain-islice)
- [Pairs and Batches: pairwise, batched](#pairs-and-batches-pairwise-batched)
- [Running Totals: accumulate](#running-totals-accumulate)
- [Filtering: takewhile, dropwhile, filterfalse, compress](#filtering-takewhile-dropwhile-filterfalse-compress)
- [Combining Iterables: zip_longest, starmap](#combining-iterables-zip_longest-starmap)
- [Grouping: groupby](#grouping-groupby)
- [Combinatorics: product, permutations, combinations](#combinatorics-product-permutations-combinations)
- [Copying an Iterator: tee](#copying-an-iterator-tee)
- [Practical Examples](#practical-examples)
- [Common Mistakes](#common-mistakes)

---

## Overview

```python
import itertools
```
| Group | Functions |
|---|---|
| Endless | `count`, `cycle`, `repeat` |
| Joining and slicing | `chain`, `chain.from_iterable`, `islice` |
| Pairs and batches | `pairwise` (3.10+), `batched` (3.12+) |
| Accumulating | `accumulate` |
| Filtering | `takewhile`, `dropwhile`, `filterfalse`, `compress` |
| Combining | `zip_longest`, `starmap` |
| Grouping | `groupby` |
| Combinatorics | `product`, `permutations`, `combinations`, `combinations_with_replacement` |
| Copying | `tee` |

They work lazily: values are made **only when asked for**, so they use almost no memory even for huge or endless sequences (see the Iterators and Generators notes). An iterator can be used **only once**.

---

## Endless Iterators: count, cycle, repeat

**`count(start, step)`** counts forever. **`cycle(iterable)`** repeats the items forever. **`repeat(x, times)`** repeats one value. Always **limit** them, for example with `islice` (below), `zip`, or a `break`.

```python
from itertools import count, cycle, repeat, islice

print(list(islice(count(10), 5)))
print(list(islice(count(0, 5), 4)))
print(list(islice(cycle("ABC"), 7)))
print(list(repeat("x", 3)))
# Output: [10, 11, 12, 13, 14]
# Output: [0, 5, 10, 15]
# Output: ['A', 'B', 'C', 'A', 'B', 'C', 'A']
# Output: ['x', 'x', 'x']
```
**Numbering items with `count`** (like `enumerate`) and **alternating a pattern**:
```python
from itertools import count, cycle

names = ["Riya", "Amit", "Zoya"]
for number, name in zip(count(1), names):
    print(number, name)

rows = ["white", "grey"]
for row, shade in zip(range(5), cycle(rows)):
    print(row, shade)
# Output: 1 Riya
# Output: 2 Amit
# Output: 3 Zoya
# Output: 0 white
# Output: 1 grey
# Output: 2 white
# Output: 3 grey
# Output: 4 white
```
`list(count())` would never finish and fill the memory.

---

## Joining and Slicing: chain, islice

**`chain(a, b, ...)`** goes through several iterables one after another **without building a new list**.

```python
from itertools import chain

print(list(chain([1, 2], (3, 4), "ab")))
# Output: [1, 2, 3, 4, 'a', 'b']
```
**`chain.from_iterable(iterable_of_iterables)`** flattens **one level**:
```python
from itertools import chain

matrix = [[1, 2], [3, 4], [5]]
print(list(chain.from_iterable(matrix)))
# Output: [1, 2, 3, 4, 5]
```
(`[n for row in matrix for n in row]` does the same; see the Comprehensions note.)

**`islice(iterable, stop)`** or **`islice(iterable, start, stop, step)`** slices **any iterable**, even a generator or a file, which ordinary `[:]` slicing cannot.

```python
from itertools import islice

print(list(islice("abcdefgh", 3)))
print(list(islice("abcdefgh", 2, 6)))
print(list(islice("abcdefgh", 0, None, 3)))
# Output: ['a', 'b', 'c']
# Output: ['c', 'd', 'e', 'f']
# Output: ['a', 'd', 'g']
```
`islice` does **not** accept negative numbers, and it **consumes** the items it skips:
```python
from itertools import islice

it = iter(range(10))
print(list(islice(it, 3)))
print(list(islice(it, 3)))
# Output: [0, 1, 2]
# Output: [3, 4, 5]
```
**Reading the first lines of a file**:
```python
from itertools import islice

with open("lines.txt", "w", encoding="utf-8") as f:
    f.write("one\ntwo\nthree\nfour\n")

with open("lines.txt", encoding="utf-8") as f:
    for line in islice(f, 2):
        print(line.rstrip())
# Output: one
# Output: two
```

---

## Pairs and Batches: pairwise, batched

**`pairwise(iterable)`** (Python 3.10+) gives each item with the next one: the overlapping pairs.

```python
from itertools import pairwise

print(list(pairwise([1, 4, 9, 16])))

prices = [100, 102, 99, 105]
print([b - a for a, b in pairwise(prices)])
# Output: [(1, 4), (4, 9), (9, 16)]
# Output: [2, -3, 6]
```
**`batched(iterable, n)`** (Python 3.12+) splits into **tuples of up to `n` items**.

```python
from itertools import batched

print(list(batched("ABCDEFG", 3)))
print([sum(b) for b in batched(range(1, 8), 2)])
# Output: [('A', 'B', 'C'), ('D', 'E', 'F'), ('G',)]
# Output: [3, 7, 11, 7]
```
The last batch may be shorter. On older Python, use a small generator:
```python
from itertools import islice

def chunks(iterable, size):
    it = iter(iterable)
    while batch := list(islice(it, size)):
        yield batch

print(list(chunks("ABCDEFG", 3)))
# Output: [['A', 'B', 'C'], ['D', 'E', 'F'], ['G']]
```

---

## Running Totals: accumulate

`accumulate(iterable)` yields the **running total**. Give it another two-argument function for other running calculations.

```python
from itertools import accumulate
import operator

nums = [1, 2, 3, 4, 5]
print(list(accumulate(nums)))
print(list(accumulate(nums, operator.mul)))
print(list(accumulate([3, 1, 4, 1, 5], max)))
print(list(accumulate(nums, initial=100)))
# Output: [1, 3, 6, 10, 15]
# Output: [1, 2, 6, 24, 120]
# Output: [3, 3, 4, 4, 5]
# Output: [100, 101, 103, 106, 110, 115]
```
| Call | Meaning |
|---|---|
| `accumulate(x)` | Running sum |
| `accumulate(x, operator.mul)` | Running product (factorials) |
| `accumulate(x, max)` | Highest value so far |
| `accumulate(x, initial=n)` | Start from `n` (3.8+); the output has one more item |

**Bank balance after each transaction**
```python
from itertools import accumulate

changes = [500, -120, -80, 300]
print(list(accumulate(changes, initial=1000)))
# Output: [1000, 1500, 1380, 1300, 1600]
```

---

## Filtering: takewhile, dropwhile, filterfalse, compress

```python
from itertools import takewhile, dropwhile, filterfalse, compress

nums = [1, 4, 6, 3, 8, 2]

print(list(takewhile(lambda n: n < 6, nums)))
print(list(dropwhile(lambda n: n < 6, nums)))
print(list(filterfalse(lambda n: n % 2 == 0, nums)))
print(list(compress("ABCDEF", [1, 0, 1, 0, 1, 1])))
# Output: [1, 4]
# Output: [6, 3, 8, 2]
# Output: [1, 3]
# Output: ['A', 'C', 'E', 'F']
```
| Function | Does |
|---|---|
| `takewhile(test, items)` | Keep items **while** the test is true, **stop at the first failure** |
| `dropwhile(test, items)` | **Skip** items while the test is true, then keep **everything** |
| `filterfalse(test, items)` | Keep the items for which the test is **false** (the opposite of `filter`) |
| `compress(items, selectors)` | Keep items whose selector is true |

`takewhile` and `dropwhile` look at the order, unlike `filter`: for `[1, 4, 6, 3]` with `n < 6`, `takewhile` gives `[1, 4]` but `filter` would give `[1, 4, 3]`.

---

## Combining Iterables: zip_longest, starmap

**`zip_longest`** is like `zip` but goes to the **longest** input and fills the gaps.

```python
from itertools import zip_longest

print(list(zip("ab", [1, 2, 3])))
print(list(zip_longest("ab", [1, 2, 3], fillvalue="-")))
# Output: [('a', 1), ('b', 2)]
# Output: [('a', 1), ('b', 2), ('-', 3)]
```
**`starmap(func, tuples)`** calls `func(*item)` for each tuple (it "unpacks" each item as arguments).

```python
from itertools import starmap

print(list(starmap(pow, [(2, 3), (3, 2), (10, 3)])))
print(list(starmap(lambda a, b: a * b, [(1, 2), (3, 4)])))
# Output: [8, 9, 1000]
# Output: [2, 12]
```
`map(pow, a, b)` takes the arguments from two **separate** lists; `starmap` takes them from **one list of pairs**.

---

## Grouping: groupby

`groupby(iterable, key)` groups **consecutive** items that have the same key. It yields `(key, group)` pairs, where `group` is an iterator.

```python
from itertools import groupby

data = "AAABBCAA"
for key, group in groupby(data):
    print(key, list(group))
# Output: A ['A', 'A', 'A']
# Output: B ['B', 'B']
# Output: C ['C']
# Output: A ['A', 'A']
```
Notice `A` appears twice: **only neighbours are grouped**. To group *everything* with the same key, **sort first by the same key**.

```python
from itertools import groupby

words = ["apple", "bat", "ant", "ball", "cat", "axe"]
words.sort(key=lambda w: w[0])
for letter, group in groupby(words, key=lambda w: w[0]):
    print(letter, list(group))
# Output: a ['apple', 'ant', 'axe']
# Output: b ['bat', 'ball']
# Output: c ['cat']
```
**Counting runs** (run-length encoding):
```python
from itertools import groupby

def compress_text(s):
    return "".join(f"{ch}{len(list(g))}" for ch, g in groupby(s))

print(compress_text("aaabccdddd"))
# Output: a3b1c2d4
```
**Group records by a field**:
```python
from itertools import groupby
from operator import itemgetter

sales = [("north", 5), ("south", 2), ("north", 3), ("south", 7)]
sales.sort(key=itemgetter(0))
for region, rows in groupby(sales, key=itemgetter(0)):
    print(region, sum(amount for _, amount in rows))
# Output: north 8
# Output: south 9
```
**The group is lazy and shared.** Use each group **before** moving to the next one. If you need it later, copy it with `list(group)` immediately.

For a simpler "group everything" job, `defaultdict(list)` (see the collections note) needs no sorting.

---

## Combinatorics: product, permutations, combinations

These generate **all the arrangements** of items.

| Function | Order matters? | Repeats allowed? | Count for `n` items, pick `k` |
|---|---|---|---|
| `product(a, b)` | n/a (pairs from different lists) | yes | `len(a) * len(b)` |
| `permutations(items, k)` | **yes** | no | n! / (n-k)! |
| `combinations(items, k)` | **no** | no | n! / (k! (n-k)!) |
| `combinations_with_replacement(items, k)` | no | **yes** | |

```python
from itertools import product, permutations, combinations, combinations_with_replacement

print(list(product("AB", [1, 2])))
print(list(product("01", repeat=2)))
print(list(permutations("ABC", 2)))
print(list(combinations("ABC", 2)))
print(list(combinations_with_replacement("AB", 2)))
# Output: [('A', 1), ('A', 2), ('B', 1), ('B', 2)]
# Output: [('0', '0'), ('0', '1'), ('1', '0'), ('1', '1')]
# Output: [('A', 'B'), ('A', 'C'), ('B', 'A'), ('B', 'C'), ('C', 'A'), ('C', 'B')]
# Output: [('A', 'B'), ('A', 'C'), ('B', 'C')]
# Output: [('A', 'A'), ('A', 'B'), ('B', 'B')]
```
- `product(a, b)` replaces **nested loops**; `product(x, repeat=n)` is `product(x, x, ..., x)`.
- `permutations(items)` with no `k` arranges **all** the items.
- Results come out in a predictable order based on the **input order**.

```python
from itertools import permutations, combinations
import math

print(len(list(permutations(range(4)))))
print(len(list(combinations(range(5), 2))))
print(math.factorial(4), math.comb(5, 2))
# Output: 24
# Output: 10
# Output: 24 10
```
The numbers grow **very fast** (10 items give 3,628,800 permutations). Use `math.perm` and `math.comb` to **count** without generating, and loop lazily instead of using `list()` on big inputs.

**All the ways to choose 2 players from 4**
```python
from itertools import combinations

players = ["Riya", "Amit", "Zoya", "Kabir"]
for pair in combinations(players, 2):
    print(" vs ".join(pair))
# Output: Riya vs Amit
# Output: Riya vs Zoya
# Output: Riya vs Kabir
# Output: Amit vs Zoya
# Output: Amit vs Kabir
# Output: Zoya vs Kabir
```

**All 3-digit PIN codes with digits 1 to 3**
```python
from itertools import product

pins = ["".join(p) for p in product("123", repeat=3)]
print(len(pins), pins[:4])
# Output: 27 ['111', '112', '113', '121']
```

**Pairs that add up to a target**
```python
from itertools import combinations

nums = [1, 3, 5, 7, 9]
print([pair for pair in combinations(nums, 2) if sum(pair) == 10])
# Output: [(1, 9), (3, 7)]
```

---

## Copying an Iterator: tee

An iterator can be read only once. `tee(it, n)` gives **`n` independent iterators** from one.

```python
from itertools import tee

numbers = iter([1, 2, 3])
a, b = tee(numbers)
print(list(a))
print(list(b))
# Output: [1, 2, 3]
# Output: [1, 2, 3]
```
After `tee`, **do not use the original iterator** any more. `tee` stores the items that one copy has read and the other has not, so for a one-time pass it is simpler to turn the data into a **list**.

---

## Practical Examples

**Flatten a list of lists**
```python
from itertools import chain

orders = [["pen", "ink"], ["book"], [], ["bag", "cap"]]
print(list(chain.from_iterable(orders)))
# Output: ['pen', 'ink', 'book', 'bag', 'cap']
```

**First n items of an endless generator**
```python
from itertools import islice

def squares():
    n = 1
    while True:
        yield n * n
        n += 1

print(list(islice(squares(), 6)))
# Output: [1, 4, 9, 16, 25, 36]
```

**Rolling pairs to find the biggest jump**
```python
from itertools import pairwise

temps = [21, 24, 23, 29, 27]
jumps = [abs(b - a) for a, b in pairwise(temps)]
print(jumps, max(jumps))
# Output: [3, 1, 6, 2] 6
```

**Round-robin between players**
```python
from itertools import cycle, islice

players = cycle(["Riya", "Amit", "Zoya"])
print(list(islice(players, 7)))
# Output: ['Riya', 'Amit', 'Zoya', 'Riya', 'Amit', 'Zoya', 'Riya']
```

**Cumulative percentage**
```python
from itertools import accumulate

sales = [10, 20, 30, 40]
total = sum(sales)
print([round(100 * s / total) for s in accumulate(sales)])
# Output: [10, 30, 60, 100]
```

**A multiplication table with `product`**
```python
from itertools import product

for a, b in product(range(1, 3), range(1, 4)):
    print(f"{a} x {b} = {a * b}")
# Output: 1 x 1 = 1
# Output: 1 x 2 = 2
# Output: 1 x 3 = 3
# Output: 2 x 1 = 2
# Output: 2 x 2 = 4
# Output: 2 x 3 = 6
```

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| `list(count())` or `list(cycle(x))` | Never finishes; memory fills up | Limit with `islice`, `zip` or `break` |
| Reading an iterator twice | The second time it is empty | Make a `list`, or use `tee`, or create it again |
| `groupby` without sorting | Same key appears in several groups | `sort(key=...)` with the **same key** first |
| Keeping a `groupby` group for later | It is empty once you move on | `list(group)` immediately |
| Negative index in `islice` | `ValueError` | Convert to a list, or use `collections.deque(maxlen=n)` |
| `permutations` when order does not matter | Too many results (duplicates by order) | `combinations` |
| `list(permutations(range(12)))` | Huge memory use (479,001,600 items) | Loop lazily, or count with `math.perm` |
| Using `pairwise` or `batched` on old Python | `ImportError` | Python 3.10+ / 3.12+, or write a small generator |
| Expecting `takewhile` to behave like `filter` | It **stops** at the first failure | Use `filter` for "all matching items" |
| Printing an `itertools` object | `<itertools.chain object at 0x...>` | Wrap with `list(...)` |
| Using the original iterator after `tee` | Items are lost for the copies | Use only the copies |
| Naming your file `itertools.py` | Hides the real module | Rename your file |

---

- [Back to section index](./README.md)
