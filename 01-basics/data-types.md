# Data Types

Every value has a **data type** that tells Python **what kind of data it is** and **what you can do with it**.

| Type | Name | Example | Meaning |
|---|---|---|---|
| `int` | Integer | `10`, `-5`, `0` | Whole numbers |
| `float` | Floating point | `3.14`, `-0.5`, `2.0` | Numbers with a decimal point |
| `complex` | Complex number | `3 + 4j` | Real + imaginary part |
| `str` | String | `"Hello"`, `'A'` | Text |
| `bool` | Boolean | `True`, `False` | Yes / No |
| `list` | List | `[1, 2, 3]` | Ordered, changeable collection |
| `tuple` | Tuple | `(1, 2, 3)` | Ordered, **unchangeable** collection |
| `set` | Set | `{1, 2, 3}` | Unordered collection of **unique** items |
| `dict` | Dictionary | `{"name": "Karan"}` | Key and value pairs |
| `NoneType` | None | `None` | "No value" |

```python
print(type(10))            # <class 'int'>
print(type(3.14))          # <class 'float'>
print(type(3 + 4j))        # <class 'complex'>
print(type("Hello"))       # <class 'str'>
print(type(True))          # <class 'bool'>
print(type([1, 2]))        # <class 'list'>
print(type((1, 2)))        # <class 'tuple'>
print(type({1, 2}))        # <class 'set'>
print(type({"a": 1}))      # <class 'dict'>
print(type(None))          # <class 'NoneType'>
```

`type()` tells you the type of any value or variable.

### Groups of data types
| Group | Types |
|---|---|
| Numeric | `int`, `float`, `complex` |
| Text | `str` |
| Boolean | `bool` |
| Sequence | `list`, `tuple`, `range` |
| Mapping | `dict` |
| Set | `set`, `frozenset` |
| None | `NoneType` |

> Lists, tuples, sets and dictionaries each have their own detailed notes. Here you only need to recognise them.

### `None` means "nothing here yet"
```python
result = None
print(result)           # None
print(result is None)   # True
```

---

- [Back to section index](./README.md)
