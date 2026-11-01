# math - Revision Notes ~ sm

> **In one line:** The **`math`** module is Python's built-in toolbox for **mathematical functions and constants**: square roots, rounding up and down, powers, logarithms, trigonometry, factorials and more. It works with real numbers (`int` and `float`). For complex numbers use `cmath`.

## Table of Contents
- [Importing math](#importing-math)
- [Constants](#constants)
- [Square Roots and Powers](#square-roots-and-powers)
- [Rounding: ceil, floor, trunc and round](#rounding-ceil-floor-trunc-and-round)
- [Integer Maths: factorial, gcd, lcm, comb, perm](#integer-maths-factorial-gcd-lcm-comb-perm)
- [Logarithms and Exponents](#logarithms-and-exponents)
- [Trigonometry](#trigonometry)
- [Distance: hypot and dist](#distance-hypot-and-dist)
- [Sums and Products](#sums-and-products)
- [Comparing Floats: isclose](#comparing-floats-isclose)
- [inf and nan](#inf-and-nan)
- [Errors](#errors)
- [Related Modules: statistics, decimal, fractions](#related-modules-statistics-decimal-fractions)
- [Common Mistakes](#common-mistakes)

---

## Importing math

```python
import math

print(math.sqrt(25))
# Output: 5.0
```
`math` is part of the standard library, so there is nothing to install. See the Modules and Packages note for the different ways to import. Use `dir(math)` to list everything, and `help(math.sqrt)` for the documentation of one function.

---

## Constants

```python
import math

print(math.pi)
print(math.e)
print(math.tau)
print(math.inf)
# Output: 3.141592653589793
# Output: 2.718281828459045
# Output: 6.283185307179586
# Output: inf
```
| Constant | Meaning |
|---|---|
| `math.pi` | π, the ratio of a circle's circumference to its diameter |
| `math.e` | Euler's number, the base of natural logarithms |
| `math.tau` | 2π, one full turn |
| `math.inf` | Positive infinity (`-math.inf` is negative infinity) |
| `math.nan` | "Not a number" |

Example: area and circumference of a circle.
```python
import math

radius = 5
print(round(math.pi * radius ** 2, 2))
print(round(2 * math.pi * radius, 2))
# Output: 78.54
# Output: 31.42
```

---

## Square Roots and Powers

```python
import math

print(math.sqrt(16))
print(math.isqrt(17))
print(math.pow(2, 3))
print(2 ** 3)
print(math.cbrt(8))
# Output: 4.0
# Output: 4
# Output: 8.0
# Output: 8
# Output: 2.0
```
| Function | Result |
|---|---|
| `math.sqrt(x)` | Square root, always a `float` |
| `math.isqrt(n)` | **Integer** square root: the largest whole number whose square is not more than `n` (exact, even for huge numbers) |
| `math.pow(x, y)` | `x` to the power `y`, always a `float` |
| `x ** y` | Power; stays an `int` for whole numbers |
| `math.cbrt(x)` | Cube root (Python 3.11+). The result can differ in the last digit between platforms, so round it when you print |

`**` and `pow()` (the built-in) are usually better than `math.pow()`, because they keep integers as integers. A square root with `** 0.5` also works: `16 ** 0.5` gives `4.0`.

---

## Rounding: ceil, floor, trunc and round

| Function | Does | `2.7` | `-2.7` |
|---|---|---|---|
| `math.ceil(x)` | Round **up** (toward +infinity) | 3 | -2 |
| `math.floor(x)` | Round **down** (toward -infinity) | 2 | -3 |
| `math.trunc(x)` | **Cut off** the decimals (toward zero) | 2 | -2 |
| `round(x)` | Nearest whole number | 3 | -3 |

```python
import math

for x in (2.7, -2.7):
    print(x, math.ceil(x), math.floor(x), math.trunc(x), round(x))
# Output: 2.7 3 2 2 3
# Output: -2.7 -2 -3 -2 -3
```
All of them return an `int` (when you pass no second argument).

**Rounding exactly half.** Python's `round()` rounds halves to the **nearest even** number ("banker's rounding"):
```python
print(round(0.5), round(1.5), round(2.5), round(3.5))
# Output: 0 2 2 4
```

**Practical use of `ceil`:** how many pages or boxes do you need?
```python
import math

items = 25
per_page = 10
print(math.ceil(items / per_page))
# Output: 3
```
Integer-only alternative: `-(-items // per_page)`.

---

## Integer Maths: factorial, gcd, lcm, comb, perm

```python
import math

print(math.factorial(5))
print(math.gcd(12, 18))
print(math.lcm(4, 6))
print(math.comb(5, 2))
print(math.perm(5, 2))
print(math.prod([1, 2, 3, 4]))
# Output: 120
# Output: 6
# Output: 12
# Output: 10
# Output: 20
# Output: 24
```
| Function | Meaning | Python |
|---|---|---|
| `factorial(n)` | n × (n-1) × ... × 1 | all |
| `gcd(a, b)` | Greatest common divisor | all (many arguments from 3.9) |
| `lcm(a, b)` | Least common multiple | 3.9+ |
| `comb(n, k)` | Ways to **choose** `k` of `n`, order **does not** matter | 3.8+ |
| `perm(n, k)` | Ways to **arrange** `k` of `n`, order **matters** | 3.8+ |
| `prod(items)` | Product of all items | 3.8+ |

`factorial` uses exact integers, so big results are correct:
```python
import math

print(math.factorial(20))
print(len(str(math.factorial(100))))
# Output: 2432902008176640000
# Output: 158
```

---

## Logarithms and Exponents

```python
import math

print(math.log(math.e))
print(math.log(8, 2))
print(math.log2(8))
print(math.log10(1000))
print(math.exp(0))
# Output: 1.0
# Output: 3.0
# Output: 3.0
# Output: 3.0
# Output: 1.0
```
| Function | Meaning |
|---|---|
| `math.log(x)` | Natural logarithm (base `e`) |
| `math.log(x, base)` | Logarithm to any base |
| `math.log2(x)` / `math.log10(x)` | Base 2 / base 10 (more accurate than `log(x, 2)`) |
| `math.exp(x)` | `e` to the power `x` |

Use: "how many digits does a number have?" and "how many times can I halve this?".
```python
import math

n = 123456
print(math.floor(math.log10(n)) + 1)
print(math.floor(math.log2(1000)))
# Output: 6
# Output: 9
```

---

## Trigonometry

Trigonometric functions work in **radians**, not degrees. Convert with `math.radians()` and `math.degrees()`.

```python
import math

print(math.degrees(math.pi))
print(math.radians(180))
print(math.sin(math.radians(90)))
print(math.cos(math.radians(60)))
print(math.atan2(1, 1) == math.pi / 4)
# Output: 180.0
# Output: 3.141592653589793
# Output: 1.0
# Output: 0.5000000000000001
# Output: True
```
Note `cos(60°)` is `0.5000000000000001`, not exactly `0.5`: a floating-point rounding effect. Round the result, or compare with `math.isclose`.

Common functions: `sin`, `cos`, `tan`, `asin`, `acos`, `atan`, `atan2(y, x)`, `sinh`, `cosh`, `tanh`.

---

## Distance: hypot and dist

```python
import math

print(math.hypot(3, 4))
print(math.dist((0, 0), (3, 4)))
print(math.dist((1, 1, 1), (4, 5, 1)))
# Output: 5.0
# Output: 5.0
# Output: 5.0
```
`hypot(a, b)` is the length of the hypotenuse, `sqrt(a*a + b*b)`, calculated safely. `dist(p, q)` is the straight-line distance between two points of any dimension (Python 3.8+).

---

## Sums and Products

Adding many floats one by one collects tiny rounding errors.

```python
import math

total = 0.0
for _ in range(10):
    total += 0.1
print(total)
print(math.fsum([0.1] * 10))
# Output: 0.9999999999999999
# Output: 1.0
```
`math.fsum()` tracks the lost digits, so it is **always accurate**. The built-in `sum()` was less accurate in older versions; since Python **3.12** `sum()` also compensates for float errors, so `sum([0.1] * 10)` gives `1.0` there. `fsum` is the safe choice if your code must work the same on every version.

For money, avoid floats altogether and use the `decimal` module (see below).

`math.prod()` multiplies:
```python
import math

print(math.prod([2, 3, 4]))
print(math.prod([]))
# Output: 24
# Output: 1
```

---

## Comparing Floats: isclose

Floats cannot store most decimals exactly, so never compare them with `==`.

```python
import math

print(0.1 + 0.2 == 0.3)
print(math.isclose(0.1 + 0.2, 0.3))
print(math.isclose(100.0, 100.4, abs_tol=0.5))
# Output: False
# Output: True
# Output: True
```
`isclose(a, b)` uses a relative tolerance of `1e-09` by default. Use `abs_tol=` when one of the values can be `0`:
```python
import math

print(math.isclose(0, 1e-12))
print(math.isclose(0, 1e-12, abs_tol=1e-9))
# Output: False
# Output: True
```

---

## inf and nan

```python
import math

print(math.inf > 10 ** 100)
print(-math.inf < 0)
print(math.isinf(float("inf")))

nan = float("nan")
print(nan == nan)
print(math.isnan(nan))
print(math.isfinite(5.0), math.isfinite(math.inf))
# Output: True
# Output: True
# Output: True
# Output: False
# Output: True
# Output: True False
```
- `inf` is handy as a **starting value** when looking for a minimum: `lowest = math.inf`.
- `nan` is **not equal to anything, even itself**. Test it with `math.isnan()`.

```python
import math

prices = [30, 12, 45]
lowest = math.inf
for p in prices:
    if p < lowest:
        lowest = p
print(lowest)
# Output: 12
```

---

## Errors

`math` functions raise `ValueError` when the input is outside what the function allows, and `TypeError` for the wrong type.

```python
import math

math.sqrt(-1)
# Error: ValueError: math domain error
```
```python
import math

math.factorial(-3)
# Error: ValueError: factorial() not defined for negative values
```
```python
import math

math.sqrt("9")
# Error: TypeError: must be real number, not str
```
Handle them like any exception:
```python
import math

def safe_sqrt(x):
    try:
        return math.sqrt(x)
    except ValueError:
        return None

print(safe_sqrt(9), safe_sqrt(-9))
# Output: 3.0 None
```
(The exact wording of the messages can change between Python versions.) To take the square root of a negative number you need complex numbers: `import cmath; cmath.sqrt(-1)` gives `1j`.

---

## Related Modules: statistics, decimal, fractions

**`statistics`: mean, median, mode, spread**
```python
import statistics

marks = [2, 4, 4, 4, 5, 5, 7, 9]
print(statistics.mean(marks))
print(statistics.median([5, 1, 3]))
print(statistics.median([1, 2, 3, 4]))
print(statistics.mode([1, 2, 2, 3]))
print(statistics.pstdev(marks))
print(round(statistics.stdev(marks), 2))
# Output: 5
# Output: 3
# Output: 2.5
# Output: 2
# Output: 2.0
# Output: 2.14
```
`pstdev` is the standard deviation of a whole population, `stdev` of a sample.

**`decimal`: exact decimal arithmetic (money)**
```python
from decimal import Decimal

print(0.1 + 0.2)
print(Decimal("0.1") + Decimal("0.2"))
# Output: 0.30000000000000004
# Output: 0.3
```
Always build a `Decimal` from a **string**: `Decimal(0.1)` carries the float's error.

**`fractions`: exact fractions**
```python
from fractions import Fraction

print(Fraction(1, 3) + Fraction(1, 6))
print(float(Fraction(3, 4)))
# Output: 1/2
# Output: 0.75
```

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Using `math.sqrt` without importing `math` | `NameError: name 'math' is not defined` | `import math` |
| Naming your own file `math.py` | Your file hides the real module | Rename it (see Modules and Packages) |
| `math.sqrt(-4)` | `ValueError: math domain error` | Check the input first, or use `cmath` |
| `math.sin(90)` expecting degrees | Wrong answer (it is radians) | `math.sin(math.radians(90))` |
| `0.1 + 0.2 == 0.3` | `False` | `math.isclose(...)` or `Decimal` |
| `nan == nan` to detect nan | Always `False` | `math.isnan(x)` |
| Expecting `round(2.5)` to give 3 | It gives 2 (round half to even) | Use `math.floor(x + 0.5)` if you need "half up" |
| `math.factorial(3.5)` | `TypeError` (must be an integer) | Use whole numbers, or `math.gamma` for non-integers |
| Using `math.pow` and expecting an `int` | It always returns a `float` | `2 ** 3` or `pow(2, 3)` |
| Calling `math.log(0)` | `ValueError` | Check that the argument is positive |
| Using `math.floor` for negative numbers as "remove decimals" | `-2.7` becomes `-3` | `math.trunc` or `int()` |

---

- [Back to section index](./README.md)
