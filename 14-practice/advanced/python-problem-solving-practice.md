# Python Problem Solving Practice (Advanced)

Related notes: [OOP](../../09-object-oriented-programming/README.md), [Advanced Python](../../10-advanced-python/README.md), [Standard Library](../../11-useful-standard-library/README.md), [Testing and Code Quality](../../12-testing-and-code-quality/README.md)

> Advanced: harder problems that need a clear plan before coding. **Questions only, no solutions.** Write and run your own code, and test it on empty, single-item and large inputs.

## Practice Questions

1. Build a tiny **plugin system**: use a class decorator or `__init_subclass__` to register subclasses by name in a dictionary, and create instances by name.
2. Write a **descriptor** class `Positive` that validates attributes (`price`, `quantity`) on a class, and a version using `__set_name__`.
3. Implement `functools.lru_cache` yourself as a decorator that supports `maxsize` and `cache_info()` (hits, misses).
4. Write a decorator that works **with and without arguments**: both `@log` and `@log(level="INFO")` should work.
5. Create an **async** program with `asyncio` that downloads (simulate with `asyncio.sleep`) ten items concurrently with a limit of three at a time, using `asyncio.Semaphore`. Compare timing with a sequential version.
6. Use `concurrent.futures.ThreadPoolExecutor` and `ProcessPoolExecutor` to run an I/O-bound and a CPU-bound task. Measure and explain the difference.
7. Write a metaclass that prevents a class from defining a method called `delete`, and another that makes a class a singleton. Then explain when a class decorator would be simpler.
8. Implement a **linked-list-backed** `Deque` class with `__len__`, `__iter__`, `__getitem__`, `__contains__` and `__repr__`, following Python's data model.
9. Write a **context manager** that redirects `print` output into a string, supports nesting, and restores the original output on errors.
10. Implement a mini **event system**: `on(event, handler)`, `off(event, handler)` and `emit(event, *args)`. Handle exceptions in handlers without stopping the others.
11. Write a **property-based test** with `hypothesis` (if installed) or by hand with random inputs: for any list, `sorted(x)` is ordered, has the same length and the same items.
12. Parse arithmetic expressions such as `"3 + 4 * (2 - 1)"` into an abstract syntax tree using a recursive-descent parser, and evaluate it. No `eval`.
13. Write a **rate-limited, cached API client** class: caching with expiry, retry with exponential backoff, and logging. Use a fake function in place of real HTTP so it can be tested.
14. Use `logging` with a custom `Formatter`, a file handler and a console handler of different levels. Show how to log a traceback with `logger.exception`.
15. Write a **memory-efficient** version of a program that counts word frequency in a 1 GB file. Measure memory with `tracemalloc` for the naive and the streaming versions.
16. Design a small **inventory system** with classes, a JSON save/load layer, custom exceptions, type hints, unit tests and a command-line interface. Document the design decisions in a README.
17. Write a `__slots__` version of a class and compare memory use against the normal version for 100,000 instances.
18. Implement a **thread-safe counter** with and without `threading.Lock`. Show the race condition that happens without it.

---

- [Back to practice index](../README.md)
