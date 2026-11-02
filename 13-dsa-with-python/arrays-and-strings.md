# Arrays and Strings - Revision Notes ~ sm

> **In one line:** An **array** is a row of items stored one after another, so any item can be reached **instantly by its index**. In Python the everyday array is the **`list`**, and a **string** is an immutable sequence of characters. Most interview problems on them are solved with a few patterns: **two pointers**, **sliding window**, **prefix sums** and **in-place swaps**.

## Table of Contents
- [Arrays in Python](#arrays-in-python)
- [Basic Operations and Their Cost](#basic-operations-and-their-cost)
- [Two-Dimensional Arrays (Grids)](#two-dimensional-arrays-grids)
- [Pattern 1: Two Pointers](#pattern-1-two-pointers)
- [Pattern 2: Sliding Window](#pattern-2-sliding-window)
- [Pattern 3: Prefix Sums](#pattern-3-prefix-sums)
- [Pattern 4: Kadane's Algorithm](#pattern-4-kadanes-algorithm)
- [Pattern 5: In-place Tricks](#pattern-5-in-place-tricks)
- [Matrices](#matrices)
- [Strings](#strings)
- [String Problems](#string-problems)
- [array and bytearray](#array-and-bytearray)
- [Common Mistakes](#common-mistakes)

---

## Arrays in Python

A classic array is a **block of memory** where item `i` sits at a fixed distance from the start. That is why `items[i]` is **O(1)**: no searching, just arithmetic.

Python's **`list`** is a **dynamic array**:
- It holds **references** (pointers) to objects, so one list can mix types.
- It **grows automatically** when you append (amortized `O(1)`, see the Complexity note).
- Indexing is `O(1)`; searching with `in` is `O(n)`.

```python
items = [10, 20, 30, 40]
print(items[0], items[-1], len(items))
print(items[1:3])
# Output: 10 40 4
# Output: [20, 30]
```
For huge numeric arrays, the third-party **NumPy** library stores raw numbers compactly and is much faster. The standard library also has a compact `array` module (see the end of this note).

---

## Basic Operations and Their Cost

| Operation | Example | Cost |
|---|---|---|
| Read or write by index | `a[i]`, `a[i] = x` | `O(1)` |
| Add at the end | `a.append(x)` | `O(1)` amortized |
| Remove from the end | `a.pop()` | `O(1)` |
| Insert or delete in the middle or front | `a.insert(i, x)`, `a.pop(i)` | `O(n)` (items shift) |
| Search | `x in a`, `a.index(x)` | `O(n)` |
| Slice | `a[i:j]` | `O(j - i)` (a **copy**) |
| Visit everything | `for x in a` | `O(n)` |

```python
a = [1, 2, 3]
a.insert(0, 0)       # shifts every item to the right: O(n)
a.append(4)          # no shifting: O(1)
a.pop()              # O(1)
a.pop(0)             # shifts every item to the left: O(n)
print(a)
# Output: [1, 2, 3]
```

---

## Two-Dimensional Arrays (Grids)

A grid is a **list of lists**. `grid[row][col]`.

```python
grid = [[0] * 3 for _ in range(2)]      # 2 rows, 3 columns
grid[0][1] = 5
print(grid)
print(len(grid), len(grid[0]))
# Output: [[0, 5, 0], [0, 0, 0]]
# Output: 2 3
```
**The classic trap:** `[[0] * 3] * 2` repeats the **same inner list**, so all rows are one object.
```python
bad = [[0] * 3] * 2
bad[0][0] = 9
print(bad)

good = [[0] * 3 for _ in range(2)]
good[0][0] = 9
print(good)
# Output: [[9, 0, 0], [9, 0, 0]]
# Output: [[9, 0, 0], [0, 0, 0]]
```
Visit every cell:
```python
grid = [[1, 2, 3], [4, 5, 6]]
for r, row in enumerate(grid):
    for c, value in enumerate(row):
        if value % 2 == 0:
            print(f"({r},{c})={value}")
# Output: (0,1)=2
# Output: (1,0)=4
# Output: (1,2)=6
```
A grid with `R` rows and `C` columns takes `O(R * C)` to visit and to store.

---

## Pattern 1: Two Pointers

Use **two indexes** that move through the data, instead of a nested loop. It often turns `O(n²)` into `O(n)`.

**A) Pointers from both ends, moving towards each other**

*Reverse in place*
```python
def reverse(items):
    left, right = 0, len(items) - 1
    while left < right:
        items[left], items[right] = items[right], items[left]
        left += 1
        right -= 1

data = [1, 2, 3, 4, 5]
reverse(data)
print(data)
# Output: [5, 4, 3, 2, 1]
```
*Two numbers in a **sorted** list that add up to a target*
```python
def two_sum_sorted(nums, target):
    left, right = 0, len(nums) - 1
    while left < right:
        total = nums[left] + nums[right]
        if total == target:
            return left, right
        if total < target:
            left += 1          # need a bigger sum
        else:
            right -= 1         # need a smaller sum
    return None

print(two_sum_sorted([1, 2, 4, 7, 11, 15], 15))
print(two_sum_sorted([1, 2, 3], 100))
# Output: (2, 4)
# Output: None
```
This is `O(n)` time and `O(1)` space. It works **only because the list is sorted**: if the sum is too small, moving `left` right is the only way to increase it.

*Palindrome check ignoring punctuation and case*
```python
def is_palindrome(text):
    left, right = 0, len(text) - 1
    while left < right:
        if not text[left].isalnum():
            left += 1
        elif not text[right].isalnum():
            right -= 1
        elif text[left].lower() != text[right].lower():
            return False
        else:
            left += 1
            right -= 1
    return True

print(is_palindrome("A man, a plan, a canal: Panama"), is_palindrome("hello"))
# Output: True False
```

**B) Two pointers moving the same way: a "reader" and a "writer"**

*Remove duplicates from a sorted list, in place*
```python
def remove_duplicates(nums):
    if not nums:
        return 0
    write = 1
    for read in range(1, len(nums)):
        if nums[read] != nums[write - 1]:
            nums[write] = nums[read]
            write += 1
    return write

data = [1, 1, 2, 2, 2, 3]
k = remove_duplicates(data)
print(k, data[:k])
# Output: 3 [1, 2, 3]
```
*Move all zeros to the end, keeping the order of the others*
```python
def move_zeros(nums):
    write = 0
    for read in range(len(nums)):
        if nums[read] != 0:
            nums[write], nums[read] = nums[read], nums[write]
            write += 1

data = [0, 1, 0, 3, 12]
move_zeros(data)
print(data)
# Output: [1, 3, 12, 0, 0]
```
The `read` pointer scans every item; the `write` pointer marks where the next kept item goes.

---

## Pattern 2: Sliding Window

A **window** is a stretch of consecutive items. Instead of recalculating it from scratch each time, **slide** it by adding the item that enters and removing the one that leaves.

**Fixed-size window: biggest sum of `k` neighbours**
```python
def max_sum_of_k(nums, k):
    window = sum(nums[:k])
    best = window
    for i in range(k, len(nums)):
        window += nums[i] - nums[i - k]      # add the new item, drop the old one
        best = max(best, window)
    return best

print(max_sum_of_k([2, 1, 5, 1, 3, 2], 3))
# Output: 9
```
`O(n)`, instead of `O(n * k)` by adding `k` numbers at every position.

**Variable-size window: longest substring without a repeated character**
```python
def longest_unique(text):
    seen = {}                 # character -> last index
    start = 0
    best = 0
    for end, ch in enumerate(text):
        if ch in seen and seen[ch] >= start:
            start = seen[ch] + 1      # shrink: jump past the earlier copy
        seen[ch] = end
        best = max(best, end - start + 1)
    return best

print(longest_unique("abcabcbb"), longest_unique("pwwkew"), longest_unique(""))
# Output: 3 3 0
```
**Variable-size window: smallest sub-list with sum at least `target`** (positive numbers)
```python
def min_length(target, nums):
    start = 0
    total = 0
    best = float("inf")
    for end, value in enumerate(nums):
        total += value                    # grow the window on the right
        while total >= target:            # shrink on the left while still valid
            best = min(best, end - start + 1)
            total -= nums[start]
            start += 1
    return 0 if best == float("inf") else best

print(min_length(7, [2, 3, 1, 2, 4, 3]), min_length(100, [1, 2]))
# Output: 2 0
```
Each item enters the window once and leaves once, so this is `O(n)` even though there is a loop inside a loop.

**When to think "sliding window":** the question asks for a **longest / shortest / best sub-list or substring** (contiguous), often with a condition on its contents.

---

## Pattern 3: Prefix Sums

A **prefix sum** array stores the running total, so the sum of **any range** is one subtraction.

```python
from itertools import accumulate

nums = [3, 1, 4, 1, 5, 9]
prefix = [0] + list(accumulate(nums))
print(prefix)

def range_sum(i, j):                 # sum of nums[i..j], both included
    return prefix[j + 1] - prefix[i]

print(range_sum(1, 4), sum(nums[1:5]))
# Output: [0, 3, 4, 8, 9, 14, 23]
# Output: 11 11
```
Building it costs `O(n)` once; every query is then `O(1)` instead of `O(n)`. The extra `0` at the front makes `prefix[i]` mean "the sum of the first `i` items".

**Equilibrium index** (left sum equals right sum):
```python
def equilibrium(nums):
    total = sum(nums)
    left = 0
    for i, value in enumerate(nums):
        if left == total - left - value:
            return i
        left += value
    return -1

print(equilibrium([1, 7, 3, 6, 5, 6]), equilibrium([1, 2, 3]))
# Output: 3 -1
```
The same idea with a **dictionary of prefix sums** counts sub-lists with a given sum (see the Hash Maps note).

---

## Pattern 4: Kadane's Algorithm

**Largest sum of a contiguous sub-list.** At each item decide: extend the current run, or start a new run here?

```python
def max_subarray(nums):
    best = current = nums[0]
    for value in nums[1:]:
        current = max(value, current + value)
        best = max(best, current)
    return best

print(max_subarray([-2, 1, -3, 4, -1, 2, 1, -5, 4]))
print(max_subarray([-3, -1, -2]))
# Output: 6
# Output: -1
```
The best run is `[4, -1, 2, 1]`, with sum `6`. `O(n)` time, `O(1)` space. If **all numbers are negative** the answer is the least negative number, so do not start `best` at `0`.

---

## Pattern 5: In-place Tricks

**Rotate a list to the right by `k`** using three reversals.
```python
def reverse_range(items, left, right):
    while left < right:
        items[left], items[right] = items[right], items[left]
        left += 1
        right -= 1

def rotate_right(items, k):
    n = len(items)
    k %= n
    reverse_range(items, 0, n - 1)       # reverse everything
    reverse_range(items, 0, k - 1)       # reverse the first k
    reverse_range(items, k, n - 1)       # reverse the rest

data = [1, 2, 3, 4, 5, 6, 7]
rotate_right(data, 3)
print(data)
# Output: [5, 6, 7, 1, 2, 3, 4]
```
`O(n)` time, `O(1)` extra space. (The short version `items[-k:] + items[:-k]` is `O(n)` extra space.)

**Sort a list of `0`s, `1`s and `2`s in one pass** (Dutch national flag)
```python
def sort_colors(nums):
    low, mid, high = 0, 0, len(nums) - 1
    while mid <= high:
        if nums[mid] == 0:
            nums[low], nums[mid] = nums[mid], nums[low]
            low += 1
            mid += 1
        elif nums[mid] == 1:
            mid += 1
        else:
            nums[mid], nums[high] = nums[high], nums[mid]
            high -= 1

data = [2, 0, 2, 1, 1, 0]
sort_colors(data)
print(data)
# Output: [0, 0, 1, 1, 2, 2]
```
Three pointers split the list into three zones: `0`s, `1`s and unsorted.

---

## Matrices

```python
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9],
]
```
**Transpose** (rows become columns)
```python
matrix = [[1, 2, 3], [4, 5, 6]]
print([list(col) for col in zip(*matrix)])
# Output: [[1, 4], [2, 5], [3, 6]]
```
**Rotate 90 degrees clockwise**: transpose, then reverse each row.
```python
def rotate_90(matrix):
    return [list(reversed(col)) for col in zip(*matrix)]

print(rotate_90([[1, 2], [3, 4]]))
print(rotate_90([[1, 2, 3], [4, 5, 6]]))
# Output: [[3, 1], [4, 2]]
# Output: [[4, 1], [5, 2], [6, 3]]
```
**Spiral order**
```python
def spiral(matrix):
    result = []
    top, bottom = 0, len(matrix) - 1
    left, right = 0, len(matrix[0]) - 1
    while top <= bottom and left <= right:
        for c in range(left, right + 1):
            result.append(matrix[top][c])
        top += 1
        for r in range(top, bottom + 1):
            result.append(matrix[r][right])
        right -= 1
        if top <= bottom:
            for c in range(right, left - 1, -1):
                result.append(matrix[bottom][c])
            bottom -= 1
        if left <= right:
            for r in range(bottom, top - 1, -1):
                result.append(matrix[r][left])
            left += 1
    return result

print(spiral([[1, 2, 3], [4, 5, 6], [7, 8, 9]]))
# Output: [1, 2, 3, 6, 9, 8, 7, 4, 5]
```
All of these are `O(R * C)`. The two `if` checks stop the spiral from repeating items when one row or column is left.

**Neighbours in a grid** (used for flood fill, paths, islands)
```python
DIRECTIONS = [(-1, 0), (1, 0), (0, -1), (0, 1)]     # up, down, left, right

def neighbours(r, c, rows, cols):
    for dr, dc in DIRECTIONS:
        nr, nc = r + dr, c + dc
        if 0 <= nr < rows and 0 <= nc < cols:
            yield nr, nc

print(list(neighbours(0, 0, 3, 3)))
# Output: [(1, 0), (0, 1)]
```
Always check the **bounds** before using a neighbour, or a negative index will silently wrap around to the other end.

---

## Strings

A string is an **immutable sequence of characters**: you can index and slice it exactly like a list, but you cannot change a character in place.

```python
s = "python"
print(s[0], s[-1], s[1:4], s[::-1])
# Output: p n yth nohtyp
```
```python
s = "python"
s[0] = "P"
# Error: TypeError: 'str' object does not support item assignment
```
**What this means for algorithms**
- "Changing" a string builds a **new** one: `O(n)`.
- To modify characters repeatedly, convert to a **list**, change it, and `"".join()` it back.
- Build a result with a **list of pieces and `"".join`**, not `+=` in a loop.

```python
text = "hello"
chars = list(text)
chars[0] = "J"
print("".join(chars))
# Output: Jello
```
**Useful tools**

| Tool | Does |
|---|---|
| `ord("a")` / `chr(97)` | Character to its number / number to character |
| `s.isalpha()`, `isdigit()`, `isalnum()` | Character classes |
| `s.lower()`, `s.upper()` | Change case (returns a new string) |
| `s.split()`, `sep.join(parts)` | Split into words / join back |
| `s.find(sub)` | Position of `sub`, or `-1` |
| `s.startswith(x)`, `s.endswith(x)` | Prefix and suffix tests |
| `collections.Counter(s)` | Character frequencies |
| `sorted(s)` | List of characters in order |

`ord` and `chr` let you turn letters into indexes `0` to `25`:
```python
print(ord("c") - ord("a"), chr(ord("a") + 3))
# Output: 2 d
```

---

## String Problems

**Anagrams: same letters, different order**
```python
from collections import Counter

def is_anagram(a, b):
    return Counter(a) == Counter(b)            # O(n)

def is_anagram_sorted(a, b):
    return sorted(a) == sorted(b)              # O(n log n)

print(is_anagram("listen", "silent"), is_anagram_sorted("abc", "abd"))
# Output: True False
```
A fixed array of 26 counters also works for lowercase letters:
```python
def is_anagram_array(a, b):
    if len(a) != len(b):
        return False
    counts = [0] * 26
    for x, y in zip(a, b):
        counts[ord(x) - ord("a")] += 1
        counts[ord(y) - ord("a")] -= 1
    return all(c == 0 for c in counts)

print(is_anagram_array("listen", "silent"), is_anagram_array("aab", "abb"))
# Output: True False
```
`O(n)` time and `O(1)` space (26 counters, no matter how long the words are).

**Reverse the words of a sentence**
```python
sentence = "  the sky   is blue "
print(" ".join(sentence.split()[::-1]))
# Output: blue is sky the
```
`split()` with no argument ignores extra spaces.

**Run-length compression**
```python
def compress(text):
    if not text:
        return ""
    parts = []
    count = 1
    for previous, current in zip(text, text[1:]):
        if current == previous:
            count += 1
        else:
            parts.append(f"{previous}{count}")
            count = 1
    parts.append(f"{text[-1]}{count}")
    return "".join(parts)

print(compress("aabcccccaaa"))
# Output: a2b1c5a3
```

**Longest common prefix**
```python
def common_prefix(words):
    if not words:
        return ""
    prefix = words[0]
    for word in words[1:]:
        while not word.startswith(prefix):
            prefix = prefix[:-1]
    return prefix

print(repr(common_prefix(["flower", "flow", "flight"])), repr(common_prefix(["dog", "car"])))
# Output: 'fl' ''
```

**Find every position of a pattern (naive search)**
```python
def find_all(text, pattern):
    positions = []
    for i in range(len(text) - len(pattern) + 1):
        if text[i:i + len(pattern)] == pattern:
            positions.append(i)
    return positions

print(find_all("aabaabaa", "aab"))
# Output: [0, 3]
```
This is `O(n * m)` (text length times pattern length). Faster algorithms exist (KMP, Rabin-Karp), but Python's built-in `str.find` is usually enough.

**First character that does not repeat**
```python
from collections import Counter

def first_unique(text):
    counts = Counter(text)
    for i, ch in enumerate(text):
        if counts[ch] == 1:
            return i
    return -1

print(first_unique("swiss"), first_unique("aabb"))
# Output: 1 -1
```

**Check that a string is a rotation of another**
```python
def is_rotation(a, b):
    return len(a) == len(b) and b in a + a

print(is_rotation("waterbottle", "erbottlewat"), is_rotation("abc", "acb"))
# Output: True False
```
If `b` is a rotation of `a`, then it appears inside `a` written twice.

---

## array and bytearray

The `array` module stores **numbers of one type** compactly (it is not a list of objects).

```python
from array import array

numbers = array("i", [1, 2, 3])        # "i" = signed int
numbers.append(4)
print(numbers, numbers.tolist(), numbers.typecode)
# Output: array('i', [1, 2, 3, 4]) [1, 2, 3, 4] i
```
```python
from array import array

numbers = array("i", [1, 2, 3])
numbers.append("x")
# Error: TypeError: 'str' object cannot be interpreted as an integer
```
A `bytearray` is a **mutable** sequence of bytes, handy for changing text-like data in place:
```python
data = bytearray(b"hello")
data[0] = 72                     # 72 is the code for "H"
print(data, data.decode())
# Output: bytearray(b'Hello') Hello
```
For heavy numeric work use **NumPy**; for everything else a `list` is fine.

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| `[[0] * 3] * 2` for a grid | All rows are the same list | `[[0] * 3 for _ in range(2)]` |
| Using a nested loop when sorted data allows two pointers | `O(n²)` | Two pointers (`O(n)`) |
| Using two pointers on **unsorted** data | Wrong answers | Sort first, or use a dictionary |
| Recomputing a window's sum from scratch | `O(n * k)` | Add the entering item, subtract the leaving one |
| Starting Kadane's `best` at `0` | Wrong when every number is negative | Start with the first item |
| `items.pop(0)` / `insert(0, x)` in a loop | Each shift is `O(n)` | `collections.deque`, or index pointers |
| `text = text + ch` in a long loop | Copies the string each time | Collect in a list and `"".join` |
| Trying `s[i] = "x"` | `TypeError` | Convert to a list, change it, join back |
| Slicing inside a loop (`items[1:]` repeated) | Hidden `O(n)` copies | Pass indexes instead |
| Off-by-one in `range` | Missing the last item or `IndexError` | Remember the stop is **not** included |
| Negative index from `r - 1` at the edge | Silently reads the **last** item | Check `0 <= r < rows` first |
| Forgetting `k %= len(items)` when rotating | Wrong result or error for big `k` | Reduce `k` first (and handle an empty list) |
| Modifying a list while looping over it | Skipped items | Loop over a copy, or build a new list |
| Comparing strings without thinking of case | `"A" != "a"` | `.lower()` or `.casefold()` first |

---

- [Back to section index](./README.md)
