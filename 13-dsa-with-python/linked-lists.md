# Linked Lists - Revision Notes ~ sm

> **In one line:** A **linked list** is a chain of **nodes**, where each node holds a **value** and a **reference (pointer) to the next node**. Unlike an array, the nodes can be anywhere in memory, so **inserting or removing at a known spot is cheap (`O(1)`)**, but reaching the `i`-th item means walking the chain (`O(n)`).

## Table of Contents
- [The Idea](#the-idea)
- [Linked List vs Array (List)](#linked-list-vs-array-list)
- [A Node](#a-node)
- [Walking the List](#walking-the-list)
- [A Singly Linked List Class](#a-singly-linked-list-class)
- [Edge Cases](#edge-cases)
- [The Dummy Node Trick](#the-dummy-node-trick)
- [Classic Problem: Reverse a List](#classic-problem-reverse-a-list)
- [Fast and Slow Pointers](#fast-and-slow-pointers)
- [Detecting a Cycle](#detecting-a-cycle)
- [Two Pointers with a Gap](#two-pointers-with-a-gap)
- [Merging Sorted Lists](#merging-sorted-lists)
- [Palindrome Check](#palindrome-check)
- [Doubly Linked List](#doubly-linked-list)
- [Circular Linked List](#circular-linked-list)
- [Linked Lists in Real Python Code](#linked-lists-in-real-python-code)
- [Common Mistakes](#common-mistakes)

---

## The Idea

Imagine a **treasure hunt**: each clue tells you where the next clue is hidden. You can only find clue 5 by following clues 1 to 4. Adding a new clue in the middle just means changing **two notes**: the new clue points to the next one, and the previous clue points to the new one. Nobody has to move.

```text
head
 |
 v
[ 10 | * ]--->[ 20 | * ]--->[ 30 | * ]--->[ 40 | None ]
  value next    value next    value next    value next (end of the list)
```
- The list is reached through its **head** (the first node).
- The last node's `next` is `None`.
- An **empty** list has `head = None`.

---

## Linked List vs Array (List)

| | Python `list` (dynamic array) | Linked list |
|---|---|---|
| Memory layout | Items side by side | Nodes scattered, joined by references |
| Access the `i`-th item | `O(1)` | `O(n)` (walk from the head) |
| Insert / delete at the **front** | `O(n)` (shifts items) | **`O(1)`** |
| Insert / delete **after a node you already hold** | `O(n)` | **`O(1)`** |
| Insert at the end | `O(1)` amortized | `O(1)` if you keep a `tail`, else `O(n)` |
| Search by value | `O(n)` | `O(n)` |
| Extra memory per item | Just the reference | Reference **plus** a `next` pointer, plus node object overhead |
| Cache-friendly | Yes | No |

The linked list wins only when you do **many inserts and removals at known places** and rarely need random access. In everyday Python you use `list` or `collections.deque` (a queue). Linked lists matter because they teach **pointers**, and they are a **very common interview topic**.

---

## A Node

```python
class Node:
    def __init__(self, value, next=None):
        self.value = value
        self.next = next

a = Node(10)
b = Node(20)
c = Node(30)
a.next = b
b.next = c

print(a.value, a.next.value, a.next.next.value, a.next.next.next)
# Output: 10 20 30 None
```
`a`, `b` and `c` are linked: `a → b → c → None`. `head = a` is the whole list.

Two helper functions make testing easy:
```python
def from_list(values):
    head = None
    for value in reversed(values):
        head = Node(value, head)          # put each new node in front
    return head

def to_list(head):
    values = []
    while head:
        values.append(head.value)
        head = head.next
    return values

print(to_list(from_list([1, 2, 3])), to_list(None))
# Output: [1, 2, 3] []
```

---

## Walking the List

The basic loop: start at the head, follow `next` until `None`.

```python
def length(head):
    count = 0
    node = head
    while node:                # stops when node is None
        count += 1
        node = node.next
    return count

def contains(head, target):
    node = head
    while node:
        if node.value == target:
            return True
        node = node.next
    return False

head = from_list([10, 20, 30, 40])
print(length(head), contains(head, 30), contains(head, 99))
# Output: 4 True False
```
Both are `O(n)`. Always write `while node:` (or `while node and node.next:`) so you never call `.next` on `None`.

---

## A Singly Linked List Class

Wrapping the nodes in a class keeps `head`, `tail` and `size` together. Keeping a **tail** pointer makes `append` `O(1)`.

```python
class LinkedList:
    def __init__(self, values=()):
        self.head = None
        self.tail = None
        self.size = 0
        for value in values:
            self.append(value)

    def append(self, value):                 # add at the end: O(1)
        node = Node(value)
        if self.head is None:
            self.head = self.tail = node
        else:
            self.tail.next = node
            self.tail = node
        self.size += 1

    def prepend(self, value):                # add at the front: O(1)
        node = Node(value, self.head)
        self.head = node
        if self.tail is None:
            self.tail = node
        self.size += 1

    def find(self, value):                   # O(n)
        node = self.head
        while node:
            if node.value == value:
                return node
            node = node.next
        return None

    def insert_after(self, node, value):     # O(1), given the node
        node.next = Node(value, node.next)
        if node is self.tail:
            self.tail = node.next
        self.size += 1

    def remove(self, value):                 # O(n) to find, O(1) to unlink
        previous = None
        node = self.head
        while node:
            if node.value == value:
                if previous is None:         # removing the head
                    self.head = node.next
                else:
                    previous.next = node.next
                if node is self.tail:        # removing the tail
                    self.tail = previous
                self.size -= 1
                return True
            previous = node
            node = node.next
        return False

    def __iter__(self):
        node = self.head
        while node:
            yield node.value
            node = node.next

    def __len__(self):
        return self.size

    def __repr__(self):
        return " -> ".join(str(v) for v in self) + " -> None" if self.head else "empty"

items = LinkedList([1, 2, 3])
items.append(4)
items.prepend(0)
print(items, len(items))
items.insert_after(items.find(2), 99)
print(items)
print(items.remove(0), items.remove(4), items.remove(42))
print(items, len(items), items.tail.value)
print(list(items))
# Output: 0 -> 1 -> 2 -> 3 -> 4 -> None 5
# Output: 0 -> 1 -> 2 -> 99 -> 3 -> 4 -> None
# Output: True True False
# Output: 1 -> 2 -> 99 -> 3 -> None 4 3
# Output: [1, 2, 99, 3]
```
| Operation | Cost |
|---|---|
| `append`, `prepend` | `O(1)` |
| `insert_after(node, x)` | `O(1)` |
| `find`, `remove(value)` | `O(n)` |
| Get the `i`-th item | `O(n)` |
| `len()` | `O(1)` (kept in `size`) |

**Removing a node = bypass it.** `previous.next = node.next` makes the chain skip `node`.

---

## Edge Cases

Most linked-list bugs come from the special cases. Check each one:

| Case | What to watch |
|---|---|
| **Empty list** (`head is None`) | Do not touch `head.next` or `head.value` |
| **One node** | `head` and `tail` are the same node |
| **Insert or delete at the head** | `head` itself must change |
| **Insert or delete at the tail** | `tail` must change |
| **Value not found** | Return cleanly, change nothing |
| **Cycles** | A loop that never ends (see below) |

```python
empty = LinkedList()
print(empty, empty.remove(1), len(empty))

one = LinkedList([7])
one.remove(7)
print(one, one.head, one.tail)
# Output: empty False 0
# Output: empty None None
```

---

## The Dummy Node Trick

A **dummy (sentinel) node** placed before the real head removes the "is this the head?" special case: every real node now has a `previous`.

```python
def remove_all(head, target):
    dummy = Node(None, head)
    previous = dummy
    while previous.next:
        if previous.next.value == target:
            previous.next = previous.next.next      # bypass
        else:
            previous = previous.next
    return dummy.next                               # the real head (it may have changed)

print(to_list(remove_all(from_list([1, 2, 2, 3, 2]), 2)))
print(to_list(remove_all(from_list([2, 2]), 2)))
# Output: [1, 3]
# Output: []
```
Without the dummy you would need extra code to handle removing the head(s). Use a dummy whenever the **head can change**.

---

## Classic Problem: Reverse a List

Walk the list and **turn each `next` pointer around**. Use three names: `previous`, `current`, `next_node`.

```python
def reverse(head):
    previous = None
    current = head
    while current:
        next_node = current.next      # 1. remember what comes next
        current.next = previous       # 2. point backwards
        previous = current            # 3. step forward
        current = next_node
    return previous                   # the new head

print(to_list(reverse(from_list([1, 2, 3, 4]))))
print(to_list(reverse(None)), to_list(reverse(from_list([5]))))
# Output: [4, 3, 2, 1]
# Output: [] [5]
```
Trace for `1 → 2 → 3`:

| Step | previous | current | List state |
|---|---|---|---|
| start | `None` | 1 | `1 → 2 → 3` |
| 1 | 1 | 2 | `None ← 1`, `2 → 3` |
| 2 | 2 | 3 | `None ← 1 ← 2`, `3` |
| 3 | 3 | `None` | `None ← 1 ← 2 ← 3`: return `3` |

`O(n)` time, `O(1)` space. Save `current.next` **before** you overwrite it, or you lose the rest of the list.

**Recursive version** (uses `O(n)` stack space)
```python
def reverse_recursive(head):
    if head is None or head.next is None:
        return head
    new_head = reverse_recursive(head.next)
    head.next.next = head          # the next node now points back at head
    head.next = None
    return new_head

print(to_list(reverse_recursive(from_list([1, 2, 3, 4]))))
# Output: [4, 3, 2, 1]
```

---

## Fast and Slow Pointers

Two pointers walk the list at **different speeds**: `slow` moves 1 step, `fast` moves 2 steps. When `fast` reaches the end, `slow` is in the **middle**.

```python
def middle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow

print(middle(from_list([1, 2, 3, 4, 5])).value)
print(middle(from_list([1, 2, 3, 4, 5, 6])).value)
# Output: 3
# Output: 4
```
For an even length it returns the **second** of the two middle nodes. One pass, `O(n)` time, `O(1)` space: there is no need to count the length first.

---

## Detecting a Cycle

If a node's `next` leads back to an earlier node, walking the list **never ends**. **Floyd's tortoise and hare**: if there is a cycle, the fast pointer will eventually **catch up** with the slow one inside it (like a faster runner lapping a slower one on a track); if there is none, `fast` reaches `None`.

```python
def has_cycle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow is fast:               # same node (use `is`, not ==)
            return True
    return False

def build_with_cycle(values, join_index):
    head = from_list(values)
    nodes = []
    node = head
    while node:
        nodes.append(node)
        node = node.next
    nodes[-1].next = nodes[join_index]       # tail points back
    return head

print(has_cycle(from_list([1, 2, 3, 4])))
print(has_cycle(build_with_cycle([1, 2, 3, 4], 1)))
# Output: False
# Output: True
```
`O(n)` time and **`O(1)` space**. (A simpler `O(n)`-space version keeps a `set` of visited nodes.)

**Where does the cycle begin?** After the pointers meet, restart one pointer at the head; move both **one step at a time**; they meet at the cycle's first node.
```python
def cycle_start(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow is fast:
            pointer = head
            while pointer is not slow:
                pointer = pointer.next
                slow = slow.next
            return pointer
    return None

head = build_with_cycle([10, 20, 30, 40, 50], 2)
print(cycle_start(head).value, cycle_start(from_list([1, 2, 3])))
# Output: 30 None
```

---

## Two Pointers with a Gap

**Remove the `n`-th node from the end** in one pass: move `fast` `n` steps ahead, then move both until `fast` reaches the end. `slow` is now just before the node to delete.

```python
def remove_nth_from_end(head, n):
    dummy = Node(None, head)
    slow = fast = dummy
    for _ in range(n):
        fast = fast.next
    while fast.next:
        slow = slow.next
        fast = fast.next
    slow.next = slow.next.next
    return dummy.next

print(to_list(remove_nth_from_end(from_list([1, 2, 3, 4, 5]), 2)))
print(to_list(remove_nth_from_end(from_list([1]), 1)))
# Output: [1, 2, 3, 5]
# Output: []
```
The dummy node makes removing the **head** (when `n` equals the length) work with no special case.

---

## Merging Sorted Lists

Take the smaller front node each time, as in the `merge` step of merge sort.

```python
def merge_sorted(a, b):
    dummy = Node(None)
    tail = dummy
    while a and b:
        if a.value <= b.value:
            tail.next = a
            a = a.next
        else:
            tail.next = b
            b = b.next
        tail = tail.next
    tail.next = a or b              # attach whatever is left
    return dummy.next

print(to_list(merge_sorted(from_list([1, 2, 4]), from_list([1, 3, 4]))))
# Output: [1, 1, 2, 3, 4, 4]
```
`O(a + b)` time and **`O(1)` extra space** (it re-uses the nodes). Linked lists are the natural fit for merge sort.

**Remove duplicates from a sorted list**
```python
def remove_duplicates(head):
    node = head
    while node and node.next:
        if node.value == node.next.value:
            node.next = node.next.next
        else:
            node = node.next
    return head

print(to_list(remove_duplicates(from_list([1, 1, 2, 3, 3, 3]))))
# Output: [1, 2, 3]
```

---

## Palindrome Check

Find the middle, **reverse the second half**, and compare the two halves.

```python
def is_palindrome(head):
    if head is None or head.next is None:
        return True
    slow = fast = head
    while fast and fast.next:                # find the middle
        slow = slow.next
        fast = fast.next.next
    second = reverse(slow)                   # reverse from the middle
    first = head
    while second:
        if first.value != second.value:
            return False
        first = first.next
        second = second.next
    return True

print(is_palindrome(from_list([1, 2, 2, 1])), is_palindrome(from_list([1, 2, 3])))
print(is_palindrome(from_list([1, 2, 3, 2, 1])))
# Output: True False
# Output: True
```
`O(n)` time, `O(1)` space. (Copying the values into a Python list and comparing with its reverse is simpler, but `O(n)` space.) This version changes the second half of the list; reverse it again if the original must survive.

---

## Doubly Linked List

Each node also points **back** to the previous node, so you can walk in both directions and **delete a node in `O(1)` when you hold it** (a singly linked list must first find the previous node).

```python
class DNode:
    def __init__(self, value):
        self.value = value
        self.prev = None
        self.next = None

class DoublyLinkedList:
    def __init__(self):
        self.head = None
        self.tail = None

    def append(self, value):
        node = DNode(value)
        if self.tail is None:
            self.head = self.tail = node
        else:
            node.prev = self.tail
            self.tail.next = node
            self.tail = node
        return node

    def remove(self, node):                  # O(1): no searching
        if node.prev:
            node.prev.next = node.next
        else:
            self.head = node.next
        if node.next:
            node.next.prev = node.prev
        else:
            self.tail = node.prev

    def forward(self):
        node = self.head
        while node:
            yield node.value
            node = node.next

    def backward(self):
        node = self.tail
        while node:
            yield node.value
            node = node.prev

d = DoublyLinkedList()
nodes = [d.append(v) for v in (1, 2, 3, 4)]
d.remove(nodes[1])                          # delete the node holding 2
print(list(d.forward()), list(d.backward()))
# Output: [1, 3, 4] [4, 3, 1]
```
| | Singly | Doubly |
|---|---|---|
| Pointers per node | 1 | 2 |
| Move backwards | No | **Yes** |
| Delete a given node | `O(n)` (find the previous) | **`O(1)`** |
| Memory | Less | More |

A doubly linked list plus a dictionary is how an **LRU cache** is built: the dictionary finds a node in `O(1)`, and the list moves it to the front in `O(1)`.

---

## Circular Linked List

The last node points back to the **head** instead of `None`. Walking it never ends by itself, so the loop must stop when it returns to the start.

```python
head = from_list(["a", "b", "c"])
last = head
while last.next:
    last = last.next
last.next = head                 # close the circle

node = head
seen = []
for _ in range(7):               # go around more than once
    seen.append(node.value)
    node = node.next
print(seen)
# Output: ['a', 'b', 'c', 'a', 'b', 'c', 'a']
```
Used for round-robin scheduling and "turn" games.

---

## Linked Lists in Real Python Code

- Python has **no built-in linked list**. `list` is a dynamic array; for a fast queue use **`collections.deque`** (it is internally a doubly linked list of blocks).
- A real program rarely needs a hand-made linked list. They are important for **understanding pointers and references** and for **interviews**, and many structures (trees, graphs, hash-table chains, LRU caches) are built from linked nodes.
- Python variables are references, so `node.next = other` just stores a reference: no pointer arithmetic, and the memory of nodes nobody refers to is freed automatically.
- Comparing nodes: use **`is`** to ask "the same node?", and `==` (or `.value`) to compare contents.

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| `node.next.value` when `node.next` is `None` | `AttributeError: 'NoneType' object has no attribute 'value'` | Check `if node.next:` first |
| Losing the rest of the list when rewiring | Nodes become unreachable | Save `next_node = current.next` **before** changing `current.next` |
| Forgetting to update `head` when deleting or inserting at the front | The change is not visible | Return the new head, or update `self.head` |
| Forgetting to update `tail` | `append` breaks later | Update `tail` for tail inserts and removals |
| Forgetting the one-node and empty cases | Crash on tiny inputs | Test `[]`, `[x]` and `[x, y]` |
| Using `==` to test for a cycle meeting point | Compares values, not nodes | `slow is fast` |
| Looping on a list that has a cycle | Infinite loop | Detect the cycle first, or limit the steps |
| Making a cycle by accident when linking | `node.next = node` or pointing to an earlier node | Draw the pointers on paper first |
| Treating `list[i]` as `O(1)` for linked nodes | It is `O(n)` | Walk, or choose an array |
| Counting the length twice in a "middle" problem | Two passes instead of one | Fast and slow pointers |
| `while node.next:` on an empty list | `AttributeError` | `while node and node.next:` |
| Reversing a half and forgetting to restore it | The original list is changed | Reverse again after comparing |

---

- [Back to section index](./README.md)
