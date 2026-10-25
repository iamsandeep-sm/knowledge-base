# Comprehensions Practice

Related notes: [List Comprehensions](../../05-comprehensions/list-comprehensions.md), [Dictionary Comprehensions](../../05-comprehensions/dictionary-comprehensions.md), [Set Comprehensions](../../05-comprehensions/set-comprehensions.md), [Generator Expressions](../../05-comprehensions/generator-expressions.md)

## Table of Contents
- [Practice Questions](#practice-questions)
- [Practice Answers](#practice-answers)

---

## Practice Questions

1. Create the list of cubes of `1` to `5` using a list comprehension.
2. From `range(20)` build a list of numbers divisible by 3.
3. Convert `["10", "20", "30"]` into a list of integers.
4. Given `words = ["python", "is", "fun"]`, make a list of their lengths and a list of the upper-case words.
5. From `[3, -1, 4, -5, 0]` make a list where negative numbers are replaced by `0`.
6. Label each number in `[1, 2, 3, 4]` as `"even"` or `"odd"`.
7. Create the list of all pairs `(x, y)` with `x` in `[1, 2]` and `y` in `[10, 20]`.
8. Flatten `[[1, 2], [3], [4, 5]]` into one list.
9. Create a 3 x 3 multiplication-table grid (list of lists).
10. Why is `[[0] * 3] * 3` a bad way to make a grid? How do you fix it?
11. Make the dictionary `{1: 1, 2: 4, 3: 9}` with a dictionary comprehension.
12. From `{"Riya": 90, "Amit": 45, "Sam": 82}` keep only those with marks of 50 or more.
13. Swap the keys and values of `{"a": 1, "b": 2}`.
14. Make a set of the unique lower-case first letters in `["Apple", "avocado", "Banana"]`.
15. What is the difference between `{x for x in a}` and `{x: x for x in a}`?
16. Compute the sum of squares of `1` to `10` without building a list.
17. What is the type of `(x for x in range(3))`? How do you get a tuple from it?
18. What does this print?
    ```python
    g = (n for n in range(3))
    print(list(g))
    print(list(g))
    ```
19. Why is `len((x for x in range(5)))` an error?
20. Rewrite as a comprehension:
    ```python
    result = []
    for n in range(10):
        if n % 2 == 1:
            result.append(n * n)
    ```

---

## Practice Answers

Try each question yourself first, then compare.

**Answer 1**
```python
print([x ** 3 for x in range(1, 6)])
# Output: [1, 8, 27, 64, 125]
```

**Answer 2**
```python
print([n for n in range(20) if n % 3 == 0])
# Output: [0, 3, 6, 9, 12, 15, 18]
```

**Answer 3**
```python
print([int(s) for s in ["10", "20", "30"]])
# Output: [10, 20, 30]
```

**Answer 4**
```python
words = ["python", "is", "fun"]
print([len(w) for w in words])
print([w.upper() for w in words])
# Output: [6, 2, 3]
# Output: ['PYTHON', 'IS', 'FUN']
```

**Answer 5**
```python
print([n if n > 0 else 0 for n in [3, -1, 4, -5, 0]])
# Output: [3, 0, 4, 0, 0]
```
`if ... else` goes **before** the `for`.

**Answer 6**
```python
print(["even" if n % 2 == 0 else "odd" for n in [1, 2, 3, 4]])
# Output: ['odd', 'even', 'odd', 'even']
```

**Answer 7**
```python
print([(x, y) for x in [1, 2] for y in [10, 20]])
# Output: [(1, 10), (1, 20), (2, 10), (2, 20)]
```

**Answer 8**
```python
print([n for row in [[1, 2], [3], [4, 5]] for n in row])
# Output: [1, 2, 3, 4, 5]
```

**Answer 9**
```python
print([[r * c for c in range(1, 4)] for r in range(1, 4)])
# Output: [[1, 2, 3], [2, 4, 6], [3, 6, 9]]
```

**Answer 10**

`[[0] * 3] * 3` repeats a **reference to the same inner list**, so changing one row changes all rows. Create a new row each time:
```python
grid = [[0] * 3 for _ in range(3)]
grid[0][0] = 5
print(grid)
# Output: [[5, 0, 0], [0, 0, 0], [0, 0, 0]]
```

**Answer 11**
```python
print({x: x ** 2 for x in range(1, 4)})
# Output: {1: 1, 2: 4, 3: 9}
```

**Answer 12**
```python
scores = {"Riya": 90, "Amit": 45, "Sam": 82}
print({k: v for k, v in scores.items() if v >= 50})
# Output: {'Riya': 90, 'Sam': 82}
```

**Answer 13**
```python
print({v: k for k, v in {"a": 1, "b": 2}.items()})
# Output: {1: 'a', 2: 'b'}
```

**Answer 14**
```python
print(sorted({w[0].lower() for w in ["Apple", "avocado", "Banana"]}))
# Output: ['a', 'b']
```

**Answer 15**

`{x for x in a}` is a **set comprehension** (unique items, no order). `{x: x for x in a}` is a **dictionary comprehension** (key-value pairs, because of the colon).

**Answer 16**
```python
print(sum(n ** 2 for n in range(1, 11)))
# Output: 385
```
A generator expression gives the numbers to `sum` one at a time, so no list is stored.

**Answer 17**

It is a **generator** (`<class 'generator'>`), not a tuple. Use `tuple(...)`:
```python
print(tuple(x for x in range(3)))
# Output: (0, 1, 2)
```

**Answer 18**
```python
g = (n for n in range(3))
print(list(g))
print(list(g))
# Output: [0, 1, 2]
# Output: []
```
A generator can be used only once; the second time it is already used up.

**Answer 19**

A generator produces values one by one and does not know how many it will make, so it has no length: `TypeError: object of type 'generator' has no len()`. Convert it to a list first, or count while looping.

**Answer 20**
```python
result = [n * n for n in range(10) if n % 2 == 1]
print(result)
# Output: [1, 9, 25, 49, 81]
```

---

- [Back to practice index](../README.md)
