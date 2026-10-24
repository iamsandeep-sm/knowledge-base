# Numbers

### The three number types

```python
a = 25            # int      whole number
b = 3.14          # float    has a decimal point
c = 2 + 3j        # complex  real part + imaginary part (j)

print(type(a), type(b), type(c))
# Output: <class 'int'> <class 'float'> <class 'complex'>
```

| Type | Notes |
|---|---|
| `int` | Can be **any size**: `2 ** 100` works with no overflow |
| `float` | Has a decimal point (`5.0` is a float, `5` is an int) or uses e-notation like `1e3` |
| `complex` | Rarely needed in beginner programs |

### Useful number facts
```python
print(2 ** 100)         # 1267650600228229401496703205376   (ints can be huge)
print(1_000_000)        # 1000000    underscores make big numbers readable
print(1e3)              # 1000.0     scientific notation gives a float
print(type(5.0))        # <class 'float'>
print(0.1 + 0.2)        # 0.30000000000000004
```
> **Why is `0.1 + 0.2` not exactly `0.3`?** Computers store decimals in binary, so many decimal numbers are only stored **approximately**. This happens in every programming language. Use `round()` when printing, and never compare floats with `==`.

### Complex numbers (quick look)
```python
z = 3 + 4j
print(z.real)      # 3.0
print(z.imag)      # 4.0
print(abs(z))      # 5.0
```

### Handy built-in functions for numbers
| Function | What it does | Example | Result |
|---|---|---|---|
| `abs(x)` | Positive value | `abs(-5)` | `5` |
| `round(x)` | Nearest whole number | `round(3.7)` | `4` |
| `round(x, n)` | Round to `n` decimals | `round(3.14159, 2)` | `3.14` |
| `pow(a, b)` | `a` to the power `b` | `pow(2, 3)` | `8` |
| `min(...)` | Smallest | `min(4, 9, 2)` | `2` |
| `max(...)` | Largest | `max(4, 9, 2)` | `9` |
| `divmod(a, b)` | Quotient and remainder | `divmod(17, 5)` | `(3, 2)` |

```python
print(abs(-5), round(3.7), round(3.14159, 2), pow(2, 3), min(4, 9, 2), max(4, 9, 2))
# Output: 5 4 3.14 8 2 9
print(divmod(17, 5))
# Output: (3, 2)
```
> `round()` rounds exactly halves to the nearest **even** number: `round(2.5)` is `2` and `round(3.5)` is `4`. This is normal Python behaviour.

### The `math` module
Python has extra tools you can bring in with `import`.
```python
import math

print(math.sqrt(16))    # 4.0
print(math.floor(3.7))  # 3     round down
print(math.ceil(3.2))   # 4     round up
print(math.pi)          # 3.141592653589793
```

---

- [Back to section index](./README.md)
