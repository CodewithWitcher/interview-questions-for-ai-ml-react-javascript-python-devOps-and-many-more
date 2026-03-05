# Core Python Fundamentals — Interview Questions & Answers

## 1. What are Python's key features that make it suitable for AI/ML?

**Answer:** Python offers:
- **Simple syntax** — reduces development time and improves readability
- **Rich ecosystem** — NumPy, Pandas, Scikit-learn, TensorFlow, PyTorch
- **Dynamic typing** — rapid prototyping
- **Interpreted language** — easy debugging and testing
- **Cross-platform** — runs on all major operating systems
- **Large community** — extensive documentation and support
- **Integration** — easily integrates with C/C++ for performance-critical code

---

## 2. What is the difference between a list and a tuple?

**Answer:**

| Feature | List | Tuple |
|---------|------|-------|
| Mutability | Mutable | Immutable |
| Syntax | `[1, 2, 3]` | `(1, 2, 3)` |
| Performance | Slower | Faster |
| Memory | More | Less |
| Use case | Dynamic collections | Fixed collections, dict keys |
| Methods | Many (append, extend, etc.) | Few (count, index) |

```python
my_list = [1, 2, 3]
my_list[0] = 10  # Works

my_tuple = (1, 2, 3)
my_tuple[0] = 10  # TypeError
```

---

## 3. Explain Python's memory management.

**Answer:** Python uses a private heap space managed internally:

- **Reference counting** — each object tracks how many references point to it
- **Garbage collector** — handles cyclic references using generational GC
- **Memory allocator (pymalloc)** — optimizes allocation for small objects (< 512 bytes)
- **Memory pools** — blocks of memory organized in pools for objects of the same size

```python
import sys
a = [1, 2, 3]
print(sys.getrefcount(a))  # Shows reference count (includes function arg)
```

---

## 4. What is the difference between `is` and `==`?

**Answer:**
- `==` checks **value equality** (calls `__eq__`)
- `is` checks **identity** (same object in memory)

```python
a = [1, 2, 3]
b = [1, 2, 3]
print(a == b)   # True (same values)
print(a is b)   # False (different objects)

x = 256
y = 256
print(x is y)   # True (Python caches integers -5 to 256)

x = 257
y = 257
print(x is y)   # False (outside cache range)
```

---

## 5. What are `*args` and `**kwargs`?

**Answer:**
- `*args` collects positional arguments into a **tuple**
- `**kwargs` collects keyword arguments into a **dictionary**

```python
def example(*args, **kwargs):
    print(f"args: {args}")      # (1, 2, 3)
    print(f"kwargs: {kwargs}")  # {'name': 'Alice', 'age': 30}

example(1, 2, 3, name="Alice", age=30)
```

**Order of parameters:** `def func(pos, /, pos_or_kw, *, kw_only, **kwargs)`

---

## 6. Explain Python's GIL (Global Interpreter Lock).

**Answer:** The GIL is a mutex in CPython that allows only one thread to execute Python bytecode at a time:

- **Why it exists** — simplifies memory management (reference counting is not thread-safe)
- **Impact** — CPU-bound multithreaded programs don't run truly in parallel
- **Workarounds:**
  - Use `multiprocessing` for CPU-bound tasks
  - Use `threading` for I/O-bound tasks (GIL is released during I/O)
  - Use `asyncio` for concurrent I/O
  - Use C extensions or Cython

```python
import multiprocessing
# Each process has its own GIL
pool = multiprocessing.Pool(4)
results = pool.map(cpu_intensive_func, data)
```

---

## 7. What are Python namespaces and scope (LEGB rule)?

**Answer:** Python resolves names using the **LEGB** rule:

1. **L**ocal — inside the current function
2. **E**nclosing — in enclosing function (closures)
3. **G**lobal — module-level
4. **B**uilt-in — Python's built-in names

```python
x = "global"

def outer():
    x = "enclosing"
    
    def inner():
        x = "local"
        print(x)  # "local"
    
    inner()

outer()
print(x)  # "global"
```

Use `global` and `nonlocal` keywords to modify outer scopes.

---

## 8. What is the difference between shallow copy and deep copy?

**Answer:**

```python
import copy

original = [[1, 2], [3, 4]]

# Shallow copy — copies outer object, shares inner references
shallow = copy.copy(original)
shallow[0][0] = 99
print(original[0][0])  # 99 (changed!)

# Deep copy — recursively copies all nested objects
original = [[1, 2], [3, 4]]
deep = copy.deepcopy(original)
deep[0][0] = 99
print(original[0][0])  # 1 (unchanged)
```

| | Shallow Copy | Deep Copy |
|---|---|---|
| Copies nested objects? | No (shares references) | Yes (full clone) |
| Performance | Faster | Slower |
| Memory | Less | More |

---

## 9. What are Python decorators?

**Answer:** Decorators are functions that modify the behavior of other functions/classes:

```python
import functools
import time

def timer(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        elapsed = time.time() - start
        print(f"{func.__name__} took {elapsed:.4f}s")
        return result
    return wrapper

@timer
def slow_function():
    time.sleep(1)

slow_function()  # "slow_function took 1.00xxs"
```

**Common built-in decorators:** `@property`, `@staticmethod`, `@classmethod`, `@functools.lru_cache`

---

## 10. Explain list comprehension vs generator expression.

**Answer:**

```python
# List comprehension — creates full list in memory
squares_list = [x**2 for x in range(1000000)]  # ~8MB in memory

# Generator expression — yields items lazily
squares_gen = (x**2 for x in range(1000000))   # ~120 bytes
```

| Feature | List Comprehension | Generator Expression |
|---------|-------------------|---------------------|
| Syntax | `[expr for x in iter]` | `(expr for x in iter)` |
| Memory | Entire list in memory | One item at a time |
| Reusable | Yes | No (exhausted after one pass) |
| Speed | Faster for small data | Better for large datasets |
| Returns | `list` | `generator` object |

---

## 11. What are Python's mutable and immutable types?

**Answer:**

| Mutable | Immutable |
|---------|-----------|
| `list` | `int` |
| `dict` | `float` |
| `set` | `str` |
| `bytearray` | `tuple` |
| Custom objects (by default) | `frozenset` |
| | `bytes` |

**Key implication:** Immutable objects are hashable (can be dict keys/set members). Mutable objects cannot be used as dictionary keys.

---

## 12. How does Python handle exceptions? Explain try/except/else/finally.

**Answer:**

```python
try:
    result = 10 / 0
except ZeroDivisionError as e:
    print(f"Error: {e}")        # Handles specific exception
except Exception as e:
    print(f"General error: {e}")  # Catches all other exceptions
else:
    print("No exception!")       # Runs only if no exception
finally:
    print("Always runs")         # Cleanup, always executes
```

**Custom exceptions:**
```python
class ModelNotTrainedError(Exception):
    """Raised when prediction is called before training."""
    pass
```

---

## 13. What is the difference between `append()`, `extend()`, and `insert()`?

**Answer:**

```python
lst = [1, 2, 3]

lst.append([4, 5])   # [1, 2, 3, [4, 5]] — adds single element
lst = [1, 2, 3]

lst.extend([4, 5])   # [1, 2, 3, 4, 5] — unpacks iterable
lst = [1, 2, 3]

lst.insert(1, 99)    # [1, 99, 2, 3] — inserts at index
```

---

## 14. Explain Python's string formatting methods.

**Answer:**

```python
name, age = "Alice", 30

# 1. f-strings (Python 3.6+) — preferred
print(f"Name: {name}, Age: {age}")

# 2. .format()
print("Name: {}, Age: {}".format(name, age))

# 3. % formatting (legacy)
print("Name: %s, Age: %d" % (name, age))

# 4. Template strings
from string import Template
t = Template("Name: $name, Age: $age")
print(t.substitute(name=name, age=age))
```

---

## 15. What are `lambda` functions?

**Answer:** Anonymous, single-expression functions:

```python
# Regular function
def square(x):
    return x ** 2

# Lambda equivalent
square = lambda x: x ** 2

# Common use with higher-order functions
data = [("Alice", 90), ("Bob", 75), ("Charlie", 95)]
sorted_data = sorted(data, key=lambda x: x[1], reverse=True)
# [('Charlie', 95), ('Alice', 90), ('Bob', 75)]

# With map/filter
numbers = [1, 2, 3, 4, 5]
evens = list(filter(lambda x: x % 2 == 0, numbers))  # [2, 4]
doubled = list(map(lambda x: x * 2, numbers))         # [2, 4, 6, 8, 10]
```

---

## 16. What is the difference between `@staticmethod` and `@classmethod`?

**Answer:**

```python
class MyClass:
    class_var = "Hello"
    
    @staticmethod
    def static_method():
        # No access to class or instance
        return "I'm a utility function"
    
    @classmethod
    def class_method(cls):
        # Has access to class (not instance)
        return f"Class var: {cls.class_var}"
    
    def instance_method(self):
        # Has access to instance (and class via self.__class__)
        return f"Instance of {self.__class__.__name__}"
```

| Feature | `@staticmethod` | `@classmethod` | Instance method |
|---------|----------------|----------------|-----------------|
| First arg | None | `cls` (class) | `self` (instance) |
| Access class? | No | Yes | Yes (via `self`) |
| Access instance? | No | No | Yes |
| Can modify state? | No | Class-level only | Instance + class |

---

## 17. What are `__init__` and `__new__` in Python?

**Answer:**
- `__new__` — creates the instance (rarely overridden)
- `__init__` — initializes the instance

```python
class Singleton:
    _instance = None
    
    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance
    
    def __init__(self):
        self.value = 42

a = Singleton()
b = Singleton()
print(a is b)  # True — same object
```

---

## 18. Explain the `with` statement and context managers.

**Answer:** The `with` statement ensures proper resource management:

```python
# Using built-in context manager
with open("file.txt", "r") as f:
    data = f.read()
# File automatically closed, even if exception occurs

# Custom context manager using class
class Timer:
    def __enter__(self):
        self.start = time.time()
        return self
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        self.elapsed = time.time() - self.start
        print(f"Elapsed: {self.elapsed:.4f}s")
        return False  # Don't suppress exceptions

# Using contextlib
from contextlib import contextmanager

@contextmanager
def timer():
    start = time.time()
    yield
    print(f"Elapsed: {time.time() - start:.4f}s")
```

---

## 19. What is the difference between `range()` and `xrange()`?

**Answer:**
- In **Python 2**: `range()` returns a list, `xrange()` returns a lazy iterator
- In **Python 3**: `range()` returns a lazy range object (similar to Python 2's `xrange`). `xrange()` doesn't exist.

```python
# Python 3
r = range(1000000)
print(type(r))        # <class 'range'>
print(r[500000])      # 500000 — supports indexing
print(500 in r)       # True — supports O(1) membership testing
print(sys.getsizeof(r))  # 48 bytes (regardless of range size)
```

---

## 20. How do you handle file I/O in Python?

**Answer:**

```python
# Reading
with open("data.txt", "r") as f:
    content = f.read()           # Read entire file
    lines = f.readlines()        # Read all lines as list
    for line in f:               # Iterate line by line (memory efficient)
        process(line)

# Writing
with open("output.txt", "w") as f:
    f.write("Hello\n")
    f.writelines(["line1\n", "line2\n"])

# Append
with open("log.txt", "a") as f:
    f.write("New entry\n")

# Binary
with open("model.pkl", "rb") as f:
    data = f.read()

# CSV
import csv
with open("data.csv") as f:
    reader = csv.DictReader(f)
    for row in reader:
        print(row)

# JSON
import json
with open("config.json") as f:
    config = json.load(f)
```

---

## 21. What is `None` in Python?

**Answer:** `None` is Python's null value — a singleton object of type `NoneType`:

```python
x = None
print(type(x))      # <class 'NoneType'>
print(x is None)    # True (always use 'is' to check for None)
print(x == None)    # True (but 'is' is preferred)

# Common uses
def find_item(lst, target):
    for item in lst:
        if item == target:
            return item
    return None  # Explicit return of None

# Default function arguments
def greet(name=None):
    if name is None:
        name = "World"
    print(f"Hello, {name}!")
```

---

## 22. What are Python's built-in data types?

**Answer:**

| Category | Types |
|----------|-------|
| **Numeric** | `int`, `float`, `complex`, `bool` |
| **Sequence** | `str`, `list`, `tuple`, `range`, `bytes`, `bytearray` |
| **Mapping** | `dict` |
| **Set** | `set`, `frozenset` |
| **None** | `NoneType` |

---

## 23. How does Python's dictionary work internally?

**Answer:** Python dicts use a **hash table**:

1. Key is hashed using `hash()` function
2. Hash determines the bucket/slot
3. Collisions handled via **open addressing** (probing)
4. As of Python 3.7+, dicts maintain **insertion order**

```python
# Hash function
print(hash("hello"))      # Consistent integer
print(hash(42))           # 42 (integers hash to themselves)
print(hash((1, 2)))       # Tuples are hashable
# hash([1, 2])            # TypeError — lists are NOT hashable

# Time complexity
d = {"key": "value"}
d["key"]          # O(1) average lookup
d["new"] = "val"  # O(1) average insertion
"key" in d        # O(1) average membership test
```

---

## 24. What is the walrus operator (`:=`)?

**Answer:** Introduced in Python 3.8, it assigns values as part of an expression:

```python
# Without walrus
data = input("Enter: ")
if len(data) > 10:
    print(f"Too long: {len(data)} chars")

# With walrus
if (n := len(input("Enter: "))) > 10:
    print(f"Too long: {n} chars")

# In while loops
while (line := input(">>> ")) != "quit":
    print(f"You said: {line}")

# In list comprehensions
results = [y for x in data if (y := expensive_func(x)) is not None]
```

---

## 25. Explain `enumerate()`, `zip()`, and `map()`.

**Answer:**

```python
# enumerate — adds index to iterable
fruits = ["apple", "banana", "cherry"]
for i, fruit in enumerate(fruits, start=1):
    print(f"{i}. {fruit}")

# zip — combines multiple iterables
names = ["Alice", "Bob"]
scores = [90, 85]
for name, score in zip(names, scores):
    print(f"{name}: {score}")

# zip_longest — handles unequal lengths
from itertools import zip_longest
list(zip_longest([1, 2, 3], [4, 5], fillvalue=0))
# [(1,4), (2,5), (3,0)]

# map — applies function to all items
numbers = [1, 2, 3, 4]
squared = list(map(lambda x: x**2, numbers))  # [1, 4, 9, 16]
```

---

## 26. What are `set` operations in Python?

**Answer:**

```python
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}

a | b     # Union:        {1, 2, 3, 4, 5, 6}
a & b     # Intersection:  {3, 4}
a - b     # Difference:    {1, 2}
a ^ b     # Symmetric diff:{1, 2, 5, 6}
a <= b    # Subset:         False
a >= b    # Superset:       False

# frozenset — immutable set (can be used as dict key)
fs = frozenset([1, 2, 3])
d = {fs: "value"}  # Valid
```

---

## 27. How do you sort in Python?

**Answer:**

```python
# sorted() — returns new sorted list
sorted([3, 1, 4, 1, 5])                    # [1, 1, 3, 4, 5]
sorted([3, 1, 4], reverse=True)             # [4, 3, 1]
sorted(["banana", "apple"], key=len)        # ['apple', 'banana']

# list.sort() — sorts in-place, returns None
lst = [3, 1, 4]
lst.sort()

# Custom sorting
students = [("Alice", 90), ("Bob", 75), ("Charlie", 95)]
sorted(students, key=lambda s: s[1])        # Sort by score

# Multiple criteria
from operator import itemgetter
sorted(students, key=itemgetter(1, 0))      # By score, then name
```

---

## 28. What are `__str__` and `__repr__`?

**Answer:**
- `__str__` — human-readable representation (used by `print()`)
- `__repr__` — unambiguous, developer-facing representation (used in REPL)

```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y
    
    def __repr__(self):
        return f"Vector({self.x}, {self.y})"
    
    def __str__(self):
        return f"({self.x}, {self.y})"

v = Vector(3, 4)
print(v)        # (3, 4) — calls __str__
print(repr(v))  # Vector(3, 4) — calls __repr__
print([v])      # [Vector(3, 4)] — containers use __repr__
```

**Rule:** Always implement `__repr__`. Implement `__str__` only if you want a different user-facing format.

---

## 29. What is `pass`, `continue`, and `break`?

**Answer:**

```python
# pass — does nothing (placeholder)
class EmptyClass:
    pass

# continue — skip to next iteration
for i in range(5):
    if i == 2:
        continue
    print(i)  # 0, 1, 3, 4

# break — exit the loop
for i in range(5):
    if i == 3:
        break
    print(i)  # 0, 1, 2
```

---

## 30. What is `type()` vs `isinstance()`?

**Answer:**

```python
class Animal: pass
class Dog(Animal): pass

d = Dog()

# type() — checks exact type
print(type(d) == Dog)     # True
print(type(d) == Animal)  # False (doesn't check inheritance)

# isinstance() — checks type AND inheritance
print(isinstance(d, Dog))    # True
print(isinstance(d, Animal)) # True (checks parent classes)

# Best practice: use isinstance()
if isinstance(value, (int, float)):  # Check multiple types
    print("Numeric")
```

---

## 31. How do you handle command-line arguments in Python?

**Answer:**

```python
# Method 1: sys.argv
import sys
print(sys.argv)  # ['script.py', 'arg1', 'arg2']

# Method 2: argparse (recommended)
import argparse
parser = argparse.ArgumentParser(description="Train a model")
parser.add_argument("--epochs", type=int, default=10)
parser.add_argument("--lr", type=float, default=0.001)
parser.add_argument("--model", choices=["cnn", "rnn", "transformer"])
args = parser.parse_args()
print(args.epochs, args.lr, args.model)
```

---

## 32. What are Python dictionaries' `get()`, `setdefault()`, and `defaultdict`?

**Answer:**

```python
d = {"a": 1, "b": 2}

# get() — safe access with default
d.get("c", 0)        # 0 (no KeyError)
d["c"]                # KeyError!

# setdefault() — get or set if missing
d.setdefault("c", 3)  # Sets d["c"] = 3 and returns 3

# defaultdict — automatic defaults
from collections import defaultdict
word_count = defaultdict(int)
for word in ["hello", "world", "hello"]:
    word_count[word] += 1
# {"hello": 2, "world": 1}

# Counter — even simpler for counting
from collections import Counter
Counter(["hello", "world", "hello"])
# Counter({'hello': 2, 'world': 1})
```

---

## 33. What is `__slots__`?

**Answer:** Restricts instance attributes and saves memory by not using `__dict__`:

```python
class WithDict:
    def __init__(self, x, y):
        self.x = x
        self.y = y

class WithSlots:
    __slots__ = ['x', 'y']
    def __init__(self, x, y):
        self.x = x
        self.y = y

import sys
a = WithDict(1, 2)
b = WithSlots(1, 2)
print(sys.getsizeof(a.__dict__))  # ~104 bytes
# b.__dict__  # AttributeError — no __dict__

b.z = 3  # AttributeError — can't add new attributes
```

**Use case:** Memory optimization when creating millions of instances (e.g., data points in ML).

---

## 34. What is monkey patching?

**Answer:** Dynamically modifying a class or module at runtime:

```python
class Calculator:
    def add(self, a, b):
        return a + b

# Monkey patch — add new method
Calculator.multiply = lambda self, a, b: a * b

calc = Calculator()
print(calc.multiply(3, 4))  # 12

# Common use: mocking in tests
import unittest.mock as mock

with mock.patch('module.function') as mocked:
    mocked.return_value = 42
    result = module.function()  # Returns 42
```

**Warning:** Monkey patching can make code harder to debug and maintain. Use primarily in testing.

---

## 35. What is the difference between `__getattr__` and `__getattribute__`?

**Answer:**

```python
class MyClass:
    def __init__(self):
        self.x = 10
    
    def __getattr__(self, name):
        # Called ONLY when normal lookup fails
        return f"'{name}' not found"
    
    def __getattribute__(self, name):
        # Called for EVERY attribute access
        print(f"Accessing: {name}")
        return super().__getattribute__(name)

obj = MyClass()
print(obj.x)       # Accessing: x → 10
print(obj.missing)  # Accessing: missing → "'missing' not found"
```

---

## 36. How do you work with dates and times in Python?

**Answer:**

```python
from datetime import datetime, timedelta, date

# Current date/time
now = datetime.now()
utc_now = datetime.utcnow()

# Formatting
now.strftime("%Y-%m-%d %H:%M:%S")  # "2024-01-15 14:30:00"

# Parsing
dt = datetime.strptime("2024-01-15", "%Y-%m-%d")

# Arithmetic
tomorrow = now + timedelta(days=1)
diff = datetime(2024, 12, 31) - now
print(diff.days)  # Days remaining

# Timestamps (common in ML for time-series)
timestamp = now.timestamp()  # Unix timestamp (float)
datetime.fromtimestamp(timestamp)  # Back to datetime
```

---

## 37. What is `collections.namedtuple`?

**Answer:** Immutable, lightweight class alternative:

```python
from collections import namedtuple

Point = namedtuple("Point", ["x", "y"])
p = Point(3, 4)
print(p.x, p.y)    # 3, 4
print(p[0], p[1])   # 3, 4 — also supports indexing

# With defaults (Python 3.6.1+)
Point = namedtuple("Point", ["x", "y", "z"], defaults=[0])
p = Point(3, 4)     # z defaults to 0

# Convert to dict
print(p._asdict())   # {'x': 3, 'y': 4, 'z': 0}

# Use case: structured data in ML pipelines
Result = namedtuple("Result", ["accuracy", "precision", "recall", "f1"])
metrics = Result(0.95, 0.93, 0.91, 0.92)
```

---

## 38. What is the purpose of `if __name__ == "__main__"`?

**Answer:** It ensures code runs only when the file is executed directly, not when imported:

```python
# train.py
def train_model():
    print("Training...")

if __name__ == "__main__":
    # Only runs when: python train.py
    # Does NOT run when: from train import train_model
    train_model()
```

`__name__` is set to `"__main__"` when the script is the entry point; otherwise it's set to the module name.

---

## 39. Explain `global` and `nonlocal` keywords.

**Answer:**

```python
count = 0

def increment():
    global count    # Refers to module-level variable
    count += 1

increment()
print(count)  # 1

def outer():
    x = 10
    def inner():
        nonlocal x  # Refers to enclosing function's variable
        x += 1
    inner()
    print(x)  # 11

outer()
```

**Best practice:** Avoid `global` — pass values as arguments and return results instead.

---

## 40. What are `any()` and `all()`?

**Answer:**

```python
# any() — True if ANY element is truthy
any([False, False, True])   # True
any([0, 0, 0])              # False

# all() — True if ALL elements are truthy
all([True, True, True])     # True
all([True, False, True])    # False

# Practical use in ML
predictions = [0.9, 0.8, 0.7, 0.6]
threshold = 0.5
all_above = all(p > threshold for p in predictions)  # True
any_below = any(p < threshold for p in predictions)  # False
```

---

## 41. What is the difference between `del`, `remove()`, and `pop()`?

**Answer:**

```python
lst = [1, 2, 3, 4, 5]

del lst[0]        # Removes by INDEX: [2, 3, 4, 5]
lst.remove(3)     # Removes first occurrence of VALUE: [2, 4, 5]
lst.pop()         # Removes & returns LAST item: [2, 4], returns 5
lst.pop(0)        # Removes & returns at INDEX: [4], returns 2
```

---

## 42. How do you create virtual environments?

**Answer:**

```bash
# venv (built-in)
python -m venv myenv
source myenv/bin/activate   # macOS/Linux
myenv\Scripts\activate      # Windows
pip install -r requirements.txt
deactivate

# conda
conda create -n myenv python=3.11
conda activate myenv
conda install numpy pandas scikit-learn
conda deactivate
```

**Best practice:** Always use virtual environments for project isolation, especially in AI/ML where package versions matter.

---

## 43. What are f-string debugging features (Python 3.8+)?

**Answer:**

```python
x = 42
name = "Alice"
data = [1, 2, 3]

# Self-documenting expressions with =
print(f"{x = }")            # x = 42
print(f"{name = }")         # name = 'Alice'
print(f"{len(data) = }")    # len(data) = 3
print(f"{x * 2 = }")        # x * 2 = 84

# With format specifiers
import math
print(f"{math.pi = :.4f}")  # math.pi = 3.1416
```

---

## 44. What are type hints in Python?

**Answer:**

```python
from typing import List, Dict, Optional, Tuple, Union, Callable

def train_model(
    data: List[float],
    epochs: int = 10,
    learning_rate: float = 0.001,
    callback: Optional[Callable] = None
) -> Dict[str, float]:
    """Train a model and return metrics."""
    return {"accuracy": 0.95, "loss": 0.05}

# Python 3.10+ syntax
def process(data: list[int] | None) -> dict[str, float]:
    ...

# TypeVar for generics
from typing import TypeVar
T = TypeVar('T')

def first(items: list[T]) -> T:
    return items[0]
```

Type hints don't affect runtime but help with IDE support, documentation, and static analysis tools like `mypy`.

---

## 45. What is the `collections` module?

**Answer:**

```python
from collections import (
    Counter,
    defaultdict,
    OrderedDict,
    deque,
    namedtuple,
    ChainMap
)

# Counter
Counter("hello world")  # Counter({'l': 3, 'o': 2, ...})

# deque — O(1) operations on both ends
dq = deque([1, 2, 3])
dq.appendleft(0)    # deque([0, 1, 2, 3])
dq.popleft()         # 0

# ChainMap — chain multiple dicts
defaults = {"color": "red", "size": "medium"}
user = {"color": "blue"}
config = ChainMap(user, defaults)
config["color"]  # "blue" (user overrides defaults)
config["size"]   # "medium" (falls back to defaults)
```

---

## 46. What is pickling and unpickling?

**Answer:** Serialization/deserialization of Python objects:

```python
import pickle

# Pickling (serialize)
model = {"weights": [0.1, 0.2], "bias": 0.5}
with open("model.pkl", "wb") as f:
    pickle.dump(model, f)

# Unpickling (deserialize)
with open("model.pkl", "rb") as f:
    loaded_model = pickle.load(f)

# For ML models, prefer joblib
import joblib
joblib.dump(trained_model, "model.joblib")
loaded = joblib.load("model.joblib")
```

**Warning:** Never unpickle data from untrusted sources — it can execute arbitrary code.

---

## 47. What is the `itertools` module?

**Answer:**

```python
import itertools

# Infinite iterators
itertools.count(10, 2)      # 10, 12, 14, ...
itertools.cycle([1, 2, 3])  # 1, 2, 3, 1, 2, 3, ...

# Combinatorics
list(itertools.permutations([1, 2, 3], 2))
# [(1,2), (1,3), (2,1), (2,3), (3,1), (3,2)]

list(itertools.combinations([1, 2, 3], 2))
# [(1,2), (1,3), (2,3)]

list(itertools.product([0, 1], repeat=3))
# [(0,0,0), (0,0,1), (0,1,0), ... (1,1,1)] — 8 combinations

# Chaining
list(itertools.chain([1, 2], [3, 4], [5]))  # [1, 2, 3, 4, 5]

# Grouping
data = [("A", 1), ("A", 2), ("B", 3)]
for key, group in itertools.groupby(data, key=lambda x: x[0]):
    print(key, list(group))
```

**Use case in ML:** `itertools.product` for hyperparameter grid search.

---

## 48. What is the `functools` module?

**Answer:**

```python
import functools

# lru_cache — memoization
@functools.lru_cache(maxsize=128)
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

# partial — fix function arguments
def power(base, exponent):
    return base ** exponent

square = functools.partial(power, exponent=2)
cube = functools.partial(power, exponent=3)

# reduce — cumulative operation
functools.reduce(lambda a, b: a * b, [1, 2, 3, 4])  # 24

# wraps — preserve function metadata in decorators
def my_decorator(func):
    @functools.wraps(func)  # Preserves __name__, __doc__, etc.
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper
```

---

## 49. How do you profile Python code?

**Answer:**

```python
# 1. timeit — micro-benchmarks
import timeit
timeit.timeit('sum(range(1000))', number=10000)

# 2. cProfile — function-level profiling
import cProfile
cProfile.run('my_function()')

# 3. line_profiler — line-by-line
# pip install line_profiler
# @profile decorator + kernprof -l script.py

# 4. memory_profiler
from memory_profiler import profile
@profile
def my_func():
    a = [i for i in range(1000000)]
    return a

# 5. sys.getsizeof
import sys
print(sys.getsizeof([1, 2, 3]))  # ~120 bytes
```

---

## 50. What are dataclasses?

**Answer:** Python 3.7+ feature that auto-generates boilerplate methods:

```python
from dataclasses import dataclass, field

@dataclass
class ModelConfig:
    name: str
    epochs: int = 10
    learning_rate: float = 0.001
    layers: list = field(default_factory=list)
    
    def __post_init__(self):
        if self.epochs <= 0:
            raise ValueError("epochs must be positive")

config = ModelConfig("ResNet", epochs=50, learning_rate=0.01)
print(config)
# ModelConfig(name='ResNet', epochs=50, learning_rate=0.01, layers=[])

# Auto-generated: __init__, __repr__, __eq__

# Frozen (immutable)
@dataclass(frozen=True)
class Point:
    x: float
    y: float
```
