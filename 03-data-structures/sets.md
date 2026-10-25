# Python Sets — Revision Notes ~ sm

> **One-line definition:** A set is an **unordered** collection of **unique** items, written inside curly brackets `{}`. It is **mutable** (you can add/remove items), but every item inside it must be **immutable (hashable)**.

## Table of Contents
- [What is a Set?](#what-is-a-set)
- [Creating Sets](#creating-sets)
- [Accessing Set Items](#accessing-set-items)
- [Adding Items](#adding-items)
- [Removing Items](#removing-items)
- [Join Sets — Mathematical Operations](#join-sets--mathematical-operations)
- [Other Set Methods — Comparisons](#other-set-methods--comparisons)
- [Operators Cheat Table](#operators-cheat-table)
- [Copying, Length & Membership](#copying-length--membership)
- [Frozenset — The Immutable Set](#frozenset--the-immutable-set)
- [Set Comprehension](#set-comprehension)
- [Practical Uses of Sets](#practical-uses-of-sets)
- [Set vs List vs Tuple vs Dict](#set-vs-list-vs-tuple-vs-dict)

---

## What is a Set?

| Property | Meaning |
|---|---|
| **Unordered** | No fixed position; items can appear in any order when printed |
| **Unique** | Duplicates are automatically removed |
| **Mutable** | You can add or remove items |
| **Items must be hashable** | Numbers, strings, tuples are allowed; lists, dicts, sets are not |
| **No indexing / slicing** | `s[0]` gives `TypeError` |
| **Heterogeneous** | Can mix data types |

### Why is a set "unordered"?
Sets do **not** remember the order in which items were inserted. When you print a set, Python shows the items based on their **internal memory layout**, which depends on a mechanism called **hashing**. That's why the output order can look random and can differ between runs.

> Sets use hashing, which is why checking `x in my_set` is **very fast** (average O(1)) compared to a list (O(n)).

---

## Creating Sets

### Basic creation (duplicates removed automatically)
```python
info = {"Carla", 19, False, 5.9, 19}
print(info)
# {False, 'Carla', 5.9, 19}   ← 19 appears only once (order may vary)
```

### Empty set trap
```python
a = {}           # Wrong: this is an empty DICTIONARY
b = set()        # Correct: this is an empty SET

print(type(a))   # <class 'dict'>
print(type(b))   # <class 'set'>
```
> Always use `set()` to create an empty set.

### Using the `set()` constructor
```python
s1 = set([1, 2, 2, 3])        # from list  → {1, 2, 3}
s2 = set("hello")             # from string → {'h', 'e', 'l', 'o'} (order may vary)
s3 = set((10, 20, 10))        # from tuple → {10, 20}
s4 = set(range(5))            # → {0, 1, 2, 3, 4}
```

### Items must be immutable (hashable)
```python
ok  = {1, "a", (1, 2)}         # Correct: tuple is allowed
bad = {1, [2, 3]}              # TypeError: unhashable type: 'list'
```

### Gotcha: `1`, `1.0` and `True` are treated as equal
```python
print({1, True, 1.0})          # {1}  → all three are considered the same value
print({0, False})              # {0}
```

---

## Accessing Set Items

Since sets have no index, you **cannot** do `my_set[0]`. You can only **loop** through them or **check membership**.

```python
info = {"Carla", 19, False, 5.9}
for item in info:
    print(item)
```

```python
print("Carla" in info)       # True
print("Bob" not in info)     # True
```

---

## Adding Items

| Method | Use |
|---|---|
| `add(x)` | Add a **single** item |
| `update(iterable)` | Add **multiple** items (from another set, list, tuple, string, ...) |

### `add()` — one item
```python
cities = {"Tokyo", "Madrid", "Berlin", "Delhi"}
cities.add("Helsinki")
print(cities)
```
> Adding an item that already exists does nothing (no error).

### `update()` — many items
```python
cities = {"Tokyo", "Madrid", "Berlin", "Delhi"}
cities2 = {"Helsinki", "Warsaw", "Seoul"}
cities.update(cities2)
print(cities)
```

`update()` accepts **any iterable**, not just sets:
```python
cities.update(["Rome", "Paris"])      # list
cities.update(("Oslo", "Lima"))       # tuple
```

> **Gotcha:** `update("abc")` adds the **characters** `'a'`, `'b'`, `'c'` separately, because a string is an iterable of characters. To add a whole string, use `add("abc")`.

---

## Removing Items

| Method | What it does | If item is missing |
|---|---|---|
| `remove(x)` | Removes item `x` | Raises `KeyError` |
| `discard(x)` | Removes item `x` | No error |
| `pop()` | Removes and **returns** an **arbitrary** item | `KeyError` on empty set |
| `clear()` | Removes **all** items → empty set | — |
| `del set_name` | Deletes the **entire set** (keyword, not a method) | — |

### `remove()` vs `discard()`
```python
cities = {"Tokyo", "Madrid", "Berlin", "Delhi"}
cities.remove("Tokyo")
print(cities)

cities = {"Tokyo", "Madrid", "Berlin", "Delhi"}
cities.discard("Delhi")
print(cities)

cities.discard("Paris")     # Correct: no error, even though "Paris" isn't there
# cities.remove("Paris")    # KeyError: 'Paris'
```
> **Main difference:** deleting a missing item → `remove()` raises an error, `discard()` does not.

### `pop()`, `del` and `clear()`
```python
cities = {"Tokyo", "Madrid", "Berlin", "Delhi"}
item = cities.pop()          # removes some item; we can't predict which
print(cities)
print(item)                  # the removed item

del cities                   # deletes the whole set
# print(cities)              # NameError: name 'cities' is not defined

city = {"Tokyo", "Madrid", "Berlin", "Delhi"}
city.clear()
print(city)                  # set()
```

> **Correction to remember:** `pop()` removes an **arbitrary** item — *not* "the last item" as in lists. Since sets are unordered there is no "last" item. You can store the removed item by assigning `pop()` to a variable.

> An empty set prints as `set()`, **not** `{}`.

---

## Join Sets — Mathematical Operations

Sets support operations just like **mathematical set theory** (Venn diagrams).

Using these two example sets throughout:
```python
cities  = {"Tokyo", "Madrid", "Berlin", "Delhi"}
cities2 = {"Tokyo", "Seoul", "Kabul", "Madrid"}
```

```
  only in cities     in both         only in cities2
  --------------   -------------   -----------------
  Berlin, Delhi    Tokyo, Madrid     Seoul, Kabul

  cities  = only-in-cities + both
  cities2 = both + only-in-cities2
```

### Union — everything from both sets
`union()` / `|` returns a **new** set. `update()` changes the **existing** set.

```python
cities3 = cities.union(cities2)
print(cities3)
# {'Tokyo', 'Madrid', 'Kabul', 'Seoul', 'Berlin', 'Delhi'}

cities.update(cities2)           # modifies `cities` itself
print(cities)
# {'Berlin', 'Madrid', 'Tokyo', 'Delhi', 'Kabul', 'Seoul'}
```

**Operator version:**
```python
set1 = {"a", "b", "c"}
set2 = {1, 2, 3}
set3 = set1 | set2
print(set3)
```
> `union()` can take multiple sets / any iterable: `a.union(b, c, [1, 2])`. The `|` operator needs **both sides to be sets**.

### Intersection — only items common to both
`intersection()` / `&` returns a **new** set. `intersection_update()` changes the **existing** set.

```python
cities3 = cities.intersection(cities2)
print(cities3)
# {'Madrid', 'Tokyo'}

cities.intersection_update(cities2)
print(cities)
# {'Tokyo', 'Madrid'}
```

### Symmetric Difference — items NOT common (in either, but not both)
`symmetric_difference()` / `^` returns a **new** set. `symmetric_difference_update()` changes the **existing** set.

```python
cities  = {"Tokyo", "Madrid", "Berlin", "Delhi"}
cities2 = {"Tokyo", "Seoul", "Kabul", "Madrid"}

cities3 = cities.symmetric_difference(cities2)
print(cities3)
# {'Seoul', 'Kabul', 'Berlin', 'Delhi'}

cities.symmetric_difference_update(cities2)
print(cities)
# {'Kabul', 'Delhi', 'Berlin', 'Seoul'}
```

### Difference — items only in the original set
`difference()` / `-` returns a **new** set. `difference_update()` changes the **existing** set.

> **Order matters!** `A - B` ≠ `B - A`.

```python
cities  = {"Tokyo", "Madrid", "Berlin", "Delhi"}
cities2 = {"Seoul", "Kabul", "Delhi"}

cities3 = cities.difference(cities2)
print(cities3)
# {'Tokyo', 'Madrid', 'Berlin'}

print(cities.difference(cities2))
# {'Tokyo', 'Berlin', 'Madrid'}

print(cities2 - cities)
# {'Seoul', 'Kabul'}     ← different result when reversed
```

### Summary table

| Operation | Returns new set | Updates existing set | Operator | Meaning |
|---|---|---|---|---|
| Union | `union()` | `update()` | `\|` | All items from both |
| Intersection | `intersection()` | `intersection_update()` | `&` | Only common items |
| Symmetric difference | `symmetric_difference()` | `symmetric_difference_update()` | `^` | Items not common |
| Difference | `difference()` | `difference_update()` | `-` | Only in first set |

> **Rule of thumb:** methods **without** `_update` → return a **new set**, original unchanged. Methods **with** `_update` → modify the **original** and return `None`.

---

## Other Set Methods — Comparisons

These methods return `True` / `False`.

### `isdisjoint()` — do the sets have NOTHING in common?
Returns `True` if there are **no common items**, `False` if **at least one** item is common.

```python
cities  = {"Tokyo", "Madrid", "Berlin", "Delhi"}
cities2 = {"Tokyo", "Seoul", "Kabul", "Madrid"}
print(cities.isdisjoint(cities2))     # False  → Tokyo & Madrid are common

cities  = {"Tokyo", "Madrid", "Berlin", "Delhi"}
cities2 = {"Seoul", "Kabul"}
print(cities.isdisjoint(cities2))     # True   → nothing in common
```

### `issuperset()` — does the original set contain ALL items of the other?
```python
cities = {"Tokyo", "Madrid", "Berlin", "Delhi"}

cities2 = {"Seoul", "Kabul"}
print(cities.issuperset(cities2))     # False

cities3 = {"Seoul", "Madrid", "Kabul"}
print(cities.issuperset(cities3))     # False  (Seoul, Kabul missing)

cities2 = {"Delhi", "Madrid"}
print(cities.issuperset(cities2))     # True
```

### `issubset()` — are ALL items of the original set inside the other set?
```python
cities = {"Tokyo", "Madrid", "Berlin", "Delhi"}

cities2 = {"Delhi", "Madrid"}
print(cities2.issubset(cities))       # True

cities2 = {"Seoul", "Kabul"}
print(cities2.issubset(cities))       # False

cities3 = {"Seoul", "Madrid", "Kabul"}
print(cities3.issubset(cities))       # False
```

> **Easy memory trick:**
> - `A.issubset(B)` → "Is **A inside** B?" (A is the smaller one)
> - `A.issuperset(B)` → "Does **A contain** B?" (A is the bigger one)
> - They are mirror images: `A.issubset(B)` is the same as `B.issuperset(A)`

### Operator versions
```python
A = {1, 2}
B = {1, 2, 3}

print(A <= B)    # True  → subset
print(A <  B)    # True  → PROPER subset (subset AND not equal)
print(B >= A)    # True  → superset
print(B >  A)    # True  → proper superset
print(A == B)    # False → same items?
```

---

## Operators Cheat Table

| Operator | Method equivalent | Example result (`A={1,2,3}`, `B={3,4}`) |
|---|---|---|
| `A \| B` | `A.union(B)` | `{1, 2, 3, 4}` |
| `A & B` | `A.intersection(B)` | `{3}` |
| `A - B` | `A.difference(B)` | `{1, 2}` |
| `A ^ B` | `A.symmetric_difference(B)` | `{1, 2, 4}` |
| `A <= B` | `A.issubset(B)` | `False` |
| `A >= B` | `A.issuperset(B)` | `False` |

> Operators need **both sides to be sets**. The named methods accept **any iterable** (list, tuple, ...).

---

## Copying, Length & Membership

```python
a = {1, 2, 3}

b = a.copy()          # independent copy (shallow)
print(len(a))         # 3
print(2 in a)         # True
```

> `b = a` does **not** copy — both names point to the **same** set. Use `a.copy()` or `set(a)`.

---

## Frozenset — The Immutable Set

A **frozenset** is like a set, but **cannot be changed** after creation. Because it's immutable, it is **hashable**, so it can be used as a **dictionary key** or placed **inside another set**.

```python
fs = frozenset([1, 2, 3, 2])
print(fs)                 # frozenset({1, 2, 3})

# fs.add(4)               # AttributeError: 'frozenset' object has no attribute 'add'

# Sets of sets are possible only with frozenset:
nested = {frozenset({1, 2}), frozenset({3})}
```
It still supports union, intersection, difference, `in`, `len()`, etc. (they return new frozensets).

---

## Set Comprehension

This topic has its own note: [Set Comprehensions](../05-comprehensions/set-comprehensions.md).

---

## Practical Uses of Sets

### Remove duplicates from a list
```python
nums = [1, 2, 2, 3, 3, 3, 4]
unique = list(set(nums))
print(unique)             # [1, 2, 3, 4]  (order not guaranteed)
```

### Remove duplicates but keep original order
```python
nums = [4, 1, 2, 4, 1, 3]
unique = list(dict.fromkeys(nums))
print(unique)             # [4, 1, 2, 3]
```

### Fast membership testing
```python
allowed = {"admin", "editor", "viewer"}
if "editor" in allowed:
    print("Access granted")
```

### Find common / different items between two groups
```python
python_students = {"Amit", "Riya", "Sam"}
java_students   = {"Riya", "Tom", "Sam"}

print(python_students & java_students)   # both courses    → {'Riya', 'Sam'}
print(python_students - java_students)   # only Python     → {'Amit'}
print(python_students | java_students)   # all students
print(python_students ^ java_students)   # only ONE course → {'Amit', 'Tom'}
```

---

## Set vs List vs Tuple vs Dict

| Feature | **List** | **Tuple** | **Set** | **Dict** |
|---|---|---|---|---|
| Syntax | `[ ]` | `( )` | `{ }` / `set()` | `{k: v}` |
| Ordered | Yes | Yes | No | Yes (insertion order, Python 3.7+) |
| Mutable | Yes | No | Yes | Yes |
| Duplicates | Yes | Yes | No | Keys: No / Values: Yes |
| Indexing | Yes | Yes | No | By key |
| Best for | Changing sequence | Fixed data | Unique items, math operations | Key → value mapping |

---

- [Back to section index](./README.md)
