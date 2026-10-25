# Python Lists - Revision Notes ~ sm

> **One-line definition:** A list is an **ordered**, **mutable** (changeable) collection written inside square brackets `[]`. It can hold items of **any type**, and it **allows duplicates**.

## Table of Contents
- [What is a List?](#what-is-a-list)
- [Creating Lists](#creating-lists)
- [List Indexes](#list-indexes)
- [Access List Items](#access-list-items)
- [Change List Items](#change-list-items)
- [Add List Items](#add-list-items)
- [Remove List Items](#remove-list-items)
- [Loop Lists](#loop-lists)
- [List Comprehension](#list-comprehension)
- [Sort Lists](#sort-lists)
- [Copy Lists](#copy-lists)
- [Join Lists](#join-lists)
- [List Methods](#list-methods)
- [Nested Lists](#nested-lists)
- [Built-in Functions with Lists](#built-in-functions-with-lists)
- [Commonly Used Patterns](#commonly-used-patterns)
- [List vs Tuple vs Set vs Dictionary](#list-vs-tuple-vs-set-vs-dictionary)

---

## What is a List?

```python
lst1 = [1, 2, 2, 3, 5, 4, 6]
lst2 = ["Red", "Green", "Blue"]
details = ["Abhijeet", 18, "FYBScIT", 9.8]    # mixed data types

print(lst1)       # [1, 2, 2, 3, 5, 4, 6]
print(lst2)       # ['Red', 'Green', 'Blue']
print(details)    # ['Abhijeet', 18, 'FYBScIT', 9.8]
```

| Property | Meaning |
|---|---|
| **Ordered** | Items keep the order in which you put them; each has a fixed position (index) |
| **Mutable** | You can change, add and remove items after creation |
| **Allows duplicates** | The same value can appear many times |
| **Heterogeneous** | Can mix data types (`int`, `str`, `float`, other lists, ...) |
| **Indexed** | Access items by position, starting at `0` |
| **Dynamic size** | Grows and shrinks automatically |

---

## Creating Lists

### Square brackets (most common)
```python
fruits = ["apple", "banana", "cherry"]
```

### Empty list
```python
a = []
b = list()
print(a, b)    # [] []
```

### The `list()` constructor - converts any iterable
```python
print(list("Python"))         # ['P', 'y', 't', 'h', 'o', 'n']
print(list((1, 2, 3)))        # [1, 2, 3]         from a tuple
print(list(range(5)))         # [0, 1, 2, 3, 4]   from range
print(list({"a": 1, "b": 2})) # ['a', 'b']        from a dict (gives the KEYS)
print(list({3, 1, 2}))        # order of a set is not guaranteed
```
> `list("Python")` splits the string into characters. `list(5)` gives `TypeError` because an int is not iterable.

### Repeat items with `*`
```python
zeros = [0] * 5
print(zeros)    # [0, 0, 0, 0, 0]
```

### Length
```python
print(len(["a", "b", "c"]))    # 3
```

---

## List Indexes

Each item has a unique **index**. Positive indexes count from the **start** (0, 1, 2, ...). Negative indexes count from the **end** (-1, -2, -3, ...).

```python
colors = ["Red", "Green", "Blue", "Yellow", "Green"]
#          [0]      [1]     [2]      [3]      [4]      <- positive
#          [-5]     [-4]    [-3]     [-2]     [-1]     <- negative
```

| Index | `0` | `1` | `2` | `3` | `4` |
|---|---|---|---|---|---|
| **Value** | Red | Green | Blue | Yellow | Green |
| **Negative** | `-5` | `-4` | `-3` | `-2` | `-1` |

> Accessing an index that does not exist raises `IndexError: list index out of range`.
> For a list of length `n`, valid indexes are `0` to `n-1` and `-n` to `-1`.

---

## Access List Items

### Positive indexing
```python
colors = ["Red", "Green", "Blue", "Yellow", "Green"]
print(colors[2])    # Blue
print(colors[4])    # Green
print(colors[0])    # Red
```

### Negative indexing
```python
print(colors[-1])   # Green   (last item)
print(colors[-3])   # Blue
print(colors[-5])   # Red     (first item)
```

### Check if an item exists (`in` / `not in`)
```python
if "Yellow" in colors:
    print("Yellow is present.")    # Yellow is present.
else:
    print("Yellow is absent.")

if "Orange" in colors:
    print("Orange is present.")
else:
    print("Orange is absent.")     # Orange is absent.

print("Orange" not in colors)      # True
```

### Slicing - range of index

This topic has its own note: [Slicing](./slicing.md).

### Find where an item is: `index()` and `count()`
```python
colors = ["Red", "Green", "Blue", "Yellow", "Green"]
print(colors.index("Green"))      # 1     first position only
print(colors.index("Green", 2))   # 4     search starting from index 2
print(colors.count("Green"))      # 2     how many times it appears
# colors.index("Pink")            # ValueError: 'Pink' is not in list
```

---

## Change List Items

Lists are mutable, so you can replace items using their index or an index range.

### Change a single item
```python
names = ["Harry", "Sarah", "Nadia", "Oleg", "Steve"]
names[2] = "Millie"
print(names)    # ['Harry', 'Sarah', 'Millie', 'Oleg', 'Steve']
```

### Change a range of items (slice assignment)
```python
names = ["Harry", "Sarah", "Nadia", "Oleg", "Steve"]
names[2:4] = ["juan", "Anastasia"]
print(names)    # ['Harry', 'Sarah', 'juan', 'Anastasia', 'Steve']
```

### Fewer new items than the range
All items in the range are replaced, and the list becomes **shorter**.
```python
names = ["Harry", "Sarah", "Nadia", "Oleg", "Steve"]
names[1:4] = ["juan", "Anastasia"]
print(names)    # ['Harry', 'juan', 'Anastasia', 'Steve']
```

### More new items than the range
The new items are inserted and the remaining items shift to the right, so the list becomes **longer**.
```python
names = ["Harry", "Sarah", "Nadia", "Oleg", "Steve"]
names[2:3] = ["juan", "Anastasia", "Bruno", "Olga", "Rosa"]
print(names)
# ['Harry', 'Sarah', 'juan', 'Anastasia', 'Bruno', 'Olga', 'Rosa', 'Oleg', 'Steve']
```

### Useful slice tricks
```python
nums = [1, 2, 3, 4, 5]

nums[1:3] = []              # delete a range by assigning an empty list
print(nums)                 # [1, 4, 5]

nums = [1, 2, 3]
nums[1:1] = ["a", "b"]      # insert without removing anything
print(nums)                 # [1, 'a', 'b', 2, 3]

nums = [1, 2, 3, 4, 5, 6]
nums[::2] = [0, 0, 0]       # extended slice: sizes MUST match
print(nums)                 # [0, 2, 0, 4, 0, 6]
```
> Extended slice assignment (with a step other than 1) needs the **same number of items**, otherwise `ValueError`.

### Change items using a loop
```python
prices = [10, 20, 30]
for i in range(len(prices)):
    prices[i] = prices[i] * 2
print(prices)    # [20, 40, 60]
```
> In `for p in prices: p = p * 2`, only the loop variable changes. The list does **not** (for numbers and strings, which are immutable). Assign through the index, as above, or build a new list with a comprehension.

---

## Add List Items

Three methods add items to a list: `append()`, `insert()` and `extend()`.

### `append()` - add one item at the end
```python
colors = ["violet", "indigo", "blue"]
colors.append("green")
print(colors)    # ['violet', 'indigo', 'blue', 'green']
```

### `insert(index, item)` - add at a specific position
```python
colors = ["violet", "indigo", "blue"]
#           [0]        [1]      [2]
colors.insert(1, "green")      # inserts at index 1
print(colors)    # ['violet', 'green', 'indigo', 'blue']
#                    [0]        [1]       [2]      [3]
```
> If the index is larger than the length, the item is added at the **end**. `insert(0, x)` adds at the **front**.

### `extend(iterable)` - add many items
Adds **each item** of another list, tuple, set, string or dictionary to the list.

```python
# add a list
colors = ["violet", "indigo", "blue"]
rainbow = ["green", "yellow", "orange", "red"]
colors.extend(rainbow)
print(colors)    # ['violet', 'indigo', 'blue', 'green', 'yellow', 'orange', 'red']
```
```python
# add a tuple
cars = ["Hyundai", "Tata", "Mahindra"]
cars.extend(("Mercedes", "Volkswagen", "BMW"))
print(cars)      # ['Hyundai', 'Tata', 'Mahindra', 'Mercedes', 'Volkswagen', 'BMW']
```
```python
# add a set - the order of the added items is NOT guaranteed (sets are unordered)
cars = ["Hyundai", "Tata", "Mahindra"]
cars.extend({"Mercedes", "Volkswagen", "BMW"})
print(len(cars))    # 6   (the 3 new items can appear in any order)
```
```python
# add a dictionary - only the KEYS are added, not the values
students = ["Sakshi", "Aaditya", "Ritika"]
students.extend({"Yash": 18, "Devika": 19, "Soham": 19})
print(students)    # ['Sakshi', 'Aaditya', 'Ritika', 'Yash', 'Devika', 'Soham']
```

### `append()` vs `extend()` (very common question)
```python
a = [1, 2]
a.append([3, 4])
print(a)    # [1, 2, [3, 4]]     the whole list becomes ONE item

b = [1, 2]
b.extend([3, 4])
print(b)    # [1, 2, 3, 4]       each item is added separately
```
> `extend("abc")` adds `'a'`, `'b'`, `'c'` separately because a string is an iterable of characters. Use `append("abc")` to add the whole string.

### Concatenate with `+`
```python
colors = ["violet", "indigo", "blue", "green"]
colors2 = ["yellow", "orange", "red"]
print(colors + colors2)    # ['violet', 'indigo', 'blue', 'green', 'yellow', 'orange', 'red']
```

### `+` vs `+=` vs `extend()`
```python
a = [1, 2]
b = a + [3]       # creates a NEW list; a is unchanged
print(a, b)       # [1, 2] [1, 2, 3]

a += [3]          # in place, works like extend()
print(a)          # [1, 2, 3]
```

> Important: `append()`, `insert()`, `extend()`, `sort()`, `reverse()` change the list **in place** and return `None`.
> So `x = my_list.append(5)` makes `x` equal to `None`. A common bug.

---

## Remove List Items

| Method / keyword | What it does | If it fails |
|---|---|---|
| `pop()` | Removes and **returns the last** item | `IndexError` on an empty list |
| `pop(i)` | Removes and **returns** the item at index `i` | `IndexError` if `i` is out of range |
| `remove(x)` | Removes the **first** item equal to `x` | `ValueError` if `x` is not found |
| `del lst[i]` | Removes the item at index `i` | `IndexError` |
| `del lst[a:b]` | Removes a whole slice | no error for out-of-range slices |
| `clear()` | Removes **all** items, list becomes `[]` | - |
| `del lst` | Deletes the **whole list variable** | - |

### `pop()`
```python
colors = ["Red", "Green", "Blue", "Yellow", "Green"]
last = colors.pop()          # removes the last item
print(colors)    # ['Red', 'Green', 'Blue', 'Yellow']
print(last)      # Green

colors = ["Red", "Green", "Blue", "Yellow", "Green"]
colors.pop(1)                # removes the item at index 1
print(colors)    # ['Red', 'Blue', 'Yellow', 'Green']
```

### `remove()` - remove by value
```python
colors = ["violet", "indigo", "blue", "green", "yellow"]
colors.remove("blue")
print(colors)    # ['violet', 'indigo', 'green', 'yellow']
```
> `remove()` deletes only the **first match**. To remove every occurrence, use a list comprehension: `[c for c in lst if c != "x"]`.

```python
nums = [1, 2, 2, 3, 2]
nums.remove(2)
print(nums)    # [1, 2, 3, 2]       only the first 2 is gone
```

### `del` - remove by index or slice
```python
colors = ["violet", "indigo", "blue", "green", "yellow"]
del colors[3]
print(colors)    # ['violet', 'indigo', 'blue', 'yellow']

nums = [0, 1, 2, 3, 4, 5]
del nums[1:4]
print(nums)      # [0, 4, 5]
```

### `del` without an index deletes the whole list
```python
colors = ["violet", "indigo", "blue", "green", "yellow"]
del colors
# print(colors)    # NameError: name 'colors' is not defined
```
> The entire list is gone, so the variable `colors` no longer exists.

### `clear()` - empty the list but keep it
```python
colors = ["violet", "indigo", "blue", "green", "yellow"]
colors.clear()
print(colors)    # []
```

### Do NOT remove items while looping over the same list
```python
nums = [1, 2, 2, 3, 4, 4]
for n in nums:
    if n % 2 == 0:
        nums.remove(n)
print(nums)    # [1, 2, 3, 4]   WRONG: evens are still there, because the loop skipped items
```
When an item is removed, the items after it shift left, so the loop jumps over the next one.

Safer ways:
```python
nums = [1, 2, 2, 3, 4, 4]
nums = [n for n in nums if n % 2 != 0]     # build a new list (best)
print(nums)    # [1, 3]

nums = [1, 2, 2, 3, 4, 4]
for n in nums[:]:                          # loop over a COPY, change the original
    if n % 2 == 0:
        nums.remove(n)
print(nums)    # [1, 3]
```

---

## Loop Lists

```python
fruits = ["apple", "banana", "cherry"]
```

### Loop over items (most common)
```python
for fruit in fruits:
    print(fruit)
```

### Loop with index using `range(len())`
```python
for i in range(len(fruits)):
    print(i, fruits[i])
```

### `enumerate()` - index and value together (best practice)
```python
for i, fruit in enumerate(fruits):
    print(i, fruit)
# 0 apple
# 1 banana
# 2 cherry

for i, fruit in enumerate(fruits, start=1):    # start counting from 1
    print(i, fruit)
```

### `while` loop
```python
i = 0
while i < len(fruits):
    print(fruits[i])
    i += 1
```

### Loop over two lists at once with `zip()`
```python
names = ["Karan", "Riya"]
marks = [80, 90]
for n, m in zip(names, marks):
    print(n, m)
# Karan 80
# Riya 90
```
> `zip()` stops at the **shortest** list.

### Loop in reverse
```python
for fruit in reversed(fruits):
    print(fruit)
# cherry, banana, apple
```

### Loop using a list comprehension (one-liner)
```python
[print(f) for f in fruits]    # works, but builds a useless list of None; a normal for loop is cleaner for printing
doubled = [n * 2 for n in [1, 2, 3]]    # comprehensions are meant for BUILDING a new list
```

---

## List Comprehension

This topic has its own note: [List Comprehensions](../05-comprehensions/list-comprehensions.md).

---

## Sort Lists

### `sort()` vs `sorted()`
| | `lst.sort()` | `sorted(lst)` |
|---|---|---|
| Changes original list | Yes (in place) | No |
| Returns | `None` | a **new** sorted list |
| Works on | lists only | any iterable (tuple, set, string, dict keys) |

```python
nums = [5, 2, 9, 1]

print(sorted(nums))    # [1, 2, 5, 9]    new list
print(nums)            # [5, 2, 9, 1]    original unchanged

nums.sort()
print(nums)            # [1, 2, 5, 9]    original changed
```

### Descending order
```python
nums = [5, 2, 9, 1]
nums.sort(reverse=True)
print(nums)    # [9, 5, 2, 1]
```

### Sort strings
```python
words = ["banana", "Apple", "cherry"]
print(sorted(words))                       # ['Apple', 'banana', 'cherry']   uppercase comes first
print(sorted(words, key=str.lower))        # ['Apple', 'banana', 'cherry']   case-insensitive
```
```python
words = ["banana", "apple", "Cherry"]
print(sorted(words))                       # ['Cherry', 'apple', 'banana']   capital letters sort before lowercase
print(sorted(words, key=str.lower))        # ['apple', 'banana', 'Cherry']
```

### Custom sorting with `key`
`key` is a function applied to every item; the list is sorted by the **results**.
```python
words = ["pear", "fig", "banana", "kiwi"]
print(sorted(words, key=len))                  # ['fig', 'pear', 'kiwi', 'banana']
print(sorted(words, key=len, reverse=True))    # ['banana', 'pear', 'kiwi', 'fig']

students = [("Riya", 90), ("Amit", 75), ("Sam", 82)]
students.sort(key=lambda s: s[1])              # sort by marks
print(students)    # [('Amit', 75), ('Sam', 82), ('Riya', 90)]
```

### Reverse the order (not a sort)
```python
nums = [1, 2, 3]
nums.reverse()                 # in place
print(nums)                    # [3, 2, 1]

print(list(reversed([1, 2, 3])))   # [3, 2, 1]   new list
print([1, 2, 3][::-1])             # [3, 2, 1]   new list (slice)
```

### Important facts
- Sorting is **stable**: items that compare equal keep their original relative order.
- Mixed types cannot be compared: `[3, "a", 1].sort()` raises `TypeError`.
- `sort()` returns `None`, so `nums = nums.sort()` wipes out your list (it becomes `None`).
- Default sort order for strings is by character code, so `"Z"` comes before `"a"`.

---

## Copy Lists

### Why `b = a` is NOT a copy
```python
a = [1, 2, 3]
b = a              # both names point to the SAME list
b.append(4)
print(a)           # [1, 2, 3, 4]    a changed too
print(a is b)      # True
```

### Real copies
```python
a = [1, 2, 3]
b = a.copy()       # method
c = list(a)        # constructor
d = a[:]           # slice
b.append(4)
print(a)           # [1, 2, 3]       unchanged
print(b)           # [1, 2, 3, 4]
```

### Shallow copy vs deep copy
`copy()`, `list()` and `[:]` make a **shallow** copy: the outer list is new, but nested objects (inner lists) are still **shared**.

```python
original = [[1, 2], [3, 4]]
shallow = original.copy()
shallow[0].append(99)
print(original)    # [[1, 2, 99], [3, 4]]    original changed too!
```

Use `copy.deepcopy()` for a fully independent copy:
```python
import copy
original = [[1, 2], [3, 4]]
deep = copy.deepcopy(original)
deep[0].append(99)
print(original)    # [[1, 2], [3, 4]]        safe
print(deep)        # [[1, 2, 99], [3, 4]]
```

| Method | Copy type | Nested lists |
|---|---|---|
| `b = a` | No copy (same object) | Shared |
| `a.copy()`, `list(a)`, `a[:]` | Shallow | Shared |
| `copy.deepcopy(a)` | Deep | Independent |

---

## Join Lists

### `+` operator (creates a new list)
```python
a = [1, 2]
b = [3, 4]
print(a + b)    # [1, 2, 3, 4]
```

### `extend()` (changes the first list)
```python
a = [1, 2]
a.extend([3, 4])
print(a)    # [1, 2, 3, 4]
```

### `+=` (in place) and `*` (repeat)
```python
a = [1, 2]
a += [3]
print(a)         # [1, 2, 3]
print(a * 2)     # [1, 2, 3, 1, 2, 3]
```

### Unpacking with `*` (new list, works with many lists)
```python
a, b, c = [1, 2], [3], [4, 5]
print([*a, *b, *c])    # [1, 2, 3, 4, 5]
```

### `itertools.chain` (efficient for many or large lists)
```python
from itertools import chain
print(list(chain([1, 2], [3], [4, 5])))    # [1, 2, 3, 4, 5]
```

### Pair items from two lists with `zip()`
```python
names = ["Karan", "Riya"]
marks = [80, 90]
print(list(zip(names, marks)))    # [('Karan', 80), ('Riya', 90)]
print(dict(zip(names, marks)))    # {'Karan': 80, 'Riya': 90}
```

### Join a list of strings into one string: `str.join()`
```python
words = ["Python", "is", "fun"]
print(" ".join(words))       # Python is fun
print(", ".join(words))      # Python, is, fun
print("-".join(["a", "b"]))  # a-b
# ", ".join([1, 2])          # TypeError: items must be strings
print(", ".join(str(n) for n in [1, 2, 3]))    # 1, 2, 3
```
Reverse: `"a,b,c".split(",")` gives `['a', 'b', 'c']`.

| Task | Use |
|---|---|
| Combine into a new list | `a + b` or `[*a, *b]` |
| Add to an existing list | `a.extend(b)` or `a += b` |
| Join many lists efficiently | `itertools.chain` |
| Pair items side by side | `zip(a, b)` |
| Join strings into one string | `"sep".join(list)` |

---

## List Methods

| Method | Description | Returns |
|---|---|---|
| `append(x)` | Add item `x` at the end | `None` |
| `insert(i, x)` | Insert `x` at index `i` | `None` |
| `extend(iterable)` | Add every item of the iterable | `None` |
| `remove(x)` | Remove the first item equal to `x` | `None` |
| `pop(i)` | Remove and return item at index `i` (default: last) | the item |
| `clear()` | Remove all items | `None` |
| `index(x)` | Index of the first `x` | an int |
| `count(x)` | Number of times `x` appears | an int |
| `sort()` | Sort the list in place | `None` |
| `reverse()` | Reverse the list in place | `None` |
| `copy()` | Shallow copy of the list | a new list |

There are **11 list methods**. Methods that change the list in place (`append`, `insert`, `extend`, `remove`, `clear`, `sort`, `reverse`) return `None`. The exceptions are `pop()`, which returns the removed item, and `copy()`, `index()` and `count()`, which do not change the list.

---

## Nested Lists

This topic has its own note: [Nested Data Structures](./nested-data-structures.md).

---

## Built-in Functions with Lists

```python
nums = [4, 9, 2, 7]

print(len(nums))                # 4
print(min(nums), max(nums))     # 2 9
print(sum(nums))                # 22
print(sum(nums) / len(nums))    # 5.5     average
print(sorted(nums))             # [2, 4, 7, 9]
print(any(n > 8 for n in nums)) # True    at least one is true
print(all(n > 1 for n in nums)) # True    all are true
```

### `map()` and `filter()`
```python
print(list(map(lambda x: x * 2, nums)))        # [8, 18, 4, 14]
print(list(filter(lambda x: x > 4, nums)))     # [9, 7]
# Same results with comprehensions (usually preferred):
print([x * 2 for x in nums])                   # [8, 18, 4, 14]
print([x for x in nums if x > 4])              # [9, 7]
```

### Unpacking a list

This topic has its own note: [Unpacking](./unpacking.md).


---

## Commonly Used Patterns

### Remove duplicates
```python
nums = [1, 2, 2, 3, 3, 3]
print(list(set(nums)))              # order NOT guaranteed
print(list(dict.fromkeys(nums)))    # [1, 2, 3]   keeps the original order
```

### Find the largest / smallest N
```python
nums = [4, 9, 2, 7]
print(sorted(nums, reverse=True)[:2])    # [9, 7]
```

### Count frequencies
```python
from collections import Counter
print(Counter(["a", "b", "a", "c", "a"]))    # Counter({'a': 3, 'b': 1, 'c': 1})
```

### Use a list as a stack (last in, first out)
```python
stack = []
stack.append(1)
stack.append(2)
print(stack.pop())    # 2
```

### Use a queue (first in, first out)
`list.pop(0)` is slow for big lists because every item shifts. Use `collections.deque`.
```python
from collections import deque
q = deque([1, 2, 3])
q.append(4)
print(q.popleft())    # 1
```

### Split a list into chunks
```python
nums = [1, 2, 3, 4, 5, 6, 7]
chunks = [nums[i:i + 3] for i in range(0, len(nums), 3)]
print(chunks)    # [[1, 2, 3], [4, 5, 6], [7]]
```

### Check if a list is empty
```python
items = []
if not items:
    print("empty")    # empty
```

### Get the last item, first item, or all but the last
```python
nums = [1, 2, 3, 4]
print(nums[0], nums[-1], nums[:-1])    # 1 4 [1, 2, 3]
```

---

## List vs Tuple vs Set vs Dictionary

| Feature | **List** | **Tuple** | **Set** | **Dictionary** |
|---|---|---|---|---|
| Syntax | `[ ]` | `( )` | `{ }` / `set()` | `{k: v}` |
| Ordered | Yes | Yes | No | Yes (insertion order) |
| Mutable | Yes | No | Yes | Yes |
| Duplicates | Yes | Yes | No | Keys: No / Values: Yes |
| Indexing | Yes | Yes | No | By key |
| Items must be hashable | No | No | Yes | Keys only |
| Best for | Changing sequence of items | Fixed data | Unique items, set math | Key to value lookup |

---

- [Back to section index](./README.md)
