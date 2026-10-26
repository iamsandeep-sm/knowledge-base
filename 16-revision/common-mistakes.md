# Common Mistakes

## Table of Contents
- [Getting Started](#getting-started)
- [Basics](#basics)
- [Conditionals](#conditionals)
- [Loops](#loops)
- [Lists](#lists)
- [Tuples](#tuples)
- [Dictionaries](#dictionaries)
- [Sets](#sets)
- [Functions](#functions)
- [Comprehensions](#comprehensions)
- [Modules and Packages](#modules-and-packages)
- [Exception Handling](#exception-handling)
- [File Handling](#file-handling)

---

## Getting Started

| Mistake | What happens | Fix |
|---|---|---|
| Typing `python` on Windows and the Microsoft Store opens | The app execution alias is off | Turn on **Python (default)** in *Manage app execution aliases*, or install with the install manager |
| `python` not found on macOS / Linux | `command not found` | Use `python3` |
| Typing `python hello.py` at the `>>>` prompt | `SyntaxError: invalid syntax` | `exit()` first, then run it at the terminal prompt |
| Running from the wrong folder | `can't open file ... No such file or directory` | `cd` into the folder that holds the file, or give the path |
| Saving as `hello.py.txt` | The file is not run as Python | Save with the `.py` extension (show file extensions) |
| Naming your file `random.py` (or another standard module name) | `AttributeError: module 'random' has no attribute ...` | Rename your file |
| `print "Hi"` (Python 2 style) | `SyntaxError: Missing parentheses in call to 'print'` | `print("Hi")` |
| Forgetting `)` | `SyntaxError: '(' was never closed` | Add the bracket |
| Missing `:` after `if`, `for`, `def` | `SyntaxError: expected ':'` | Add the colon |
| No indent after a colon | `IndentationError: expected an indented block` | Indent by 4 spaces |
| Mixing tabs and spaces | `TabError` | Use only spaces |
| Writing code in Word or WordPad | Hidden formatting breaks the file | Use a plain-text editor or IDE |

---

## Basics

| Mistake | What happens | Fix |
|---|---|---|
| `print("Hello)` (missing quote) | `SyntaxError` | Close every quote and bracket |
| `Print("Hi")` | `NameError`, Python is case-sensitive | `print("Hi")` |
| Using a variable before assigning it | `NameError` | Assign first, then use |
| `1name = "x"` or `my name = "x"` | `SyntaxError` | Start with a letter or `_`, no spaces |
| Using a keyword as a name (`class = 5`) | `SyntaxError` | Choose another name |
| `age = input("Age: ")` then `age + 1` | `TypeError`, because `age` is text | `age = int(input("Age: "))` |
| `"Age: " + 19` | `TypeError` | `"Age: " + str(19)` or an f-string |
| `int("3.5")` | `ValueError` | `int(float("3.5"))` |
| `int("abc")` | `ValueError` | Check the input first, or use `try` / `except` |
| `x = 5` when you meant to compare | It assigns instead of comparing | Use `==` to compare |
| `true` or `false` in lowercase | `NameError` | `True` and `False` |
| `0.1 + 0.2 == 0.3` | `False` | Use `round()` or `math.isclose()` |
| `7 / 2` expecting `3` | You get `3.5` | Use `//` for whole-number division |
| `-2 ** 2` expecting `4` | You get `-4` | `(-2) ** 2` |
| `text[0] = "J"` on a string | `TypeError`, strings cannot change | Build a new string |
| `b = a` expecting a copy of a list | Both names share one list | `b = a.copy()` |
| Naming a variable `list`, `str` or `max` | Breaks the built-in function | Use another name |
| Division by zero | `ZeroDivisionError` | Check the divisor first |

---

## Conditionals

| Mistake | Problem | Fix |
|---|---|---|
| `if x = 5:` | `SyntaxError`, `=` is assignment | `if x == 5:` |
| `if x > 5` (no colon) | `SyntaxError` | `if x > 5:` |
| No indentation after `if` | `IndentationError` | Indent the block by 4 spaces |
| Mixed tabs and spaces | `TabError` or confusing bugs | Use 4 spaces everywhere |
| `if x == 1 or 2:` | Always true, because `2` is truthy | `if x == 1 or x == 2:` or `if x in (1, 2):` |
| `else x > 5:` | `SyntaxError`, `else` has no condition | Use `elif x > 5:` |
| Using `if` repeatedly instead of `elif` | Several blocks run | Use `elif` for exclusive choices |
| Wrong `elif` order (`>= 40` before `>= 90`) | A lower grade catches high marks | Test the strictest condition first |
| Comparing `input()` to a number | `"5" == 5` is `False`; `"5" > 3` is `TypeError` | `int(input(...))` first |
| `0.1 + 0.2 == 0.3` | `False` because of float precision | `math.isclose(...)` |
| `if x == None:` | Works but not recommended | `if x is None:` |
| `if flag == True:` | Verbose | `if flag:` |
| `if len(items) == 0:` | Verbose | `if not items:` |
| `x = 5 if cond` (no else) | `SyntaxError` | `x = 5 if cond else 0` |
| Empty block with only a comment | `IndentationError` | Put `pass` in the block |
| Case-sensitive text comparison | `"Yes" != "yes"` | Compare `text.lower()` |

---

## Loops

| Mistake | What happens | Fix |
|---|---|---|
| Forgetting the colon: `for i in range(3)` | `SyntaxError` | `for i in range(3):` |
| Not indenting the body | `IndentationError` | Indent by 4 spaces |
| Forgetting to update the counter in a `while` loop | **Infinite loop** | Add `i += 1` (press Ctrl + C to stop) |
| Expecting `range(5)` to include 5 | It stops at **4** | `range(6)` or `range(1, 6)` |
| `for i in 5:` | `TypeError: 'int' object is not iterable` | `for i in range(5):` |
| `range(1.5)` or a float step | `TypeError` | Use whole numbers |
| `range(0, 10, 0)` | `ValueError`, step cannot be 0 | Use a non-zero step |
| Setting `total = 0` **inside** the loop | `total` restarts every round | Set it **before** the loop |
| Printing the total **inside** the loop by mistake | It prints every round | Put `print` after the loop (not indented) |
| Removing items from a list while looping over it | Items get skipped | Loop over a copy `nums[:]` or build a new list |
| `while x = 5:` | `SyntaxError` | `while x == 5:` |
| `break` or `continue` outside a loop | `SyntaxError` | Use them only inside a loop |
| Expecting `break` in a nested loop to stop both loops | It stops only the **inner** loop | Use a flag, then `break` again |
| `continue` in a `while` loop before updating the counter | **Infinite loop** | Update the counter first |
| Using the loop variable after an empty loop | `NameError` | Create the variable before the loop |
| Same variable name in nested loops | The inner loop overwrites the outer one | Use `i` and `j` |
| Using `range(len(x))` when you do not need the index | More code than needed | `for item in x:` |
| Thinking `else` on a loop is like `if/else` | It runs when **no break** happened | Remember: `else` = "no break" |

---

## Lists

| Mistake | Problem | Fix |
|---|---|---|
| `lst[5]` on a 3-item list | `IndexError` | Check `len(lst)` first |
| `b = a` to copy | Both names share one list | `a.copy()`, `a[:]` or `list(a)` |
| `copy()` on nested lists | Inner lists are shared | `copy.deepcopy()` |
| `x = lst.append(1)` | `x` is `None` | Just call `lst.append(1)` |
| `lst = lst.sort()` | `lst` becomes `None` | `lst.sort()` or `lst = sorted(lst)` |
| `remove("x")` when missing | `ValueError` | Check with `in` first |
| Removing items inside `for x in lst` | Skips items | Loop over a copy or use a comprehension |
| `[[0] * 3] * 3` | All rows are the same list | `[[0] * 3 for _ in range(3)]` |
| `append([3, 4])` expecting two items | Adds one nested list | Use `extend([3, 4])` |
| `extend("abc")` expecting one item | Adds each character | Use `append("abc")` |
| `", ".join([1, 2])` | `TypeError`, items must be strings | `", ".join(map(str, lst))` |
| Sorting mixed types | `TypeError` | Keep one type, or use a `key` function |
| `for p in lst: p = p * 2` | List not changed | Use `lst[i] = ...` or a comprehension |
| `index()` for a missing item | `ValueError` | Check with `in` first |

---

## Tuples

| Mistake | Problem | Fix |
|---|---|---|
| `t = (5)` | It's an `int`, not a tuple | `t = (5,)` |
| `t[0] = 10` | `TypeError` — immutable | Convert to list, change, convert back |
| `t.append(4)` | `AttributeError` — no such method | Use list, or `t + (4,)` |
| `t[10]` | `IndexError` | Check `len(t)` first |
| `a, b = (1, 2, 3)` | `ValueError` — too many values | Match variables, or use `*` |
| Expecting `sorted(t)` to return a tuple | It returns a **list** | `tuple(sorted(t))` |

---

## Dictionaries

| Mistake | Problem | Fix |
|---|---|---|
| `d['missing']` | `KeyError` | Use `d.get('missing')` or check `in` first |
| `d = {}` expecting a set | It is a dict | `set()` for an empty set |
| `d[0]` expecting the first item | Looks up the **key** `0` | Use `list(d.items())[0]` |
| `{[1, 2]: 'x'}` | `TypeError`, list is unhashable | Use a tuple `(1, 2)` as the key |
| `'Karan' in d` to search values | `in` checks **keys** | `'Karan' in d.values()` |
| Deleting keys inside `for k in d` | `RuntimeError` | Loop over `list(d)` |
| `b = a` to copy | Both names share one dict | `a.copy()` or `copy.deepcopy(a)` |
| `copy()` with nested lists/dicts | Inner objects are shared | `copy.deepcopy()` |
| `dict.fromkeys(keys, [])` | All keys share one list | Use a comprehension: `{k: [] for k in keys}` |
| Repeating a key in `{}` | Last value silently wins | Keep keys unique |
| `d.update(x)` expecting a new dict | `update()` changes in place and returns `None` | Use `d | x` or `{**d, **x}` |
| `d.keys()[0]` | `TypeError`, views are not indexable | `list(d.keys())[0]` |

---

## Sets

| Mistake | Problem | Fix |
|---|---|---|
| `s = {}` | Creates a **dict**, not a set | `s = set()` |
| `s[0]` | `TypeError` — no indexing | Loop, or convert to list/sorted list |
| `{1, [2, 3]}` | `TypeError` — unhashable item | Use a tuple `(2, 3)` instead |
| `s.remove("x")` when missing | `KeyError` | Use `discard()` |
| Thinking `pop()` removes the last item | It removes an **arbitrary** item | Don't rely on which item is removed |
| `s.add("a", "b")` | `TypeError` — add takes 1 item | Use `update(["a", "b"])` |
| `s.update("hello")` expecting one item | Adds each **character** | Use `add("hello")` |
| `sorted(s)` expecting a set back | Returns a **list** | `set(sorted(s))` won't keep order — keep it as a list |
| Expecting a fixed print order | Sets are unordered | Use `sorted(s)` when you need order |
| `A - B` vs `B - A` | Different results | Remember difference is order-sensitive |

---

## Functions

| Mistake | What happens | Fix |
|---|---|---|
| `def hi()` without the colon | `SyntaxError: expected ':'` | `def hi():` |
| Calling a function before defining it | `NameError` | Define it first |
| `hello` instead of `hello()` | Nothing runs (you get the function object) | Add the brackets |
| Using `print` instead of `return` | The caller receives `None` | `return` the value |
| Code written after `return` | It never runs | Put it before `return` |
| `return` inside the loop by mistake | Returns after the first item | Put it after the loop |
| Too few / too many arguments | `TypeError: ... missing 1 required positional argument` | Match the parameters |
| `def f(a=1, b)` | `SyntaxError` (default before non-default) | `def f(b, a=1)` |
| `def f(items=[])` | The same list is shared by all calls | Use `items=None` and create the list inside |
| `count += 1` inside a function for a global | `UnboundLocalError` | Use `global count`, or pass in and `return` |
| `sum = 10` or `list = [...]` | `TypeError: ... object is not callable` | Do not reuse built-in names |
| Recursion with no base case | `RecursionError` | Add a base case that is always reached |
| Lambdas created in a loop all give the last value | Late binding | `lambda i=i: i` |

---

## Comprehensions

| Mistake | What happens | Fix |
|---|---|---|
| `[x for x in a else 0]` | `SyntaxError` | A filter `if` goes at the **end** |
| `[x if x > 0 for x in a]` | `SyntaxError` | `if ... else` goes at the **start** |
| Expecting the original list to change | A new list is made | Assign the result |
| `[a.append(x) for x in b]` | Builds a list of `None` | Use a normal `for` loop |
| `[[0] * 3] * 3` | All rows are the same list | `[[0] * 3 for _ in range(3)]` |
| `{x for x in a}` expecting a dict | It is a set | `{x: ... for x in a}` |
| `for k, v in d` | Unpacking error (keys only) | `d.items()` |
| `(x for x in a)` expecting a tuple | It is a generator | `tuple(x for x in a)` |
| `len(gen)` or `gen[0]` | `TypeError` | Convert to a list first |
| Using a generator twice | Second time it is empty | Recreate it |
| Very long, nested comprehension | Hard to read | Use a normal loop |

---

---

## Modules and Packages

| Mistake | What happens | Fix |
|---|---|---|
| `import mymath.py` | `ModuleNotFoundError: ... 'mymath' is not a package` | `import mymath` (no `.py`) |
| Misspelled or not-installed module | `ModuleNotFoundError: No module named '...'` | Check the name, `python -m pip install ...` |
| Module is in another folder | `ModuleNotFoundError` | Keep it next to the script, or in a package |
| Naming your file `random.py`, `math.py`, `json.py` | Hides the real module (`AttributeError`) | Rename it and delete `__pycache__` |
| `from math import *` | Name clashes, unclear origin | `import math` |
| Test code at the top level of a module | Runs on every import | Put it under `if __name__ == "__main__":` |
| Editing a module and not seeing the change | Old copy is still loaded | Restart (or `importlib.reload`) |
| Two modules importing each other | `ImportError` (circular import) | Move shared code to a third module |
| Running `python pkg/mod.py` with relative imports | `ImportError: attempted relative import with no known parent package` | `python -m pkg.mod` from the project folder |
| `python -m pkg/mod` (slash) | Error | Use dots: `python -m pkg.mod` |
| Plain `pip install` goes to the wrong Python | Installed, yet `ModuleNotFoundError` | `python -m pip install ...` |
| `error: externally-managed-environment` | System Python is protected | Use a virtual environment |
| `sudo pip install` | Can damage system tools | Use a virtual environment |
| Installing before activating the venv | Package goes to the global Python | Check for `(.venv)`, then install |
| Copying or committing `.venv/` | Large, not portable | Share `requirements.txt`, add `.venv/` to `.gitignore` |
| `pip install six>=1.15` unquoted | Shell treats `>` as redirection | `pip install "six>=1.15"` |

---

## Exception Handling

| Mistake | What happens | Fix |
|---|---|---|
| Bare `except:` | Hides every error, even `Ctrl + C` | `except ValueError:` (or `except Exception:`) |
| `except: pass` | Errors vanish silently | Print or log them |
| General `except` before a specific one | The specific block never runs | Specific first |
| A huge `try` block | Hard to know what failed | Wrap only the risky line |
| `except ValueError, TypeError:` | `SyntaxError` in 3.13 and older | `except (ValueError, TypeError):` |
| Using `e` after the `except` block | `NameError` | Use it inside the block |
| `else` without `except`, or before `except` | `SyntaxError` | Order: `try`, `except`, `else`, `finally` |
| `return` inside `finally` | Overrides the result, can hide exceptions | Do not return from `finally` |
| Expecting `finally` to stop the error | It only runs, then the error continues | Add an `except` |
| Using a variable in `finally` that `try` never set | `NameError` | Give it a start value first (`f = None`) |
| `raise "text"` | `TypeError: exceptions must derive from BaseException` | `raise ValueError("text")` |
| Bare `raise` outside `except` | `RuntimeError: No active exception to reraise` | Raise a specific exception |
| New error inside `except` without `from` | Confusing "During handling..." traceback | `raise NewError(...) from e` |
| `assert` to check user input | Skipped with `python -O` | Use `if` and `raise` |
| Trying to `try/except` a syntax error in the same file | Python never starts the file | Fix the code |
| `int(input())` crashes on text | `ValueError` | Wrap in `try` / `except ValueError` in a loop |

---

---

## File Handling

| Mistake | What happens | Fix |
|---|---|---|
| Wrong file name or wrong folder | `FileNotFoundError: [Errno 2] No such file or directory` | Check the spelling and `os.getcwd()`, or use the full path |
| Opening with `"w"` when you meant to add | The old content is **erased** | Use `"a"` |
| Forgetting `\n` in `write()` | Everything on one line | `f.write(text + "\n")` |
| `f.write(42)` | `TypeError: write() argument must be str, not int` | `f.write(str(42))` |
| Not closing the file | Data may not be saved | Use `with open(...)` |
| Using the file after the `with` block | `ValueError: I/O operation on closed file.` | Work inside the block |
| Reading twice | The second `read()` is empty | `f.seek(0)` |
| `print(line)` while looping | Blank lines between lines | `print(line.strip())` |
| No `encoding` | Different results on different computers | `encoding="utf-8"` |
| Windows path `"C:\new\file.txt"` | `\n` becomes a newline character | `r"C:\new\file.txt"` or `/` |
| `"x"` on an existing file | `FileExistsError` | Another name, or `"w"` / `"a"` |
| Writing to a folder that does not exist | `FileNotFoundError` | `os.makedirs(folder, exist_ok=True)` |
| CSV without `newline=""` | Blank rows on Windows | `open(..., newline="")` |
| `line.split(",")` for CSV | Breaks on quoted commas | `csv.reader` |
| CSV numbers used as numbers | They are strings | `int(...)` / `float(...)` |
| `json.load(text)` / `json.dump(data)` | `AttributeError` / `TypeError` | `loads` for strings, `dump(data, f)` needs a file |
| Saving a set or date in JSON | `TypeError: ... is not JSON serializable` | Convert it, or use `default=str` |
| JSON with single quotes or a trailing comma | `JSONDecodeError` | Double quotes, no trailing comma |
| Integer keys / tuples after JSON | Come back as strings / lists | Convert back |
| Opening a JSON file with `"w"` before loading | The data is erased | Load first, then write |

---

---

- [Back to section index](./README.md)
