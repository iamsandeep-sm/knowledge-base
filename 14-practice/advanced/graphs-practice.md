# Graphs Practice (Advanced)

Related notes: [Graphs](../../13-dsa-with-python/graphs.md)

> Advanced: harder problems that need a clear plan before coding. **Questions only, no solutions.** Write and run your own code, and test it on empty, single-item and large inputs.

## Practice Questions

1. Implement **Dijkstra's algorithm** with `heapq`, and return both the distances and the actual shortest path. Why does it fail with negative edges?
2. Network delay time: signals travel along directed weighted edges from a start node. How long until all nodes receive it (or `-1`)?
3. Cheapest flights within `k` stops: find the cheapest price from a source to a destination with at most `k` stops.
4. Word ladder: transform one word into another changing one letter at a time, using words from a list. Return the shortest sequence length.
5. Course schedule II: return a valid course order, or an empty list if impossible.
6. Alien dictionary: from a sorted list of words in an unknown alphabet, deduce a valid letter order.
7. Implement **union-find** with path compression and union by rank. Use it to find the redundant edge that makes a tree into a graph with a cycle.
8. Find a **minimum spanning tree** with Kruskal's algorithm (using union-find). Then do it with Prim's algorithm.
9. Implement the **Bellman-Ford** algorithm and detect a negative cycle.
10. Implement **Floyd-Warshall** to compute all-pairs shortest paths. State its time and space complexity.
11. Pacific Atlantic water flow: in a height grid, find the cells from which water can reach both oceans.
12. Shortest path in a binary matrix when moving in **8 directions**.
13. Minimum height trees: choose a root that minimises the height of an undirected tree (peel leaves layer by layer).
14. Find the **strongly connected components** of a directed graph (Kosaraju or Tarjan).
15. Find all **bridges** (edges whose removal disconnects the graph).
16. Path with minimum effort: in a height grid, minimise the largest height difference along a path from top-left to bottom-right.
17. Implement **0-1 BFS** for a graph whose edge weights are only `0` or `1`, using a `deque`.
18. Detect and print a cycle in a directed graph (the actual vertices), not just `True` or `False`.
19. Is the graph a **tree**? A graph with `n` vertices is a tree when it is connected and has exactly `n - 1` edges. Check it two ways.

---

- [Back to practice index](../README.md)
