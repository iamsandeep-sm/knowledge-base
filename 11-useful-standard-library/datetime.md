# datetime - Revision Notes ~ sm

> **In one line:** The **`datetime`** module handles **dates, times and durations**: creating them, formatting them as text, reading them from text, doing arithmetic ("what is the date 30 days from now?") and working with time zones.

## Table of Contents
- [The Main Classes](#the-main-classes)
- [Creating Dates and Times](#creating-dates-and-times)
- [Today and Now](#today-and-now)
- [Reading the Parts](#reading-the-parts)
- [Formatting: strftime](#formatting-strftime)
- [Parsing: strptime](#parsing-strptime)
- [ISO Format](#iso-format)
- [timedelta: Durations and Arithmetic](#timedelta-durations-and-arithmetic)
- [Comparing and Sorting](#comparing-and-sorting)
- [Changing Parts: replace and combine](#changing-parts-replace-and-combine)
- [Weekdays, Months and the calendar Module](#weekdays-months-and-the-calendar-module)
- [Time Zones](#time-zones)
- [The time Module](#the-time-module)
- [Practical Examples](#practical-examples)
- [Common Mistakes](#common-mistakes)

---

## The Main Classes

```python
from datetime import date, time, datetime, timedelta, timezone
```
| Class | Holds | Example |
|---|---|---|
| `date` | Year, month, day | `2026-10-31` |
| `time` | Hour, minute, second, microsecond | `14:30:00` |
| `datetime` | A `date` and a `time` together | `2026-10-31 14:30:00` |
| `timedelta` | A **duration** (a length of time) | `3 days, 4:00:00` |
| `timezone` | A fixed offset from UTC | `UTC+05:30` |

The module and its main class have the same name, so people write either `import datetime` (then `datetime.datetime`) or `from datetime import datetime`. This note uses the second form.

---

## Creating Dates and Times

```python
from datetime import date, time, datetime

d = date(2026, 10, 31)
t = time(14, 30, 15)
dt = datetime(2026, 10, 31, 14, 30, 15)

print(d)
print(t)
print(dt)
# Output: 2026-10-31
# Output: 14:30:15
# Output: 2026-10-31 14:30:15
```
Arguments go in the order **year, month, day, hour, minute, second, microsecond**. Month and day cannot be written with a leading zero (`date(2026, 08, 5)` is a syntax error), and impossible dates are rejected:
```python
from datetime import date

date(2026, 2, 30)
# Error: ValueError: day is out of range for month
```

---

## Today and Now

```python
from datetime import date, datetime

today = date.today()
now = datetime.now()

print(type(today).__name__, type(now).__name__)
print(now.year >= 2024)
# Output: date datetime
# Output: True
```
`date.today()` and `datetime.now()` give the current local date and time, so their output differs on every run. That is why the other examples in this note use fixed dates, to keep results the same.

---

## Reading the Parts

```python
from datetime import datetime

dt = datetime(2026, 10, 31, 14, 30, 15)

print(dt.year, dt.month, dt.day)
print(dt.hour, dt.minute, dt.second)
print(dt.date())
print(dt.time())
print(dt.weekday())
# Output: 2026 10 31
# Output: 14 30 15
# Output: 2026-10-31
# Output: 14:30:15
# Output: 5
```
`weekday()` counts **Monday = 0 ... Sunday = 6** (`5` is Saturday). `isoweekday()` counts Monday = 1 ... Sunday = 7.

---

## Formatting: strftime

`strftime(format)` ("string from time") turns a date into text. Each `%` code is replaced by a part of the date.

```python
from datetime import datetime

dt = datetime(2026, 10, 31, 14, 5, 9)

print(dt.strftime("%d-%m-%Y"))
print(dt.strftime("%d %B %Y, %A"))
print(dt.strftime("%I:%M %p"))
print(dt.strftime("%H:%M:%S"))
print(dt.strftime("%b %d, %y"))
# Output: 31-10-2026
# Output: 31 October 2026, Saturday
# Output: 02:05 PM
# Output: 14:05:09
# Output: Oct 31, 26
```
| Code | Meaning | Example |
|---|---|---|
| `%Y` | Year, 4 digits | `2026` |
| `%y` | Year, 2 digits | `26` |
| `%m` | Month, 2 digits | `10` |
| `%B` / `%b` | Month name, full / short | `October` / `Oct` |
| `%d` | Day of month, 2 digits | `31` |
| `%A` / `%a` | Weekday name, full / short | `Saturday` / `Sat` |
| `%H` | Hour, 24-hour clock | `14` |
| `%I` | Hour, 12-hour clock | `02` |
| `%p` | AM or PM | `PM` |
| `%M` | Minute | `05` |
| `%S` | Second | `09` |
| `%f` | Microsecond | `000000` |
| `%j` | Day of the year | `304` |
| `%U` | Week number of the year (Sunday first) | `43` |
| `%z` | UTC offset (aware datetimes only) | `+0530` |
| `%Z` | Time zone name (aware datetimes only) | `UTC` |
| `%%` | A literal `%` | `%` |

Names such as `October` and `Saturday` follow the computer's **locale** (language settings). An f-string works too, with the same codes after a colon:
```python
from datetime import datetime

dt = datetime(2026, 10, 31, 14, 5)
print(f"{dt:%d/%m/%Y %H:%M}")
# Output: 31/10/2026 14:05
```

---

## Parsing: strptime

`datetime.strptime(text, format)` ("string parse time") does the reverse: it turns text into a `datetime`. The format must **match the text exactly**.

```python
from datetime import datetime

dt = datetime.strptime("25/12/2026 18:45", "%d/%m/%Y %H:%M")
print(dt)
print(dt.month)
# Output: 2026-12-25 18:45:00
# Output: 12
```
```python
from datetime import datetime

datetime.strptime("2026-12-25", "%d/%m/%Y")
# Error: ValueError: time data '2026-12-25' does not match format '%d/%m/%Y'
```
To get only a date: `datetime.strptime(text, fmt).date()`.

---

## ISO Format

The international standard `YYYY-MM-DD` (and `YYYY-MM-DDTHH:MM:SS`) is the best way to **store and exchange** dates. It sorts correctly as plain text and cannot be confused between day-first and month-first.

```python
from datetime import date, datetime

print(date(2026, 10, 31).isoformat())
print(datetime(2026, 10, 31, 14, 30).isoformat())
print(date.fromisoformat("2026-10-31"))
print(datetime.fromisoformat("2026-10-31T14:30:00"))
# Output: 2026-10-31
# Output: 2026-10-31T14:30:00
# Output: 2026-10-31
# Output: 2026-10-31 14:30:00
```
Python 3.11 and newer read more ISO variants in `fromisoformat` (for example a trailing `Z`). Older versions accept only the exact format that `isoformat()` writes.

---

## timedelta: Durations and Arithmetic

A `timedelta` is a **length of time**. You can add it to a date, subtract it, or get one by subtracting two dates.

```python
from datetime import date, datetime, timedelta

d = date(2026, 10, 31)
print(d + timedelta(days=30))
print(d - timedelta(weeks=2))

dt = datetime(2026, 10, 31, 22, 0)
print(dt + timedelta(hours=5, minutes=30))
# Output: 2026-11-30
# Output: 2026-10-17
# Output: 2026-11-01 03:30:00
```
Arguments: `days`, `seconds`, `microseconds`, `milliseconds`, `minutes`, `hours`, `weeks`. There is **no** `months` or `years`, because their length varies (see below).

**Difference between two dates**
```python
from datetime import date, datetime

diff = date(2026, 12, 25) - date(2026, 10, 31)
print(diff)
print(diff.days)

start = datetime(2026, 10, 31, 9, 0)
end = datetime(2026, 11, 2, 15, 30)
gap = end - start
print(gap)
print(gap.days, gap.seconds)
print(gap.total_seconds())
# Output: 55 days, 0:00:00
# Output: 55
# Output: 2 days, 6:30:00
# Output: 2 23400
# Output: 196200.0
```
Careful: `.seconds` is only the **leftover seconds after the whole days** (here 6 h 30 min = 23400 s), not the full length. For the full length use **`total_seconds()`**.

```python
from datetime import timedelta

gap = timedelta(days=2, hours=6, minutes=30)
print(gap.total_seconds() / 3600)
print(timedelta(hours=36))
print(-timedelta(days=1))
print(timedelta(days=1) * 3)
# Output: 54.5
# Output: 1 day, 12:00:00
# Output: -1 day, 0:00:00
# Output: 3 days, 0:00:00
```

---

## Comparing and Sorting

Dates and datetimes can be compared with `<`, `>`, `==` and sorted.

```python
from datetime import date

a = date(2026, 1, 15)
b = date(2026, 3, 1)

print(a < b)
print(max(a, b))
print(sorted([b, a]))
# Output: True
# Output: 2026-03-01
# Output: [datetime.date(2026, 1, 15), datetime.date(2026, 3, 1)]
```
You can compare a `date` with a `date`, and a `datetime` with a `datetime`, but not a `datetime` with a `date` (use `.date()` first).

---

## Changing Parts: replace and combine

Dates are **immutable**. `replace()` returns a **new** object with some parts changed.

```python
from datetime import date, datetime, time

d = date(2026, 10, 31)
print(d.replace(day=1))
print(d.replace(year=2027, month=1))

print(datetime.combine(d, time(9, 30)))
# Output: 2026-10-01
# Output: 2027-01-31
# Output: 2026-10-31 09:30:00
```
`replace` can fail if the result is impossible: `date(2026, 1, 31).replace(month=2)` raises `ValueError`.

---

## Weekdays, Months and the calendar Module

```python
import calendar
from datetime import date

print(calendar.isleap(2024), calendar.isleap(2026))
first_weekday, days_in_month = calendar.monthrange(2024, 2)
print(int(first_weekday), days_in_month)
print(calendar.day_name[date(2026, 10, 31).weekday()])
print(calendar.month_name[10])
# Output: True False
# Output: 3 29
# Output: Saturday
# Output: October
```
`calendar.monthrange(year, month)` returns `(weekday of the 1st, number of days in the month)`. Here `3` is Thursday (Monday = 0), and February 2024 has 29 days. In Python 3.12 and newer the weekday is a `calendar.Day` value that prints as `calendar.THURSDAY`; `int()` turns it into the number.

**Last day of a month**
```python
import calendar
from datetime import date

def last_day(year, month):
    return date(year, month, calendar.monthrange(year, month)[1])

print(last_day(2024, 2))
print(last_day(2026, 12))
# Output: 2024-02-29
# Output: 2026-12-31
```

**Adding months** (no built-in way; this keeps the day inside the month)
```python
import calendar
from datetime import date

def add_months(d, months):
    index = d.month - 1 + months
    year = d.year + index // 12
    month = index % 12 + 1
    day = min(d.day, calendar.monthrange(year, month)[1])
    return date(year, month, day)

print(add_months(date(2026, 1, 31), 1))
print(add_months(date(2026, 11, 15), 3))
# Output: 2026-02-28
# Output: 2027-02-15
```
The popular third-party package `python-dateutil` offers `relativedelta(months=1)` for the same job.

---

## Time Zones

A `datetime` is either:
- **naive**: no time-zone information (the default), or
- **aware**: it knows its offset from UTC.

```python
from datetime import datetime, timezone, timedelta

naive = datetime(2026, 10, 31, 12, 0)
utc = datetime(2026, 10, 31, 12, 0, tzinfo=timezone.utc)
ist = datetime(2026, 10, 31, 17, 30, tzinfo=timezone(timedelta(hours=5, minutes=30)))

print(naive.tzinfo)
print(utc)
print(ist)
print(utc == ist)
# Output: None
# Output: 2026-10-31 12:00:00+00:00
# Output: 2026-10-31 17:30:00+05:30
# Output: True
```
Two aware datetimes compare by the **moment in time they describe**, so noon in UTC equals 17:30 in India.

**Current time in UTC**
```python
from datetime import datetime, timezone

now_utc = datetime.now(timezone.utc)
print(now_utc.tzinfo)
# Output: UTC
```
`datetime.utcnow()` is **deprecated** since Python 3.12 (it returns a naive object that only pretends to be UTC). Use `datetime.now(timezone.utc)`.

**Named zones with `zoneinfo`** (Python 3.9+) handle daylight-saving rules for you.
```python
from datetime import datetime
from zoneinfo import ZoneInfo

meeting = datetime(2026, 1, 15, 12, 0, tzinfo=ZoneInfo("UTC"))
print(meeting.astimezone(ZoneInfo("Asia/Kolkata")))
print(meeting.astimezone(ZoneInfo("America/New_York")))
# Output: 2026-01-15 17:30:00+05:30
# Output: 2026-01-15 07:00:00-05:00
```
`astimezone(zone)` converts the **same moment** to another zone. On Windows, `zoneinfo` may need the data package: `python -m pip install tzdata`.

**Naive and aware cannot be mixed**
```python
from datetime import datetime, timezone

naive = datetime(2026, 10, 31)
aware = datetime(2026, 10, 31, tzinfo=timezone.utc)
naive - aware
# Error: TypeError: can't subtract offset-naive and offset-aware datetimes
```
A good rule: **store and calculate in UTC, convert to local time only when showing it** to a person.

---

## The time Module

The separate `time` module works with plain seconds. The most useful parts:

```python
import time

start = time.perf_counter()
total = sum(range(100_000))
elapsed = time.perf_counter() - start
print(elapsed >= 0)

print(time.time() > 1_700_000_000)
# Output: True
# Output: True
```
| Function | Use |
|---|---|
| `time.time()` | Seconds since 1 January 1970 (the "epoch") |
| `time.perf_counter()` | Best clock for **measuring how long code takes** |
| `time.sleep(seconds)` | Pause the program |
| `time.monotonic()` | A clock that never goes backwards |

Convert between epoch seconds and a `datetime`:
```python
from datetime import datetime, timezone

stamp = 1_700_000_000
print(datetime.fromtimestamp(stamp, tz=timezone.utc))
print(datetime(2023, 11, 14, 22, 13, 20, tzinfo=timezone.utc).timestamp())
# Output: 2023-11-14 22:13:20+00:00
# Output: 1700000000.0
```

---

## Practical Examples

**Age in years**
```python
from datetime import date

def age(born, today):
    years = today.year - born.year
    if (today.month, today.day) < (born.month, born.day):
        years -= 1
    return years

print(age(date(2005, 12, 15), date(2026, 10, 31)))
print(age(date(2005, 10, 31), date(2026, 10, 31)))
# Output: 20
# Output: 21
```

**Days until an event**
```python
from datetime import date

today = date(2026, 10, 31)
diwali = date(2026, 11, 8)
print((diwali - today).days, "days to go")
# Output: 8 days to go
```

**Is it the weekend?**
```python
from datetime import date

def is_weekend(d):
    return d.weekday() >= 5

print(is_weekend(date(2026, 10, 31)), is_weekend(date(2026, 11, 2)))
# Output: True False
```

**Counting working days between two dates**
```python
from datetime import date, timedelta

def working_days(start, end):
    days = 0
    current = start
    while current <= end:
        if current.weekday() < 5:
            days += 1
        current += timedelta(days=1)
    return days

print(working_days(date(2026, 11, 2), date(2026, 11, 13)))
# Output: 10
```

**A log line with a timestamp**
```python
from datetime import datetime

def stamp(message, when):
    return f"[{when:%Y-%m-%d %H:%M:%S}] {message}"

print(stamp("started", datetime(2026, 10, 31, 9, 5, 7)))
# Output: [2026-10-31 09:05:07] started
```

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| `import datetime`, then `datetime(2026, 1, 1)` | `TypeError: 'module' object is not callable` | `from datetime import datetime`, or `datetime.datetime(...)` |
| `from datetime import datetime`, then `datetime.date.today()` | `AttributeError` | `from datetime import date` and `date.today()` |
| Leading zero: `date(2026, 08, 5)` | `SyntaxError` | `date(2026, 8, 5)` |
| Impossible date, such as 30 February | `ValueError` | Check, or use `calendar.monthrange` |
| `strptime` format does not match the text | `ValueError` | Make the format match every character |
| Using `diff.seconds` for the whole duration | Only the part after whole days | `diff.total_seconds()` |
| `timedelta(months=1)` | `TypeError` | Use `days`, or an `add_months` function, or `dateutil` |
| Mixing naive and aware datetimes | `TypeError` | Make both aware (or both naive) |
| `datetime.utcnow()` | Deprecated naive "UTC" object | `datetime.now(timezone.utc)` |
| Day-first vs month-first confusion in `01/02/2026` | Different meaning in different countries | Store ISO text `2026-02-01` |
| Expecting `weekday()` Sunday = 0 | Monday is 0 | `isoweekday()` for Monday = 1 ... Sunday = 7 |
| Comparing `date` and `datetime` | `TypeError` | Convert with `.date()` |
| Using `time.time()` to measure short code | Less precise | `time.perf_counter()` |

---

- [Back to section index](./README.md)
