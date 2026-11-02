# Recursion and Backtracking - Revision Notes ~ sm

> **In one line:** **Recursion** is a function that solves a problem by calling **itself on a smaller version** of the same problem, until it reaches a **base case** it can answer directly. **Backtracking** is recursion that **tries a choice, explores it, and undoes it** if it leads nowhere.

## Table of Contents
- [The Idea](#the-idea)
- [The Two Parts of Every Recursive Function](#the-two-parts-of-every-recursive-function)
- [The Call Stack](#the-call-stack)
- [Classic Examples](#classic-examples)
- [Thinking Recursively](#thinking-recursively)
- [Recursion on Lists and Strings](#recursion-on-lists-and-strings)
- [Repeated Work and Memoization](#repeated-work-and-memoization)
- [Recursion Limit](#recursion-limit)
- [Recursion vs Loops](#recursion-vs-loops)
- [Divide and Conquer](#divide-and-conquer)
- [What Is Backtracking?](#what-is-backtracking)
- [Backtracking Template](#backtracking-template)
- [Subsets](#subsets)
- [Permutations](#permutations)
- [Combinations](#combinations)
- [Combination Sum and Pruning](#combination-sum-and-pruning)
- [N-Queens](#n-queens)
- [Word Search in a Grid](#word-search-in-a-grid)
- [Tower of Hanoi](#tower-of-hanoi)
- [Complexity of Backtracking](#complexity-of-backtracking)
- [Common Mistakes](#common-mistakes)

---

## The Idea

Russian nesting dolls: to find the smallest doll, open the current doll and **do the same thing to the doll inside**. When there is no doll inside, you are done.

```text
sum_to(4) = 4 + sum_to(3)
                  sum_to(3) = 3 + sum_to(2)
                                  sum_to(2) = 2 + sum_to(1)
                                                  sum_to(1) = 1      <- base case
```
Each call handles **one small step** and trusts the recursive call to handle the rest.

---

## The Two Parts of Every Recursive Function

| Part | Job |
|---|---|
| **Base case** | The smallest input, answered **directly**, with no more recursion. It stops the chain. |
| **Recursive case** | Reduce the problem to a **smaller** one, call yourself, and use the result. |

```python
def sum_to(n):
    if n == 1:                  # base case
        return 1
    return n + sum_to(n - 1)    # recursive case: a smaller problem

print(sum_to(4), sum_to(100))
# Output: 10 5050
```
Two rules keep recursion safe:
1. There **must** be a base case.
2. Every recursive call must move **closer** to it. If it does not, the function never stops (a `RecursionError`).

---

## The Call Stack

Each call gets its own **frame** holding its own variables. Frames pile up until the base case, then the answers come back **in reverse order** as each frame finishes.

```python
def countdown(n):
    if n == 0:
        print("liftoff")
        return
    print("going down:", n)
    countdown(n - 1)
    print("coming back:", n)

countdown(3)
# Output: going down: 3
# Output: going down: 2
# Output: going down: 1
# Output: liftoff
# Output: coming back: 1
# Output: coming back: 2
# Output: coming back: 3
```
Anything **before** the recursive call runs on the way **down**; anything **after** runs on the way **back up**. (This is exactly why pre-order and post-order traversals differ.) Each frame costs memory, so recursion depth `n` means `O(n)` space.

---

## Classic Examples

**Factorial**: `n! = n * (n-1)!`, with `0! = 1`.
```python
def factorial(n):
    if n < 0:
        raise ValueError("n must be >= 0")
    if n <= 1:
        return 1
    return n * factorial(n - 1)

print(factorial(5), factorial(0), factorial(10))
# Output: 120 1 3628800
```

**Fibonacci**: each number is the sum of the previous two (`0, 1, 1, 2, 3, 5, 8, ...`).
```python
def fib(n):
    if n < 2:
        return n
    return fib(n - 1) + fib(n - 2)

print([fib(i) for i in range(10)])
# Output: [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```
This version is correct but very slow for large `n` (see [Repeated Work](#repeated-work-and-memoization)).

**Power**: `x**n` by repeated halving: `x**n = (x**(n//2))**2`, times `x` if `n` is odd. Only `O(log n)` calls.
```python
def power(x, n):
    if n == 0:
        return 1
    half = power(x, n // 2)
    return half * half * x if n % 2 else half * half

print(power(2, 10), power(3, 5), power(2, 0))
# Output: 1024 243 1
```

**Greatest common divisor** (Euclid)
```python
def gcd(a, b):
    return a if b == 0 else gcd(b, a % b)

print(gcd(48, 18), gcd(7, 5))
# Output: 6 1
```

**Sum of digits**
```python
def digit_sum(n):
    if n < 10:
        return n
    return n % 10 + digit_sum(n // 10)

print(digit_sum(12345))
# Output: 15
```

---

## Thinking Recursively

A reliable recipe:

1. **What is the smallest case**, and what is its answer? (base case)
2. **Suppose the function already works for a smaller input.** How would you use that answer to solve the current one?
3. Check that every call gets **smaller** and ends at a base case.

Do not trace every call in your head. **Trust the smaller call** (the "recursive leap of faith") and just write the one step that connects it to the current problem.

---

## Recursion on Lists and Strings

Reduce the problem by removing the **first item** (or the last).

```python
def total(items):
    if not items:
        return 0
    return items[0] + total(items[1:])

def reverse_string(text):
    if len(text) <= 1:
        return text
    return reverse_string(text[1:]) + text[0]

def is_palindrome(text):
    if len(text) <= 1:
        return True
    return text[0] == text[-1] and is_palindrome(text[1:-1])

def flatten(nested):
    flat = []
    for item in nested:
        if isinstance(item, list):
            flat.extend(flatten(item))     # a list inside a list: recurse
        else:
            flat.append(item)
    return flat

print(total([1, 2, 3, 4]))
print(reverse_string("hello"))
print(is_palindrome("racecar"), is_palindrome("python"))
print(flatten([1, [2, [3, 4]], 5, [[6]]]))
# Output: 10
# Output: olleh
# Output: True False
# Output: [1, 2, 3, 4, 5, 6]
```
Slicing like `items[1:]` copies the list every time, making these `O(n²)` overall. Passing an **index** instead (`total(items, i)`) avoids the copying. `flatten` is the kind of problem where recursion is the **natural** tool: the depth of nesting is unknown, so a plain loop is awkward.

**Binary search, recursively**
```python
def binary_search(items, target, low=0, high=None):
    if high is None:
        high = len(items) - 1
    if low > high:
        return -1
    mid = (low + high) // 2
    if items[mid] == target:
        return mid
    if items[mid] < target:
        return binary_search(items, target, mid + 1, high)
    return binary_search(items, target, low, mid - 1)

print(binary_search([1, 3, 5, 7, 9, 11], 7), binary_search([1, 3, 5], 4))
# Output: 3 -1
```

---

## Repeated Work and Memoization

The plain `fib` recomputes the same values again and again:

```text
                 fib(5)
              /          \
          fib(4)          fib(3)
          /    \          /    \
      fib(3)  fib(2)   fib(2)  fib(1)
      /   \
  fib(2) fib(1)
```
`fib(3)` is computed twice, `fib(2)` three times. The number of calls roughly **doubles** with each step: about `O(2ⁿ)`.

```python
calls = 0
def slow_fib(n):
    global calls
    calls += 1
    return n if n < 2 else slow_fib(n - 1) + slow_fib(n - 2)

slow_fib(20)
print(calls)
# Output: 21891
```
**Memoization**: remember each result the first time. Python does it in one line with `functools.cache` (3.9+; `lru_cache` before that).
```python
from functools import cache

@cache
def fast_fib(n):
    return n if n < 2 else fast_fib(n - 1) + fast_fib(n - 2)

print(fast_fib(50), fast_fib(90))
print(fast_fib.cache_info().misses)
# Output: 12586269025 2880067194370816120
# Output: 91
```
Only 91 different values (`0` to `90`) were ever computed, each once: `O(n)` instead of `O(2ⁿ)`. The same idea written by hand:
```python
def fib_memo(n, memo=None):
    if memo is None:
        memo = {}
    if n in memo:
        return memo[n]
    if n < 2:
        return n
    memo[n] = fib_memo(n - 1, memo) + fib_memo(n - 2, memo)
    return memo[n]

print(fib_memo(60))
# Output: 1548008755920
```
Turning the recursion into a **bottom-up loop** is called dynamic programming: it needs no recursion and only two variables.
```python
def fib_loop(n):
    a, b = 0, 1
    for _ in range(n):
        a, b = b, a + b
    return a

print(fib_loop(60))
# Output: 1548008755920
```
Memoization only helps when **the same subproblem repeats**. Factorial never repeats a call, so caching gains nothing there.

---

## Recursion Limit

Python caps the call depth (**about 1000** by default) to protect memory.

```python
import sys

def endless(n):
    return endless(n + 1)           # no base case

try:
    endless(0)
except RecursionError as error:
    print(type(error).__name__)
print(sys.getrecursionlimit())
# Output: RecursionError
# Output: 1000
```
- `sys.setrecursionlimit(5000)` raises the cap, but a very high value can crash the interpreter (the C stack overflows). Prefer an **iterative** version for deep inputs.
- Python does **not** optimise tail calls, so a "tail-recursive" function is no safer than any other.

```python
def sum_to_loop(n):
    total = 0
    for i in range(1, n + 1):
        total += i
    return total

print(sum_to_loop(100_000))
# Output: 5000050000
```
`sum_to(100_000)` from earlier would raise a `RecursionError`; the loop version has no depth limit.

---

## Recursion vs Loops

| | Recursion | Loop |
|---|---|---|
| Reads best for | Trees, nested data, divide and conquer, backtracking | Simple repetition and counting |
| Memory | `O(depth)` stack | Usually `O(1)` |
| Depth limit | About 1000 in Python | None |
| Speed | Function-call overhead | Faster |

Anything recursive can be rewritten with a loop and your **own stack**, as the tree and graph notes do. Use recursion where it makes the code **clearer**, not just to look clever.

---

## Divide and Conquer

**Split** a problem into smaller independent parts, **solve** each recursively, **combine** the results. Merge sort and binary search are examples.

```python
def max_of(items, low=0, high=None):
    if high is None:
        high = len(items) - 1
    if low == high:
        return items[low]
    mid = (low + high) // 2
    return max(max_of(items, low, mid), max_of(items, mid + 1, high))

print(max_of([3, 9, 2, 7, 5]))
# Output: 9
```
Halving each time gives about `log n` levels, which is why divide-and-conquer algorithms are often `O(n log n)` or `O(log n)`.

---

## What Is Backtracking?

Backtracking **builds a solution one choice at a time**. After each choice it explores further; if that path **cannot work**, it **undoes the last choice** and tries another. It is like exploring a maze: walk down a corridor, and at a dead end, **step back** to the last fork and take a different turn.

```text
choose -> explore -> un-choose (undo)
```
It is used for: all subsets, permutations, combinations, puzzles (sudoku, N-Queens), and paths through grids.

---

## Backtracking Template

```text
def backtrack(current_choices):
    if the solution is complete:
        save a COPY of it
        return
    for each possible next choice:
        if the choice is valid:              # pruning
            make the choice                  # choose
            backtrack(updated choices)       # explore
            undo the choice                  # un-choose
```
The three steps **choose, explore, un-choose** appear in almost every backtracking solution.

**Save a copy, not the working list.** The same list is reused and changed as the search continues, so `results.append(path)` would store a reference that later gets emptied. Use `path[:]` or `list(path)`.

---

## Subsets

All **subsets** (the power set) of `[1, 2, 3]`: for each item, either **include** it or **skip** it, giving `2ⁿ` subsets.

```python
def subsets(items):
    results = []
    path = []

    def backtrack(index):
        if index == len(items):
            results.append(path[:])          # a COPY
            return
        backtrack(index + 1)                 # choice 1: skip items[index]
        path.append(items[index])            # choice 2: include it
        backtrack(index + 1)
        path.pop()                           # undo

    backtrack(0)
    return results

print(subsets([1, 2, 3]))
print(len(subsets(list(range(10)))))
# Output: [[], [3], [2], [2, 3], [1], [1, 3], [1, 2], [1, 2, 3]]
# Output: 1024
```
The order of results depends on the order of the choices; as a set of subsets it is the same. `itertools` has ready tools (`combinations`, `permutations`, `product`), useful for checking your own code:
```python
from itertools import combinations
print([list(c) for r in range(3) for c in combinations([1, 2], r)])
# Output: [[], [1], [2], [1, 2]]
```

---

## Permutations

All **orderings** of the items: `n!` of them.

```python
def permutations(items):
    results = []
    path = []
    used = [False] * len(items)

    def backtrack():
        if len(path) == len(items):
            results.append(path[:])
            return
        for i in range(len(items)):
            if used[i]:
                continue
            used[i] = True               # choose
            path.append(items[i])
            backtrack()                  # explore
            path.pop()                   # un-choose
            used[i] = False

    backtrack()
    return results

print(permutations([1, 2, 3]))
print(len(permutations([1, 2, 3, 4])))
# Output: [[1, 2, 3], [1, 3, 2], [2, 1, 3], [2, 3, 1], [3, 1, 2], [3, 2, 1]]
# Output: 24
```
**Permutations of a string with repeated letters, without duplicates**: sort first, and skip a letter that equals the previous letter when that previous one is not currently in use.
```python
def unique_permutations(text):
    letters = sorted(text)
    results = []
    path = []
    used = [False] * len(letters)

    def backtrack():
        if len(path) == len(letters):
            results.append("".join(path))
            return
        for i in range(len(letters)):
            if used[i]:
                continue
            if i > 0 and letters[i] == letters[i - 1] and not used[i - 1]:
                continue                 # same letter as the previous branch: skip
            used[i] = True
            path.append(letters[i])
            backtrack()
            path.pop()
            used[i] = False

    backtrack()
    return results

print(unique_permutations("aab"))
# Output: ['aab', 'aba', 'baa']
```

---

## Combinations

All ways to **choose `k` items** where order does **not** matter: "n choose k".

```python
def combine(n, k):
    results = []
    path = []

    def backtrack(start):
        if len(path) == k:
            results.append(path[:])
            return
        for number in range(start, n + 1):
            path.append(number)
            backtrack(number + 1)        # only look forward, so no repeats
            path.pop()

    backtrack(1)
    return results

print(combine(4, 2))
print(len(combine(5, 3)))
# Output: [[1, 2], [1, 3], [1, 4], [2, 3], [2, 4], [3, 4]]
# Output: 10
```
`start` is what stops `[2, 1]` from appearing after `[1, 2]`: each number is only chosen from **later** positions.

| You want | Order matters? | Repeats allowed? | Count |
|---|---|---|---|
| Subsets | No | No | `2ⁿ` |
| Permutations | **Yes** | No | `n!` |
| Combinations | No | No | `n! / (k!(n-k)!)` |

---

## Combination Sum and Pruning

**Pruning** means abandoning a path **early** as soon as it cannot lead to a solution. It makes backtracking far faster.

Find every combination of the numbers that adds up to `target` (each number may be reused):

```python
def combination_sum(candidates, target):
    candidates = sorted(candidates)
    results = []
    path = []

    def backtrack(start, remaining):
        if remaining == 0:
            results.append(path[:])
            return
        for i in range(start, len(candidates)):
            if candidates[i] > remaining:
                break                    # PRUNE: sorted, so every later number is too big too
            path.append(candidates[i])
            backtrack(i, remaining - candidates[i])    # i, not i + 1: reuse is allowed
            path.pop()

    backtrack(0, target)
    return results

print(combination_sum([2, 3, 6, 7], 7))
print(combination_sum([2, 3, 5], 8))
print(combination_sum([4, 5], 3))
# Output: [[2, 2, 3], [7]]
# Output: [[2, 2, 2, 2], [2, 3, 3], [3, 5]]
# Output: []
```
The `break` skips whole branches. Sorting first is what makes the pruning valid.

**Generate valid parentheses**: only add `(` while some are left, and `)` only when it would not close more than has been opened. Invalid strings are never even built.
```python
def parentheses(n):
    results = []

    def backtrack(current, opened, closed):
        if len(current) == 2 * n:
            results.append(current)
            return
        if opened < n:
            backtrack(current + "(", opened + 1, closed)
        if closed < opened:
            backtrack(current + ")", opened, closed + 1)

    backtrack("", 0, 0)
    return results

print(parentheses(3))
# Output: ['((()))', '(()())', '(())()', '()(())', '()()()']
```
Here `current + "("` creates a new string for each call, so there is nothing to undo: immutable values undo themselves.

---

## N-Queens

Place `n` queens on an `n x n` board so none attack each other (no two share a row, column or diagonal). Place one queen **per row**, and keep sets of the used columns and diagonals.

```python
def solve_n_queens(n):
    solutions = []
    columns = set()
    diagonals = set()                 # row - col is constant on one diagonal
    anti_diagonals = set()            # row + col is constant on the other
    placement = []                    # placement[row] = column of that row's queen

    def backtrack(row):
        if row == n:
            solutions.append(placement[:])
            return
        for col in range(n):
            if col in columns or (row - col) in diagonals or (row + col) in anti_diagonals:
                continue              # PRUNE: this square is attacked
            columns.add(col)
            diagonals.add(row - col)
            anti_diagonals.add(row + col)
            placement.append(col)
            backtrack(row + 1)
            placement.pop()           # undo everything
            columns.remove(col)
            diagonals.remove(row - col)
            anti_diagonals.remove(row + col)

    backtrack(0)
    return solutions

def draw(placement):
    n = len(placement)
    return ["".join("Q" if c == col else "." for c in range(n)) for col in placement]

answers = solve_n_queens(4)
print(len(answers), answers)
for line in draw(answers[0]):
    print(line)
print([len(solve_n_queens(n)) for n in range(1, 8)])
# Output: 2 [[1, 3, 0, 2], [2, 0, 3, 1]]
# Output: .Q..
# Output: ...Q
# Output: Q...
# Output: ..Q.
# Output: [1, 0, 0, 2, 10, 4, 40]
```
`[1, 3, 0, 2]` means: row 0 has its queen in column 1, row 1 in column 3, and so on. Without pruning, `8` queens would need to look through billions of placements; with it, only thousands of positions are tried.

---

## Word Search in a Grid

Does a word exist as a path of **adjacent** letters (up, down, left, right), using each cell **at most once**? Mark a cell as used, explore, then **restore** it.

```python
def word_exists(board, word):
    rows, cols = len(board), len(board[0])

    def backtrack(r, c, index):
        if index == len(word):
            return True
        if not (0 <= r < rows and 0 <= c < cols) or board[r][c] != word[index]:
            return False
        saved = board[r][c]
        board[r][c] = "#"                     # choose: mark as used
        found = (backtrack(r + 1, c, index + 1) or backtrack(r - 1, c, index + 1) or
                 backtrack(r, c + 1, index + 1) or backtrack(r, c - 1, index + 1))
        board[r][c] = saved                   # un-choose: restore
        return found

    return any(backtrack(r, c, 0) for r in range(rows) for c in range(cols))

board = [list("ABCE"),
         list("SFCS"),
         list("ADEE")]
print(word_exists(board, "ABCCED"), word_exists(board, "SEE"), word_exists(board, "ABCB"))
# Output: True True False
```
`ABCB` fails because the only `B` would have to be used twice. Restoring the cell is the "undo" step: forgetting it would damage the board for later searches.

---

## Tower of Hanoi

Move `n` disks from peg `A` to peg `C`, one at a time, never placing a larger disk on a smaller one. The recursive idea:
1. Move the top `n-1` disks from `A` to `B` (using `C`).
2. Move the biggest disk from `A` to `C`.
3. Move the `n-1` disks from `B` to `C` (using `A`).

```python
def hanoi(n, source, target, spare, moves):
    if n == 0:
        return
    hanoi(n - 1, source, spare, target, moves)
    moves.append((source, target))
    hanoi(n - 1, spare, target, source, moves)

moves = []
hanoi(3, "A", "C", "B", moves)
print(len(moves))
print(moves)
print([2**n - 1 for n in range(1, 6)])
# Output: 7
# Output: [('A', 'C'), ('A', 'B'), ('C', 'B'), ('A', 'C'), ('B', 'A'), ('B', 'C'), ('A', 'C')]
# Output: [1, 3, 7, 15, 31]
```
It takes `2ⁿ - 1` moves, so doubling the disks roughly **squares** the work: 64 disks would take more than 18 quintillion moves.

---

## Complexity of Backtracking

Backtracking explores a **tree of choices**, so the cost is usually **exponential** or **factorial** in the worst case. Pruning cuts branches but rarely changes the worst case.

| Problem | Number of results | Typical time |
|---|---|---|
| Subsets | `2ⁿ` | `O(n * 2ⁿ)` |
| Permutations | `n!` | `O(n * n!)` |
| Combinations | `C(n, k)` | `O(k * C(n, k))` |
| N-Queens | Grows quickly | Far less than `n!` thanks to pruning |
| Tower of Hanoi | one sequence | `O(2ⁿ)` |

- Space is mostly the **recursion depth** (and the results themselves, if they are stored).
- Such solutions are practical only for **small `n`** (roughly up to 10 to 20).
- If the same state is reached in many ways, add **memoization** (this becomes dynamic programming).

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| No base case | `RecursionError` | Decide the smallest input first |
| The call does not get smaller | Infinite recursion | Move toward the base case every call |
| Base case misses an input (negative number, empty list) | Crash or endless recursion | Check the edge cases |
| Forgetting to `return` the recursive result | `None` comes back | `return n * factorial(n - 1)` |
| Appending the working list to `results` | Every result ends up empty or identical | Append a **copy**: `path[:]` |
| Forgetting the "undo" step | Wrong or missing results | `append` is always paired with `pop` |
| Plain recursive `fib(40+)` | Extremely slow | `@cache` or a loop |
| Recursion on deep inputs | `RecursionError` at about 1000 | Loop, or an explicit stack |
| Mutable default argument (`memo={}`) | The dictionary is shared across calls | Use `None` and create it inside |
| Slicing in every call | Hidden `O(n)` copy each time | Pass indexes |
| Reusing candidates by mistake (`i + 1` vs `i`) | Missing or extra answers | Decide whether repeats are allowed |
| No pruning | Needlessly slow | Stop a branch the moment it cannot succeed |

---

- [Back to section index](./README.md)
