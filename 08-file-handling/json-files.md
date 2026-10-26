# JSON Files - Revision Notes ~ sm

> **In one line:** **JSON** (JavaScript Object Notation) is a text format for storing **structured data** such as dictionaries and lists. Python's built-in **`json`** module converts Python objects to JSON text (`dump` / `dumps`) and back (`load` / `loads`).

## Table of Contents
- [What is JSON?](#what-is-json)
- [JSON vs Python](#json-vs-python)
- [The Four Functions](#the-four-functions)
- [Python to JSON String: dumps()](#python-to-json-string-dumps)
- [JSON String to Python: loads()](#json-string-to-python-loads)
- [Writing a JSON File: dump()](#writing-a-json-file-dump)
- [Reading a JSON File: load()](#reading-a-json-file-load)
- [Pretty Printing and Sorting](#pretty-printing-and-sorting)
- [Non-English Text](#non-english-text)
- [What Cannot Be Converted](#what-cannot-be-converted)
- [Dictionary Keys Become Strings](#dictionary-keys-become-strings)
- [Invalid JSON](#invalid-json)
- [Update a JSON File](#update-a-json-file)
- [Nested Data](#nested-data)
- [Common Mistakes](#common-mistakes)

---

## What is JSON?

JSON is a plain-text way to write structured data. It looks very like Python dictionaries and lists, which is why it is so popular for **configuration files**, **saving program data**, and **web APIs**.

```json
{
  "name": "Riya",
  "age": 20,
  "skills": ["python", "sql"],
  "active": true,
  "address": null
}
```
It is understood by almost every programming language, so it is also a good way to share data between programs.

---

## JSON vs Python

| JSON | Python |
|---|---|
| object `{ }` | `dict` |
| array `[ ]` | `list` |
| string `"text"` | `str` |
| number (`10`, `3.5`) | `int` / `float` |
| `true` / `false` | `True` / `False` |
| `null` | `None` |

Differences in writing:
- JSON strings must use **double quotes** `"..."` (single quotes are invalid).
- JSON uses lower-case `true`, `false`, `null`.
- JSON has **no comments** and **no trailing commas**.
- A Python **tuple** is written as a JSON array, and comes back as a **list**.

---

## The Four Functions

| Function | Direction | Works with |
|---|---|---|
| `json.dumps(obj)` | Python to JSON | a **s**tring |
| `json.loads(text)` | JSON to Python | a **s**tring |
| `json.dump(obj, f)` | Python to JSON | a **file** |
| `json.load(f)` | JSON to Python | a **file** |

The **s** at the end means "string". Without the `s`, the function works with a file.

```python
import json
```

---

## Python to JSON String: dumps()

```python
import json

data = {"name": "Riya", "age": 20, "skills": ["python", "sql"], "active": True, "address": None}

text = json.dumps(data)
print(text)
print(type(text))
# Output: {"name": "Riya", "age": 20, "skills": ["python", "sql"], "active": true, "address": null}
# Output: <class 'str'>
```
Notice `True` became `true` and `None` became `null`.

---

## JSON String to Python: loads()

```python
import json

text = '{"name": "Riya", "age": 20, "active": true, "address": null}'
data = json.loads(text)

print(data)
print(data["name"], type(data["age"]))
# Output: {'name': 'Riya', 'age': 20, 'active': True, 'address': None}
# Output: Riya <class 'int'>
```
Now `true` is `True` and `null` is `None`, and the data is a normal dictionary.

---

## Writing a JSON File: dump()

```python
import json

data = {"name": "Riya", "scores": [90, 85, 77]}

with open("data.json", "w", encoding="utf-8") as f:
    json.dump(data, f, indent=4)

with open("data.json", encoding="utf-8") as f:
    print(f.read())
# Output: {
# Output:     "name": "Riya",
# Output:     "scores": [
# Output:         90,
# Output:         85,
# Output:         77
# Output:     ]
# Output: }
```

---

## Reading a JSON File: load()

```python
import json

with open("data.json", encoding="utf-8") as f:
    data = json.load(f)

print(data["name"])
print(sum(data["scores"]))
# Output: Riya
# Output: 252
```
`json.load(f)` reads the whole file and gives back the Python object (here a dictionary). A **round trip** (dump then load) gives you the same data back.

---

## Pretty Printing and Sorting

| Argument | Effect |
|---|---|
| `indent=4` | Spreads the JSON over several lines with 4-space indentation |
| `sort_keys=True` | Writes the keys in alphabetical order |
| `separators=(",", ":")` | Most compact output (no spaces) |

```python
import json

d = {"b": 1, "a": [1, 2]}
print(json.dumps(d, indent=2, sort_keys=True))
print(json.dumps(d, separators=(",", ":")))
# Output: {
# Output:   "a": [
# Output:     1,
# Output:     2
# Output:   ],
# Output:   "b": 1
# Output: }
# Output: {"b":1,"a":[1,2]}
```
Use `indent` for files people will read, and the compact form when size matters.

---

## Non-English Text

By default `json` writes non-ASCII characters as escape codes. Use `ensure_ascii=False` to keep the real characters (and write the file as UTF-8).

```python
import json

d = {"greeting": "नमस्ते", "city": "café"}
print(json.dumps(d))
print(json.dumps(d, ensure_ascii=False))
# Output: {"greeting": "\u0928\u092e\u0938\u094d\u0924\u0947", "city": "caf\u00e9"}
# Output: {"greeting": "नमस्ते", "city": "café"}
```
Both forms load back to the same Python data.

---

## What Cannot Be Converted

Only the types in the table above are supported. Sets, dates, and your own objects raise `TypeError`.

```python
import json

json.dumps({"tags": {1, 2}})
# Error:  TypeError: Object of type set is not JSON serializable
```
Fixes: convert before saving, or give `dumps` a `default=` function.

```python
import json
from datetime import date

print(json.dumps({"tags": sorted({2, 1})}))
print(json.dumps({"day": date(2026, 10, 26)}, default=str))
# Output: {"tags": [1, 2]}
# Output: {"day": "2026-10-26"}
```
`default=str` is called for every value `json` does not understand, here turning the date into text. It will come back as a **string**, not a date.

Other values: `float("nan")` and infinity are written as `NaN` / `Infinity`, which is not valid standard JSON.

---

## Dictionary Keys Become Strings

JSON object keys are always strings, so other key types change after a round trip.

```python
import json

d = {1: "one", 2: "two"}
back = json.loads(json.dumps(d))
print(back)
print(back == d)
# Output: {'1': 'one', '2': 'two'}
# Output: False
```
Tuples also come back as lists:
```python
import json

print(json.loads(json.dumps({"point": (3, 4)})))
# Output: {'point': [3, 4]}
```

---

## Invalid JSON

Wrong JSON raises `json.JSONDecodeError` (a subclass of `ValueError`).

```python
import json

bad = "{'name': 'Riya'}"          # single quotes are not allowed
try:
    json.loads(bad)
except json.JSONDecodeError as e:
    print("Invalid JSON:", e.msg)
# Output: Invalid JSON: Expecting property name enclosed in double quotes
```
Typical causes: single quotes, trailing comma, a missing quote or bracket, `True`/`None` instead of `true`/`null`, a comment, or an empty file.

---

## Update a JSON File

The usual pattern is **load, change, save**.

```python
import json

with open("data.json", encoding="utf-8") as f:
    data = json.load(f)

data["scores"].append(100)
data["city"] = "Pune"

with open("data.json", "w", encoding="utf-8") as f:
    json.dump(data, f, indent=4)

with open("data.json", encoding="utf-8") as f:
    print(json.load(f))
# Output: {'name': 'Riya', 'scores': [90, 85, 77, 100], 'city': 'Pune'}
```
Do not open the file with `"w"` **before** loading it: `"w"` empties the file at once. Load first, close, then open for writing.

A safe helper that handles a missing file:
```python
import json
import os

def load_or_default(path, default):
    if not os.path.exists(path):
        return default
    with open(path, encoding="utf-8") as f:
        return json.load(f)

print(load_or_default("nothing_here.json", {"items": []}))
# Output: {'items': []}
```

---

## Nested Data

JSON can contain objects inside objects, and lists of objects. Use indexes and keys to reach the values.

```python
import json

text = """
{
  "students": [
    {"name": "Riya", "marks": {"maths": 90, "science": 85}},
    {"name": "Amit", "marks": {"maths": 70, "science": 60}}
  ]
}
"""
data = json.loads(text)

print(data["students"][0]["name"])
print(data["students"][1]["marks"]["maths"])
for s in data["students"]:
    print(s["name"], sum(s["marks"].values()))
# Output: Riya
# Output: 70
# Output: Riya 175
# Output: Amit 130
```
See the Nested Data Structures note for more on this.

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| `json.dump(data)` without a file | `TypeError: dump() missing 1 required positional argument: 'fp'` | `json.dump(data, f)` |
| `json.load(text)` with a string | `AttributeError: 'str' object has no attribute 'read'` | `json.loads(text)` |
| Mixing up `dump`/`dumps` and `load`/`loads` | Type errors | The `s` = string |
| Single quotes in JSON text | `JSONDecodeError` | Double quotes only |
| Saving a set or date | `TypeError: ... is not JSON serializable` | Convert, or use `default=` |
| Expecting integer keys to survive | They become strings | Convert back with `int(key)` |
| Expecting tuples to survive | They become lists | Convert back with `tuple(...)` |
| Opening with `"w"` before loading | The data is erased | Load first, then write |
| Reading an empty file | `JSONDecodeError: Expecting value` | Check for an empty file, or use a default |
| Writing the result of `str(data)` instead of using `json` | Python-style text (`True`, `None`, single quotes), invalid JSON | `json.dump` / `json.dumps` |

---

- [Back to section index](./README.md)
