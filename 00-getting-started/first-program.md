# First Program - Revision Notes ~ sm

> **In one line:** The first Python program is `print("Hello, World!")`. It shows how to write a line of code, save it in a `.py` file and run it, and it teaches the parts every Python line is made of: a **function**, an **argument** and a **string**.

## Table of Contents
- [Before You Start](#before-you-start)
- [Hello, World!](#hello-world)
- [Understanding Every Part](#understanding-every-part)
- [Run It Line by Line (Dry Run)](#run-it-line-by-line-dry-run)
- [More print() Examples](#more-print-examples)
- [Program 2: Greet the User](#program-2-greet-the-user)
- [Program 3: A Small Calculation](#program-3-a-small-calculation)
- [Adding Comments](#adding-comments)
- [What Can Go Wrong](#what-can-go-wrong)
- [Python 2 Style Does Not Work](#python-2-style-does-not-work)

---

## Before You Start

You need two things:
1. Python installed (see the Python Installation note). Check with `python --version` (Windows) or `python3 --version` (macOS and Linux).
2. A place to write code: a text editor or IDLE. Save files with the **`.py`** extension.

> On macOS and Linux, use `python3` instead of `python` in the commands below.

---

## Hello, World!

"Hello, World!" is the traditional first program in every language. It does one job: **show a message on the screen**.

### Steps
1. Create a folder, for example `python-notes`.
2. Create a file in it named `hello.py` and type this line:
```python
print("Hello, World!")
```
3. Save the file.
4. Open a terminal, go to the folder and run it:
```
cd python-notes
python hello.py
```
```
Hello, World!
```

If you see `Hello, World!`, Python is installed and working, and you have run your first program.

### Try it in the shell first
You can also type the same line at the `>>>` prompt (type `python` to start it). The result is the same, but nothing is saved.
```
>>> print("Hello, World!")
Hello, World!
```

---

## Understanding Every Part

```python
print("Hello, World!")
```

| Part | Name | Meaning |
|---|---|---|
| `print` | **Function** | A ready-made tool in Python that shows output on the screen |
| `( )` | **Brackets** | Needed to **call** (use) a function. The input goes inside them |
| `"Hello, World!"` | **String** (`str`) | Text. It is written inside quotes |
| The text inside the brackets | **Argument** | The value you hand to the function |
| The whole line | **Statement** | One complete instruction that Python can run |

Rules to remember:
- Python is **case-sensitive**: `print` works, `Print` and `PRINT` do not.
- The quotes can be double (`"..."`) or single (`'...'`). Use the same kind at both ends.
- The quotes are **not** printed. They only mark where the text starts and ends.
- Each `print()` starts its output on a **new line**.
- There is **no semicolon** needed at the end of the line.

---

## Run It Line by Line (Dry Run)

A *dry run* means doing what Python does, one step at a time. Take this program:
```python
print("Hello")
print("Python")
```

| Step | What Python does | Output so far |
|---|---|---|
| 1 | Reads line 1, calls `print` with `"Hello"` | `Hello` |
| 2 | Moves to line 2, calls `print` with `"Python"` | `Hello` then `Python` on the next line |
| 3 | No more lines, so the program ends | |

Python always runs the lines **in order, from top to bottom**.

---

## More print() Examples

```python
print("Hello, World!")
print("Python is fun")
print(10)
print(3 + 4)
# Output: Hello, World!
# Output: Python is fun
# Output: 10
# Output: 7
```
- Text goes inside quotes. **Numbers and calculations go without quotes**: `print(3 + 4)` shows `7`.
- With quotes, the calculation is just text: `print("3 + 4")` shows `3 + 4`.

### Several values in one print
Separate them with commas. Python puts **one space** between them.
```python
print("My name is", "Karan")
print("Age:", 19)
# Output: My name is Karan
# Output: Age: 19
```

### Change the separator and the ending
```python
print("A", "B", "C", sep="-")
print("x", end="")
print("y")
# Output: A-B-C
# Output: xy
```
`sep` is what goes **between** the items, and `end` is what goes at the **end** (a new line by default). These are explained in the Output Formatting note.

### A blank line
```python
print("Line 1")
print()
print("Line 3")
# Output: Line 1
# Output:
# Output: Line 3
```

---

## Program 2: Greet the User

This program asks for a name and an age, then replies. It uses `input()`, which **always returns text**, so the age is converted with `int()`.
```python
name = input("What is your name? ")
age = int(input("How old are you? "))
print(f"Hello, {name}! Next year you will be {age + 1}.")
# User types: Karan
# User types: 19
# Output: What is your name? Karan
# Output: How old are you? 19
# Output: Hello, Karan! Next year you will be 20.
```

| Line | What it does |
|---|---|
| 1 | Shows the question, waits for the user to type, and stores the answer in `name` |
| 2 | Asks for the age, converts the typed text to a whole number with `int()`, and stores it in `age` |
| 3 | Prints a message. The `f` before the quote lets you write variables and calculations inside `{ }` |

You will learn these properly in the Variables, User Input, Type Conversion and Output Formatting notes.

---

## Program 3: A Small Calculation

```python
price = 25
quantity = 4
total = price * quantity
print("Total:", total)
# Output: Total: 100
```
- `price`, `quantity` and `total` are **variables**: names that hold values.
- `=` **stores** a value in a name. It is not the "equals" of maths.
- `*` means multiply.
- Python runs the lines top to bottom, so `price` and `quantity` are stored before they are used on the third line.

---

## Adding Comments

A **comment** is a note for humans. Python ignores everything after `#`.
```python
# This program says hello
print("Hello, World!")   # this prints a message
# Output: Hello, World!
```
Good habit: start a program with a comment that says what it does.

---

## What Can Go Wrong

Beginners meet these errors first. Python's message tells you what is wrong, so read it.

### 1. Missing closing quote
```python
print("Hello)
```
```
SyntaxError: unterminated string literal (detected at line 1)
```
Fix: close the quote: `print("Hello")`.

### 2. Missing closing bracket
```python
print("Hello"
```
```
SyntaxError: '(' was never closed
```
Fix: add the `)`.

### 3. Wrong capital letters
```python
Print("hi")
```
```
NameError: name 'Print' is not defined. Did you mean: 'print'?
```
Fix: Python is case-sensitive, so write `print` in small letters.

### 4. Unknown name (forgot quotes around text)
```python
print(Hello)
```
This gives `NameError: name 'Hello' is not defined`, because without quotes Python looks for a **variable** called `Hello`. Text must be inside quotes.

### 5. Extra space at the start of the line
```python
 print("Hi")
```
This gives `IndentationError: unexpected indent`. A line of normal code should start at the very beginning of the line (see the Python Syntax note).

> The exact wording of an error message can differ a little between Python versions. Always read the **last line** of the message first.

---

## Python 2 Style Does Not Work

Old tutorials use Python 2, where `print` did not need brackets:
```python
print "Hello"
```
In Python 3 this is an error:
```
SyntaxError: Missing parentheses in call to 'print'. Did you mean print(...)?
```
Fix: `print("Hello")`. Python 3 always needs the brackets.

---

- [Back to section index](./README.md)
