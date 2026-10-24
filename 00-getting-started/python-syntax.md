# Python Syntax - Revision Notes ~ sm

> **In one line:** **Syntax** is the set of grammar rules of a language. In Python the most important rules are: **indentation** shows which lines belong together, a **colon `:`** starts a block, one statement usually goes on **one line**, and Python is **case-sensitive**.

## Table of Contents
- [What is Syntax?](#what-is-syntax)
- [Statements and Lines](#statements-and-lines)
- [Indentation](#indentation)
- [Blocks and the Colon](#blocks-and-the-colon)
- [Comments and Docstrings](#comments-and-docstrings)
- [Case Sensitivity](#case-sensitivity)
- [Names (Identifiers) and Keywords](#names-identifiers-and-keywords)
- [Quotes and Strings](#quotes-and-strings)
- [Numbers in Code](#numbers-in-code)
- [Style Rules (PEP 8 Basics)](#style-rules-pep-8-basics)
- [Syntax Errors](#syntax-errors)
- [A Typical Program Layout](#a-typical-program-layout)

---

## What is Syntax?

Every language has grammar rules. If you break them, the reader cannot understand you. Python is the reader of your code, and it is strict: **one broken rule and the program does not run at all.**

| Term | Plain meaning |
|---|---|
| **Syntax** | The grammar rules of Python |
| **Statement** | One complete instruction, such as `print("Hi")` or `x = 5` |
| **Expression** | Something that has a value, such as `2 + 3` or `age >= 18` |
| **Block** | A group of statements that belong together (the indented lines) |
| **Keyword** | A word Python reserves for itself, such as `if`, `for`, `def` |
| **Syntax error** | A rule was broken, so Python refuses to run the file |

---

## Statements and Lines

### One statement per line
Normally each line holds one statement. There is no semicolon at the end.
```python
x = 5
y = 10
print(x + y)
# Output: 15
```

### Several statements on one line (possible, but avoid it)
A semicolon `;` separates statements on the same line.
```python
a = 1; b = 2
print(a + b)
# Output: 3
```
Most Python code puts each statement on its own line, because it is easier to read.

### Long lines: continue on the next line
**1. Inside brackets `( )`, `[ ]`, `{ }`**, Python knows the statement is not finished, so you can simply press Enter. This is the preferred way.
```python
nums = [1,
        2,
        3]
print(nums)
# Output: [1, 2, 3]
```

**2. With a backslash `\`** at the end of the line:
```python
total = 1 + 2 + \
        3
print(total)
# Output: 6
```
The backslash must be the **last character** on the line (nothing after it, not even a space).

---

## Indentation

Many languages use `{ }` to group code. Python uses **indentation**: the spaces at the start of a line.

```python
x = 5
if x > 3:
    print("big")            # inside the if (4 spaces)
    print("still inside")   # inside the if
print("outside")            # not indented: outside the if
# Output: big
# Output: still inside
# Output: outside
```

| Rule | Detail |
|---|---|
| Standard size | **4 spaces** for each level (the PEP 8 recommendation) |
| Same block, same indentation | Every line of one block must be indented by the **same** amount |
| Do not mix tabs and spaces | Choose spaces. Mixing causes `TabError` |
| Indent only where needed | An extra indent on a normal line is an error |
| Nested blocks | Each new level adds 4 more spaces |

```python
for i in range(2):
    if i == 0:
        print("zero")       # 8 spaces: inside the for AND the if
    else:
        print("not zero")   # 8 spaces
# Output: zero
# Output: not zero
```

### Indentation errors you will meet

**1. No indentation after a colon**
```python
if True:
print("x")
```
```
IndentationError: expected an indented block after 'if' statement on line 1
```

**2. Indentation where none is expected**
```python
x = 1
 y = 2
```
```
IndentationError: unexpected indent
```

**3. Indentation that matches no earlier level**
```python
if True:
        print("a")
    print("b")
```
```
IndentationError: unindent does not match any outer indentation level
```

**4. Tabs and spaces mixed in one block**
```
TabError: inconsistent use of tabs and spaces in indentation
```
Fix: use only spaces. Most editors can insert 4 spaces when you press Tab.

> `IndentationError` and `TabError` are special kinds of `SyntaxError`.

---

## Blocks and the Colon

A line that **starts a block** ends with a **colon `:`**. The indented lines below it form the block.
```python
if age >= 18:      # colon, then an indented block
    print("Adult")
```

These statements are followed by a colon and a block:

| Statement | Used for |
|---|---|
| `if`, `elif`, `else` | Decisions |
| `for`, `while` | Loops |
| `def` | Functions |
| `class` | Classes |
| `try`, `except`, `else`, `finally` | Error handling |
| `with` | Context managers |
| `match`, `case` | Pattern matching |

Forgetting the colon:
```python
x = 5
if x > 3
    print("big")
```
```
SyntaxError: expected ':'
```

### An empty block needs `pass`
A block cannot be empty. `pass` is a statement that does nothing, used as a placeholder.
```python
if x > 10:
    pass          # write the code later
```

---

## Comments and Docstrings

### Comments
Everything after `#` on a line is ignored by Python.
```python
# This whole line is a comment
print("Hi")    # a comment after code
# Output: Hi
```

### Docstrings
A **docstring** is a string placed as the **first line** of a function (or class or file) to describe it. It uses triple quotes.
```python
def greet():
    """Say hello."""
    return "hello"

print(greet.__doc__)
# Output: Say hello.
```
A docstring is documentation that Python keeps and tools can show (for example `help(greet)`). A comment is thrown away.

---

## Case Sensitivity

Python treats capital and small letters as **different**.
```python
age = 1
Age = 2
print(age, Age)
# Output: 1 2
```

| Written | Result |
|---|---|
| `print("Hi")` | Works |
| `Print("Hi")` | `NameError: name 'Print' is not defined` |
| `True` / `False` / `None` | Work |
| `true` / `false` / `none` | `NameError`, because these names are not defined |

This applies to variable names, function names and keywords.

---

## Names (Identifiers) and Keywords

A **name** (identifier) is what you call a variable, function or class.

### Rules for names
- Use letters, digits and underscore `_`.
- A name **cannot start with a digit**.
- **No spaces** and no symbols like `-`, `$`, `@`.
- A name **cannot be a keyword**.
- Names are case-sensitive.

| Valid | Invalid | Why |
|---|---|---|
| `user_name` | `user-name` | `-` is not allowed |
| `item1` | `1item` | Starts with a digit |
| `first_name` | `first name` | Contains a space |
| `class_name` | `class` | `class` is a keyword |

> Names such as `café` are allowed too, because Python accepts Unicode letters. For clarity, most code uses English letters, digits and `_`.

### Keywords
**Keywords** are words with a special meaning. You cannot use them as names. Python has **35** of them:

```
False   None    True    and     as      assert  async   await
break   class   continue def    del     elif    else    except
finally for     from    global  if      import  in      is
lambda  nonlocal not    or      pass    raise   return  try
while   with    yield
```
You can print the list on your own computer:
```python
import keyword
print(keyword.kwlist)
```

### Soft keywords
`match`, `case`, `type` and `_` are **soft keywords**. They only act as keywords in certain places, so they can still be used as ordinary names elsewhere. It is still best to avoid them.
```python
import keyword
print(keyword.softkwlist)
# Output: ['_', 'case', 'match', 'type']
```

> Built-in function names such as `print`, `list`, `str`, `max` and `sum` are **not** keywords, but do not use them as variable names, because you would hide the function.

---

## Quotes and Strings

- Text goes inside **single `'...'`** or **double `"..."`** quotes. Both are the same.
- Use **triple quotes** (`"""..."""`) for text over several lines.
- Start and end with the **same** kind of quote.
```python
print('It is a "quote"')
print("It's fine")
print("""Line 1
Line 2""")
# Output: It is a "quote"
# Output: It's fine
# Output: Line 1
# Output: Line 2
```
Two strings written side by side are joined automatically:
```python
s = "a" "b"
print(s)
# Output: ab
```
Strings are covered fully in the Strings note.

---

## Numbers in Code

- Whole numbers: `10`, `-5`. Decimals: `3.14`. Do not use commas: `1,000` is **not** one thousand.
- Use underscores to make long numbers easy to read:
```python
print(1_000_000)
# Output: 1000000
```
- `**` is power (`2 ** 3` is `8`), and `//` and `%` give the whole-number part and the remainder of a division. See the Numbers and Operators notes.

---

## Style Rules (PEP 8 Basics)

**PEP 8** is the official style guide for Python code. These rules are not forced by Python, but nearly every Python programmer follows them.

| Rule | Good | Avoid |
|---|---|---|
| Indent with 4 spaces | `····print("x")` | Tabs, 2 spaces, 8 spaces |
| Lines up to 79 characters | Break long lines inside brackets | Very long lines |
| Variables and functions in `snake_case` | `total_marks` | `TotalMarks`, `totalMarks` |
| Constants in `UPPER_CASE` | `MAX_SIZE = 100` | |
| Classes in `CapWords` | `StudentRecord` | |
| Spaces around `=` and operators | `x = a + b` | `x=a+b` |
| One statement per line | Separate lines | `a = 1; b = 2` |
| Meaningful names | `price` | `p` |

> Linters and formatters can check style for you. PEP 8 and clean code have their own note in the Testing and Code Quality section.

---

## Syntax Errors

A **syntax error** means the code breaks a grammar rule. Python finds it **before running anything**, so none of the program's output appears.
```python
print("This line never runs")
x = (1 + 2
```
```
SyntaxError: '(' was never closed
```

| Error | Usual cause |
|---|---|
| `SyntaxError: expected ':'` | Missing colon after `if`, `for`, `def` ... |
| `SyntaxError: '(' was never closed` | Missing closing bracket |
| `SyntaxError: unterminated string literal` | Missing closing quote |
| `SyntaxError: invalid syntax` | A rule is broken in some other way (for example `=` instead of `==` in an `if`; newer versions add a hint such as "Maybe you meant '=='") |
| `IndentationError` | Wrong or missing indentation |
| `TabError` | Tabs and spaces mixed |

### Syntax error vs runtime error
| | Syntax error | Runtime error (exception) |
|---|---|---|
| When it is found | **Before** the program runs | **While** the program runs |
| Example | Missing colon | `10 / 0` (`ZeroDivisionError`), unknown name (`NameError`) |
| Output before it | None | Earlier lines already ran |

```python
print("start")
print(undefined_name)
# Output: start
# Error:  NameError: name 'undefined_name' is not defined
```
Runtime errors are handled with `try` / `except` (see the Exception Handling section).

---

## A Typical Program Layout

```python
# greeting.py
# A small program that greets the user.

import math                         # 1. imports at the top

PI_ROUNDED = round(math.pi, 2)      # 2. constants

def greet(name):                    # 3. functions
    """Return a greeting."""
    return f"Hello, {name}!"

user = "Karan"                      # 4. the main code
print(greet(user))
print(PI_ROUNDED)
# Output: Hello, Karan!
# Output: 3.14
```
Order: comments and imports first, then constants and functions, then the code that runs. You will learn each part in later notes.

---

- [Back to section index](./README.md)
