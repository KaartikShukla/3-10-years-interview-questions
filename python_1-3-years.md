## Basic Questions

**1. What is Python and what are its key features?**

Python is a high-level, dynamically typed, garbage-collected, general-purpose language with readable syntax. It supports procedural, object-oriented and functional styles, ships with a large standard library, and has a huge ecosystem (web, data, ML, automation). CPython compiles code to bytecode and runs it on a virtual machine.

**2. What are Python's built-in data types?**

- Numeric: `int`, `float`, `complex`, `bool`
- Text and binary: `str`, `bytes`, `bytearray`
- Sequences: `list`, `tuple`, `range`
- Mapping: `dict`; sets: `set`, `frozenset`
- `NoneType`

```python
print(type(3), type(3.0), type('a'), type([1]), type({'a': 1}), type(None))
```

**3. What is the difference between a list and a tuple?**

Lists are mutable and used for collections that change; tuples are immutable and used for fixed records. Tuples are slightly smaller and faster, and they are hashable when their items are, so they can be dict keys.

```python
nums = [1, 2, 3]
nums.append(4)          # fine
point = (1, 2)
point[0] = 9            # TypeError: tuple does not support item assignment
```

**4. What is the difference between mutable and immutable types?**

Mutable objects can change in place (`list`, `dict`, `set`). Immutable objects cannot (`int`, `float`, `str`, `tuple`, `frozenset`); 'modifying' them creates a new object. This matters when you pass objects to functions or share them between variables.

```python
a = [1, 2]; b = a
b.append(3)
print(a)        # [1, 2, 3] - same list

s = 'hi'; t = s
t += '!'
print(s)        # 'hi' - strings are immutable
```

**5. How do dictionaries work and which methods do you use most?**

A dict maps unique, hashable keys to values with O(1) average lookup, and keeps insertion order (guaranteed since 3.7). Common tools: `get`, `setdefault`, `items`, `pop`, `update`, and the `|` merge operator (3.9+).

```python
user = {'name': 'Ann', 'age': 30}
print(user.get('email', 'n/a'))        # 'n/a' - no KeyError
user.setdefault('role', 'dev')
merged = user | {'age': 31}            # new dict, right side wins
for key, value in user.items():
    print(key, value)
```

**6. What are sets and which operations do they support?**

A set is an unordered collection of unique hashable items with O(1) average membership tests. It supports union, intersection, difference and symmetric difference, and is the quickest way to remove duplicates.

```python
a, b = {1, 2, 3}, {3, 4}
print(a | b)   # {1, 2, 3, 4}
print(a & b)   # {3}
print(a - b)   # {1, 2}
print(a ^ b)   # {1, 2, 4}
print(set([1, 1, 2]))   # {1, 2}
```

**7. How do strings work? Name useful string methods.**

Strings are immutable sequences of Unicode characters, so they support indexing and slicing. Useful methods: `strip`, `lower`/`upper`, `split`, `join`, `replace`, `startswith`/`endswith`, `find`, `isdigit`. Build large strings with `''.join(...)` rather than repeated `+=`.

```python
s = '  Hello, World  '
print(s.strip().lower())            # 'hello, world'
print('-'.join(['a', 'b', 'c']))    # 'a-b-c'
print('a,b,c'.split(','))           # ['a', 'b', 'c']
print('Hello'[::-1])                # 'olleH'
```

**8. What are f-strings?**

f-strings (3.6+) embed expressions directly inside string literals. They are the most readable and fastest way to format strings, and support format specs and the `=` debug shortcut (3.8+).

```python
name, price = 'Tea', 3.14159
print(f'{name}: {price:.2f}')   # Tea: 3.14
print(f'{price=}')              # price=3.14159
print(f'{1_000_000:,}')         # 1,000,000
```

**9. What is the difference between `is` and `==`?**

`==` compares values (it calls `__eq__`); `is` checks whether two names point to the same object. Use `is` only for singletons such as `None`.

```python
a = [1, 2]; b = [1, 2]
print(a == b, a is b)   # True False
x = None
print(x is None)        # True
```

**10. What are truthy and falsy values? What is `None`?**

Falsy values: `None`, `False`, `0`, `0.0`, empty strings and empty containers. Everything else is truthy. `None` represents 'no value' and is what a function returns when it has no `return` statement.

```python
items = []
if not items:
    print('empty')
print(bool('0'), bool(0), bool(None))   # True False False
```

**11. How do loops and control flow work (`break`, `continue`, `pass`, `else`)?**

`break` exits the loop, `continue` skips to the next iteration, and `pass` is a no-op placeholder. A loop's `else` block runs only when the loop finishes without hitting `break`.

```python
for n in range(2, 10):
    for d in range(2, n):
        if n % d == 0:
            break
    else:
        print(n, 'is prime')    # 2 3 5 7
```

**12. How do `range`, `enumerate`, and `zip` work?**

`range` produces a lazy sequence of integers. `enumerate` gives `(index, item)` pairs. `zip` walks several iterables in parallel and stops at the shortest one.

```python
names, scores = ['Ann', 'Bob'], [90, 85]
for i, name in enumerate(names, start=1):
    print(i, name)
print(dict(zip(names, scores)))   # {'Ann': 90, 'Bob': 85}
```

**13. How do functions work? Cover default, keyword, and multiple return values.**

Functions are defined with `def`. Parameters can have defaults and can be passed by keyword. Returning several values actually returns a tuple, which you can unpack. A bare `*` forces the following parameters to be keyword-only.

```python
def stats(nums, *, round_to=2):
    total = sum(nums)
    return total, round(total / len(nums), round_to)

total, avg = stats([1, 2, 4], round_to=1)
print(total, avg)   # 7 2.3
```

**14. What are `*args` and `**kwargs`?**

`*args` collects extra positional arguments into a tuple and `**kwargs` collects extra keyword arguments into a dict. They also unpack sequences and dicts when calling a function.

```python
def show(*args, **kwargs):
    print(args, kwargs)

show(1, 2, name='Ann')          # (1, 2) {'name': 'Ann'}
show(*[1, 2], **{'x': 1})       # (1, 2) {'x': 1}
```

**15. What is the mutable default argument pitfall?**

Default values are evaluated once when the function is defined, so a mutable default (list, dict) is shared between calls. Use `None` as the default and create the object inside the function.

```python
def bad(x, bucket=[]):
    bucket.append(x)
    return bucket

bad(1); print(bad(2))   # [1, 2] - surprise

def good(x, bucket=None):
    bucket = [] if bucket is None else bucket
    bucket.append(x)
    return bucket
```

**16. Explain variable scope (LEGB) and closures.**

Python looks up names in this order: Local, Enclosing, Global, Built-in. Use `global` to rebind a module-level name and `nonlocal` to rebind a name from an enclosing function. A closure is an inner function that remembers variables from the enclosing function after it has returned.

```python
def counter():
    count = 0
    def inc():
        nonlocal count
        count += 1
        return count
    return inc

nxt = counter()
print(nxt(), nxt())   # 1 2
```

**17. What are list, dict, and set comprehensions?**

Comprehensions build collections in a single readable expression, optionally with a condition. A generator expression (round brackets) does the same lazily without building the whole list.

```python
squares = [n * n for n in range(6) if n % 2 == 0]   # [0, 4, 16]
lengths = {w: len(w) for w in ['a', 'bb']}           # {'a': 1, 'bb': 2}
unique = {c.lower() for c in 'Hello'}                # {'h', 'e', 'l', 'o'}
total = sum(n * n for n in range(1000))              # generator, no list
```

**18. What are `lambda`, `map`, and `filter`?**

`lambda` creates a small anonymous function from a single expression. `map` applies a function to each item and `filter` keeps items for which the function is true; both return lazy iterators. Comprehensions are usually more readable.

```python
nums = [1, 2, 3, 4]
print(list(map(lambda x: x * 2, nums)))      # [2, 4, 6, 8]
print(list(filter(lambda x: x % 2, nums)))   # [1, 3]
print([x * 2 for x in nums])                 # preferred style
```

**19. How does exception handling work? How do you create custom exceptions?**

`try` holds the risky code, `except` handles specific errors, `else` runs if nothing failed, and `finally` always runs for cleanup. Raise errors with `raise`, and define custom exceptions by subclassing `Exception`. Catch specific exceptions rather than using a bare `except:`.

```python
class InsufficientFunds(Exception):
    pass

def withdraw(balance, amount):
    if amount > balance:
        raise InsufficientFunds(f'need {amount - balance} more')
    return balance - amount

try:
    withdraw(10, 50)
except InsufficientFunds as e:
    print('error:', e)
finally:
    print('done')
```

**20. What are modules and packages? What does `if __name__ == '__main__'` do?**

A module is a `.py` file and a package is a folder of modules. You load them with `import`. `__name__` equals `'__main__'` only when the file is run directly, so the guard keeps script code from running when the file is imported.

```python
# utils.py
def greet(name):
    return f'Hello, {name}'

if __name__ == '__main__':
    print(greet('Ann'))      # runs only via: python utils.py

# elsewhere
from utils import greet
```

## Intermediate Questions

**21. What is the difference between an iterable and an iterator?**

An iterable can produce an iterator (it has `__iter__`): lists, strings, dicts, files. An iterator produces items one at a time via `__next__` and raises `StopIteration` when finished; it can only be consumed once. A `for` loop calls `iter()` and then `next()` for you.

```python
it = iter([10, 20])
print(next(it), next(it))   # 10 20
next(it)                    # StopIteration
```

**22. What are generators and why use them?**

A function with `yield` returns a generator, which produces values lazily and remembers its state between them. Generators keep memory use low for large or endless streams.

```python
def read_squares(limit):
    for n in range(limit):
        yield n * n

gen = read_squares(1_000_000)    # nothing computed yet
print(next(gen), next(gen))      # 0 1
```

**23. What is a decorator?**

A decorator is a function that takes another function and returns an enhanced version, used for logging, timing, caching, authentication and more. `@decorator` above a function is shorthand for `func = decorator(func)`. Use `functools.wraps` to preserve the original function's name and docstring.

```python
from functools import wraps
import time

def timer(fn):
    @wraps(fn)
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        result = fn(*args, **kwargs)
        print(f'{fn.__name__} took {time.perf_counter() - start:.4f}s')
        return result
    return wrapper

@timer
def work():
    return sum(range(10**6))
```

**24. How does memory management work in Python?**

CPython manages memory automatically. Every object has a reference count and is freed when the count reaches zero. A cyclic garbage collector (the `gc` module) cleans up reference cycles that counting cannot free. `del name` only removes a name binding; the object is freed when nothing else refers to it.

```python
import sys, gc

x = [1, 2, 3]
y = x
print(sys.getrefcount(x))   # count includes the temporary argument reference
del y
gc.collect()                # force a cycle collection if you ever need to
```

**25. What is the difference between a shallow copy and a deep copy?**

A shallow copy creates a new outer container but reuses the inner objects. A deep copy recursively copies everything, so nested objects become independent.

```python
import copy

original = [[1, 2], [3, 4]]
shallow = copy.copy(original)
deep = copy.deepcopy(original)
original[0].append(99)
print(shallow[0])   # [1, 2, 99] - shared inner list
print(deep[0])      # [1, 2]     - independent
```

**26. What are context managers and the `with` statement?**

A context manager guarantees setup and cleanup around a block of code, even if an exception occurs. It is used for files, locks, database connections and temporary state. Classes implement `__enter__` and `__exit__`, or you write one with `contextlib.contextmanager`.

```python
from contextlib import contextmanager
import time

@contextmanager
def timer(label):
    start = time.perf_counter()
    try:
        yield
    finally:
        print(f'{label}: {time.perf_counter() - start:.3f}s')

with timer('sum'):
    sum(range(10**6))
```

**27. How do you read and write files, JSON, and CSV?**

Use `with open(...)` (or `pathlib.Path`) so files are always closed, and pass `encoding='utf-8'`. The `json` module converts between Python objects and JSON text; the `csv` module reads and writes tabular data, and `csv.DictReader` gives each row as a dict.

```python
import json, csv
from pathlib import Path

path = Path('data.json')
path.write_text(json.dumps({'name': 'Ann', 'skills': ['py', 'sql']}), encoding='utf-8')
data = json.loads(path.read_text(encoding='utf-8'))
print(data['skills'])      # ['py', 'sql']

with open('people.csv', newline='', encoding='utf-8') as f:
    for row in csv.DictReader(f):
        print(row['name'])
```

**28. What are classes and objects? What do `__init__` and `self` mean?**

A class is a blueprint and an object is an instance of it. `__init__` is the initializer that runs when an instance is created, and `self` is the instance the method is working on (passed automatically).

```python
class Dog:
    def __init__(self, name):
        self.name = name
    def speak(self):
        return f'{self.name} says woof'

rex = Dog('Rex')
print(rex.speak())   # Rex says woof
```

**29. What is the difference between class attributes, instance attributes, `@classmethod`, and `@staticmethod`?**

Class attributes are shared by all instances, while instance attributes belong to one object. A `@classmethod` receives the class (`cls`) and is often used as an alternative constructor. A `@staticmethod` receives neither and is just a function grouped inside the class.

```python
class User:
    count = 0                      # class attribute

    def __init__(self, name):
        self.name = name           # instance attribute
        User.count += 1

    @classmethod
    def from_email(cls, email):
        return cls(email.split('@')[0])

    @staticmethod
    def is_valid(name):
        return name.isidentifier()

u = User.from_email('ann@example.com')
print(u.name, User.count)   # ann 1
```

**30. How do inheritance, `super()`, and the MRO work?**

A subclass inherits attributes and methods from its parent and can override them. `super()` calls the next class in the Method Resolution Order (MRO), which Python computes for multiple inheritance; you can inspect it with `ClassName.__mro__`.

```python
class Animal:
    def speak(self):
        return '...'

class Dog(Animal):
    def speak(self):
        return super().speak() + ' woof'

print(Dog().speak())    # ... woof
print(Dog.__mro__)      # Dog, Animal, object
```

**31. What are dunder methods? What is the difference between `__str__` and `__repr__`?**

Dunder (double underscore) methods let your objects work with Python syntax and built-ins, for example `__len__`, `__eq__`, `__add__`. `__repr__` is a precise developer-facing string; `__str__` is a friendly user-facing string, and `print()` uses `__str__` first.

```python
class Point:
    def __init__(self, x, y):
        self.x, self.y = x, y
    def __repr__(self):
        return f'Point({self.x}, {self.y})'
    def __eq__(self, other):
        return (self.x, self.y) == (other.x, other.y)

print(Point(1, 2), Point(1, 2) == Point(1, 2))   # Point(1, 2) True
```

**32. What is `@property` and why use it?**

`@property` lets a method be accessed like an attribute, so you can add validation or computed values without changing how callers use the class. A `.setter` controls assignment.

```python
class Account:
    def __init__(self, balance):
        self._balance = balance

    @property
    def balance(self):
        return self._balance

    @balance.setter
    def balance(self, value):
        if value < 0:
            raise ValueError('balance cannot be negative')
        self._balance = value

acc = Account(100)
acc.balance = 150
print(acc.balance)   # 150
```

**33. What are abstract classes and duck typing?**

Duck typing means an object is used based on the methods it has, not its declared type. Abstract base classes (`abc.ABC`) define an interface that subclasses must implement; trying to instantiate a class with unimplemented abstract methods raises `TypeError`.

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self): ...

class Square(Shape):
    def __init__(self, side):
        self.side = side
    def area(self):
        return self.side ** 2

print(Square(3).area())   # 9
Shape()                   # TypeError
```

**34. What are dataclasses?**

`@dataclass` generates `__init__`, `__repr__` and `__eq__` from annotated fields, removing boilerplate for classes that mainly hold data. Use `field(default_factory=list)` for mutable defaults and `frozen=True` for immutable instances.

```python
from dataclasses import dataclass, field

@dataclass
class Order:
    id: int
    items: list[str] = field(default_factory=list)

o = Order(1)
o.items.append('tea')
print(o)   # Order(id=1, items=['tea'])
```

**35. What are type hints? Does Python enforce them?**

Type hints document the expected types of variables, parameters and return values. The interpreter does not enforce them; tools like mypy or pyright check them statically, and your editor uses them for autocomplete.

```python
def total_price(prices: list[float], tax: float = 0.0) -> float:
    return sum(prices) * (1 + tax)

def find_user(user_id: int) -> str | None:     # 3.10+ union syntax
    return None
```

**36. Which `collections` types should you know?**

- `Counter`: counts items
- `defaultdict`: gives missing keys a default value
- `deque`: fast appends and pops at both ends
- `namedtuple`: lightweight named records
- `OrderedDict`: dict with `move_to_end` helpers

```python
from collections import Counter, defaultdict, deque

print(Counter('banana'))                  # Counter({'a': 3, 'n': 2, 'b': 1})
groups = defaultdict(list)
groups['fruits'].append('apple')
queue = deque([1, 2, 3])
queue.appendleft(0)
print(queue)                              # deque([0, 1, 2, 3])
```

**37. What are `itertools` and `functools` used for?**

`itertools` offers memory-efficient iteration helpers such as `chain`, `islice`, `groupby`, `product` and `combinations`. `functools` offers `lru_cache` (memoization), `partial` (pre-filled arguments), `reduce` and `wraps`.

```python
from functools import lru_cache, partial
from itertools import chain, combinations

@lru_cache(maxsize=None)
def fib(n):
    return n if n < 2 else fib(n - 1) + fib(n - 2)

print(fib(50))                                    # 12586269025
print(list(chain([1, 2], [3])))                   # [1, 2, 3]
print(list(combinations('abc', 2)))               # [('a','b'), ('a','c'), ('b','c')]
print(partial(int, base=2)('101'))                # 5
```

**38. How do `sorted()` and `list.sort()` differ? How does `key` work?**

`list.sort()` sorts in place and returns `None`; `sorted()` returns a new list from any iterable. The `key` function turns each item into the value used for comparison. Sorting is stable, so equal items keep their original order.

```python
people = [('Ann', 30), ('Bob', 25), ('Cy', 30)]
print(sorted(people, key=lambda p: p[1]))                   # by age
print(sorted(people, key=lambda p: (-p[1], p[0])))          # age desc, then name
print(sorted(['b', 'A', 'c'], key=str.lower, reverse=True)) # ['c', 'b', 'A']
```

**39. What are virtual environments and how do you manage packages?**

A virtual environment gives each project its own isolated Python packages so versions do not clash. Create one with `python -m venv .venv`, install packages with `pip`, and record dependencies in `requirements.txt` (or `pyproject.toml` with tools such as Poetry or uv).

```bash
python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install requests pytest
pip freeze > requirements.txt
pip install -r requirements.txt
```

**40. What is PEP 8 and what makes code 'Pythonic'?**

PEP 8 is the style guide: `snake_case` for functions and variables, `CapWords` for classes, 4-space indentation, sensible line lengths, and imports at the top. Pythonic code favors readability: use `enumerate` instead of index loops, unpacking for swaps, comprehensions, `with` for resources, and EAFP ('easier to ask forgiveness than permission') over many pre-checks. Tools such as Ruff, Black and flake8 enforce it.

```python
items = ['a', 'b', 'c']

# Not Pythonic
for i in range(len(items)):
    print(i, items[i])

# Pythonic
for i, item in enumerate(items):
    print(i, item)

a, b = 1, 2
a, b = b, a                    # swap without a temp variable

try:                           # EAFP
    value = {'x': 1}['y']
except KeyError:
    value = 0
```

## Applied and Coding Questions

**41. What is the GIL? When do you use threads vs processes?**

The Global Interpreter Lock lets only one thread run Python bytecode at a time in standard CPython. Threads still help with I/O-bound work (network, disk) because the lock is released while waiting. For CPU-bound work use `multiprocessing` (or `ProcessPoolExecutor`) to run in separate processes.

```python
from concurrent.futures import ThreadPoolExecutor
import time

def fetch(n):
    time.sleep(1)               # stands in for a network call
    return n

with ThreadPoolExecutor(max_workers=5) as pool:
    print(list(pool.map(fetch, range(5))))   # ~1 second, not 5
```

**42. What are `async` and `await`? When would you use `asyncio`?**

`async def` defines a coroutine and `await` pauses it while waiting, letting the event loop run other tasks on the same thread. Use `asyncio` for many concurrent I/O operations (HTTP calls, sockets, databases) when async-aware libraries are available.

```python
import asyncio

async def fetch(i):
    await asyncio.sleep(1)
    return i

async def main():
    results = await asyncio.gather(*(fetch(i) for i in range(5)))
    print(results)               # ~1 second total

asyncio.run(main())
```

**43. How do you write tests in Python?**

`unittest` is built in and class-based. `pytest` is the popular choice: plain functions with `assert`, fixtures for setup, and `parametrize` for many inputs. Name files `test_*.py` and run `pytest`.

```python
import pytest

def divide(a, b):
    return a / b

@pytest.mark.parametrize('a,b,expected', [(6, 3, 2), (5, 2, 2.5)])
def test_divide(a, b, expected):
    assert divide(a, b) == expected

def test_divide_by_zero():
    with pytest.raises(ZeroDivisionError):
        divide(1, 0)
```

**44. How do you use the `logging` module?**

Use `logging` instead of `print` for diagnostics. Create a logger per module, set the level once at startup, and use `debug`, `info`, `warning`, `error` and `exception` (which logs the traceback). Pass values as arguments rather than building the string yourself.

```python
import logging

logging.basicConfig(level=logging.INFO,
                    format='%(asctime)s %(levelname)s %(name)s: %(message)s')
log = logging.getLogger(__name__)

log.info('Processing %s items', 3)
try:
    1 / 0
except ZeroDivisionError:
    log.exception('Calculation failed')
```

**45. How do you call a REST API from Python?**

Use the `requests` library (`pip install requests`). Always set a `timeout`, call `raise_for_status()` to turn HTTP errors into exceptions, and read the body with `.json()`.

```python
import requests

resp = requests.get('https://api.github.com/repos/python/cpython', timeout=10)
resp.raise_for_status()
data = resp.json()
print(data['name'], data['stargazers_count'])

created = requests.post('https://httpbin.org/post', json={'name': 'Ann'}, timeout=10)
print(created.status_code)
```

**46. How do you work with a database safely in Python?**

Use a driver such as `sqlite3` (built in), `psycopg` or an ORM like SQLAlchemy. Always use parameterized queries (placeholders), never string formatting, to prevent SQL injection, and commit or roll back transactions explicitly.

```python
import sqlite3

conn = sqlite3.connect(':memory:')
conn.execute('CREATE TABLE users (id INTEGER PRIMARY KEY, name TEXT)')
conn.execute('INSERT INTO users (name) VALUES (?)', ('Ann',))
conn.commit()

for row in conn.execute('SELECT * FROM users WHERE name = ?', ('Ann',)):
    print(row)               # (1, 'Ann')
conn.close()
```

**47. Coding: reverse a string and check for a palindrome.**

Slicing reverses a string in one step. For a robust palindrome check, ignore case and non-alphanumeric characters.

```python
def reverse(s):
    return s[::-1]

def is_palindrome(s):
    cleaned = [c.lower() for c in s if c.isalnum()]
    return cleaned == cleaned[::-1]

print(reverse('python'))                              # nohtyp
print(is_palindrome('A man, a plan, a canal: Panama'))   # True
```

**48. Coding: write FizzBuzz and count word frequencies.**

FizzBuzz checks divisibility by 15 first. `collections.Counter` is the cleanest way to count words.

```python
from collections import Counter

for i in range(1, 16):
    if i % 15 == 0:
        print('FizzBuzz')
    elif i % 3 == 0:
        print('Fizz')
    elif i % 5 == 0:
        print('Buzz')
    else:
        print(i)

text = 'the cat and the hat'
print(Counter(text.split()).most_common(2))   # [('the', 2), ('cat', 1)]
```

**49. Coding: find duplicates in a list and solve two-sum.**

Both are solved in one pass with a set or dict, giving O(n) time.

```python
def find_duplicates(items):
    seen, dups = set(), set()
    for x in items:
        if x in seen:
            dups.add(x)
        seen.add(x)
    return dups

def two_sum(nums, target):
    seen = {}                        # value -> index
    for i, n in enumerate(nums):
        if target - n in seen:
            return seen[target - n], i
        seen[n] = i

print(find_duplicates([1, 2, 3, 2, 4, 1]))   # {1, 2}
print(two_sum([2, 7, 11, 15], 9))            # (0, 1)
```

**50. Coding: flatten a nested list and remove duplicates while keeping order.**

A recursive generator flattens any depth. `dict.fromkeys` removes duplicates and keeps the first-seen order because dicts preserve insertion order.

```python
def flatten(items):
    for x in items:
        if isinstance(x, list):
            yield from flatten(x)
        else:
            yield x

def dedupe(items):
    return list(dict.fromkeys(items))

print(list(flatten([1, [2, [3, 4]], 5])))   # [1, 2, 3, 4, 5]
print(dedupe([3, 1, 3, 2, 1]))              # [3, 1, 2]
```

---

