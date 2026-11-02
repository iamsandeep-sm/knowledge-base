# Hash Maps Practice (Advanced)

Related notes: [Hash Maps](../../13-dsa-with-python/hash-maps.md)

> Advanced: harder problems that need a clear plan before coding. **Questions only, no solutions.** Write and run your own code, and test it on empty, single-item and large inputs.

## Practice Questions

1. Implement an **LRU cache** with `get` and `put` in `O(1)`: first with `OrderedDict`, then with a dictionary and your own doubly linked list.
2. Find the length of the longest run of consecutive integers in an unsorted list in `O(n)`. Example: `[100, 4, 200, 1, 3, 2]` gives `4`.
3. Count subarrays whose sum is divisible by `k`, using a dictionary of remainders.
4. Find the longest subarray with an equal number of `0`s and `1`s.
5. 4Sum II: given four lists, count tuples `(i, j, k, l)` where `a[i] + b[j] + c[k] + d[l] == 0` in `O(n²)`.
6. Design a data structure with `insert`, `delete` and `get_random`, all in average `O(1)`.
7. Implement a hash table with **open addressing** (linear probing). Handle deletions with tombstones, and show why simply emptying a slot would break lookups.
8. Design a hit counter that returns the number of hits in the last 300 seconds.
9. Group strings that are shifts of each other. Example: `"abc"`, `"bcd"`, `"xyz"` belong together.
10. Deep-copy a linked list where each node also has a `random` pointer, using a dictionary from old nodes to new nodes.
11. Find all starting indices of substrings that are a concatenation of every word in a list (all words the same length).
12. Count pairs of items `(i, j)` with `i < j` whose sums of digits are equal.
13. Demonstrate hash collisions: create several unequal objects with the same hash and show how dictionary lookup time degrades. Then explain why Python randomises string hashes between runs.
14. Why can a `list` not be a dictionary key while a `tuple` of numbers can? What about a `tuple` containing a list? Prove it with code and explain the rule.
15. Implement a Bloom-filter-like structure with `k` hash functions and a bit array. Measure its false-positive rate for different sizes.

---

- [Back to practice index](../README.md)
