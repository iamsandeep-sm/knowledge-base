# Lambda Functions - Revision Notes ~ sm

> **In one line:** A **lambda** is a small, **nameless** function written in one line: `lambda parameters: expression`. It automatically returns the value of that one expression.

## Table of Contents
- [What is a Lambda?](#what-is-a-lambda)
- [Lambda vs def](#lambda-vs-def)
- [Syntax and Examples](#syntax-and-examples)
- [Lambda with if else](#lambda-with-if-else)
- [Where Lambdas are Used](#where-lambdas-are-used)
- [Lambda with sorted, min and max](#lambda-with-sorted-min-and-max)
- [Lambda with map and filter](#lambda-with-map-and-filter)
- [Limits of a Lambda](#limits-of-a-lambda)
- [The Loop Trap](#the-loop-trap)
- [When to Use What](#when-to-use-what)

---

## What is a Lambda?

Sometimes you need a tiny function just once, for example "give me the second item of a pair". Writing a full `def` with a name feels too heavy. A lambda is a quick, **anonymous** (no name) function.

```python
def square(n):
    return n * n

square2 = lambda n: n * n

print(square(5))
print(square2(5))
# Output: 25
# Output: 25
```
Both do the same job.

---

## Lambda vs def

| | `def` function | `lambda` |
|---|---|---|
| Name | Has a name | Anonymous (can be stored in a variable) |
| Body | Any number of statements | **One expression only** |
| `return` | You write it | Automatic: the expression's value is returned |
| Docstring | Possible | No |
| Best for | Normal, reusable code | Short throw-away functions |

---

## Syntax and Examples

```
lambda parameters: expression
```
No brackets around the parameters, no `return`, no colon-then-newline.

```python
add = lambda a, b: a + b
print(add(2, 3))

greet = lambda: "Hello"                    # no parameters
print(greet())

power = lambda base, exp=2: base ** exp    # default value
print(power(4))
print(power(2, 5))

total = lambda *nums: sum(nums)            # *args works too
print(total(1, 2, 3))

print((lambda x: x + 1)(10))               # call at once
# Output: 5
# Output: Hello
# Output: 16
# Output: 32
# Output: 6
# Output: 11
```

---

## Lambda with if else

A lambda cannot hold an `if` **statement**, but it can use the one-line **conditional expression**: `value_if_true if condition else value_if_false`.

```python
check = lambda n: "even" if n % 2 == 0 else "odd"
print(check(4))
print(check(7))
# Output: even
# Output: odd
```

---

## Where Lambdas are Used

The main use is passing a small function **into** another function (one that accepts a function as an argument, like `sorted`, `map`, `filter`, `max`, `min`).

---

## Lambda with sorted, min and max

The `key` argument says **what to compare**.

```python
words = ["banana", "kiwi", "apple", "fig"]

print(sorted(words, key=lambda w: len(w)))
print(max(words, key=lambda w: len(w)))
print(min(words, key=lambda w: len(w)))
# Output: ['fig', 'kiwi', 'apple', 'banana']
# Output: banana
# Output: fig
```
Sorting a list of pairs by the second value:
```python
students = [("Riya", 82), ("Amit", 91), ("Sam", 67)]
print(sorted(students, key=lambda s: s[1]))
print(sorted(students, key=lambda s: s[1], reverse=True))
# Output: [('Sam', 67), ('Riya', 82), ('Amit', 91)]
# Output: [('Amit', 91), ('Riya', 82), ('Sam', 67)]
```
Sorting a list of dictionaries:
```python
people = [{"name": "Riya", "age": 20}, {"name": "Amit", "age": 18}]
print(sorted(people, key=lambda p: p["age"]))
# Output: [{'name': 'Amit', 'age': 18}, {'name': 'Riya', 'age': 20}]
```
If two items have the same key, `sorted` keeps their original order (it is **stable**).

---

## Lambda with map and filter

`map(function, iterable)` applies the function to every item. `filter(function, iterable)` keeps the items where the function returns `True`. Both return an **iterator**, so wrap them in `list()` to see the result.

```python
nums = [1, 2, 3, 4, 5, 6]

print(list(map(lambda n: n * 2, nums)))
print(list(filter(lambda n: n % 2 == 0, nums)))
# Output: [2, 4, 6, 8, 10, 12]
# Output: [2, 4, 6]
```
The same thing as a list comprehension (usually clearer):
```python
print([n * 2 for n in nums])
print([n for n in nums if n % 2 == 0])
# Output: [2, 4, 6, 8, 10, 12]
# Output: [2, 4, 6]
```

---

## Limits of a Lambda

- **Only one expression.** No assignment statements, loops, `try`, `return`, or `print` statements in the usual sense (calling `print(...)` is allowed because it is an expression, but it is poor style).
- Harder to debug: error messages show `<lambda>` instead of a name.
- A long lambda is hard to read. If it needs more than a short line, write a `def`.

PEP 8 advises **not** to assign a lambda to a name (`square = lambda n: n * n`); write `def square(n):` instead. Use lambdas directly as an argument.

---

## The Loop Trap

A lambda looks up outer variables **when it is called**, not when it is created.

```python
funcs = [lambda: i for i in range(3)]
print([f() for f in funcs])
# Output: [2, 2, 2]
```
All three see the final value of `i`. Fix: capture the value with a default argument.
```python
funcs = [lambda i=i: i for i in range(3)]
print([f() for f in funcs])
# Output: [0, 1, 2]
```

---

## When to Use What

| Situation | Use |
|---|---|
| Small throw-away key for `sorted`, `min`, `max` | `lambda` |
| A function you will use in several places | `def` |
| Needs several lines or a docstring | `def` |
| Transforming or filtering a list | List comprehension (often clearer than `map` / `filter` with a lambda) |

---

- [Back to section index](./README.md)
