# Conditionals Practice

Related notes: [Conditionals](../../02-control-flow/conditionals.md)

## Practice Questions

1. Write a program to check whether a number is positive, negative or zero.
2. Write a program to check whether a number is even or odd.
3. Take a person's age and print "Adult" if it is 18 or more, otherwise "Minor". Then rewrite it using the ternary operator.
4. Write a grading program: 90+ is A, 75 to 89 is B, 60 to 74 is C, 40 to 59 is D, below 40 is Fail.
5. Check whether a year is a leap year. Test with 1900, 2000, 2023 and 2024.
6. Find the largest of three numbers using nested if, then using `and`.
7. Why does `if color == "red" or "blue":` always run? Fix it in two ways.
8. What is the difference between a chain of `if / elif / else` and several separate `if` statements?
9. What does `pass` do? Write an empty function and an empty `if` block using it.
10. Predict the output: `print(0 or "hello")`, `print("a" and "b")`, `print(not [])`.
11. Check whether a character is a vowel or a consonant, ignoring upper or lower case.
12. A shop gives a 10% discount on bills above 1000. If the customer is also a member, the discount becomes 15%. Print the final amount for a bill of 1200 and a member.
13. Print FizzBuzz for numbers 1 to 30.
14. Rewrite a 4-branch `if / elif` chain using `match / case` (Python 3.10+).

---

## Practice Answers

Try each question yourself first, then compare. There is often more than one correct way.

**Answer 1: positive, negative or zero**
```python
n = 0
if n > 0:
    print("Positive")
elif n < 0:
    print("Negative")
else:
    print("Zero")
# Output: Zero
```

**Answer 2: even or odd** (a number is even when dividing by 2 leaves remainder 0)
```python
n = 9
if n % 2 == 0:
    print("Even")
else:
    print("Odd")
# Output: Odd
```

**Answer 3: adult or minor, normal form and ternary form**
```python
age = 16
if age >= 18:
    print("Adult")
else:
    print("Minor")
# Output: Minor

print("Adult" if age >= 18 else "Minor")
# Output: Minor
```

**Answer 4: grading program**
```python
marks = 82
if marks >= 90:
    print("A")
elif marks >= 75:
    print("B")
elif marks >= 60:
    print("C")
elif marks >= 40:
    print("D")
else:
    print("Fail")
# Output: B
```
Because the chain goes top to bottom, each `elif` already knows the earlier conditions were false, so you only write the lower limit.

**Answer 5: leap years**
```python
for year in (1900, 2000, 2023, 2024):
    if (year % 4 == 0 and year % 100 != 0) or year % 400 == 0:
        print(year, "Leap year")
    else:
        print(year, "Not a leap year")
# Output: 1900 Not a leap year
# Output: 2000 Leap year
# Output: 2023 Not a leap year
# Output: 2024 Leap year
```

**Answer 6: largest of three numbers**
```python
a, b, c = 12, 45, 30

# using nested if
if a >= b:
    if a >= c:
        print("a")
    else:
        print("c")
else:
    if b >= c:
        print("b")
    else:
        print("c")
# Output: b

# using and
if a >= b and a >= c:
    print("a")
elif b >= a and b >= c:
    print("b")
else:
    print("c")
# Output: b
```

**Answer 7: why `if color == "red" or "blue":` always runs**

Python reads it as `(color == "red") or ("blue")`. The second part, `"blue"`, is a non-empty string, which is truthy, so the whole condition is always true. Two fixes:
```python
color = "green"
if color == "red" or color == "blue":
    print("matched")
else:
    print("not matched")
# Output: not matched

if color in ("red", "blue"):
    print("matched")
else:
    print("not matched")
# Output: not matched
```

**Answer 8: `if / elif / else` chain vs separate `if` statements**

In a chain, Python stops at the **first** true condition, so **at most one** block runs. With separate `if` statements, **every** condition is checked on its own, so **several** blocks can run (see [Elif](../../02-control-flow/conditionals.md#python-elif)).

**Answer 9: `pass`**

`pass` does nothing. It fills a block that has no code yet, because Python does not allow an empty block.
```python
def todo():
    pass

x = 5
if x > 10:
    pass
else:
    print("small")
# Output: small
```

**Answer 10: predict the output**
```python
print(0 or "hello")    # hello   0 is falsy, so or moves on and returns "hello"
print("a" and "b")     # b       "a" is truthy, so and returns the last value
print(not [])          # True    an empty list is falsy, and not flips it to True
```

**Answer 11: vowel or consonant, ignoring case**
```python
ch = "E"
if ch.lower() in "aeiou":
    print("Vowel")
else:
    print("Consonant")
# Output: Vowel
```

**Answer 12: discount**
```python
bill = 1200
is_member = True

discount = 0
if bill > 1000:
    discount = 10
    if is_member:
        discount = 15

final = bill - bill * discount / 100
print(final)
# Output: 1020.0
```

**Answer 13: FizzBuzz for 1 to 30**
```python
for i in range(1, 31):
    if i % 15 == 0:
        print("FizzBuzz")
    elif i % 3 == 0:
        print("Fizz")
    elif i % 5 == 0:
        print("Buzz")
    else:
        print(i)
```
`range(1, 31)` is used because the end value of `range` is not included.

**Answer 14: if / elif chain rewritten with match / case** (Python 3.10+)
```python
day = 2

# if / elif version
if day == 1:
    print("Monday")
elif day == 2:
    print("Tuesday")
elif day == 3:
    print("Wednesday")
else:
    print("Other")
# Output: Tuesday

# match / case version
match day:
    case 1:
        print("Monday")
    case 2:
        print("Tuesday")
    case 3:
        print("Wednesday")
    case _:
        print("Other")
# Output: Tuesday
```

---

- [Back to practice index](../README.md)
