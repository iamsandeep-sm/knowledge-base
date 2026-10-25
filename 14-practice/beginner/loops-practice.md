# Loops Practice

Related notes: [Loops](../../02-control-flow/for-loops.md)

## Practice Questions

1. Print the numbers from 1 to 10 using a `for` loop.
2. Print the even numbers from 2 to 20.
3. Find the sum of the numbers from 1 to 100.
4. Print the multiplication table of 4 (from 4 x 1 to 4 x 5).
5. Print every letter of `"Python"` on **one line**, separated by spaces.
6. Find the factorial of 5.
7. Using a `while` loop, count down from 5 to 1 and then print `Go!`.
8. Print the first 8 Fibonacci numbers.
9. Print a right triangle of stars with 4 rows.
10. Find the first number between 1 and 100 that is divisible by **both** 7 and 5. Use `break`.
11. Print the numbers from 1 to 10 **except** the multiples of 3. Use `continue`.
12. Check whether 13 is a prime number using `for ... else`.
13. Find the sum of the digits of `4567` using a `while` loop.
14. Predict the output of this nested loop, then check by running it:
    ```python
    for i in range(3):
        for j in range(2):
            print(i, j)
    ```

---

## Practice Answers

Try each question yourself first, then compare.

**Answer 1**
```python
for i in range(1, 11):
    print(i, end=" ")
print()
# Output: 1 2 3 4 5 6 7 8 9 10
```

**Answer 2**
```python
for i in range(2, 21, 2):
    print(i, end=" ")
print()
# Output: 2 4 6 8 10 12 14 16 18 20
```

**Answer 3**
```python
total = 0
for i in range(1, 101):
    total += i
print(total)
# Output: 5050
```
(`range(1, 101)` is used because the end value is not included.)

**Answer 4**
```python
for i in range(1, 6):
    print(f"4 x {i} = {4 * i}")
# Output: 4 x 1 = 4
# Output: 4 x 2 = 8
# Output: 4 x 3 = 12
# Output: 4 x 4 = 16
# Output: 4 x 5 = 20
```

**Answer 5**
```python
for ch in "Python":
    print(ch, end=" ")
print()
# Output: P y t h o n
```

**Answer 6**
```python
fact = 1
for i in range(1, 6):
    fact *= i
print(fact)
# Output: 120
```

**Answer 7**
```python
n = 5
while n >= 1:
    print(n)
    n -= 1
print("Go!")
# Output: 5
# Output: 4
# Output: 3
# Output: 2
# Output: 1
# Output: Go!
```

**Answer 8**
```python
a, b = 0, 1
for _ in range(8):
    print(a, end=" ")
    a, b = b, a + b
print()
# Output: 0 1 1 2 3 5 8 13
```

**Answer 9**
```python
for i in range(1, 5):
    print("*" * i)
# Output: *
# Output: **
# Output: ***
# Output: ****
```

**Answer 10**
```python
for n in range(1, 101):
    if n % 7 == 0 and n % 5 == 0:
        print(n)
        break
# Output: 35
```

**Answer 11**
```python
for n in range(1, 11):
    if n % 3 == 0:
        continue
    print(n, end=" ")
print()
# Output: 1 2 4 5 7 8 10
```

**Answer 12**
```python
n = 13
if n < 2:
    print(n, "is not prime")
else:
    for i in range(2, n):
        if n % i == 0:
            print(n, "is not prime")
            break
    else:
        print(n, "is prime")
# Output: 13 is prime
```

**Answer 13**
```python
n = 4567
total = 0
while n > 0:
    total += n % 10
    n //= 10
print(total)
# Output: 22
```
(7 + 6 + 5 + 4 = 22)

**Answer 14**
```python
for i in range(3):
    for j in range(2):
        print(i, j)
# Output: 0 0
# Output: 0 1
# Output: 1 0
# Output: 1 1
# Output: 2 0
# Output: 2 1
```
The outer loop runs 3 times, and the inner loop runs 2 times for each of them, so there are 3 x 2 = 6 lines.

---

- [Back to practice index](../README.md)
