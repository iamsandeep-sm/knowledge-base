# Slicing

## Slicing Lists

```
List[start : end : step]
```

| Part | Meaning | Default |
|---|---|---|
| `start` | Index where slicing begins (**included**) | `0` |
| `end` | Index where slicing stops (**excluded**) | end of list |
| `step` | Jump between items (optional) | `1` |

```python
animals = ["cat", "dog", "bat", "mouse", "pig", "horse", "donkey", "goat", "cow"]
#            0      1      2      3        4      5        6        7       8

print(animals[3:7])       # ['mouse', 'pig', 'horse', 'donkey']          positive indexes
print(animals[-7:-2])     # ['bat', 'mouse', 'pig', 'horse', 'donkey']   negative indexes
```

**From an index to the end** (no end given):
```python
print(animals[4:])        # ['pig', 'horse', 'donkey', 'goat', 'cow']
print(animals[-4:])       # ['horse', 'donkey', 'goat', 'cow']
```

**From the start up to an index** (no start given):
```python
print(animals[:6])        # ['cat', 'dog', 'bat', 'mouse', 'pig', 'horse']
print(animals[:-3])       # ['cat', 'dog', 'bat', 'mouse', 'pig', 'horse']
```

**Alternate items (step / jump index):**
```python
print(animals[::2])         # ['cat', 'bat', 'pig', 'donkey', 'cow']
print(animals[-8:-1:2])     # ['dog', 'mouse', 'horse', 'goat']
print(animals[1:8:3])       # ['dog', 'pig', 'goat']    every 3rd item between index 1 and 7
```

**Reverse and copy:**
```python
print(animals[::-1])        # ['cow', 'goat', 'donkey', 'horse', 'pig', 'mouse', 'bat', 'dog', 'cat']
print(animals[:])           # full (shallow) copy of the list
```

> The **end** index is never included.
> Slicing **never raises IndexError**: `animals[50:100]` simply returns `[]`.
> A slice always creates a **new list**.
> A negative step walks **backwards**, so `start` should be larger than `end`: `animals[7:2:-1]`.

---

## Slicing Tuples

You can extract a portion of a tuple by giving where to **start**, where to **stop**, and (optionally) how many items to **jump**.

```
tuple[start : end : step]
```

| Part | Meaning | Default |
|---|---|---|
| `start` | Index where slicing begins (**included**) | `0` |
| `end` | Index where slicing stops (**excluded**) | end of tuple |
| `step` | Jump between items (optional) | `1` |

```python
animals = ("cat", "dog", "bat", "mouse", "pig", "horse", "donkey", "goat", "cow")
#            0      1      2      3        4      5        6        7       8

print(animals[3:7])     # ('mouse', 'pig', 'horse', 'donkey')       → positive indexes
print(animals[-7:-2])   # ('bat', 'mouse', 'pig', 'horse', 'donkey') → negative indexes
```

### More slicing patterns
```python
print(animals[:3])      # ('cat', 'dog', 'bat')          → from start up to index 2
print(animals[5:])      # ('horse', 'donkey', 'goat', 'cow') → from index 5 to end
print(animals[::2])     # ('cat', 'bat', 'pig', 'donkey', 'cow') → every 2nd item
print(animals[::-1])    # reversed tuple (cow ... cat)
print(animals[:])       # full copy
```

>  **Trick:** `tuple[::-1]` is the quickest way to **reverse** a tuple.
> **Slicing** never raises IndexError** — out-of-range slices just return what is available.

---

- [Back to section index](./README.md)
