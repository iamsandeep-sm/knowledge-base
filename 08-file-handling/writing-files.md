# Writing Files - Revision Notes ~ sm

> **In one line:** To write to a file, open it in a **write mode** — `"w"` (replace the content), `"a"` (add to the end) or `"x"` (create only if new) — and use `.write()` or `.writelines()`. Always use `with` so the file is closed and saved.

## Table of Contents
- [File Modes](#file-modes)
- [Write with "w"](#write-with-w)
- [The "w" Mode Erases the File](#the-w-mode-erases-the-file)
- [Append with "a"](#append-with-a)
- [Create Only if New with "x"](#create-only-if-new-with-x)
- [Writing Lines: Remember the Newline](#writing-lines-remember-the-newline)
- [writelines()](#writelines)
- [Writing Non-Text Values](#writing-non-text-values)
- [print() into a File](#print-into-a-file)
- [Read and Write: "r+" Mode](#read-and-write-r-mode)
- [Is the Data Saved Immediately?](#is-the-data-saved-immediately)
- [Binary Files](#binary-files)
- [Useful File and Folder Operations](#useful-file-and-folder-operations)
- [Small Programs](#small-programs)
- [Common Mistakes](#common-mistakes)

---

## File Modes

The second argument of `open()` is the **mode**.

| Mode | Name | If the file exists | If it does not exist | Position |
|---|---|---|---|---|
| `"r"` | Read (default) | Opened | `FileNotFoundError` | Start |
| `"w"` | Write | **Erased (emptied)** | Created | Start |
| `"a"` | Append | Kept | Created | **End** |
| `"x"` | Exclusive create | `FileExistsError` | Created | Start |
| `"r+"` | Read and write | Kept | `FileNotFoundError` | Start |

Letters that can be added:

| Letter | Meaning |
|---|---|
| `t` | Text mode (the default, so `"r"` means `"rt"`) |
| `b` | **Binary** mode (bytes), for images and other non-text files |
| `+` | Also allow the other operation (reading **and** writing) |

---

## Write with "w"

```python
with open("out.txt", "w", encoding="utf-8") as f:
    f.write("Hello\n")
    f.write("World\n")

with open("out.txt", encoding="utf-8") as f:
    print(f.read())
# Output: Hello
# Output: World
# Output:
```
(The last empty line is there because the text ends with `\n` and `print` adds another.) `write()` returns the **number of characters** written, and the file is created if it does not exist.

```python
with open("out.txt", "w", encoding="utf-8") as f:
    count = f.write("abc")
print(count)
# Output: 3
```

---

## The "w" Mode Erases the File

Opening an existing file with `"w"` **empties it immediately**, even before you write anything.

```python
with open("data.txt", "w", encoding="utf-8") as f:
    f.write("first version")

with open("data.txt", "w", encoding="utf-8") as f:
    f.write("second version")

with open("data.txt", encoding="utf-8") as f:
    print(f.read())
# Output: second version
```
Use `"a"` to **add** instead, and use `"x"` if you want to be protected from overwriting.

---

## Append with "a"

`"a"` keeps the old content and writes at the **end**.

```python
with open("log.txt", "w", encoding="utf-8") as f:
    f.write("line 1\n")

with open("log.txt", "a", encoding="utf-8") as f:
    f.write("line 2\n")
    f.write("line 3\n")

with open("log.txt", encoding="utf-8") as f:
    print(f.read().splitlines())
# Output: ['line 1', 'line 2', 'line 3']
```
Appending is the usual way to build a **log file**.

---

## Create Only if New with "x"

`"x"` creates the file but refuses to touch an existing one.

```python
try:
    with open("log.txt", "x", encoding="utf-8") as f:
        f.write("new")
except FileExistsError:
    print("File already exists, not overwritten")
# Output: File already exists, not overwritten
```

---

## Writing Lines: Remember the Newline

`write()` writes **exactly** what you give it. It does **not** add a newline.

```python
with open("names.txt", "w", encoding="utf-8") as f:
    f.write("Riya")
    f.write("Amit")

with open("names.txt", encoding="utf-8") as f:
    print(f.read())
# Output: RiyaAmit
```
Add `\n` yourself:
```python
names = ["Riya", "Amit", "Sam"]

with open("names.txt", "w", encoding="utf-8") as f:
    for name in names:
        f.write(name + "\n")

with open("names.txt", encoding="utf-8") as f:
    print(f.read().splitlines())
# Output: ['Riya', 'Amit', 'Sam']
```
In text mode, `\n` is converted to the correct line ending for your operating system, so always write `\n`, not `\r\n`.

---

## writelines()

`writelines(iterable)` writes every string of a list or other iterable. It does **not** add newlines either.

```python
lines = ["one\n", "two\n", "three\n"]

with open("lines.txt", "w", encoding="utf-8") as f:
    f.writelines(lines)

with open("lines.txt", encoding="utf-8") as f:
    print(f.read().splitlines())
# Output: ['one', 'two', 'three']
```
When your list has no newlines, add them with a comprehension:
```python
words = ["one", "two", "three"]
with open("lines.txt", "w", encoding="utf-8") as f:
    f.writelines(w + "\n" for w in words)
```

---

## Writing Non-Text Values

`write()` accepts **strings only**.

```python
with open("n.txt", "w", encoding="utf-8") as f:
    f.write(42)
# Error:  TypeError: write() argument must be str, not int
```
Convert first:
```python
with open("n.txt", "w", encoding="utf-8") as f:
    f.write(str(42) + "\n")
    f.write(f"Total: {3 * 14}\n")
```
For lists, dictionaries and other structures, use JSON (see the JSON Files note).

---

## print() into a File

`print()` can write to a file with `file=`, and it adds the newline for you.

```python
with open("report.txt", "w", encoding="utf-8") as f:
    print("Report", file=f)
    print("Total:", 100, file=f)

with open("report.txt", encoding="utf-8") as f:
    print(f.read().splitlines())
# Output: ['Report', 'Total: 100']
```

---

## Read and Write: "r+" Mode

`"r+"` opens an existing file for **both** reading and writing, starting at the beginning. Writing **overwrites** the characters at the current position; it does not insert.

```python
with open("rp.txt", "w", encoding="utf-8") as f:
    f.write("Hello World")

with open("rp.txt", "r+", encoding="utf-8") as f:
    f.write("HELLO")
    f.seek(0)
    print(f.read())
# Output: HELLO World
```
To change the middle of a file safely, usually read all the content, modify the text in Python, and write it all back with `"w"`.

---

## Is the Data Saved Immediately?

Data you write is first kept in a **buffer** and is moved to the disk when:
- the file is **closed** (the `with` block ends), or
- the buffer fills up, or
- you call `f.flush()`.

If the program crashes **before** the file is closed, some data may be lost. This is another reason to use `with`.

---

## Binary Files

Add `b` to the mode to work with **bytes**, for files that are not text (images, PDFs, zip files). No `encoding` is allowed in binary mode.

```python
data = bytes([72, 105, 33])          # the bytes of "Hi!"

with open("raw.bin", "wb") as f:
    f.write(data)

with open("raw.bin", "rb") as f:
    content = f.read()

print(content)
print(len(content))
# Output: b'Hi!'
# Output: 3
```
Copy any file, such as an image, safely in binary mode:
```python
with open("raw.bin", "rb") as source, open("copy.bin", "wb") as target:
    target.write(source.read())
```

---

## Useful File and Folder Operations

The `os` module (and `pathlib`, see the Standard Library section) handles files themselves, not just their content.

| Task | Code |
|---|---|
| Does it exist? | `os.path.exists(path)` |
| Is it a file / folder? | `os.path.isfile(path)` / `os.path.isdir(path)` |
| Size in bytes | `os.path.getsize(path)` |
| Delete a file | `os.remove(path)` |
| Rename a file | `os.rename(old, new)` |
| Make a folder | `os.makedirs(path, exist_ok=True)` |
| List a folder | `os.listdir(folder)` |
| Delete an empty folder | `os.rmdir(folder)` |
| Join path parts | `os.path.join("data", "notes.txt")` |

```python
import os

os.makedirs("demo_folder", exist_ok=True)
path = os.path.join("demo_folder", "a.txt")

with open(path, "w", encoding="utf-8") as f:
    f.write("hello")

print(os.listdir("demo_folder"))
print(os.path.getsize(path))

os.remove(path)
os.rmdir("demo_folder")
print(os.path.exists("demo_folder"))
# Output: ['a.txt']
# Output: 5
# Output: False
```
Deleting is **permanent**: there is no recycle bin, so be careful.

---

## Small Programs

**Save a to-do list and read it back**
```python
tasks = ["buy milk", "study python", "call mom"]

with open("todo.txt", "w", encoding="utf-8") as f:
    for task in tasks:
        f.write(task + "\n")

with open("todo.txt", encoding="utf-8") as f:
    loaded = [line.strip() for line in f]

print(loaded)
# Output: ['buy milk', 'study python', 'call mom']
```

**Count lines and words in a file**
```python
with open("todo.txt", encoding="utf-8") as f:
    text = f.read()

print("lines:", len(text.splitlines()))
print("words:", len(text.split()))
# Output: lines: 3
# Output: words: 6
```

**Copy a text file in upper case**
```python
with open("todo.txt", encoding="utf-8") as src, open("todo_upper.txt", "w", encoding="utf-8") as dst:
    for line in src:
        dst.write(line.upper())

with open("todo_upper.txt", encoding="utf-8") as f:
    print(f.readline().strip())
# Output: BUY MILK
```

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Using `"w"` when you meant to add | The old content is **erased** | Use `"a"` |
| Forgetting `\n` | All text on one line | `f.write(text + "\n")` |
| `f.write(42)` | `TypeError: write() argument must be str, not int` | `f.write(str(42))` |
| Not closing the file | Data may not be saved | Use `with` |
| Writing after the `with` block | `ValueError: I/O operation on closed file.` | Write inside the block |
| `"b"` mode with `encoding=` | `ValueError: binary mode doesn't take an encoding argument` | Remove `encoding` |
| `"r"` mode and then `write()` | `io.UnsupportedOperation: not writable` | Open with `"w"`, `"a"` or `"r+"` |
| `"x"` on an existing file | `FileExistsError` | Use another name, or `"w"`/`"a"` |
| Writing to a folder that does not exist | `FileNotFoundError` | `os.makedirs(folder, exist_ok=True)` first |

---

- [Back to section index](./README.md)
