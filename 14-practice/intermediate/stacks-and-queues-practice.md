# Stacks and Queues Practice (Intermediate)

Related notes: [Stacks and Queues](../../13-dsa-with-python/stacks-and-queues.md)

> Intermediate: comfortable with the basics, now combining ideas. **Questions only, no solutions.** Write and run your own code, and test it on empty, single-item and large inputs.

## Practice Questions

1. Check whether a string of brackets `()[]{}` is balanced. Test with `"{[()]}"`, `"([)]"` and `"(("`.
2. Reverse a string and reverse the words of a sentence using a stack.
3. Write a `Stack` class with `push`, `pop`, `peek`, `is_empty` and `size` that raises a clear error when popping from an empty stack.
4. Write a `Queue` class using `collections.deque` with `enqueue`, `dequeue`, `front` and `is_empty`. Why is `list.pop(0)` a poor choice?
5. Implement a queue using **two stacks**. State the amortized cost of each operation.
6. Implement a stack using **two queues**.
7. Design a `MinStack` that supports `push`, `pop`, `top` and `get_min`, all in `O(1)`.
8. Evaluate a postfix expression. Example: `"2 3 + 4 *"` gives `20`.
9. Convert an infix expression with `+ - * /` and parentheses to postfix.
10. For each number in a list, find the **next greater element** to its right (or `-1`). Example: `[2, 1, 2, 4, 3]` gives `[4, 2, 4, -1, -1]`.
11. Daily temperatures: for each day, how many days until a warmer day? Example: `[73, 74, 75, 71, 69, 72, 76, 73]` gives `[1, 1, 4, 2, 1, 1, 0, 0]`.
12. Remove all adjacent duplicate characters repeatedly. Example: `"abbaca"` gives `"ca"`.
13. Decode a string such as `"3[a2[c]]"` into `"accaccacc"`.
14. Implement a fixed-size **circular queue** with `enqueue`, `dequeue`, `is_full` and `is_empty`.
15. Generate the first `n` binary numbers (`"1"`, `"10"`, `"11"`, `"100"`, ...) using a queue.
16. Simulate browser back and forward buttons with two stacks.
17. Build an undo and redo feature for a simple text editor using two stacks.
18. Use `queue.Queue` and two threads to build a producer-consumer example. What does `join()` do?

---

- [Back to practice index](../README.md)
