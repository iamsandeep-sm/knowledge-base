# Generator Expressions - Revision Notes ~ sm

> **In one line:** A **generator expression** looks like a list comprehension but uses **round brackets** `( )`. It does not build the whole list; it produces **one item at a time, only when asked** (lazy), so it saves memory.

## Table of Contents
- [Syntax](#syntax)
- [List Comprehension vs Generator Expression](#list-comprehension-vs-generator-expression)
- [Getting Values Out](#getting-values-out)
- [A Generator Can Be Used Only Once](#a-generator-can-be-used-only-once)
- [Passing to Functions (sum, max, any ...)](#passing-to-functions-sum-max-any-)
- [Memory Saving](#memory-saving)
- [Lazy Evaluation](#lazy-evaluation)
- [No Tuple Comprehension](#no-tuple-comprehension)
- [Generator Expression vs Generator Function](#generator-expression-vs-generator-function)
- [When to Use What](#when-to-use-what)
- [Common Mistakes](#common-mistakes)

---

## Syntax

```
(expression for item in iterable if condition)
```
Only the brackets differ from a list comprehension.

---

## List Comprehension vs Generator Expression

```python
squares_list = [x ** 2 for x in range(5)]
squares_gen = (x ** 2 for x in range(5))

print(squares_list)
print(type(squares_gen))
# Output: [0, 1, 4, 9, 16]
# Output: <class 'generator'>
```

| | List comprehension `[ ]` | Generator expression `( )` |
|---|---|---|
| Result | A real list, built **at once** | A generator object, items made **one by one** |
| Memory | Stores every item | Stores almost nothing |
| Can be indexed (`g[0]`)? | Yes | **No** |
| Can be reused? | Yes | **No** (one pass only) |
| `len()` works? | Yes | **No** |

Printing a generator does not show its items, only something like `<generator object <genexpr> at 0x...>`.

---

## Getting Values Out

**1. With a `for` loop**
```python
gen = (x * 10 for x in range(3))
for value in gen:
    print(value)
# Output: 0
# Output: 10
# Output: 20
```

**2. With `next()`** (one value per call)
```python
gen = (x * 10 for x in range(3))
print(next(gen))
print(next(gen))
print(next(gen))
# Output: 0
# Output: 10
# Output: 20
```
Another `next(gen)` raises `StopIteration`, meaning "no more values".

**3. By converting it**
```python
gen = (x * 10 for x in range(3))
print(list(gen))
# Output: [0, 10, 20]
```

---

## A Generator Can Be Used Only Once

After it is used up (**exhausted**), it is empty.

```python
gen = (n for n in range(3))
print(list(gen))
print(list(gen))
# Output: [0, 1, 2]
# Output: []
```
To use the values again, create the generator again or store the values in a list.

---

## Passing to Functions (sum, max, any ...)

When a generator expression is the **only argument**, the extra brackets can be left out.

```python
nums = [1, 2, 3, 4, 5]

print(sum(x ** 2 for x in nums))
print(max(len(w) for w in ["a", "abc", "ab"]))
print(any(n > 4 for n in nums))
print(all(n > 0 for n in nums))
print(", ".join(str(n) for n in nums))
# Output: 55
# Output: 3
# Output: True
# Output: True
# Output: 1, 2, 3, 4, 5
```
`sum([x ** 2 for x in nums])` works too, but builds a temporary list first. The generator form does not.

---

## Memory Saving

```python
import sys

big_list = [n for n in range(100_000)]
big_gen = (n for n in range(100_000))

print(sys.getsizeof(big_list) > sys.getsizeof(big_gen))
# Output: True
```
The list holds 100 000 items. The generator holds only the recipe, so its size stays small, **however large the range is**. The exact byte counts depend on the Python version, so only the comparison is shown.

```python
total = sum(n for n in range(10_000_000))
print(total)
# Output: 49999995000000
```
This adds ten million numbers without ever storing them all.

---

## Lazy Evaluation

Nothing is calculated until a value is **asked for**.

```python
def noisy(n):
    print("working on", n)
    return n * 2

gen = (noisy(n) for n in range(3))
print("generator created")
print(next(gen))
# Output: generator created
# Output: working on 0
# Output: 0
```
The function ran only when `next` asked for the first value. Because of this, a generator can also describe a very long or even endless stream of values, and `any()` / `all()` stop early as soon as the answer is known.

```python
print(any(n > 2 for n in range(1_000_000_000)))
# Output: True
```
This returns immediately after reaching `3`, not after a billion steps.

> Because values are made later, a generator sees the data **as it is at that moment**. Do not change a list while a generator based on it is still unfinished.

---

## No Tuple Comprehension

```python
t = (x for x in range(3))
print(type(t))
print(tuple(x for x in range(3)))
# Output: <class 'generator'>
# Output: (0, 1, 2)
```
Round brackets make a **generator**, not a tuple. Use `tuple(...)` to get a tuple.

---

## Generator Expression vs Generator Function

A generator expression is a short form. For more complex logic, write a **generator function** that uses `yield`:

```python
def squares(n):
    for x in range(n):
        yield x ** 2

print(list(squares(4)))
# Output: [0, 1, 4, 9]
```
`yield` gives a value and **pauses** the function until the next value is asked. Both create the same kind of generator object. Generator functions and iterators are covered in the Generators and Iterators notes in the Advanced Python section.

---

## When to Use What

| Situation | Use |
|---|---|
| Need the whole result, index it, reuse it, or know its length | List comprehension |
| Feeding the result straight into `sum`, `max`, `min`, `any`, `all`, `join`, `sorted` | Generator expression |
| Very large or unknown-size data, read once | Generator expression |
| Need unique items | Set comprehension |
| Need key-value pairs | Dictionary comprehension |

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Printing a generator and expecting items | You see `<generator object ...>` | `list(gen)` or loop over it |
| `gen[0]` | `TypeError: 'generator' object is not subscriptable` | Convert to a list first |
| `len(gen)` | `TypeError: object of type 'generator' has no len()` | Count while looping, or use a list |
| Using the generator twice | The second time it is empty | Recreate it or store a list |
| Thinking `( )` makes a tuple | It makes a generator | `tuple(...)` |
| `next(gen)` past the end | `StopIteration` | Use a `for` loop, or `next(gen, default)` |

```python
gen = (x for x in range(2))
print(next(gen, "done"))
print(next(gen, "done"))
print(next(gen, "done"))
# Output: 0
# Output: 1
# Output: done
```

---

- [Back to section index](./README.md)
