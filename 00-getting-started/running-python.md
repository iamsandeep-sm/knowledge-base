# Running Python - Revision Notes ~ sm

> **In one line:** You can run Python in two ways: **interactive mode** (type one line at a time in the `>>>` prompt and see the result at once) or by running a **script** (a `.py` file) with `python filename.py`.

## Table of Contents
- [Two Ways to Run Python](#two-ways-to-run-python)
- [Using a Terminal](#using-a-terminal)
- [Interactive Mode (the Python Shell)](#interactive-mode-the-python-shell)
- [Running a Script File](#running-a-script-file)
- [Useful Command-Line Options](#useful-command-line-options)
- [Stopping a Program](#stopping-a-program)
- [Reading an Error Message (Traceback)](#reading-an-error-message-traceback)
- [Running Python from an Editor](#running-python-from-an-editor)
- [Running a Script on macOS and Linux Directly](#running-a-script-on-macos-and-linux-directly)

---

## Two Ways to Run Python

| | Interactive mode | Script file |
|---|---|---|
| How to start | Type `python` (Windows) or `python3` (macOS / Linux) | `python hello.py` |
| You type | One line at a time | A whole program saved in a file |
| Result | Shown immediately | Shown when the whole file has run |
| Code is saved? | **No**, it is gone when you exit | **Yes**, in the `.py` file |
| Best for | Trying a small idea, testing a function, using Python as a calculator | Real programs you want to keep and run again |

> In the examples below, `python` means the command that works on your computer. On macOS and Linux use `python3` (see the Python Installation note).

---

## Using a Terminal

Both ways start from a **terminal** (also called command prompt or shell): a text window where you type commands.

| System | How to open it |
|---|---|
| Windows | Open **Command Prompt** or **PowerShell** from the Start menu |
| macOS | Open **Terminal** (in Applications, Utilities) |
| Linux | Open the **Terminal** app of your desktop |

Two commands you need to move around folders:

| Command | What it does | Example |
|---|---|---|
| `cd folder` | Go into a folder | `cd python-notes` |
| `cd ..` | Go up one folder | `cd ..` |

> A script runs from the folder where you typed the command, so first `cd` into the folder that contains your `.py` file.

---

## Interactive Mode (the Python Shell)

### Start it
Type `python` and press Enter. Python shows its version and the **`>>>` prompt**, which means "I am ready for a line of code".
```
>>> 2 + 3
5
>>> name = "Py"
>>> name
'Py'
>>> print("Hello")
Hello
```

- An **expression** (like `2 + 3` or `name`) shows its result automatically. In a script you would need `print()`.
- Text results appear with quotes (`'Py'`), which shows the value. `print()` shows it without quotes.
- `_` holds the **last result**:
```
>>> 10 * 2
20
>>> _ + 1
21
```

### Multi-line code
Lines that end with a colon (`for`, `if`, `def` ...) change the prompt to `...`. Indent the body, then press Enter on an empty line to finish.
```
>>> for i in range(2):
...     print(i)
...
0
1
```

### Get help
```
>>> help(print)       # documentation of print
>>> dir()             # names defined right now
```
Press `q` to leave the help screen.

### Exit
| System | Way to exit |
|---|---|
| Any | `exit()` or `quit()` |
| macOS / Linux | `Ctrl + D` |
| Windows | `Ctrl + Z`, then Enter |

> Always write the brackets in `exit()`. It works in every version. (Newer versions also accept plain `exit` in a terminal, but older ones only print a reminder.)

> The shell forgets everything when you exit. To keep your code, write it in a file.

---

## Running a Script File

### Steps
1. Write your code in an editor and save it with the **`.py`** extension, for example `hello.py`:
```python
print("Hello, World!")
```
2. Open a terminal and `cd` into the folder that holds the file.
3. Run it:
```
python hello.py
```
```
Hello, World!
```
(On Windows you can also write `py hello.py`. On macOS and Linux use `python3 hello.py`.)

### What the terminal needs
- Python runs the file from the **first line to the last**, one statement at a time, then stops.
- The file name is written **after** the command, with its `.py` extension.
- If the file is in another folder, give the path: `python notes/hello.py`.

### Do not mix up the two modes
Typing `python hello.py` **inside** the `>>>` prompt is an error, because that prompt expects Python code, not a terminal command:
```
>>> python hello.py
  File "<stdin>", line 1
    python hello.py
           ^^^^^
SyntaxError: invalid syntax
```
Exit the shell first (`exit()`), then type the command at the normal terminal prompt.

### A script prints only what you `print()`
```python
2 + 3
print(2 + 3)
# Output: 5
```
The first line calculates 5 but shows nothing. Only `print()` displays it.

### Imported modules are cached
When your program imports another `.py` file (a module), Python saves a compiled copy in a folder named `__pycache__` so that the next run is faster. You can ignore this folder and it can be deleted safely.

---

## Useful Command-Line Options

The text after `python` is called an **option** (or flag). These are the ones worth knowing:

| Command | What it does |
|---|---|
| `python --version` or `python -V` | Prints the Python version and exits |
| `python -VV` | Prints the version with build details |
| `python -c "code"` | Runs the code inside the quotes and exits |
| `python -m module` | Runs a module that is installed, for example `python -m pip` |
| `python -i script.py` | Runs the script, then stays in the `>>>` prompt so you can inspect its variables |

```
python -c "print(2 + 3)"
```
```
5
```
```
python -m venv .venv
python -m pip --version
```
`python -m pip` and `python -m venv` are the recommended way to use pip and virtual environments, because they use the same Python you just typed.

Using `-i`:
```python
# i.py
x = 42
print("script done")
```
```
python -i i.py
```
```
script done
>>> x * 2
84
```

> `python -m` takes the module **name** (`venv`), not a file name (`venv.py`).

---

## Stopping a Program

| Situation | What to do |
|---|---|
| A program is stuck or running forever (for example, an infinite loop) | Press **`Ctrl + C`** in the terminal. Python stops and shows `KeyboardInterrupt` |
| You are in the `>>>` prompt and want to cancel the line you are typing | Press `Ctrl + C` |
| You want to leave the shell | `exit()` (see above) |

---

## Reading an Error Message (Traceback)

When something goes wrong, Python stops and prints a **traceback**. Read it **from the bottom up**.
```python
# tb.py
def a():
    return 1 / 0

print("start")
a()
```
Output (simplified):
```
start
Traceback (most recent call last):
  File "tb.py", line 5, in <module>
    a()
  File "tb.py", line 2, in a
    return 1 / 0
ZeroDivisionError: division by zero
```

| Part | Meaning |
|---|---|
| `start` | Output printed **before** the error still appears |
| `Traceback (most recent call last)` | The list of calls that led to the error, oldest first |
| `File "tb.py", line 5, in <module>` | Where the call happened (`<module>` means the main part of the file) |
| `File "tb.py", line 2, in a` | The line **inside function `a`** where it failed |
| **Last line** | The **error type** and a short message. Start reading here |

> The exact wording and the small `^` marker lines change a little between Python versions, but the structure is always the same.

If the file name is wrong or you are in the wrong folder, Python cannot even start your program:
```
python nofile.py
```
```
python: can't open file '/home/user/nofile.py': [Errno 2] No such file or directory
```

---

## Running Python from an Editor

You do not have to type the command every time. Editors run the file for you:

| Tool | How |
|---|---|
| **IDLE** | Open the file, then use **Run > Run Module** (shortcut **F5**) |
| **VS Code** | With the Python extension installed, use the **Run** (play) button at the top right of the file |
| **PyCharm** | Right-click the file and choose **Run** |

The output appears in a panel inside the editor. This does the same thing as `python filename.py` in a terminal.

---

## Running a Script on macOS and Linux Directly

You can make a script runnable like a program. Put a **shebang line** as the **first line**, then make the file executable:
```python
#!/usr/bin/env python3
print("hi from shebang")
```
```
chmod +x hello.py
./hello.py
```
```
hi from shebang
```
- `#!/usr/bin/env python3` tells the system to find `python3` on your `PATH` and use it. The official docs recommend this form on Unix.
- To Python itself the shebang is just a comment (it starts with `#`).
- This is optional. `python3 hello.py` always works.

---

- [Back to section index](./README.md)
