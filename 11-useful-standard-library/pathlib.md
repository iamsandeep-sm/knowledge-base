# pathlib - Revision Notes ~ sm

> **In one line:** **`pathlib`** treats file and folder paths as **objects** instead of plain strings. You build paths with the `/` operator, ask them questions (`.exists()`, `.suffix`), and read, write, list and delete files with short methods, in a way that works on Windows, macOS and Linux alike.

## Table of Contents
- [Why pathlib?](#why-pathlib)
- [Creating Paths](#creating-paths)
- [Joining Paths with /](#joining-paths-with-)
- [Parts of a Path](#parts-of-a-path)
- [Changing Parts](#changing-parts)
- [Absolute, Relative and Resolve](#absolute-relative-and-resolve)
- [Checking a Path](#checking-a-path)
- [Creating Folders](#creating-folders)
- [Reading and Writing Files](#reading-and-writing-files)
- [Listing a Folder](#listing-a-folder)
- [Finding Files with glob](#finding-files-with-glob)
- [Renaming, Moving and Deleting](#renaming-moving-and-deleting)
- [File Information](#file-information)
- [Copying and Removing Trees: shutil](#copying-and-removing-trees-shutil)
- [pathlib vs os.path](#pathlib-vs-ospath)
- [Practical Examples](#practical-examples)
- [Common Mistakes](#common-mistakes)

The examples create files and folders in the **current folder**. Output is shown for Linux and macOS; on Windows the separator is a backslash `\`.

---

## Why pathlib?

```python
# The old way: strings
import os
path = os.path.join("notes", "day1.txt")

# The pathlib way: objects
from pathlib import Path
path = Path("notes") / "day1.txt"
print(path)
# Output: notes/day1.txt
```
| Problem with plain strings | pathlib's answer |
|---|---|
| `"folder" + "/" + "file"` breaks on Windows | `Path("folder") / "file"` uses the right separator |
| Many separate functions: `os.path.exists`, `os.path.splitext`, `os.mkdir`, `open` | One object with methods |
| Easy to forget a separator or add a double one | `/` handles it |

`pathlib` is part of the standard library (Python 3.4+). The file handling notes use plain strings for simplicity; real programs often prefer `pathlib`.

---

## Creating Paths

```python
from pathlib import Path

p1 = Path("notes/day1.txt")
p2 = Path("/usr/local/bin")
p3 = Path(".")
print(p1, p2, p3)
print(type(p1).__name__)
# Output: notes/day1.txt /usr/local/bin .
# Output: PosixPath
```
On Windows `Path` becomes `WindowsPath`. Making a `Path` object **does not touch the disk**: the file does not need to exist.

Useful starting points:
```python
from pathlib import Path

print(Path.cwd() == Path(".").resolve())
print(Path.home().is_absolute())
# Output: True
# Output: True
```
`Path.cwd()` is the folder the program runs in. `Path.home()` is the user's home folder. To get the folder of the **current script**: `Path(__file__).parent` (in a `.py` file, not in the shell).

---

## Joining Paths with /

```python
from pathlib import Path

base = Path("projects")
full = base / "python" / "app.py"
print(full)
print(base / Path("data") / "file.csv")
print(Path("a").joinpath("b", "c"))
# Output: projects/python/app.py
# Output: projects/data/file.csv
# Output: a/b/c
```
The `/` works because `Path` redefines the division operator. At least one side must be a `Path` (a string alone, `"a" / "b"`, is a `TypeError`).

**Watch out:** joining an **absolute** path **replaces** everything before it.
```python
from pathlib import Path

print(Path("projects") / "/etc")
# Output: /etc
```
Never join user-supplied text onto a base folder without checking it, for example `"../../secret"`.

---

## Parts of a Path

```python
from pathlib import Path

p = Path("/home/riya/reports/summary.final.txt")

print(p.name)
print(p.stem)
print(p.suffix)
print(p.parent)
print(p.parts)
print(p.anchor)
# Output: summary.final.txt
# Output: summary.final
# Output: .txt
# Output: /home/riya/reports
# Output: ('/', 'home', 'riya', 'reports', 'summary.final.txt')
# Output: /
```
| Attribute | Meaning |
|---|---|
| `name` | Final part, with extension |
| `stem` | Name **without** the last extension |
| `suffix` | Last extension, including the dot (`''` if none) |
| `suffixes` | List of all extensions |
| `parent` | The folder containing it |
| `parents` | All the ancestor folders |
| `parts` | Tuple of every part |
| `anchor` | The root (`/` or `C:\`) |

```python
from pathlib import Path

p = Path("archive/report.tar.gz")
print(p.suffix)
print(p.suffixes)
print(p.stem)
print(list(p.parents))
# Output: .gz
# Output: ['.tar', '.gz']
# Output: report.tar
# Output: [PosixPath('archive'), PosixPath('.')]
```

---

## Changing Parts

Paths are **immutable**. These methods give back a **new** path.

```python
from pathlib import Path

p = Path("docs/report.txt")

print(p.with_suffix(".md"))
print(p.with_name("final.txt"))
print(p.with_stem("summary"))
print(p.parent / "backup" / p.name)
# Output: docs/report.md
# Output: docs/final.txt
# Output: docs/summary.txt
# Output: docs/backup/report.txt
```
`with_stem()` needs Python 3.9+. The original `p` is unchanged. Changing the extension **only changes the name**; it does not convert the file's content or rename the file on disk.

---

## Absolute, Relative and Resolve

```python
from pathlib import Path

p = Path("docs/../notes/day1.txt")

print(p.is_absolute())
print(p.resolve() == (Path.cwd() / "notes" / "day1.txt"))
print(Path("/home/riya/docs/a.txt").relative_to("/home/riya"))
print(Path("/home/riya/docs/a.txt").is_relative_to("/home/riya"))
# Output: False
# Output: True
# Output: docs/a.txt
# Output: True
```
| Method | Does |
|---|---|
| `absolute()` | Puts the current folder in front (does **not** clean `..`) |
| `resolve()` | Absolute path, with `..` and symbolic links worked out |
| `relative_to(base)` | The path **from** `base` (error if not inside it) |
| `is_relative_to(base)` | `True` if inside `base` (Python 3.9+) |

---

## Checking a Path

```python
from pathlib import Path

Path("check").mkdir(exist_ok=True)
Path("check/a.txt").write_text("hello", encoding="utf-8")

print(Path("check").exists())
print(Path("check").is_dir())
print(Path("check/a.txt").is_file())
print(Path("check/missing.txt").exists())
# Output: True
# Output: True
# Output: True
# Output: False
```
`exists()`, `is_file()`, `is_dir()`, `is_symlink()`. They return `False` (not an error) when the path does not exist.

---

## Creating Folders

```python
from pathlib import Path

Path("data/2026/oct").mkdir(parents=True, exist_ok=True)
print(Path("data/2026/oct").is_dir())
# Output: True
```
| Option | Meaning |
|---|---|
| `parents=True` | Create any missing parent folders too (like `mkdir -p`) |
| `exist_ok=True` | Do not fail if the folder already exists |

Without them, a missing parent gives `FileNotFoundError` and an existing folder gives `FileExistsError`.

---

## Reading and Writing Files

For small files, `Path` has one-line helpers.

```python
from pathlib import Path

note = Path("note.txt")
note.write_text("first line\nsecond line\n", encoding="utf-8")

text = note.read_text(encoding="utf-8")
print(repr(text))
print(text.splitlines())
# Output: 'first line\nsecond line\n'
# Output: ['first line', 'second line']
```
```python
from pathlib import Path

data = Path("blob.bin")
data.write_bytes(b"\x00\x01\x02")
print(data.read_bytes())
# Output: b'\x00\x01\x02'
```
`write_text` **replaces** the whole file (like mode `"w"`) and returns the number of characters written. Always give `encoding="utf-8"`.

For line-by-line reading, appending or large files, use `open()`; a `Path` can be passed to `open()` directly, or use its own `open` method:
```python
from pathlib import Path

log = Path("log.txt")
with log.open("a", encoding="utf-8") as f:
    f.write("one\n")
with log.open("a", encoding="utf-8") as f:
    f.write("two\n")

with log.open(encoding="utf-8") as f:
    for line in f:
        print(line.rstrip())
# Output: one
# Output: two
```

---

## Listing a Folder

```python
from pathlib import Path

root = Path("project")
(root / "src").mkdir(parents=True, exist_ok=True)
(root / "main.py").write_text("print('hi')", encoding="utf-8")
(root / "README.md").write_text("# Project", encoding="utf-8")

for item in sorted(root.iterdir()):
    kind = "folder" if item.is_dir() else "file"
    print(kind, item.name)
# Output: file README.md
# Output: file main.py
# Output: folder src
```
`iterdir()` yields every item directly inside the folder, in **no guaranteed order**: wrap it in `sorted()` when order matters. (Capital letters sort before lowercase, so `README.md` comes first.)

---

## Finding Files with glob

`glob(pattern)` finds paths matching a pattern **inside one folder**; `rglob(pattern)` searches **all subfolders** too.

```python
from pathlib import Path

root = Path("project")
(root / "src" / "utils.py").write_text("x = 1", encoding="utf-8")

print(sorted(p.name for p in root.glob("*.py")))
print(sorted(p.name for p in root.rglob("*.py")))
print(sorted(p.name for p in root.glob("*.md")))
# Output: ['main.py']
# Output: ['main.py', 'utils.py']
# Output: ['README.md']
```
| Pattern | Matches |
|---|---|
| `*` | Any characters (not `/`) |
| `?` | Exactly one character |
| `[abc]` | One of `a`, `b`, `c` |
| `**/` | Any number of folders (`rglob` does this for you) |

Also `Path.walk()` (Python 3.12+) goes through a folder tree like `os.walk`.

---

## Renaming, Moving and Deleting

```python
from pathlib import Path

a = Path("old.txt")
a.write_text("data", encoding="utf-8")

b = a.rename("new.txt")
print(a.exists(), b.exists())

b.unlink()
print(b.exists())
# Output: False True
# Output: False
```
| Method | Does |
|---|---|
| `rename(target)` | Rename or move; returns the new `Path` |
| `replace(target)` | Same, but silently **overwrites** an existing target on every system |
| `unlink(missing_ok=True)` | Delete a **file** (`missing_ok` needs 3.8+) |
| `rmdir()` | Delete an **empty** folder |
| `touch()` | Create an empty file (or update its time) |

```python
from pathlib import Path

Path("empty_dir").mkdir(exist_ok=True)
Path("empty_dir").rmdir()
Path("never_existed.txt").unlink(missing_ok=True)
Path("stamp.txt").touch()
print(Path("stamp.txt").exists())
# Output: True
```
Deleting is **permanent**: there is no recycle bin.

---

## File Information

```python
from pathlib import Path

f = Path("sized.txt")
f.write_text("hello world", encoding="utf-8")

info = f.stat()
print(info.st_size)
print(info.st_mtime > 0)
# Output: 11
# Output: True
```
`stat().st_size` is the size in **bytes** and `st_mtime` the last-modified time in seconds since 1970 (convert with `datetime.fromtimestamp`, see the datetime note).

---

## Copying and Removing Trees: shutil

`Path` cannot copy files or delete a folder that is not empty. The `shutil` module does that.

```python
import shutil
from pathlib import Path

Path("src_dir").mkdir(exist_ok=True)
Path("src_dir/a.txt").write_text("a", encoding="utf-8")

shutil.copy("src_dir/a.txt", "copy_of_a.txt")
shutil.copytree("src_dir", "src_backup", dirs_exist_ok=True)
print(Path("copy_of_a.txt").exists(), Path("src_backup/a.txt").exists())

shutil.rmtree("src_backup")
print(Path("src_backup").exists())
# Output: True True
# Output: False
```
`shutil.rmtree()` deletes a folder **and everything inside it** with no confirmation. Double-check the path first.

---

## pathlib vs os.path

| Task | `os` / `os.path` | `pathlib` |
|---|---|---|
| Join | `os.path.join("a", "b")` | `Path("a") / "b"` |
| File name | `os.path.basename(p)` | `p.name` |
| Folder | `os.path.dirname(p)` | `p.parent` |
| Extension | `os.path.splitext(p)[1]` | `p.suffix` |
| Exists | `os.path.exists(p)` | `p.exists()` |
| Is file | `os.path.isfile(p)` | `p.is_file()` |
| Make folder | `os.makedirs(p, exist_ok=True)` | `p.mkdir(parents=True, exist_ok=True)` |
| List | `os.listdir(p)` | `p.iterdir()` |
| Absolute | `os.path.abspath(p)` | `p.resolve()` |
| Delete file | `os.remove(p)` | `p.unlink()` |
| Read text | `open(p).read()` | `p.read_text(encoding="utf-8")` |

Convert between them: `str(path)` or `os.fspath(path)` gives a string; `Path(string)` gives a path. Most modern functions (`open`, `shutil`, `os`) accept `Path` objects directly.

**Paths from another system**, without touching the disk, use the "pure" classes:
```python
from pathlib import PureWindowsPath, PurePosixPath

w = PureWindowsPath("C:/Users/Riya/report.txt")
print(w.name, w.suffix, w.drive)
print(PurePosixPath("/var/log/app.log").parent)
# Output: report.txt .txt C:
# Output: /var/log
```

---

## Practical Examples

**Count files by extension**
```python
from collections import Counter
from pathlib import Path

root = Path("counting")
root.mkdir(exist_ok=True)
for name in ["a.py", "b.py", "c.txt", "d"]:
    (root / name).write_text("", encoding="utf-8")

counts = Counter(p.suffix or "(none)" for p in root.iterdir())
print(sorted(counts.items()))
# Output: [('(none)', 1), ('.py', 2), ('.txt', 1)]
```

**Organise files into folders by extension**
```python
from pathlib import Path

inbox = Path("inbox")
inbox.mkdir(exist_ok=True)
for name in ["photo.jpg", "notes.txt", "song.mp3", "cat.jpg"]:
    (inbox / name).write_text("", encoding="utf-8")

for file in list(inbox.iterdir()):
    if file.is_file():
        folder = inbox / file.suffix.lstrip(".")
        folder.mkdir(exist_ok=True)
        file.rename(folder / file.name)

for file in sorted(inbox.rglob("*")):
    if file.is_file():
        print(file.relative_to(inbox))
# Output: jpg/cat.jpg
# Output: jpg/photo.jpg
# Output: mp3/song.mp3
# Output: txt/notes.txt
```

**Total size of a folder**
```python
from pathlib import Path

folder = Path("sizes")
folder.mkdir(exist_ok=True)
(folder / "a.txt").write_text("12345", encoding="utf-8")
(folder / "b.txt").write_text("123", encoding="utf-8")

total = sum(f.stat().st_size for f in folder.rglob("*") if f.is_file())
print(total, "bytes")
# Output: 8 bytes
```

**Safe file reading**
```python
from pathlib import Path

def read_or_default(path, default=""):
    p = Path(path)
    if p.is_file():
        return p.read_text(encoding="utf-8")
    return default

print(repr(read_or_default("no_such_file.txt", "none")))
# Output: 'none'
```

**Tidy up the demo files**
```python
import shutil
from pathlib import Path

for name in ["check", "data", "project", "counting", "inbox", "sizes", "src_dir", "empty_dir"]:
    shutil.rmtree(name, ignore_errors=True)
for name in ["note.txt", "blob.bin", "log.txt", "new.txt", "stamp.txt", "sized.txt", "copy_of_a.txt"]:
    Path(name).unlink(missing_ok=True)
print("cleaned")
# Output: cleaned
```

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| `"folder" / "file"` (strings only) | `TypeError` | `Path("folder") / "file"` |
| Joining an absolute path: `Path("a") / "/b"` | Result is `/b` | Do not start the second part with `/` |
| Expecting `Path("x")` to create the file | Nothing happens on disk | `.touch()`, `.write_text()`, `.mkdir()` |
| `mkdir()` for a nested folder without `parents=True` | `FileNotFoundError` | `parents=True` |
| `mkdir()` on an existing folder | `FileExistsError` | `exist_ok=True` |
| `rmdir()` on a folder that is not empty | `OSError` | `shutil.rmtree()` (careful) |
| `write_text()` on an existing file | Old content lost | Use `open(..., "a")` to append |
| Forgetting `encoding="utf-8"` | Depends on the computer | Always give it |
| Relying on the order of `iterdir()` or `glob()` | Order varies | `sorted(...)` |
| Backslashes in a normal string: `"C:\new\table.txt"` | `\n` and `\t` become special characters | Raw string `r"C:\new\table.txt"` or forward slashes |
| `p.suffix` on `archive.tar.gz` expecting `.tar.gz` | Gives only `.gz` | `"".join(p.suffixes)` |
| Treating `with_suffix` as a file conversion | Only the name changes | Convert the content separately |
| `rename()` onto an existing file | Behaves differently on Windows and Linux | `replace()` |
| Using `Path(__file__)` in the interactive shell | `NameError` | Only inside a script file |

---

- [Back to section index](./README.md)
