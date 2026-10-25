# Dictionary Comprehensions - Revision Notes ~ sm

> **In one line:** A **dictionary comprehension** builds a dictionary in one line: `{key_expr: value_expr for item in iterable if condition}`.

## Table of Contents
- [Syntax](#syntax)
- [Basic Example](#basic-example)
- [Filter with a Condition](#filter-with-a-condition)
- [Transform Values](#transform-values)
- [Swap Keys and Values](#swap-keys-and-values)
- [Build from Two Lists](#build-from-two-lists)
- [if else for the Value](#if-else-for-the-value)
- [Counting and Grouping Ideas](#counting-and-grouping-ideas)
- [Duplicate Keys](#duplicate-keys)
- [Common Mistakes](#common-mistakes)

---

## Syntax

```
{key_expression: value_expression for item in iterable if condition}
```

| Part | Meaning |
|---|---|
| `key_expression` | What becomes the **key** (must be hashable: number, string, tuple) |
| `value_expression` | What becomes the **value** |
| `for item in iterable` | The loop |
| `if condition` | Optional filter |

The `{ }` with a `key: value` pair tells Python it is a dictionary (not a set).

---

## Basic Example

```python
squares = {x: x ** 2 for x in range(1, 6)}
print(squares)
# Output: {1: 1, 2: 4, 3: 9, 4: 16, 5: 25}
```
The same with a normal loop:
```python
squares = {}
for x in range(1, 6):
    squares[x] = x ** 2
```

---

## Filter with a Condition

```python
scores = {'Riya': 90, 'Amit': 45, 'Sam': 82}
passed = {name: m for name, m in scores.items() if m >= 50}
print(passed)
# Output: {'Riya': 90, 'Sam': 82}
```
`.items()` gives `(key, value)` pairs, which are unpacked into `name` and `m`.

---

## Transform Values

```python
scores = {'Riya': 90, 'Amit': 45, 'Sam': 82}
doubled = {k: v * 2 for k, v in scores.items()}
print(doubled)
# Output: {'Riya': 180, 'Amit': 90, 'Sam': 164}
```
Transform keys:
```python
print({k.upper(): v for k, v in scores.items()})
# Output: {'RIYA': 90, 'AMIT': 45, 'SAM': 82}
```

---

## Swap Keys and Values

```python
swapped = {v: k for k, v in {'a': 1, 'b': 2}.items()}
print(swapped)
# Output: {1: 'a', 2: 'b'}
```
The values must be **hashable and unique**. If two keys share a value, the last one wins and the other is lost.

---

## Build from Two Lists

```python
names = ["Riya", "Amit", "Sam"]
marks = [90, 45, 82]
d = {n: m for n, m in zip(names, marks)}
print(d)
# Output: {'Riya': 90, 'Amit': 45, 'Sam': 82}
```
`dict(zip(names, marks))` gives the same result.

From a list of words:
```python
words = ["apple", "kiwi", "fig"]
print({w: len(w) for w in words})
# Output: {'apple': 5, 'kiwi': 4, 'fig': 3}
```

---

## if else for the Value

To keep **every** key but change the value, put `if ... else` inside the value expression.

```python
scores = {'Riya': 90, 'Amit': 45, 'Sam': 82}
result = {n: ("pass" if m >= 50 else "fail") for n, m in scores.items()}
print(result)
# Output: {'Riya': 'pass', 'Amit': 'fail', 'Sam': 'pass'}
```
Compare: an `if` at the **end** removes keys; `if ... else` in the value keeps all keys.

---

## Counting and Grouping Ideas

Count letters (simple, but slow for big text because `count` scans each time):
```python
word = "banana"
counts = {ch: word.count(ch) for ch in set(word)}
print(sorted(counts.items()))
# Output: [('a', 3), ('b', 1), ('n', 2)]
```
For real counting use `collections.Counter` (see the Standard Library section). Dictionaries made with comprehensions keep the order in which keys were produced.

---

## Duplicate Keys

If the same key is produced more than once, the **last value replaces** the earlier one.

```python
d = {len(w): w for w in ["a", "bb", "cc", "d"]}
print(d)
# Output: {1: 'd', 2: 'cc'}
```

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| `{x for x in range(3)}` expecting a dict | It is a **set** (no colon) | Write `{x: ... for ...}` |
| Looping over the dict directly `for k, v in scores` | `ValueError` such as `too many values to unpack` (you get keys only) | `scores.items()` |
| Unhashable key `{[1]: 2 ...}` | `TypeError: unhashable type: 'list'` | Use a tuple or string as the key |
| `{}` for an empty dict vs set | `{}` is a dict | Use `set()` for an empty set |
| Changing the dict while looping over it | `RuntimeError: dictionary changed size during iteration` | Build a **new** dict with a comprehension |

---

- [Back to section index](./README.md)
