# Loop Patterns

## Nested Loops

A **nested loop** is a loop **inside another loop**. For every single round of the **outer** loop, the **inner** loop runs **completely**.

> Clock example: the hour hand (outer loop) moves one step, and for each hour the minute hand (inner loop) goes all the way round.

### Basic example
```python
for i in range(1, 4):          # outer loop: 3 rounds
    for j in range(1, 3):      # inner loop: 2 rounds for EACH outer round
        print(i, j)
# Output: 1 1
# Output: 1 2
# Output: 2 1
# Output: 2 2
# Output: 3 1
# Output: 3 2
```
The inner loop runs fully for `i = 1`, then fully for `i = 2`, then fully for `i = 3`.

**How many times does the inner body run?** `outer rounds x inner rounds`
```python
count = 0
for i in range(3):
    for j in range(4):
        count += 1
print(count)
# Output: 12
```

### Multiplication table grid
```python
for i in range(1, 4):
    for j in range(1, 4):
        print(i * j, end=" ")
    print()                    # new line after each row
# Output: 1 2 3
# Output: 2 4 6
# Output: 3 6 9
```
- The **inner** loop prints one **row** (using `end=" "` to stay on one line).
- The bare `print()` after the inner loop moves to the **next row**. It is indented at the **outer** loop level.

### Star patterns
**Rectangle (3 rows, 5 stars):**
```python
for i in range(3):
    for j in range(5):
        print("*", end=" ")
    print()
# Output: * * * * *
# Output: * * * * *
# Output: * * * * *
```

**Right triangle:** row `i` has `i` stars.
```python
for i in range(1, 5):
    for j in range(i):
        print("*", end="")
    print()
# Output: *
# Output: **
# Output: ***
# Output: ****
```
A shortcut for the same picture, using string repetition:
```python
for i in range(1, 5):
    print("*" * i)
# Output: *
# Output: **
# Output: ***
# Output: ****
```

**Inverted triangle:**
```python
for i in range(4, 0, -1):
    print("*" * i)
# Output: ****
# Output: ***
# Output: **
# Output: *
```

**Number triangle:**
```python
for i in range(1, 5):
    for j in range(1, i + 1):
        print(j, end=" ")
    print()
# Output: 1
# Output: 1 2
# Output: 1 2 3
# Output: 1 2 3 4
```

### Looping through a list of lists (a grid or matrix)
```python
matrix = [[1, 2, 3], [4, 5, 6]]

for row in matrix:
    for value in row:
        print(value, end=" ")
    print()
# Output: 1 2 3
# Output: 4 5 6
```

### `break` inside nested loops only stops the INNER loop
```python
for i in range(1, 4):
    for j in range(1, 4):
        if j == 2:
            break              # leaves the inner loop only
        print(i, j)
# Output: 1 1
# Output: 2 1
# Output: 3 1
```
To stop **both** loops, use a flag (a True/False variable):
```python
found = False
for i in range(1, 4):
    for j in range(1, 4):
        if i * j == 6:
            found = True
            break              # leaves the inner loop
    if found:
        break                  # leaves the outer loop
print(i, j)
# Output: 2 3
```

### Tips
- Nested loops get **slow** with big numbers (a loop of 1000 inside a loop of 1000 is 1,000,000 rounds).
- Avoid more than 2 or 3 levels of nesting; it becomes hard to read.
- Use **different** variable names for each loop (`i`, `j`, `k`).

---

## Commonly Used Patterns

### Factorial (5! = 5 x 4 x 3 x 2 x 1)
```python
fact = 1
for i in range(1, 6):
    fact *= i
print(fact)
# Output: 120
```

### Multiplication table
```python
n = 5
for i in range(1, 6):
    print(f"{n} x {i} = {n * i}")
# Output: 5 x 1 = 5
# Output: 5 x 2 = 10
# Output: 5 x 3 = 15
# Output: 5 x 4 = 20
# Output: 5 x 5 = 25
```

### Sum of the even numbers from 1 to 10
```python
total = 0
for n in range(1, 11):
    if n % 2 == 0:
        total += n
print(total)
# Output: 30
```

### Find the largest number without `max()`
```python
nums = [4, 9, 2, 7]
biggest = nums[0]
for n in nums:
    if n > biggest:
        biggest = n
print(biggest)
# Output: 9
```

### Count the vowels in a word
```python
word = "education"
count = 0
for ch in word:
    if ch in "aeiou":
        count += 1
print(count)
# Output: 5
```

### Reverse a string
```python
text = "Python"
rev = ""
for ch in text:
    rev = ch + rev          # put each new letter at the FRONT
print(rev)
# Output: nohtyP
```
(The shortcut is `text[::-1]`.)

### Fibonacci numbers (each number is the sum of the two before it)
```python
a, b = 0, 1
for _ in range(8):
    print(a, end=" ")
    a, b = b, a + b
print()
# Output: 0 1 1 2 3 5 8 13
```

### Average of a list
```python
marks = [80, 90, 70]
total = 0
for m in marks:
    total += m
print("Average:", total / len(marks))
# Output: Average: 80.0
```

### The "accumulator" idea
Most loops follow this recipe:
1. Create a variable **before** the loop (`total = 0`, `count = 0`, `text = ""`, `result = []`).
2. **Update** it inside the loop.
3. Use it **after** the loop.

---

- [Back to section index](./README.md)
