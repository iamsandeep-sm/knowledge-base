# Operators

Operators are symbols that **do something with values**.

### Arithmetic operators (maths)

| Operator | Name | Example | Result |
|---|---|---|---|
| `+` | Add | `7 + 2` | `9` |
| `-` | Subtract | `7 - 2` | `5` |
| `*` | Multiply | `7 * 2` | `14` |
| `/` | Divide (**always a float**) | `7 / 2` | `3.5` |
| `//` | Floor divide (whole-number part) | `7 // 2` | `3` |
| `%` | Modulus (remainder) | `7 % 2` | `1` |
| `**` | Power | `2 ** 3` | `8` |

```python
print(7 + 2, 7 - 2, 7 * 2)
print(7 / 2, 7 // 2, 7 % 2, 2 ** 3)
print(10 / 2)
# Output: 9 5 14
# Output: 3.5 3 1 8
# Output: 5.0
```
- `/` always gives a **float**, even when the answer is a whole number (`10 / 2` is `5.0`).
- `//` rounds **down**: `-7 // 2` is `-4`, not `-3`.
- `%` is handy: `n % 2 == 0` means `n` is even.
- Dividing by zero gives `ZeroDivisionError`.

### Order of operations (precedence)
Python follows the same order as maths: **brackets, power, multiply/divide, add/subtract**.
```python
print(2 + 3 * 4)       # 14
print((2 + 3) * 4)     # 20
print(2 ** 3 ** 2)     # 512     power works right to left: 2 ** (3 ** 2)
print(-2 ** 2)         # -4      power happens first, then the minus
print((-2) ** 2)       # 4
```
> When unsure, use **brackets**. They make the order clear to everyone.

### Assignment operators (shortcuts)
Each row below starts again from `x = 10`.
| Operator | Same as | Example (`x = 10`) | New x |
|---|---|---|---|
| `=` | assign | `x = 10` | `10` |
| `+=` | `x = x + n` | `x += 5` | `15` |
| `-=` | `x = x - n` | `x -= 5` | `5` |
| `*=` | `x = x * n` | `x *= 2` | `20` |
| `/=` | `x = x / n` | `x /= 2` | `5.0` |
| `//=` | `x = x // n` | `x //= 3` | `3` |
| `%=` | `x = x % n` | `x %= 3` | `1` |
| `**=` | `x = x ** n` | `x **= 2` | `100` |

```python
x = 10
x += 5
print(x)    # 15
x *= 2
print(x)    # 30
```

### Comparison operators (answer is True or False)
| Operator | Meaning | Example | Result |
|---|---|---|---|
| `==` | Equal to | `5 == 5` | `True` |
| `!=` | Not equal to | `5 != 3` | `True` |
| `>` | Greater than | `5 > 3` | `True` |
| `<` | Less than | `5 < 3` | `False` |
| `>=` | Greater than or equal | `5 >= 5` | `True` |
| `<=` | Less than or equal | `4 <= 3` | `False` |

> `=` **stores** a value. `==` **compares** two values. Do not mix them up.

### Logical operators (combine True / False answers)
| Operator | Meaning | Example | Result |
|---|---|---|---|
| `and` | Both must be true | `True and False` | `False` |
| `or` | At least one is true | `True or False` | `True` |
| `not` | Reverses the answer | `not True` | `False` |

```python
age = 25
print(age > 18 and age < 60)    # True
print(age < 18 or age > 60)     # False
print(not age > 18)             # False
```

### Membership and identity operators
```python
print("a" in "banana")            # True    is "a" inside "banana"?
print(3 not in [1, 2, 3])         # False
x = None
print(x is None)                  # True    is it exactly this object?
print(x is not None)              # False
```

### Operators on strings
```python
print("Hi " + "there")     # Hi there
print("ab" * 3)            # ababab
print("a" < "b")           # True     compared alphabetically
```

---

- [Back to section index](./README.md)
