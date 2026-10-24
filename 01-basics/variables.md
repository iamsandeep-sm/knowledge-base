# Variables

A **variable** is a **name that refers to a value**. Think of it as a **label** you stick on a value so you can use it later.

```python
name = "Karan"
age = 19
height = 5.9
is_student = True

print(name, age, height, is_student)
# Output: Karan 19 5.9 True
```

- `=` means **assign** ("store this value under this name"). It is **not** "equals" from maths.
- You do **not** declare a type. Python works it out from the value (this is called **dynamic typing**).
- A variable must be assigned **before** you use it, otherwise you get `NameError`.

### Changing a variable
You can give a variable a new value any time, even a value of a different type.
```python
x = 10
print(x)
x = x + 5
print(x)
x = "ten"
print(x)
# Output: 10
# Output: 15
# Output: ten
```

### Assigning several variables
```python
a, b, c = 1, 2, 3        # three values to three names
x = y = z = 0            # same value to all names
print(a, b, c)
print(x, y, z)
# Output: 1 2 3
# Output: 0 0 0
```

### Swapping two variables
```python
a = 10
b = 20
a, b = b, a
print(a, b)
# Output: 20 10
```

### Naming rules (these are mandatory)

| Rule | Valid | Invalid |
|---|---|---|
| Use letters, digits and underscore `_` only | `user_name`, `total2` | `user-name`, `total$` |
| Cannot start with a digit | `item1`, `_count` | `1item` |
| No spaces | `first_name` | `first name` |
| Cannot be a Python keyword | `class_name` | `class`, `if`, `for`, `True` |
| Case-sensitive | `age` and `Age` are **different** variables | |

Keywords are words Python reserves for itself, such as `if`, `else`, `for`, `while`, `def`, `class`, `import`, `return`, `True`, `False`, `None`.

### Naming style (conventions, good habits)
| Style | Use for | Example |
|---|---|---|
| `snake_case` | variables and functions | `total_marks`, `user_age` |
| `UPPER_CASE` | constants (values that should not change) | `PI = 3.14`, `MAX_SIZE = 100` |
| Meaningful names | always | `price` is better than `p` |

> Do not use names of built-in functions as variable names, such as `list`, `str`, `print`, `max`, `sum`. It will stop those functions working in your program.

---

- [Back to section index](./README.md)
