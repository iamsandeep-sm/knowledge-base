# List Comprehensions - Revision Notes ~ sm

> **In one line:** A **list comprehension** is a short way to create a **new list** from an existing iterable (list, tuple, dictionary, set, string, range, ...) in a single line: `[expression for item in iterable if condition]`.

## Table of Contents
- [Why Use a List Comprehension?](#why-use-a-list-comprehension)
- [Syntax](#syntax)
- [Without a Condition](#without-a-condition)
- [With a Condition (Filter)](#with-a-condition-filter)
- [Transform Every Item](#transform-every-item)
- [if else Inside a Comprehension](#if-else-inside-a-comprehension)
- [Nested Loops in a Comprehension](#nested-loops-in-a-comprehension)
- [From Other Iterables](#from-other-iterables)
- [Comprehension vs for Loop vs map / filter](#comprehension-vs-for-loop-vs-map--filter)
- [Useful Patterns](#useful-patterns)
- [Common Mistakes](#common-mistakes)
- [When Not to Use One](#when-not-to-use-one)

---

## Why Use a List Comprehension?

Building a list with a normal loop takes three lines:

```python
squares = []
for x in range(1, 6):
    squares.append(x ** 2)
print(squares)
# Output: [1, 4, 9, 16, 25]
```
A list comprehension does the same in one line:
```python
squares = [x ** 2 for x in range(1, 6)]
print(squares)
# Output: [1, 4, 9, 16, 25]
```
It is shorter, easy to read once you know the pattern, and usually a little faster.

---

## Syntax

```
new_list = [expression for item in iterable if condition]
```

| Part | Meaning |
|---|---|
| `expression` | The value to put into the new list |
| `item` | Loop variable |
| `iterable` | Anything you can loop over |
| `condition` | Optional filter: only items where it is `True` are used |

A way to read it: *"give me **expression** for each **item** in **iterable** (if **condition**)"*.

The original iterable is **not changed**. A new list is made.

---

## Without a Condition

```python
squares = [x ** 2 for x in range(1, 6)]
print(squares)
# Output: [1, 4, 9, 16, 25]
```

Dry run of `[x * 2 for x in [1, 2, 3]]`:

| Step | `x` | Expression `x * 2` | List so far |
|---|---|---|---|
| 1 | 1 | 2 | `[2]` |
| 2 | 2 | 4 | `[2, 4]` |
| 3 | 3 | 6 | `[2, 4, 6]` |

---

## With a Condition (Filter)

The `if` at the **end** keeps only the items that pass.

### Names that contain "o"
```python
names = ["Milo", "Sarah", "Bruno", "Anastasia", "Rosa"]
namesWith_O = [item for item in names if "o" in item]
print(namesWith_O)
# Output: ['Milo', 'Bruno', 'Rosa']
```
The comprehension checks each name and includes it only if it contains the letter `"o"`.

### Names longer than 4 letters
```python
names = ["Milo", "Sarah", "Bruno", "Anastasia", "Rosa"]
namesLong = [item for item in names if len(item) > 4]
print(namesLong)
# Output: ['Sarah', 'Bruno', 'Anastasia']
```

### Even numbers
```python
evens = [n for n in range(10) if n % 2 == 0]
print(evens)
# Output: [0, 2, 4, 6, 8]
```
Several conditions can be joined with `and` / `or`:
```python
print([n for n in range(20) if n % 2 == 0 and n % 3 == 0])
# Output: [0, 6, 12, 18]
```

---

## Transform Every Item

```python
names = ["Milo", "Sarah", "Bruno", "Anastasia", "Rosa"]
print([n.upper() for n in names])
print([len(n) for n in names])
# Output: ['MILO', 'SARAH', 'BRUNO', 'ANASTASIA', 'ROSA']
# Output: [4, 5, 5, 9, 4]
```
Transform **and** filter together:
```python
print([n.upper() for n in names if len(n) > 4])
# Output: ['SARAH', 'BRUNO', 'ANASTASIA']
```

---

## if else Inside a Comprehension

When you use `else`, the condition moves **before** the `for`. This is not a filter; every item is kept, but its value depends on the condition.

```python
nums = [1, 2, 3, 4, 5]
labels = ["even" if n % 2 == 0 else "odd" for n in nums]
print(labels)
# Output: ['odd', 'even', 'odd', 'even', 'odd']
```

| Form | Where the `if` goes | Effect |
|---|---|---|
| Filter only (no else) | at the **end**: `[x for x in a if x > 0]` | Fewer items |
| `if ... else` (transform) | at the **start**: `[x if x > 0 else 0 for x in a]` | Same number of items |

```python
a = [3, -1, 4, -5]
print([x for x in a if x > 0])
print([x if x > 0 else 0 for x in a])
# Output: [3, 4]
# Output: [3, 0, 4, 0]
```

---

## Nested Loops in a Comprehension

More than one `for` is allowed. They run in the **same order as nested loops** (left one is the outer loop).

```python
pairs = [(x, y) for x in [1, 2] for y in ["a", "b"]]
print(pairs)
# Output: [(1, 'a'), (1, 'b'), (2, 'a'), (2, 'b')]
```
Same as:
```python
pairs = []
for x in [1, 2]:
    for y in ["a", "b"]:
        pairs.append((x, y))
```

### Flatten a 2D list
```python
matrix = [[1, 2], [3, 4], [5, 6]]
flat = [n for row in matrix for n in row]
print(flat)
# Output: [1, 2, 3, 4, 5, 6]
```

### A 2D list (list of lists)
An inner comprehension builds each row:
```python
grid = [[r * c for c in range(1, 4)] for r in range(1, 4)]
print(grid)
# Output: [[1, 2, 3], [2, 4, 6], [3, 6, 9]]
```

---

## From Other Iterables

```python
print([ch for ch in "hello"])
print([k for k in {"a": 1, "b": 2}])
print([x for x in (10, 20, 30) if x > 10])
# Output: ['h', 'e', 'l', 'l', 'o']
# Output: ['a', 'b']
# Output: [20, 30]
```
Looping over a dictionary gives its **keys**. Use `.items()` for pairs:
```python
scores = {"Riya": 90, "Amit": 45}
print([f"{name}: {mark}" for name, mark in scores.items()])
# Output: ['Riya: 90', 'Amit: 45']
```

---

## Comprehension vs for Loop vs map / filter

```python
nums = [1, 2, 3, 4, 5, 6]

# for loop
result = []
for n in nums:
    if n % 2 == 0:
        result.append(n * 10)

# comprehension
result2 = [n * 10 for n in nums if n % 2 == 0]

# map + filter
result3 = list(map(lambda n: n * 10, filter(lambda n: n % 2 == 0, nums)))

print(result, result2, result3)
# Output: [20, 40, 60] [20, 40, 60] [20, 40, 60]
```
All three give the same list. The comprehension is usually the most readable.

---

## Useful Patterns

```python
# convert text numbers to int
print([int(s) for s in ["1", "2", "3"]])

# remove spaces from each word
print([w.strip() for w in [" a ", "b  ", "  c"]])

# first letters
print([w[0] for w in ["python", "java", "go"]])

# index of every match
letters = ["a", "b", "a", "c"]
print([i for i, ch in enumerate(letters) if ch == "a"])

# add two lists item by item
print([a + b for a, b in zip([1, 2, 3], [10, 20, 30])])
# Output: [1, 2, 3]
# Output: ['a', 'b', 'c']
# Output: ['p', 'j', 'g']
# Output: [0, 2]
# Output: [11, 22, 33]
```

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| `[x for x in a else 0]` or `[x if x > 0 for x in a]` | `SyntaxError` | `if` alone goes at the **end**; `if ... else` goes at the **start** |
| Expecting the original list to change | It does not; a new list is made | Assign it: `a = [x * 2 for x in a]` |
| Using `append` inside: `[a.append(x) for x in b]` | Builds a useless list of `None` | Use a normal `for` loop for side effects |
| Using the loop variable after the comprehension | `NameError` (it does not leak, see the Scope note) | Use the result list instead |
| `[[0] * 3] * 3` for a grid | All rows are the **same** list | `[[0] * 3 for _ in range(3)]` |

The last one in detail:
```python
bad = [[0] * 2] * 2
bad[0][0] = 9
print(bad)

good = [[0] * 2 for _ in range(2)]
good[0][0] = 9
print(good)
# Output: [[9, 0], [9, 0]]
# Output: [[9, 0], [0, 0]]
```

---

## When Not to Use One

- The logic needs **several statements** or is hard to read at a glance.
- Nesting is more than two levels deep.
- You do not need the list itself (you only want a side effect like printing).
- You only need to loop once over a huge result: a **generator expression** `( ... )` uses far less memory (see the Generator Expressions note).

> The same idea works for sets `{x for ...}` and dictionaries `{k: v for ...}`.
> If a comprehension becomes long or hard to read, use a normal `for` loop instead.

---

- [Back to section index](./README.md)
