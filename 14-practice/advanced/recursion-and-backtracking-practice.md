# Recursion and Backtracking Practice (Advanced)

Related notes: [Recursion and Backtracking](../../13-dsa-with-python/recursion-and-backtracking.md)

> Advanced: harder problems that need a clear plan before coding. **Questions only, no solutions.** Write and run your own code, and test it on empty, single-item and large inputs.

## Practice Questions

1. **N-Queens**: return all solutions for `n = 6`, and the count of solutions for `n = 4..9`. Use sets for columns and diagonals.
2. Write a **Sudoku solver** using backtracking. Explain how checking rows, columns and boxes in `O(1)` speeds it up.
3. **Word search** in a grid: does a word exist as a path of adjacent letters, using each cell at most once? Then find *all* words from a list (hint: a trie).
4. **Combination sum II**: choose numbers from a list that may contain duplicates, each used at most once, to reach a target, with no duplicate combinations.
5. **Palindrome partitioning**: return every way to split a string into palindromic pieces.
6. Restore IP addresses: from `"25525511135"` return all valid IPv4 addresses.
7. **Subsets II** and **Permutations II**: handle input lists that contain duplicate values without producing duplicate results.
8. Rat in a maze: print all paths from the top-left to the bottom-right through open cells (`1`) moving in four directions without revisiting.
9. Partition a list into `k` subsets of equal sum. Add pruning so it finishes quickly for 16 numbers.
10. Expression add operators: insert `+`, `-` or `*` between digits of `"123"` so the result equals a target. Example: target `6` gives `"1+2+3"` and `"1*2*3"`.
11. Generate the **Gray code** sequence for `n` bits recursively and with the formula `i ^ (i >> 1)`.
12. Minimum coins: write the plain recursion, then the memoised version, then a bottom-up loop for `coins = [1, 5, 10, 25]` and amount `63`. Compare the number of calls.
13. **Word break**: can a string be segmented into dictionary words? Write it with memoisation and count the subproblems.
14. **Decode ways**: how many ways can `"226"` be decoded as letters (`A=1 ... Z=26`)?
15. Generate all **generalised abbreviations** of a word. Example: `"word"` has 16 abbreviations.
16. Knight's tour on a 5x5 board: find one tour that visits every square once. Compare plain backtracking with Warnsdorff's heuristic.
17. Convert a recursive depth-first search into an iterative one with an explicit stack, and show that it handles an input that makes the recursive version fail.

---

- [Back to practice index](../README.md)
