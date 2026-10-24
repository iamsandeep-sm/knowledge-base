# Print and Comments

### `print()` shows output on the screen
```python
print("Hello, World!")
print("Python is fun")
print(10)
print(3 + 4)
# Output: Hello, World!
# Output: Python is fun
# Output: 10
# Output: 7
```

| Rule | Example |
|---|---|
| Text (a **string**) goes inside quotes, single or double | `"Hello"` or `'Hello'` |
| Numbers and calculations go **without** quotes | `print(3 + 4)` prints `7` |
| Each `print()` starts on a **new line** | |
| Python is **case-sensitive** | `print` works, `Print` gives `NameError` |
| Brackets `( )` are required | `print "Hi"` is an error in Python 3 |

### Printing several things at once
Separate them with commas. Python puts **one space** between them.
```python
print("Age:", 19)
print("Name:", "Karan", "City:", "Pune")
# Output: Age: 19
# Output: Name: Karan City: Pune
```

### Comments: notes for humans
Python **ignores** everything after `#`. Use comments to explain your code.
```python
# This is a comment on its own line
print("Hi")    # this is a comment after code
# Output: Hi
```
- There is no special multi-line comment. Put `#` at the start of each line. (Triple quotes `"""..."""` make a **string**, not a comment, even though you will sometimes see them used like comments.)
- Comments are not printed and do not affect the program.

---

- [Back to section index](./README.md)
