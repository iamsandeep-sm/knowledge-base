# File Handling Practice

Related notes: [Reading Files](../../08-file-handling/reading-files.md), [Writing Files](../../08-file-handling/writing-files.md), [Context Managers](../../08-file-handling/context-manager.md), [CSV Files](../../08-file-handling/csv-files.md), [JSON Files](../../08-file-handling/json-files.md)

## Table of Contents
- [Practice Questions](#practice-questions)
- [Practice Answers](#practice-answers)

---

## Practice Questions

1. Write a file `hello.txt` containing the lines `Hello`, `Python` and `Files`, then read it back and print the whole content.
2. What is the difference between the modes `"r"`, `"w"`, `"a"` and `"x"`?
3. Why should you use `with open(...)` instead of calling `close()` yourself?
4. Print the lines of `hello.txt` one by one without blank lines between them.
5. Read `hello.txt` into a list of lines **without** the newline characters.
6. What do `read()`, `readline()` and `readlines()` return?
7. Append the line `More` to `hello.txt` without erasing the old content.
8. What happens to an existing file when you open it with `"w"`?
9. Why does this write everything on one line? Fix it.
   ```python
   with open("names.txt", "w", encoding="utf-8") as f:
       for name in ["Riya", "Amit", "Sam"]:
           f.write(name)
   ```
10. Count the number of lines and words in `hello.txt`.
11. Read a file twice: why is the second `read()` empty, and how do you fix it?
12. What error appears when you open a file that does not exist? Handle it with a friendly message.
13. Why should you add `encoding="utf-8"` when opening a text file?
14. Write a program that copies `hello.txt` to `copy.txt` in upper case.
15. Why does `f.write(42)` fail? Write the number correctly.
16. Write the list `[["name", "age"], ["Riya", 20], ["Amit", 22]]` to `people.csv` correctly. Why `newline=""`?
17. Read `people.csv` with `csv.DictReader` and print the total of the ages.
18. Why is `line.split(",")` not a safe way to read CSV?
19. Save the dictionary `{"name": "Riya", "scores": [90, 85]}` to `data.json` with indentation, then load it and print the average score.
20. What are the differences between `json.dump`, `json.dumps`, `json.load` and `json.loads`? What happens to a tuple and to an integer dictionary key after saving and loading?

---

## Practice Answers

Try each question yourself first, then compare.

**Answer 1**
```python
with open("hello.txt", "w", encoding="utf-8") as f:
    f.write("Hello\nPython\nFiles\n")

with open("hello.txt", encoding="utf-8") as f:
    print(f.read())
# Output: Hello
# Output: Python
# Output: Files
# Output:
```
(The last empty line is the newline at the end of the text plus the one `print` adds.)

**Answer 2**

| Mode | Meaning |
|---|---|
| `"r"` | Read (default). Error if the file does not exist |
| `"w"` | Write. Creates the file, or **erases** an existing one |
| `"a"` | Append. Keeps the content and writes at the end |
| `"x"` | Create a new file. `FileExistsError` if it already exists |

**Answer 3**

`with` closes the file automatically, even when an error occurs inside the block. With `close()` you can forget it, or an error can skip it, which can lose data and leave the file locked.

**Answer 4**
```python
with open("hello.txt", encoding="utf-8") as f:
    for line in f:
        print(line.strip())
# Output: Hello
# Output: Python
# Output: Files
```

**Answer 5**
```python
with open("hello.txt", encoding="utf-8") as f:
    lines = f.read().splitlines()
print(lines)
# Output: ['Hello', 'Python', 'Files']
```

**Answer 6**

`read()` returns the whole file as **one string**. `readline()` returns **one line** (and `""` at the end). `readlines()` returns a **list of all lines** (each with its `\n`).

**Answer 7**
```python
with open("hello.txt", "a", encoding="utf-8") as f:
    f.write("More\n")

with open("hello.txt", encoding="utf-8") as f:
    print(f.read().splitlines())
# Output: ['Hello', 'Python', 'Files', 'More']
```

**Answer 8**

It is **emptied immediately**, even before anything is written. Use `"a"` to keep the old content.

**Answer 9**

`write()` does not add a newline. Add `\n`:
```python
with open("names.txt", "w", encoding="utf-8") as f:
    for name in ["Riya", "Amit", "Sam"]:
        f.write(name + "\n")

with open("names.txt", encoding="utf-8") as f:
    print(f.read().splitlines())
# Output: ['Riya', 'Amit', 'Sam']
```

**Answer 10**
```python
with open("hello.txt", encoding="utf-8") as f:
    text = f.read()
print("lines:", len(text.splitlines()))
print("words:", len(text.split()))
# Output: lines: 4
# Output: words: 4
```

**Answer 11**

After the first `read()` the file position is at the **end**. Go back with `f.seek(0)`, or reopen the file.
```python
with open("hello.txt", encoding="utf-8") as f:
    first = f.read()
    second = f.read()
    f.seek(0)
    third = f.read()
print(len(first) > 0, second == "", third == first)
# Output: True True True
```

**Answer 12**

`FileNotFoundError`.
```python
try:
    with open("missing.txt", encoding="utf-8") as f:
        print(f.read())
except FileNotFoundError:
    print("Sorry, that file does not exist")
# Output: Sorry, that file does not exist
```

**Answer 13**

The default encoding depends on the computer (and Python version; 3.15 makes UTF-8 the default), so the same program can read or write different characters on different systems. `encoding="utf-8"` makes it behave the same everywhere.

**Answer 14**
```python
with open("hello.txt", encoding="utf-8") as src, open("copy.txt", "w", encoding="utf-8") as dst:
    for line in src:
        dst.write(line.upper())

with open("copy.txt", encoding="utf-8") as f:
    print(f.read().splitlines())
# Output: ['HELLO', 'PYTHON', 'FILES', 'MORE']
```

**Answer 15**

`write()` accepts only strings: `TypeError: write() argument must be str, not int`. Convert it:
```python
with open("number.txt", "w", encoding="utf-8") as f:
    f.write(str(42))

with open("number.txt", encoding="utf-8") as f:
    print(int(f.read()) + 1)
# Output: 43
```

**Answer 16**
```python
import csv

rows = [["name", "age"], ["Riya", 20], ["Amit", 22]]
with open("people.csv", "w", newline="", encoding="utf-8") as f:
    csv.writer(f).writerows(rows)

with open("people.csv", encoding="utf-8") as f:
    print(f.read().splitlines())
# Output: ['name,age', 'Riya,20', 'Amit,22']
```
The `csv` module handles line endings itself. Without `newline=""` you can get blank lines between rows on Windows, and quoted values that contain line breaks can be read wrongly.

**Answer 17**
```python
import csv

with open("people.csv", newline="", encoding="utf-8") as f:
    total = sum(int(row["age"]) for row in csv.DictReader(f))
print(total)
# Output: 42
```
CSV values are always strings, so `int()` is needed.

**Answer 18**

A value can contain a comma inside quotes, such as `"Pune, India"`. `split(",")` cuts it into two pieces, while `csv.reader` handles quotes correctly.

**Answer 19**
```python
import json

data = {"name": "Riya", "scores": [90, 85]}
with open("data.json", "w", encoding="utf-8") as f:
    json.dump(data, f, indent=4)

with open("data.json", encoding="utf-8") as f:
    loaded = json.load(f)
print(sum(loaded["scores"]) / len(loaded["scores"]))
# Output: 87.5
```

**Answer 20**

| Function | Direction | Uses |
|---|---|---|
| `json.dump(obj, f)` | Python to JSON | a file |
| `json.dumps(obj)` | Python to JSON | a string |
| `json.load(f)` | JSON to Python | a file |
| `json.loads(text)` | JSON to Python | a string |

After saving and loading, a **tuple becomes a list** and an **integer key becomes a string**:
```python
import json

back = json.loads(json.dumps({"point": (3, 4), 1: "one"}))
print(back)
# Output: {'point': [3, 4], '1': 'one'}
```

---

- [Back to practice index](../README.md)
