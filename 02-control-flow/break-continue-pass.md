# Break, Continue and Pass

## Control Statements in Loops

Control statements change how a loop normally runs.

| Statement | What it does |
|---|---|
| `break` | **Stops the whole loop** immediately |
| `continue` | **Skips the rest of this round** and jumps to the next round |
| `pass` | **Does nothing** (a placeholder) |
| `else` (on a loop) | Runs **only if the loop finished without `break`** |

### `break` - stop the loop
```python
for n in range(1, 50):
    if n % 7 == 0:
        print("First multiple of 7:", n)
        break
# Output: First multiple of 7: 7
```
Without `break` the loop would continue to 49 even after finding the answer.

Searching a list:
```python
names = ["Karan", "Riya", "Sam", "Tom"]
for name in names:
    if name == "Sam":
        print("Found Sam")
        break
    print("Checking", name)
# Output: Checking Karan
# Output: Checking Riya
# Output: Found Sam
```

### `continue` - skip this round
```python
for n in range(1, 10):
    if n % 2 == 0:
        continue          # skip even numbers
    print(n, end=" ")
print()
# Output: 1 3 5 7 9
```
When `continue` runs, the lines **below it** are skipped for that round, and the loop goes straight to the next item.

> `break` ends the **whole loop**. `continue` ends only the **current round**.

With a `while` loop, make sure the counter is updated **before** `continue`, or you create an infinite loop:
```python
i = 0
while i < 5:
    i += 1                # update FIRST
    if i == 3:
        continue
    print(i, end=" ")
print()
# Output: 1 2 4 5
```

### `pass` - do nothing
Python needs at least one line inside a loop body. `pass` fills it when you have no code yet.
```python
for i in range(3):
    pass                  # TODO: write the code later
print("Loop finished")
# Output: Loop finished
```

### `else` with loops
A loop can have an `else` part. It runs **when the loop ends normally** (it was **not** stopped by `break`).
```python
for n in [1, 3, 5]:
    if n % 2 == 0:
        print("Found an even number")
        break
else:
    print("No even number found")
# Output: No even number found
```
Now the same code with a list that contains an even number. `break` happens, so `else` is **skipped**:
```python
for n in [1, 2, 3]:
    if n % 2 == 0:
        print("Found an even number")
        break
else:
    print("No even number found")
# Output: Found an even number
```
Easy way to remember: **`else` means "no break happened"**.

### Prime number check using `for ... else`
A prime number is greater than 1 and divisible only by 1 and itself.
```python
n = 8
if n < 2:
    print(n, "is not prime")
else:
    for i in range(2, n):
        if n % i == 0:
            print(n, "is not prime")
            break
    else:
        print(n, "is prime")
# Output: 8 is not prime
```
(The `n < 2` check is needed because 0 and 1 are not prime.)

### `break`, `continue` and `pass` side by side
```python
for n in range(1, 6):
    if n == 2:
        continue          # skip 2
    if n == 4:
        break             # stop at 4
    print(n)
# Output: 1
# Output: 3
```
Round by round: 1 is printed. 2 is skipped. 3 is printed. At 4 the loop stops, so 5 is never reached.

---

## Pass Statement

`pass` is a statement that **does nothing**. Python needs at least one statement inside every block, so `pass` acts as a **placeholder** when you have no code yet.

```python
age = 20

if age >= 18:
    pass            # TODO: write the code later
print("Program runs fine")
# Output: Program runs fine
```

### Why it is needed
An empty block is an error:
```python
# if age >= 18:
# print("x")        # IndentationError: expected an indented block
```
With `pass` the block is valid.

### Where `pass` is used
```python
# empty if / elif / else branch
x = 5
if x > 10:
    pass
else:
    print("x is small")
# Output: x is small

# empty function (write the body later)
def calculate_tax():
    pass

# empty class
class Student:
    pass

# empty loop body
for i in range(3):
    pass
```

### `pass` vs `continue` vs `break`
| Statement | What it does |
|---|---|
| `pass` | Does **nothing**; the program carries on to the next line |
| `continue` | Skips the rest of this loop turn and goes to the next turn |
| `break` | Stops the loop completely |

> Comments (`# ...`) are **not** statements, so a block containing only a comment is still empty and needs `pass`.

---

- [Back to section index](./README.md)
