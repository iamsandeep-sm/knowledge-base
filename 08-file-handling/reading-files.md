# Reading Files - Revision Notes ~ sm

> **In one line:** To read a file, **open** it with `open(path, "r", encoding="utf-8")`, read its content with `.read()`, `.readline()`, `.readlines()` or a `for` loop, and **close** it (the `with` statement does this for you).

## Table of Contents
- [Why Files?](#why-files)
- [A File to Practise With](#a-file-to-practise-with)
- [open() and the File Object](#open-and-the-file-object)
- [Reading Everything: read()](#reading-everything-read)
- [Reading Line by Line: for Loop](#reading-line-by-line-for-loop)
- [readline() and readlines()](#readline-and-readlines)
- [Reading a Part: read(n)](#reading-a-part-readn)
- [The Newline Character](#the-newline-character)
- [The File Position: tell() and seek()](#the-file-position-tell-and-seek)
- [File Paths](#file-paths)
- [Encoding](#encoding)
- [Checking a File Exists](#checking-a-file-exists)
- [Common Errors](#common-errors)
- [Which Method to Use](#which-method-to-use)

---

## Why Files?

Variables vanish when the program ends. A **file** stores data on the disk so it stays after the program stops, and can be read again later or shared with other programs.

| Kind of file | Examples |
|---|---|
| **Text file** | `.txt`, `.py`, `.csv`, `.json`, `.md` (readable characters) |
| **Binary file** | images, PDFs, `.zip`, `.exe` (raw bytes) |

This note is about **text files**. Writing is in the next note, `with` in the Context Managers note.

---

## A File to Practise With

The examples below read a file named `notes.txt` in the **same folder** as the script. Create it first:

```python
with open("notes.txt", "w", encoding="utf-8") as f:
    f.write("Python is fun\nFiles store data\nLast line")
```
The file now contains three lines:
```
Python is fun
Files store data
Last line
```

---

## open() and the File Object

```python
open(file, mode="r", encoding=None)
```

| Argument | Meaning |
|---|---|
| `file` | The path of the file, as a string |
| `mode` | What you want to do. `"r"` (read) is the default |
| `encoding` | How characters are stored, almost always `"utf-8"` |

`open()` returns a **file object**, your handle for reading.

```python
f = open("notes.txt", "r", encoding="utf-8")
print(type(f))
print(f.name)
print(f.closed)
f.close()
print(f.closed)
# Output: <class '_io.TextIOWrapper'>
# Output: notes.txt
# Output: False
# Output: True
```
Always **close** a file when you finish: it frees the file for other programs and makes sure data is written. The safest way is the `with` statement:

```python
with open("notes.txt", "r", encoding="utf-8") as f:
    content = f.read()
# the file is closed automatically here
print(f.closed)
# Output: True
```
The rest of this note uses `with`.

---

## Reading Everything: read()

`read()` returns the **whole file as one string**.

```python
with open("notes.txt", encoding="utf-8") as f:
    text = f.read()

print(text)
print(len(text))
# Output: Python is fun
# Output: Files store data
# Output: Last line
# Output: 40
```
(The `"r"` mode can be left out because it is the default.) The result includes the `\n` characters that end each line. Good for small files. A huge file would use a lot of memory.

---

## Reading Line by Line: for Loop

A file object is **iterable**: looping over it gives one line at a time. This is the best way for most files, because only one line is in memory at once.

```python
with open("notes.txt", encoding="utf-8") as f:
    for line in f:
        print(line.strip())
# Output: Python is fun
# Output: Files store data
# Output: Last line
```
`line.strip()` removes the `\n` at the end (otherwise `print` would add a second newline and show blank lines).

With line numbers:
```python
with open("notes.txt", encoding="utf-8") as f:
    for number, line in enumerate(f, start=1):
        print(number, line.rstrip("\n"))
# Output: 1 Python is fun
# Output: 2 Files store data
# Output: 3 Last line
```

---

## readline() and readlines()

| Method | Returns |
|---|---|
| `f.readline()` | **One** line (including its `\n`); an empty string `""` at the end of the file |
| `f.readlines()` | A **list** of all lines (each with its `\n`) |
| `list(f)` | Same as `readlines()` |

```python
with open("notes.txt", encoding="utf-8") as f:
    first = f.readline()
    second = f.readline()
    print(repr(first))
    print(repr(second))
# Output: 'Python is fun\n'
# Output: 'Files store data\n'
```
```python
with open("notes.txt", encoding="utf-8") as f:
    lines = f.readlines()

print(lines)
print(len(lines))
# Output: ['Python is fun\n', 'Files store data\n', 'Last line']
# Output: 3
```
A list of lines **without** newlines:
```python
with open("notes.txt", encoding="utf-8") as f:
    clean = f.read().splitlines()

print(clean)
# Output: ['Python is fun', 'Files store data', 'Last line']
```

---

## Reading a Part: read(n)

`read(n)` reads at most `n` **characters** and remembers where it stopped, so the next call continues from there.

```python
with open("notes.txt", encoding="utf-8") as f:
    print(f.read(6))
    print(f.read(3))
# Output: Python
# Output:  is
```
(The second result starts with a space.) Reading in chunks is how very large files are processed.

---

## The Newline Character

Lines in a file are separated by a **newline** (`\n` in Python). When reading in text mode, Python converts Windows `\r\n` and old Mac `\r` endings to `\n` for you.

| Method | Newline kept? |
|---|---|
| `read()`, `readline()`, `readlines()`, `for line in f` | Yes |
| `.splitlines()` on the text | No |
| `line.strip()` / `line.rstrip("\n")` | Removed |

---

## The File Position: tell() and seek()

A file object has a **position** (a cursor). Reading moves it forward, and when it reaches the end, further reads return nothing.

```python
with open("notes.txt", encoding="utf-8") as f:
    print(f.read(6))
    print(f.tell())
    f.seek(0)             # go back to the start
    print(f.read(6))
    rest = f.read()
    print(f.read() == "")
# Output: Python
# Output: 6
# Output: Python
# Output: True
```
- `f.tell()` gives the current position.
- `f.seek(0)` moves back to the start so you can read again.
- Reading a file **twice** without `seek(0)` gives an empty result the second time.

---

## File Paths

| Path type | Example | Meaning |
|---|---|---|
| **Relative** | `"notes.txt"`, `"data/notes.txt"` | Relative to the folder you **run Python from** (the current working directory) |
| **Absolute** | `"/home/riya/notes.txt"`, `"C:\\Users\\Riya\\notes.txt"` | The full address from the top |

```python
import os
print(os.path.basename(os.getcwd()) != "")
# Output: True
```
`os.getcwd()` shows the current working directory. A very common problem: the file exists, but Python is **not running in that folder**, so `FileNotFoundError` appears.

**Windows paths:** the backslash `\` starts an escape in normal strings (`"C:\new"` contains a newline!). Use one of:
```python
path1 = r"C:\Users\Riya\notes.txt"       # raw string
path2 = "C:\\Users\\Riya\\notes.txt"     # doubled backslashes
path3 = "C:/Users/Riya/notes.txt"        # forward slashes also work on Windows
```
The `pathlib` module (Standard Library section) is a modern way to build paths that works on every system.

---

## Encoding

A text file stores **bytes**; the **encoding** says how bytes map to characters. The common one is **UTF-8**.

```python
with open("hindi.txt", "w", encoding="utf-8") as f:
    f.write("नमस्ते Python")

with open("hindi.txt", encoding="utf-8") as f:
    print(f.read())
# Output: नमस्ते Python
```
- If you leave out `encoding`, older Python versions use the **system's** default (on Windows often `cp1252`), so the same code can behave differently on different computers. Python 3.15 makes UTF-8 the default.
- To stay safe on every version, **always write `encoding="utf-8"`**.
- Reading a file with the wrong encoding gives `UnicodeDecodeError` or strange characters.

---

## Checking a File Exists

```python
import os

print(os.path.exists("notes.txt"))
print(os.path.isfile("notes.txt"))
print(os.path.exists("missing.txt"))
# Output: True
# Output: True
# Output: False
```
Or simply try to open it and handle `FileNotFoundError` (see the Exception Handling section):

```python
try:
    with open("missing.txt", encoding="utf-8") as f:
        print(f.read())
except FileNotFoundError:
    print("File not found")
# Output: File not found
```

---

## Common Errors

| Error | Cause | Fix |
|---|---|---|
| `FileNotFoundError: [Errno 2] No such file or directory: 'x.txt'` | Wrong name, or wrong folder | Check the spelling and `os.getcwd()`, or use the full path |
| `PermissionError` | You may not read that file, or the path is a folder | Check permissions and the path |
| `IsADirectoryError` | The path is a folder | Give the file's path |
| `UnicodeDecodeError` | Wrong encoding | `encoding="utf-8"` (or the file's real encoding) |
| `ValueError: I/O operation on closed file.` | Using the file after the `with` block | Read inside the block |
| Empty result on a second `read()` | The position is at the end | `f.seek(0)` first |
| Blank lines in the output | `print(line)` adds a second newline | `print(line.strip())` or `print(line, end="")` |
| `io.UnsupportedOperation: not writable` | Tried to write to a file opened for reading | Open with `"w"` or `"a"` |

```python
with open("notes.txt", encoding="utf-8") as f:
    pass
f.read()
# Error:  ValueError: I/O operation on closed file.
```

---

## Which Method to Use

| Need | Use |
|---|---|
| The whole content as text (small file) | `f.read()` |
| Process each line (any size) | `for line in f:` |
| All lines as a list | `f.readlines()` or `f.read().splitlines()` |
| Only the first line | `f.readline()` |
| Huge file in pieces | `f.read(size)` in a loop |

---

- [Back to section index](./README.md)
