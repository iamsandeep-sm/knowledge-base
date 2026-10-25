# Recursion - Revision Notes ~ sm

> **In one line:** **Recursion** means a function **calls itself** to solve a smaller version of the same problem. Every recursive function needs a **base case** (when to stop) and a **recursive case** (the call to itself on a smaller input).

## Table of Contents
- [What is Recursion?](#what-is-recursion)
- [The Two Parts](#the-two-parts)
- [Example: Countdown](#example-countdown)
- [Example: Factorial](#example-factorial)
- [How It Runs (The Call Stack)](#how-it-runs-the-call-stack)
- [More Examples](#more-examples)
- [Missing Base Case: RecursionError](#missing-base-case-recursionerror)
- [Fibonacci and Repeated Work](#fibonacci-and-repeated-work)
- [Recursion vs Loop](#recursion-vs-loop)
- [Common Mistakes](#common-mistakes)

---

## What is Recursion?

Imagine standing in a queue and wanting to know your position. You ask the person in front: "What is your position?" They ask the person in front of them, and so on. The first person says "1". Each person then adds 1 and passes the answer back. That is recursion: **a big question is answered by asking the same question on something smaller**, until the answer is obvious.

---

## The Two Parts

| Part | Meaning |
|---|---|
| **Base case** | The simplest input, answered directly **without** another call. It stops the recursion |
| **Recursive case** | The function calls itself with a **smaller** input, moving towards the base case |

If there is no base case, or the input never gets smaller, the function never stops.

---

## Example: Countdown

```python
def countdown(n):
    if n == 0:                 # base case
        print("Go!")
        return
    print(n)
    countdown(n - 1)           # recursive case

countdown(3)
# Output: 3
# Output: 2
# Output: 1
# Output: Go!
```

---

## Example: Factorial

`n! = n × (n-1) × ... × 1`, and `0! = 1`. Notice `n! = n × (n-1)!`: the problem contains a smaller copy of itself.

```python
def factorial(n):
    if n == 0:                         # base case
        return 1
    return n * factorial(n - 1)        # recursive case

print(factorial(5))
# Output: 120
```

---

## How It Runs (The Call Stack)

Each call waits for the call it made. Python keeps the waiting calls on a **call stack**. Trace `factorial(3)`:

| Step | What happens |
|---|---|
| 1 | `factorial(3)` needs `3 * factorial(2)`, so it waits |
| 2 | `factorial(2)` needs `2 * factorial(1)`, so it waits |
| 3 | `factorial(1)` needs `1 * factorial(0)`, so it waits |
| 4 | `factorial(0)` is the base case and returns `1` |
| 5 | `factorial(1)` returns `1 * 1 = 1` |
| 6 | `factorial(2)` returns `2 * 1 = 2` |
| 7 | `factorial(3)` returns `3 * 2 = 6` |

The calls go **down** to the base case, then the answers come **back up**. Each call has its own local variables (see the Scope note).

---

## More Examples

**Sum of numbers 1 to n**
```python
def sum_to(n):
    if n == 0:
        return 0
    return n + sum_to(n - 1)

print(sum_to(4))
# Output: 10
```

**Sum of a list**
```python
def list_sum(items):
    if not items:                      # empty list: base case
        return 0
    return items[0] + list_sum(items[1:])

print(list_sum([2, 4, 6]))
# Output: 12
```

**Reverse a string**
```python
def reverse(text):
    if len(text) <= 1:
        return text
    return reverse(text[1:]) + text[0]

print(reverse("python"))
# Output: nohtyp
```

**Palindrome check**
```python
def is_palindrome(text):
    if len(text) <= 1:
        return True
    if text[0] != text[-1]:
        return False
    return is_palindrome(text[1:-1])

print(is_palindrome("level"))
print(is_palindrome("hello"))
# Output: True
# Output: False
```

**Power**
```python
def power(base, exp):
    if exp == 0:
        return 1
    return base * power(base, exp - 1)

print(power(2, 5))
# Output: 32
```

---

## Missing Base Case: RecursionError

Python limits how deep the stack may grow (usually **1000** calls) to protect memory.

```python
def forever(n):
    return forever(n + 1)      # no base case

forever(1)
# Error:  RecursionError: maximum recursion depth exceeded
```
You can read the limit with `sys.getrecursionlimit()`:
```python
import sys
print(sys.getrecursionlimit())
# Output: 1000
```
Changing it with `sys.setrecursionlimit()` is possible, but it is rarely the right answer: use a loop or a better algorithm instead.

---

## Fibonacci and Repeated Work

Fibonacci numbers: `0, 1, 1, 2, 3, 5, 8, ...` where each is the sum of the two before.

```python
def fib(n):
    if n < 2:                          # base cases: fib(0)=0, fib(1)=1
        return n
    return fib(n - 1) + fib(n - 2)

print([fib(i) for i in range(10)])
# Output: [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```
This is clear but **slow** for big `n`, because it recalculates the same values many times (`fib(30)` makes over a million calls). The fix is to remember results (**memoization**). Python has a ready-made tool, `functools.cache` (Python 3.9+):

```python
from functools import cache

@cache
def fib(n):
    if n < 2:
        return n
    return fib(n - 1) + fib(n - 2)

print(fib(80))
# Output: 23416728348467685
```
The `@cache` line is a decorator (see the Decorators note). Each value is calculated once and then reused.

---

## Recursion vs Loop

| | Recursion | Loop |
|---|---|---|
| Idea | Function calls itself | Repeats a block |
| Stops by | Base case | Loop condition / items finish |
| Memory | Each call uses stack space; depth is limited | Constant |
| Speed | Slower (function-call overhead) | Faster |
| Best for | Naturally nested problems: folders inside folders, trees, nested lists, divide and conquer | Simple counting and repeating |

Every recursion can be rewritten as a loop:
```python
def factorial_loop(n):
    result = 1
    for i in range(2, n + 1):
        result *= i
    return result

print(factorial_loop(5))
# Output: 120
```
Python does **not** optimise deep recursion, so for simple repetition a loop is the better choice.

---

## Common Mistakes

| Mistake | Result | Fix |
|---|---|---|
| No base case | `RecursionError` | Always write the stopping condition first |
| Input does not shrink (`f(n)` instead of `f(n - 1)`) | `RecursionError` | Move towards the base case |
| Forgetting `return` on the recursive call (`factorial(n - 1)` alone) | Function returns `None` | `return n * factorial(n - 1)` |
| Base case that skips a value (`n == 1` when `0` can be passed) | `RecursionError` for 0 | Cover every possible input |
| Using recursion for very deep inputs | `RecursionError` | Use a loop |

---

- [Back to section index](./README.md)
