# Searching - Revision Notes ~ sm

> **In one line:** **Searching** means finding an item (or its position) in a collection. **Linear search** checks items one by one (`O(n)`, works on anything). **Binary search** repeatedly **halves a sorted collection** (`O(log n)`, needs sorted data), and the same halving idea can also find the best **answer** to a problem, not just a value in a list.

## Table of Contents
- [Linear Search](#linear-search)
- [Binary Search: The Idea](#binary-search-the-idea)
- [Binary Search: Iterative Code](#binary-search-iterative-code)
- [Tracing a Binary Search](#tracing-a-binary-search)
- [Binary Search: Recursive Code](#binary-search-recursive-code)
- [Getting the Details Right](#getting-the-details-right)
- [Lower Bound and Upper Bound](#lower-bound-and-upper-bound)
- [The bisect Module](#the-bisect-module)
- [Searching Variations](#searching-variations)
- [Binary Search on the Answer](#binary-search-on-the-answer)
- [Searching a Sorted Matrix](#searching-a-sorted-matrix)
- [Other Ways to Search](#other-ways-to-search)
- [Choosing a Search](#choosing-a-search)
- [Common Mistakes](#common-mistakes)

---

## Linear Search

Check every item from the start until you find the target.

```python
def linear_search(items, target):
    for index, item in enumerate(items):
        if item == target:
            return index
    return -1

data = [7, 3, 9, 4, 1]
print(linear_search(data, 9), linear_search(data, 100))
# Output: 2 -1
```
| | |
|---|---|
| Time | `O(n)` worst case (last item or missing), `O(1)` best case |
| Space | `O(1)` |
| Needs sorted data? | **No** |
| Works on | Lists, strings, files, anything you can loop over |

Python's `x in items` and `items.index(x)` are linear searches written in C.

---

## Binary Search: The Idea

Think of guessing a number from 1 to 100. Someone says "higher" or "lower" after each guess. The smartest guess is always the **middle** of what is still possible, because every answer throws away **half** of the options:

100 → 50 → 25 → 12 → 6 → 3 → 2 → 1 : at most **7 guesses**.

**Binary search does exactly this on a sorted list.** Compare the target with the **middle** item:
- equal: found,
- target is smaller: it can only be in the **left half**,
- target is larger: it can only be in the **right half**.

Repeat on the half that is left. The list **must be sorted**, otherwise throwing half away is wrong.

| Items `n` | Linear search worst case | Binary search worst case |
|---|---|---|
| 100 | 100 | 7 |
| 1,000 | 1,000 | 10 |
| 1,000,000 | 1,000,000 | 20 |
| 1,000,000,000 | 1,000,000,000 | 30 |

---

## Binary Search: Iterative Code

```python
def binary_search(items, target):
    low, high = 0, len(items) - 1          # the range still possible: low..high
    while low <= high:
        mid = (low + high) // 2
        if items[mid] == target:
            return mid
        if items[mid] < target:
            low = mid + 1                  # discard the left half and mid
        else:
            high = mid - 1                 # discard the right half and mid
    return -1                              # range is empty: not found

data = [1, 3, 5, 7, 9, 11, 13]
print(binary_search(data, 7))
print(binary_search(data, 1), binary_search(data, 13))
print(binary_search(data, 4))
print(binary_search([], 4))
# Output: 3
# Output: 0 6
# Output: -1
# Output: -1
```
| | |
|---|---|
| Time | `O(log n)` |
| Space | `O(1)` iterative |
| Needs sorted data? | **Yes** |

`low` and `high` are the **inclusive** ends of the range that could still hold the target. The loop runs while the range is **not empty** (`low <= high`).

---

## Tracing a Binary Search

Searching for `11` in `[1, 3, 5, 7, 9, 11, 13]`:

| Step | low | high | mid | items[mid] | Decision |
|---|---|---|---|---|---|
| 1 | 0 | 6 | 3 | 7 | `7 < 11`, go right: `low = 4` |
| 2 | 4 | 6 | 5 | 11 | found at index 5 |

Searching for `4` (missing):

| Step | low | high | mid | items[mid] | Decision |
|---|---|---|---|---|---|
| 1 | 0 | 6 | 3 | 7 | `7 > 4`, go left: `high = 2` |
| 2 | 0 | 2 | 1 | 3 | `3 < 4`, go right: `low = 2` |
| 3 | 2 | 2 | 2 | 5 | `5 > 4`, go left: `high = 1` |
| end | 2 | 1 | | | `low > high`: empty range, not found |

Always trace small examples by hand when you write your own version.

---

## Binary Search: Recursive Code

```python
def binary_search_rec(items, target, low=0, high=None):
    if high is None:
        high = len(items) - 1
    if low > high:
        return -1
    mid = (low + high) // 2
    if items[mid] == target:
        return mid
    if items[mid] < target:
        return binary_search_rec(items, target, mid + 1, high)
    return binary_search_rec(items, target, low, mid - 1)

print(binary_search_rec([2, 4, 6, 8, 10], 8), binary_search_rec([2, 4, 6, 8, 10], 5))
# Output: 3 -1
```
It does the same work, but uses `O(log n)` stack space. Pass **indexes**, not slices: `items[mid + 1:]` would copy half the list at each step and turn the search into `O(n)`.

---

## Getting the Details Right

Binary search is famous for small bugs. Check these:

| Detail | Why it matters |
|---|---|
| **`low <= high`** (not `<`) | With `<` the loop stops while one candidate is still unchecked |
| **`mid + 1` and `mid - 1`** | `mid` has been checked, so it must leave the range. Writing `low = mid` can loop forever |
| **Sorted input** | Unsorted data gives wrong answers with no error |
| **Midpoint** | `(low + high) // 2` is fine in Python (integers never overflow). In languages with fixed-size integers write `low + (high - low) // 2` |
| **Empty list** | `high = -1`, so the loop does not run and `-1` is returned |
| **Duplicates** | A plain binary search returns **one** matching index, not necessarily the first or last (use the bounds below) |
| **Termination** | Every pass must shrink the range |

```python
def broken(items, target):
    low, high = 0, len(items) - 1
    while low < high:                     # bug: misses a one-item range
        mid = (low + high) // 2
        if items[mid] == target:
            return mid
        if items[mid] < target:
            low = mid + 1
        else:
            high = mid - 1
    return -1

print(broken([1, 3, 5], 5), broken([5], 5))
# Output: -1 -1
```
Both answers are wrong: `5` is in both lists. Once the range shrinks to **one** item (`low == high`), `low < high` is false and that last candidate is never looked at. For `[1, 3, 5]` the search moves `low` to index 2 and then stops without checking it.

---

## Lower Bound and Upper Bound

With duplicates, two functions answer most questions:

- **lower bound** = the first index whose value is **greater than or equal to** the target (the position where the target would be inserted at the left end of any equal items),
- **upper bound** = the first index whose value is **greater than** the target.

```python
def lower_bound(items, target):
    low, high = 0, len(items)              # high is len(items): "past the end" is allowed
    while low < high:
        mid = (low + high) // 2
        if items[mid] < target:
            low = mid + 1
        else:
            high = mid
    return low

def upper_bound(items, target):
    low, high = 0, len(items)
    while low < high:
        mid = (low + high) // 2
        if items[mid] <= target:
            low = mid + 1
        else:
            high = mid
    return low

data = [1, 2, 2, 2, 3, 5]
print(lower_bound(data, 2), upper_bound(data, 2))
print(lower_bound(data, 4), lower_bound(data, 100))
# Output: 1 4
# Output: 5 6
```
From these:
```python
data = [1, 2, 2, 2, 3, 5]
first = lower_bound(data, 2)
last = upper_bound(data, 2) - 1
count = upper_bound(data, 2) - lower_bound(data, 2)
print(first, last, count)
# Output: 1 3 3
```
(Check `first < len(data) and data[first] == target` to know whether the target really exists.) This style uses a **half-open** range `[low, high)` and `while low < high`, with `high = mid` instead of `mid - 1`. It is a good template for "find the first position where a condition becomes true".

---

## The bisect Module

Python's standard library already has these functions, written in C.

```python
from bisect import bisect_left, bisect_right, insort

data = [1, 2, 2, 2, 3, 5]
print(bisect_left(data, 2))
print(bisect_right(data, 2))
print(bisect_left(data, 4))

insort(data, 4)
print(data)
# Output: 1
# Output: 4
# Output: 5
# Output: [1, 2, 2, 2, 3, 4, 5]
```
| Function | Meaning |
|---|---|
| `bisect_left(a, x)` | Lower bound: insert before equal items |
| `bisect_right(a, x)` or `bisect(a, x)` | Upper bound: insert after equal items |
| `insort_left`, `insort_right`, `insort` | Insert **and keep the list sorted** (`O(n)`, because items shift) |
| `key=` argument | Python 3.10+: compare a field of each item (the list must be sorted by that field) |

**Membership test on a sorted list**
```python
from bisect import bisect_left

def contains(sorted_items, x):
    i = bisect_left(sorted_items, x)
    return i < len(sorted_items) and sorted_items[i] == x

print(contains([1, 3, 5, 7], 5), contains([1, 3, 5, 7], 4))
# Output: True False
```
**A lookup table: marks to grades**
```python
from bisect import bisect_right

breakpoints = [60, 70, 80, 90]
grades = "FDCBA"

def grade(score):
    return grades[bisect_right(breakpoints, score)]

print([grade(s) for s in (59, 60, 85, 90, 100)])
# Output: ['F', 'D', 'B', 'A', 'A']
```
`bisect_right` counts how many breakpoints are less than or equal to the score, which is exactly the index of the grade.

**`key=` (Python 3.10 and newer)**
```python
from bisect import bisect_left

people = [("Riya", 18), ("Amit", 25), ("Zoya", 31)]    # sorted by age
position = bisect_left(people, 25, key=lambda p: p[1])
print(position, people[position])
# Output: 1 ('Amit', 25)
```

---

## Searching Variations

**Search in a rotated sorted list** (distinct values; for example `[4, 5, 6, 7, 0, 1, 2]` is `[0, 1, 2, 4, 5, 6, 7]` rotated)

At every step **one of the two halves is still in order**. Find which, check whether the target lies in it, and keep that half or the other.

```python
def search_rotated(nums, target):
    low, high = 0, len(nums) - 1
    while low <= high:
        mid = (low + high) // 2
        if nums[mid] == target:
            return mid
        if nums[low] <= nums[mid]:                     # left half is sorted
            if nums[low] <= target < nums[mid]:
                high = mid - 1
            else:
                low = mid + 1
        else:                                          # right half is sorted
            if nums[mid] < target <= nums[high]:
                low = mid + 1
            else:
                high = mid - 1
    return -1

print(search_rotated([4, 5, 6, 7, 0, 1, 2], 0), search_rotated([4, 5, 6, 7, 0, 1, 2], 3))
# Output: 4 -1
```
**Smallest item of a rotated sorted list**
```python
def find_min(nums):
    low, high = 0, len(nums) - 1
    while low < high:
        mid = (low + high) // 2
        if nums[mid] > nums[high]:
            low = mid + 1          # the minimum is to the right of mid
        else:
            high = mid             # mid could be the minimum
    return nums[low]

print(find_min([4, 5, 6, 7, 0, 1, 2]), find_min([1, 2, 3]))
# Output: 0 1
```
**A peak element** (bigger than its neighbours; neighbours of the ends count as smaller). Always move **uphill**.
```python
def find_peak(nums):
    low, high = 0, len(nums) - 1
    while low < high:
        mid = (low + high) // 2
        if nums[mid] < nums[mid + 1]:
            low = mid + 1          # a peak exists to the right
        else:
            high = mid
    return low

data = [1, 2, 1, 3, 5, 6, 4]
index = find_peak(data)
print(index, data[index])
# Output: 5 6
```
(There are two peaks here, `2` and `6`; the method finds one of them.)

---

## Binary Search on the Answer

Binary search is not only for lists. If the possible answers form a range `lo..hi` and there is a yes/no question **"is this value good enough?"** that flips **once** from "no" to "yes" (it is **monotonic**), you can binary search for the boundary. The "list" never exists in memory.

**Integer square root**: the largest `x` with `x * x <= n`
```python
def isqrt(n):
    low, high = 0, n
    answer = 0
    while low <= high:
        mid = (low + high) // 2
        if mid * mid <= n:
            answer = mid           # mid works; look for something bigger
            low = mid + 1
        else:
            high = mid - 1
    return answer

print(isqrt(16), isqrt(17), isqrt(1_000_000_000_000))
# Output: 4 4 1000000
```
**Smallest eating speed** (Koko and the bananas): piles of bananas, `h` hours; each hour she eats from one pile at speed `k`. Find the smallest whole `k` that finishes in `h` hours.
```python
import math

def min_speed(piles, hours):
    def can_finish(speed):
        return sum(math.ceil(p / speed) for p in piles) <= hours

    low, high = 1, max(piles)
    while low < high:
        mid = (low + high) // 2
        if can_finish(mid):
            high = mid             # works; try slower
        else:
            low = mid + 1          # too slow
    return low

print(min_speed([3, 6, 7, 11], 8))
# Output: 4
```
A faster speed always still works, so "can finish?" goes from no to yes exactly once: perfect for binary search. Total cost: `O(n log(max pile))`.

**Real numbers**: stop when the range is smaller than a tolerance.
```python
def sqrt_approx(x, tolerance=1e-9):
    low, high = 0.0, max(1.0, x)
    while high - low > tolerance:
        mid = (low + high) / 2
        if mid * mid < x:
            low = mid
        else:
            high = mid
    return (low + high) / 2

print(round(sqrt_approx(2), 6))
# Output: 1.414214
```
**A recipe for "binary search on the answer"**

1. Define the range of possible answers (`low`, `high`).
2. Write a function `good(x)` that checks one candidate.
3. Make sure `good` is **monotonic** (all "no" then all "yes", or the reverse).
4. Search for the first good (or last good) value.

---

## Searching a Sorted Matrix

A matrix whose **rows are sorted** and **columns are sorted**. Start at the **top-right corner**: if the value is too big move **left**, if too small move **down**. Each step removes a row or a column.

```python
def search_matrix(matrix, target):
    row, col = 0, len(matrix[0]) - 1
    while row < len(matrix) and col >= 0:
        value = matrix[row][col]
        if value == target:
            return True
        if value > target:
            col -= 1
        else:
            row += 1
    return False

grid = [
    [1, 4, 7, 11],
    [2, 5, 8, 12],
    [3, 6, 9, 16],
    [10, 13, 14, 17],
]
print(search_matrix(grid, 5), search_matrix(grid, 20))
# Output: True False
```
`O(rows + cols)`. If the matrix is fully sorted (each row begins after the previous row ends), treat it as one long list and use ordinary binary search with `row, col = divmod(mid, cols)`.

---

## Other Ways to Search

| Method | Idea | Needs | Time |
|---|---|---|---|
| **Hash lookup** (`set`, `dict`) | Jump straight to the item using its hash | Hashable items | `O(1)` average |
| **Jump search** | Skip ahead by `√n`, then linear search back | Sorted | `O(√n)` |
| **Exponential search** | Double the index until the target is passed, then binary search | Sorted | `O(log n)`, good for very large or endless sorted data |
| **Interpolation search** | Guess the position from the value (like looking for "Zoya" near the end of a phone book) | Sorted, evenly spread | `O(log log n)` average, `O(n)` worst |
| **Search tree** | Walk down a balanced binary search tree | A tree (see the Trees note) | `O(log n)` |

If you will search **many times**, sort once (or build a set or dictionary once) and then search quickly each time. For a single search in an unsorted list, a linear scan is cheapest, because sorting first costs `O(n log n)`.

---

## Choosing a Search

| Situation | Use |
|---|---|
| Unsorted list, one search | Linear search (`x in items`) |
| Many membership tests | Convert to a `set` once (`O(1)` each) |
| Sorted list, find a value or position | Binary search or `bisect` |
| Sorted list with duplicates, first, last or count | `bisect_left` and `bisect_right` |
| Insert while staying sorted | `insort` (or a heap / balanced tree if you do it very often) |
| "Smallest or largest value that satisfies a condition" | Binary search on the answer |
| Need the key → value mapping | `dict` |

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Binary search on an unsorted list | Wrong answer, no error | Sort first, or use linear search |
| `while low < high` with inclusive bounds | Misses the last candidate | `while low <= high` (or switch to a half-open template consistently) |
| `low = mid` or `high = mid` in the inclusive version | Infinite loop | `mid + 1` / `mid - 1` |
| Mixing the two templates (inclusive `high = len - 1` with `high = mid`) | Subtle off-by-one bugs | Pick one template and keep all its parts |
| Slicing in a recursive version | `O(n)` copy at each step | Pass `low` and `high` |
| Expecting the first match when there are duplicates | Gets any match | `bisect_left` / `lower_bound` |
| Forgetting `i < len(items)` after `bisect_left` | `IndexError` when the target is bigger than everything | Check the bound before indexing |
| Using binary search once on unsorted data after sorting each time | `O(n log n)` per query | Sort once, search many times |
| `bisect` with `key=` on Python older than 3.10 | `TypeError` | Search a separate list of keys |
| Binary search on the answer with a non-monotonic check | Meaningless result | Verify "no, no, ..., yes, yes" behaviour |
| Floating-point `mid == target` | Never exactly equal | Compare with a tolerance |
| Calling `insort` in a big loop | `O(n)` per insert | Collect, then sort once; or use `heapq` |

---

- [Back to section index](./README.md)
