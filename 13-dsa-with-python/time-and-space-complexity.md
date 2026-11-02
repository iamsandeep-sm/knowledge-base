# Time and Space Complexity - Revision Notes ~ sm

> **In one line:** **Complexity** describes **how the work an algorithm does (time) and the extra memory it needs (space) grow as the input size `n` grows**. It is written with **Big-O notation** such as `O(1)`, `O(log n)`, `O(n)` and `O(n²)`, and it lets you compare algorithms without a stopwatch.

## Table of Contents
- [Why Complexity?](#why-complexity)
- [Count Steps, Not Seconds](#count-steps-not-seconds)
- [Big-O Notation](#big-o-notation)
- [The Common Growth Rates](#the-common-growth-rates)
- [How to Work Out Big-O](#how-to-work-out-big-o)
- [Examples, One by One](#examples-one-by-one)
- [Best, Average and Worst Case](#best-average-and-worst-case)
- [Amortized Cost](#amortized-cost)
- [Space Complexity](#space-complexity)
- [Recursion and Complexity](#recursion-and-complexity)
- [Cost of Python's Built-in Operations](#cost-of-pythons-built-in-operations)
- [Measuring Real Running Time](#measuring-real-running-time)
- [What Size of n Is Realistic?](#what-size-of-n-is-realistic)
- [Common Mistakes](#common-mistakes)

---

## Why Complexity?

Two programs can give the **same answer** and still be very different: one finishes in a blink, the other runs for hours once the input gets big.

Imagine finding a name in a phone book of 1,000,000 pages:
- **Page by page from the start** might need 1,000,000 looks.
- **Open the middle, then the correct half, repeat** needs about 20 looks.

Both work. Only one still works when the book gets 1000 times bigger. Complexity gives that difference a name, so you can choose well **before** writing the code.

---

## Count Steps, Not Seconds

A stopwatch depends on the computer, the Python version and what else is running. Instead, count **how many basic steps** the algorithm takes for an input of size `n`.

```python
def count_steps_linear(items, target):
    steps = 0
    for item in items:
        steps += 1
        if item == target:
            break
    return steps

print(count_steps_linear(list(range(10)), 9))
print(count_steps_linear(list(range(1000)), 999))
# Output: 10
# Output: 1000
```
100 times more data meant 100 times more steps: the work grows **in step with `n`**. That is what we describe.

---

## Big-O Notation

**Big-O** gives an **upper bound on the growth** of the work as `n` becomes large. We keep only the part that dominates.

> `3n² + 5n + 20` steps is written **O(n²)**.

Why? For `n = 1000` the pieces are `3,000,000`, `5,000` and `20`. The `n²` part is nearly all of it, and it decides how the algorithm scales.

```python
for n in (10, 100, 1000):
    exact = 3 * n * n + 5 * n + 20
    print(n, exact, 3 * n * n)
# Output: 10 370 300
# Output: 100 30520 30000
# Output: 1000 3005020 3000000
```
Big-O answers "**how does it grow?**", not "**how many seconds?**".

---

## The Common Growth Rates

From the slowest growth (best) to the fastest growth (worst):

| Big-O | Name | Typical example |
|---|---|---|
| `O(1)` | **Constant** | Index a list `items[5]`; dictionary lookup; push on a stack |
| `O(log n)` | **Logarithmic** | Binary search; walking down a balanced tree |
| `O(n)` | **Linear** | One loop over the data; `sum`, `max`, `x in list` |
| `O(n log n)` | **Linearithmic** | Good sorting (merge sort, `sorted()`) |
| `O(n²)` | **Quadratic** | Two nested loops over the data; bubble sort |
| `O(n³)` | **Cubic** | Three nested loops; naive matrix multiplication |
| `O(2ⁿ)` | **Exponential** | All subsets; naive recursive Fibonacci |
| `O(n!)` | **Factorial** | All permutations; brute-force travelling salesman |

How fast each one grows (approximate number of steps):

```python
import math

print(f"{'n':>8} {'log n':>8} {'n log n':>12} {'n^2':>14}")
for n in (10, 100, 1000, 10000):
    log_n = math.log2(n)
    print(f"{n:>8} {log_n:>8.1f} {n * log_n:>12.0f} {n * n:>14}")
# Output:        n    log n      n log n            n^2
# Output:       10      3.3           33            100
# Output:      100      6.6          664          10000
# Output:     1000     10.0         9966        1000000
# Output:    10000     13.3       132877      100000000
```
Look at `n = 10000`: `log n` is only 13 steps, `n log n` is about 133 thousand, but `n²` is **100 million**. And `2ⁿ` for just `n = 100` already has 31 digits:
```python
print(len(str(2 ** 100)), len(str(math.factorial(25))))
# Output: 31 26
```
So `O(2ⁿ)` and `O(n!)` are useful only for tiny inputs.

---

## How to Work Out Big-O

**Rule 1: Drop constants.** `O(2n)` and `O(n/2)` are both `O(n)`. Doing a loop twice is still linear.

**Rule 2: Keep the biggest term.** `O(n² + n + 1)` is `O(n²)`.

**Rule 3: Steps one after another ADD; steps inside steps MULTIPLY.**
```python
def sequential(items):
    for x in items:          # n steps
        pass
    for x in items:          # + n steps   -> O(n + n) = O(n)
        pass

def nested(items):
    for x in items:          # n times
        for y in items:      #   n steps each   -> O(n * n) = O(n^2)
            pass
```

**Rule 4: Different inputs get different letters.** Looping over two separate lists of sizes `a` and `b` is `O(a + b)` (or `O(a * b)` if nested), **not** `O(n)`.

**Rule 5: A loop that halves (or doubles) each time is logarithmic.**
```python
def halvings(n):
    steps = 0
    while n > 1:
        n //= 2
        steps += 1
    return steps

print(halvings(8), halvings(1000), halvings(1_000_000))
# Output: 3 9 19
```
A million becomes 1 in only 19 halvings: that is `O(log n)`.

**Rule 6: Count the worst-looking place.** A simple statement is `O(1)`, but a function call costs whatever that function costs, and so do operations like slicing, `in`, `sorted()` and `list.insert(0, x)`.

---

## Examples, One by One

**O(1): constant**
```python
def first_item(items):
    return items[0]

print(first_item([5, 6, 7]), first_item(list(range(10 ** 6))))
# Output: 5 0
```
The work does not depend on the size of the list.

**O(log n): halving**
```python
def binary_search_steps(items, target):
    low, high = 0, len(items) - 1
    steps = 0
    while low <= high:
        steps += 1
        mid = (low + high) // 2
        if items[mid] == target:
            return steps
        if items[mid] < target:
            low = mid + 1
        else:
            high = mid - 1
    return steps

big = list(range(1_000_000))
print(binary_search_steps(big, 999_999))
print(binary_search_steps(big, -1))
# Output: 20
# Output: 19
```
A million items need at most about 20 steps (a missing value can end a little earlier).

**O(n): one pass**
```python
def maximum(items):
    best = items[0]
    for item in items:
        if item > best:
            best = item
    return best

print(maximum([3, 9, 2, 7]))
# Output: 9
```

**O(n log n): sort, then walk**
```python
def has_duplicate(items):
    ordered = sorted(items)                # O(n log n)
    for a, b in zip(ordered, ordered[1:]):  # O(n)
        if a == b:
            return True
    return False

print(has_duplicate([4, 2, 7, 2]), has_duplicate([1, 2, 3]))
# Output: True False
```
The total is `O(n log n + n)`, which is `O(n log n)`.

**O(n²): all pairs**
```python
def has_duplicate_slow(items):
    for i in range(len(items)):
        for j in range(i + 1, len(items)):
            if items[i] == items[j]:
                return True
    return False

print(has_duplicate_slow([4, 2, 7, 2]), has_duplicate_slow([1, 2, 3]))
# Output: True False
```
The same job in `O(n)` with a set:
```python
def has_duplicate_fast(items):
    seen = set()
    for item in items:
        if item in seen:           # O(1) on average
            return True
        seen.add(item)
    return False

print(has_duplicate_fast([4, 2, 7, 2]), has_duplicate_fast([1, 2, 3]))
# Output: True False
```
This is the most common improvement in everyday code: **replace a nested loop by a dictionary or set lookup** (a trade of memory for time).

**O(2ⁿ): two calls per call**
```python
calls = 0

def fib(n):
    global calls
    calls += 1
    return n if n < 2 else fib(n - 1) + fib(n - 2)

fib(20)
print(calls)
calls = 0
fib(25)
print(calls)
# Output: 21891
# Output: 242785
```
Adding 5 to `n` multiplied the calls by about 11. With memoization (`functools.cache`) the same function is `O(n)`.

---

## Best, Average and Worst Case

The same algorithm can behave differently depending on the input.

| Case | Meaning | Linear search for a target |
|---|---|---|
| **Best** | The luckiest input | Target is first: `O(1)` |
| **Average** | A typical input | Somewhere in the middle: `O(n)` |
| **Worst** | The unluckiest input | Target is last or missing: `O(n)` |

```python
def search_steps(items, target):
    for steps, item in enumerate(items, start=1):
        if item == target:
            return steps
    return len(items)

data = list(range(100))
print(search_steps(data, 0))
print(search_steps(data, 49))
print(search_steps(data, 1000))
# Output: 1
# Output: 50
# Output: 100
```
Unless told otherwise, "the complexity" means the **worst case**: a guarantee you can rely on. (Quick sort is the famous case where the average, `O(n log n)`, and worst, `O(n²)`, differ.)

Big-O is an **upper bound**. You may also meet **Ω (omega)** for a lower bound and **Θ (theta)** for a tight bound (both). In everyday talk and in interviews people say "Big-O" and mean the tight worst case.

---

## Amortized Cost

Some operations are cheap **almost always** and expensive **rarely**. The **amortized** cost is the average cost per operation over a long series.

Python's `list.append` is the standard example. A list keeps spare room; when it is full, Python allocates a **bigger** block and copies everything, which costs `O(n)`. But that happens rarely, because each time the room grows by a **fraction of the current size** (CPython's exact growth rule is an implementation detail). A simulation with a simple "double the room when full" rule shows the idea:

```python
capacity = 1
size = 0
copies = 0

for _ in range(1000):
    if size == capacity:
        copies += size          # copy all items into the new, larger block
        capacity *= 2
    size += 1

print(size, copies)
# Output: 1000 1023
```
For 1000 appends only about 1000 extra copy steps happened in total: **about one extra step per append**. So `append` is **amortized `O(1)`**, even though a single call can occasionally be slow.

---

## Space Complexity

**Space complexity** is the **extra memory** the algorithm needs, apart from the input itself (the "auxiliary space").

| Space | Example |
|---|---|
| `O(1)` | A few variables: `total = 0`; reversing a list **in place** |
| `O(n)` | Building a new list of the same size; a set of seen items; recursion `n` levels deep |
| `O(n²)` | An `n × n` table |

```python
def reverse_in_place(items):             # O(1) extra space
    left, right = 0, len(items) - 1
    while left < right:
        items[left], items[right] = items[right], items[left]
        left += 1
        right -= 1

def reversed_copy(items):                # O(n) extra space
    return items[::-1]

numbers = [1, 2, 3, 4]
reverse_in_place(numbers)
print(numbers, reversed_copy(numbers))
# Output: [4, 3, 2, 1] [1, 2, 3, 4]
```
**Time and space often trade against each other.** The set in `has_duplicate_fast` uses `O(n)` space to save time; memoization stores results to avoid recomputing; a lookup table speeds up queries.

Remember the hidden memory cost of **recursion**: every call that is still waiting sits on the call stack (next section).

---

## Recursion and Complexity

A recursive function uses stack space equal to its **depth**.

```python
import sys

def depth(n):
    return 0 if n == 0 else 1 + depth(n - 1)

print(depth(500))
print(sys.getrecursionlimit())
# Output: 500
# Output: 1000
```
```python
def depth(n):
    return 0 if n == 0 else 1 + depth(n - 1)

depth(10_000)
# Error: RecursionError: maximum recursion depth exceeded
```
`depth(n)` is `O(n)` time **and** `O(n)` space. Python's default limit is about 1000 nested calls (it does not do tail-call optimisation), so very deep recursion is rewritten as a loop.

**Reading a recursive algorithm's cost**

| Pattern | Cost | Example |
|---|---|---|
| One call, input shrinks by 1 | `O(n)` | `factorial(n)` |
| One call, input **halves** | `O(log n)` | Binary search (recursive) |
| Two calls, each on **half** the input, plus `O(n)` work | `O(n log n)` | Merge sort |
| Two calls, each on `n - 1` | `O(2ⁿ)` | Naive Fibonacci |
| A call for each of `n` choices, `n - 1` left, ... | `O(n!)` | Permutations |

Picture the recursion as a **tree of calls**: the total work is the work per call times the number of calls, and the stack space is the **height** of the tree.

---

## Cost of Python's Built-in Operations

(Average cases for CPython.)

**list**

| Operation | Cost | Note |
|---|---|---|
| `items[i]`, `items[i] = x` | `O(1)` | |
| `len(items)` | `O(1)` | |
| `items.append(x)`, `items.pop()` | `O(1)` amortized | At the **end** |
| `items.insert(i, x)`, `items.pop(i)`, `del items[i]` | `O(n)` | Shifts the items after `i` (`i = 0` is the worst) |
| `x in items`, `items.index(x)`, `items.remove(x)`, `items.count(x)` | `O(n)` | Linear search |
| `items[a:b]` | `O(b - a)` | Makes a **copy** |
| `items + other`, `items.copy()` | `O(n)` | |
| `items.extend(other)` | `O(len(other))` | |
| `items.sort()`, `sorted(items)` | `O(n log n)` | |
| `min(items)`, `max(items)`, `sum(items)` | `O(n)` | |

**dict and set**

| Operation | Average | Worst |
|---|---|---|
| `d[key]`, `d[key] = v`, `key in d`, `del d[key]` | `O(1)` | `O(n)` (many hash collisions) |
| `s.add(x)`, `x in s`, `s.remove(x)` | `O(1)` | `O(n)` |
| iterate over all | `O(n)` | |
| `a \| b`, `a & b` (sets) | `O(len(a) + len(b))` (about) | |

**str** (strings are immutable)

| Operation | Cost |
|---|---|
| `s[i]`, `len(s)` | `O(1)` |
| `s + t` | `O(len(s) + len(t))` (a new string) |
| `s * k`, `s[a:b]`, `s.upper()`, `s.split()` | `O(n)` |
| `sub in s` | `O(n)` or more (searching) |
| `"".join(parts)` | `O(total length)` |

Building a string with `s += piece` in a loop **can** become `O(n²)`, because each `+` may copy the whole string. Collect the pieces in a list and `"".join()` them.

**collections.deque**: `append`, `appendleft`, `pop`, `popleft` are `O(1)`; indexing in the middle is `O(n)`.

**heapq**: `heappush` and `heappop` are `O(log n)`, `heapify` is `O(n)`, and looking at the smallest `heap[0]` is `O(1)`.

**bisect**: `bisect_left` is `O(log n)` on a sorted list. `insort` is `O(n)` overall, because inserting shifts items.

```python
# a list used as a queue is slow at the front; a deque is not
from collections import deque

queue = deque([1, 2, 3])
queue.append(4)
print(queue.popleft(), list(queue))
# Output: 1 [2, 3, 4]
```

---

## Measuring Real Running Time

Big-O predicts the **trend**. To check it, time the code with `time.perf_counter()` or `timeit` and see how the time changes when `n` doubles. The numbers differ on every computer, so compare **ratios**, not seconds, and use a repeat count.

```python
import timeit

def linear(n):
    return sum(1 for _ in range(n))

small = timeit.timeit(lambda: linear(10_000), number=20)
large = timeit.timeit(lambda: linear(100_000), number=20)
print(large > small)
# Output: True
```
If the input grows 10 times and the time grows about 10 times, it is linear; about 100 times, quadratic; barely changes, constant or logarithmic.

Big-O ignores constants, so a "worse" algorithm can be faster for **small** `n`, and Python's built-in functions (written in C) are usually much faster than the same loop written in Python even when the Big-O is the same. Use Big-O to choose the **approach**, then measure if speed really matters.

---

## What Size of n Is Realistic?

A rough rule of thumb: **plain Python does about 10 million simple operations per second** (this varies a lot with the machine and the operations). Use it only as an estimate.

| Input size `n` | Algorithms that are usually fine |
|---|---|
| up to about 10 | `O(n!)` |
| up to about 20 | `O(2ⁿ)` |
| up to about 500 | `O(n³)` |
| up to about 5,000 | `O(n²)` |
| up to about 100,000 to 1,000,000 | `O(n log n)` |
| 1,000,000 or more | `O(n)`, `O(log n)`, `O(1)` |

If a problem says `n ≤ 100,000` and your idea is `O(n²)` (10 billion steps), look for a better idea (a dictionary, sorting, two pointers, a heap or binary search).

---

## Common Mistakes

| Mistake | What is wrong | Fix |
|---|---|---|
| Thinking `O(2n)` is slower than `O(n)` | Constants are dropped | Both are `O(n)` |
| Writing `O(n² + n)` | The smaller term does not matter | `O(n²)` |
| Calling two separate loops `O(n²)` | Loops one after another **add** | `O(n)` |
| Using `n` for every input | Different inputs have different sizes | `O(a + b)`, `O(rows * cols)` |
| Treating `x in my_list` as `O(1)` | It scans the list | Use a `set` or `dict` for fast membership |
| Forgetting that slicing copies | `items[1:]` in a loop makes `O(n²)` | Use indexes or `deque` |
| `s += "x"` in a long loop | May copy the string each time | `"".join(parts)` |
| `list.pop(0)` or `insert(0, x)` in a loop | Each is `O(n)` | `collections.deque` |
| Forgetting hidden loops | `sum`, `max`, `in`, `sorted`, `.count()` are all loops | Count them |
| Ignoring recursion's stack space | `O(n)` depth uses `O(n)` memory and may hit `RecursionError` | Iterate, or limit the depth |
| Believing Big-O is the running time in seconds | It is the **growth**, not the speed | Measure real code when it matters |
| Optimising too early | Complex code for no real gain | Make it correct, clear, then fast **if needed** |
| Quoting best case as if it were the guarantee | Misleading | Say worst case, and mention the average when it differs |

---

- [Back to section index](./README.md)
