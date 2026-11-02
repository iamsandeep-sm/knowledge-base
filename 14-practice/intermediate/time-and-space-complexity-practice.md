# Time and Space Complexity Practice (Intermediate)

Related notes: [Time and Space Complexity](../../13-dsa-with-python/time-and-space-complexity.md)

> Intermediate: comfortable with the basics, now combining ideas. **Questions only, no solutions.** Write and run your own code, and test it on empty, single-item and large inputs.

## Practice Questions

1. State the time complexity of each: (a) one loop over `n` items, (b) two nested loops that both run `n` times, (c) a loop where `i` starts at 1 and doubles each time until it passes `n`, (d) a loop over `i` in `range(n)` with an inner loop over `j` in `range(i, n)`.
2. Give the Big-O of each operation on a Python `list`, `set`, `dict` and `deque`: membership test, `append`, insert at the front, remove from the front, access by index or key.
3. Simplify each into Big-O form: `3n² + 5n + 100`, `n + log n`, `2ⁿ + n³`, `n * m` (where `m` is a separate input), `500`.
4. Explain in your own words why `list.append` is called `O(1)` amortized even though an occasional append has to copy the whole list.
5. Write three functions that check whether a list contains a duplicate: (a) nested loops, (b) a `set`, (c) sorting first. State the time and space complexity of each.
6. What is the time and space complexity of a recursive `factorial(n)` compared with a loop-based one?
7. Binary search on 1,000,000 sorted items needs at most about how many steps? Show how you got the number, and write code that confirms it.
8. Write a function that counts the number of loop steps when `i` doubles each time until it passes `n`. Compare its result with `math.log2(n)` for `n = 10, 1000, 10**6`.
9. Using `timeit` or `time.perf_counter`, measure `x in some_list` against `x in some_set` for sizes 1,000, 10,000 and 100,000 (search for a missing value). Describe how each time grows.
10. Compare the space complexity of building `[i * i for i in range(n)]` with a generator expression `(i * i for i in range(n))` when you only need to loop over the values once.
11. What are the best, average and worst cases of linear search? Of insertion sort? Give an input for each case.
12. Why is `"".join(parts)` preferred over `result += part` in a loop? Measure both for 100,000 short strings.
13. For `n = 100,000` and a time budget of about one second in Python, which of `O(n)`, `O(n log n)`, `O(n²)` are realistic? Explain.
14. Rank from fastest-growing to slowest-growing: `O(n!)`, `O(n²)`, `O(log n)`, `O(2ⁿ)`, `O(n log n)`, `O(1)`, `O(n)`. Print a table of their values for `n = 10, 20, 30` for the ones you can compute.
15. What is the complexity of `sum(range(n))`? Write an `O(1)` alternative and prove it matches for several values.
16. Take any function you wrote earlier in this course and write down its time and space complexity with a one-line reason for each.

---

- [Back to practice index](../README.md)
