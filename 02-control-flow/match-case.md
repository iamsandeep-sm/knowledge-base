# Match Case

Python 3.10+ has `match` / `case` ("structural pattern matching"), like `switch` in other languages. It is a cleaner alternative to a long `if / elif` chain that compares **one value** against many options.

```python
day = 3

match day:
    case 1:
        print("Monday")
    case 2:
        print("Tuesday")
    case 3:
        print("Wednesday")
    case _:
        print("Other day")
# Output: Wednesday
```

- `case _:` is the **default** (like `else`); it matches anything.
- Only the **first** matching case runs. There is no fall-through, so no `break` is needed.

### Match several values with `|`
```python
day = "Sat"

match day:
    case "Sat" | "Sun":
        print("Weekend")
    case _:
        print("Weekday")
# Output: Weekend
```

### Add a condition (guard)
```python
n = 15

match n:
    case x if x < 0:
        print("Negative")
    case 0:
        print("Zero")
    case x if x < 10:
        print("Small")
    case _:
        print("Large")
# Output: Large
```
> `match` needs **Python 3.10 or newer**. On older versions it is a `SyntaxError`, so use `if / elif / else`.

### Gotcha: a plain name in `case` does NOT compare with a variable
A bare name in a `case` is a **capture pattern**: it matches **anything** and stores the value in that name.
```python
RED = "red"
color = "blue"

match color:
    case RED:                 # does NOT mean color == RED; it matches ANY value and assigns it to RED
        print("matched the first case")
# Output: matched the first case
```
To compare with a constant, use a literal (`case "red":`) or a dotted name such as `Color.RED`.

---

- [Back to section index](./README.md)
