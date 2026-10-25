# Unpacking

## Unpacking Tuples

**Packing** = putting values into a tuple. **Unpacking** = pulling values out into separate variables, in one line.

```python
info = ("Marcus", 20, "MIT")          # packing
(name, age, university) = info        # unpacking
print("Name:", name)
print("Age:", age)
print("Studies at:", university)
```
> The number of variables **must match** the number of items — otherwise `ValueError`.

### Using `*` (star) for extra items
If you have **more items than variables**, put `*` before one variable. It collects the leftovers into a **list**.

```python
fauna = ("cat", "dog", "horse", "pig", "parrot", "salmon")
(*animals, bird, fish) = fauna

print("Animals:", animals)   # ['cat', 'dog', 'horse', 'pig']  ← a LIST
print("Bird:", bird)         # parrot
print("Fish:", fish)         # salmon
```

The `*` variable can be anywhere:
```python
first, *middle, last = (1, 2, 3, 4, 5)
print(first)    # 1
print(middle)   # [2, 3, 4]
print(last)     # 5
```

### Ignoring values with `_`
```python
name, _, university = ("Marcus", 20, "MIT")   # age ignored
```

### Swapping variables (classic trick)
```python
a, b = 10, 20
a, b = b, a
print(a, b)    # 20 10
```

### Returning multiple values from a function
```python
def min_max(nums):
    return min(nums), max(nums)     # returns a tuple

low, high = min_max((4, 9, 2, 7))
print(low, high)    # 2 9
```

---

## Unpacking a List

```python
a, b, c = [1, 2, 3]
first, *rest = [1, 2, 3, 4]
print(first, rest)    # 1 [2, 3, 4]
a, b = b, a           # swap two variables
```

---

- [Back to section index](./README.md)
