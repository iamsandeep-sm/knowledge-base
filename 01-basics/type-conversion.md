# Type Casting and Data Conversion

**Type casting** means **changing a value from one data type to another**.

### Two kinds

| Kind | Who does it | Example |
|---|---|---|
| **Implicit** (automatic) | Python | `5 + 2.0` becomes `7.0` |
| **Explicit** (you do it) | You, with `int()`, `float()`, `str()` ... | `int("42")` becomes `42` |

**Implicit conversion**: Python quietly changes a smaller type to a bigger one so the maths works (`bool` to `int` to `float`).
```python
print(5 + 2.0)       # 7.0     int became float
print(True + 1)      # 2       bool became int
print(type(5 + 2.0)) # <class 'float'>
```

Python will **not** guess when it would be unsafe, such as number + text:
```python
# print("Age: " + 19)    # TypeError: can only concatenate str (not "int") to str
print("Age: " + str(19))   # Age: 19     convert the number to text yourself
```

### Conversion functions

| Function | Converts to | Examples |
|---|---|---|
| `int()` | Integer | `int("42")` gives `42`, `int(3.9)` gives `3` |
| `float()` | Float | `float("3.14")` gives `3.14`, `float(5)` gives `5.0` |
| `str()` | String | `str(10)` gives `"10"`, `str(True)` gives `"True"` |
| `bool()` | Boolean | `bool(0)` gives `False`, `bool("a")` gives `True` |
| `list()` | List | `list("abc")` gives `['a', 'b', 'c']` |
| `tuple()` | Tuple | `tuple([1, 2])` gives `(1, 2)` |
| `set()` | Set | `set([1, 1, 2])` gives `{1, 2}` |

```python
print(int("42") + 1)        # 43
print(int(3.9))             # 3       cuts off the decimal part, does NOT round
print(int(-3.9))            # -3
print(float("3.14"))        # 3.14
print(float(5))             # 5.0
print(str(10) + "5")        # 105     text joined
print(int(True))            # 1
print(list("abc"))          # ['a', 'b', 'c']
print(tuple([1, 2]))        # (1, 2)
print(set([1, 1, 2]))       # {1, 2}
print(int("  42  "))        # 42      spaces around the number are allowed
```

### Conversions that fail
```python
# int("abc")     # ValueError: invalid literal for int() with base 10: 'abc'
# int("3.5")     # ValueError: invalid literal for int() with base 10: '3.5'
print(int(float("3.5")))   # 3      first to float, then to int
```
- `int()` of a **string** needs a **whole number** text such as `"42"`.
- `int()` of a **float** (number) cuts the decimals.
- Use `round()` if you want proper rounding: `round(3.9)` is `4`, but `int(3.9)` is `3`.

### Quick conversion map
```
str  <---->  int / float      int("12")  float("1.5")   str(12)
int  <---->  float            float(3)   int(3.7)
any  ---->   bool             bool(value)
str  ---->   list             list("abc")
list <---->  tuple <----> set
```

---

- [Back to section index](./README.md)
