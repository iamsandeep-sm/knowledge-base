# Strings Basics

A **string** (`str`) is **text**: any characters inside quotes.

```python
s1 = "Hello"
s2 = 'World'
s3 = """This string
spans many lines"""
print(s1, s2)
print(s3)
# Output: Hello World
# Output: This string
# Output: spans many lines
```
- Single `'...'` and double `"..."` quotes work the same. Use triple quotes `"""..."""` for multiple lines.
- Numbers inside quotes are **text**, not numbers: `"5"` is a string.

### Joining and repeating
```python
first = "Py"
second = "thon"
print(first + second)    # Python      + joins strings
print("ha" * 3)          # hahaha      * repeats a string
print("5" + "3")         # 53          text joined, NOT added
```

### Length, indexes and slices
Each character has a position called an **index**, starting at **0**.
```python
word = "Python"
#       P  y  t  h  o  n
#       0  1  2  3  4  5       positive index
#      -6 -5 -4 -3 -2 -1       negative index

print(len(word))      # 6
print(word[0])        # P
print(word[-1])       # n
print(word[0:3])      # Pyt     from index 0 up to (not including) 3
```

### Common string methods
```python
text = "  hello world  "
print(text.strip())            # hello world        removes spaces at both ends
print("hello".upper())         # HELLO
print("HELLO".lower())         # hello
print("hello world".title())   # Hello World
print("a-b-c".replace("-", "+"))   # a+b+c
print("a,b,c".split(","))      # ['a', 'b', 'c']
print("hello".startswith("he"))   # True
```

### Special characters (escape sequences)
A backslash `\` followed by a letter gives a special character.
```python
print("Line 1\nLine 2")        # \n  new line (prints Line 1, then Line 2 on the next line)
print("Name:\tKaran")          # \t  tab space (a wide gap between Name: and Karan)
print("She said \"Hi\"")       # \"  a quote inside a string
print("C:\\folder")            # \\  a single backslash
```
Output on screen:
```
Line 1
Line 2
Name:   Karan
She said "Hi"
C:\folder
```

### Strings cannot be changed (immutable)
```python
word = "Python"
# word[0] = "J"      # TypeError: 'str' object does not support item assignment
word = "J" + word[1:]    # build a NEW string instead
print(word)              # Jython
```

> Strings have many more features. They have their own detailed notes.

---

- [Back to section index](./README.md)
