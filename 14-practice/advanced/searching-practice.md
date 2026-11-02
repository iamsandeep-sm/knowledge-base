# Searching Practice (Advanced)

Related notes: [Searching](../../13-dsa-with-python/searching.md)

> Advanced: harder problems that need a clear plan before coding. **Questions only, no solutions.** Write and run your own code, and test it on empty, single-item and large inputs.

## Practice Questions

1. Search for a target in a **rotated sorted list** of distinct numbers in `O(log n)`. Example: `[4, 5, 6, 7, 0, 1, 2]`, target `0` gives `4`.
2. Repeat the previous problem when the list may contain **duplicates**. Explain why the worst case becomes `O(n)`.
3. Koko eating bananas: piles `[3, 6, 7, 11]` must be eaten within `h` hours; she eats at most `k` bananas per hour from a single pile. Find the smallest `k` using binary search on the answer.
4. Capacity to ship packages within `d` days: weights in order, find the smallest ship capacity. Explain the feasibility check.
5. Split array largest sum: split a list into `m` contiguous parts to minimise the largest part sum.
6. Median of two sorted arrays in `O(log(min(m, n)))`.
7. Find the `k`-th smallest element in a matrix whose rows and columns are sorted.
8. Search in a sorted list of **unknown (infinite) size** using exponential search, then binary search.
9. Aggressive cows: place `c` cows in stalls at given positions so that the **minimum** distance between any two is as large as possible.
10. Implement a time-based key-value store `set(key, value, timestamp)` / `get(key, timestamp)` using `bisect`.
11. Find the cube root of a number to 6 decimal places using binary search on floats. Explain why a loop count is safer than comparing floats for equality.
12. Find the single element that appears once in a sorted list where every other element appears exactly twice, in `O(log n)`.
13. Find the smallest missing positive integer in an unsorted list in `O(n)` time and `O(1)` extra space.
14. Given a sorted list of integers and `k`, find the `k` closest elements to a value `x` using binary search plus a window.

---

- [Back to practice index](../README.md)
