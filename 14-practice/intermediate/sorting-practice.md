# Sorting Practice (Intermediate)

Related notes: [Sorting](../../13-dsa-with-python/sorting.md)

> Intermediate: comfortable with the basics, now combining ideas. **Questions only, no solutions.** Write and run your own code, and test it on empty, single-item and large inputs.

## Practice Questions

1. Sort a list of `(name, age)` tuples by age, then sort them by age **descending** and name ascending.
2. Implement bubble sort with an early exit when no swaps happen. Count the swaps and comparisons.
3. Implement selection sort. Why does it always do `O(n²)` comparisons?
4. Implement insertion sort. Why is it fast on nearly sorted data? Measure it against a reversed list.
5. Implement merge sort (return a new list) and write the `merge` helper separately.
6. Implement quick sort in two ways: a list-comprehension version and an in-place version with a partition function.
7. Implement counting sort for a list of digits `0-9`. When is counting sort a good choice?
8. Sort strings by length, then alphabetically. Then sort them ignoring case.
9. Show with an example what **stable** sorting means and why it matters when sorting by two keys one after the other.
10. Sort the characters of a string by frequency (most common first), breaking ties alphabetically.
11. Sort a list containing only `0`, `1` and `2` in a single pass with no extra list (Dutch national flag).
12. Check whether a list is sorted. Then check whether it can be sorted by swapping exactly one pair.
13. Merge two sorted lists into one without calling a sort function.
14. Find the `k`-th largest element in three ways: sorting, `heapq.nlargest`, and a min-heap of size `k`.
15. Sort a list of dictionaries (students with `name` and `marks`) by marks descending, then by name.
16. Sort a list of version strings such as `"1.10.2"` and `"1.9"` correctly (numeric parts, not text).
17. Write a function `is_permutation_sorted(a, b)` that checks whether `b` is just `a` sorted, without calling `sorted(a)`.

---

- [Back to practice index](../README.md)
