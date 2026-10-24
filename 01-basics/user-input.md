# Input from the User

`input()` **pauses the program**, shows a message (the **prompt**), waits for the user to type something and press Enter, then **returns what was typed**.

```python
name = input("Enter your name: ")
print("Hello", name)
# User types: Karan
# Output: Enter your name: Karan
# Output: Hello Karan
```

### The most important rule: `input()` always returns a STRING
Even if the user types a number, you get **text**.
```python
age = input("Enter your age: ")
print(age, type(age))
# User types: 19
# Output: Enter your age: 19
# Output: 19 <class 'str'>
```

### Convert the input when you need a number
Wrap `input()` in `int()` (whole number) or `float()` (decimal).
```python
age = int(input("Enter your age: "))
print("Next year you will be", age + 1)
# User types: 19
# Output: Enter your age: 19
# Output: Next year you will be 20
```

### What goes wrong without conversion?
```python
a = input("First number: ")
b = input("Second number: ")
print(a + b)
# User types: 4
# User types: 5
# Output: First number: 4
# Output: Second number: 5
# Output: 45
```
`"4" + "5"` joins the two **texts** into `"45"`. Convert first:
```python
a = int(input("First number: "))
b = int(input("Second number: "))
print(a + b)
# User types: 4
# User types: 5
# Output: First number: 4
# Output: Second number: 5
# Output: 9
```

### Several values on one line
`split()` cuts the typed text at spaces into a list of strings. You must type **exactly as many values** as there are variables on the left, otherwise you get a `ValueError`.
```python
a, b = input("Enter two numbers: ").split()
print(a, b)
# User types: 3 4
# Output: Enter two numbers: 3 4
# Output: 3 4
```
Convert them to numbers at the same time with `map()`:
```python
a, b = map(int, input("Enter two numbers: ").split())
print(a + b)
# User types: 3 4
# Output: Enter two numbers: 3 4
# Output: 7
```

### If the user types something that is not a number
`int("abc")` stops the program with `ValueError`. You can protect against this with `try` / `except` (explained in detail in the error handling notes):
```python
text = input("Enter a number: ")
try:
    number = int(text)
    print("Double is", number * 2)
except ValueError:
    print("That is not a valid whole number")
# User types: abc
# Output: Enter a number: abc
# Output: That is not a valid whole number
```

---

- [Back to section index](./README.md)
