# Python Dictionaries - Revision Notes ~ sm

> **One-line definition:** A dictionary stores data as **key:value pairs** inside curly brackets `{}`. It is **mutable** (changeable), and **keys must be unique and hashable**.

## Table of Contents
- [What is a Dictionary?](#what-is-a-dictionary)
- [Creating Dictionaries](#creating-dictionaries)
- [Access Items](#access-items)
- [Change Items](#change-items)
- [Add Items](#add-items)
- [Remove Items](#remove-items)
- [Loop Dictionaries](#loop-dictionaries)
- [Copy Dictionaries](#copy-dictionaries)
- [Nested Dictionaries](#nested-dictionaries)
- [Dictionary Methods](#dictionary-methods)
- [Dictionary Comprehension](#dictionary-comprehension)
- [Commonly Used Patterns](#commonly-used-patterns)
- [Dictionary vs List vs Tuple vs Set](#dictionary-vs-list-vs-tuple-vs-set)

---

## What is a Dictionary?

```python
info = {'name': 'Karan', 'age': 19, 'eligible': True}
print(info)
# {'name': 'Karan', 'age': 19, 'eligible': True}
```

| Property | Meaning |
|---|---|
| **Key:value pairs** | Each item has a key and a value; you refer to an item by its **key**, not by an index |
| **Ordered** | Items keep the order in which they were inserted (guaranteed from Python 3.7) |
| **Mutable** | You can change, add and remove items after creation |
| **Unique keys** | A key cannot appear twice; a repeated key keeps the **last** value |
| **Keys must be hashable** | Strings, numbers, tuples (of hashable items) are allowed; lists, sets, dicts are not |
| **Values can be anything** | Any type, including lists, other dicts, functions, duplicates |
| **No indexing / slicing** | `info[0]` looks for the **key** `0`, it does not mean "first item" |

### "Ordered" vs "Unordered" (do not mix them up)
- **Ordered** means the items have a defined order that stays the same (insertion order). Dictionaries are ordered.
- **Unordered** means items have no defined order. Sets are unordered.
- Even though a dictionary is ordered, you still cannot access items by **position**. You access them by **key**.

> Note: In Python 3.6 and earlier, dictionaries did not guarantee order. From Python 3.7 onwards, insertion order is part of the language.

---

## Creating Dictionaries

### Curly brackets (most common)
```python
info = {'name': 'Karan', 'age': 19, 'eligible': True}
```

### Empty dictionary
```python
a = {}
b = dict()
print(type(a), type(b))    # <class 'dict'> <class 'dict'>
```
> `{}` creates an empty **dictionary**, not an empty set. Use `set()` for an empty set.

### The `dict()` constructor
```python
d1 = dict(name='Karan', age=19)                 # keyword arguments (keys become strings)
d2 = dict([('name', 'Karan'), ('age', 19)])     # list of (key, value) pairs
d3 = dict(zip(['a', 'b', 'c'], [1, 2, 3]))      # two lists joined with zip()
print(d1)    # {'name': 'Karan', 'age': 19}
print(d2)    # {'name': 'Karan', 'age': 19}
print(d3)    # {'a': 1, 'b': 2, 'c': 3}
```

### `fromkeys()` - same value for many keys
```python
d = dict.fromkeys(['a', 'b', 'c'], 0)
print(d)    # {'a': 0, 'b': 0, 'c': 0}
```
> Warning: if the shared value is a mutable object (like a list), **all keys point to the same object**.
```python
bad = dict.fromkeys(['a', 'b'], [])
bad['a'].append(1)
print(bad)    # {'a': [1], 'b': [1]}   <- both changed!
```

### Rules for keys
```python
ok = {1: 'one', 'two': 2, (3, 4): 'tuple key', 5.5: 'float key'}   # valid keys
# bad = {[1, 2]: 'x'}     # TypeError: unhashable type: 'list'
```

### Duplicate keys - the last value wins
```python
d = {'a': 1, 'b': 2, 'a': 99}
print(d)    # {'a': 99, 'b': 2}
```

### Gotcha: `1`, `1.0` and `True` are the same key
```python
d = {1: 'int', True: 'bool', 1.0: 'float'}
print(d)    # {1: 'float'}   (one key, last value wins)
```

---

## Access Items

### Single value: square brackets or `get()`
```python
info = {'name': 'Karan', 'age': 19, 'eligible': True}
print(info['name'])           # Karan
print(info.get('eligible'))   # True
```

### `[ ]` vs `get()` - the key difference
| | `info['x']` | `info.get('x')` |
|---|---|---|
| Key exists | returns the value | returns the value |
| Key missing | raises `KeyError` | returns `None` (no error) |
| Custom fallback | not possible | `info.get('x', default)` |

```python
print(info.get('city'))              # None
print(info.get('city', 'Unknown'))   # Unknown
# print(info['city'])                # KeyError: 'city'
```

### All values: `values()`
```python
print(info.values())    # dict_values(['Karan', 19, True])
```

### All keys: `keys()`
```python
print(info.keys())      # dict_keys(['name', 'age', 'eligible'])
```

### All key-value pairs: `items()`
```python
print(info.items())     # dict_items([('name', 'Karan'), ('age', 19), ('eligible', True)])
```

### Views are live
`keys()`, `values()` and `items()` return **view objects**. They update automatically when the dictionary changes.
```python
info = {'name': 'Karan', 'age': 19}
k = info.keys()
info['city'] = 'Pune'
print(k)                # dict_keys(['name', 'age', 'city'])
print(list(k))          # ['name', 'age', 'city']   <- convert to a list if needed
```

### Check if a key exists
`in` checks **keys** only (not values).
```python
info = {'name': 'Karan', 'age': 19}
print('name' in info)             # True
print('Karan' in info)            # False   (that is a value, not a key)
print('Karan' in info.values())   # True
print('city' not in info)         # True
print(len(info))                  # 2
```

---

## Change Items

### Assign to an existing key
```python
info = {'name': 'Karan', 'age': 19, 'eligible': True}
info['age'] = 20
print(info)    # {'name': 'Karan', 'age': 20, 'eligible': True}
```

### `update()` - change one or many values
```python
info.update({'age': 21, 'eligible': False})
print(info)    # {'name': 'Karan', 'age': 21, 'eligible': False}

info.update(age=22)      # keyword form (works for string keys)
print(info['age'])       # 22
```

### Change a value using its current value
```python
scores = {'math': 80, 'sci': 90}
scores['math'] += 5
print(scores)    # {'math': 85, 'sci': 90}
```

### Change every value (loop)
```python
prices = {'pen': 10, 'book': 100}
for key in prices:
    prices[key] = prices[key] * 2      # changing a VALUE while looping is safe
print(prices)    # {'pen': 20, 'book': 200}
```

> Note: `update()` and plain assignment behave the same on an **existing** key. The difference appears for a **new** key, see the next section.

---

## Add Items

### Assign to a new key
```python
info = {'name': 'Karan', 'age': 19, 'eligible': True}
info['DOB'] = 2001
print(info)    # {'name': 'Karan', 'age': 19, 'eligible': True, 'DOB': 2001}
```

### `update()` - updates an existing key, creates a new key if missing
```python
info = {'name': 'Karan', 'age': 19, 'eligible': True}
info.update({'age': 20})
info.update({'DOB': 2001})
print(info)    # {'name': 'Karan', 'age': 20, 'eligible': True, 'DOB': 2001}
```

### `setdefault()` - add only if the key is missing
Returns the value of the key. If the key does not exist, it inserts the key with the given default.
```python
d = {'a': 1}
print(d.setdefault('a', 100))   # 1     (key exists, nothing changes)
print(d.setdefault('b', 2))     # 2     (key added)
print(d)                        # {'a': 1, 'b': 2}
```

### Merge dictionaries with `|` (Python 3.9+)
```python
a = {'x': 1, 'y': 2}
b = {'y': 99, 'z': 3}

print(a | b)    # {'x': 1, 'y': 99, 'z': 3}   new dict, b wins on clashes
a |= b          # in-place, same as a.update(b)
print(a)        # {'x': 1, 'y': 99, 'z': 3}
```
For older versions: `{**a, **b}`.

### Add many keys with the same value
```python
d = dict.fromkeys(['x', 'y'], 0)
print(d)    # {'x': 0, 'y': 0}
```

---

## Remove Items

| Method / keyword | What it does | If key is missing |
|---|---|---|
| `pop(key)` | Removes the key and **returns its value** | `KeyError` (unless a default is given) |
| `pop(key, default)` | Same, but returns `default` instead of an error | No error |
| `popitem()` | Removes and returns the **last inserted** `(key, value)` pair | `KeyError` on an empty dict |
| `del d[key]` | Removes that key (returns nothing) | `KeyError` |
| `clear()` | Removes **all** items, dict becomes `{}` | - |
| `del d` | Deletes the **whole dictionary variable** | - |

### `pop()`
```python
info = {'name': 'Karan', 'age': 19, 'eligible': True}
removed = info.pop('eligible')
print(info)       # {'name': 'Karan', 'age': 19}
print(removed)    # True

print(info.pop('city', 'not found'))   # not found   (safe, no error)
```

### `popitem()`
```python
info = {'name': 'Karan', 'age': 19, 'eligible': True, 'DOB': 2003}
last = info.popitem()
print(info)    # {'name': 'Karan', 'age': 19, 'eligible': True}
print(last)    # ('DOB', 2003)
```
> `popitem()` removes the **last inserted** pair (LIFO) in Python 3.7+. In older versions it removed an arbitrary item.

### `del` with a key
```python
info = {'name': 'Karan', 'age': 19, 'eligible': True, 'DOB': 2003}
del info['age']
print(info)    # {'name': 'Karan', 'eligible': True, 'DOB': 2003}
```

### `clear()`
```python
info = {'name': 'Karan', 'age': 19}
info.clear()
print(info)    # {}
```

### `del` without a key deletes the whole dictionary
```python
info = {'name': 'Karan', 'age': 19}
del info
# print(info)    # NameError: name 'info' is not defined
```
> `clear()` empties the dictionary (variable still exists). `del info` removes the variable itself.

---

## Loop Dictionaries

```python
info = {'name': 'Karan', 'age': 19, 'eligible': True}
```

### Loop over keys (default)
```python
for key in info:
    print(key)
# same as: for key in info.keys():
```

### Loop over values
```python
for value in info.values():
    print(value)
```

### Loop over key and value together (most used)
```python
for key, value in info.items():
    print(key, '->', value)
# name -> Karan
# age -> 19
# eligible -> True
```

### Using `enumerate()` for a counter
```python
for i, (key, value) in enumerate(info.items(), start=1):
    print(i, key, value)
```

### Sorted loops
```python
scores = {'Riya': 90, 'Amit': 75, 'Sam': 82}

for name in sorted(scores):                       # sorted by key
    print(name, scores[name])

for name, mark in sorted(scores.items(), key=lambda kv: kv[1], reverse=True):   # by value, high to low
    print(name, mark)
```

### Reverse order (Python 3.8+)
```python
for key in reversed(info):
    print(key)
```

### Do NOT add or remove keys while looping
```python
d = {'a': 1, 'b': 2, 'c': 3}
# for k in d:
#     del d[k]            # RuntimeError: dictionary changed size during iteration

for k in list(d):         # loop over a COPY of the keys instead
    if d[k] == 2:
        del d[k]
print(d)    # {'a': 1, 'c': 3}
```
> Changing the **value** of an existing key while looping is fine. Changing the **size** (add/remove keys) is not.

---

## Copy Dictionaries

### Why `b = a` is NOT a copy
```python
a = {'x': 1}
b = a              # both names point to the SAME dictionary
b['x'] = 99
print(a)           # {'x': 99}   <- a changed too
```

### `copy()`
```python
info = {'name': 'Karan', 'age': 19, 'eligible': True, 'DOB': 2003}
newDictionary = info.copy()
print(newDictionary)    # {'name': 'Karan', 'age': 19, 'eligible': True, 'DOB': 2003}
```

### `dict()`
```python
newDictionary = dict(info)
print(newDictionary)    # {'name': 'Karan', 'age': 19, 'eligible': True, 'DOB': 2003}
```

Other ways: `{**info}` and `info | {}` (3.9+).

### Shallow copy vs deep copy
`copy()` and `dict()` make a **shallow** copy: the outer dictionary is new, but nested objects (lists, dicts) are still **shared**.

```python
original = {'name': 'Karan', 'marks': [80, 90]}
shallow = original.copy()
shallow['marks'].append(100)
print(original)    # {'name': 'Karan', 'marks': [80, 90, 100]}   <- changed!
```

Use `copy.deepcopy()` for a fully independent copy:
```python
import copy
original = {'name': 'Karan', 'marks': [80, 90]}
deep = copy.deepcopy(original)
deep['marks'].append(100)
print(original)    # {'name': 'Karan', 'marks': [80, 90]}    <- safe
print(deep)        # {'name': 'Karan', 'marks': [80, 90, 100]}
```

| Method | Copy type | Nested objects |
|---|---|---|
| `b = a` | No copy (same object) | Shared |
| `a.copy()`, `dict(a)`, `{**a}` | Shallow | Shared |
| `copy.deepcopy(a)` | Deep | Independent |

---

## Nested Dictionaries

This topic has its own note: [Nested Data Structures](./nested-data-structures.md).

---

## Dictionary Methods

| Method | Description | Example |
|---|---|---|
| `clear()` | Removes all items | `d.clear()` |
| `copy()` | Returns a shallow copy | `d2 = d.copy()` |
| `fromkeys(keys, value)` | New dict with the given keys, all set to `value` | `dict.fromkeys(['a','b'], 0)` |
| `get(key, default)` | Value of key, or `default` (`None`) if missing | `d.get('x', 0)` |
| `items()` | View of `(key, value)` pairs | `for k, v in d.items()` |
| `keys()` | View of all keys | `list(d.keys())` |
| `pop(key, default)` | Removes key and returns its value | `d.pop('x', None)` |
| `popitem()` | Removes and returns the last inserted pair | `d.popitem()` |
| `setdefault(key, default)` | Returns value; inserts key with `default` if missing | `d.setdefault('x', 0)` |
| `update(other)` | Updates/adds items from another dict or pairs | `d.update({'a': 1})` |
| `values()` | View of all values | `sum(d.values())` |

### Useful built-in functions with dictionaries
```python
d = {'a': 3, 'b': 1, 'c': 2}

print(len(d))                  # 3       number of pairs
print(sorted(d))               # ['a', 'b', 'c']        sorted keys (returns a list)
print(max(d))                  # 'c'     largest KEY
print(max(d.values()))         # 3       largest VALUE
print(max(d, key=d.get))       # 'a'     KEY that has the largest value
print(sum(d.values()))         # 6
print(list(d))                 # ['a', 'b', 'c']
print(any(v > 2 for v in d.values()))   # True
```

---

## Dictionary Comprehension

This topic has its own note: [Dictionary Comprehensions](../05-comprehensions/dictionary-comprehensions.md).

---

## Commonly Used Patterns

### Count occurrences
```python
text = "banana"
count = {}
for ch in text:
    count[ch] = count.get(ch, 0) + 1
print(count)    # {'b': 1, 'a': 3, 'n': 2}
```
Shortcut with `collections.Counter`:
```python
from collections import Counter
print(Counter("banana"))                  # Counter({'a': 3, 'n': 2, 'b': 1})
print(Counter("banana").most_common(1))   # [('a', 3)]
```

### Group items
```python
words = ['apple', 'avocado', 'banana', 'blueberry', 'cherry']
groups = {}
for w in words:
    groups.setdefault(w[0], []).append(w)
print(groups)
# {'a': ['apple', 'avocado'], 'b': ['banana', 'blueberry'], 'c': ['cherry']}
```
Shortcut with `collections.defaultdict` (missing keys are created automatically):
```python
from collections import defaultdict
groups = defaultdict(list)
for w in words:
    groups[w[0]].append(w)
```

### Create a dictionary from two lists
```python
keys = ['name', 'age']
values = ['Karan', 19]
print(dict(zip(keys, values)))    # {'name': 'Karan', 'age': 19}
```

### Sort a dictionary by value
```python
scores = {'Riya': 90, 'Amit': 75, 'Sam': 82}
by_value = dict(sorted(scores.items(), key=lambda kv: kv[1]))
print(by_value)    # {'Amit': 75, 'Sam': 82, 'Riya': 90}
```

### Key with the maximum value
```python
best = max(scores, key=scores.get)
print(best)    # Riya
```

### Safe nested access
```python
config = {'db': {'host': 'localhost'}}
print(config.get('db', {}).get('port', 3306))    # 3306
```

### Dictionaries and JSON
JSON objects map directly to Python dictionaries.
```python
import json
data = {'name': 'Karan', 'age': 19}
text = json.dumps(data)           # dict -> JSON string
print(text)                       # {"name": "Karan", "age": 19}
back = json.loads(text)           # JSON string -> dict
print(back == data)               # True
```

### Dictionary as a replacement for if-elif chains
```python
def add(a, b): return a + b
def sub(a, b): return a - b

ops = {'+': add, '-': sub}
print(ops['+'](5, 3))    # 8
```

---

## Dictionary vs List vs Tuple vs Set

| Feature | **Dictionary** | **List** | **Tuple** | **Set** |
|---|---|---|---|---|
| Syntax | `{k: v}` | `[ ]` | `( )` | `{ }` / `set()` |
| Stores | Key:value pairs | Items | Items | Unique items |
| Ordered | Yes (insertion order) | Yes | Yes | No |
| Mutable | Yes | Yes | No | Yes |
| Duplicates | Keys: No / Values: Yes | Yes | Yes | No |
| Access by | Key | Index | Index | Not possible (loop / `in`) |
| Lookup speed | Very fast (hash) | Slow for search | Slow for search | Very fast (hash) |
| Best for | Labelled data, fast lookup by key | Changing sequences | Fixed data | Uniqueness, set math |

---

- [Back to section index](./README.md)
