# Checking Types

### `type()`
```python
age = 19
print(type(age))    # <class 'int'>
```

### `isinstance()` - is it this type? (gives True or False)
```python
print(isinstance(19, int))        # True
print(isinstance("hi", str))      # True
print(isinstance(3.5, int))       # False
print(isinstance(3, (int, float)))   # True    matches ANY type in the tuple
```

### Python can change type when you reassign
```python
x = 5
print(type(x))     # <class 'int'>
x = "five"
print(type(x))     # <class 'str'>
```

---

- [Back to section index](./README.md)
