# Hash Maps Practice (Intermediate)

Related notes: [Hash Maps](../../13-dsa-with-python/hash-maps.md)

> Intermediate: comfortable with the basics, now combining ideas. **Questions only, no solutions.** Write and run your own code, and test it on empty, single-item and large inputs.

## Practice Questions

1. Count how often each word appears in a sentence and print the three most common words (with and without `collections.Counter`).
2. Two sum with a dictionary: return the indices of the two numbers that add up to a target in one pass.
3. Find the first non-repeating character in a string. Example: `"swiss"` gives `"w"`.
4. Group anagrams together. Example: `["eat", "tea", "tan", "ate", "nat", "bat"]` gives three groups.
5. Check whether a list contains any duplicate. Then check whether two equal values lie within `k` positions of each other.
6. Isomorphic strings: can the characters of `s` be replaced to get `t`, with a one-to-one mapping? Example: `"egg"` and `"add"` is `True`; `"foo"` and `"bar"` is `False`.
7. Word pattern: does a string follow a pattern? Example: pattern `"abba"` and `"dog cat cat dog"` is `True`.
8. Find the intersection of two lists in two ways: as a set (unique values) and as a multiset (keeping counts).
9. Find the majority element (appears more than `n / 2` times) using a dictionary, then using the Boyer-Moore voting idea.
10. Ransom note: can the letters of one string be built from the letters of another (each letter used once)?
11. Find every value that appears more than once in a list, in `O(n)` time.
12. Invert a dictionary. What should happen when two keys share a value? Write a version that keeps all keys in a list.
13. Make a `Point(x, y)` class usable as a dictionary key and inside a `set`. Implement `__eq__` and `__hash__` correctly, and show what breaks if you implement only one.
14. Build a simple `HashMap` class with separate chaining: `put`, `get`, `remove`, `__len__`, and resizing when the load factor passes `0.75`.
15. Find the length of the longest substring made of only two distinct characters.
16. Use `defaultdict(list)` to build an index: map every word in a list of sentences to the sentence numbers it appears in.
17. Given a list of `(student, subject, marks)` rows, compute each student's average using one dictionary pass.

---

- [Back to practice index](../README.md)
