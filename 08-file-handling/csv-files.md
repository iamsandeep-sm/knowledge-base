# CSV Files - Revision Notes ~ sm

> **In one line:** A **CSV** (Comma-Separated Values) file stores a **table** as plain text: one row per line, with the columns separated by commas. Python's built-in **`csv`** module reads and writes it correctly, including values that themselves contain commas or quotes.

## Table of Contents
- [What is a CSV File?](#what-is-a-csv-file)
- [Why Use the csv Module?](#why-use-the-csv-module)
- [Writing with csv.writer](#writing-with-csvwriter)
- [Why newline=""?](#why-newline)
- [Reading with csv.reader](#reading-with-csvreader)
- [Everything Read is Text](#everything-read-is-text)
- [DictWriter](#dictwriter)
- [DictReader](#dictreader)
- [Other Delimiters](#other-delimiters)
- [Quotes and Commas Inside Values](#quotes-and-commas-inside-values)
- [Append Rows](#append-rows)
- [Practical Examples](#practical-examples)
- [Common Mistakes](#common-mistakes)

---

## What is a CSV File?

A spreadsheet-like table written as text. The first row is usually the **header** (column names).

```
name,age,city
Riya,20,Pune
Amit,22,Delhi
Sam,19,Mumbai
```
CSV is the common exchange format for spreadsheets (Excel, Google Sheets), databases and data tools.

| Word | Meaning |
|---|---|
| **Row / record** | One line |
| **Field / column** | One value in a row |
| **Header** | First row with column names |
| **Delimiter** | The separator character (comma by default) |

---

## Why Use the csv Module?

You could use `line.split(",")`, but it breaks when a value contains a comma or a quote, such as `"Pune, India"`. The `csv` module handles these cases for you.

```python
line = 'Riya,"Pune, India",20'
print(line.split(","))
# Output: ['Riya', '"Pune', ' India"', '20']
```
Three pieces instead of the expected two. The `csv` module reads this line correctly (shown later).

---

## Writing with csv.writer

```python
import csv

rows = [
    ["name", "age", "city"],
    ["Riya", 20, "Pune"],
    ["Amit", 22, "Delhi"],
    ["Sam", 19, "Mumbai"],
]

with open("people.csv", "w", newline="", encoding="utf-8") as f:
    writer = csv.writer(f)
    writer.writerows(rows)              # many rows at once

with open("people.csv", encoding="utf-8") as f:
    print(f.read())
# Output: name,age,city
# Output: Riya,20,Pune
# Output: Amit,22,Delhi
# Output: Sam,19,Mumbai
# Output:
```

| Method | Writes |
|---|---|
| `writer.writerow(row)` | **One** row (a list or tuple) |
| `writer.writerows(rows)` | Many rows (a list of lists) |

Numbers are converted to text automatically.

---

## Why newline=""?

The documentation says to open CSV files with `newline=""`. The `csv` module manages line endings itself, and without it, files written on Windows can get an **extra blank line** between rows, and quoted values that contain line breaks can be read wrongly.

Rule: **always** write `open(path, "w", newline="", encoding="utf-8")` for CSV files (and `newline=""` when reading too).

---

## Reading with csv.reader

`csv.reader(f)` gives each row as a **list of strings**.

```python
import csv

with open("people.csv", newline="", encoding="utf-8") as f:
    reader = csv.reader(f)
    header = next(reader)               # first row
    print(header)
    for row in reader:
        print(row)
# Output: ['name', 'age', 'city']
# Output: ['Riya', '20', 'Pune']
# Output: ['Amit', '22', 'Delhi']
# Output: ['Sam', '19', 'Mumbai']
```
- `next(reader)` takes the header off, so the loop sees only the data rows.
- To load every row into a list: `rows = list(reader)`.
- The reader is an iterator: it can be looped over **once**.

---

## Everything Read is Text

CSV has no types. Every value comes back as a **string**, so convert it yourself.

```python
import csv

total_age = 0
with open("people.csv", newline="", encoding="utf-8") as f:
    reader = csv.reader(f)
    next(reader)
    for name, age, city in reader:
        total_age += int(age)           # '20' -> 20

print(total_age)
# Output: 61
```
Without `int()`, `"20" + "22"` would give the text `"2022"` instead of a sum. (Unpacking each row into `name, age, city` works because each row is a list of three items.)

---

## DictWriter

Write dictionaries, one per row. You provide the column names with `fieldnames`.

```python
import csv

people = [
    {"name": "Riya", "age": 20, "city": "Pune"},
    {"name": "Amit", "age": 22, "city": "Delhi"},
]

with open("people2.csv", "w", newline="", encoding="utf-8") as f:
    writer = csv.DictWriter(f, fieldnames=["name", "age", "city"])
    writer.writeheader()                # writes name,age,city
    writer.writerows(people)

with open("people2.csv", encoding="utf-8") as f:
    print(f.read().splitlines())
# Output: ['name,age,city', 'Riya,20,Pune', 'Amit,22,Delhi']
```
`writeheader()` writes the header row. A dictionary with a key that is **not** in `fieldnames` raises `ValueError`.

---

## DictReader

Each row becomes a **dictionary** keyed by the header. The code is easier to read because you use column **names**, not positions.

```python
import csv

with open("people.csv", newline="", encoding="utf-8") as f:
    for row in csv.DictReader(f):
        print(row["name"], row["city"])
# Output: Riya Pune
# Output: Amit Delhi
# Output: Sam Mumbai
```
Each `row` is a regular dictionary (in Python 3.8 and newer):
```python
import csv

with open("people.csv", newline="", encoding="utf-8") as f:
    rows = list(csv.DictReader(f))

print(rows[0])
print(sum(int(r["age"]) for r in rows) / len(rows))
# Output: {'name': 'Riya', 'age': '20', 'city': 'Pune'}
# Output: 20.333333333333332
```
The values are still strings (`'20'`), so convert them when you calculate.

---

## Other Delimiters

Some files use tabs, semicolons or pipes. Pass `delimiter=`.

```python
import csv

with open("semi.csv", "w", newline="", encoding="utf-8") as f:
    csv.writer(f, delimiter=";").writerow(["a", "b", "c"])

with open("semi.csv", newline="", encoding="utf-8") as f:
    print(next(csv.reader(f, delimiter=";")))
# Output: ['a', 'b', 'c']
```
For tab-separated files (`.tsv`) use `delimiter="\t"`. Excel in some countries saves CSV with `;` because the comma is the decimal mark there.

---

## Quotes and Commas Inside Values

`csv.writer` automatically puts quotes around values that need them, and `csv.reader` removes them again.

```python
import csv

with open("quote.csv", "w", newline="", encoding="utf-8") as f:
    csv.writer(f).writerow(["Riya", "Pune, India", 'She said "hi"'])

with open("quote.csv", encoding="utf-8") as f:
    print(f.read().strip())

with open("quote.csv", newline="", encoding="utf-8") as f:
    print(next(csv.reader(f)))
# Output: Riya,"Pune, India","She said ""hi"""
# Output: ['Riya', 'Pune, India', 'She said "hi"']
```
Inside a quoted value, a quote mark is written twice (`""`). You do not need to handle any of this yourself.

---

## Append Rows

Open with `"a"` and write the new rows (do **not** write the header again).

```python
import csv

with open("people.csv", "a", newline="", encoding="utf-8") as f:
    csv.writer(f).writerow(["Zoya", 21, "Goa"])

with open("people.csv", newline="", encoding="utf-8") as f:
    print(len(list(csv.reader(f))))
# Output: 5
```

---

## Practical Examples

**Filter rows into a new file**
```python
import csv

with open("people.csv", newline="", encoding="utf-8") as src, \
     open("adults_over_20.csv", "w", newline="", encoding="utf-8") as dst:
    reader = csv.DictReader(src)
    writer = csv.DictWriter(dst, fieldnames=reader.fieldnames)
    writer.writeheader()
    for row in reader:
        if int(row["age"]) > 20:
            writer.writerow(row)

with open("adults_over_20.csv", encoding="utf-8") as f:
    print(f.read().splitlines())
# Output: ['name,age,city', 'Amit,22,Delhi', 'Zoya,21,Goa']
```

**Count rows per city**
```python
import csv
from collections import Counter

with open("people.csv", newline="", encoding="utf-8") as f:
    counts = Counter(row["city"] for row in csv.DictReader(f))

print(counts["Pune"], counts["Goa"])
# Output: 1 1
```

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Forgetting `newline=""` | Blank lines between rows (Windows), broken multi-line values | `open(..., newline="")` |
| Using `line.split(",")` | Breaks on commas inside quotes | Use `csv.reader` |
| Treating values as numbers | They are strings: `"20" + "22"` is `"2022"` | `int(...)` / `float(...)` |
| Header included in the calculations | `ValueError: invalid literal for int() ... 'age'` | `next(reader)` first, or use `DictReader` |
| Looping over a `reader` twice | Second loop is empty | `rows = list(reader)` |
| Wrong delimiter | Each row is one big string | `delimiter=";"` |
| Writing the header again when appending | A repeated header row in the middle | Write it only for a new file |
| Dictionary key missing from `fieldnames` | `ValueError: dict contains fields not in fieldnames` | Add it to `fieldnames` |
| Special characters look wrong in Excel | Encoding mismatch | For Excel on Windows try `encoding="utf-8-sig"` |

---

- [Back to section index](./README.md)
