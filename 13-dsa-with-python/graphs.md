# Graphs - Revision Notes ~ sm

> **In one line:** A **graph** is a set of **nodes (vertices)** joined by **edges**. It models anything made of "things and connections": roads between cities, friends on a social network, web page links, course prerequisites. A tree is just a graph with no cycles.

## Table of Contents
- [The Idea](#the-idea)
- [Graph Vocabulary](#graph-vocabulary)
- [Representing a Graph](#representing-a-graph)
- [Building a Graph from Edges](#building-a-graph-from-edges)
- [Breadth-First Search (BFS)](#breadth-first-search-bfs)
- [Depth-First Search (DFS)](#depth-first-search-dfs)
- [BFS vs DFS](#bfs-vs-dfs)
- [Is There a Path?](#is-there-a-path)
- [Connected Components](#connected-components)
- [Cycle Detection](#cycle-detection)
- [Grids as Graphs](#grids-as-graphs)
- [Topological Sort](#topological-sort)
- [Shortest Path in Weighted Graphs: Dijkstra](#shortest-path-in-weighted-graphs-dijkstra)
- [Union-Find (Disjoint Set)](#union-find-disjoint-set)
- [Common Mistakes](#common-mistakes)

---

## The Idea

```text
   A --- B
   |     |
   |     |
   C --- D --- E
```
- The **nodes** are `A` to `E`. The **edges** are the lines.
- Unlike a tree, a graph can have **cycles** (`A-B-D-C-A`), no single root, and several separate pieces.

---

## Graph Vocabulary

| Term | Meaning |
|---|---|
| **Vertex / node** | A thing |
| **Edge** | A connection between two vertices |
| **Undirected** | Edges work both ways (friendship) |
| **Directed** | Edges have a direction `A -> B` (following someone, one-way streets) |
| **Weighted** | Each edge carries a number (distance, cost, time) |
| **Neighbours / adjacent** | Vertices joined directly by an edge |
| **Degree** | Number of edges at a vertex |
| **Path** | A sequence of vertices joined by edges |
| **Cycle** | A path that returns to its start |
| **Connected** | Every vertex can reach every other vertex |
| **DAG** | **D**irected **A**cyclic **G**raph: directed, no cycles |

`V` = number of vertices, `E` = number of edges. Graph algorithm costs are written using both.

---

## Representing a Graph

**1. Adjacency list** (the usual choice): for each vertex, a list of its neighbours.
```python
graph = {
    "A": ["B", "C"],
    "B": ["A", "D"],
    "C": ["A", "D"],
    "D": ["B", "C", "E"],
    "E": ["D"],
}
print(graph["D"])
print(len(graph["D"]))            # the degree of D
# Output: ['B', 'C', 'E']
# Output: 3
```

**2. Adjacency matrix**: a table where `matrix[i][j] = 1` if there is an edge from `i` to `j`.
```python
#          A  B  C  D  E
matrix = [[0, 1, 1, 0, 0],      # A
          [1, 0, 0, 1, 0],      # B
          [1, 0, 0, 1, 0],      # C
          [0, 1, 1, 0, 1],      # D
          [0, 0, 0, 1, 0]]      # E
print(matrix[0][1], matrix[0][3])     # A-B edge? A-D edge?
# Output: 1 0
```

| | Adjacency list | Adjacency matrix |
|---|---|---|
| Memory | `O(V + E)` | `O(V²)` |
| "Is there an edge `u-v`?" | `O(degree)` (or `O(1)` if neighbours are sets) | **`O(1)`** |
| List all neighbours of `v` | **`O(degree)`** | `O(V)` |
| Best for | **Sparse** graphs (few edges): most real ones | **Dense** graphs, or fast edge checks |

**3. Edge list**: just a list of pairs, `[("A", "B"), ("A", "C"), ...]`. Simple to store and read; you usually convert it to an adjacency list before using it.

**Weighted graph**: store `(neighbour, weight)` pairs.
```python
roads = {
    "A": [("B", 4), ("C", 1)],
    "B": [("D", 1)],
    "C": [("B", 2), ("D", 5)],
    "D": [],
}
print(roads["C"])
# Output: [('B', 2), ('D', 5)]
```

---

## Building a Graph from Edges

```python
from collections import defaultdict

def build_graph(edges, directed=False):
    graph = defaultdict(list)
    for u, v in edges:
        graph[u].append(v)
        if not directed:
            graph[v].append(u)          # undirected: add both directions
    return graph

edges = [("A", "B"), ("A", "C"), ("B", "D"), ("C", "D"), ("D", "E")]
g = build_graph(edges)
print(dict(g))
# Output: {'A': ['B', 'C'], 'B': ['A', 'D'], 'C': ['A', 'D'], 'D': ['B', 'C', 'E'], 'E': ['D']}
```
`defaultdict(list)` means a vertex with no entry yet gets an empty list automatically. Forgetting the second `append` for undirected graphs is a very common bug.

We will use `g` (and the lists above) in the examples below.

---

## Breadth-First Search (BFS)

BFS explores **level by level**: first all vertices 1 step away, then 2 steps, and so on. It uses a **queue** (`deque`) and a **visited** set.

```python
from collections import deque

def bfs(graph, start):
    visited = {start}
    order = []
    queue = deque([start])
    while queue:
        node = queue.popleft()
        order.append(node)
        for neighbour in graph[node]:
            if neighbour not in visited:
                visited.add(neighbour)       # mark when you ADD to the queue
                queue.append(neighbour)
    return order

print(bfs(g, "A"))
# Output: ['A', 'B', 'C', 'D', 'E']
```
- Mark a vertex **visited when you enqueue it**, not when you dequeue it, otherwise it can be queued many times.
- `O(V + E)` time, `O(V)` space.

**Shortest path (fewest edges)**: BFS reaches every vertex by the fewest steps possible, so record each vertex's `parent` and walk back.
```python
def shortest_path(graph, start, goal):
    parent = {start: None}
    queue = deque([start])
    while queue:
        node = queue.popleft()
        if node == goal:
            path = []
            while node is not None:
                path.append(node)
                node = parent[node]
            return path[::-1]
        for neighbour in graph[node]:
            if neighbour not in parent:          # parent doubles as "visited"
                parent[neighbour] = node
                queue.append(neighbour)
    return None                                  # unreachable

print(shortest_path(g, "A", "E"))
print(shortest_path(g, "E", "A"))
print(shortest_path({"X": [], "Y": []}, "X", "Y"))
# Output: ['A', 'B', 'D', 'E']
# Output: ['E', 'D', 'B', 'A']
# Output: None
```
This works for **unweighted** graphs only (every edge counts as 1).

**Distances from the start**
```python
def distances(graph, start):
    dist = {start: 0}
    queue = deque([start])
    while queue:
        node = queue.popleft()
        for neighbour in graph[node]:
            if neighbour not in dist:
                dist[neighbour] = dist[node] + 1
                queue.append(neighbour)
    return dist

print(distances(g, "A"))
# Output: {'A': 0, 'B': 1, 'C': 1, 'D': 2, 'E': 3}
```

---

## Depth-First Search (DFS)

DFS goes **as deep as possible** down one path, then backs up and tries the next. It uses **recursion** (the call stack) or an explicit **stack**.

**Recursive**
```python
def dfs(graph, node, visited=None):
    if visited is None:
        visited = set()
    visited.add(node)
    order = [node]
    for neighbour in graph[node]:
        if neighbour not in visited:
            order += dfs(graph, neighbour, visited)
    return order

print(dfs(g, "A"))
# Output: ['A', 'B', 'D', 'C', 'E']
```
**Iterative with a stack**
```python
def dfs_iterative(graph, start):
    visited = set()
    order = []
    stack = [start]
    while stack:
        node = stack.pop()
        if node in visited:
            continue
        visited.add(node)
        order.append(node)
        for neighbour in reversed(graph[node]):    # reversed: so the first neighbour is visited first
            if neighbour not in visited:
                stack.append(neighbour)
    return order

print(dfs_iterative(g, "A"))
# Output: ['A', 'B', 'D', 'C', 'E']
```
Both are `O(V + E)`. Recursive DFS can hit Python's recursion limit (about 1000) on very long chains; the iterative one cannot.

---

## BFS vs DFS

| | BFS | DFS |
|---|---|---|
| Data structure | Queue | Stack / recursion |
| Explores | Level by level (near first) | One path to the end, then backtracks |
| Finds the **fewest-edge path** | **Yes** | No |
| Memory | Can be large for wide graphs | Depends on depth |
| Good for | Shortest path, "within k steps", level problems | Cycles, components, paths, topological sort, backtracking, exploring everything |

---

## Is There a Path?

```python
def has_path(graph, start, goal):
    visited = set()
    stack = [start]
    while stack:
        node = stack.pop()
        if node == goal:
            return True
        if node in visited:
            continue
        visited.add(node)
        stack.extend(graph[node])
    return False

print(has_path(g, "A", "E"), has_path({"X": [], "Y": []}, "X", "Y"))
# Output: True False
```

---

## Connected Components

A **connected component** is a group of vertices that can all reach each other. To count them, start a search from every vertex you have not seen yet.

```python
def connected_components(graph):
    visited = set()
    components = []
    for start in graph:
        if start in visited:
            continue
        component = []
        stack = [start]
        visited.add(start)
        while stack:
            node = stack.pop()
            component.append(node)
            for neighbour in graph[node]:
                if neighbour not in visited:
                    visited.add(neighbour)
                    stack.append(neighbour)
        components.append(sorted(component))
    return components

pieces = {
    1: [2], 2: [1, 3], 3: [2],
    4: [5], 5: [4],
    6: [],
}
print(connected_components(pieces))
print(len(connected_components(pieces)))
# Output: [[1, 2, 3], [4, 5], [6]]
# Output: 3
```
The outer `for` is needed because a single search only reaches **one** component.

---

## Cycle Detection

**Undirected graph**: while exploring, if you meet an already-visited neighbour that is **not the vertex you just came from**, there is a cycle.

```python
def has_cycle_undirected(graph):
    visited = set()
    def visit(node, parent):
        visited.add(node)
        for neighbour in graph[node]:
            if neighbour not in visited:
                if visit(neighbour, node):
                    return True
            elif neighbour != parent:
                return True
        return False
    return any(visit(n, None) for n in graph if n not in visited)

print(has_cycle_undirected(g))
print(has_cycle_undirected({1: [2], 2: [1, 3], 3: [2]}))
# Output: True
# Output: False
```
(`g` has the cycle A-B-D-C-A.)

**Directed graph**: a cycle exists if DFS reaches a vertex that is **still on the current path**. Track three states: not seen, in progress, finished.
```python
def has_cycle_directed(graph):
    state = {}                        # missing = unseen, 1 = in progress, 2 = done
    def visit(node):
        state[node] = 1
        for neighbour in graph.get(node, []):
            if state.get(neighbour) == 1:
                return True           # back to a node still being explored
            if neighbour not in state and visit(neighbour):
                return True
        state[node] = 2
        return False
    return any(visit(n) for n in list(graph) if n not in state)

print(has_cycle_directed({"A": ["B"], "B": ["C"], "C": ["A"]}))
print(has_cycle_directed({"A": ["B", "C"], "B": ["C"], "C": []}))
# Output: True
# Output: False
```
In the second graph `C` is reached twice but not from itself: that is **not** a cycle, which is why "visited" alone is not enough for directed graphs.

---

## Grids as Graphs

A 2D grid is a graph: each cell is a vertex; its neighbours are the cells above, below, left and right (4 directions).

```python
DIRECTIONS = [(1, 0), (-1, 0), (0, 1), (0, -1)]

def count_islands(grid):
    rows, cols = len(grid), len(grid[0])
    seen = set()

    def sink(r, c):
        stack = [(r, c)]
        seen.add((r, c))
        while stack:
            row, col = stack.pop()
            for dr, dc in DIRECTIONS:
                nr, nc = row + dr, col + dc
                if (0 <= nr < rows and 0 <= nc < cols
                        and grid[nr][nc] == "1" and (nr, nc) not in seen):
                    seen.add((nr, nc))
                    stack.append((nr, nc))

    islands = 0
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == "1" and (r, c) not in seen:
                islands += 1
                sink(r, c)
    return islands

land = ["11000",
        "11000",
        "00100",
        "00011"]
print(count_islands(land))
# Output: 3
```
Always **bounds-check** (`0 <= nr < rows`) before reading `grid[nr][nc]`.

**Shortest path through a maze** (BFS)
```python
def maze_steps(maze, start, goal):
    rows, cols = len(maze), len(maze[0])
    queue = deque([(start, 0)])
    seen = {start}
    while queue:
        (r, c), steps = queue.popleft()
        if (r, c) == goal:
            return steps
        for dr, dc in DIRECTIONS:
            nr, nc = r + dr, c + dc
            if (0 <= nr < rows and 0 <= nc < cols
                    and maze[nr][nc] == "." and (nr, nc) not in seen):
                seen.add((nr, nc))
                queue.append(((nr, nc), steps + 1))
    return -1

maze = ["..#.",
        ".##.",
        "....",
        "#..."]
print(maze_steps(maze, (0, 0), (0, 3)))
print(maze_steps(["..#", "###", "..."], (0, 0), (2, 2)))
# Output: 7
# Output: -1
```
`#` is a wall, `.` is open. The route goes down the left column, across the third row, then up the right column.

---

## Topological Sort

For a **DAG**, a topological order lists every vertex **before** the vertices it points to. Think of **course prerequisites**: a valid study order. If the graph has a cycle, no such order exists.

**Kahn's algorithm**: repeatedly take a vertex with **in-degree 0** (no unmet prerequisites).
```python
def topological_sort(graph):
    in_degree = {node: 0 for node in graph}
    for node in graph:
        for neighbour in graph[node]:
            in_degree[neighbour] += 1

    queue = deque(node for node in graph if in_degree[node] == 0)
    order = []
    while queue:
        node = queue.popleft()
        order.append(node)
        for neighbour in graph[node]:
            in_degree[neighbour] -= 1
            if in_degree[neighbour] == 0:
                queue.append(neighbour)

    return order if len(order) == len(graph) else None   # None = there is a cycle

courses = {
    "Math": ["Physics", "Stats"],
    "Physics": ["Engineering"],
    "Stats": ["Engineering"],
    "Engineering": [],
}
print(topological_sort(courses))
print(topological_sort({"A": ["B"], "B": ["A"]}))
# Output: ['Math', 'Physics', 'Stats', 'Engineering']
# Output: None
```
`O(V + E)`. If fewer vertices come out than went in, a cycle blocked the rest: so this also **detects cycles**. Python's standard library has this ready-made too: `graphlib.TopologicalSorter` (Python 3.9+).

```python
from graphlib import TopologicalSorter

# graphlib wants "node: its prerequisites"
prerequisites = {"Physics": {"Math"}, "Stats": {"Math"}, "Engineering": {"Physics", "Stats"}}
print(list(TopologicalSorter(prerequisites).static_order()))
# Output: ['Math', 'Physics', 'Stats', 'Engineering']
```
(When several orders are valid, any of them is correct; do not rely on a particular one.)

---

## Shortest Path in Weighted Graphs: Dijkstra

BFS counts edges. When edges have **weights**, use **Dijkstra's algorithm**: always expand the **cheapest vertex found so far**, using a **min-heap** (`heapq`). It needs **non-negative** weights.

```python
import heapq

def dijkstra(graph, start):
    dist = {start: 0}
    heap = [(0, start)]                       # (distance so far, vertex)
    while heap:
        d, node = heapq.heappop(heap)
        if d > dist.get(node, float("inf")):
            continue                          # an old, longer entry: skip it
        for neighbour, weight in graph[node]:
            new_d = d + weight
            if new_d < dist.get(neighbour, float("inf")):
                dist[neighbour] = new_d
                heapq.heappush(heap, (new_d, neighbour))
    return dist

print(dijkstra(roads, "A"))
# Output: {'A': 0, 'B': 3, 'C': 1, 'D': 4}
```
Why `B` is 3 and not 4: the direct edge `A-B` costs 4, but `A -> C -> B` costs `1 + 2 = 3`. And `D` is `A -> C -> B -> D = 1 + 2 + 1 = 4`.

With the previous-vertex map you can also rebuild the actual route:
```python
def dijkstra_path(graph, start, goal):
    dist = {start: 0}
    previous = {}
    heap = [(0, start)]
    while heap:
        d, node = heapq.heappop(heap)
        if node == goal:
            break
        if d > dist.get(node, float("inf")):
            continue
        for neighbour, weight in graph[node]:
            new_d = d + weight
            if new_d < dist.get(neighbour, float("inf")):
                dist[neighbour] = new_d
                previous[neighbour] = node
                heapq.heappush(heap, (new_d, neighbour))
    if goal not in dist:
        return None, float("inf")
    path = [goal]
    while path[-1] != start:
        path.append(previous[path[-1]])
    return path[::-1], dist[goal]

print(dijkstra_path(roads, "A", "D"))
# Output: (['A', 'C', 'B', 'D'], 4)
```
`O((V + E) log V)` time with a heap. **Negative weights** break Dijkstra; Bellman-Ford handles them (not covered here).

| Graph | Use |
|---|---|
| Unweighted | BFS |
| Weighted, non-negative | Dijkstra |
| Negative weights | Bellman-Ford |

---

## Union-Find (Disjoint Set)

A structure that tracks **which group each item belongs to**, with two operations: **`find(x)`** (which group?) and **`union(a, b)`** (merge two groups). Ideal for "are these two connected?" while edges are being added.

```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))       # everyone starts in their own group
        self.rank = [0] * n
        self.groups = n

    def find(self, x):
        while self.parent[x] != x:
            self.parent[x] = self.parent[self.parent[x]]   # path compression (halving)
            x = self.parent[x]
        return x

    def union(self, a, b):
        root_a, root_b = self.find(a), self.find(b)
        if root_a == root_b:
            return False                   # already together
        if self.rank[root_a] < self.rank[root_b]:
            root_a, root_b = root_b, root_a
        self.parent[root_b] = root_a       # attach the shallower tree to the deeper one
        if self.rank[root_a] == self.rank[root_b]:
            self.rank[root_a] += 1
        self.groups -= 1
        return True

uf = UnionFind(6)
uf.union(0, 1)
uf.union(1, 2)
uf.union(3, 4)
print(uf.find(0) == uf.find(2), uf.find(0) == uf.find(3))
print(uf.groups)
print(uf.union(0, 2))                      # already connected
# Output: True False
# Output: 3
# Output: False
```
With path compression and union by rank each operation is effectively **constant time** (technically near `O(1)`, the inverse Ackermann function).

**Cycle detection while adding edges**: if `union` returns `False`, that edge closes a cycle.
```python
def first_cycle_edge(n, edges):
    uf = UnionFind(n)
    for u, v in edges:
        if not uf.union(u, v):
            return (u, v)
    return None

print(first_cycle_edge(4, [(0, 1), (1, 2), (2, 3), (3, 0)]))
print(first_cycle_edge(4, [(0, 1), (1, 2), (2, 3)]))
# Output: (3, 0)
# Output: None
```

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| No `visited` set | Infinite loop on any cycle | Always track visited vertices |
| Marking visited on dequeue in BFS | Vertices queued many times, slow | Mark when you **enqueue** |
| Forgetting both directions for an undirected edge | Missing connections | Add `v` to `u`'s list **and** `u` to `v`'s |
| Using BFS-style "visited" for directed cycle detection | False cycle reports | Track an **in-progress** state (or use topological sort) |
| Using a stack for BFS or a queue for DFS | Wrong order | BFS = queue, DFS = stack |
| `list.pop(0)` as the queue | `O(n)` per pop | `deque.popleft()` |
| Not bounds-checking grid neighbours | `IndexError`, or negative indexes silently wrap to the other side | Check `0 <= r < rows and 0 <= c < cols` first |
| Only searching from one start vertex | Misses other components | Loop over all vertices |
| BFS for weighted shortest paths | Wrong answer | Dijkstra |
| Dijkstra with negative weights | Wrong answer | Bellman-Ford |
| `graph[node]` for a vertex with no entry | `KeyError` | `defaultdict(list)` or `graph.get(node, [])` |
| Deep recursive DFS on a big graph | `RecursionError` | Iterative DFS with a stack |

---

- [Back to section index](./README.md)
