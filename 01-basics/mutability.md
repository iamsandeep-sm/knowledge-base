# Mutable vs Immutable

- **Mutable** = can be **changed in place** after it is created.
- **Immutable** = **cannot be changed**. Any "change" creates a **new** value.

| Immutable (cannot change) | Mutable (can change) |
|---|---|
| `int`, `float`, `complex` | `list` |
| `str` | `dict` |
| `bool` | `set` |
| `tuple` | |
| `None`, `frozenset` | |

> A tuple itself cannot change, but if it **contains** a mutable item (like a list), that inner item can still be changed.

### Immutable example
```python
text = "cat"
# text[0] = "b"      # TypeError: 'str' object does not support item assignment

t = (1, 2, 3)
# t[0] = 99          # TypeError: 'tuple' object does not support item assignment
```

### Mutable example
```python
fruits = ["apple", "banana"]
fruits[0] = "mango"       # change an item in place
fruits.append("cherry")   # add an item
print(fruits)
# Output: ['mango', 'banana', 'cherry']
```

### Why it matters: two names, one list
```python
a = [1, 2]
b = a              # b is NOT a copy; both names point to the same list
b.append(3)
print(a)           # [1, 2, 3]     a changed too!
```
```python
x = 5
y = x
y = y + 1          # an int is immutable, so y becomes a NEW value
print(x, y)        # 5 6           x is untouched
```

### Proof with `id()`
`id()` gives an object's identity (roughly, where it lives in memory). The same `id` means the same object.
```python
nums = [1]
before = id(nums)
nums.append(2)
print(id(nums) == before)    # True     same list, changed in place

n = 5
before = id(n)
n = n + 1
print(id(n) == before)       # False    a new value
```

---

- [Back to section index](./README.md)
