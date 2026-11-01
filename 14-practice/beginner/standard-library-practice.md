# Standard Library Practice

Related notes: [math](../../11-useful-standard-library/math.md), [random](../../11-useful-standard-library/random.md), [datetime](../../11-useful-standard-library/datetime.md), [pathlib](../../11-useful-standard-library/pathlib.md), [collections](../../11-useful-standard-library/collections.md), [itertools](../../11-useful-standard-library/itertools.md)

## Table of Contents
- [Practice Questions](#practice-questions)
- [Practice Answers](#practice-answers)

---

## Practice Questions

1. Find the area of a circle with radius `3`, rounded to 2 decimals.
2. What do `ceil`, `floor`, `trunc` and `round` give for `-3.5`?
3. You have `47` items and each page holds `10`. How many pages do you need?
4. Find the GCD and the LCM of `24` and `36`.
5. Why is `0.1 + 0.2 == 0.3` false? Compare them correctly.
6. In how many ways can you choose `3` people from `10`? In how many ways can you arrange `3` of the `10` in a row?
7. Find the distance between the points `(1, 2)` and `(4, 6)`.
8. Find the mean, median and mode of `[3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5]`.
9. Simulate rolling two dice. What are the smallest and largest possible totals?
10. How do you make `random` give the same numbers on every run?
11. Pick `3` different names from a list of 10 names.
12. What does `random.shuffle(items)` return?
13. Make a random 20-character hexadecimal token that is safe to use for a password reset.
14. Pick `"win"` about 10% of the time and `"lose"` about 90% of the time.
15. Show the date `2026-11-01` as `01 Nov 2026 (Sunday)`.
16. Read the text `"15-08-2026"` and print the weekday of that date.
17. How many days are there from `2026-11-01` to `2027-01-01`?
18. What is the date 100 days after `2026-11-01`?
19. Write `age(born, today)` that returns the age in whole years.
20. Convert `12:00` UTC on `2026-01-15` to Indian time.
21. For `Path("/home/riya/report.final.pdf")`, find the name, stem, suffix and parent folder.
22. Create a folder `notes`, write two `.txt` files and one `.md` file in it, and list only the `.txt` files in order.
23. Find the two most common words in `"to be or not to be that is the question"`.
24. Group the names `["Riya", "Rahul", "Amit", "Anil", "Zoya"]` by their first letter.
25. Keep only the last `3` numbers of a stream of numbers `1` to `6`.
26. Make a `Point` with `x` and `y`, then make a copy with `y` changed.
27. Give the running total of `[5, 10, 15, 20]`.
28. Flatten `[[1, 2], [3], [4, 5]]` without a loop of your own.
29. How many different 3-letter groups can you make from `"abcd"` if the order does not matter? List them.
30. List all two-digit codes made from the digits `1`, `2` and `3`, repeats allowed.
31. Compress `"aaabccdddd"` into `"a3b1c2d4"`.

---

## Practice Answers

Try each question yourself first, then compare.

**Answer 1**
```python
import math

print(round(math.pi * 3 ** 2, 2))
# Output: 28.27
```

**Answer 2**
```python
import math

x = -3.5
print(math.ceil(x), math.floor(x), math.trunc(x), round(x))
# Output: -3 -4 -3 -4
```
`ceil` goes up, `floor` goes down, `trunc` cuts the decimals, and `round` sends the exact half to the nearest **even** number (`-4`).

**Answer 3**
```python
import math

print(math.ceil(47 / 10))
# Output: 5
```

**Answer 4**
```python
import math

print(math.gcd(24, 36), math.lcm(24, 36))
# Output: 12 72
```

**Answer 5**

Floats are stored in binary, and `0.1` and `0.2` cannot be stored exactly, so the sum is `0.30000000000000004`. Compare with a tolerance:
```python
import math

print(0.1 + 0.2 == 0.3)
print(math.isclose(0.1 + 0.2, 0.3))
# Output: False
# Output: True
```

**Answer 6**
```python
import math

print(math.comb(10, 3), math.perm(10, 3))
# Output: 120 720
```
Choosing (order does not matter) is `comb`, arranging (order matters) is `perm`.

**Answer 7**
```python
import math

print(math.dist((1, 2), (4, 6)))
# Output: 5.0
```

**Answer 8**
```python
import statistics

data = [3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5]
print(statistics.mean(data), statistics.median(data), statistics.mode(data))
# Output: 4 4 5
```

**Answer 9**
```python
import random

total = random.randint(1, 6) + random.randint(1, 6)
print(2 <= total <= 12)
# Output: True
```
The smallest total is `2` (1 + 1) and the largest is `12` (6 + 6). The exact total changes on every run.

**Answer 10**

Call `random.seed(some_number)` once at the start.
```python
import random

random.seed(5)
first = [random.randint(1, 10) for _ in range(4)]
random.seed(5)
second = [random.randint(1, 10) for _ in range(4)]
print(first == second)
# Output: True
```

**Answer 11**
```python
import random

names = ["Riya", "Amit", "Zoya", "Kabir", "Meera", "Neel", "Isha", "Dev", "Tara", "Om"]
picked = random.sample(names, 3)
print(len(picked), len(set(picked)))
# Output: 3 3
```
`sample` never picks the same item twice. `choices` can.

**Answer 12**
```python
import random

items = [1, 2, 3]
print(random.shuffle(items))
print(sorted(items))
# Output: None
# Output: [1, 2, 3]
```
It shuffles the list **in place** and returns `None`.

**Answer 13**
```python
import secrets

token = secrets.token_hex(10)
print(len(token))
# Output: 20
```
`secrets` uses a secure source. `random` is predictable and must not be used for passwords or tokens.

**Answer 14**
```python
import random

random.seed(3)
results = random.choices(["win", "lose"], weights=[10, 90], k=1000)
print(results.count("lose") > results.count("win"))
# Output: True
```

**Answer 15**
```python
from datetime import date

print(date(2026, 11, 1).strftime("%d %b %Y (%A)"))
# Output: 01 Nov 2026 (Sunday)
```

**Answer 16**
```python
from datetime import datetime

d = datetime.strptime("15-08-2026", "%d-%m-%Y")
print(d.strftime("%A"))
# Output: Saturday
```

**Answer 17**
```python
from datetime import date

print((date(2027, 1, 1) - date(2026, 11, 1)).days)
# Output: 61
```

**Answer 18**
```python
from datetime import date, timedelta

print(date(2026, 11, 1) + timedelta(days=100))
# Output: 2027-02-09
```

**Answer 19**
```python
from datetime import date

def age(born, today):
    years = today.year - born.year
    if (today.month, today.day) < (born.month, born.day):
        years -= 1
    return years

print(age(date(2005, 12, 15), date(2026, 11, 1)))
print(age(date(2005, 11, 1), date(2026, 11, 1)))
# Output: 20
# Output: 21
```
If the birthday has not happened yet this year, subtract one.

**Answer 20**
```python
from datetime import datetime
from zoneinfo import ZoneInfo

utc_time = datetime(2026, 1, 15, 12, 0, tzinfo=ZoneInfo("UTC"))
print(utc_time.astimezone(ZoneInfo("Asia/Kolkata")))
# Output: 2026-01-15 17:30:00+05:30
```

**Answer 21**
```python
from pathlib import Path

p = Path("/home/riya/report.final.pdf")
print(p.name)
print(p.stem)
print(p.suffix)
print(p.parent)
# Output: report.final.pdf
# Output: report.final
# Output: .pdf
# Output: /home/riya
```

**Answer 22**
```python
import shutil
from pathlib import Path

folder = Path("notes")
folder.mkdir(exist_ok=True)
(folder / "b.txt").write_text("b", encoding="utf-8")
(folder / "a.txt").write_text("a", encoding="utf-8")
(folder / "c.md").write_text("c", encoding="utf-8")

print([p.name for p in sorted(folder.glob("*.txt"))])

shutil.rmtree(folder)
# Output: ['a.txt', 'b.txt']
```
`glob` gives no guaranteed order, so use `sorted()`. The last line removes the demo folder.

**Answer 23**
```python
from collections import Counter

text = "to be or not to be that is the question"
print(Counter(text.split()).most_common(2))
# Output: [('to', 2), ('be', 2)]
```

**Answer 24**
```python
from collections import defaultdict

groups = defaultdict(list)
for name in ["Riya", "Rahul", "Amit", "Anil", "Zoya"]:
    groups[name[0]].append(name)
print(dict(groups))
# Output: {'R': ['Riya', 'Rahul'], 'A': ['Amit', 'Anil'], 'Z': ['Zoya']}
```

**Answer 25**
```python
from collections import deque

last = deque(maxlen=3)
for n in range(1, 7):
    last.append(n)
print(list(last))
# Output: [4, 5, 6]
```

**Answer 26**
```python
from collections import namedtuple

Point = namedtuple("Point", "x y")
p = Point(1, 2)
q = p._replace(y=9)
print(p, q)
# Output: Point(x=1, y=2) Point(x=1, y=9)
```
A `namedtuple` is immutable, so `_replace` makes a new one.

**Answer 27**
```python
from itertools import accumulate

print(list(accumulate([5, 10, 15, 20])))
# Output: [5, 15, 30, 50]
```

**Answer 28**
```python
from itertools import chain

print(list(chain.from_iterable([[1, 2], [3], [4, 5]])))
# Output: [1, 2, 3, 4, 5]
```

**Answer 29**
```python
from itertools import combinations

groups = list(combinations("abcd", 3))
print(len(groups))
print(["".join(g) for g in groups])
# Output: 4
# Output: ['abc', 'abd', 'acd', 'bcd']
```
Order does not matter, so use `combinations` (with `permutations` there would be 24).

**Answer 30**
```python
from itertools import product

codes = ["".join(p) for p in product("123", repeat=2)]
print(codes)
# Output: ['11', '12', '13', '21', '22', '23', '31', '32', '33']
```

**Answer 31**
```python
from itertools import groupby

text = "aaabccdddd"
print("".join(f"{ch}{len(list(group))}" for ch, group in groupby(text)))
# Output: a3b1c2d4
```
`groupby` groups **neighbouring** equal items.

---

- [Back to practice index](../README.md)
