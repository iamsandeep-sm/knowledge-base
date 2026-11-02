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
- [Object-Oriented Programming](#object-oriented-programming)
- [Advanced Python](#advanced-python)
- [Useful Standard Library](#useful-standard-library)
- [Testing and Code Quality](#testing-and-code-quality)
- [Data Structures and Algorithms](#data-structures-and-algorithms)

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

## Object-Oriented Programming

| Mistake | What happens | Fix |
|---|---|---|
| Method without `self`: `def bark():` | `TypeError: Dog.bark() takes 0 positional arguments but 1 was given` | `def bark(self):` |
| Calling a method on the class: `Dog.bark()` | `TypeError: ... missing 1 required positional argument: 'self'` | Make an object first |
| `name = name` in `__init__` | Nothing is stored on the object | `self.name = name` |
| Misspelling `__init__` (`__int__`, `_init_`) | It never runs automatically | Two underscores on both sides |
| `tommy = Dog` (no brackets) | `tommy` is the class, not an object | `Dog()` |
| Passing arguments to a class without `__init__` | `TypeError: Dog() takes no arguments` | Add `__init__` |
| Returning a value from `__init__` | `TypeError: __init__() should return None, not 'int'` | Do not return |
| Mutable default (`items=[]`) or mutable class attribute | All objects share one list | `None` default / create in `__init__` |
| `self.count += 1` for a shared counter | Creates an instance attribute; class counter unchanged | `ClassName.count += 1` |
| `a.school = "X"` expecting all objects to change | Only `a` changes | `Student.school = "X"` |
| Child `__init__` without `super().__init__()` | Parent attributes are missing (`AttributeError`) | Call `super().__init__(...)` |
| Inheritance for "has a" | Wrong design | Composition (`self.engine = Engine()`) |
| `type(x) == Parent` | Rejects child objects | `isinstance(x, Parent)` |
| Setter stores in the property's own name | `RecursionError` | Store in `self._name` |
| Assigning to a read-only property | `AttributeError: property 'x' of 'C' object has no setter` | Add a setter |
| Thinking `__x` is truly private | `obj._Class__x` still works | It is a convention, not security |
| Not inheriting from `ABC` | `@abstractmethod` is not enforced | `class Shape(ABC):` |
| Creating an abstract class or an incomplete child | `TypeError: Can't instantiate abstract class ...` | Implement every abstract method |
| Defining a method twice (expecting overloading) | The second replaces the first | Default arguments or `*args` |
| `__str__` that prints instead of returning | `TypeError: __str__ returned non-string` | `return` a string |
| `__eq__` without `__hash__` | Object cannot go in a set or be a dict key | Also define `__hash__` |
| `3 + obj` fails | No `__radd__` | Add `__radd__` |
| `__iadd__` without `return self` | Variable becomes `None` | End with `return self` |

---

## Advanced Python

| Mistake | What happens | Fix |
|---|---|---|
| Looping over an iterator or generator twice | The second loop gets nothing | Create it again, or store the results in a list |
| Calling `next()` with no items left | `StopIteration` | `next(it, default)` |
| `__iter__` not returning an iterator | `TypeError: iter() returned non-iterator` | Return `self` (with `__next__`) or `iter(...)` |
| `list()` on an endless generator | The program never finishes | `itertools.islice(gen, n)` |
| `send(value)` before the first `next()` | `TypeError` | Call `next(gen)` first |
| Forgetting `nonlocal` when changing an outer variable | `UnboundLocalError` | `nonlocal count` |
| Functions made in a loop all return the last value | Late binding | Default argument `i=i`, or a factory function |
| Decorator wrapper does not `return` the result | The function returns `None` | `return func(*args, **kwargs)` |
| Forgetting `@wraps(func)` | `__name__` and `__doc__` are lost | Add `@wraps(func)` |
| `@repeat` without brackets when it takes an argument | The function becomes the argument | `@repeat(3)` |
| `__enter__` without `return` | `as name` is `None` | `return self` |
| `__exit__` returns `True` by accident | Every exception is hidden | Return `False` or nothing |
| `@contextmanager` without `try` / `finally` around `yield` | Clean-up is skipped on error | Wrap `yield` in `try` / `finally` |
| Expecting type hints to reject wrong types | Python ignores them at run time | Use mypy or pyright, or `isinstance` checks |
| `int \| None` on Python 3.9 | `TypeError` | Python 3.10+, or `Optional[int]` |

---

## Useful Standard Library

| Mistake | What happens | Fix |
|---|---|---|
| Naming your file `math.py`, `random.py`, `datetime.py`, `collections.py` or `itertools.py` | Your file hides the real module | Rename your file |
| `math.sqrt(-4)` | `ValueError: math domain error` | Check the input, or use `cmath` |
| `math.sin(90)` expecting degrees | Wrong answer (radians) | `math.sin(math.radians(90))` |
| Comparing floats with `==` | `0.1 + 0.2 == 0.3` is `False` | `math.isclose(a, b)` or `Decimal` |
| `random.randrange(1, 6)` expecting 6 | Stop is not included | `random.randint(1, 6)` |
| `x = random.shuffle(items)` | `x` is `None`; the list is shuffled in place | `random.shuffle(items)` |
| Using `random` for passwords or tokens | Predictable | Use `secrets` |
| `import datetime` then `datetime(2026, 1, 1)` | `TypeError: 'module' object is not callable` | `from datetime import datetime` |
| `diff.seconds` for the full duration | Only the part after whole days | `diff.total_seconds()` |
| `timedelta(months=1)` | `TypeError` | Use `days`, or `dateutil` |
| Mixing naive and aware datetimes | `TypeError` | Make both aware (use UTC) |
| `datetime.utcnow()` | Deprecated since Python 3.12 | `datetime.now(timezone.utc)` |
| `"folder" / "file"` with plain strings | `TypeError` | `Path("folder") / "file"` |
| `Path("a") / "/b"` | Result is `/b` (absolute part wins) | Do not start the second part with `/` |
| `mkdir()` without `parents=True` / `exist_ok=True` | `FileNotFoundError` / `FileExistsError` | Add the options |
| Reading a missing `defaultdict` key to test it | The key is created | `key in d` or `d.get(key)` |
| `defaultdict(list())` | `TypeError` | `defaultdict(list)` (the function, no brackets) |
| `list.pop(0)` for a queue | Slow for big lists | `collections.deque` and `popleft()` |
| `groupby` without sorting first | The same key shows up in several groups | Sort by the same key first |
| `list(itertools.count())` | Never ends | Limit it with `islice` |
| Reusing an iterator | Second time it is empty | Make a list or create it again |

---

## Testing and Code Quality

| Mistake | What happens | Fix |
|---|---|---|
| Validating user input with `assert` | Checks vanish under `python -O` | `if ...: raise ValueError(...)` |
| `assert (condition, "message")` | A tuple is always true: it never fails | `assert condition, "message"` |
| Putting required work inside `assert` | Skipped under `-O` | Do the work first, then assert on the result |
| Test file or method not starting with `test` | The test is never run | `test_*.py`, `def test_...` |
| Test class not inheriting `unittest.TestCase` | `unittest` ignores it | `class TestX(unittest.TestCase):` |
| `assertEqual` on floats | Fails from rounding | `assertAlmostEqual`, `pytest.approx` |
| Tests depending on each other | Pass alone, fail together | `setUp` / fixtures for a fresh start |
| Tests using the real clock, network or randomness | Slow or unpredictable | `Mock` and `patch` |
| Patching the wrong name | The real function still runs | Patch where the name is **used** |
| Only testing the happy path | Edge-case bugs slip through | Test empty, zero, one item, negative, errors |
| Reading a traceback from the top | You miss the error | Start at the **last line** |
| Fixing the line that crashed, not the cause | The bug moves | Find where the bad value came from |
| Leaving `print` or `breakpoint()` in finished code | Noise, or the program stops | Remove them, or use `logging` |
| `except: pass` | Hides the bug | Catch specific errors and log them |
| `logging.basicConfig()` has no effect the second time | It is ignored once configured | `force=True` |
| `import *`, one-letter names, `l`, `O`, `I` | Unreadable code | Import what you need; descriptive names |
| A function named `CamelCase` or a class named `snake_case` | Misleads readers | Functions `snake_case`, classes `CapWords` |
| Comments repeating the code | Noise that goes out of date | Explain **why**, or rename things |
| Deep nesting | Hard to read | Guard clauses and small functions |
| Formatting by hand | Time lost | `black` or `ruff format` |

## Data Structures and Algorithms

| Mistake | What happens | Fix |
|---|---|---|
| `x in some_list` inside a loop on big data | `O(n²)` overall | Convert the list to a `set` once |
| `list.pop(0)` / `insert(0, x)` as a queue | `O(n)` each | `collections.deque` |
| `result += text` in a long loop | Slow repeated copying | Collect pieces and `"".join(...)` |
| Binary search on unsorted data | Wrong answers | Sort first, or use linear search |
| `(low + high) // 2` loops that never move | Infinite loop | `low = mid + 1` / `high = mid - 1` |
| Wrong loop condition (`<` vs `<=`) | Off-by-one, missed last item | Test with 0, 1 and 2 items |
| `[[0] * 3] * 3` for a matrix | All rows are the same list | `[[0] * 3 for _ in range(3)]` |
| Changing a list while looping over it | Items skipped | Loop over a copy, or build a new list |
| Using `.sort()` result | It returns `None` | `sorted(x)` or `x.sort()` alone |
| Expecting `heapq` to print sorted | It is only partly ordered | `heappop` repeatedly, or `sorted()` |
| `heapq` for the largest item | It is a min-heap | Push negated values |
| Unhashable key (`list`, `dict`, `set`) | `TypeError: unhashable type` | Use a `tuple` or `frozenset` |
| Defining `__eq__` without `__hash__` | Object cannot be a key | Define both, based on the same fields |
| Mutating an object used as a key | Lookups fail | Use immutable keys |
| `dict[key]` for a missing key | `KeyError` | `.get()`, `defaultdict`, `Counter` |
| Popping from an empty stack or queue | `IndexError` | Check `if stack:` first |
| Marking visited when dequeuing (BFS) | Vertices queued many times | Mark when you **enqueue** |
| No visited set in graph search | Infinite loop on cycles | Always track visited nodes |
| One edge direction only for an undirected graph | Missing connections | Add both directions |
| Grid neighbours without bounds checks | `IndexError`, or negative indexes wrap around | `0 <= r < rows and 0 <= c < cols` |
| Using BFS for weighted shortest paths | Wrong answer | Dijkstra (non-negative weights) |
| `node.next.value` on the last node | `AttributeError` on `None` | Check `if node.next:` |
| Overwriting `current.next` before saving it | Rest of the list is lost | `next_node = current.next` first |
| Forgetting to update `head` or `tail` | Changes disappear | Return the new head, update `tail` |
| Comparing nodes with `==` for cycle detection | Compares values, not identity | `slow is fast` |
| Recursive function with no base case | `RecursionError` | Decide the smallest input first |
| Appending the working list in backtracking | Every result ends up the same or empty | `results.append(path[:])` |
| Forgetting the undo step (`pop`) | Wrong or missing results | Pair every `append` with a `pop` |
| Plain recursive `fib(40)` | Takes forever | `@cache` or a loop |
| `memo={}` as a default argument | The dictionary is shared across calls | `memo=None`, create it inside |
| BST checked only with `left < node < right` | Deep violations are missed | Pass down `(low, high)` limits |
| Inserting sorted data into a plain BST | A chain, `O(n)` operations | Shuffle, or use a balanced structure |

---

---

- [Back to section index](./README.md)
