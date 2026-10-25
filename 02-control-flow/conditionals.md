# Python Conditional Statements - Revision Notes ~ sm

> **One-line definition:** Conditional statements let a program **make decisions**: it runs a block of code **only if a condition is true**, and can run different code when the condition is false.

## Table of Contents
- [What are Conditional Statements?](#what-are-conditional-statements)
- [Comparison Operators](#comparison-operators)
- [Python If](#python-if)
- [Indentation Rules](#indentation-rules)
- [Python Elif](#python-elif)
- [Python Else](#python-else)
- [Shorthand If](#shorthand-if)
- [Logical Operators](#logical-operators)
- [Truthy and Falsy Values](#truthy-and-falsy-values)
- [Membership and Identity in Conditions](#membership-and-identity-in-conditions)
- [Nested If](#nested-if)
- [Commonly Used Patterns](#commonly-used-patterns)

---

## What are Conditional Statements?

Normally Python runs code **line by line, top to bottom**. A conditional statement lets the program **choose** which lines to run, depending on whether something is `True` or `False`.

Real-life example: *"If it is raining, take an umbrella. Otherwise, wear sunglasses."*

```python
is_raining = True

if is_raining:
    print("Take an umbrella")
else:
    print("Wear sunglasses")
# Output: Take an umbrella
```

### Key words to know before you start

| Term | Plain meaning |
|---|---|
| **Condition** | A question whose answer is `True` or `False`, like `age >= 18` |
| **Boolean (`bool`)** | The only two possible answers: `True` or `False` |
| **Block** | The group of indented lines that belong to an `if`, `elif` or `else` |
| **Indentation** | The spaces at the start of a line; they show which block the line belongs to |
| **Colon `:`** | Ends the `if` / `elif` / `else` line and says "the block starts on the next line" |

### How to read an if statement in plain English
```python
age = 20
if age >= 18:
    print("Adult")
```
Read it like a sentence: **"If `age` is 18 or more, then do the indented lines below."**
If the answer to the condition is `False`, Python **skips** the indented lines and moves on.

### The four building blocks

| Keyword | Meaning | Used |
|---|---|---|
| `if` | Run this block **if** the condition is true | Always first, only once |
| `elif` | "else if": check **another** condition if the earlier ones were false | Zero or many times |
| `else` | Run this block if **none** of the conditions above were true | Zero or one time, always last |
| `pass` | Do nothing (placeholder) | Anywhere a block is required |

### Flow of an if-elif-else chain
```
condition 1 true?  --yes-->  run block 1  --> (skip the rest) --> continue program
        |no
condition 2 true?  --yes-->  run block 2  --> (skip the rest) --> continue program
        |no
      else block  ---------->  run else block                --> continue program
```
> Only **one** block of an if-elif-else chain ever runs.

---

## Comparison Operators

A **condition** is usually a comparison that gives `True` or `False` (a `bool` value).

| Operator | Meaning | Example | Result |
|---|---|---|---|
| `==` | Equal to | `5 == 5` | `True` |
| `!=` | Not equal to | `5 != 3` | `True` |
| `>` | Greater than | `5 > 3` | `True` |
| `<` | Less than | `5 < 3` | `False` |
| `>=` | Greater than or equal to | `5 >= 5` | `True` |
| `<=` | Less than or equal to | `4 <= 3` | `False` |

```python
a, b = 10, 20
print(a == b)    # False
print(a != b)    # True
print(a < b)     # True
print(a >= 10)   # True
```

### `=` is NOT `==`
- `=` is **assignment** (stores a value): `x = 5`
- `==` is **comparison** (asks a question): `x == 5`

```python
x = 5            # assignment
print(x == 5)    # True   comparison
# if x = 5:      # SyntaxError, you meant ==
```

### Chained comparisons (Python special)
```python
age = 25
print(18 <= age < 60)    # True    same as: age >= 18 and age < 60
print(1 < 5 < 3)         # False
```

### Comparing strings
String comparison is **case-sensitive**.
```python
print("apple" == "apple")    # True
print("apple" == "Apple")    # False
print("apple" == "Apple".lower())    # True
print("apple" < "banana")    # True   compared alphabetically (by character code)
```

### Comparing different types
```python
print(5 == 5.0)       # True    int and float can be equal
print("5" == 5)       # False   a string is never equal to a number
# print("5" > 3)      # TypeError: '>' not supported between instances of 'str' and 'int'
```

### Float comparison warning
Decimal numbers are stored approximately, so never rely on `==` with floats.
```python
import math
print(0.1 + 0.2 == 0.3)               # False
print(math.isclose(0.1 + 0.2, 0.3))   # True    correct way
```

---

## Python If

The `if` statement runs a block of code **only when its condition is `True`**.

### Syntax
```
if condition:
    # code that runs when the condition is True
```
- The line with `if` **must end with a colon `:`**.
- The code inside the block must be **indented** (usually 4 spaces).

### Example
```python
age = 20

if age >= 18:
    print("You are an adult")
# Output: You are an adult
```

### If the condition is false, nothing happens
```python
age = 15

if age >= 18:
    print("You are an adult")    # skipped

print("Program continues")
# Output: Program continues
```

### Code after the block always runs
Only the **indented** lines belong to the `if`. Unindented lines run every time.
```python
marks = 30

if marks >= 40:
    print("Pass")        # belongs to the if
    print("Well done")   # belongs to the if
print("Result checked")  # always runs
# Output: Result checked
```

### Taking input from the user
`input()` always returns a **string**, so convert it with `int()` or `float()` before comparing numbers.
```python
age = int(input("Enter your age: "))    # suppose the user types 20
if age >= 18:
    print("Eligible to vote")
# Output: Eligible to vote
```

---

## Indentation Rules

Python uses **indentation (spaces at the start of a line)** to show which code belongs inside a block. Other languages use `{ }`.

```python
number = 7

if number > 0:
    print("Positive")        # 4 spaces: inside the if
    print("Greater than 0")  # 4 spaces: still inside the if
print("Done")                # no indent: outside the if
# Output: Positive
# Output: Greater than 0
# Output: Done
```

| Rule | Detail |
|---|---|
| Standard size | **4 spaces** per level (PEP 8 recommendation) |
| Consistency | Every line in one block must have the **same** indentation |
| Do not mix | Do not mix tabs and spaces in the same file |
| Missing indent | `IndentationError: expected an indented block after 'if' statement` |
| Missing colon | `SyntaxError: expected ':'` |

```python
# Wrong: no indentation
# if 5 > 2:
# print("Five is greater")        # IndentationError

# Wrong: missing colon
# if 5 > 2
#     print("Five is greater")    # SyntaxError
```

---

## Python Elif

`elif` means **"else if"**. It lets you test **more than one condition** in order. Python checks them **top to bottom** and runs the **first** block whose condition is true, then skips all the rest.

### Syntax
```
if condition1:
    # runs if condition1 is True
elif condition2:
    # runs if condition1 is False AND condition2 is True
elif condition3:
    # runs if the earlier ones are False AND condition3 is True
```

### Example: grading
```python
marks = 72

if marks >= 90:
    print("Grade A")
elif marks >= 75:
    print("Grade B")
elif marks >= 60:
    print("Grade C")
elif marks >= 40:
    print("Grade D")
# Output: Grade C
```
Here `72 >= 90` is false, `72 >= 75` is false, `72 >= 60` is **true**, so "Grade C" prints and the remaining `elif` is **never checked**.

### Dry run: follow Python step by step
A *dry run* means doing what Python does, one line at a time, on paper. Try it for `marks = 72`:

| Step | Condition checked | Result | What happens |
|---|---|---|---|
| 1 | `marks >= 90` (72 >= 90) | False | Skip this block, go to the next check |
| 2 | `marks >= 75` (72 >= 75) | False | Skip this block, go to the next check |
| 3 | `marks >= 60` (72 >= 60) | **True** | Run `print("Grade C")` |
| 4 | `marks >= 40` | not checked | Python already found a match, so it jumps past the whole chain |

> Tip: when your program gives a wrong answer, do a dry run with the exact values. You will usually spot the mistake straight away.

### Order matters!
Put the **most specific / strictest** condition first.
```python
marks = 95

# Wrong order: the first condition is true for ALL marks of 40 or more
if marks >= 40:
    print("Grade D")        # prints, even though 95 deserves an A
elif marks >= 90:
    print("Grade A")        # never reached
# Output: Grade D
```

### `elif` chain vs separate `if` statements
```python
n = 10

# elif chain: only ONE block runs
if n > 5:
    print("greater than 5")
elif n > 8:
    print("greater than 8")       # skipped, because the first block already ran
# Output: greater than 5

# separate ifs: EACH condition is checked independently
if n > 5:
    print("greater than 5")
if n > 8:
    print("greater than 8")
# Output: greater than 5
# Output: greater than 8
```

| Structure | How many blocks can run? |
|---|---|
| `if` / `elif` / `elif` | **At most one** |
| `if` / `if` / `if` | **Any number** (each is checked separately) |

---

## Python Else

`else` catches **everything that did not match** the earlier conditions. It has **no condition** of its own.

### Syntax
```
if condition:
    # runs if condition is True
else:
    # runs if condition is False
```

### if-else
```python
age = 15

if age >= 18:
    print("Adult")
else:
    print("Minor")
# Output: Minor
```

### if-elif-else
```python
temperature = 28

if temperature >= 35:
    print("Very hot")
elif temperature >= 25:
    print("Warm")
elif temperature >= 15:
    print("Pleasant")
else:
    print("Cold")
# Output: Warm
```

### Even or odd
```python
number = 7

if number % 2 == 0:
    print("Even")
else:
    print("Odd")
# Output: Odd
```

### Rules for `else`
- It comes **last** in the chain.
- It has **no condition** and **no expression** after it, just `else:`.
- You can have only **one** `else` per `if`.
- `else` is **optional**.

```python
# Wrong: else cannot have a condition
# else age < 18:      # SyntaxError
```

---

## Shorthand If

Python offers one-line forms for simple conditions.

### One-line if (shorthand if)
When the block has **only one statement**, it can go on the same line.
```python
a, b = 10, 5

if a > b: print("a is greater")
# Output: a is greater
```

### Shorthand if-else (ternary operator)
```
value_if_true  if  condition  else  value_if_false
```
```python
age = 20
status = "Adult" if age >= 18 else "Minor"
print(status)    # Adult
```

Compare with the normal form:
```python
age = 20
if age >= 18:
    status = "Adult"
else:
    status = "Minor"
```

Printing directly:
```python
a, b = 10, 5
print("a is greater") if a > b else print("b is greater")
# Output: a is greater
```

### Multiple conditions in one line
```python
a, b = 330, 330
print("A") if a > b else print("=") if a == b else print("B")
# Output: =
```
> Chaining like this is hard to read. For more than two outcomes, prefer a normal `if / elif / else`.

### Useful ternary examples
```python
x = -5
print(abs(x) if x < 0 else x)                     # 5

n = 7
print("Even" if n % 2 == 0 else "Odd")            # Odd

items = []
print("Empty" if not items else "Has items")      # Empty

a, b = 3, 9
biggest = a if a > b else b
print(biggest)                                    # 9
```

### Rules
- Use the ternary form only for **simple, short** decisions.
- The ternary form is an **expression**: it produces a value you can store in a variable.
- It **must** have an `else` part. `x = 5 if cond` alone is a `SyntaxError`.

---

## Logical Operators

Logical operators **combine** conditions.

| Operator | Meaning | Result is `True` when |
|---|---|---|
| `and` | Both conditions | **both** are true |
| `or` | Either condition | **at least one** is true |
| `not` | Reverses | the condition is **false** |

### Truth tables

| A | B | `A and B` | `A or B` |
|---|---|---|---|
| True | True | True | True |
| True | False | False | True |
| False | True | False | True |
| False | False | False | False |

| A | `not A` |
|---|---|
| True | False |
| False | True |

### `and`
```python
age = 25
has_id = True

if age >= 18 and has_id:
    print("Entry allowed")
# Output: Entry allowed
```

### `or`
```python
day = "Sunday"

if day == "Saturday" or day == "Sunday":
    print("Weekend")
# Output: Weekend
```

### `not`
```python
logged_in = False

if not logged_in:
    print("Please log in")
# Output: Please log in
```

### Combining all three
```python
age = 20
has_ticket = True
is_banned = False

if age >= 18 and has_ticket and not is_banned:
    print("Welcome")
# Output: Welcome
```

### Precedence (order of evaluation)
`not` is checked first, then `and`, then `or`. Use **brackets** to make the intention clear.
```python
print(True or False and False)      # True    and runs first: True or (False and False)
print((True or False) and False)    # False   brackets change the result
print(not True or True)             # True    (not True) or True
```

### Short-circuit evaluation
Python stops as soon as the result is known.
- `A and B`: if `A` is false, `B` is **never checked**.
- `A or B`: if `A` is true, `B` is **never checked**.

```python
x = 0
if x != 0 and 10 / x > 1:    # safe: 10 / x is skipped when x is 0
    print("big")
else:
    print("skipped safely")
# Output: skipped safely
```

### `and` / `or` return one of the values, not just True/False
```python
print(0 or "default")        # default     first truthy value
print("hello" or "default")  # hello
print("a" and "b")           # b           last value when all are truthy
print([] and "b")            # []          first falsy value
name = ""
print(name or "Guest")       # Guest       common way to set a default
```

### Common mistake with `or`
```python
color = "red"

# Wrong: this is ALWAYS true, because the non-empty string "green" is truthy
if color == "blue" or "green":
    print("Matched")
# Output: Matched

# Correct ways
if color == "blue" or color == "green":
    print("Matched")        # not printed

if color in ("blue", "green"):
    print("Matched")        # not printed
```

---

## Truthy and Falsy Values

A condition does not have to be `True` or `False`. Python can treat **any value** as true or false.

### Falsy values (treated as `False`)
| Value | Type |
|---|---|
| `False` | bool |
| `None` | NoneType |
| `0`, `0.0`, `0j` | zero numbers |
| `""` | empty string |
| `[]`, `()`, `{}`, `set()`, `range(0)` | empty collections |

**Everything else is truthy** (non-zero numbers, non-empty strings and collections, etc.).

```python
print(bool(0))          # False
print(bool(""))         # False
print(bool([]))         # False
print(bool(None))       # False
print(bool(5))          # True
print(bool("hello"))    # True
print(bool([0]))        # True    a list with an item is truthy, even if the item is 0
print(bool("False"))    # True    a non-empty string is always truthy
```

### Using truthiness in `if`
```python
name = "Karan"
if name:                      # same as: if name != ""
    print("Hello", name)
# Output: Hello Karan

items = []
if not items:                 # preferred over: if len(items) == 0
    print("List is empty")
# Output: List is empty
```

---

## Membership and Identity in Conditions

### `in` and `not in` - is a value inside a collection?
```python
fruits = ["apple", "banana", "cherry"]

if "banana" in fruits:
    print("Banana found")
# Output: Banana found

print("grape" not in fruits)    # True
print("an" in "banana")         # True    works for substrings too
print("name" in {"name": "Karan", "age": 19})    # True   checks dictionary KEYS
```

### `is` and `is not` - is it the very same object?
`is` checks **identity** (same object in memory), `==` checks **equal value**.
```python
a = [1, 2]
b = [1, 2]
print(a == b)     # True    same values
print(a is b)     # False   different objects
```

Use `is` / `is not` mainly for `None`, `True` and `False`:
```python
result = None
if result is None:
    print("No result yet")
# Output: No result yet

print(result is not None)    # False
```

---

## Nested If

An `if` statement **inside another** `if` is called a **nested if**. The inner condition is checked **only if the outer condition is true**.

### Syntax
```
if outer_condition:
    # outer block
    if inner_condition:
        # inner block
```

### Example
```python
age = 25
has_license = True

if age >= 18:
    print("Age is fine")
    if has_license:
        print("You can drive")
    else:
        print("Get a license first")
else:
    print("Too young to drive")
# Output: Age is fine
# Output: You can drive
```

### Nested if with elif and else
```python
marks = 82
attendance = 70

if marks >= 40:
    if attendance >= 75:
        print("Pass")
    else:
        print("Pass, but attendance is low")
else:
    print("Fail")
# Output: Pass, but attendance is low
```

### Reduce nesting with `and`
A nested if often becomes simpler with `and`.
```python
age = 25
has_license = True

# Nested
if age >= 18:
    if has_license:
        print("You can drive")

# Same result, flatter
if age >= 18 and has_license:
    print("You can drive")
# Output: You can drive
# Output: You can drive
```
> Use a nested `if` when the inner check needs **different messages** for each case. Otherwise prefer `and`. Avoid going more than 2 to 3 levels deep.

### Nested if-else: largest of three numbers
```python
a, b, c = 12, 45, 30

if a >= b:
    if a >= c:
        print("a is largest")
    else:
        print("c is largest")
else:
    if b >= c:
        print("b is largest")
    else:
        print("c is largest")
# Output: b is largest
```
(Shorter way: `print(max(a, b, c))`.)

---

## Commonly Used Patterns

### Positive, negative or zero
```python
n = -4
if n > 0:
    print("Positive")
elif n < 0:
    print("Negative")
else:
    print("Zero")
# Output: Negative
```

### Leap year
A year is a leap year if it is divisible by 4 **and not** by 100, **or** it is divisible by 400.
```python
year = 2024
if (year % 4 == 0 and year % 100 != 0) or year % 400 == 0:
    print("Leap year")
else:
    print("Not a leap year")
# Output: Leap year

print(1900 % 4 == 0 and 1900 % 100 != 0 or 1900 % 400 == 0)    # False
print(2000 % 4 == 0 and 2000 % 100 != 0 or 2000 % 400 == 0)    # True
```

### Simple login check
```python
username = "admin"
password = "1234"

if username == "admin" and password == "1234":
    print("Login successful")
else:
    print("Invalid credentials")
# Output: Login successful
```

### Check a vowel
```python
ch = "e"
if ch.lower() in "aeiou":
    print("Vowel")
else:
    print("Consonant")
# Output: Vowel
```
> This works when `ch` is **one letter**. A longer text like `"ae"` would also pass `in "aeiou"`, so check `len(ch) == 1` first if the input comes from a user. Digits and symbols would be reported as "Consonant" too.

### Divisible by two numbers
```python
n = 30
if n % 3 == 0 and n % 5 == 0:
    print("Divisible by both 3 and 5")
elif n % 3 == 0:
    print("Divisible by 3 only")
elif n % 5 == 0:
    print("Divisible by 5 only")
else:
    print("Divisible by neither")
# Output: Divisible by both 3 and 5
```

### FizzBuzz (classic interview question)
```python
for i in range(1, 16):
    if i % 15 == 0:
        print("FizzBuzz")
    elif i % 3 == 0:
        print("Fizz")
    elif i % 5 == 0:
        print("Buzz")
    else:
        print(i)
```
> The `i % 15 == 0` check must come **first**, otherwise numbers like 15 would print only "Fizz".

Output of the program above (one item per line, shown here on one line):
```
1, 2, Fizz, 4, Buzz, Fizz, 7, 8, Fizz, Buzz, 11, Fizz, 13, 14, FizzBuzz
```

### Validate input
```python
age = 150
if age < 0 or age > 120:
    print("Invalid age")
else:
    print("Valid age")
# Output: Invalid age
```

### Triangle type
```python
a, b, c = 5, 5, 8
if a == b == c:
    print("Equilateral")
elif a == b or b == c or a == c:
    print("Isosceles")
else:
    print("Scalene")
# Output: Isosceles
```

### Dictionary instead of a long elif chain
```python
choice = "b"
actions = {"a": "Add", "b": "Subtract", "c": "Multiply"}
print(actions.get(choice, "Invalid choice"))    # Subtract
print(actions.get("z", "Invalid choice"))       # Invalid choice
```

---

- [Back to section index](./README.md)
