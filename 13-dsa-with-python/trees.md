# Trees - Revision Notes ~ sm

> **In one line:** A **tree** is a hierarchy of **nodes**: one **root** at the top, and every node can have **children** below it. A **binary tree** allows at most **two** children per node (left and right), and a **binary search tree (BST)** keeps smaller values on the left and larger on the right, so searching is fast.

## Table of Contents
- [The Idea](#the-idea)
- [Tree Vocabulary](#tree-vocabulary)
- [A Binary Tree Node](#a-binary-tree-node)
- [Height, Size and Leaves](#height-size-and-leaves)
- [Depth-First Traversals](#depth-first-traversals)
- [Iterative Traversals](#iterative-traversals)
- [Level-Order (Breadth-First) Traversal](#level-order-breadth-first-traversal)
- [Common Binary Tree Problems](#common-binary-tree-problems)
- [Binary Search Tree](#binary-search-tree)
- [BST: Insert and Search](#bst-insert-and-search)
- [BST: Delete](#bst-delete)
- [Is It a Valid BST?](#is-it-a-valid-bst)
- [Balanced Trees](#balanced-trees)
- [Heaps and heapq](#heaps-and-heapq)
- [Trie (Prefix Tree)](#trie-prefix-tree)
- [General Trees (Many Children)](#general-trees-many-children)
- [Which Traversal When?](#which-traversal-when)
- [Common Mistakes](#common-mistakes)

---

## The Idea

Trees show up whenever data is **hierarchical**: folders and files, an HTML page, a company org chart, a family tree, a game's decision moves.

```text
          8            <- root
        /   \
       3     10        <- 3 and 10 are children of 8
      / \      \
     1   6      14     <- 1, 6, 14 ...
        / \    /
       4   7  13       <- leaves have no children
```
- Each node has exactly **one parent** (except the root, which has none).
- There are **no cycles**. (A structure with cycles is a *graph*; see the graphs note.)
- Trees are naturally **recursive**: every node is the root of a smaller tree (its **subtree**).

---

## Tree Vocabulary

| Term | Meaning |
|---|---|
| **Root** | The top node (no parent) |
| **Parent / child** | A node and the nodes directly below it |
| **Leaf** | A node with no children |
| **Sibling** | Nodes with the same parent |
| **Edge** | The link between a parent and a child |
| **Depth of a node** | Number of edges from the root down to it (root = 0) |
| **Height of a node** | Number of edges on the longest path down to a leaf (a leaf = 0) |
| **Height of the tree** | Height of the root |
| **Level** | All nodes at the same depth |
| **Subtree** | A node together with all its descendants |

> Some books count **nodes** instead of edges for height, so an empty tree has height `0` and a single node has height `1`. In these notes: height counts **edges**, so a single node has height `0` and an empty tree has height `-1`. Always check which one a problem uses.

---

## A Binary Tree Node

```python
class TreeNode:
    def __init__(self, value, left=None, right=None):
        self.value = value
        self.left = left
        self.right = right

#        1
#       / \
#      2   3
#     / \
#    4   5
root = TreeNode(1,
                TreeNode(2, TreeNode(4), TreeNode(5)),
                TreeNode(3))

print(root.value, root.left.value, root.right.value, root.left.right.value)
# Output: 1 2 3 5
```
An empty tree is just `None`. This is the same idea as a linked list node, but with **two** `next` pointers.

We will reuse this tree in the examples below.

---

## Height, Size and Leaves

Trees are recursive, so the code is too: **answer for a node = combine the answers of its left and right subtrees.**

```python
def height(node):
    if node is None:
        return -1                       # empty tree (edges counted)
    return 1 + max(height(node.left), height(node.right))

def size(node):                          # number of nodes
    if node is None:
        return 0
    return 1 + size(node.left) + size(node.right)

def count_leaves(node):
    if node is None:
        return 0
    if node.left is None and node.right is None:
        return 1
    return count_leaves(node.left) + count_leaves(node.right)

print(height(root), size(root), count_leaves(root), height(None))
# Output: 2 5 3 -1
```
Every one of these has the same shape: **base case** (`None`), then combine the results from the two children. Each visits every node once: `O(n)` time. The recursion uses `O(h)` stack space, where `h` is the tree height.

---

## Depth-First Traversals

A **traversal** visits every node exactly once. The three depth-first orders differ only in **when the node itself is processed**:

| Order | Visit order | Memory trick |
|---|---|---|
| **Pre-order** | node, left, right | node first ("pre") |
| **In-order** | left, node, right | node in the middle |
| **Post-order** | left, right, node | node last ("post") |

```python
def preorder(node, out):
    if node:
        out.append(node.value)
        preorder(node.left, out)
        preorder(node.right, out)
    return out

def inorder(node, out):
    if node:
        inorder(node.left, out)
        out.append(node.value)
        inorder(node.right, out)
    return out

def postorder(node, out):
    if node:
        postorder(node.left, out)
        postorder(node.right, out)
        out.append(node.value)
    return out

print(preorder(root, []))
print(inorder(root, []))
print(postorder(root, []))
# Output: [1, 2, 4, 5, 3]
# Output: [4, 2, 5, 1, 3]
# Output: [4, 5, 2, 3, 1]
```
Using a **generator** avoids passing the `out` list around:
```python
def inorder_gen(node):
    if node:
        yield from inorder_gen(node.left)
        yield node.value
        yield from inorder_gen(node.right)

print(list(inorder_gen(root)))
# Output: [4, 2, 5, 1, 3]
```
All three are `O(n)` time and `O(h)` space.

---

## Iterative Traversals

Recursion uses the call stack. You can do the same with your **own stack**, which avoids hitting Python's recursion limit (about 1000) on very deep trees.

```python
def inorder_iterative(root):
    result = []
    stack = []
    node = root
    while node or stack:
        while node:                    # go as far left as possible
            stack.append(node)
            node = node.left
        node = stack.pop()
        result.append(node.value)
        node = node.right              # then explore the right subtree
    return result

def preorder_iterative(root):
    if root is None:
        return []
    result = []
    stack = [root]
    while stack:
        node = stack.pop()
        result.append(node.value)
        if node.right:                 # push right first so left is popped first
            stack.append(node.right)
        if node.left:
            stack.append(node.left)
    return result

print(inorder_iterative(root))
print(preorder_iterative(root))
# Output: [4, 2, 5, 1, 3]
# Output: [1, 2, 4, 5, 3]
```
Post-order iteratively is trickier; a simple trick is to do a "node, right, left" pre-order and **reverse** the result.
```python
def postorder_iterative(root):
    if root is None:
        return []
    result = []
    stack = [root]
    while stack:
        node = stack.pop()
        result.append(node.value)
        if node.left:
            stack.append(node.left)
        if node.right:
            stack.append(node.right)
    return result[::-1]

print(postorder_iterative(root))
# Output: [4, 5, 2, 3, 1]
```

---

## Level-Order (Breadth-First) Traversal

Visit the tree **level by level**, top to bottom, left to right. Use a **queue** (`collections.deque`), not a stack.

```python
from collections import deque

def level_order(root):
    if root is None:
        return []
    levels = []
    queue = deque([root])
    while queue:
        level = []
        for _ in range(len(queue)):        # exactly one level per pass
            node = queue.popleft()
            level.append(node.value)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        levels.append(level)
    return levels

print(level_order(root))
# Output: [[1], [2, 3], [4, 5]]
```
`for _ in range(len(queue))` fixes the number of nodes in the **current** level before new children are added: that is how levels stay separate. `O(n)` time; `O(w)` space where `w` is the widest level.

**Right-side view** (the last node of each level)
```python
print([level[-1] for level in level_order(root)])
# Output: [1, 3, 5]
```

---

## Common Binary Tree Problems

**Maximum root-to-leaf path sum / does a path sum exist**
```python
def has_path_sum(node, target):
    if node is None:
        return False
    if node.left is None and node.right is None:
        return node.value == target
    remaining = target - node.value
    return has_path_sum(node.left, remaining) or has_path_sum(node.right, remaining)

print(has_path_sum(root, 7), has_path_sum(root, 4), has_path_sum(root, 9))
# Output: True True False
```
(The root-to-leaf paths total 1+2+4 = 7, 1+2+5 = 8 and 1+3 = 4, so 9 is not possible.)

**Invert (mirror) a tree**
```python
def invert(node):
    if node:
        node.left, node.right = invert(node.right), invert(node.left)
    return node

mirror = invert(TreeNode(1, TreeNode(2), TreeNode(3)))
print(mirror.left.value, mirror.right.value)
# Output: 3 2
```

**Are two trees identical?**
```python
def same_tree(a, b):
    if a is None and b is None:
        return True
    if a is None or b is None:
        return False
    return a.value == b.value and same_tree(a.left, b.left) and same_tree(a.right, b.right)

print(same_tree(root, root), same_tree(root, TreeNode(1)))
# Output: True False
```

**Is the tree symmetric?** (left subtree is the mirror of the right)
```python
def is_symmetric(node):
    def mirror(a, b):
        if a is None and b is None:
            return True
        if a is None or b is None:
            return False
        return a.value == b.value and mirror(a.left, b.right) and mirror(a.right, b.left)
    return node is None or mirror(node.left, node.right)

print(is_symmetric(TreeNode(1, TreeNode(2), TreeNode(2))), is_symmetric(root))
# Output: True False
```

**Lowest common ancestor** (the lowest node that has both targets in its subtree)
```python
def lca(node, a, b):
    if node is None or node.value == a or node.value == b:
        return node
    left = lca(node.left, a, b)
    right = lca(node.right, a, b)
    if left and right:
        return node                    # a is on one side, b on the other
    return left or right

print(lca(root, 4, 5).value, lca(root, 4, 3).value)
# Output: 2 1
```

**Diameter** (the longest path between any two nodes, counted in edges)
```python
def diameter(root):
    best = 0
    def depth(node):
        nonlocal best
        if node is None:
            return 0
        left = depth(node.left)
        right = depth(node.right)
        best = max(best, left + right)
        return 1 + max(left, right)
    depth(root)
    return best

print(diameter(root))
# Output: 3
```
(The path 4 → 2 → 1 → 3 has 3 edges.)

**Build a tree from a list in level order** (`None` for a missing child)
```python
def build(values):
    if not values or values[0] is None:
        return None
    nodes = [TreeNode(v) if v is not None else None for v in values]
    children = iter(nodes[1:])
    for node in nodes:
        if node:
            node.left = next(children, None)
            node.right = next(children, None)
    return nodes[0]

tree = build([1, 2, 3, 4, 5])
print(level_order(tree))
# Output: [[1], [2, 3], [4, 5]]
```

---

## Binary Search Tree

A **BST** adds one rule at **every** node:

> everything in the **left** subtree is **smaller**, everything in the **right** subtree is **larger**.

```text
        8
      /   \
     3     10
    / \      \
   1   6      14
      / \    /
     4   7  13
```
- Searching works like binary search: compare, then go left or right, throwing away half the tree (if it is balanced).
- **In-order traversal of a BST gives the values in sorted order.**

| Operation | Balanced tree | Worst case (a chain) |
|---|---|---|
| Search / insert / delete | `O(log n)` | `O(n)` |

The worst case happens when you insert already-sorted data: every node goes right and the tree becomes a linked list.

---

## BST: Insert and Search

```python
class BST:
    def __init__(self):
        self.root = None
        self.size = 0

    def insert(self, value):
        if self.root is None:
            self.root = TreeNode(value)
            self.size += 1
            return True
        node = self.root
        while True:
            if value == node.value:
                return False                   # ignore duplicates
            elif value < node.value:
                if node.left is None:
                    node.left = TreeNode(value)
                    break
                node = node.left
            else:
                if node.right is None:
                    node.right = TreeNode(value)
                    break
                node = node.right
        self.size += 1
        return True

    def search(self, value):
        node = self.root
        while node:
            if value == node.value:
                return True
            node = node.left if value < node.value else node.right
        return False

    def minimum(self):
        node = self.root
        while node.left:
            node = node.left
        return node.value

    def maximum(self):
        node = self.root
        while node.right:
            node = node.right
        return node.value

    def sorted_values(self):
        return inorder(self.root, [])

bst = BST()
for number in [8, 3, 10, 1, 6, 14, 4, 7, 13, 6]:
    bst.insert(number)

print(bst.sorted_values(), bst.size)
print(bst.search(6), bst.search(5))
print(bst.minimum(), bst.maximum(), height(bst.root))
# Output: [1, 3, 4, 6, 7, 8, 10, 13, 14] 9
# Output: True False
# Output: 1 14 3
```
The second `6` was a duplicate, so it was ignored (`size` is 9). Notice that `sorted_values()` came out **sorted** without ever calling `sort`.

**Sorted input makes a chain**
```python
chain = BST()
for number in range(1, 8):
    chain.insert(number)
print(height(chain.root))
# Output: 6
```
Seven nodes, height 6: a straight line, so every search is `O(n)`.

---

## BST: Delete

Deleting has three cases:

| The node has | What to do |
|---|---|
| **No children** (leaf) | Just remove it |
| **One child** | Replace the node with that child |
| **Two children** | Replace its value with its **in-order successor** (the smallest value in the right subtree), then delete that successor |

```python
def delete(node, value):
    """Return the new root of this subtree after deleting value."""
    if node is None:
        return None
    if value < node.value:
        node.left = delete(node.left, value)
    elif value > node.value:
        node.right = delete(node.right, value)
    else:
        if node.left is None:                 # zero or one child
            return node.right
        if node.right is None:
            return node.left
        successor = node.right                # two children
        while successor.left:
            successor = successor.left
        node.value = successor.value
        node.right = delete(node.right, successor.value)
    return node

bst.root = delete(bst.root, 1)       # a leaf
print(bst.sorted_values())
bst.root = delete(bst.root, 14)      # one child (13)
print(bst.sorted_values())
bst.root = delete(bst.root, 8)       # two children (the root)
print(bst.sorted_values(), bst.root.value)
# Output: [3, 4, 6, 7, 8, 10, 13, 14]
# Output: [3, 4, 6, 7, 8, 10, 13]
# Output: [3, 4, 6, 7, 10, 13] 10
```
Deleting the root `8` replaced it with `10`, the smallest value in its right subtree.

> `BST.size` is not updated by the standalone `delete` function above. In a full class, put `delete` inside the class and decrease `size` when a value is actually removed.

---

## Is It a Valid BST?

Checking only that `left < node < right` for each node is **not enough**: a deep node can break the rule for an ancestor. Pass down the allowed **range** instead.

```python
def is_valid_bst(node, low=float("-inf"), high=float("inf")):
    if node is None:
        return True
    if not (low < node.value < high):
        return False
    return (is_valid_bst(node.left, low, node.value) and
            is_valid_bst(node.right, node.value, high))

good = TreeNode(5, TreeNode(3), TreeNode(8))
#       5
#      / \
#     3   8
#        / \
#       4   9      4 is in 8's left, but 4 < 5: it must not be in 5's right
bad = TreeNode(5, TreeNode(3), TreeNode(8, TreeNode(4), TreeNode(9)))

print(is_valid_bst(good), is_valid_bst(bad))
# Output: True False
```
Another way: the in-order traversal of a valid BST is **strictly increasing**.

---

## Balanced Trees

A BST is only fast if it is **balanced**: the left and right sides of every node have similar heights, so the height stays about `log2(n)`.

```python
import math

def is_balanced(node):
    """Heights of the two subtrees differ by at most 1, everywhere."""
    def check(n):
        if n is None:
            return 0
        left = check(n.left)
        right = check(n.right)
        if left == -1 or right == -1 or abs(left - right) > 1:
            return -1                    # -1 signals "unbalanced"
        return 1 + max(left, right)
    return check(node) != -1

print(is_balanced(root), is_balanced(chain.root))
print(math.ceil(math.log2(1_000_001)))
# Output: True False
# Output: 20
```
A balanced tree with a million nodes is only about 20 levels deep. A chain of a million nodes is a million levels deep.

**Self-balancing trees** (AVL trees, red-black trees, B-trees) **rotate** nodes after inserts and deletes to keep the height `O(log n)` automatically. Databases and file systems use B-trees. Python has **no** built-in balanced tree; the usual substitutes:

| Need | Use |
|---|---|
| A sorted list with fast search | A `list` kept sorted with `bisect` |
| Fast min or max | `heapq` (below) |
| Key to value lookup | `dict` (hash map) |
| Real sorted-map features | The third-party `sortedcontainers` package |

---

## Heaps and heapq

A **heap** is a tree (stored in a list) where each parent is **no larger** than its children (**min-heap**). The smallest item is always at the top, so you get the minimum in `O(1)` and push or pop in `O(log n)`.

```text
        1                list form:  [1, 3, 2, 7, 4]
      /   \              parent of index i  -> (i - 1) // 2
     3     2             children of index i -> 2*i + 1 and 2*i + 2
    / \
   7   4
```
Python's **`heapq`** module works on an ordinary list:
```python
import heapq

heap = []
for number in [7, 3, 9, 1, 4]:
    heapq.heappush(heap, number)

print(heap[0])                       # the smallest is always at index 0
print(heapq.heappop(heap), heapq.heappop(heap))
print(len(heap))
# Output: 1
# Output: 1 3
# Output: 3
```
```python
data = [7, 3, 9, 1, 4]
heapq.heapify(data)                  # turn a list into a heap in O(n), in place
print(data[0])

print(heapq.nsmallest(2, [7, 3, 9, 1, 4]))
print(heapq.nlargest(2, [7, 3, 9, 1, 4]))
# Output: 1
# Output: [1, 3]
# Output: [9, 7]
```
| Operation | Cost |
|---|---|
| `heap[0]` (peek the smallest) | `O(1)` |
| `heappush`, `heappop` | `O(log n)` |
| `heapify(list)` | `O(n)` |
| `nsmallest(k, ...)`, `nlargest(k, ...)` | about `O(n log k)` |

- `heapq` is a **min-heap only**. For a **max-heap**, push the **negated** values.
- The list is only **partly ordered**: printing `heap` does not show sorted order. Only `heap[0]` is guaranteed to be the smallest.

**Max-heap and the k-th largest**
```python
max_heap = []
for number in [7, 3, 9, 1, 4]:
    heapq.heappush(max_heap, -number)
print(-heapq.heappop(max_heap))
# Output: 9

def kth_largest(numbers, k):
    heap = []                              # a min-heap holding the k largest so far
    for number in numbers:
        heapq.heappush(heap, number)
        if len(heap) > k:
            heapq.heappop(heap)            # drop the smallest
    return heap[0]

print(kth_largest([3, 2, 1, 5, 6, 4], 2))
# Output: 5
```
This uses only `O(k)` memory, which is useful for streams of data.

**Heap sort** (see the sorting note) is just: heapify, then pop everything.
```python
def heap_sort(items):
    heap = list(items)
    heapq.heapify(heap)
    return [heapq.heappop(heap) for _ in range(len(heap))]

print(heap_sort([5, 2, 8, 1, 9, 3]))
# Output: [1, 2, 3, 5, 8, 9]
```
For items that are not directly comparable, push **tuples** `(priority, item)`; the first element decides the order.

---

## Trie (Prefix Tree)

A **trie** stores strings **one character per node**. Words that share a prefix share the same path. It is ideal for **autocomplete** and **"does any word start with ...?"**.

```text
 words: cat, car, dog       root
                           /    \
                          c      d
                          |      |
                          a      o
                         / \     |
                        t   r    g
                      (end)(end)(end)
```
```python
class Trie:
    def __init__(self):
        self.children = {}
        self.is_word = False

    def insert(self, word):
        node = self
        for char in word:
            if char not in node.children:
                node.children[char] = Trie()
            node = node.children[char]
        node.is_word = True

    def _find(self, prefix):
        node = self
        for char in prefix:
            node = node.children.get(char)
            if node is None:
                return None
        return node

    def search(self, word):                     # is this exact word stored?
        node = self._find(word)
        return node is not None and node.is_word

    def starts_with(self, prefix):              # does any word start with prefix?
        return self._find(prefix) is not None

    def words_with_prefix(self, prefix):        # autocomplete
        node = self._find(prefix)
        found = []
        def walk(current, path):
            if current.is_word:
                found.append(path)
            for char in sorted(current.children):
                walk(current.children[char], path + char)
        if node:
            walk(node, prefix)
        return found

trie = Trie()
for word in ["cat", "car", "card", "dog"]:
    trie.insert(word)

print(trie.search("car"), trie.search("ca"), trie.starts_with("ca"))
print(trie.words_with_prefix("car"))
print(trie.words_with_prefix("x"))
# Output: True False True
# Output: ['car', 'card']
# Output: []
```
Insert, search and prefix lookup cost `O(L)` where `L` is the **word length**, regardless of how many words are stored. `ca` is a prefix but **not** a stored word, which is why `is_word` is a separate flag.

---

## General Trees (Many Children)

When a node can have any number of children, keep them in a **list**. The traversals are the same ideas.

```python
class Folder:
    def __init__(self, name, children=()):
        self.name = name
        self.children = list(children)

tree = Folder("project", [
    Folder("src", [Folder("app.py"), Folder("utils.py")]),
    Folder("tests", [Folder("test_app.py")]),
    Folder("README.md"),
])

def show(node, depth=0):
    print("  " * depth + node.name)
    for child in node.children:
        show(child, depth + 1)

show(tree)

def count_nodes(node):
    return 1 + sum(count_nodes(child) for child in node.children)

print(count_nodes(tree))
# Output: project
# Output:   src
# Output:     app.py
# Output:     utils.py
# Output:   tests
# Output:     test_app.py
# Output:   README.md
# Output: 7
```
This is exactly how folder structures, HTML documents and JSON data are organised. (`os.walk` and `pathlib.Path.rglob` walk a real folder tree for you.)

---

## Which Traversal When?

| Goal | Use |
|---|---|
| Copy a tree, save it, or print a prefix expression | **Pre-order** |
| Get a BST's values in sorted order | **In-order** |
| Delete a tree, or evaluate an expression (children before the parent) | **Post-order** |
| Process level by level; find the shortest path in an unweighted tree | **Level-order** (BFS) |

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Forgetting the `None` base case | `AttributeError: 'NoneType' object has no attribute 'left'` | Start recursive functions with `if node is None:` |
| Checking only `left < node < right` for a BST | Deep violations are missed | Pass down a `(low, high)` range |
| Mixing up the height of an empty tree (`0` or `-1`) and edges versus nodes | Off-by-one answers | Decide the convention once and state it |
| Inserting sorted data into a plain BST | Degenerates into a chain, `O(n)` operations | Shuffle first, or use a balanced structure or `bisect` |
| Using a stack for level-order | Wrong order (that is depth-first) | Use a `deque` queue |
| Using `list.pop(0)` as a queue | `O(n)` per pop | `deque.popleft()` |
| Recursing on very deep trees | `RecursionError` (limit about 1000) | Use an iterative traversal with your own stack |
| Forgetting `heapq` is a **min**-heap | "Largest" comes out as the smallest | Negate values for a max-heap |
| Expecting a heap list to print sorted | It is only partly ordered | Pop repeatedly or use `sorted()` |
| Forgetting `is_word` in a trie | `search("ca")` wrongly returns `True` | Mark the end of each stored word |
| Deleting a two-child BST node by just unlinking it | Loses a whole subtree | Replace with the in-order successor |
| Forgetting that in-order sorted order only applies to a **BST** | A plain binary tree's in-order is not sorted | Check the BST property first |

---

- [Back to section index](./README.md)
