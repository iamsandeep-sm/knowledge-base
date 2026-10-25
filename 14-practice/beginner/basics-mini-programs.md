# Basics Mini Programs

Related notes: [Basics notes](../../01-basics/README.md)

### Greeting
```python
name = input("What is your name? ")
print(f"Welcome, {name}!")
# User types: Riya
# Output: What is your name? Riya
# Output: Welcome, Riya!
```

### Area of a rectangle
```python
length = float(input("Length: "))
width = float(input("Width: "))
area = length * width
print(f"Area = {area}")
# User types: 5
# User types: 3
# Output: Length: 5
# Output: Width: 3
# Output: Area = 15.0
```

### Area of a circle
```python
import math

radius = float(input("Radius: "))
area = math.pi * radius ** 2
print(f"Area = {area:.2f}")
# User types: 2
# Output: Radius: 2
# Output: Area = 12.57
```

### Simple interest
`Simple interest = (principal x rate x time) / 100`
```python
principal = float(input("Principal: "))
rate = float(input("Rate (%): "))
years = float(input("Time (years): "))
interest = principal * rate * years / 100
print(f"Interest = {interest}")
# User types: 1000
# User types: 5
# User types: 2
# Output: Principal: 1000
# Output: Rate (%): 5
# Output: Time (years): 2
# Output: Interest = 100.0
```

### Celsius to Fahrenheit
`F = C x 9/5 + 32`
```python
celsius = float(input("Temperature in Celsius: "))
fahrenheit = celsius * 9 / 5 + 32
print(f"{celsius}C = {fahrenheit}F")
# User types: 37
# Output: Temperature in Celsius: 37
# Output: 37.0C = 98.6F
```
Some inputs give a long decimal. For example, `36.6` gives `97.88000000000001`, because of how floats are stored (see [Numbers](../../01-basics/numbers.md)). Use `{fahrenheit:.1f}` to always print a tidy `97.9`.

### Swap two numbers read from the user
```python
a = int(input("a: "))
b = int(input("b: "))
a, b = b, a
print(f"a = {a}, b = {b}")
# User types: 10
# User types: 20
# Output: a: 10
# Output: b: 20
# Output: a = 20, b = 10
```

### Sum, difference and average of two numbers
```python
x, y = map(int, input("Enter two numbers: ").split())
print("Sum:", x + y)
print("Difference:", x - y)
print("Average:", (x + y) / 2)
# User types: 10 4
# Output: Enter two numbers: 10 4
# Output: Sum: 14
# Output: Difference: 6
# Output: Average: 7.0
```

---

- [Back to practice index](../README.md)
