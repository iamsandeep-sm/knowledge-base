# Scope - Revision Notes ~ sm

> **In one line:** **Scope** decides **where a variable can be used**. A variable created inside a function is **local** (it exists only there); a variable created outside is **global**. Python looks names up in the order **L**ocal, **E**nclosing, **G**lobal, **B**uilt-in (LEGB).

## Table of Contents
- [What is Scope?](#what-is-scope)
- [Local Variables](#local-variables)
- [Global Variables](#global-variables)
- [Same Name in Two Scopes](#same-name-in-two-scopes)
- [The LEGB Rule](#the-legb-rule)
- [Changing a Global with global](#changing-a-global-with-global)
- [UnboundLocalError](#unboundlocalerror)
- [Enclosing Scope and nonlocal](#enclosing-scope-and-nonlocal)
- [Built-in Scope and Shadowing](#built-in-scope-and-shadowing)
- [Loops, if Blocks and Comprehensions](#loops-if-blocks-and-comprehensions)
- [Best Practices](#best-practices)

---

## What is Scope?

Think of a function as a **room**. Things made inside the room stay in the room. Things in the open hall (outside every function) can be seen from every room.

| Scope | Where the variable is created | Lives until |
|---|---|---|
| **Local** | Inside a function (including its parameters) | The function ends |
| **Global** | At the top level of the file | The program ends |

---

## Local Variables

```python
def calculate():
    result = 10 * 2        # local variable
    print(result)

calculate()
print(result)
# Output: 20
# Error:  NameError: name 'result' is not defined
```
`result` is created when `calculate()` runs and **disappears** when it ends. Every call gets a fresh set of local variables. Parameters are local variables too.

---

## Global Variables

A variable defined outside all functions can be **read** inside any function.

```python
tax_rate = 0.18            # global

def price_with_tax(price):
    return price + price * tax_rate     # reading a global

print(price_with_tax(100))
# Output: 118.0
```

---

## Same Name in Two Scopes

If a function **assigns** to a name, Python creates a **new local variable** with that name. The global one is not touched.

```python
x = "global"

def show():
    x = "local"            # a separate, local x
    print(x)

show()
print(x)
# Output: local
# Output: global
```

---

## The LEGB Rule

When Python meets a name, it searches in this order and stops at the first match:

| Letter | Scope | Meaning |
|---|---|---|
| **L** | Local | Inside the current function |
| **E** | Enclosing | Inside any outer function (when functions are nested) |
| **G** | Global | At the top of the file (module) |
| **B** | Built-in | Names Python provides, such as `print`, `len` |

```python
x = "global"

def outer():
    x = "enclosing"
    def inner():
        x = "local"
        print(x)
    inner()
    print(x)

outer()
print(x)
# Output: local
# Output: enclosing
# Output: global
```
If the name is found nowhere, you get `NameError`.

---

## Changing a Global with global

To **assign** to a global variable inside a function, declare it with `global`.

```python
count = 0

def increase():
    global count           # "count means the global one"
    count += 1

increase()
increase()
print(count)
# Output: 2
```
Without the `global` line, `count += 1` would try to change a local `count` and fail (see the next section).

> Mutating a global **list or dictionary** (`items.append(1)`) works **without** `global`, because you are not assigning a new value to the name. Only **assignment** needs `global`.

---

## UnboundLocalError

If a function assigns to a name **anywhere** in its body, that name is local for the **whole** function. Using it before the assignment is an error.

```python
count = 0

def increase():
    count += 1             # count is local here, but has no value yet

increase()
# Error:  UnboundLocalError: cannot access local variable 'count' where it is not associated with a value
```
Fixes: add `global count`, or (better) pass the value in and return the result:
```python
def increase(n):
    return n + 1

count = 0
count = increase(count)
print(count)
# Output: 1
```
The wording of the message differs a little in older versions (`local variable 'count' referenced before assignment`).

---

## Enclosing Scope and nonlocal

When a function is defined **inside another function**, the inner one can read the outer one's variables. To **assign** to them, use `nonlocal`.

```python
def counter():
    count = 0
    def add():
        nonlocal count     # the count of counter(), not a new local
        count += 1
        return count
    return add

c = counter()
print(c())
print(c())
print(c())
# Output: 1
# Output: 2
# Output: 3
```
The inner function "remembers" `count` even after `counter()` has finished. This is a **closure** (see the Closures note).

| Keyword | Refers to |
|---|---|
| `global x` | `x` at the top level of the file |
| `nonlocal x` | `x` in the nearest enclosing **function** |

---

## Built-in Scope and Shadowing

Names like `print`, `len`, `sum` live in the built-in scope. If you create a variable with the same name, you **hide (shadow)** the built-in one.

```python
sum = 100                  # shadows the built-in sum()
print(sum([1, 2, 3]))
# Error:  TypeError: 'int' object is not callable
```
Fix: use another name (`total`) and restart Python, or `del sum` to remove your variable and get the built-in back.

---

## Loops, if Blocks and Comprehensions

In Python only **functions** (and classes and modules) create a new scope. `if`, `for` and `while` blocks do **not**.

```python
for i in range(3):
    last = i * 10

print(i, last)             # still available after the loop
# Output: 2 20

if True:
    message = "hello"
print(message)
# Output: hello
```
A **comprehension** does have its own scope, so its loop variable does not leak:
```python
squares = [n * n for n in range(3)]
print(squares)
print(n)
# Output: [0, 1, 4]
# Error:  NameError: name 'n' is not defined
```

---

## Best Practices

- Prefer **local** variables, and pass data into functions with **parameters** and get results out with **return**.
- Avoid changing globals from inside functions. It makes bugs hard to find because any function could change them.
- Global **constants** in `UPPER_CASE` (`TAX_RATE = 0.18`) are fine, as they are only read.
- Do not name variables after built-ins (`list`, `str`, `sum`, `max`, `input`).
- Use `nonlocal` only for small closures.

---

- [Back to section index](./README.md)
