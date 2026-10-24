# Booleans

A **boolean** (`bool`) has only **two values**: `True` and `False`. Both start with a **capital letter**.

```python
is_raining = True
is_sunny = False
print(is_raining, type(is_raining))
# Output: True <class 'bool'>
```

### Comparisons give booleans
```python
print(10 > 5)      # True
print(10 == 5)     # False
print(10 != 5)     # True
print("a" == "a")  # True
```

### `bool()` - is a value true or false?
Almost every value counts as `True`. The common values that count as `False` (called **falsy** values) are:

| Falsy values |
|---|
| `False`, `None` |
| `0`, `0.0` |
| `""` (empty string) |
| `[]`, `()`, `{}`, `set()` (empty collections) |

```python
print(bool(0))         # False
print(bool(""))        # False
print(bool([]))        # False
print(bool(None))      # False
print(bool(5))         # True
print(bool("hello"))   # True
print(bool("False"))   # True     a non-empty string is always True
```

### Booleans behave like numbers
`True` is `1` and `False` is `0` when used in calculations.
```python
print(True + True)        # 2
print(True * 10)          # 10
print(isinstance(True, int))   # True
```

---

- [Back to section index](./README.md)
