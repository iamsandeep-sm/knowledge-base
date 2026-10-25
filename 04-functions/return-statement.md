# Return Statement - Revision Notes ~ sm

> **In one line:** `return` **ends** a function and **sends a value back** to the place where the function was called. If a function has no `return` (or a bare `return`), it gives back `None`.

## Table of Contents
- [What does return do?](#what-does-return-do)
- [return vs print](#return-vs-print)
- [Code After return Never Runs](#code-after-return-never-runs)
- [Functions Without return (None)](#functions-without-return-none)
- [Returning Multiple Values](#returning-multiple-values)
- [Early return](#early-return)
- [Returning True or False](#returning-true-or-false)
- [return Inside a Loop](#return-inside-a-loop)
- [Returning Other Things](#returning-other-things)
- [Common Mistakes](#common-mistakes)

---

## What does return do?

```python
def add(a, b):
    return a + b

result = add(2, 3)      # the call is replaced by the returned value 5
print(result)
print(add(1, 1) * 10)
# Output: 5
# Output: 20
```
Think of a function call as a question and `return` as the answer. `add(2, 3)` "becomes" `5` wherever it is written, so you can store it, print it, or use it in a calculation.

---

## return vs print

| | `print()` | `return` |
|---|---|---|
| What it does | **Shows** a value on the screen | **Sends** a value back to the caller |
| Can the result be reused? | No | Yes |
| Ends the function? | No | **Yes** |

```python
def show_double(n):
    print(n * 2)          # only displays

def get_double(n):
    return n * 2          # gives the value back

a = show_double(4)        # prints 8
b = get_double(4)         # prints nothing
print(a)
print(b)
# Output: 8
# Output: None
# Output: 8
```
`a` is `None` because `show_double` returned nothing. Prefer `return` in functions and print the result **outside**.

---

## Code After return Never Runs

`return` leaves the function immediately.

```python
def test():
    print("before")
    return "done"
    print("after")        # never runs

print(test())
# Output: before
# Output: done
```

---

## Functions Without return (None)

If there is no `return`, Python returns the special value `None` ("nothing").

```python
def hello():
    print("hi")

x = hello()
print(x)
print(type(x))
# Output: hi
# Output: None
# Output: <class 'NoneType'>
```
A bare `return` (with no value) also gives `None`. It is used to stop a function early.

---

## Returning Multiple Values

Separate the values with commas. Python packs them into **one tuple**.

```python
def min_max(numbers):
    return min(numbers), max(numbers)

result = min_max([4, 9, 2])
print(result)
print(type(result))

low, high = min_max([4, 9, 2])      # unpack into two names
print(low, high)
# Output: (2, 9)
# Output: <class 'tuple'>
# Output: 2 9
```
Unpacking is covered in the Unpacking note.

---

## Early return

Use `return` to stop as soon as the answer is known. This avoids deep nesting.

```python
def grade(marks):
    if marks < 0 or marks > 100:
        return "Invalid"
    if marks >= 40:
        return "Pass"
    return "Fail"

print(grade(75))
print(grade(20))
print(grade(150))
# Output: Pass
# Output: Fail
# Output: Invalid
```
Notice there is no `else`: once a `return` runs, the later lines are skipped.

---

## Returning True or False

Functions that answer a yes/no question usually return a boolean. Names often start with `is_` or `has_`.

```python
def is_even(n):
    return n % 2 == 0

print(is_even(4))
print(is_even(7))
# Output: True
# Output: False
```
`return n % 2 == 0` is shorter than `if ...: return True else: return False`.

---

## return Inside a Loop

`return` inside a loop ends the **whole function**, not just the loop (use `break` to leave only the loop).

```python
def first_negative(numbers):
    for n in numbers:
        if n < 0:
            return n          # leaves the function at once
    return None               # reached only if nothing was found

print(first_negative([5, 3, -2, -8]))
print(first_negative([1, 2]))
# Output: -2
# Output: None
```
A common mistake is putting `return` **inside** the loop when it should be after it:
```python
def total_wrong(numbers):
    total = 0
    for n in numbers:
        total += n
        return total          # returns after the FIRST item

def total_right(numbers):
    total = 0
    for n in numbers:
        total += n
    return total              # returns after the loop

print(total_wrong([1, 2, 3]))
print(total_right([1, 2, 3]))
# Output: 1
# Output: 6
```

---

## Returning Other Things

A function can return any object: a list, a dictionary, a string, even another function.

```python
def make_user(name, age):
    return {"name": name, "age": age}

print(make_user("Riya", 20))
# Output: {'name': 'Riya', 'age': 20}
```

```python
def make_multiplier(n):
    def multiply(x):
        return x * n
    return multiply           # returns a function

times3 = make_multiplier(3)
print(times3(10))
# Output: 30
```
Returning functions is explained in the Closures note.

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Using `print` instead of `return` | The caller gets `None` | `return` the value |
| Code written after `return` | It never runs | Put it before `return` |
| `return` inside the loop by mistake | Returns after the first item | Dedent it so it is after the loop |
| Using the result of a function that returns `None` | `TypeError` such as `unsupported operand type(s) for +: 'NoneType' and 'int'` | Make sure the function returns a value |
| Writing `return a, b` and expecting two separate values | You get one tuple | Unpack: `x, y = f()` |

```python
def forgot(n):
    n * 2                 # calculated, thrown away

print(forgot(5))
# Output: None
```

---

- [Back to section index](./README.md)
