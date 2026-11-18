
## Basic Questions

**1. What is Python and what are its key features?**

Python is a high-level, dynamically typed, garbage-collected, general-purpose language known for readable syntax. It is multi-paradigm (procedural, object-oriented, functional), has a very large standard library and ecosystem (web, data, ML, automation, DevOps), and is executed by an interpreter/VM.

**2. Is Python compiled or interpreted?**

Both. CPython compiles source to bytecode (cached in `__pycache__/*.pyc`), and the Python Virtual Machine then executes that bytecode. Other implementations include PyPy (JIT), Jython, IronPython and MicroPython.

```python
import dis
dis.dis(lambda x: x + 1)   # shows the bytecode
```

**3. What are Python's built-in data types?**

- Numeric: `int`, `float`, `complex`, `bool`
- Text and binary: `str`, `bytes`, `bytearray`, `memoryview`
- Sequences: `list`, `tuple`, `range`
- Mapping: `dict`; sets: `set`, `frozenset`
- `NoneType`

**4. What is the difference between mutable and immutable types?**

Mutable objects can change in place (`list`, `dict`, `set`, `bytearray`, most user-defined classes). Immutable objects cannot (`int`, `float`, `str`, `tuple`, `frozenset`, `bytes`). Immutable objects are safe to share and, when all their contents are hashable, can be used as dict keys.

```python
a = [1, 2]; b = a
b.append(3)
print(a)        # [1, 2, 3] - same object

s = 'hi'; s2 = s
s2 += '!'
print(s)        # 'hi' - a new string was created
```

**5. What is the difference between list, tuple, set, and dict?**

- `list`: ordered, mutable, allows duplicates
- `tuple`: ordered, immutable, good for fixed records
- `set`: unordered, unique hashable items, O(1) average membership test
- `dict`: key → value mapping, insertion-ordered (guaranteed since 3.7), O(1) average lookup

```python
point = (1, 2)
unique = {1, 2, 2, 3}            # {1, 2, 3}
user = {'id': 1, 'name': 'Ann'}
```

**6. How does a dictionary work internally?**

A dict is a hash table with open addressing. The key's hash selects a slot in a sparse index table that points into a compact entries array (which is what preserves insertion order). Average get/set/delete is O(1); worst case is O(n) with heavy collisions. Keys must be hashable.

```python
d = {}
d[[1, 2]] = 'x'   # TypeError: unhashable type: 'list'
d[(1, 2)] = 'x'   # OK
```

**7. What is the difference between `is` and `==`?**

`==` compares values (calls `__eq__`). `is` compares identity (same object in memory). Use `is` for `None` and other singletons.

```python
a = [1, 2]; b = [1, 2]
print(a == b)    # True
print(a is b)    # False
x = None
print(x is None) # idiomatic
```

**8. Does Python pass arguments by value or by reference?**

Neither, strictly. It is pass-by-object-reference (pass by assignment): the parameter name is bound to the same object. Mutating a mutable argument is visible to the caller; rebinding the local name is not.

```python
def f(lst, n):
    lst.append(1)   # visible to caller
    n += 1          # rebinds the local name only

nums, k = [], 0
f(nums, k)
print(nums, k)      # [1] 0
```

**9. What are `*args` and `**kwargs`?**

`*args` collects extra positional arguments into a tuple; `**kwargs` collects extra keyword arguments into a dict. They also unpack iterables/dicts at call time. Parameters after a bare `*` are keyword-only; parameters before `/` are positional-only.

```python
def f(a, /, b, *args, c, **kwargs):
    print(a, b, args, c, kwargs)

f(1, 2, 3, 4, c=5, d=6)   # 1 2 (3, 4) 5 {'d': 6}
```

**10. What is the mutable default argument pitfall?**

Default values are evaluated once, when the function is defined, so a mutable default is shared between calls. Use `None` as a sentinel.

```python
def bad(x, acc=[]):
    acc.append(x)
    return acc

bad(1); print(bad(2))   # [1, 2] - surprise!

def good(x, acc=None):
    acc = [] if acc is None else acc
    acc.append(x)
    return acc
```

**11. Explain the LEGB rule, `global`, and `nonlocal`.**

Name lookup order is Local → Enclosing → Global → Built-in. `global` lets a function rebind a module-level name; `nonlocal` rebinds a name in the nearest enclosing function scope.

```python
def outer():
    x = 'enclosing'
    def inner():
        nonlocal x
        x = 'changed'
    inner()
    return x            # 'changed'
```

**12. What are comprehensions? How do they differ from generator expressions?**

Comprehensions build lists, dicts or sets concisely. A generator expression uses parentheses and is lazy: it produces one item at a time and uses O(1) memory.

```python
squares = [n * n for n in range(5)]            # list
lookup  = {n: n * n for n in range(5)}         # dict
evens   = {n for n in range(10) if n % 2 == 0} # set
total   = sum(n * n for n in range(10**6))     # no big list built
```

**13. How does slicing work?**

`seq[start:stop:step]` — `stop` is exclusive, negative indices count from the end, and slicing a list returns a new (shallow) copy. Out-of-range slices never raise.

```python
s = [0, 1, 2, 3, 4, 5]
s[1:4]    # [1, 2, 3]
s[::-1]   # reversed copy
s[::2]    # [0, 2, 4]
s[-2:]    # [4, 5]
```

**14. What are f-strings and how do they compare to `format()` and `%`?**

f-strings (3.6+) evaluate expressions inline and are the fastest and most readable option. They support format specs, the `=` debug specifier (3.8), and since 3.12 can reuse the same quote type inside expressions.

```python
name, price = 'Tea', 3.14159
print(f'{name!r}: {price:.2f}')   # 'Tea': 3.14
print(f'{price=}')                # price=3.14159
```

**15. How do `range`, `enumerate`, and `zip` work?**

`range` is a lazy, immutable sequence of ints. `enumerate` yields `(index, item)` pairs. `zip` pairs items from several iterables and stops at the shortest (use `strict=True` on 3.10+ to raise on length mismatch, or `itertools.zip_longest`).

```python
for i, ch in enumerate('abc', start=1):
    print(i, ch)

print(dict(zip(['a', 'b'], [1, 2])))   # {'a': 1, 'b': 2}
```

**16. What are truthy and falsy values? What is `None`?**

Falsy: `None`, `False`, numeric zero, empty containers/strings, and objects whose `__bool__` returns `False` or `__len__` returns 0. Everything else is truthy. `None` is the singleton meaning 'no value'; a function with no `return` returns `None`.

```python
if not []:
    print('empty list is falsy')
print(bool(None), bool('0'))   # False True
```

**17. How does `try / except / else / finally` work?**

`try` runs the code; `except` handles specific exceptions; `else` runs only if nothing was raised; `finally` always runs (cleanup), even if the block returns. Catch specific exceptions and avoid a bare `except:`.

```python
try:
    n = int('42')
except ValueError as e:
    print('bad input', e)
else:
    print('parsed', n)
finally:
    print('always runs')
```

**18. What does `if __name__ == '__main__':` do?**

`__name__` is `'__main__'` when the file is executed as a script and the module's dotted name when it is imported. The guard prevents script-only code from running on import, and it is mandatory for `multiprocessing` on platforms that use the `spawn` start method.

```python
def main():
    print('running as a script')

if __name__ == '__main__':
    main()
```

**19. What is the difference between a module and a package? How does importing work?**

A module is a single `.py` file; a package is a directory of modules (`__init__.py` is conventional, optional for namespace packages). `import` checks `sys.modules`, then asks the finders on `sys.meta_path` (searching `sys.path`), executes the module once, and caches it. Prefer absolute imports; use relative imports inside packages.

```python
import os.path
from collections import defaultdict
from . import utils        # relative import inside a package
```

**20. What are `lambda`, `map`, `filter`, and `reduce`?**

`lambda` creates a small anonymous single-expression function. `map` and `filter` return lazy iterators; `reduce` (in `functools`) folds a sequence into one value. Comprehensions are usually more Pythonic.

```python
from functools import reduce
nums = [1, 2, 3, 4]
print(list(map(lambda x: x * 2, nums)))      # [2, 4, 6, 8]
print(list(filter(lambda x: x % 2, nums)))   # [1, 3]
print(reduce(lambda a, b: a + b, nums))      # 10
```

## Intermediate Questions

**21. What is a decorator?**

A decorator is a callable that takes a function (or class) and returns a replacement, typically adding behavior such as logging, auth, caching or timing. `@dec` is sugar for `f = dec(f)`.

```python
import time

def timer(fn):
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

**22. How do you write a decorator that takes arguments? Why use `functools.wraps`?**

It needs three levels: a factory that takes the arguments, the decorator, and the wrapper. `functools.wraps` copies `__name__`, `__doc__` and sets `__wrapped__`, so introspection, debugging and docs still work.

```python
from functools import wraps

def repeat(n):
    def decorator(fn):
        @wraps(fn)
        def wrapper(*a, **kw):
            for _ in range(n):
                result = fn(*a, **kw)
            return result
        return wrapper
    return decorator

@repeat(3)
def hi():
    print('hi')
```

**23. What is a closure?**

A closure is a function that remembers variables from its enclosing scope after the outer function has returned (stored in `__closure__` cells). Watch out for late binding inside loops.

```python
def counter():
    c = 0
    def inc():
        nonlocal c
        c += 1
        return c
    return inc

nxt = counter()
print(nxt(), nxt())                  # 1 2

fs = [lambda: i for i in range(3)]
print([f() for f in fs])             # [2, 2, 2] - late binding
fs = [lambda i=i: i for i in range(3)]
print([f() for f in fs])             # [0, 1, 2] - fixed
```

**24. What are generators and what does `yield` do?**

A function containing `yield` returns a generator: a lazy iterator that pauses at each `yield` and resumes with its local state intact. It is ideal for large or infinite streams because values are produced on demand.

```python
from itertools import islice

def fib():
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b

print(list(islice(fib(), 8)))   # [0, 1, 1, 2, 3, 5, 8, 13]
```

**25. What is the difference between an iterable and an iterator?**

An iterable has `__iter__` returning an iterator (list, str, dict, file). An iterator has `__next__` (and `__iter__` returning itself), raises `StopIteration` when exhausted, and is single-pass. A `for` loop calls `iter()` once and then `next()` repeatedly.

```python
class Countdown:
    def __init__(self, n):
        self.n = n
    def __iter__(self):
        return self
    def __next__(self):
        if self.n <= 0:
            raise StopIteration
        self.n -= 1
        return self.n + 1

print(list(Countdown(3)))   # [3, 2, 1]
```

**26. What is the difference between a shallow copy and a deep copy?**

A shallow copy (`copy.copy`, `list.copy()`, `lst[:]`) creates a new container but shares the nested objects. A deep copy (`copy.deepcopy`) recursively copies everything and handles cycles via a memo.

```python
import copy
a = [[1, 2], [3]]
s = copy.copy(a)
d = copy.deepcopy(a)
a[0].append(99)
print(s[0], d[0])    # [1, 2, 99] [1, 2]
```

**27. What are context managers?**

Objects with `__enter__` and `__exit__` that guarantee setup/teardown inside a `with` block (files, locks, DB transactions). `__exit__` receives exception details and can suppress the exception by returning `True`. `contextlib.contextmanager` builds one from a generator.

```python
from contextlib import contextmanager

@contextmanager
def transaction(conn):
    try:
        yield conn
        conn.commit()
    except Exception:
        conn.rollback()
        raise
```

**28. What is the difference between class attributes and instance attributes?**

Class attributes live on the class and are shared by all instances; instance attributes live in each instance's `__dict__`. Assigning through an instance creates a new instance attribute that shadows the class one. Mutable class attributes are a classic bug.

```python
class A:
    tags = []                # shared by every instance!
    def __init__(self):
        self.n = 0           # per instance

a, b = A(), A()
a.tags.append('x')
print(b.tags)                # ['x']
```

**29. Explain `@staticmethod`, `@classmethod`, and instance methods.**

Instance methods receive `self`. `@classmethod` receives `cls` (alternate constructors; works correctly with inheritance). `@staticmethod` receives nothing and is just a function namespaced inside the class.

```python
class User:
    def __init__(self, name):
        self.name = name

    @classmethod
    def from_email(cls, email):
        return cls(email.split('@')[0])

    @staticmethod
    def is_valid(name):
        return name.isidentifier()
```

**30. How do inheritance, `super()`, and the MRO work?**

Python supports multiple inheritance. The Method Resolution Order is computed with C3 linearization (children before parents, left-to-right order preserved). `super()` follows the MRO of the instance's class, not just 'the parent'. Inspect it with `Cls.__mro__`.

```python
class A:
    def hi(self): print('A')
class B(A):
    def hi(self): print('B'); super().hi()
class C(A):
    def hi(self): print('C'); super().hi()
class D(B, C):
    pass

D().hi()            # B, C, A
print(D.__mro__)    # D, B, C, A, object
```

**31. What are dunder (magic) methods? `__str__` vs `__repr__`?**

Dunder methods hook your objects into syntax and built-ins (`__len__`, `__add__`, `__eq__`, `__enter__`, ...). `__repr__` is unambiguous and aimed at developers (ideally eval-able); `__str__` is readable and aimed at users. `print()` uses `__str__` and falls back to `__repr__`.

```python
class P:
    def __init__(self, x, y):
        self.x, self.y = x, y
    def __repr__(self):
        return f'P({self.x}, {self.y})'
    def __add__(self, o):
        return P(self.x + o.x, self.y + o.y)

print(P(1, 2) + P(3, 4))   # P(4, 6)
```

**32. What are `@property` and name mangling? How does Python do encapsulation?**

`@property` exposes computed or validated attributes with plain attribute syntax. Python has no true private members: `_x` means 'internal by convention', and `__x` is name-mangled to `_Class__x` to avoid clashes in subclasses (not for security).

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
            raise ValueError('negative balance')
        self._balance = value
```

**33. What are abstract base classes, duck typing, and polymorphism?**

Duck typing cares about behavior rather than declared type. Abstract base classes (`abc`) enforce an interface: a subclass cannot be instantiated until every abstract method is implemented. Polymorphism means one interface with many implementations.

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self) -> float: ...

class Square(Shape):
    def __init__(self, s):
        self.s = s
    def area(self):
        return self.s ** 2

Shape()          # TypeError: can't instantiate abstract class
print(Square(3).area())   # 9
```

**34. What are dataclasses?**

`@dataclass` (3.7+) generates `__init__`, `__repr__` and `__eq__` (and optionally ordering/hash) from annotated fields. Useful options: `frozen=True`, `slots=True` and `kw_only=True` (3.10+), and `field(default_factory=...)` for mutable defaults.

```python
from dataclasses import dataclass, field

@dataclass(slots=True)
class Cart:
    owner: str
    items: list[str] = field(default_factory=list)

c = Cart('Ann'); c.items.append('tea')
print(c)   # Cart(owner='Ann', items=['tea'])
```

**35. How do type hints work? Are they enforced at runtime?**

Annotations are metadata and are not enforced by the interpreter; static checkers (mypy, pyright) verify them. Modern syntax includes `list[int]` and `X | None` (3.10+). Libraries like Pydantic and FastAPI do read annotations at runtime.

```python
def greet(name: str, times: int = 1) -> str | None:
    return name * times if times else None

greet(123)   # runs fine; mypy would flag it
```

**36. Name the most useful types in the `collections` module.**

- `Counter`: frequency counts
- `defaultdict`: creates missing values automatically
- `deque`: O(1) appends and pops at both ends
- `namedtuple`: lightweight immutable records
- `ChainMap` and `OrderedDict` (mostly for `move_to_end`)

```python
from collections import Counter, defaultdict, deque

print(Counter('banana').most_common(1))   # [('a', 3)]
g = defaultdict(list); g['k'].append(1)
q = deque([1, 2, 3], maxlen=3); q.append(4)
print(q)                                  # deque([2, 3, 4], maxlen=3)
```

**37. What do `itertools` and `functools` offer?**

`itertools` provides lazy building blocks: `chain`, `islice`, `groupby`, `product`, `permutations`, `combinations`, `accumulate`, `zip_longest`, and `batched` (3.12+). `functools` provides `partial`, `reduce`, `lru_cache`/`cache`, `wraps`, `cached_property`, and `singledispatch`.

```python
from itertools import combinations, batched
from functools import partial

print(list(combinations('abc', 2)))      # [('a','b'), ('a','c'), ('b','c')]
print(list(batched(range(5), 2)))        # [(0, 1), (2, 3), (4,)]
to_int = partial(int, base=2)
print(to_int('101'))                     # 5
```

**38. How do you work with files and paths?**

Use `with open(...)` or `Path.open()` so files always close, pass an explicit `encoding`, and use `pathlib.Path` for OS-independent paths. Iterate line by line for large files instead of reading everything.

```python
from pathlib import Path

p = Path('data') / 'log.txt'
p.parent.mkdir(exist_ok=True)
p.write_text('hello', encoding='utf-8')

with p.open(encoding='utf-8') as f:
    for line in f:          # lazy, constant memory
        print(line.strip())
```

**39. How do you define custom exceptions and chain them?**

Subclass `Exception` (not `BaseException`). Use `raise NewError(...) from err` to keep the causal chain in `__cause__`, or `from None` to suppress the original context.

```python
class PaymentError(Exception):
    def __init__(self, msg, code):
        super().__init__(msg)
        self.code = code

try:
    int('x')
except ValueError as e:
    raise PaymentError('bad amount', 400) from e
```

**40. `sorted()` vs `list.sort()`: how does `key` work and is sorting stable?**

`list.sort()` sorts in place and returns `None`; `sorted()` accepts any iterable and returns a new list. Both use Timsort: O(n log n) and stable, so equal elements keep their relative order (which makes multi-pass sorting possible). The `key` function is called once per element.

```python
people = [('Ann', 30), ('Bob', 25), ('Cy', 30)]
print(sorted(people, key=lambda p: (-p[1], p[0])))
# [('Ann', 30), ('Cy', 30), ('Bob', 25)]
```

## Advanced Questions

**41. What is the GIL and what are its implications?**

The Global Interpreter Lock in CPython lets only one thread execute Python bytecode at a time, which protects reference counts and interpreter state. Threads therefore do not speed up CPU-bound pure-Python code, but they still help I/O-bound work because the GIL is released while blocking on I/O. Workarounds: `multiprocessing`, C extensions that release the GIL (NumPy), `asyncio`, or a free-threaded build (see Q72).

```python
from concurrent.futures import ProcessPoolExecutor

def cpu(n):
    return sum(i * i for i in range(n))

if __name__ == '__main__':
    with ProcessPoolExecutor() as ex:       # real parallelism across cores
        print(list(ex.map(cpu, [10**6] * 4)))
```

**42. Threading vs multiprocessing vs asyncio — when do you use which?**

- `threading`: I/O-bound work with simple shared memory; the GIL limits CPU-bound gains.
- `multiprocessing`: CPU-bound work; separate processes bypass the GIL but pay for pickling and IPC.
- `asyncio`: very high-concurrency I/O (thousands of sockets) on one thread with cooperative multitasking; requires async-aware libraries.

**43. How does `asyncio` work?**

A single-threaded event loop runs coroutines (`async def`). `await` hands control back to the loop while waiting on I/O. `asyncio.run()` starts the loop; `create_task`, `gather` and `TaskGroup` run coroutines concurrently. A blocking call freezes the whole loop, so push it to a thread with `asyncio.to_thread`.

```python
import asyncio

async def fetch(i):
    await asyncio.sleep(1)
    return i

async def main():
    print(await asyncio.gather(*(fetch(i) for i in range(5))))   # ~1s total

asyncio.run(main())
```

**44. How does memory management and garbage collection work in CPython?**

Objects live on a private heap. The primary mechanism is reference counting: memory is freed the moment the count reaches zero. A supplementary generational cycle collector (`gc` module) reclaims reference cycles that counting alone cannot free. The exact collection strategy has been tuned across versions, so treat it as an implementation detail.

```python
import sys, gc

a = []
print(sys.getrefcount(a))   # includes the temporary argument reference
a.append(a)                 # reference cycle
del a
print(gc.collect())         # the cycle collector reclaims it
```

**45. What is `__slots__`?**

`__slots__` declares a fixed set of attributes and removes the per-instance `__dict__`, which cuts memory (valuable with millions of objects) and speeds up attribute access slightly. Trade-offs: no arbitrary new attributes, and some inheritance and weakref caveats.

```python
class Point:
    __slots__ = ('x', 'y')
    def __init__(self, x, y):
        self.x, self.y = x, y

Point(1, 2).z = 3   # AttributeError
```

**46. What are metaclasses?**

A metaclass is the class of a class (the default is `type`). It controls class creation, which makes it useful for registries, validation and ORMs (Django models). In practice `__init_subclass__` or a class decorator is usually simpler.

```python
class Registry(type):
    classes = {}
    def __new__(mcs, name, bases, ns):
        cls = super().__new__(mcs, name, bases, ns)
        mcs.classes[name] = cls
        return cls

class Plugin(metaclass=Registry): pass
class Csv(Plugin): pass
print(list(Registry.classes))   # ['Plugin', 'Csv']
```

**47. What are descriptors?**

Descriptors are objects defined on a class that implement `__get__`, `__set__`, `__delete__` and optionally `__set_name__`. They power `property`, methods, `classmethod`, `staticmethod` and `__slots__`. A data descriptor (one with `__set__`) takes precedence over the instance `__dict__`.

```python
class Positive:
    def __set_name__(self, owner, name):
        self.name = '_' + name
    def __get__(self, obj, owner=None):
        return self if obj is None else getattr(obj, self.name)
    def __set__(self, obj, value):
        if value <= 0:
            raise ValueError('must be > 0')
        setattr(obj, self.name, value)

class Order:
    qty = Positive()
    def __init__(self, qty):
        self.qty = qty
```

**48. `__new__` vs `__init__`, and how do you implement a singleton?**

`__new__` creates the instance (it is effectively a static method that returns the object); `__init__` initializes it. Singleton options: a module-level instance (most Pythonic), overriding `__new__`, a decorator, or a metaclass. Note that with the `__new__` approach `__init__` still runs on every call.

```python
class Singleton:
    _inst = None
    def __new__(cls, *args, **kwargs):
        if cls._inst is None:
            cls._inst = super().__new__(cls)
        return cls._inst

assert Singleton() is Singleton()
```

**49. What is `weakref` and when would you use it?**

A weak reference does not increase the reference count, so the target can still be garbage-collected. It is used for caches, observer lists and avoiding leaks (`WeakValueDictionary`, `WeakSet`). Some built-ins (such as `list`, `dict`, `int`) cannot be weakly referenced directly.

```python
import weakref

class Node: pass
n = Node()
r = weakref.ref(n)
print(r() is n)   # True
del n
print(r())        # None (object collected)
```

**50. Explain `yield from`, `send()`, and async generators.**

`yield from` delegates to a sub-generator and forwards `send`/`throw` and the return value. `send(v)` resumes a generator and makes the paused `yield` evaluate to `v` (coroutine-style). Async generators (`async def` with `yield`) are consumed with `async for`.

```python
def averager():
    total = count = 0
    avg = None
    while True:
        x = yield avg
        total += x
        count += 1
        avg = total / count

g = averager(); next(g)
print(g.send(10), g.send(20))   # 10.0 15.0

async def ticker(n):
    for i in range(n):
        yield i
```

**51. What is `concurrent.futures`?**

A high-level API over thread and process pools. `submit()` returns a `Future`; `map()` returns results in order; `as_completed()` yields futures as they finish; `future.result()` re-raises any exception from the worker.

```python
from concurrent.futures import ThreadPoolExecutor, as_completed
import urllib.request

def size(url):
    with urllib.request.urlopen(url) as r:
        return len(r.read())

urls = ['https://example.com'] * 3
with ThreadPoolExecutor(max_workers=3) as ex:
    futures = [ex.submit(size, u) for u in urls]
    for f in as_completed(futures):
        print(f.result())
```

**52. What are race conditions and deadlocks? How do you synchronize threads?**

A race condition occurs when the outcome depends on thread interleaving (`counter += 1` is not atomic). Protect shared state with `Lock`/`RLock`, `Semaphore`, `Event`, `Condition`, or pass work through a thread-safe `queue.Queue`. Avoid deadlocks with consistent lock ordering, timeouts and `with` blocks.

```python
import threading

count, lock = 0, threading.Lock()

def work():
    global count
    for _ in range(100_000):
        with lock:
            count += 1

threads = [threading.Thread(target=work) for _ in range(4)]
for t in threads: t.start()
for t in threads: t.join()
print(count)   # 400000
```

**53. What are the walrus operator and structural pattern matching?**

`:=` (3.8) assigns a value inside an expression. `match/case` (3.10) performs structural pattern matching: it can destructure sequences, mappings and class instances and apply guards, so it is much more than a switch statement.

```python
if (n := len('hello')) > 3:
    print(n)

def handle(cmd):
    match cmd:
        case ['go', direction]:      return f'go {direction}'
        case {'type': 'quit'}:       return 'bye'
        case int(x) if x > 0:        return 'positive'
        case _:                      return 'unknown'
```

**54. What is the difference between `__getattr__` and `__getattribute__`?**

`__getattribute__` runs on every attribute access. `__getattr__` runs only when normal lookup fails, which makes it a fallback hook for proxies and lazy attributes. Inside `__getattribute__` use `object.__getattribute__` to avoid infinite recursion.

```python
class Config:
    def __init__(self):
        self._data = {'debug': True}
    def __getattr__(self, name):
        try:
            return self._data[name]
        except KeyError:
            raise AttributeError(name)

print(Config().debug)   # True
```

**55. What is `__call__`? How do callable objects and operator overloading help?**

Defining `__call__` makes instances callable like functions, which is handy for stateful callables and class-based decorators. Operator overloading (`__add__`, `__eq__`, `__lt__`, `__getitem__`, ...) lets objects work with built-in syntax; return `NotImplemented` for unsupported operand types.

```python
class Multiplier:
    def __init__(self, k):
        self.k = k
    def __call__(self, x):
        return x * self.k

double = Multiplier(2)
print(double(21), callable(double))   # 42 True
```

**56. How does `lru_cache` work and what are its caveats?**

It memoizes results keyed by the call arguments (which must be hashable) and evicts the least recently used entries once `maxsize` is reached; `functools.cache` is the unbounded variant. Caveats: on methods it keeps `self` alive (a memory leak risk), mutable arguments are not allowed, there is no TTL, and two threads may compute the same key concurrently.

```python
from functools import lru_cache

@lru_cache(maxsize=None)
def fib(n):
    return n if n < 2 else fib(n - 1) + fib(n - 2)

fib(80)
print(fib.cache_info())
```

**57. How do you profile and optimize Python code?**

Measure first. Use `timeit` for micro-benchmarks, `cProfile`/`pstats` for function-level profiles, `line_profiler` for hot lines, `py-spy` for sampling a running process, and `tracemalloc`/`memray` for memory. Then optimize: better algorithms and data structures, built-ins and comprehensions, `''.join` instead of repeated string concatenation, generators, caching, vectorization (NumPy), concurrency, or compiled extensions.

```python
import cProfile, pstats

cProfile.run('sum(i * i for i in range(10**6))', 'out.prof')
pstats.Stats('out.prof').sort_stats('cumtime').print_stats(5)
```

**58. `unittest` vs `pytest`?**

`unittest` is in the standard library and class-based (`TestCase`, `setUp`). `pytest` uses plain functions and bare `assert`, plus fixtures (dependency-injected setup), parametrization and a rich plugin ecosystem (coverage, xdist, asyncio); it can also run `unittest` tests.

```python
import pytest

@pytest.fixture
def numbers():
    return [1, 2, 3]

@pytest.mark.parametrize('a,b,expected', [(1, 2, 3), (0, 0, 0)])
def test_add(a, b, expected):
    assert a + b == expected

def test_raises():
    with pytest.raises(ZeroDivisionError):
        1 / 0
```

**59. How do you mock dependencies in Python tests?**

Use `unittest.mock` (`Mock`, `MagicMock`, `AsyncMock`, `patch`, `autospec`). Patch the name where it is looked up (the module that imports it), not where it is defined.

```python
from unittest.mock import patch
from app.service import run      # app/service.py does: from app.client import fetch

@patch('app.service.fetch')
def test_service(mock_fetch):
    mock_fetch.return_value = {'ok': True}
    assert run()['ok']
    mock_fetch.assert_called_once()
```

**60. How do you use the `logging` module properly?**

Create one logger per module with `logging.getLogger(__name__)`, configure handlers and formatters once at the application entry point (libraries should not), use levels appropriately, pass lazy arguments (`log.info('x=%s', x)`), call `log.exception` inside `except` blocks, and emit structured (JSON) logs in production. Do not use `print` for diagnostics.

```python
import logging

logging.basicConfig(level=logging.INFO,
                    format='%(asctime)s %(name)s %(levelname)s %(message)s')
log = logging.getLogger(__name__)

try:
    1 / 0
except ZeroDivisionError:
    log.exception('calculation failed')
```

**61. How do JSON and `pickle` differ? Are there security concerns?**

JSON is text, language-neutral and limited to basic types (use `default=` to extend it). `pickle` is Python-specific and can serialize almost any object, but unpickling untrusted data can execute arbitrary code, so never load pickles you do not trust. Alternatives: JSON, MessagePack, Protocol Buffers, or validated Pydantic models.

```python
import json, pickle

data = {'a': [1, 2], 'b': None}
assert json.loads(json.dumps(data)) == data
print(json.dumps({'when': object()}, default=str)[:20])
blob = pickle.dumps(data)      # only unpickle data you trust
```

**62. How do you manage virtual environments and dependencies?**

A virtual environment isolates a project's packages (`python -m venv .venv`). Common tooling: `pip` with pinned requirements (and hashes), `pip-tools`, Poetry, PDM, and `uv` (fast resolver with lockfiles). Modern projects declare metadata in `pyproject.toml` (PEP 621). Commit lockfiles, separate dev dependencies, and pin exact versions for applications while keeping ranges for libraries.

```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
pip freeze > requirements.lock.txt
```

**63. Why must `__hash__` and `__eq__` be consistent?**

If `a == b` then `hash(a) == hash(b)` must hold, because dicts and sets locate items by hash first and then confirm with equality. Defining `__eq__` without `__hash__` makes a class unhashable. An object's hash must not change while it sits in a set or dict, so base it on immutable fields.

```python
class Pt:
    def __init__(self, x, y):
        self.x, self.y = x, y
    def __eq__(self, other):
        return (self.x, self.y) == (other.x, other.y)
    def __hash__(self):
        return hash((self.x, self.y))

print(len({Pt(1, 2), Pt(1, 2)}))   # 1
```

**64. What is interning and the small-integer cache?**

CPython caches small integers (-5 to 256) and interns some strings (identifier-like literals), so `is` can return `True` by accident. This is an implementation detail: never use `is` to compare values.

```python
import sys

a, b = 256, 256
print(a is b)        # True (cached)
x = int('1000'); y = int('1000')
print(x is y)        # False - equal values, different objects
print(sys.intern('some long string') is sys.intern('some long string'))   # True
```

**65. How do you make a class behave like a container?**

Implement `__len__`, `__getitem__`, `__iter__`, `__contains__` (and `__setitem__`/`__delitem__` if mutable), or subclass `collections.abc.Sequence`/`Mapping`/`MutableMapping` to get the remaining methods as free mixins.

```python
from collections.abc import Sequence

class Deck(Sequence):
    def __init__(self):
        self._cards = list(range(52))
    def __len__(self):
        return len(self._cards)
    def __getitem__(self, i):
        return self._cards[i]

d = Deck()
print(len(d), d[0], 5 in d, list(reversed(d))[:2])   # 52 0 True [51, 50]
```

**66. What are `Protocol`, `TypedDict`, `Generic`, and `TypeVar`?**

`Protocol` is structural typing (statically checked duck typing). `TypedDict` describes a dict with fixed typed keys. `Generic`/`TypeVar` parameterize types over other types; Python 3.12 added a shorter syntax (PEP 695) such as `def first[T](...)`.

```python
from typing import Protocol, TypedDict

class Closeable(Protocol):
    def close(self) -> None: ...

class Movie(TypedDict):
    title: str
    year: int

def first[T](items: list[T]) -> T:    # 3.12+ syntax
    return items[0]
```

**67. When is the GIL released, and why can NumPy use several cores?**

The GIL is released during blocking I/O (files, sockets, `time.sleep`) and by C extensions that explicitly drop it around heavy computation (NumPy routines, `hashlib`, `zlib`). Threads can therefore overlap these operations even in a standard CPython build.

**68. What are class decorators, and in what order do stacked decorators apply?**

A class decorator receives a class and returns a class (register it, add methods, wrap attributes; `@dataclass` is one). Stacked decorators are applied bottom-up at definition time, so the one nearest the function wraps it first.

```python
REGISTRY = {}

def register(cls):
    REGISTRY[cls.__name__] = cls
    return cls

@register
class Handler: ...

# @a
# @b
# def f(): ...     is equivalent to   f = a(b(f))
```

**69. What are `async with`, `async for`, and `contextvars`?**

`async with` uses `__aenter__`/`__aexit__` (async DB sessions, locks) and `async for` uses `__aiter__`/`__anext__`. `contextvars.ContextVar` stores per-task or per-request state (request IDs, user context) where thread-locals would break under `asyncio`.

```python
import asyncio, contextvars

request_id = contextvars.ContextVar('request_id', default='-')

async def handler(rid):
    request_id.set(rid)
    await asyncio.sleep(0)
    print(request_id.get())      # each task sees its own value

async def main():
    await asyncio.gather(handler('a'), handler('b'))

asyncio.run(main())
```

**70. How does `Enum` work?**

`enum.Enum` defines named constants. Members are singletons compared by identity and are safer than magic strings. Variants: `IntEnum`, `Flag`, `StrEnum` (3.11+), and `auto()` for automatic values.

```python
from enum import Enum, auto

class Status(Enum):
    ACTIVE = auto()
    BLOCKED = auto()

print(Status.ACTIVE.name, Status['BLOCKED'].value)   # ACTIVE 2
```

## Expert Questions

**71. How does CPython execute code? What are bytecode, `dis`, and the eval loop?**

Source is tokenized, parsed into an AST, compiled into a code object (bytecode, cached as `.pyc`), and run by the eval loop of a stack-based virtual machine. `dis` shows the instructions. Since 3.11 the interpreter is adaptive: it specializes hot bytecode for speed, and 3.13+ ships an optional, experimental JIT.

```python
import dis

def add(a, b):
    return a + b

dis.dis(add)
print(add.__code__.co_varnames)   # ('a', 'b')
```

**72. What is free-threaded (no-GIL) Python?**

PEP 703 defines a CPython build without the GIL, enabling true multi-core threading. It was experimental in 3.13 and is officially supported, but still optional (a separate build, often named `python3.14t`), in 3.14; the default build keeps the GIL. Cautions: C extensions must be compatible, single-thread speed carries some overhead, and your own shared mutable state still needs locks.

```python
import sys

# False on a free-threaded build where the GIL is actually disabled
print(sys._is_gil_enabled())
```

**73. What are subinterpreters (PEP 734)?**

Subinterpreters are multiple isolated Python interpreters inside one process, each with its own module state (and, since 3.12, its own GIL per PEP 684). Python 3.14 exposes them in the standard library via `concurrent.interpreters` and `InterpreterPoolExecutor`. They are lighter than processes and safer than shared-memory threads; data moves between them by copying or through queues.

```python
from concurrent.futures import InterpreterPoolExecutor   # Python 3.14+

def work(n):
    return sum(range(n))

with InterpreterPoolExecutor() as ex:
    print(list(ex.map(work, [10**6] * 4)))
```

**74. What notable features arrived in Python 3.10 to 3.14?**

- 3.10: `match` statements, `X | Y` union types, better error messages, parenthesized context managers
- 3.11: large speedups, `ExceptionGroup` and `except*`, `tomllib`, `asyncio.TaskGroup`, `typing.Self`
- 3.12: PEP 695 generics syntax and the `type` statement, more flexible f-strings, per-interpreter GIL, `itertools.batched`
- 3.13: new interactive REPL, experimental free-threaded build, experimental JIT
- 3.14: template strings (t-strings, PEP 750), deferred evaluation of annotations, officially supported free-threaded build, subinterpreters in the stdlib, `compression.zstd`

```python
# Python 3.14: a t-string keeps interpolations structured instead of joining them
name = 'Ann'
tpl = t'Hello {name}'      # a string.templatelib.Template, not a str
print(type(tpl).__name__)  # Template
```

**75. What are `ExceptionGroup` and `except*`?**

Introduced in 3.11 to raise and handle several unrelated errors at once (for example from concurrent tasks). `except*` matches the sub-group of a given type and lets the remaining errors keep propagating. `asyncio.TaskGroup` raises an `ExceptionGroup` when several tasks fail.

```python
try:
    raise ExceptionGroup('batch', [ValueError('a'), TypeError('b')])
except* ValueError as eg:
    print('value errors:', eg.exceptions)
except* TypeError as eg:
    print('type errors:', eg.exceptions)
```

**76. How do you find and fix memory leaks in Python?**

Typical causes: unbounded global caches or lists, lingering references (closures, listeners, stored tracebacks), unclosed resources, and leaks in C extensions. Tools: `tracemalloc` snapshot diffs, `objgraph`, `memray`, and `gc.get_objects()`. Fixes: bounded or TTL caches, weak references, context managers, and dropping references promptly.

```python
import tracemalloc

tracemalloc.start()
snap1 = tracemalloc.take_snapshot()
# ... run the suspicious workload ...
snap2 = tracemalloc.take_snapshot()
for stat in snap2.compare_to(snap1, 'lineno')[:5]:
    print(stat)
```

**77. What do `__init_subclass__` and `__set_name__` do?**

`__init_subclass__` is called on the parent whenever a subclass is defined, which suits plugin registration and validation without a metaclass. `__set_name__` is called on descriptors when the owning class is created and receives the attribute name.

```python
class Plugin:
    registry = {}
    def __init_subclass__(cls, key=None, **kwargs):
        super().__init_subclass__(**kwargs)
        Plugin.registry[key or cls.__name__.lower()] = cls

class Csv(Plugin, key='csv'): pass
class Json(Plugin): pass

print(list(Plugin.registry))   # ['csv', 'json']
```

**78. How can you speed up Python beyond pure-Python tuning?**

Vectorize with NumPy, pandas or Polars; JIT numeric code with Numba; compile hot paths with Cython; call C through `ctypes`/`cffi` or write extensions; write Rust extensions with PyO3/maturin; try PyPy; or use multiprocessing/free-threading for parallelism. Always profile first to pick the right tool.

```python
from numba import njit      # pip install numba

@njit
def total(n):
    s = 0
    for i in range(n):
        s += i
    return s

print(total(10**7))
```

**79. Which design patterns are idiomatic in Python?**

First-class functions and modules simplify many classic patterns: Singleton becomes a module, Strategy becomes passing a function, Factory becomes a dict of callables or a classmethod, Observer becomes callbacks, Decorator becomes `@`, Iterator becomes a generator, and Resource management becomes a context manager.

```python
# Strategy via first-class functions
def pct(p):
    return lambda amount: amount * (1 - p)

DISCOUNTS = {'vip': pct(0.2), 'none': pct(0)}
print(DISCOUNTS['vip'](100))   # 80.0
```

**80. How do SOLID principles and dependency injection apply in Python?**

Single responsibility: small focused modules and functions. Open/closed: extend through protocols and plugins. Liskov: subclasses honor the base contract. Interface segregation: small `Protocol`s. Dependency inversion: depend on abstractions that are passed in. Constructor injection is usually enough (no container needed) and makes mocking trivial.

```python
from typing import Protocol

class Notifier(Protocol):
    def send(self, to: str, msg: str) -> None: ...

class SignupService:
    def __init__(self, notifier: Notifier):
        self.notifier = notifier
    def register(self, email: str):
        self.notifier.send(email, 'Welcome!')
```

**81. WSGI vs ASGI, and Django vs Flask vs FastAPI?**

WSGI is the synchronous server interface (Gunicorn with Flask or Django). ASGI supports async, WebSockets and long-lived connections (Uvicorn, Hypercorn, Daphne). Django is batteries-included (ORM, admin, auth, migrations). Flask is a flexible micro-framework, sync-first. FastAPI is ASGI-based, driven by type hints and Pydantic, and generates OpenAPI docs automatically.

```python
from fastapi import FastAPI

app = FastAPI()

@app.get('/ping')
async def ping():
    return {'pong': True}

# run with: uvicorn main:app --workers 4
```

**82. What is the N+1 query problem in the Django ORM and how do you fix it?**

Looping over objects and touching a related object issues one extra query per row. Fix it with `select_related` (SQL JOIN for foreign keys and one-to-one), `prefetch_related` (a second query joined in Python, for many-to-many and reverse relations), `only()`/`defer()`, database-side `annotate`/`aggregate`, and `bulk_create`/`bulk_update`.

```python
# N+1: one query for books, plus one per book for its author
for book in Book.objects.all():
    print(book.author.name)

# Fixed: a single JOINed query
for book in Book.objects.select_related('author'):
    print(book.author.name)
```

**83. How do Pydantic and dependency injection work in FastAPI?**

Request bodies and parameters are declared as type-annotated Pydantic models, which parse, coerce and validate input and return structured 422 errors on failure. They also serialize responses and feed the generated OpenAPI schema. `Depends()` provides dependency injection for things like auth and database sessions.

```python
from fastapi import FastAPI, Depends
from pydantic import BaseModel, Field

class UserIn(BaseModel):
    email: str
    age: int = Field(ge=18)

app = FastAPI()

def get_db():
    ...   # yield a session here

@app.post('/users')
async def create(user: UserIn, db=Depends(get_db)):
    return {'email': user.email}
```

**84. How do you secure a Python application?**

- Validate input and use parameterized SQL or an ORM; never build queries with string formatting.
- Never run `eval`/`exec`, `pickle.loads` or `yaml.load` (use `safe_load`) on untrusted data.
- Keep secrets in environment variables or a secrets manager; use `secrets` for tokens and argon2/bcrypt for password hashing.
- Call `subprocess` with a list of arguments and avoid `shell=True` with user input.
- Scan dependencies (`pip-audit`), pin versions, and run `bandit`.
- Use HTTPS, CSRF/XSS/CORS protections, rate limiting, and least privilege.

```python
cur.execute('SELECT * FROM users WHERE email = %s', (email,))   # safe: parameterized
# cur.execute(f"SELECT * FROM users WHERE email = '{email}'")   # injectable - never do this
```

**85. How do SQLAlchemy sessions, transactions, and connection pooling work?**

The `Engine` owns a connection pool (`pool_size`, `max_overflow`, `pool_pre_ping`). A `Session` is a unit of work with an identity map: it tracks changes and flushes them on commit. Use one session per request or task, wrap it in a context manager, and roll back on errors. SQLAlchemy 2.0 uses `select()` statements and supports async engines.

```python
from sqlalchemy import create_engine, select
from sqlalchemy.orm import Session

engine = create_engine('postgresql+psycopg://user:pw@host/db',
                       pool_size=10, pool_pre_ping=True)

with Session(engine) as session, session.begin():   # auto commit / rollback
    session.add(User(name='Ann'))
    users = session.scalars(select(User).where(User.name == 'Ann')).all()
```

**86. What caching strategies do you use in Python services?**

Layers: in-process (`lru_cache`, `cachetools` with TTL), distributed (Redis, Memcached), HTTP caching (ETag, Cache-Control) and CDNs. Patterns: cache-aside, write-through, TTL with jitter, stampede protection (locks), and explicit invalidation. Always weigh staleness against speed and memory.

```python
import json, redis
r = redis.Redis()

def get_user(uid):
    key = f'user:{uid}'
    if (cached := r.get(key)):          # cache-aside
        return json.loads(cached)
    user = db.fetch_user(uid)           # db layer defined elsewhere
    r.setex(key, 300, json.dumps(user))
    return user
```

**87. How do task queues like Celery work, and why does idempotency matter?**

Producers push tasks to a broker (Redis or RabbitMQ), workers execute them, and an optional result backend stores outcomes. Celery offers retries with backoff, scheduling (beat), routing and rate limits. Delivery is typically at-least-once, so tasks may run twice; make them idempotent with dedupe keys or upserts. Alternatives: RQ, Dramatiq, arq.

```python
from celery import Celery

app = Celery('proj', broker='redis://localhost:6379/0')

@app.task(autoretry_for=(ConnectionError,), retry_backoff=True, max_retries=5)
def send_invoice(order_id):
    ...

send_invoice.delay(42)
```

**88. How do you process data that is larger than memory?**

Stream with generators and iterators, read in chunks (`pandas.read_csv(chunksize=...)`), process files line by line, use memory-mapped files (`mmap`, `np.memmap`), columnar formats (Parquet/Arrow), lazy engines (Polars, Dask), and push aggregation into the database. Avoid building intermediate lists.

```python
import pandas as pd

total = 0
for chunk in pd.read_csv('big.csv', chunksize=100_000):
    total += chunk['amount'].sum()
print(total)
```

**89. Why is NumPy faster than Python lists, and what are common pandas pitfalls?**

NumPy stores typed values in contiguous memory and runs vectorized loops in C (often SIMD) with broadcasting, avoiding per-element object overhead. Common pandas pitfalls: row-wise `apply`/`iterrows` (slow), chained assignment (`SettingWithCopy`), accidental `object` dtypes, and needless copies. Prefer vectorized operations, `.loc`, and categorical dtypes.

```python
import numpy as np

a = np.arange(1_000_000)
print((a * 2 + 1).sum())       # vectorized, no Python loop
# df['total'] = df['qty'] * df['price']    # instead of df.apply(..., axis=1)
```

**90. How do you handle timeouts, cancellation, and concurrency limits in `asyncio`?**

Use `asyncio.timeout()` (3.11+) or `wait_for`, `TaskGroup` for structured concurrency (a failure cancels sibling tasks), `Semaphore` to bound concurrency, and re-raise `CancelledError` after cleanup. Never block the event loop with synchronous work.

```python
import asyncio

sem = asyncio.Semaphore(10)

async def call(i):
    async with sem:
        await asyncio.sleep(0.1)
        return i

async def main():
    try:
        async with asyncio.timeout(2), asyncio.TaskGroup() as tg:
            tasks = [tg.create_task(call(i)) for i in range(50)]
    except TimeoutError:
        print('took too long')
    else:
        print(sum(t.result() for t in tasks))   # 1225

asyncio.run(main())
```

**91. How does `multiprocessing` work and what are its pitfalls?**

It starts worker processes using a start method (`spawn`, `fork` or `forkserver`; the default on Linux moved away from `fork` in 3.14). Arguments and results are pickled, so lambdas and local functions cannot be sent. Under `spawn` you need the `if __name__ == '__main__'` guard. Share state with `Queue`, `Pipe`, `shared_memory` or `Manager`. Per-task overhead makes it unsuitable for tiny tasks, and forking a process that already runs threads is unsafe.

```python
from multiprocessing import Pool

def square(x):
    return x * x

if __name__ == '__main__':
    with Pool(4) as pool:
        print(pool.map(square, range(8)))
```

**92. What is monkey patching and when is it risky?**

Monkey patching modifies classes or modules at runtime. It is useful in tests (`monkeypatch`, `mock.patch`) and for hotfixes, but it creates hidden global side effects, breaks across library versions and is hard to debug, so scope it tightly and restore the original.

```python
import time

def test_slow(monkeypatch):
    monkeypatch.setattr(time, 'sleep', lambda s: None)
    time.sleep(10)     # returns instantly inside this test
```

**93. How does the import system work, and how do you resolve circular imports?**

Import looks in `sys.modules`, then asks finders on `sys.meta_path` (path-based lookup over `sys.path`), and loaders execute the module once and cache it. A circular import exposes a partially initialized module and raises `ImportError` or `AttributeError`. Fixes: restructure shared code into a third module, import inside the function, use `import module` instead of `from module import name`, or guard type-only imports with `TYPE_CHECKING`.

```python
from __future__ import annotations
from typing import TYPE_CHECKING

if TYPE_CHECKING:
    from .orders import Order      # only imported for type checkers

class Customer:
    def add(self, order: Order) -> None: ...
```

**94. How do you package and ship a Python application?**

Declare metadata in `pyproject.toml` with a build backend (setuptools, hatchling, poetry-core), build a wheel and sdist (`python -m build`), and publish to PyPI or a private index (preferably with trusted publishing). Expose CLIs through entry points. For services, use multi-stage Docker builds, a slim base image, a non-root user and pinned dependencies.

```toml
[project]
name = 'mytool'
version = '1.0.0'
requires-python = '>=3.10'
dependencies = ['requests>=2.31']

[project.scripts]
mytool = 'mytool.cli:main'

[build-system]
requires = ['hatchling']
build-backend = 'hatchling.build'
```

**95. Which tools enforce Python code quality?**

Ruff (linter and formatter that replaces flake8, isort and often Black), Black, mypy or pyright for type checking, pytest with coverage for tests, bandit and pip-audit for security, and pre-commit plus CI to run everything automatically.

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: vX.Y.Z        # pin to a current release tag
    hooks:
      - id: ruff
      - id: ruff-format
```

**96. Coding: implement an LRU cache.**

An `OrderedDict` gives O(1) get and put by moving touched keys to the end and evicting from the front.

```python
from collections import OrderedDict

class LRUCache:
    def __init__(self, capacity: int):
        self.cap = capacity
        self.data = OrderedDict()

    def get(self, key, default=-1):
        if key not in self.data:
            return default
        self.data.move_to_end(key)
        return self.data[key]

    def put(self, key, value):
        self.data[key] = value
        self.data.move_to_end(key)
        if len(self.data) > self.cap:
            self.data.popitem(last=False)
```

**97. Coding: write a retry decorator with exponential backoff.**

Retry only idempotent operations, re-raise after the last attempt, and add jitter to avoid thundering herds.

```python
import time, random
from functools import wraps

def retry(times=3, base=0.5, exceptions=(Exception,)):
    def decorator(fn):
        @wraps(fn)
        def wrapper(*args, **kwargs):
            for attempt in range(1, times + 1):
                try:
                    return fn(*args, **kwargs)
                except exceptions:
                    if attempt == times:
                        raise
                    time.sleep(base * 2 ** (attempt - 1) + random.uniform(0, 0.1))
        return wrapper
    return decorator
```

**98. Coding: build an asyncio producer-consumer pipeline.**

A bounded `asyncio.Queue` provides backpressure: the producer waits when the consumer falls behind.

```python
import asyncio

async def producer(q):
    for i in range(5):
        await q.put(i)
    await q.put(None)              # sentinel: no more work

async def consumer(q):
    while (item := await q.get()) is not None:
        print('processed', item)

async def main():
    q = asyncio.Queue(maxsize=2)   # bounded queue = backpressure
    await asyncio.gather(producer(q), consumer(q))

asyncio.run(main())
```

**99. Coding: solve two-sum and group anagrams in a Pythonic way.**

Both run in linear passes using hash maps (group anagrams is O(n * k log k) for words of length k).

```python
from collections import defaultdict

def two_sum(nums, target):
    seen = {}
    for i, n in enumerate(nums):
        if target - n in seen:
            return seen[target - n], i
        seen[n] = i

def group_anagrams(words):
    groups = defaultdict(list)
    for w in words:
        groups[''.join(sorted(w))].append(w)
    return list(groups.values())

print(two_sum([2, 7, 11, 15], 9))                        # (0, 1)
print(group_anagrams(['eat', 'tea', 'tan', 'nat', 'bat']))
# [['eat', 'tea'], ['tan', 'nat'], ['bat']]
```

**100. System design: how would you build a scalable Python service handling about 10k requests per second?**

- Keep the service stateless and run it as an ASGI app (FastAPI with Uvicorn), several worker processes per host, horizontally scaled behind a load balancer with autoscaling.
- Use async I/O for database and HTTP calls, connection pooling (for example PgBouncer), timeouts, retries and circuit breakers.
- Cache hot reads (Redis, CDN), paginate, add the right indexes, and use read replicas.
- Offload slow or bursty work to queues (Celery, arq) with idempotent consumers.
- Move CPU-heavy work to separate worker processes or compiled libraries.
- Add observability: structured logs, metrics (Prometheus), tracing (OpenTelemetry), and profiling in production with `py-spy`.
- Protect the system with rate limiting, authentication, health checks, graceful shutdown, and load tests (Locust, k6).

```bash
uvicorn app.main:app --workers 4 --host 0.0.0.0 --port 8000
```

---


