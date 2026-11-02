# Python Problem Solving Practice (Intermediate)

Related notes: [OOP](../../09-object-oriented-programming/README.md), [Advanced Python](../../10-advanced-python/README.md), [Standard Library](../../11-useful-standard-library/README.md), [Testing and Code Quality](../../12-testing-and-code-quality/README.md)

> Intermediate: comfortable with the basics, now combining ideas. **Questions only, no solutions.** Write and run your own code, and test it on empty, single-item and large inputs.

## Practice Questions

1. Write a class `BankAccount` with `deposit`, `withdraw` and a balance that can never be set directly. Raise a custom `InsufficientFunds` exception, and add `__repr__` and `__eq__`.
2. Create a `Temperature` class with a `celsius` property and `fahrenheit` computed property, validating that the temperature is not below absolute zero.
3. Build a `Shape` base class with `area()` and subclasses `Circle`, `Rectangle` and `Triangle`. Use polymorphism to compute the total area of a mixed list.
4. Write a `@dataclass` called `Student` with `name`, `marks` (list) and a computed `average`. Sort a list of students by average, highest first.
5. Write a generator `read_chunks(path, size)` that yields a file in chunks. Explain why this uses constant memory.
6. Write an infinite generator `primes()` and use `itertools.islice` to print the first 20 primes.
7. Write a decorator `@timer` that prints how long a function took, preserving the function's name and docstring with `functools.wraps`.
8. Write a decorator `@retry(times=3)` that re-runs a function when it raises an exception, and re-raises the last error if all attempts fail.
9. Write a decorator `@memoize` yourself (without `functools.cache`) and test it on a recursive Fibonacci function.
10. Build a context manager (both with a class and with `contextlib.contextmanager`) that measures how long a block takes.
11. Write a closure `make_counter()` that returns a function increasing a private count each call. Use `nonlocal`.
12. Add type hints to five of your earlier functions, including `list[int]`, `dict[str, int]`, `X | None`, and `Callable`. Run `mypy` (if installed) and fix any reported problems.
13. Read a CSV file of sales with `csv.DictReader`, compute total sales per category, and write the summary to a new CSV file.
14. Parse a JSON file of users, filter the adults, sort by name, and save the result with `json.dump(..., indent=2)`.
15. Write a function that safely parses user input into an `int`, retries until valid, and handles `ValueError` and `KeyboardInterrupt` separately.
16. Use `collections.Counter` and regular expressions to list the 10 most common words in a text file (ignore case and punctuation).
17. Use `pathlib` to find all `.py` files under a folder, and report the number of lines in each, largest first.
18. Write unit tests with `unittest` or `pytest` for a `is_prime(n)` function. Cover `0`, `1`, `2`, negative numbers, large primes and non-primes. Check coverage with `pytest --cov` if installed.
19. Create a small package with `__init__.py`, two modules and a `__main__.py`, and run it with `python -m package_name`.
20. Write a command-line tool with `argparse` that takes a file name and options `--top N` and `--ignore-case` and prints the most common words.
21. Implement `__iter__` and `__next__` on a `Countdown` class, then reimplement it as a generator and compare.
22. Implement `__enter__` and `__exit__` on a class that temporarily changes the current working directory and always restores it, even if an error occurs.
23. Use `datetime` to calculate the number of days until the next birthday, and the day of the week a given date falls on.
24. Write a function that takes `*args` and `**kwargs`, prints them, and passes them through to another function. Add a keyword-only argument and a positional-only argument.

---

- [Back to practice index](../README.md)
