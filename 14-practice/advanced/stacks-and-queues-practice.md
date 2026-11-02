# Stacks and Queues Practice (Advanced)

Related notes: [Stacks and Queues](../../13-dsa-with-python/stacks-and-queues.md)

> Advanced: harder problems that need a clear plan before coding. **Questions only, no solutions.** Write and run your own code, and test it on empty, single-item and large inputs.

## Practice Questions

1. Largest rectangle in a histogram: `[2, 1, 5, 6, 2, 3]` gives `10`. Solve in `O(n)` with a monotonic stack.
2. Sliding window maximum: for window size `k`, return the maximum of each window using a `deque`, in `O(n)`.
3. Basic calculator: evaluate expressions containing integers, `+`, `-` and nested parentheses, such as `"1 + (2 - (3 + 4))"`.
4. Trapping rain water using a monotonic stack (rather than two pointers).
5. Simplify a Unix path such as `"/a/./b/../../c/"` into its canonical form `"/c"`.
6. Asteroid collision: positive numbers move right, negative move left; larger asteroids destroy smaller ones. Simulate with a stack.
7. Online stock span: design a class whose `next(price)` returns the number of consecutive previous days with price `<=` today's.
8. Design a **max stack** supporting `push`, `pop`, `top`, `peek_max` and `pop_max`.
9. Longest valid parentheses substring: `")()())"` gives `4`. Solve with a stack and then with `O(1)` space.
10. Remove `k` digits from a number string to make the smallest possible number. Example: `"1432219"`, `k = 3` gives `"1219"`.
11. Sum of subarray minimums: compute the sum of the minimum of every contiguous subarray in `O(n)`.
12. Implement a circular **double-ended** queue (deque) from scratch with a fixed array.
13. Task scheduler: given tasks with a cooldown `n` between identical tasks, compute the minimum time (use a heap, then a formula).
14. Build a priority queue with `heapq` that supports tasks of the same priority in insertion order, and supports **changing** a task's priority.
15. Implement a **rate limiter** that allows at most `n` calls in any rolling window of `t` seconds, using a deque.

---

- [Back to practice index](../README.md)
