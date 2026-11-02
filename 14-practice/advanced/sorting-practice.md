# Sorting Practice (Advanced)

Related notes: [Sorting](../../13-dsa-with-python/sorting.md)

> Advanced: harder problems that need a clear plan before coding. **Questions only, no solutions.** Write and run your own code, and test it on empty, single-item and large inputs.

## Practice Questions

1. Arrange a list of non-negative integers to form the **largest possible number** when joined. Example: `[3, 30, 34, 5, 9]` gives `"9534330"`. Use `functools.cmp_to_key`.
2. Count the **inversions** in a list (pairs `i < j` with `a[i] > a[j]`) using merge sort in `O(n log n)`.
3. Implement **quickselect** to find the `k`-th smallest in expected `O(n)`. Explain why a random pivot matters.
4. Sort a singly linked list in `O(n log n)` time using merge sort.
5. Merge overlapping intervals and then find the **minimum number of meeting rooms** needed for a list of meetings.
6. Given an alien alphabet order string, check whether a list of words is sorted in that order.
7. Implement **heap sort** in place (build a max-heap, then repeatedly swap and sift down).
8. Implement **radix sort** for non-negative integers using a stable counting sort for each digit.
9. Find the `k` most frequent elements in `O(n log k)` with a heap, then in `O(n)` using bucket sort.
10. Rearrange a list so that `a[0] <= a[1] >= a[2] <= a[3] ...` (wiggle sort), in one pass.
11. Find the minimum number of swaps needed to sort a list of distinct numbers (cycle counting).
12. Sort a list where each element is at most `k` places away from its sorted position, in `O(n log k)`.
13. Sort array 1 so its elements follow the relative order of array 2; elements missing from array 2 go at the end in ascending order.
14. Benchmark your own bubble, insertion, merge and quick sort against `sorted()` for sizes 1,000 / 5,000 / 20,000, and explain the results.
15. Write an external-sort simulation: sort a very large list by splitting it into sorted chunks and merging them with `heapq.merge`.

---

- [Back to practice index](../README.md)
