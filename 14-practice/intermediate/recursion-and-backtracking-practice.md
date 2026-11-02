# Recursion and Backtracking Practice (Intermediate)

Related notes: [Recursion and Backtracking](../../13-dsa-with-python/recursion-and-backtracking.md)

> Intermediate: comfortable with the basics, now combining ideas. **Questions only, no solutions.** Write and run your own code, and test it on empty, single-item and large inputs.

## Practice Questions

1. Write recursive functions for factorial, the sum of the first `n` numbers, and `power(x, n)`. Add input validation.
2. Compute Fibonacci recursively, count how many calls `fib(20)` makes, then fix it with `functools.cache` and compare the call counts.
3. Reverse a string recursively. Then check for a palindrome recursively.
4. Write recursive `digit_sum(n)` and `count_digits(n)`.
5. Compute the greatest common divisor with Euclid's algorithm, recursively.
6. Flatten an arbitrarily nested list such as `[1, [2, [3, [4]]], 5]`.
7. Print the binary representation of a positive integer recursively.
8. Write a recursive binary search. What is the maximum recursion depth for 1,000,000 items?
9. Generate every **subset** of a list.
10. Generate every **permutation** of a list. Then generate only the **unique** permutations of `"aab"`.
11. Generate all combinations of `k` numbers chosen from `1..n`.
12. Generate all valid strings of `n` pairs of parentheses.
13. Solve the Tower of Hanoi for `n` disks and print every move. Verify the move count equals `2ⁿ - 1`.
14. Letter combinations of a phone number: `"23"` gives `ad, ae, af, bd, be, bf, cd, ce, cf`.
15. Climbing stairs: how many ways to climb `n` steps taking 1 or 2 steps at a time? Write the plain recursion, the memoised one and the loop.
16. Count the paths from the top-left to the bottom-right of an `m x n` grid moving only right or down. Add a version with blocked cells.
17. Generate all binary strings of length `n` that have no two consecutive `1`s.
18. Trigger a `RecursionError` on purpose, catch it, and print `sys.getrecursionlimit()`. Then rewrite the function as a loop.
19. Write a recursive function that lists all files in a folder tree (using `os.listdir` or `pathlib`), indented by depth.

---

- [Back to practice index](../README.md)
