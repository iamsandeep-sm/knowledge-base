# Closures - Revision Notes ~ sm

> **In one line:** A **closure** is an **inner function that remembers the variables of the outer function** in which it was created, even after the outer function has finished running. Closures are the foundation of decorators.

## Table of Contents
- [Functions Inside Functions](#functions-inside-functions)
- [What is a Closure?](#what-is-a-closure)
- [Three Conditions](#three-conditions)
- [Function Factories](#function-factories)
- [Closures Keep State: nonlocal](#closures-keep-state-nonlocal)
- [Looking Inside a Closure](#looking-inside-a-closure)
- [Each Closure Has Its Own Variables](#each-closure-has-its-own-variables)
- [The Late Binding Trap](#the-late-binding-trap)
- [Closure vs Class](#closure-vs-class)
- [Closures and Decorators](#closures-and-decorators)
- [Common Mistakes](#common-mistakes)

---

## Functions Inside Functions

A function can be defined **inside** another function, and it can be **returned** like any other value (functions are objects).

```python
def outer():
    def inner():
        return "hello from inner"
    return inner              # return the function itself, not a call

f = outer()
print(f())
# Output: hello from inner
```
`outer()` returns the function `inner`. We store it in `f` and call it later.

---

## What is a Closure?

When the inner function **uses a variable from the outer function**, Python keeps that variable alive for it.

```python
def make_greeter(greeting):
    def greet(name):
        return f"{greeting}, {name}!"      # greeting comes from outside
    return greet

hello = make_greeter("Hello")
print(hello("Riya"))
print(hello("Amit"))
# Output: Hello, Riya!
# Output: Hello, Amit!
```
`make_greeter` has already finished, yet `hello` still knows `greeting = "Hello"`. The inner function has **closed over** that variable: it is a **closure**. The remembered variable is called a **free variable**.

---

## Three Conditions

A closure exists when:

1. There is a **nested** function (a function inside a function).
2. The inner function **uses a variable** of the outer function.
3. The outer function **returns** the inner function (or passes it somewhere it will be called later).

---

## Function Factories

A closure lets one function **produce many customised functions**.

```python
def multiplier(n):
    def multiply(x):
        return x * n
    return multiply

double = multiplier(2)
triple = multiplier(3)

print(double(10))
print(triple(10))
print(double(triple(2)))
# Output: 20
# Output: 30
# Output: 12
```
`double` and `triple` come from the same code but remember different values of `n`.

---

## Closures Keep State: nonlocal

A closure can **change** the remembered variable if you declare it with `nonlocal` (see the Scope note).

```python
def make_counter():
    count = 0
    def counter():
        nonlocal count
        count += 1
        return count
    return counter

c = make_counter()
print(c(), c(), c())

d = make_counter()           # a separate counter
print(d())
# Output: 1 2 3
# Output: 1
```
The count is stored **inside the closure**, not in a global variable, and nobody can reach it except through `counter`. That is a form of **data hiding**.

Without `nonlocal`, `count += 1` would try to create a new local variable and fail:
```python
def make_counter():
    count = 0
    def counter():
        count += 1
        return count
    return counter

make_counter()()
# Error:  UnboundLocalError: cannot access local variable 'count' where it is not associated with a value
```
Only **assigning** needs `nonlocal`. Mutating an object does not: `items.append(x)` works on a remembered list.

---

## Looking Inside a Closure

A function keeps its free variables in `__closure__`.

```python
def make_adder(n):
    def add(x):
        return x + n
    return add

add5 = make_adder(5)
print(add5.__closure__ is not None)
print(add5.__closure__[0].cell_contents)
print(add5.__code__.co_freevars)
# Output: True
# Output: 5
# Output: ('n',)
```
A normal function has `__closure__` equal to `None`:
```python
def plain(x):
    return x

print(plain.__closure__)
# Output: None
```

---

## Each Closure Has Its Own Variables

```python
def make_account(balance):
    def deposit(amount):
        nonlocal balance
        balance += amount
        return balance
    return deposit

a = make_account(100)
b = make_account(500)
print(a(50))
print(b(50))
print(a(10))
# Output: 150
# Output: 550
# Output: 160
```
Every call to `make_account` creates a **new** set of variables, so `a` and `b` do not interfere.

---

## The Late Binding Trap

A closure remembers the **variable**, not its value at that moment. If the variable changes later, the closure sees the **new** value. This often surprises people with loops.

```python
funcs = []
for i in range(3):
    def show():
        return i
    funcs.append(show)

print([f() for f in funcs])
# Output: [2, 2, 2]
```
All three functions share the same `i`, which is `2` when the loop ends. **Fix 1:** capture the value as a default argument.
```python
funcs = []
for i in range(3):
    def show(i=i):
        return i
    funcs.append(show)

print([f() for f in funcs])
# Output: [0, 1, 2]
```
**Fix 2:** build the function in a factory, so each call has its own variable.
```python
def make_show(i):
    def show():
        return i
    return show

funcs = [make_show(i) for i in range(3)]
print([f() for f in funcs])
# Output: [0, 1, 2]
```
(The same trap affects lambdas in loops, see the Lambda Functions note.)

---

## Closure vs Class

Both can keep state between calls.

```python
# closure
def make_counter():
    count = 0
    def counter():
        nonlocal count
        count += 1
        return count
    return counter

# class
class Counter:
    def __init__(self):
        self.count = 0

    def __call__(self):
        self.count += 1
        return self.count

c1 = make_counter()
c2 = Counter()
print(c1(), c1(), c2(), c2())
# Output: 1 2 1 2
```

| | Closure | Class |
|---|---|---|
| Best for | One small behaviour with a little hidden state | Several methods, attributes, inheritance |
| Data visible from outside | No | Yes (unless made private) |
| Amount of code | Less | More |

---

## Closures and Decorators

A **decorator** is a function that takes a function and returns a new function that **remembers** the original. That remembering is exactly a closure.

```python
def shout(func):
    def wrapper():
        return func().upper()      # func is remembered from the outside
    return wrapper

def hello():
    return "hello"

loud = shout(hello)
print(loud())
# Output: HELLO
```
See the Decorators note for the `@` syntax.

---

## Common Mistakes

| Mistake | What happens | Fix |
|---|---|---|
| Assigning to the outer variable without `nonlocal` | `UnboundLocalError` | `nonlocal count` |
| Returning `inner()` instead of `inner` | You get the result, not a function | `return inner` |
| Creating functions in a loop and using the loop variable | All see the last value (late binding) | Default argument `i=i`, or a factory function |
| Using `nonlocal` for a name that does not exist in an enclosing function | `SyntaxError: no binding for nonlocal 'x' found` | Define it in the outer function first |
| Using `global` when you meant `nonlocal` | Changes a module-level variable instead | `nonlocal` for the enclosing function |
| Expecting two closures to share a counter | Each call to the factory makes its own | Share an object (list, dict, class) if you need sharing |

---

- [Back to section index](./README.md)
