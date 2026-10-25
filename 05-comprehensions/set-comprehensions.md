# Set Comprehensions - Revision Notes ~ sm

> **In one line:** A **set comprehension** is just like a list comprehension, but with `{ }` and it builds a **set**: `{expression for item in iterable if condition}`. Duplicates are removed automatically.

## Table of Contents
- [Syntax](#syntax)
- [Basic Examples](#basic-examples)
- [Filter with a Condition](#filter-with-a-condition)
- [Remove Duplicates](#remove-duplicates)
- [Set Operations on Results](#set-operations-on-results)
- [Common Mistakes](#common-mistakes)
- [Which Comprehension to Use](#which-comprehension-to-use)

---

## Syntax

```
{expression for item in iterable if condition}
```
Sets are **unordered** and keep only **unique** items (see the Sets note), so the printed order can vary.

---

## Basic Examples

```python
squares = {x ** 2 for x in range(-3, 4)}
print(sorted(squares))
# Output: [0, 1, 4, 9]
```
(The set itself holds `0, 1, 4, 9` but prints in any order, so `sorted()` is used to show a fixed order.)
`(-2)²` and `2²` are both `4`, but a set keeps only one copy, so the duplicates are removed.

```python
vowels = {ch for ch in "programming" if ch in "aeiou"}
print(sorted(vowels))
# Output: ['a', 'i', 'o']
```
Printed directly, the set could appear in any order (for example `{'o', 'a', 'i'}`).

---

## Filter with a Condition

```python
nums = [1, 2, 2, 3, 4, 4, 5]
print(sorted({n for n in nums if n % 2 == 0}))
# Output: [2, 4]
```

---

## Remove Duplicates

A set comprehension can clean and de-duplicate data at the same time.

```python
words = ["Apple", "apple", "BANANA", "banana", "Cherry"]
unique = {w.lower() for w in words}
print(sorted(unique))
# Output: ['apple', 'banana', 'cherry']
```
Unique first letters:
```python
print(sorted({w[0] for w in ["python", "pandas", "java"]}))
# Output: ['j', 'p']
```

---

## Set Operations on Results

```python
a = {x for x in range(1, 8) if x % 2 == 0}      # {2, 4, 6}
b = {x for x in range(1, 8) if x % 3 == 0}      # {3, 6}
print(sorted(a & b))      # in both
print(sorted(a | b))      # in either
# Output: [6]
# Output: [2, 3, 4, 6]
```

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| `{}` for an empty set | It is an empty **dict** | `set()` |
| Expecting a fixed order | A set has no order | `sorted(...)` when you need one |
| Putting lists inside: `{[x] for x in a}` | `TypeError: unhashable type: 'list'` | Use tuples: `{(x,) for x in a}` |
| Expecting indexing `s[0]` | `TypeError` | Convert to a list or sort first |
| Expecting the count to equal the loop count | Duplicates collapse | Use a list comprehension to keep them |

---

## Which Comprehension to Use

| You want | Use | Brackets |
|---|---|---|
| Ordered items, duplicates allowed | List comprehension | `[ ]` |
| Unique items | Set comprehension | `{ }` |
| Key-value pairs | Dictionary comprehension | `{k: v ...}` |
| One item at a time (lazy, memory friendly) | Generator expression | `( )` |

There is **no tuple comprehension**. `(x for x in a)` is a generator. Use `tuple(x for x in a)` to make a tuple.

---

- [Back to section index](./README.md)
