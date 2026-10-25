# Nested Data Structures

## Nested Lists

A list can contain other lists. A list of lists is also called a **2D list** or a **matrix**.

### Creating and accessing
```python
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9],
]
print(matrix[0])        # [1, 2, 3]     first row
print(matrix[1][2])     # 6             row 1, column 2
print(matrix[-1][-1])   # 9             last row, last column
```

### Changing and adding
```python
matrix[0][0] = 100
matrix.append([10, 11, 12])      # add a new row
matrix[1].append(99)             # add an item inside a row
print(matrix[0])    # [100, 2, 3]
print(matrix[1])    # [4, 5, 6, 99]
```

### Looping through a nested list
```python
grid = [[1, 2, 3], [4, 5, 6]]
for row in grid:
    for value in row:
        print(value, end=" ")
    print()
# 1 2 3
# 4 5 6
```

### Flatten a 2D list
```python
grid = [[1, 2, 3], [4, 5, 6]]
flat = [n for row in grid for n in row]
print(flat)    # [1, 2, 3, 4, 5, 6]
```

### Transpose rows and columns
```python
grid = [[1, 2, 3], [4, 5, 6]]
print([list(col) for col in zip(*grid)])    # [[1, 4], [2, 5], [3, 6]]
```

### The classic trap: `[[0] * 3] * 3`
```python
bad = [[0] * 3] * 3          # the SAME inner list is repeated 3 times
bad[0][0] = 1
print(bad)    # [[1, 0, 0], [1, 0, 0], [1, 0, 0]]    all rows changed!

good = [[0] * 3 for _ in range(3)]    # a NEW inner list each time
good[0][0] = 1
print(good)   # [[1, 0, 0], [0, 0, 0], [0, 0, 0]]
```

### Building a nested list (3x3 table)
```python
table = [[r * c for c in range(1, 4)] for r in range(1, 4)]
print(table)    # [[1, 2, 3], [2, 4, 6], [3, 6, 9]]
```

---

## Nested Dictionaries

A dictionary can contain other dictionaries (or lists) as values.

### Creating
```python
students = {
    'stu1': {'name': 'Karan', 'age': 19},
    'stu2': {'name': 'Riya',  'age': 20},
}
```

### Building from separate dictionaries
```python
s1 = {'name': 'Karan', 'age': 19}
s2 = {'name': 'Riya',  'age': 20}
students = {'stu1': s1, 'stu2': s2}
```

### Accessing (chain the keys)
```python
print(students['stu1'])            # {'name': 'Karan', 'age': 19}
print(students['stu1']['name'])    # Karan
print(students.get('stu3', {}).get('name'))   # None  (safe access, no KeyError)
```

### Changing and adding
```python
students['stu1']['age'] = 21                          # change
students['stu3'] = {'name': 'Sam', 'age': 22}         # add a new inner dict
students['stu2']['city'] = 'Pune'                     # add a new key inside an inner dict
```

### Removing
```python
del students['stu3']                 # remove a whole inner dict
students['stu2'].pop('city')         # remove one key from an inner dict
```

### Looping through nested dictionaries
```python
for sid, details in students.items():
    print(sid)
    for key, value in details.items():
        print('   ', key, ':', value)
```

### Dictionaries with lists as values
```python
courses = {'Karan': ['Python', 'SQL'], 'Riya': ['Java']}
courses['Riya'].append('Python')
print(courses)    # {'Karan': ['Python', 'SQL'], 'Riya': ['Java', 'Python']}
```

---

- [Back to section index](./README.md)
