# Sorting - Revision Notes ~ sm

> **In one line:** **Sorting** puts items in order (smallest to largest, A to Z, by date...). In real Python code you almost always call the built-in **`sorted()`** or **`list.sort()`** (fast and **stable**, `O(n log n)`), but you should understand how the classic algorithms work: **bubble, selection and insertion** sort (`O(n²)`), and **merge, quick and heap** sort (`O(n log n)`), plus **counting sort** for small whole-number ranges.

## Table of Contents
- [Why Sort?](#why-sort)
- [Vocabulary](#vocabulary)
- [Python's Built-in Sorting](#pythons-built-in-sorting)
- [Bubble Sort](#bubble-sort)
- [Selection Sort](#selection-sort)
- [Insertion Sort](#insertion-sort)
- [Merge Sort](#merge-sort)
- [Quick Sort](#quick-sort)
- [Heap Sort](#heap-sort)
- [Counting Sort](#counting-sort)
- [Comparing the Algorithms](#comparing-the-algorithms)
- [Problems Solved by Sorting](#problems-solved-by-sorting)
- [Common Mistakes](#common-mistakes)

---

## Why Sort?

- Sorted data can be searched with **binary search** (`O(log n)`).
- Duplicates and equal items end up **next to each other**.
- Many problems become simple after sorting: closest pair, merging intervals, the median, grouping.
- People like to read ordered data.

---

## Vocabulary

| Term | Meaning |
|---|---|
| **In-place** | Sorts inside the same list, using `O(1)` extra memory (or very little) |
| **Stable** | Items that compare **equal keep their original order** |
| **Comparison sort** | Decides the order only by comparing pairs. **No** comparison sort can beat `O(n log n)` in the worst case |
| **Adaptive** | Gets faster when the input is already nearly sorted |
| **Internal vs external** | All data fits in memory vs data too big, read from disk in pieces |

**Why stability matters.** Sort people by **age**, then by **city**. If the second sort is stable, people from the same city stay in age order.

---

## Python's Built-in Sorting

```python
nums = [5, 2, 9, 1, 7]

print(sorted(nums))
print(sorted(nums, reverse=True))
print(nums)
nums.sort()
print(nums)
# Output: [1, 2, 5, 7, 9]
# Output: [9, 7, 5, 2, 1]
# Output: [5, 2, 9, 1, 7]
# Output: [1, 2, 5, 7, 9]
```
| | `sorted(iterable)` | `list.sort()` |
|---|---|---|
| Returns | A **new list** | `None` (changes the list **in place**) |
| Works on | Any iterable (list, tuple, string, set, dict keys) | Lists only |
| Original | Unchanged | Changed |

Both use **Timsort**: a hybrid of merge sort and insertion sort that is **stable**, `O(n log n)` in the worst case, and close to `O(n)` on data that is already (nearly) sorted. For ordinary use it is the best choice.

**`key=` turns each item into the value used for comparing**
```python
words = ["banana", "kiwi", "apple", "fig"]
print(sorted(words, key=len))
print(sorted(words, key=lambda w: w[-1]))
print(sorted(["banana", "apple", "Cherry"]))
print(sorted(["banana", "apple", "Cherry"], key=str.lower))
# Output: ['fig', 'kiwi', 'apple', 'banana']
# Output: ['banana', 'apple', 'fig', 'kiwi']
# Output: ['Cherry', 'apple', 'banana']
# Output: ['apple', 'banana', 'Cherry']
```
The key function is called **once per item**. Capital letters sort before lowercase ones, because comparison is by character code.

**Numbers stored as text sort alphabetically unless you convert them**
```python
print(sorted(["10", "9", "2"]))
print(sorted(["10", "9", "2"], key=int))
# Output: ['10', '2', '9']
# Output: ['2', '9', '10']
```
**Stability, shown**
```python
records = [("Riya", "B"), ("Amit", "A"), ("Zoya", "B"), ("Kabir", "A")]
print(sorted(records, key=lambda r: r[1]))
# Output: [('Amit', 'A'), ('Kabir', 'A'), ('Riya', 'B'), ('Zoya', 'B')]
```
Within grade `A`, Amit still comes before Kabir (original order), and Riya before Zoya in `B`.

**Several criteria**: use a tuple key, with a minus sign to reverse a number.
```python
people = [("Riya", 25), ("Amit", 30), ("Zoya", 25), ("Kabir", 30)]
print(sorted(people, key=lambda p: (-p[1], p[0])))
# Output: [('Amit', 30), ('Kabir', 30), ('Riya', 25), ('Zoya', 25)]
```
Oldest first, and for the same age, by name. Or use the **two-pass trick**, because sorting is stable: sort by the **least** important key first, then by the most important:
```python
people = [("Riya", 25), ("Amit", 30), ("Zoya", 25), ("Kabir", 30)]
people.sort(key=lambda p: p[0])                 # by name
people.sort(key=lambda p: p[1], reverse=True)   # then by age, oldest first
print(people)
# Output: [('Amit', 30), ('Kabir', 30), ('Riya', 25), ('Zoya', 25)]
```
**A custom comparison** (rare): `functools.cmp_to_key` turns an old-style compare function (negative, zero or positive) into a key. Example: arrange numbers to make the biggest possible number.
```python
from functools import cmp_to_key

def compare(a, b):
    if a + b > b + a:
        return -1          # a should come first
    if a + b < b + a:
        return 1
    return 0

numbers = [3, 30, 34, 5, 9]
ordered = sorted(map(str, numbers), key=cmp_to_key(compare))
print("".join(ordered))
# Output: 9534330
```
**Sorting different types fails**
```python
sorted([3, "a", 1])
# Error: TypeError
```
Numbers and strings cannot be compared with `<`. Convert them first or use a key.

**Sorting other things**
```python
scores = {"Riya": 90, "Amit": 75, "Zoya": 82}
print(sorted(scores))
print(sorted(scores.items(), key=lambda item: item[1], reverse=True))
print(sorted("python"))
# Output: ['Amit', 'Riya', 'Zoya']
# Output: [('Riya', 90), ('Zoya', 82), ('Amit', 75)]
# Output: ['h', 'n', 'o', 'p', 't', 'y']
```
**Top-`k` without sorting everything:** `heapq.nlargest(k, items)` and `heapq.nsmallest(k, items)` are faster than a full sort when `k` is small.
```python
import heapq

nums = [5, 1, 8, 3, 9, 2]
print(heapq.nlargest(3, nums), heapq.nsmallest(2, nums))
# Output: [9, 8, 5] [1, 2]
```

---

## Bubble Sort

Compare **neighbouring** pairs and swap them if they are in the wrong order. After each full pass the **largest remaining item has "bubbled" to its final place** at the end.

```python
def bubble_sort(items):
    n = len(items)
    for end in range(n - 1, 0, -1):
        swapped = False
        for i in range(end):
            if items[i] > items[i + 1]:
                items[i], items[i + 1] = items[i + 1], items[i]
                swapped = True
        if not swapped:                 # a pass without swaps: already sorted
            break
    return items

print(bubble_sort([5, 1, 4, 2, 8]))
# Output: [1, 2, 4, 5, 8]
```
Passes, step by step, for `[5, 1, 4, 2, 8]`:

| Pass | List after the pass |
|---|---|
| Start | `[5, 1, 4, 2, 8]` |
| 1 | `[1, 4, 2, 5, 8]` (5 bubbled up to just before 8) |
| 2 | `[1, 2, 4, 5, 8]` |
| 3 | no swaps: finished |

| | |
|---|---|
| Time | `O(n²)` worst and average; `O(n)` best (already sorted, thanks to the `swapped` check) |
| Space | `O(1)` |
| Stable | Yes |

Easy to understand, but too slow for real use. Count the comparisons on a reversed list of 100 items:
```python
def bubble_comparisons(items):
    items = items[:]
    comparisons = 0
    for end in range(len(items) - 1, 0, -1):
        for i in range(end):
            comparisons += 1
            if items[i] > items[i + 1]:
                items[i], items[i + 1] = items[i + 1], items[i]
    return comparisons

print(bubble_comparisons(list(range(100, 0, -1))))
# Output: 4950
```
`100 * 99 / 2 = 4950`: quadratic growth.

---

## Selection Sort

Repeatedly **select the smallest** item of the unsorted part and **swap it into the next position** of the sorted part.

```python
def selection_sort(items):
    n = len(items)
    for i in range(n - 1):
        smallest = i
        for j in range(i + 1, n):
            if items[j] < items[smallest]:
                smallest = j
        items[i], items[smallest] = items[smallest], items[i]
    return items

print(selection_sort([64, 25, 12, 22, 11]))
# Output: [11, 12, 22, 25, 64]
```
| | |
|---|---|
| Time | `O(n²)` in **every** case (it always scans the rest) |
| Space | `O(1)` |
| Stable | **No** (the long-distance swap can reorder equal items) |
| Good point | At most `n - 1` swaps: useful when writing to memory is very expensive |

---

## Insertion Sort

Like sorting playing cards in your hand: take the next item and **insert it into its correct place among the already-sorted items on its left**, shifting bigger ones to the right.

```python
def insertion_sort(items):
    for i in range(1, len(items)):
        current = items[i]
        j = i - 1
        while j >= 0 and items[j] > current:
            items[j + 1] = items[j]         # shift right
            j -= 1
        items[j + 1] = current
    return items

print(insertion_sort([12, 11, 13, 5, 6]))
# Output: [5, 6, 11, 12, 13]
```
Trace for `[12, 11, 13, 5, 6]`:

| After inserting | List (sorted part first) |
|---|---|
| `11` | `[11, 12, 13, 5, 6]` |
| `13` | `[11, 12, 13, 5, 6]` (already in place) |
| `5` | `[5, 11, 12, 13, 6]` |
| `6` | `[5, 6, 11, 12, 13]` |

| | |
|---|---|
| Time | `O(n²)` worst and average; **`O(n)` on nearly sorted data** (adaptive) |
| Space | `O(1)` |
| Stable | Yes |
| Good point | Very fast for **small** lists and nearly sorted data. Timsort uses it for short runs |

```python
def insertion_comparisons(items):
    items = items[:]
    comparisons = 0
    for i in range(1, len(items)):
        current = items[i]
        j = i - 1
        while j >= 0:
            comparisons += 1
            if items[j] <= current:
                break
            items[j + 1] = items[j]
            j -= 1
        items[j + 1] = current
    return comparisons

print(insertion_comparisons(list(range(100))))
print(insertion_comparisons(list(range(100, 0, -1))))
# Output: 99
# Output: 4950
```
Already sorted: 99 comparisons (linear). Reversed: 4950 (quadratic).

---

## Merge Sort

**Divide and conquer:** split the list in half, sort each half **recursively**, then **merge** the two sorted halves into one sorted list.

```python
def merge(left, right):
    result = []
    i = j = 0
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:            # <= keeps the sort stable
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    result.extend(left[i:])                # one side may have leftovers
    result.extend(right[j:])
    return result

def merge_sort(items):
    if len(items) <= 1:                    # base case
        return items
    mid = len(items) // 2
    return merge(merge_sort(items[:mid]), merge_sort(items[mid:]))

print(merge([1, 4, 7], [2, 3, 9]))
print(merge_sort([38, 27, 43, 3, 9, 82, 10]))
# Output: [1, 2, 3, 4, 7, 9]
# Output: [3, 9, 10, 27, 38, 43, 82]
```
**Splitting and merging** for `[38, 27, 43, 3]`:
```text
[38, 27, 43, 3]
 /            \
[38, 27]    [43, 3]          split until single items
 /   \       /   \
[38] [27]  [43]  [3]
 \   /       \   /
[27, 38]    [3, 43]          merge pairs
     \        /
[3, 27, 38, 43]              merge the halves
```
**Why `O(n log n)`:** the list can be halved only `log n` times (the number of levels), and at each level the merges handle **all `n` items once**: `n * log n`.

| | |
|---|---|
| Time | `O(n log n)` in **every** case |
| Space | `O(n)` extra (the merged lists) |
| Stable | Yes |
| Good point | Predictable; great for linked lists and for data that does not fit in memory (external sorting) |

---

## Quick Sort

Choose a **pivot**. Move everything smaller to its left and everything larger to its right (**partition**). The pivot is now in its final place. Then sort the left and right parts recursively.

**Simple version** (easy to read, uses extra memory)
```python
def quick_sort_simple(items):
    if len(items) <= 1:
        return items
    pivot = items[len(items) // 2]
    smaller = [x for x in items if x < pivot]
    equal = [x for x in items if x == pivot]
    larger = [x for x in items if x > pivot]
    return quick_sort_simple(smaller) + equal + quick_sort_simple(larger)

print(quick_sort_simple([10, 7, 8, 9, 1, 5, 3, 7]))
# Output: [1, 3, 5, 7, 7, 8, 9, 10]
```
**In-place version** (Lomuto partition, random pivot)
```python
import random

def partition(items, low, high):
    pivot_index = random.randint(low, high)               # random pivot
    items[pivot_index], items[high] = items[high], items[pivot_index]
    pivot = items[high]
    boundary = low                                        # items[low:boundary] are < pivot
    for i in range(low, high):
        if items[i] < pivot:
            items[i], items[boundary] = items[boundary], items[i]
            boundary += 1
    items[boundary], items[high] = items[high], items[boundary]
    return boundary                                       # the pivot's final index

def quick_sort(items, low=0, high=None):
    if high is None:
        high = len(items) - 1
    if low < high:
        p = partition(items, low, high)
        quick_sort(items, low, p - 1)
        quick_sort(items, p + 1, high)
    return items

print(quick_sort([10, 7, 8, 9, 1, 5, 3, 7]))
# Output: [1, 3, 5, 7, 7, 8, 9, 10]
```
| | |
|---|---|
| Time | **Average `O(n log n)`**, but **worst `O(n²)`** if the pivot is always the smallest or largest (for example a sorted list with a bad pivot rule) |
| Space | `O(log n)` for the recursion on average (`O(n)` worst) |
| Stable | **No** |
| Good point | Very fast in practice and in-place; a **random** pivot makes the worst case extremely unlikely |

The partition idea also gives **quickselect**: find the `k`-th smallest item without sorting everything, in `O(n)` on average.

---

## Heap Sort

A **heap** keeps the smallest item at the front and can give it up in `O(log n)`. Push everything in, pop everything out, and the items come out in order.

```python
import heapq

def heap_sort(items):
    heap = list(items)
    heapq.heapify(heap)                    # O(n)
    return [heapq.heappop(heap) for _ in range(len(heap))]   # n pops, O(log n) each

print(heap_sort([9, 4, 7, 1, 8, 2]))
# Output: [1, 2, 4, 7, 8, 9]
```
| | |
|---|---|
| Time | `O(n log n)` in every case |
| Space | `O(n)` here (a separate list); the classic version works `O(1)` in place |
| Stable | No |

See the Trees note for how a heap works.

---

## Counting Sort

When the values are **whole numbers in a small range** `0..k`, you do not need comparisons at all: **count** how many times each value appears, then write them out.

```python
def counting_sort(items, max_value):
    counts = [0] * (max_value + 1)
    for x in items:
        counts[x] += 1
    result = []
    for value, count in enumerate(counts):
        result.extend([value] * count)
    return result

print(counting_sort([4, 2, 2, 8, 3, 3, 1], 8))
# Output: [1, 2, 2, 3, 3, 4, 8]
```
| | |
|---|---|
| Time | `O(n + k)` where `k` is the range of values |
| Space | `O(k)` |
| Good when | `k` is not much bigger than `n` (ages, marks out of 100, letters) |
| Bad when | The range is huge (sorting `[1, 1000000000]` would allocate a billion counters) |

Related: **radix sort** (sort digit by digit) and **bucket sort** (spread into buckets, sort each).

---

## Comparing the Algorithms

| Algorithm | Best | Average | Worst | Space | Stable | In-place |
|---|---|---|---|---|---|---|
| Bubble | `O(n)` | `O(n²)` | `O(n²)` | `O(1)` | Yes | Yes |
| Selection | `O(n²)` | `O(n²)` | `O(n²)` | `O(1)` | No | Yes |
| Insertion | `O(n)` | `O(n²)` | `O(n²)` | `O(1)` | Yes | Yes |
| Merge | `O(n log n)` | `O(n log n)` | `O(n log n)` | `O(n)` | Yes | No |
| Quick | `O(n log n)` | `O(n log n)` | `O(n²)` | `O(log n)` | No | Yes |
| Heap | `O(n log n)` | `O(n log n)` | `O(n log n)` | `O(1)` (classic) | No | Yes |
| Counting | `O(n + k)` | `O(n + k)` | `O(n + k)` | `O(k)` | Can be | No |
| **Timsort** (Python) | `O(n)` | `O(n log n)` | `O(n log n)` | `O(n)` | **Yes** | No |

**Which to use?**
- In Python code: `sorted()` / `.sort()`. Do not write your own unless you are learning or the task requires it.
- Tiny or nearly sorted data: insertion sort.
- Need a guarantee and stability: merge sort.
- Need speed and low memory, and can tolerate the rare slow case: quick sort with a random pivot.
- Small range of integers: counting sort.

---

## Problems Solved by Sorting

**Merge overlapping intervals**: sort by start, then sweep.
```python
def merge_intervals(intervals):
    intervals = sorted(intervals)
    merged = [intervals[0]]
    for start, end in intervals[1:]:
        if start <= merged[-1][1]:                       # overlaps the last one
            merged[-1][1] = max(merged[-1][1], end)
        else:
            merged.append([start, end])
    return merged

print(merge_intervals([[1, 3], [2, 6], [8, 10], [15, 18]]))
# Output: [[1, 6], [8, 10], [15, 18]]
```
**The `k`-th largest item**
```python
import heapq

nums = [3, 2, 1, 5, 6, 4]
k = 2
print(sorted(nums, reverse=True)[k - 1])        # O(n log n)
print(heapq.nlargest(k, nums)[-1])              # O(n log k)
# Output: 5
# Output: 5
```
**Is there a duplicate? Closest pair?** After sorting, only **neighbours** need checking.
```python
nums = [8, 1, 14, 3, 11]
ordered = sorted(nums)
smallest_gap = min(b - a for a, b in zip(ordered, ordered[1:]))
print(smallest_gap)
# Output: 2
```
**Can one person attend all meetings?** Sort by start time; no meeting may start before the previous one ends.
```python
def can_attend_all(meetings):
    meetings = sorted(meetings)
    return all(a[1] <= b[0] for a, b in zip(meetings, meetings[1:]))

print(can_attend_all([(0, 30), (35, 40)]), can_attend_all([(0, 30), (5, 10)]))
# Output: True False
```
**Group anagrams**: words that are anagrams have the same sorted letters.
```python
from collections import defaultdict

groups = defaultdict(list)
for word in ["eat", "tea", "tan", "ate", "nat", "bat"]:
    groups["".join(sorted(word))].append(word)
print(list(groups.values()))
# Output: [['eat', 'tea', 'ate'], ['tan', 'nat'], ['bat']]
```
**Median**
```python
def median(nums):
    ordered = sorted(nums)
    mid = len(ordered) // 2
    if len(ordered) % 2:
        return ordered[mid]
    return (ordered[mid - 1] + ordered[mid]) / 2

print(median([5, 1, 3]), median([4, 1, 3, 2]))
# Output: 3 2.5
```

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| `nums = nums.sort()` | `nums` becomes `None` | `nums.sort()` or `nums = sorted(nums)` |
| Calling `.sort()` on a tuple or a string | `AttributeError` | `sorted(x)` |
| Sorting numbers stored as strings | `"10" < "9"` | `key=int` |
| Sorting a mixed list of numbers and strings | `TypeError` | Convert, or use a key |
| Forgetting capital letters sort first | `"Zebra"` before `"apple"` | `key=str.lower` (or `str.casefold`) |
| Using `cmp=` (Python 2 style) | `TypeError` | `key=` or `functools.cmp_to_key` |
| Writing your own `O(n²)` sort for big data | Very slow | Use `sorted()` |
| Quick sort with the first or last item as pivot on sorted data | `O(n²)` and deep recursion (`RecursionError`) | Random pivot, or median of three |
| Merge sort with `<` instead of `<=` in `merge` | Loses stability | `<=` |
| Slicing `items[:mid]` in recursion and expecting `O(1)` space | Each slice is a copy | It is fine for learning, use indexes for efficiency |
| Counting sort with a huge range | Memory blow-up | Use a comparison sort |
| Sorting on every loop iteration | `O(n log n)` each time | Sort once; keep sorted with `bisect.insort`, or use a heap |
| Assuming `sorted()` changes the original | It returns a new list | Assign the result |
| Using `reverse=True` and worrying about stability | It **is** still stable (equal items keep original order) | Nothing to fix |

---

- [Back to section index](./README.md)
