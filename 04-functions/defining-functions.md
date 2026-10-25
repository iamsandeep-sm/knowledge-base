# Defining Functions - Revision Notes ~ sm

> **In one line:** A **function** is a named block of code that you write once and **call** (run) as many times as you want. You create it with the keyword `def`, followed by a name, brackets `( )` and a colon `:`.

## Table of Contents
- [What is a Function?](#what-is-a-function)
- [Defining and Calling](#defining-and-calling)
- [Anatomy of a Function](#anatomy-of-a-function)
- [Function with Parameters](#function-with-parameters)
- [Function that Returns a Value](#function-that-returns-a-value)
- [Run It Line by Line (Dry Run)](#run-it-line-by-line-dry-run)
- [Docstrings](#docstrings)
- [Empty Function with pass](#empty-function-with-pass)
- [Naming Functions](#naming-functions)
- [Functions are Objects](#functions-are-objects)
- [Functions Calling Functions](#functions-calling-functions)
- [Common Mistakes](#common-mistakes)

---

## What is a Function?

Without functions you repeat the same code again and again. A function lets you **write it once and reuse it**.

```python
print("Welcome, Riya!")
print("Welcome, Amit!")
print("Welcome, Sam!")
```
The same job is done three times. With a function:
```python
def welcome(name):
    print("Welcome,", name + "!")

welcome("Riya")
welcome("Amit")
welcome("Sam")
# Output: Welcome, Riya!
# Output: Welcome, Amit!
# Output: Welcome, Sam!
```

| Benefit | Meaning |
|---|---|
| **Reuse** | Write once, use many times (the "DRY" idea: Don't Repeat Yourself) |
| **Easy to read** | A good name explains what a block of code does |
| **Easy to fix** | A bug is corrected in one place, not ten |
| **Easy to test** | A small function can be checked on its own |

> You have already used functions: `print()`, `len()`, `input()`, `range()`. These are **built-in functions** (see the Built-in Functions note). Now you will write your own.

---

## Defining and Calling

There are two steps: **define** the function, then **call** it.

```python
def say_hello():          # 1. define
    print("Hello!")

say_hello()               # 2. call
say_hello()               # call it again
# Output: Hello!
# Output: Hello!
```

- **Defining** only stores the function. Nothing is printed yet.
- **Calling** (writing the name with brackets) runs the code inside it.
- A function can be called any number of times.

---

## Anatomy of a Function

```python
def add(a, b):
    """Return the sum of a and b."""
    total = a + b
    return total
```

| Part | Name | Meaning |
|---|---|---|
| `def` | Keyword | Tells Python "a function definition starts here" |
| `add` | Function name | What you type to call it |
| `(a, b)` | Parameters | Names for the values the function receives |
| `:` | Colon | Starts the function body |
| Indented lines | **Body** | The code that runs when the function is called (4 spaces) |
| `"""..."""` | Docstring | A description of the function (optional) |
| `return total` | Return statement | Sends a value back to the caller |

The body is a **block**, so the indentation rules from the Python Syntax note apply.

---

## Function with Parameters

A **parameter** is a name in the definition. An **argument** is the real value you give when calling.

```python
def greet(name):              # name is a parameter
    print("Hello,", name)

greet("Karan")                # "Karan" is an argument
# Output: Hello, Karan
```

More than one parameter, separated by commas:
```python
def introduce(name, city):
    print(name, "lives in", city)

introduce("Riya", "Pune")
# Output: Riya lives in Pune
```
Parameters are explained fully in the Parameters and Arguments note.

---

## Function that Returns a Value

`print()` only **shows** something. `return` **gives a value back** so you can store or use it.

```python
def square(n):
    return n * n

result = square(5)
print(result)
print(square(3) + square(4))
# Output: 25
# Output: 25
```
A function that has no `return` gives back `None`. See the Return Statement note.

---

## Run It Line by Line (Dry Run)

```python
def add(a, b):
    return a + b

x = add(2, 3)
print(x)
```

| Step | What Python does |
|---|---|
| 1 | Reads `def add...` and **remembers** the function. The body does not run yet |
| 2 | Runs `x = add(2, 3)`: calls `add` with `a = 2` and `b = 3` |
| 3 | Inside the function: `return a + b` calculates `5` and sends it back |
| 4 | `5` is stored in `x` |
| 5 | `print(x)` shows `5` |

Python runs a program from top to bottom. A function must be **defined before the line that calls it runs**.

---

## Docstrings

A **docstring** is a string in triple quotes placed as the **first line** of the body. It describes what the function does.

```python
def area(width, height):
    """Return the area of a rectangle."""
    return width * height

print(area.__doc__)
help(area)
# Output: Return the area of a rectangle.
```
`help(area)` prints the function's signature and its docstring. Write a docstring for every function that is not completely obvious.

---

## Empty Function with pass

A body cannot be empty. Use `pass` as a placeholder while you plan the code.

```python
def calculate_tax():
    pass            # write the code later

calculate_tax()     # runs, does nothing
```

---

## Naming Functions

| Rule / habit | Example |
|---|---|
| Same rules as variable names (letters, digits, `_`, not starting with a digit, not a keyword) | `total_price` |
| Use `snake_case` (small letters, words joined by `_`) | `get_user_name` |
| Start with a **verb**, because a function *does* something | `calculate_total`, `is_even` |
| Do not reuse built-in names | not `list`, `sum`, `max` |

---

## Functions are Objects

A function is a value like any other. Its name is a variable that points to it.

```python
def hello():
    return "hi"

print(hello())              # call it: runs the code
print(type(hello))          # the function object
say = hello                 # another name for the same function
print(say())
# Output: hi
# Output: <class 'function'>
# Output: hi
```
- `hello()` **calls** the function.
- `hello` (no brackets) is the function **itself**. It is not called.
- You can store functions in variables, put them in lists, and pass them to other functions.

---

## Functions Calling Functions

One function can call another.

```python
def square(n):
    return n * n

def sum_of_squares(a, b):
    return square(a) + square(b)

print(sum_of_squares(3, 4))
# Output: 25
```
Breaking a big job into small functions makes a program easier to understand.

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Forgetting the colon `def hi()` | `SyntaxError: expected ':'` | `def hi():` |
| Body not indented | `IndentationError: expected an indented block after function definition` | Indent by 4 spaces |
| Calling before defining | `NameError: name 'hi' is not defined` | Define the function first |
| Writing `hello` instead of `hello()` | Nothing runs; you get the function object | Add the brackets |
| Using `print` when you needed `return` | The result cannot be reused (you get `None`) | Use `return` |
| Wrong number of arguments | `TypeError: ... missing 1 required positional argument` | Give every parameter a value |

Calling before defining:
```python
hi()

def hi():
    print("hi")
# Error:  NameError: name 'hi' is not defined
```

---

- [Back to section index](./README.md)
