# While Loops

## While Loop

A `while` loop **keeps repeating as long as its condition is `True`**. When the condition becomes `False`, the loop stops.

### Syntax
```
while condition:
    # body: repeats while the condition is True
```

### Flow of a while loop
```
check the condition  --False-->  loop ends, program continues
        |True
   run the body
        |
   (go back and check the condition again)
```
> The condition is checked **before every round**, including the first. If it is `False` at the start, the body **never runs**.

### Your first while loop
```python
count = 1
while count <= 3:
    print("Count is", count)
    count += 1
# Output: Count is 1
# Output: Count is 2
# Output: Count is 3
```

| Round | `count` | `count <= 3` | What happens |
|---|---|---|---|
| 1 | 1 | True | print, then `count` becomes 2 |
| 2 | 2 | True | print, then `count` becomes 3 |
| 3 | 3 | True | print, then `count` becomes 4 |
| 4 | 4 | **False** | the loop stops |

### Three things every while loop needs
1. **A starting value** before the loop: `count = 1`
2. **A condition** that can become `False`: `count <= 3`
3. **A change inside the body** that moves toward `False`: `count += 1`

### Infinite loop: the classic mistake
If nothing in the body changes the condition, the loop **never stops**.
```python
# count = 1
# while count <= 3:
#     print("Count is", count)     # forgot count += 1, so this prints forever
```
If your program is stuck, press **Ctrl + C** to stop it.

### Countdown
```python
n = 3
while n > 0:
    print(n)
    n -= 1
print("Liftoff!")
# Output: 3
# Output: 2
# Output: 1
# Output: Liftoff!
```

### Sum of the numbers 1 to 5
```python
i = 1
total = 0
while i <= 5:
    total += i
    i += 1
print("Total:", total)
# Output: Total: 15
```

### Sum of the digits of a number
`n % 10` gives the **last digit**, and `n // 10` **removes** the last digit.
```python
n = 1234
total = 0
while n > 0:
    total += n % 10
    n //= 10
print("Sum of digits:", total)
# Output: Sum of digits: 10
```

### Reverse a number
```python
n = 1234
rev = 0
while n > 0:
    rev = rev * 10 + n % 10
    n //= 10
print("Reversed:", rev)
# Output: Reversed: 4321
```

### Keep asking until the answer is right
This is where `while` is better than `for`: you do not know how many attempts the user will need.
```python
password = ""
while password != "python123":
    password = input("Enter password: ")
print("Access granted")
# User types: abc
# User types: python123
# Output: Enter password: abc
# Output: Enter password: python123
# Output: Access granted
```

### `while True` with `break`
`while True` runs forever **on purpose**, and `break` ends it when you decide.
```python
total = 0
while True:
    text = input("Enter a number (or 'done'): ")
    if text == "done":
        break
    total += int(text)
print("Total:", total)
# User types: 5
# User types: 10
# User types: done
# Output: Enter a number (or 'done'): 5
# Output: Enter a number (or 'done'): 10
# Output: Enter a number (or 'done'): done
# Output: Total: 15
```

---

## for vs while

| | `for` loop | `while` loop |
|---|---|---|
| Repeats | For **each item**, or a **fixed** number of times | **While a condition is True** |
| Best when | You know how many times, or you have a collection | You do **not** know how many times |
| Counter | Handled for you | **You** must create and update it |
| Risk of infinite loop | Very low | **High**, if you forget to update |
| Example | Print each name in a list | Ask until the password is correct |

**Simple rule:** if you can say "for each ..." or "N times", use `for`. If you can say "keep going until ...", use `while`.

The same task both ways (print 1 to 3):
```python
for i in range(1, 4):
    print(i)
# Output: 1
# Output: 2
# Output: 3

i = 1
while i <= 3:
    print(i)
    i += 1
# Output: 1
# Output: 2
# Output: 3
```

---

- [Back to section index](./README.md)
