# Searching Practice (Intermediate)

Related notes: [Searching](../../13-dsa-with-python/searching.md)

> Intermediate: comfortable with the basics, now combining ideas. **Questions only, no solutions.** Write and run your own code, and test it on empty, single-item and large inputs.

## Practice Questions

1. Write `linear_search(items, target)` that returns the index of the first match or `-1`. Then write a version that returns **every** index.
2. Write an iterative `binary_search(items, target)`. Test it on an empty list, a one-item list, a missing value, the first item and the last item.
3. Write a recursive binary search. Compare it with the iterative one in terms of space.
4. In a sorted list with duplicates, find the **first** and **last** index of a target. Example: `[1, 2, 2, 2, 3]`, target `2` gives `(1, 3)`.
5. Count how many times a value appears in a sorted list using `bisect_left` and `bisect_right`.
6. Return the index where a target should be inserted to keep a sorted list sorted. Check it against `bisect`.
7. Compute the integer square root of `n` using binary search (no `math.sqrt`). Example: `isqrt(17)` is `4`.
8. Given a sorted list of letters, find the smallest letter greater than a target, wrapping around to the first. Example: `['c', 'f', 'j']`, target `'j'` gives `'c'`.
9. Find the minimum in a sorted list that has been rotated. Example: `[4, 5, 6, 7, 0, 1, 2]` gives `0`.
10. Find any peak element (greater than its neighbours) in `O(log n)`. Example: `[1, 3, 20, 4, 1, 0]` has a peak at index `2`.
11. Find how many times a sorted list was rotated.
12. Check whether `n` is a perfect square using binary search only.
13. Find the element in a sorted list that is closest to a target value.
14. Search for a value in a matrix whose rows and columns are both sorted (the staircase method). State the complexity.
15. Measure the time of linear search versus binary search on 1,000,000 sorted numbers for 100 random targets. What do you notice?
16. Write a function that finds the **smallest** value `x` in `[0, 10**9]` for which a given monotonic `True/False` function becomes `True`, using binary search.

---

- [Back to practice index](../README.md)
