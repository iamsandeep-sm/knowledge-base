# Debugging - Revision Notes ~ sm

> **In one line:** **Debugging** is the skill of **finding out why a program does not do what you expect, and fixing it**. The tools are: reading the **traceback**, adding **`print`** or **`logging`** output, stepping through the code with the **debugger** (`pdb` or an editor), and a calm, step-by-step method.

## Table of Contents
- [Three Kinds of Errors](#three-kinds-of-errors)
- [Reading a Traceback](#reading-a-traceback)
- [Common Exceptions and Their Usual Causes](#common-exceptions-and-their-usual-causes)
- [A Method for Debugging](#a-method-for-debugging)
- [Print Debugging](#print-debugging)
- [Inspecting Objects](#inspecting-objects)
- [Logging](#logging)
- [The Debugger: pdb](#the-debugger-pdb)
- [Debugging in an Editor](#debugging-in-an-editor)
- [The traceback Module](#the-traceback-module)
- [Typical Bugs and Their Fixes](#typical-bugs-and-their-fixes)
- [Finding Slow Code](#finding-slow-code)
- [Common Mistakes](#common-mistakes)

---

## Three Kinds of Errors

| Kind | When it shows up | Example | How you find it |
|---|---|---|---|
| **Syntax error** | Before the program runs: Python cannot even read the code | A missing colon or bracket | Python points to the line |
| **Runtime error** (exception) | While the program runs | `1 / 0`, `int("abc")`, a missing key | The traceback shows where |
| **Logic error** | The program runs without errors but gives the **wrong answer** | Using `>` instead of `>=` | You must notice the wrong result and investigate |

Logic errors are the hardest, because Python has nothing to complain about. Tests (see the Unit Testing note) are the best way to catch them.

```python
def total(prices):
    result = 0
    for p in prices:
        result = p                 # logic error: should be result += p
    return result

print(total([10, 20, 30]))
# Output: 30
```
No error appears, but the answer should be `60`.

---

## Reading a Traceback

When an exception is not handled, Python prints a **traceback**: the chain of function calls that led to the error. Take this program, `shop.py`:

```text
def average(prices):
    total = sum(prices)
    return total / len(prices)


def report(cart):
    for name, prices in cart.items():
        print(name, average(prices))


cart = {"fruit": [10, 20], "toys": []}
report(cart)
```
Running it:
```text
fruit 15.0
Traceback (most recent call last):
  File "shop.py", line 12, in <module>
    report(cart)
    ~~~~~~^^^^^^
  File "shop.py", line 8, in report
    print(name, average(prices))
                ~~~~~~~^^^^^^^^
  File "shop.py", line 3, in average
    return total / len(prices)
           ~~~~~~^~~~~~~~~~~~~
ZeroDivisionError: division by zero
```
**How to read it**

1. **Start at the bottom.** The last line is the **error type and message**: `ZeroDivisionError: division by zero`.
2. The line above it is the **place where it happened**: file `shop.py`, line 3, inside the function `average`.
3. Going **up** shows **who called whom**: `average` was called by `report` (line 8), which was called from the main program (line 12). The newest call is last (`most recent call last`).
4. The `~~~^^^` marks (Python 3.11+) underline the part of the line that failed.
5. The first lines of output (`fruit 15.0`) ran fine before the crash.

**The real cause is often not on the line that crashed.** Here the crash is in `average`, but the **reason** is that `report` passed an empty list for `"toys"`. Ask: "what value made this line fail, and where did that value come from?" The fix may be a check in `average`, or filtering empty carts.

```python
def average(prices):
    if not prices:
        return 0
    return sum(prices) / len(prices)

print(average([10, 20]), average([]))
# Output: 15.0 0
```
**Long tracebacks**: the part that matters is usually the **last frame that is in your own code**, plus the error message. Frames inside library code only show how the library reached the error.

**A syntax error** also names the line and shows a marker:
```python
print("hello"
# Error: SyntaxError: '(' was never closed
```
(Run in a file, Python shows the line with `^` under the problem. The problem is often on the line **before** the one reported, such as a missing closing bracket.)

---

## Common Exceptions and Their Usual Causes

| Exception | Typical message | Usual cause |
|---|---|---|
| `SyntaxError` | `invalid syntax`, `'(' was never closed` | Missing colon, bracket or quote; misspelt keyword |
| `IndentationError` | `expected an indented block` | Wrong or mixed indentation |
| `NameError` | `name 'x' is not defined` | Typo, using a variable before assigning it, forgot to `import` |
| `TypeError` | `unsupported operand type(s)`, `... missing 1 required positional argument` | Wrong type (`"5" + 5`), wrong number of arguments, calling something not callable |
| `ValueError` | `invalid literal for int() with base 10: 'abc'` | Right type, wrong value |
| `IndexError` | `list index out of range` | Index too large; empty list; off-by-one |
| `KeyError` | `'age'` | Dictionary key does not exist |
| `AttributeError` | `'str' object has no attribute 'push'` | Wrong type of object, typo in method name, a variable that is `None` |
| `ZeroDivisionError` | `division by zero` | Dividing by `0` |
| `FileNotFoundError` | `No such file or directory` | Wrong path or working folder |
| `ModuleNotFoundError` | `No module named 'x'` | Not installed, wrong environment, or your own file name hides it |
| `RecursionError` | `maximum recursion depth exceeded` | Missing base case |
| `UnboundLocalError` | `cannot access local variable` | Assigned inside a function without `global` / `nonlocal` |

```python
data = {"name": "Riya"}
data["age"]
# Error: KeyError: 'age'
```
```python
numbers = [1, 2, 3]
numbers[3]
# Error: IndexError: list index out of range
```
```python
int("abc")
# Error: ValueError: invalid literal for int() with base 10: 'abc'
```
```python
"5" + 5
# Error: TypeError: can only concatenate str (not "int") to str
```
```python
result = None
result.upper()
# Error: AttributeError: 'NoneType' object has no attribute 'upper'
```
The last one is extremely common: a function **forgot to `return`** (or returned `None` on one path), and the next line used the result. Look **upwards** to find where the `None` came from.

---

## A Method for Debugging

Guessing and changing things at random wastes time. Work like a detective:

| Step | What to do |
|---|---|
| 1. **Reproduce** | Make the bug happen reliably. Note the exact input. |
| 2. **Read** the error message and traceback fully | They usually say what and where. |
| 3. **Isolate** | Shrink the problem: find the smallest code and input that still fails (a **minimal example**). |
| 4. **Form a hypothesis** | "I think `n` is a string at this point." |
| 5. **Test the hypothesis** | Print the value, use the debugger, or write a test. Do **not** change code yet. |
| 6. **Fix** one thing | Change one thing at a time. |
| 7. **Verify** | Run again, and run the other tests: did you break anything? |
| 8. **Prevent** | Add a test so the bug cannot return. |

Useful habits:
- **Check your assumptions.** "The list has 5 items" is a belief; `print(len(items))` is a fact.
- **Divide and conquer.** Print a value halfway through the code. If it is right, the bug is later; if wrong, earlier. Repeat (like a binary search).
- **Rubber duck debugging.** Explain the code line by line, out loud, to a rubber duck or a friend. You often find the mistake yourself while explaining.
- **Take a break.** A fresh mind sees what a tired one misses.
- **Read the code you wrote, not the code you meant to write.**
- **Do not trust the comments or function names**; trust what you can observe.
- **Use version control (`git`)**. `git diff` shows what changed since it last worked; `git bisect` finds the change that introduced a bug.
- **Search the exact error message** (without your own names). Someone has met it before.

---

## Print Debugging

The simplest tool: print values and see what the program is doing.

```python
x = 7
name = "Riya"
print(f"{x=}")
print(f"{name=}")
print(f"{x * 2 = }")
# Output: x=7
# Output: name='Riya'
# Output: x * 2 = 14
```
`f"{x=}"` (Python 3.8+) prints the **name and the value** together, and uses `repr`, so strings show their quotes.

Tips for useful prints:
```python
def find_max(numbers):
    best = numbers[0]
    for n in numbers:
        print(f"checking {n!r}, best so far {best!r}")      # trace
        if n > best:
            best = n
    return best

print(find_max([3, 9, 4]))
# Output: checking 3, best so far 3
# Output: checking 9, best so far 3
# Output: checking 4, best so far 9
# Output: 9
```
| Tip | Why |
|---|---|
| **Label** every print (`"after loop:"`) | You can tell which line printed |
| Use **`!r`** or `repr()` | Shows `'5'` vs `5` and hidden spaces: `repr("hi ")` is `'hi '` |
| Print the **type** too: `print(type(x))` | Many bugs are "this is a string, not a number" |
| Print **lengths** and **empty collections** | Spot empty or too-short data |
| Print **before** the line that crashes | The failing line never gets to print |
| Use `print(..., file=sys.stderr)` | Keeps debug output apart from real output |

```python
value = "42 "
print(repr(value), type(value))
print(value == "42", value.strip() == "42")
# Output: '42 ' <class 'str'>
# Output: False True
```
**Remove the debug prints** when you are done, or better, switch to `logging`, which can be turned on and off without editing the code.

---

## Inspecting Objects

When you do not know what an object is or can do:

```python
text = "hello"

print(type(text))
print(isinstance(text, str))
print("upper" in dir(text))
print(len(dir(text)) > 40)
# Output: <class 'str'>
# Output: True
# Output: True
# Output: True
```
| Tool | Shows |
|---|---|
| `type(x)` | The type |
| `isinstance(x, T)` | Is it that type (or a child)? |
| `dir(x)` | Names of all attributes and methods |
| `help(x)` | Documentation (press `q` to leave) |
| `vars(obj)` / `obj.__dict__` | An object's attributes as a dictionary |
| `id(x)` | Identity: are two names the same object? |
| `len(x)` | Size |
| `callable(x)` | Can it be called like a function? |

```python
class Dog:
    def __init__(self, name):
        self.name = name
        self.age = 3

d = Dog("Tommy")
print(vars(d))
# Output: {'name': 'Tommy', 'age': 3}
```
The interactive shell (`python`) or `python -i script.py` (run the script, then stay in the shell with all its variables) are perfect for trying things.

---

## Logging

`print` is quick, but `logging` is the proper tool: it has **levels**, **timestamps**, can write to a **file**, and can be switched on or off without editing code.

| Level | Number | Use for |
|---|---|---|
| `DEBUG` | 10 | Detailed information for diagnosing problems |
| `INFO` | 20 | Confirmation that things work as expected |
| `WARNING` | 30 | Something unexpected, but the program continues (this is the **default** threshold) |
| `ERROR` | 40 | A serious problem: something failed |
| `CRITICAL` | 50 | The program may not be able to continue |

By default only `WARNING` and above are shown:
```python
import logging
import sys

logging.basicConfig(stream=sys.stdout, format="%(levelname)s: %(message)s", force=True)

logging.debug("details")
logging.info("started")
logging.warning("disk almost full")
logging.error("could not save")
# Output: WARNING: disk almost full
# Output: ERROR: could not save
```
Set the level to see more:
```python
import logging
import sys

logging.basicConfig(
    stream=sys.stdout,
    level=logging.DEBUG,
    format="%(levelname)s: %(message)s",
    force=True,
)

logging.debug("x = %s", 5)
logging.info("started")
# Output: DEBUG: x = 5
# Output: INFO: started
```
Notes:
- `logging.basicConfig(...)` configures the whole program. It is ignored if logging is already configured, unless you pass `force=True` (Python 3.8+).
- Without `stream=`, the output goes to **standard error** (the terminal), not to standard output.
- Prefer `logging.debug("x = %s", x)` over an f-string: the text is built **only if** that level is enabled.

**Useful format fields**

| Field | Meaning |
|---|---|
| `%(asctime)s` | Date and time |
| `%(levelname)s` | `DEBUG`, `INFO`, ... |
| `%(name)s` | Name of the logger |
| `%(message)s` | Your message |
| `%(filename)s`, `%(lineno)d`, `%(funcName)s` | Where the log call is |

A realistic setup that writes to a **file**:
```text
logging.basicConfig(
    filename="app.log",
    level=logging.INFO,
    format="%(asctime)s %(levelname)s %(name)s: %(message)s",
)
```
**One logger per module** is the usual habit:
```python
import logging

log = logging.getLogger(__name__)      # the name is the module's name

def divide(a, b):
    if b == 0:
        log.error("attempt to divide %s by zero", a)
        return None
    return a / b

print(divide(1, 0))
# Output: None
```
**Log an exception with its traceback** using `logging.exception()` inside an `except` block (or `log.exception`). It logs at `ERROR` level and adds the traceback:
```python
import io
import logging

buffer = io.StringIO()
log = logging.getLogger("demo")
log.addHandler(logging.StreamHandler(buffer))
log.propagate = False

try:
    1 / 0
except ZeroDivisionError:
    log.exception("calculation failed")

lines = buffer.getvalue().splitlines()
print(lines[0])
print(lines[-1])
# Output: calculation failed
# Output: ZeroDivisionError: division by zero
```
This is how real programs record errors without crashing: catch, log with the traceback, carry on.

---

## The Debugger: pdb

A **debugger** lets you **pause** the program on a chosen line and look around: see variables, run line by line, and watch how the values change. Python's built-in debugger is **`pdb`**.

**Start it**

1. Put `breakpoint()` in your code (Python 3.7+). The program stops there.
2. Or run the whole script under the debugger: `python -m pdb script.py`.

If an uncaught exception happens under `python -m pdb`, the debugger stops at the failing line ("post-mortem") so you can inspect the state.

Here is a buggy program, `buggy.py`, meant to count the **even** numbers:
```text
def count_even(numbers):
    count = 0
    for n in numbers:
        if n % 2 == 1:
            count += 1
    return count


result = count_even([1, 2, 3, 4])
print(result)
```
It prints `2`, which looks right for `[1, 2, 3, 4]` (two evens), but for `[2, 4, 6]` it would print `0`. Run `python -m pdb buggy.py` and type these commands (shown after the `(Pdb)` prompt):

```text
> buggy.py(1)<module>()
-> def count_even(numbers):
(Pdb) b 4
Breakpoint 1 at buggy.py:4
(Pdb) c
> buggy.py(4)count_even()
-> if n % 2 == 1:
(Pdb) p n
1
(Pdb) p n % 2 == 1
True
(Pdb) c
> buggy.py(4)count_even()
-> if n % 2 == 1:
(Pdb) p n
2
(Pdb) p count
1
(Pdb) l
  1  	def count_even(numbers):
  2  	    count = 0
  3  	    for n in numbers:
  4 B->	        if n % 2 == 1:
  5  	            count += 1
  6  	    return count
  7  	
  8  	
  9  	result = count_even([1, 2, 3, 4])
 10  	print(result)
(Pdb) q
```
What we learn: for `n = 1` the condition `n % 2 == 1` is **true**, so `1` (an **odd** number) was counted. The test should be `== 0`. The debugger showed the real values at the moment of the bug.

**The commands you need**

| Command | Long name | Does |
|---|---|---|
| `n` | `next` | Run the current line, **skip over** function calls |
| `s` | `step` | Run the current line, **step into** a function call |
| `c` | `continue` | Run until the next breakpoint (or the end) |
| `r` | `return` | Run until the current function returns |
| `l` | `list` | Show the source around the current line |
| `ll` | `longlist` | Show the whole current function |
| `p expr` | `print` | Print the value of an expression |
| `pp expr` | `pprint` | Pretty-print (good for big dictionaries) |
| `w` | `where` | Show the call stack |
| `u` / `d` | `up` / `down` | Move up or down the call stack |
| `b 12` | `break` | Set a breakpoint at line 12 (`b file.py:12` for another file) |
| `b 12, n > 5` | | A **conditional** breakpoint: stops only when `n > 5` |
| `cl 1` | `clear` | Remove breakpoint number 1 |
| `unt 20` | `until` | Continue until line 20 |
| `a` | `args` | Show the function's arguments |
| `h` | `help` | Help (`h n` for one command) |
| `q` | `quit` | Leave the debugger |
| Enter | | Repeat the last command |

Anything that is not a command is run as Python: typing `count + 10` shows the result. Use `!` before a name that clashes with a command (`!n = 5`).

**`breakpoint()` in code**
```text
def process(items):
    for item in items:
        breakpoint()            # the program stops here, at every pass
        handle(item)
```
Set the environment variable `PYTHONBREAKPOINT=0` to **switch every `breakpoint()` off** without editing the code. Remove the calls before sharing your program.

---

## Debugging in an Editor

Editors such as **VS Code** and **PyCharm** have a graphical debugger. It does what `pdb` does, with buttons and windows.

| Feature | Meaning |
|---|---|
| **Breakpoint** | Click beside a line number; the program pauses there |
| **Step Over** | Run the line (like `n`) |
| **Step Into** | Go inside the function being called (like `s`) |
| **Step Out** | Finish the current function (like `r`) |
| **Continue** | Run to the next breakpoint (like `c`) |
| **Variables** panel | All local and global variables with their current values |
| **Watch** | Type an expression to see it update at each step |
| **Call stack** | The chain of calls (like `w`) |
| **Conditional breakpoint** | Stops only when a condition is true |
| **Debug console** | Run Python on the paused program |

For anything bigger than a few lines, a graphical debugger is the quickest way to understand what is happening.

---

## The traceback Module

When you catch an exception you can still get the traceback text.

```python
import traceback

def risky():
    return 1 / 0

try:
    risky()
except ZeroDivisionError:
    text = traceback.format_exc()

lines = text.splitlines()
print(lines[0])
print(lines[-1])
# Output: Traceback (most recent call last):
# Output: ZeroDivisionError: division by zero
```
| Function | Does |
|---|---|
| `traceback.format_exc()` | The traceback as a **string** |
| `traceback.print_exc()` | **Print** the traceback (to stderr) |
| `traceback.print_stack()` | Print the current call stack |

The exception object has details too: `except ValueError as error:` gives `error.args`, `str(error)`, `type(error).__name__` and `error.__traceback__`.

```python
try:
    int("abc")
except ValueError as error:
    print(type(error).__name__)
    print(error)
# Output: ValueError
# Output: invalid literal for int() with base 10: 'abc'
```

---

## Typical Bugs and Their Fixes

| Bug | Example | Fix |
|---|---|---|
| **Off-by-one** | `range(1, 10)` to get 1 to 10 | `range(1, 11)`: the stop is not included |
| **`=` instead of `==`** | `if x = 5:` | `if x == 5:` (`SyntaxError`) |
| **`input()` is text** | `age = input()` then `age + 1` | `int(input())` |
| **Integer vs float division** | `7 / 2` gives `3.5`, `7 // 2` gives `3` | Pick the one you mean |
| **Forgot `return`** | A function prints but the caller gets `None` | `return value` |
| **Mutable default argument** | `def f(x, items=[])` | `items=None` |
| **Changing a list while looping over it** | `for x in items: items.remove(x)` | Loop over a copy or build a new list |
| **Alias, not copy** | `b = a` then changing `b` changes `a` | `b = a.copy()` or `copy.deepcopy(a)` |
| **Shadowing a built-in** | `list = [1, 2]` then `list("ab")` | Use other names (`items`, `total`) |
| **Comparing floats with `==`** | `0.1 + 0.2 == 0.3` | `math.isclose` |
| **Wrong scope** | Assigning to a global inside a function | `global`, or better, pass and return values |
| **Unclosed resources** | A file never closed | `with open(...)` |
| **Wrong indentation** | A line is inside or outside the loop by mistake | Check the indentation level |
| **Forgetting that strings are immutable** | `s.upper()` on its own line | `s = s.upper()` |
| **Late binding in loops** | Functions made in a loop all use the last value | Default argument `i=i` |
| **Wrong working folder** | `FileNotFoundError` for a file that exists | `print(Path.cwd())`; use full paths |

```python
numbers = [1, 2, 3, 4, 5, 6]

# Bug: removing while looping skips items
for n in numbers:
    if n % 2 == 0:
        numbers.remove(n)
print(numbers)

# Fix: build a new list
numbers = [1, 2, 3, 4, 5, 6]
numbers = [n for n in numbers if n % 2 != 0]
print(numbers)
# Output: [1, 3, 5]
# Output: [1, 3, 5]
```
(In this one example the buggy loop gives the right-looking answer; with `[2, 4, 6]` it would leave `[4]`. Never rely on it.)

---

## Finding Slow Code

Do not guess where a program is slow: **measure**.

```python
import timeit

seconds = timeit.timeit("sum(range(1000))", number=1000)
print(type(seconds).__name__, seconds > 0)
# Output: float True
```
`timeit.timeit(code, number=n)` runs `code` `n` times and returns the **total seconds**. Compare two ways of doing the same thing:
```python
import timeit

loop = timeit.timeit("[n * 2 for n in range(100)]", number=2000)
mapped = timeit.timeit("list(map(lambda n: n * 2, range(100)))", number=2000)
print(loop > 0 and mapped > 0)
# Output: True
```
(Which is faster depends on the computer and Python version, so run it yourself.)

To find which **function** takes the time, use the profiler from the terminal:
```text
python -m cProfile -s cumtime script.py
```
It lists every function with the number of calls and the time spent. Fix the biggest cost first. For memory use, see the `tracemalloc` module. Remember: **make it correct first, then make it fast**, and only if it is too slow.

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Reading the traceback from the top | You miss the actual error | Start at the **last line** |
| Changing many things at once | You cannot tell what fixed (or broke) it | One change at a time |
| Debugging by guessing | Hours wasted | Print or inspect the real values |
| Fixing the line that crashed, not the cause | The bug moves elsewhere | Find where the bad value **came from** |
| Leaving `print` or `breakpoint()` in the final code | Noisy output; the program stops in production | Remove them, or use `logging` |
| `except: pass` | Hides the very error you need | Catch specific exceptions and log them |
| Using `print` for everything | No levels, no file, hard to switch off | `logging` |
| Calling `logging.basicConfig()` twice and getting no change | The second call is ignored | `force=True`, or configure once at the start |
| Forgetting `-m` in `python -m pdb script.py` | `pdb` is not run as a program | `python -m pdb script.py` |
| Debugging in the wrong environment | `ModuleNotFoundError` for an installed package | Check `sys.executable` and the active virtual environment |
| Not reproducing the bug first | You "fix" something that was not the problem | Reproduce, then fix, then verify |
| No test after the fix | The bug returns | Add a test for it |
| Trusting a function without testing its edge cases | Empty and zero inputs crash it | Test empty, zero, one item, negative |

---

- [Back to section index](./README.md)
