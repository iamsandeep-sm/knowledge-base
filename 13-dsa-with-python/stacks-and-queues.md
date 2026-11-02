# Stacks and Queues - Revision Notes ~ sm

> **In one line:** A **stack** is **last in, first out (LIFO)**: you only add and remove at the **top**, like a pile of plates. A **queue** is **first in, first out (FIFO)**: you add at the **back** and remove from the **front**, like people waiting in a line. Both give `O(1)` adding and removing when built the right way, and they are the backbone of undo, expression evaluation, scheduling and graph searches.

## Table of Contents
- [Stack: Last In, First Out](#stack-last-in-first-out)
- [A Stack in Python](#a-stack-in-python)
- [A Stack Class](#a-stack-class)
- [Where Stacks Are Used](#where-stacks-are-used)
- [Stack Problems](#stack-problems)
- [Queue: First In, First Out](#queue-first-in-first-out)
- [A Queue in Python: deque](#a-queue-in-python-deque)
- [A Queue Class](#a-queue-class)
- [Other Queues](#other-queues)
- [Priority Queue](#priority-queue)
- [Queue Problems](#queue-problems)
- [Stack vs Queue](#stack-vs-queue)
- [Common Mistakes](#common-mistakes)

---

## Stack: Last In, First Out

A stack of plates: you put a plate on the **top**, and you take a plate from the **top**. The plate you added **last** is the one you remove **first**.

| Operation | Meaning | Cost |
|---|---|---|
| `push(x)` | Put `x` on top | `O(1)` |
| `pop()` | Remove and return the top | `O(1)` |
| `peek()` | Look at the top without removing it | `O(1)` |
| `is_empty()` | Is it empty? | `O(1)` |
| `len` | Number of items | `O(1)` |

```text
push 1     push 2     push 3     pop -> 3    pop -> 2
 [ ]        [ ]        [3]        [ ]         [ ]
 [ ]        [2]        [2]        [2]         [ ]
 [1]        [1]        [1]        [1]         [1]
```

---

## A Stack in Python

A plain **`list`** is a good stack: `append` pushes and `pop()` removes from the **end**, both `O(1)`.

```python
stack = []
stack.append(1)         # push
stack.append(2)
stack.append(3)
print(stack[-1])        # peek
print(stack.pop())      # pop
print(stack.pop())
print(stack, len(stack), not stack)
# Output: 3
# Output: 3
# Output: 2
# Output: [1] 1 False
```
`not stack` is `True` when the stack is empty. Popping an empty list raises `IndexError`:
```python
stack = []
stack.pop()
# Error: IndexError: pop from empty list
```
Always check `if stack:` before `pop()` or `stack[-1]`.

---

## A Stack Class

```python
class Stack:
    def __init__(self):
        self._items = []

    def push(self, item):
        self._items.append(item)

    def pop(self):
        if not self._items:
            raise IndexError("pop from empty stack")
        return self._items.pop()

    def peek(self):
        if not self._items:
            raise IndexError("peek at empty stack")
        return self._items[-1]

    def is_empty(self):
        return not self._items

    def __len__(self):
        return len(self._items)

    def __repr__(self):
        return f"Stack({self._items})"

s = Stack()
s.push("a")
s.push("b")
print(s, s.peek(), len(s))
print(s.pop(), s.pop(), s.is_empty())
# Output: Stack(['a', 'b']) b 2
# Output: b a True
```
Hiding the list behind `push` and `pop` means nobody can accidentally touch the middle of the stack.

---

## Where Stacks Are Used

- The **call stack**: each function call is pushed, and removed when the function returns. This is why deep recursion gives `RecursionError` (a "stack overflow").
- **Undo / redo** in editors; the **back** button of a browser.
- **Matching brackets** and checking syntax.
- **Evaluating expressions** (postfix notation) and converting between notations.
- **Depth-first search** of trees and graphs, and **backtracking**.
- Finding the **next greater** item (monotonic stack).

---

## Stack Problems

**Reverse a string** (the last character pushed is the first popped)
```python
def reverse_with_stack(text):
    stack = list(text)
    return "".join(stack.pop() for _ in range(len(stack)))

print(reverse_with_stack("python"))
# Output: nohtyp
```

**Balanced brackets**: every opening bracket must be closed by the **same kind**, in the correct order. Push the openers; when you meet a closer, the top of the stack must be its partner.
```python
def is_balanced(text):
    pairs = {")": "(", "]": "[", "}": "{"}
    stack = []
    for ch in text:
        if ch in "([{":
            stack.append(ch)
        elif ch in pairs:
            if not stack or stack.pop() != pairs[ch]:
                return False
    return not stack            # nothing may be left open

for sample in ["{[()]}", "{[(])}", "((", ")(", ""]:
    print(repr(sample), is_balanced(sample))
# Output: '{[()]}' True
# Output: '{[(])}' False
# Output: '((' False
# Output: ')(' False
# Output: '' True
```
`O(n)` time, `O(n)` space. Three ways it can fail: a closer with nothing open, a closer of the wrong kind, or openers left over at the end.

**Evaluate a postfix expression** (Reverse Polish Notation). Numbers are pushed; an operator takes the **top two** numbers. `3 4 + 2 *` means `(3 + 4) * 2`.
```python
def eval_postfix(tokens):
    stack = []
    for token in tokens:
        if token in {"+", "-", "*", "/"}:
            right = stack.pop()              # the second operand is popped first
            left = stack.pop()
            if token == "+":
                stack.append(left + right)
            elif token == "-":
                stack.append(left - right)
            elif token == "*":
                stack.append(left * right)
            else:
                stack.append(int(left / right))      # truncate toward zero
        else:
            stack.append(int(token))
    return stack[0]

print(eval_postfix(["2", "1", "+", "3", "*"]))
print(eval_postfix(["4", "13", "5", "/", "+"]))
# Output: 9
# Output: 6
```
Note the order: for `-` and `/` the **first** popped item is the **right** operand.

**Next greater element**: for every item, the first larger item to its right (or `-1`). A **monotonic stack** keeps the indexes of items still waiting for their answer, in decreasing order of value.
```python
def next_greater(nums):
    result = [-1] * len(nums)
    waiting = []                          # indexes whose answer is not found yet
    for i, value in enumerate(nums):
        while waiting and nums[waiting[-1]] < value:
            result[waiting.pop()] = value       # value is the answer for them
        waiting.append(i)
    return result

print(next_greater([2, 1, 2, 4, 3]))
# Output: [4, 2, 4, -1, -1]
```
It looks like a loop inside a loop, but every index is pushed once and popped once, so the total is `O(n)`.

**Daily temperatures**: how many days until a warmer day?
```python
def days_until_warmer(temps):
    answer = [0] * len(temps)
    waiting = []
    for i, t in enumerate(temps):
        while waiting and temps[waiting[-1]] < t:
            j = waiting.pop()
            answer[j] = i - j
        waiting.append(i)
    return answer

print(days_until_warmer([73, 74, 75, 71, 69, 72, 76, 73]))
# Output: [1, 1, 4, 2, 1, 1, 0, 0]
```

**A stack that knows its minimum in `O(1)`**: store, with each item, the smallest value so far.
```python
class MinStack:
    def __init__(self):
        self._items = []                     # pairs: (value, minimum so far)

    def push(self, value):
        smallest = value if not self._items else min(value, self._items[-1][1])
        self._items.append((value, smallest))

    def pop(self):
        return self._items.pop()[0]

    def minimum(self):
        return self._items[-1][1]

m = MinStack()
for n in (5, 3, 7, 2):
    m.push(n)
print(m.minimum())
m.pop()
print(m.minimum())
m.pop()
m.pop()
print(m.minimum())
# Output: 2
# Output: 3
# Output: 5
```

**Remove adjacent duplicates** ("abbaca" becomes "ca")
```python
def remove_adjacent(text):
    stack = []
    for ch in text:
        if stack and stack[-1] == ch:
            stack.pop()
        else:
            stack.append(ch)
    return "".join(stack)

print(remove_adjacent("abbaca"))
# Output: ca
```

**Simplify a file path**
```python
def simplify_path(path):
    stack = []
    for part in path.split("/"):
        if part == "..":
            if stack:
                stack.pop()
        elif part and part != ".":
            stack.append(part)
    return "/" + "/".join(stack)

print(simplify_path("/home//docs/../pics/./a.png"))
# Output: /home/pics/a.png
```

---

## Queue: First In, First Out

A line at a ticket counter: people join at the **back** and are served from the **front**. The person who arrived **first** leaves first.

| Operation | Meaning | Cost |
|---|---|---|
| `enqueue(x)` | Add `x` at the back | `O(1)` |
| `dequeue()` | Remove and return the front | `O(1)` |
| `front()` / `peek()` | Look at the front | `O(1)` |
| `is_empty()` | Is it empty? | `O(1)` |

```text
front                  back
 [1] [2] [3]   enqueue 4 ->  [1] [2] [3] [4]
 dequeue -> 1                [2] [3] [4]
```

---

## A Queue in Python: deque

**Do not use a list as a queue.** `list.pop(0)` removes from the front and shifts every other item: `O(n)`. Use **`collections.deque`**, where both ends are `O(1)`.

```python
from collections import deque

queue = deque()
queue.append("Riya")          # enqueue at the back
queue.append("Amit")
queue.append("Zoya")
print(queue[0])               # front
print(queue.popleft())        # dequeue from the front
print(queue.popleft())
print(queue, len(queue))
# Output: Riya
# Output: Riya
# Output: Amit
# Output: deque(['Zoya']) 1
```
`popleft()` on an empty deque raises `IndexError`. A `deque` can also act as a **stack** (`append` and `pop`), and a double-ended queue (`appendleft`, `pop`) when you need both ends.

---

## A Queue Class

```python
from collections import deque

class Queue:
    def __init__(self):
        self._items = deque()

    def enqueue(self, item):
        self._items.append(item)

    def dequeue(self):
        if not self._items:
            raise IndexError("dequeue from empty queue")
        return self._items.popleft()

    def front(self):
        if not self._items:
            raise IndexError("front of empty queue")
        return self._items[0]

    def is_empty(self):
        return not self._items

    def __len__(self):
        return len(self._items)

q = Queue()
for name in ("a", "b", "c"):
    q.enqueue(name)
print(q.dequeue(), q.front(), len(q))
# Output: a b 2
```

---

## Other Queues

**Circular queue (ring buffer)**: a fixed-size array where the front and back indexes **wrap around**. It never shifts items and never grows.
```python
class CircularQueue:
    def __init__(self, capacity):
        self.items = [None] * capacity
        self.capacity = capacity
        self.head = 0                # index of the front item
        self.size = 0

    def enqueue(self, value):
        if self.size == self.capacity:
            raise OverflowError("queue is full")
        tail = (self.head + self.size) % self.capacity    # wrap around with %
        self.items[tail] = value
        self.size += 1

    def dequeue(self):
        if self.size == 0:
            raise IndexError("queue is empty")
        value = self.items[self.head]
        self.head = (self.head + 1) % self.capacity
        self.size -= 1
        return value

cq = CircularQueue(3)
cq.enqueue(1)
cq.enqueue(2)
cq.enqueue(3)
print(cq.dequeue(), cq.dequeue())
cq.enqueue(4)                         # reuses the freed space
cq.enqueue(5)
print([cq.dequeue() for _ in range(3)])
# Output: 1 2
# Output: [3, 4, 5]
```

**`queue.Queue`**: a **thread-safe** queue from the standard library, for programs that use several threads (producer and consumer). It can block when empty or full, which `deque` does not.
```python
import queue

q = queue.Queue()
q.put("job1")
q.put("job2")
print(q.get(), q.qsize(), q.empty())

try:
    queue.Queue().get_nowait()
except queue.Empty:
    print("nothing to take")
# Output: job1 1 False
# Output: nothing to take
```
`queue.LifoQueue` is the thread-safe **stack**, and `queue.PriorityQueue` the thread-safe priority queue. For ordinary single-thread code, `deque` and `list` are simpler and faster.

---

## Priority Queue

In a **priority queue** the item removed next is the one with the **highest priority** (the smallest number, in Python's version), not the oldest. Use the standard **`heapq`** module, a min-heap in which push and pop cost `O(log n)` (see the Trees note for how a heap works).

```python
import heapq

tasks = []
heapq.heappush(tasks, (3, "write report"))
heapq.heappush(tasks, (1, "fix bug"))
heapq.heappush(tasks, (2, "reply to email"))

print(tasks[0])                         # smallest, without removing
while tasks:
    priority, name = heapq.heappop(tasks)
    print(priority, name)
# Output: (1, 'fix bug')
# Output: 1 fix bug
# Output: 2 reply to email
# Output: 3 write report
```
Items are compared as tuples, so the **first element is the priority**. If two priorities are equal Python compares the next element, which fails for objects that cannot be compared. Add a **counter** as a tie-breaker (it also keeps first-come, first-served order):
```python
import heapq
from itertools import count

order = count()
heap = []
heapq.heappush(heap, (1, next(order), {"task": "A"}))
heapq.heappush(heap, (1, next(order), {"task": "B"}))
print(heapq.heappop(heap)[2], heapq.heappop(heap)[2])
# Output: {'task': 'A'} {'task': 'B'}
```
For a **max-heap** (largest first) push the **negated** priority. Uses: task schedulers, Dijkstra's shortest path, "top k", merging sorted lists.

---

## Queue Problems

**Hot potato (the Josephus problem)**: people stand in a circle; every `k`-th person leaves; who is left? A deque can **rotate**.
```python
from collections import deque

def last_survivor(n, k):
    circle = deque(range(1, n + 1))
    while len(circle) > 1:
        circle.rotate(-(k - 1))      # move k-1 people from the front to the back
        circle.popleft()             # the k-th person leaves
    return circle[0]

print(last_survivor(7, 3))
# Output: 4
```
**Queue from two stacks**: one stack takes new items; the other hands them out in the right order.
```python
class QueueFromStacks:
    def __init__(self):
        self.inbox = []
        self.outbox = []

    def enqueue(self, x):
        self.inbox.append(x)

    def dequeue(self):
        if not self.outbox:
            while self.inbox:                      # reverses the order
                self.outbox.append(self.inbox.pop())
        return self.outbox.pop()

q = QueueFromStacks()
for n in (1, 2, 3):
    q.enqueue(n)
print(q.dequeue(), q.dequeue())
q.enqueue(4)
print(q.dequeue(), q.dequeue())
# Output: 1 2
# Output: 3 4
```
Each item moves between the stacks at most once, so each operation is `O(1)` **amortized**.

**Sliding window maximum**: the biggest value in every window of `k` neighbours. A deque keeps the **indexes** of useful candidates in decreasing order of value (a **monotonic queue**): the front is always the current maximum.
```python
from collections import deque

def sliding_max(nums, k):
    window = deque()                         # indexes; their values are decreasing
    result = []
    for i, value in enumerate(nums):
        while window and nums[window[-1]] <= value:
            window.pop()                     # smaller items can never be the maximum
        window.append(i)
        if window[0] <= i - k:               # the front slid out of the window
            window.popleft()
        if i >= k - 1:
            result.append(nums[window[0]])
    return result

print(sliding_max([1, 3, -1, -3, 5, 3, 6, 7], 3))
# Output: [3, 3, 5, 5, 6, 7]
```
`O(n)` overall: each index enters and leaves the deque once (the naive way, `max()` of each window, is `O(n * k)`).

**Level-by-level traversal** and **shortest path in an unweighted graph** both use a queue: see the Trees and Graphs notes.

**Simulating a ticket line**
```python
from collections import deque

line = deque(["Riya", "Amit", "Zoya"])
served = []
while line:
    person = line.popleft()
    served.append(person)
    if person == "Amit":
        line.append("Kabir")           # a new person joins at the back
print(served)
# Output: ['Riya', 'Amit', 'Zoya', 'Kabir']
```

---

## Stack vs Queue

| | Stack | Queue |
|---|---|---|
| Order | **LIFO**: last in, first out | **FIFO**: first in, first out |
| Add at | Top | Back |
| Remove from | Top | Front |
| Python tool | `list` | `collections.deque` |
| Typical use | Undo, brackets, recursion, DFS | Scheduling, buffering, BFS |
| Graph search | **Depth-first** | **Breadth-first** |

If you need the **order of processing to follow arrival**, use a queue. If the **most recent thing must be handled first**, use a stack. If **importance** decides, use a priority queue.

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| `list.pop(0)` as a queue | `O(n)` for every dequeue | `collections.deque` and `popleft()` |
| Popping or peeking an empty stack | `IndexError` | `if stack:` first |
| Using `stack[0]` as the top of a list-based stack | That is the **bottom** | `stack[-1]` |
| Wrong operand order for `-` and `/` in postfix evaluation | Wrong sign or quotient | The first popped item is the **right** operand |
| Forgetting leftover openers in a bracket check | `"(("` counted as balanced | `return not stack` |
| Checking only the **count** of brackets | `"([)]"` passes | Match the **type and order** with a stack |
| Using a plain list for a priority queue | `O(n)` to find the smallest | `heapq` |
| Priority-queue ties between uncomparable objects | `TypeError` | Add a counter as a tie-breaker |
| Expecting a **max**-heap from `heapq` | It is a min-heap | Push negated values |
| Using `queue.Queue` in single-threaded code and getting stuck on `get()` | `get()` waits forever on an empty queue | `get_nowait()`, or check `empty()`, or use `deque` |
| Indexing into the middle of a `deque` in a loop | `O(n)` per access | Use a list when you need random access |
| Unbounded recursion instead of an explicit stack | `RecursionError` on deep inputs | Use a list as your own stack |

---

- [Back to section index](./README.md)
