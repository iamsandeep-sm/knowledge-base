# random - Revision Notes ~ sm

> **In one line:** The **`random`** module produces **pseudo-random** numbers and makes random choices: dice rolls, shuffling, picking a winner, test data. It is **not secure** (use `secrets` for passwords and tokens).

## Table of Contents
- [Importing random](#importing-random)
- [Random Numbers](#random-numbers)
- [Random Choices from a Sequence](#random-choices-from-a-sequence)
- [Shuffling](#shuffling)
- [Seeds: Repeatable Randomness](#seeds-repeatable-randomness)
- [Random Generator Objects](#random-generator-objects)
- [Weighted Choices](#weighted-choices)
- [Other Distributions](#other-distributions)
- [secrets: Randomness for Security](#secrets-randomness-for-security)
- [Practical Examples](#practical-examples)
- [Common Mistakes](#common-mistakes)

---

## Importing random

```python
import random

n = random.randint(1, 6)
print(1 <= n <= 6)
# Output: True
```
Real random values are different every run, so the examples in this note print **properties** of the results (such as "is the value between 1 and 6") instead of the values themselves.

"Pseudo-random" means the numbers come from a formula (the Mersenne Twister algorithm) that starts from a **seed**. They look random but are fully determined by the seed.

---

## Random Numbers

| Function | Gives | Range |
|---|---|---|
| `random.random()` | A `float` | `0.0` up to but **not including** `1.0` |
| `random.uniform(a, b)` | A `float` | between `a` and `b` |
| `random.randint(a, b)` | An `int` | `a` to `b`, **both included** |
| `random.randrange(stop)` | An `int` | `0` to `stop - 1` |
| `random.randrange(start, stop, step)` | An `int` | like `range()`, `stop` **not** included |
| `random.getrandbits(k)` | An `int` | `k` random bits |

```python
import random

print(0.0 <= random.random() < 1.0)
print(2.5 <= random.uniform(2.5, 7.5) <= 7.5)
print(random.randint(1, 6) in {1, 2, 3, 4, 5, 6})
print(random.randrange(10) in range(10))
print(random.randrange(0, 100, 10) % 10 == 0)
# Output: True
# Output: True
# Output: True
# Output: True
# Output: True
```
The classic trap: **`randint` includes both ends, `randrange` does not**. `random.randint(1, 6)` is a die, `random.randrange(1, 6)` can never give 6.

Scaling `random()` yourself: `a + (b - a) * random.random()` is what `uniform(a, b)` does.

---

## Random Choices from a Sequence

```python
import random

colors = ["red", "green", "blue"]

print(random.choice(colors) in colors)
print(len(random.sample(colors, 2)))
print(len(random.choices(colors, k=5)))
# Output: True
# Output: 2
# Output: 5
```
| Function | Picks | Same item twice? | Returns |
|---|---|---|---|
| `random.choice(seq)` | **One** item | n/a | the item |
| `random.sample(seq, k)` | `k` items | **No** (without replacement) | new list |
| `random.choices(seq, k=n)` | `n` items | **Yes** (with replacement) | new list |

`sample()` is for "draw 5 different winners". `choices()` is for "roll a die 5 times".
```python
import random

winners = random.sample(range(1, 50), 6)
print(len(winners), len(set(winners)))
# Output: 6 6
```
Asking for more items than exist fails:
```python
import random

random.sample([1, 2, 3], 5)
# Error: ValueError: Sample larger than population or is negative
```
`choice([])` on an empty sequence raises `IndexError`. `sample()` needs a **sequence** (a list, tuple, string or `range`); a `set` is not accepted in current Python versions (convert it with `list()` or `sorted()`).

---

## Shuffling

`random.shuffle(list)` mixes a list **in place** and returns `None`.

```python
import random

cards = [1, 2, 3, 4, 5]
result = random.shuffle(cards)
print(result)
print(sorted(cards))
# Output: None
# Output: [1, 2, 3, 4, 5]
```
To keep the original order, shuffle a copy, or use `sample()` on the whole list:
```python
import random

original = [1, 2, 3, 4, 5]
mixed = random.sample(original, k=len(original))
print(original)
print(sorted(mixed))
# Output: [1, 2, 3, 4, 5]
# Output: [1, 2, 3, 4, 5]
```
Only **mutable** sequences can be shuffled in place. A string or tuple raises `TypeError`; convert first: `random.sample(text, len(text))` gives a list of shuffled characters.

---

## Seeds: Repeatable Randomness

`random.seed(value)` starts the generator from a known point. The same seed gives the same sequence.

```python
import random

random.seed(42)
first = [random.randint(1, 100) for _ in range(5)]

random.seed(42)
second = [random.randint(1, 100) for _ in range(5)]

print(first == second)
# Output: True
```
Python guarantees that `random.random()` after `random.seed(42)` gives the same value on every version and computer:
```python
import random

random.seed(42)
print(random.random())
# Output: 0.6394267984578837
```
Use seeds for **repeatable tests**, simulations and teaching. Do not rely on the exact numbers `randint`, `choice`, `shuffle` and `sample` produce being identical across different Python versions; only repeat the same results on the same version.

If you never call `seed()`, Python seeds from the operating system (and the clock), so each run differs.

---

## Random Generator Objects

The functions above share **one hidden generator**. A `random.Random(seed)` object is a separate generator with its own state, useful when different parts of a program need independent, repeatable sequences.

```python
import random

a = random.Random(1)
b = random.Random(1)

random.seed(99)          # the shared generator is separate ...
random.random()          # ... so using it does not disturb a or b
print(a.randint(1, 100) == b.randint(1, 100))
# Output: True
```
All the functions (`randint`, `choice`, `shuffle`, ...) exist as methods of a `Random` object.

---

## Weighted Choices

`choices()` accepts `weights`: bigger weight, more likely.

```python
import random

random.seed(7)
picks = random.choices(["win", "lose"], weights=[1, 9], k=1000)
print(picks.count("lose") > picks.count("win"))
# Output: True
```
Weights are **relative** (`[1, 9]` is the same as `[10, 90]`). `cum_weights=` accepts running totals instead.

---

## Other Distributions

| Function | Distribution |
|---|---|
| `random.gauss(mu, sigma)` | Normal ("bell curve"), average `mu`, spread `sigma` |
| `random.triangular(low, high, mode)` | Triangle shape |
| `random.expovariate(lambd)` | Exponential |
| `random.betavariate(a, b)` | Beta |

```python
import random

random.seed(1)
heights = [random.gauss(170, 10) for _ in range(1000)]
average = sum(heights) / len(heights)
print(160 < average < 180)
# Output: True
```

---

## secrets: Randomness for Security

`random` is **predictable**: someone who sees enough outputs can work out the rest. For **passwords, tokens, reset links and keys**, use the `secrets` module, which uses the operating system's secure source.

```python
import secrets
import string

token = secrets.token_hex(16)
print(len(token))

alphabet = string.ascii_letters + string.digits
password = "".join(secrets.choice(alphabet) for _ in range(12))
print(len(password))
print(secrets.randbelow(10) in range(10))
# Output: 32
# Output: 12
# Output: True
```
| Function | Gives |
|---|---|
| `secrets.token_hex(n)` | A string of `2 * n` hex characters from `n` random bytes |
| `secrets.token_urlsafe(n)` | A URL-safe text token |
| `secrets.choice(seq)` | A secure `random.choice` |
| `secrets.randbelow(n)` | An `int` from `0` to `n - 1` |

You **cannot** seed `secrets` and get repeatable results, which is exactly the point.

---

## Practical Examples

**Dice roller**
```python
import random

def roll(dice=2, sides=6):
    return [random.randint(1, sides) for _ in range(dice)]

rolls = roll()
print(len(rolls), all(1 <= r <= 6 for r in rolls))
# Output: 2 True
```

**Coin flip counts**
```python
import random

flips = [random.choice(["heads", "tails"]) for _ in range(100)]
print(flips.count("heads") + flips.count("tails"))
# Output: 100
```

**Shuffle and deal a deck**
```python
import random

suits = ["S", "H", "D", "C"]
ranks = ["A", "2", "3", "4", "5", "6", "7", "8", "9", "10", "J", "Q", "K"]
deck = [rank + suit for suit in suits for rank in ranks]
random.shuffle(deck)
hand = deck[:5]
print(len(deck), len(hand), len(set(deck)))
# Output: 52 5 52
```

**Number guessing game (checking a guess)**
```python
import random

def check(guess, secret):
    if guess == secret:
        return "Correct"
    return "Too low" if guess < secret else "Too high"

secret = random.randint(1, 20)
print(check(10, secret) in {"Correct", "Too low", "Too high"})
print(check(10, 10), check(3, 10), check(15, 10))
# Output: True
# Output: Correct Too low Too high
```
Keeping the checking in a function that takes the secret as an argument makes it easy to test: the random part is outside.

**Random item with a fixed seed, for a repeatable test**
```python
import random

random.seed(0)
names = ["Riya", "Amit", "Zoya"]
winner = random.choice(names)

random.seed(0)
print(winner == random.choice(names))
# Output: True
```

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Naming your file `random.py` | `import random` loads your file, not the module | Rename the file (see Modules and Packages) |
| `random.randrange(1, 6)` expecting 6 to be possible | Never gives 6 | `random.randint(1, 6)` |
| `x = random.shuffle(items)` | `x` is `None`; the list was shuffled in place | `random.shuffle(items)`, then use `items` |
| Shuffling a tuple or string | `TypeError` | Convert to a list first |
| `random.sample(items, k)` with `k` too big | `ValueError` | `k <= len(items)`, or use `choices` |
| `random.choice([])` | `IndexError` | Check the sequence is not empty |
| Using `random` for passwords or tokens | Guessable | Use `secrets` |
| Expecting the same numbers without a seed | Different every run | `random.seed(n)` for repeatability |
| Expecting `random.random()` to reach `1.0` | It is always less than `1.0` | `uniform(a, b)` may reach `b` (rounding), `random()` never reaches `1.0` |
| Calling `random.seed()` inside a loop | Same value every time | Seed **once** at the start |
| `from random import *` | Names such as `sample` or `choice` can clash with yours | `import random` |

---

- [Back to section index](./README.md)
