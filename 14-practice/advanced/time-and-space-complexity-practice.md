# Time and Space Complexity Practice (Advanced)

Related notes: [Time and Space Complexity](../../13-dsa-with-python/time-and-space-complexity.md)

> Advanced: harder problems that need a clear plan before coding. **Questions only, no solutions.** Write and run your own code, and test it on empty, single-item and large inputs.

## Practice Questions

1. Show that a dynamic array that **doubles** its capacity when full performs fewer than `2n` element copies in total over `n` appends. Then work out what happens if it grows by a fixed `+10` slots instead.
2. Find the exact number of inner-loop steps (as a formula) for: (a) `for i in range(n): for j in range(i): ...`, (b) `for i in range(n): for j in range(i * i): ...`. Give the Big-O of each and confirm with code.
3. Solve each recurrence and say which algorithm it looks like: `T(n) = T(n/2) + 1`, `T(n) = 2T(n/2) + n`, `T(n) = 2T(n-1) + 1`, `T(n) = T(n-1) + T(n-2)`.
4. What is the time complexity of generating **and printing** every subset of an `n`-item list? Why is it not just `O(2ⁿ)`?
5. Explain why building a heap with `heapify` is `O(n)` while `n` calls to `heappush` is `O(n log n)`. Time both for 1,000,000 numbers.
6. What is the **space** complexity of recursive DFS on (a) a balanced binary tree and (b) a tree that is a single long chain? Show a recursion-limit failure for (b).
7. Explain the difference between `O`, `Ω` and `Θ`. Decide which of these are true: `n² = O(n³)`, `n² = Ω(n)`, `n log n = Θ(n)`, `2ⁿ = O(3ⁿ)`.
8. An amortized-analysis puzzle: a binary counter is incremented `n` times. Show that the total number of bit flips is `O(n)`, so each increment is `O(1)` amortized. Write a simulation that counts the flips.
9. What is the worst-case lookup cost in a dictionary if every key had the same hash? Build a class that causes it and measure the slowdown for 1,000 versus 5,000 keys.
10. You must answer `q` queries on an array of size `n`. Compare the total cost of (a) scanning for each query, (b) sorting once and using binary search, (c) building a hash map once. For which values of `q` does each win?
11. A function runs in `O(n)` time but its input is a number `n` given in **binary digits**. Explain why this is sometimes called pseudo-polynomial, using an example such as counting up to `n`.
12. Design an experiment that measures running time for sizes doubling from 1,000 up to 1,024,000 and uses the ratio of consecutive times to guess whether a function is `O(n)`, `O(n log n)` or `O(n²)`. Test it on three functions you write.

---

- [Back to practice index](../README.md)
