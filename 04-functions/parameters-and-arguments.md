# Parameters and Arguments - Revision Notes ~ sm

> **In one line:** **Parameters** are the names in a function definition; **arguments** are the actual values you pass when calling it. Python lets you pass them by position, by name (keyword), with default values, or in any number using `*args` and `**kwargs`.

## Table of Contents
- [Parameter vs Argument](#parameter-vs-argument)
- [Positional Arguments](#positional-arguments)
- [Keyword Arguments](#keyword-arguments)
- [Default Parameter Values](#default-parameter-values)
- [The Mutable Default Trap](#the-mutable-default-trap)
- [Arbitrary Positional Arguments (*args)](#arbitrary-positional-arguments-args)
- [Arbitrary Keyword Arguments (**kwargs)](#arbitrary-keyword-arguments-kwargs)
- [Order of Parameters](#order-of-parameters)
- [Keyword-Only and Positional-Only](#keyword-only-and-positional-only)
- [Unpacking When Calling](#unpacking-when-calling)
- [Are Arguments Copied?](#are-arguments-copied)
- [Argument Errors](#argument-errors)

---

## Parameter vs Argument

```python
def power(base, exp):         # base and exp are PARAMETERS
    return base ** exp

print(power(2, 3))            # 2 and 3 are ARGUMENTS
# Output: 8
```

| Term | Where | Meaning |
|---|---|---|
| **Parameter** | In `def` | A name that receives a value |
| **Argument** | In the call | The value that is passed in |

People often use the two words loosely, but the difference is: *parameter = in the definition, argument = in the call*.

---

## Positional Arguments

Values are matched to parameters **by position**: first to first, second to second.

```python
def describe(name, age):
    print(name, "is", age)

describe("Riya", 20)
describe(20, "Riya")      # wrong order: no error, but a silly result
# Output: Riya is 20
# Output: 20 is Riya
```
The number of arguments must match the number of parameters (unless defaults are used).

---

## Keyword Arguments

Write `name=value` in the call. The **order no longer matters**, and the call is easier to read.

```python
def describe(name, age):
    print(name, "is", age)

describe(age=20, name="Riya")
describe("Amit", age=21)         # positional first, then keyword
# Output: Riya is 20
# Output: Amit is 21
```
Rule: **positional arguments must come before keyword arguments** in a call. `describe(name="Amit", 21)` is a `SyntaxError`.

You have used keyword arguments already: `print("a", "b", sep="-")`.

---

## Default Parameter Values

Give a parameter a default with `=` in the definition. If the caller skips it, the default is used.

```python
def greet(name, greeting="Hello"):
    print(greeting + ", " + name + "!")

greet("Riya")
greet("Riya", "Namaste")
greet("Riya", greeting="Hi")
# Output: Hello, Riya!
# Output: Namaste, Riya!
# Output: Hi, Riya!
```
- Parameters with defaults must come **after** those without: `def f(a, b=1)` is fine, `def f(a=1, b)` is a `SyntaxError`.
- The default is created **once**, when the function is defined (see the next section).

---

## The Mutable Default Trap

A default value that is a **list, dictionary or set** is shared between all calls.

```python
def add_item(item, items=[]):      # BAD
    items.append(item)
    return items

print(add_item("a"))
print(add_item("b"))               # the same list is reused!
# Output: ['a']
# Output: ['a', 'b']
```
The standard fix: use `None` as the default and create a new list inside.

```python
def add_item(item, items=None):    # GOOD
    if items is None:
        items = []
    items.append(item)
    return items

print(add_item("a"))
print(add_item("b"))
# Output: ['a']
# Output: ['b']
```
Safe defaults: numbers, strings, `True`/`False`, `None`, tuples (all immutable).

---

## Arbitrary Positional Arguments (*args)

Put `*` before a parameter name to accept **any number** of positional arguments. They arrive as a **tuple**.

```python
def total(*numbers):
    print(numbers)
    return sum(numbers)

print(total(1, 2, 3))
print(total())
# Output: (1, 2, 3)
# Output: 6
# Output: ()
# Output: 0
```
`*args` is only a convention; the `*` is what matters (`*numbers` works the same).

---

## Arbitrary Keyword Arguments (**kwargs)

Put `**` before a parameter name to accept any number of **keyword** arguments. They arrive as a **dictionary**.

```python
def show(**info):
    print(info)
    for key, value in info.items():
        print(key, "=", value)

show(name="Riya", age=20)
# Output: {'name': 'Riya', 'age': 20}
# Output: name = Riya
# Output: age = 20
```

| Form | Collects | Type inside the function |
|---|---|---|
| `*args` | Extra positional arguments | `tuple` |
| `**kwargs` | Extra keyword arguments | `dict` |

---

## Order of Parameters

When a definition has several kinds, they must appear in this order:

```
def f(positional, default=value, *args, keyword_only, **kwargs):
```

```python
def demo(a, b=2, *args, c=3, **kwargs):
    print(a, b, args, c, kwargs)

demo(1)
demo(1, 5, 6, 7, c=9, x=10)
# Output: 1 2 () 3 {}
# Output: 1 5 (6, 7) 9 {'x': 10}
```

---

## Keyword-Only and Positional-Only

**Keyword-only:** parameters after a `*` (or after `*args`) **must** be passed by name.
```python
def connect(host, *, port=80):
    print(host, port)

connect("site.com", port=8080)
# Output: site.com 8080
```
`connect("site.com", 8080)` gives `TypeError: connect() takes 1 positional argument but 2 were given`.

**Positional-only (Python 3.8+):** parameters before a `/` can only be passed by position.
```python
def double(n, /):
    return n * 2

print(double(4))
# Output: 8
```
`double(n=4)` is a `TypeError`. Many built-ins work this way, for example `len()`.

---

## Unpacking When Calling

`*` and `**` also work in the **call**: they spread a collection into arguments.

```python
def add(a, b, c):
    return a + b + c

nums = [1, 2, 3]
print(add(*nums))             # same as add(1, 2, 3)

data = {"a": 1, "b": 2, "c": 3}
print(add(**data))            # same as add(a=1, b=2, c=3)
# Output: 6
# Output: 6
```

---

## Are Arguments Copied?

Python passes a **reference to the object**, not a copy. What happens next depends on whether the object is mutable.

**Mutable object (list): changes inside the function are visible outside**
```python
def add_one(items):
    items.append(1)

data = [0]
add_one(data)
print(data)
# Output: [0, 1]
```

**Reassigning the parameter does not change the outside variable**
```python
def reset(items):
    items = []            # the name now points to a new list

data = [5, 6]
reset(data)
print(data)
# Output: [5, 6]
```

**Immutable objects (numbers, strings, tuples)** cannot be changed in place, so the caller's value stays the same.
```python
def add_ten(n):
    n = n + 10
    return n

x = 5
print(add_ten(x))
print(x)
# Output: 15
# Output: 5
```
To avoid changing the caller's list, work on a copy: `items = items.copy()`. See the Mutability note.

---

## Argument Errors

| Call | Error message |
|---|---|
| Too few arguments | `TypeError: f() missing 1 required positional argument: 'a'` |
| Too many arguments | `TypeError: f() takes from 1 to 2 positional arguments but 3 were given` (wording changes if there are no defaults: `takes 1 positional argument but 2 were given`) |
| Unknown keyword | `TypeError: f() got an unexpected keyword argument 'x'` |
| Same value twice | `TypeError: f() got multiple values for argument 'a'` |

```python
def f(a, b=1):
    pass

f()
# Error:  TypeError: f() missing 1 required positional argument: 'a'
```
Read the message: it names the function and the parameter.

---

- [Back to section index](./README.md)
