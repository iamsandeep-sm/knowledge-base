# Python Loops - Revision Notes ~ sm

> **In one line:** A loop **repeats a block of code** again and again, so you do not have to write the same lines many times. Python has two loops: **`for`** (repeat for each item or a fixed number of times) and **`while`** (repeat as long as a condition is true).

## Table of Contents
- [What are Loops?](#what-are-loops)
- [for Loop](#for-loop)
- [The range Function](#the-range-function)
- [Useful Tools with for Loops](#useful-tools-with-for-loops)

---

## What are Loops?

Imagine you must write "Hello" three times. Without a loop you repeat the line:
```python
print("Hello")
print("Hello")
print("Hello")
# Output: Hello
# Output: Hello
# Output: Hello
```
With a loop you write it **once** and tell Python how many times to repeat:
```python
for i in range(3):
    print("Hello")
# Output: Hello
# Output: Hello
# Output: Hello
```
Now imagine 1000 times. Writing 1000 lines is silly, but changing `3` to `1000` is easy. That is the power of loops.

### Real-life examples of loops
- "For **each** student in the class, take attendance."  (`for` loop)
- "**While** the pot is not boiling, keep heating."  (`while` loop)

### The two loops in Python

| Loop | Use it when | Example |
|---|---|---|
| `for` | You know **how many times**, or you want to go through **each item** of something | Print every name in a list |
| `while` | You do **not** know how many times; repeat **until a condition becomes false** | Keep asking for a password until it is right |

### Key words to know

| Term | Plain meaning |
|---|---|
| **Iteration** | One single run of the loop body ("one round") |
| **Loop body** | The indented lines that repeat |
| **Iterable** | Anything a `for` loop can go through item by item: list, string, tuple, dictionary, set, `range(...)` |
| **Loop variable** | The name that holds the current item (`fruit` in `for fruit in fruits`) |
| **Infinite loop** | A loop that never stops (usually a bug) |

---

## for Loop

A `for` loop takes the items of an iterable **one by one** and runs the body once for each item.

### Syntax
```
for variable in iterable:
    # body: runs once for every item
```
- The `for` line **ends with a colon `:`**.
- The body is **indented** (4 spaces).
- `variable` is a name you choose. In each round it holds the **next item**.

### Flow of a for loop
```
take the next item  -->  any item left?  --no-->  loop ends, program continues
                              |yes
                         run the body with that item
                              |
                         (go back and take the next item)
```

### Loop through a list
```python
fruits = ["apple", "banana", "cherry"]

for fruit in fruits:
    print(fruit)
# Output: apple
# Output: banana
# Output: cherry
```
Round 1: `fruit` is `"apple"`. Round 2: `"banana"`. Round 3: `"cherry"`. No items left, so the loop ends.

### Loop through a string (letter by letter)
```python
for ch in "Hi!":
    print(ch)
# Output: H
# Output: i
# Output: !
```

### Loop through a tuple
```python
for n in (10, 20, 30):
    print(n)
# Output: 10
# Output: 20
# Output: 30
```

### Loop through a dictionary
By default a dictionary loop gives the **keys**.
```python
info = {"name": "Karan", "age": 19}

for key in info:
    print(key)
for value in info.values():
    print(value)
for key, value in info.items():
    print(key, "->", value)
# Output: name
# Output: age
# Output: Karan
# Output: 19
# Output: name -> Karan
# Output: age -> 19
```

### Doing something useful: add up numbers
```python
total = 0                 # start the total at 0, BEFORE the loop
for n in [10, 20, 30]:
    total += n            # total = total + n
print("Total:", total)    # runs after the loop (not indented)
# Output: Total: 60
```

### The loop variable stays after the loop
```python
for i in range(3):
    pass
print(i)
# Output: 2
```
> If the loop had no items at all (for example an empty list), the variable would **never be created**, and using it afterwards gives `NameError`.

### Only indented lines repeat
```python
for i in range(2):
    print("inside the loop")      # repeats
print("outside the loop")         # runs once, after the loop ends
# Output: inside the loop
# Output: inside the loop
# Output: outside the loop
```

---

## The range Function

`range()` creates a sequence of **numbers** to loop over. It is the most common partner of the `for` loop.

### Three ways to write it

| Form | Meaning | Example | Numbers produced |
|---|---|---|---|
| `range(stop)` | From **0** up to (not including) `stop` | `range(5)` | `0, 1, 2, 3, 4` |
| `range(start, stop)` | From `start` up to (not including) `stop` | `range(2, 6)` | `2, 3, 4, 5` |
| `range(start, stop, step)` | Jump by `step` each time | `range(0, 10, 2)` | `0, 2, 4, 6, 8` |

> **The `stop` number is never included.** `range(5)` gives 5 numbers but the last one is 4. To include 5, write `range(6)` or `range(1, 6)`.

```python
print(list(range(5)))           # [0, 1, 2, 3, 4]
print(list(range(1, 6)))        # [1, 2, 3, 4, 5]
print(list(range(0, 10, 2)))    # [0, 2, 4, 6, 8]
print(list(range(10, 0, -3)))   # [10, 7, 4, 1]
print(list(range(5, 0, -1)))    # [5, 4, 3, 2, 1]
print(list(range(0)))           # []
```
`list(...)` is used here only to **show** the numbers.

### Counting up and down
```python
for i in range(1, 6):
    print(i, end=" ")
print()
# Output: 1 2 3 4 5

for i in range(5, 0, -1):          # a negative step counts DOWN
    print(i, end=" ")
print()
# Output: 5 4 3 2 1
```
`end=" "` keeps the output on **one line**, with a space after each item. The empty `print()` then moves to a new line.

### Repeat something N times
When you do not need the number itself, use `_` as the variable name.
```python
for _ in range(3):
    print("Python")
# Output: Python
# Output: Python
# Output: Python
```

### Loop with the index
```python
colors = ["red", "green", "blue"]
for i in range(len(colors)):
    print(i, colors[i])
# Output: 0 red
# Output: 1 green
# Output: 2 blue
```
(In the next section you will see an easier way: `enumerate()`.)

### Facts about range
- `range` works with **whole numbers only**. `range(1.5)` gives `TypeError`.
- The step cannot be `0` (`ValueError`).
- `range(5)` does not store all the numbers; it makes them one at a time, so even `range(1000000)` is light on memory.

---

## Useful Tools with for Loops

### `enumerate()` - get the position and the item together
```python
fruits = ["apple", "banana", "cherry"]

for index, fruit in enumerate(fruits):
    print(index, fruit)
# Output: 0 apple
# Output: 1 banana
# Output: 2 cherry

for number, fruit in enumerate(fruits, start=1):    # count from 1 instead of 0
    print(number, fruit)
# Output: 1 apple
# Output: 2 banana
# Output: 3 cherry
```

### `zip()` - go through two lists at the same time
```python
names = ["Karan", "Riya"]
marks = [80, 90]

for name, mark in zip(names, marks):
    print(name, mark)
# Output: Karan 80
# Output: Riya 90
```
`zip()` stops when the **shorter** list ends.

### `reversed()` and `sorted()`
```python
for n in reversed([1, 2, 3]):
    print(n, end=" ")
print()
# Output: 3 2 1

for n in sorted([3, 1, 2]):
    print(n, end=" ")
print()
# Output: 1 2 3
```

### Loop over a copy when you change the list
Do **not** add or remove items of a list while looping over that same list. Loop over a **copy** (`nums[:]`) instead.
```python
nums = [1, 2, 2, 3, 4, 4]
for n in nums[:]:             # loop over a copy
    if n % 2 == 0:
        nums.remove(n)        # change the original
print(nums)
# Output: [1, 3]
```
Or build a new list: `[n for n in nums if n % 2 != 0]`.

### Quick one-line loop: list comprehension
```python
squares = [x * x for x in range(1, 6)]
print(squares)
# Output: [1, 4, 9, 16, 25]
```
(See the list notes for comprehensions in detail.)

---

- [Back to section index](./README.md)
