# Formatting Output

### Control how `print()` ends and separates
```python
print("A", "B", "C", sep="-")
print("Hello", end=" ")
print("World")
print()                      # prints an empty line
print("Done")
# Output: A-B-C
# Output: Hello World
# Output:
# Output: Done
```

| Option | Meaning | Default |
|---|---|---|
| `sep=` | What goes **between** the items | one space |
| `end=` | What goes at the **end** | a new line `\n` |

### f-strings (the best way, Python 3.6+)
Put an `f` before the quote and write variables **inside `{ }`**.
```python
name = "Karan"
age = 19
print(f"My name is {name} and I am {age} years old")
print(f"Next year I will be {age + 1}")
print(f"{name.upper()} has {len(name)} letters")
# Output: My name is Karan and I am 19 years old
# Output: Next year I will be 20
# Output: KARAN has 5 letters
```
You can put **any expression** inside the braces.

### Formatting numbers inside f-strings
Syntax: `{value:format}`
```python
pi = 3.14159265
price = 1234567.891
score = 0.256
n = 7

print(f"{pi:.2f}")          # 3.14          2 decimal places
print(f"{price:,.2f}")      # 1,234,567.89  commas as thousand separators
print(f"{score:.1%}")       # 25.6%         percentage
print(f"{n:03}")            # 007           pad with zeros to width 3
print(f"|{'hi':>6}|")       # |    hi|      right-align in a width of 6
print(f"|{'hi':<6}|")       # |hi    |      left-align
print(f"|{'hi':^6}|")       # |  hi  |      centre
```
(The `|` bars are only there so you can see the spaces.)

| Format | Meaning | Example | Result |
|---|---|---|---|
| `.2f` | 2 decimal places | `f"{3.14159:.2f}"` | `3.14` |
| `,` | Thousand separator | `f"{1000000:,}"` | `1,000,000` |
| `.1%` | Percentage | `f"{0.256:.1%}"` | `25.6%` |
| `05` | Zero-pad to width 5 | `f"{42:05}"` | `00042` |
| `>8` | Right-align in width 8 | `f"{'a':>8}"` | `       a` |

### Quick debugging with `=` (Python 3.8+)
```python
x = 5
print(f"{x=}")       # x=5
```

### Older ways (you will see them in old code)
```python
name = "Karan"
age = 19
print("Name: {} Age: {}".format(name, age))     # str.format()
print("Name: %s Age: %d" % (name, age))         # % formatting
# Output: Name: Karan Age: 19
# Output: Name: Karan Age: 19
```
Prefer **f-strings** in new code.

### Printing special text
```python
print("Hello\nWorld")           # two lines
print('It\'s fine')             # escape a quote
print("It's fine")              # or use the other type of quote
print(r"C:\new\table")          # r"..." raw string: backslashes are kept as they are
```

---

- [Back to section index](./README.md)
