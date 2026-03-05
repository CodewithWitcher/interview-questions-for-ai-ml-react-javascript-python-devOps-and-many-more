# Advanced Python Concepts — Interview Questions & Answers

## 1. What are decorators and how do they work internally?

**Answer:** A decorator is a function that takes another function as input, extends its behavior, and returns a modified function — without altering the original function's source code.

Internally, decorators use **closures** and **first-class functions**.

```python
import functools

def timer(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        import time
        start = time.perf_counter()
        result = func(*args, **kwargs)
        elapsed = time.perf_counter() - start
        print(f"{func.__name__} took {elapsed:.4f}s")
        return result
    return wrapper

@timer
def train_model(epochs):
    """Train a simple model."""
    total = sum(range(epochs * 100000))
    return total

# @timer is syntactic sugar for: train_model = timer(train_model)
train_model(10)
```

**Key points:**
- `@functools.wraps(func)` preserves the original function's `__name__`, `__doc__`, and `__module__`
- Decorators can be **stacked** — applied bottom-up
- They are evaluated at **import time**, not call time

---

## 2. How do you create a decorator that accepts arguments?

**Answer:** You need a **three-level nested function**: the outer function takes the decorator arguments, the middle function takes the decorated function, and the inner function is the actual wrapper.

```python
import functools

def repeat(n=2):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            results = []
            for _ in range(n):
                results.append(func(*args, **kwargs))
            return results
        return wrapper
    return decorator

@repeat(n=3)
def greet(name):
    return f"Hello, {name}!"

print(greet("Alice"))  # ['Hello, Alice!', 'Hello, Alice!', 'Hello, Alice!']
```

**AI/ML use case** — retry decorator for API calls:

```python
def retry(max_attempts=3, delay=1.0):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            import time
            for attempt in range(max_attempts):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    if attempt == max_attempts - 1:
                        raise
                    time.sleep(delay * (2 ** attempt))  # Exponential backoff
        return wrapper
    return decorator

@retry(max_attempts=5, delay=0.5)
def call_llm_api(prompt):
    # API call that might fail
    pass
```

---

## 3. What are generators and how do they differ from regular functions?

**Answer:** Generators are functions that use `yield` instead of `return`. They produce values **lazily** — one at a time — and maintain their state between calls.

| Feature | Regular Function | Generator |
|---------|-----------------|-----------|
| Keyword | `return` | `yield` |
| Memory | Stores all results | Produces one at a time |
| State | Lost after return | Preserved between yields |
| Returns | Value | Generator object (iterator) |
| Reusable | Yes | Single-pass only |

```python
# Memory-efficient data loading for ML
def batch_loader(data, batch_size=32):
    """Yield successive batches from data."""
    for i in range(0, len(data), batch_size):
        yield data[i:i + batch_size]

dataset = list(range(100))
for batch in batch_loader(dataset, batch_size=16):
    print(f"Processing batch of size {len(batch)}")
```

**Generator expressions** (like list comprehensions but lazy):

```python
# List comprehension — all in memory
squares_list = [x**2 for x in range(1000000)]  # ~8MB

# Generator expression — lazy evaluation
squares_gen = (x**2 for x in range(1000000))   # ~120 bytes
```

---

## 4. Explain the `yield from` statement.

**Answer:** `yield from` delegates to a sub-generator, simplifying nested generator chains.

```python
def flatten(nested):
    for item in nested:
        if isinstance(item, (list, tuple)):
            yield from flatten(item)  # Delegate to recursive call
        else:
            yield item

data = [1, [2, 3, [4, 5]], 6, [7, [8, 9]]]
print(list(flatten(data)))  # [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

Without `yield from`, you'd need an explicit loop:
```python
# Equivalent without yield from
for value in flatten(item):
    yield value
```

---

## 5. What are context managers and the `with` statement?

**Answer:** Context managers ensure proper **resource acquisition and release** using `__enter__` and `__exit__` methods. The `with` statement automates this pattern.

```python
# Using a class-based context manager
class ModelCheckpoint:
    def __init__(self, filepath):
        self.filepath = filepath
        self.best_loss = float('inf')

    def __enter__(self):
        print(f"Starting training, checkpointing to {self.filepath}")
        return self

    def __exit__(self, exc_type, exc_val, exc_tb):
        if exc_type is not None:
            print(f"Training interrupted: {exc_val}")
        print("Cleaning up resources...")
        return False  # Don't suppress exceptions

    def save_if_best(self, loss):
        if loss < self.best_loss:
            self.best_loss = loss
            print(f"New best loss: {loss:.4f}")

with ModelCheckpoint("model.pt") as ckpt:
    for epoch in range(5):
        loss = 1.0 / (epoch + 1)
        ckpt.save_if_best(loss)
```

**Using `contextlib`** for simpler cases:

```python
from contextlib import contextmanager

@contextmanager
def gpu_memory_tracker():
    import tracemalloc
    tracemalloc.start()
    yield
    current, peak = tracemalloc.get_traced_memory()
    tracemalloc.stop()
    print(f"Current: {current / 1024:.1f}KB, Peak: {peak / 1024:.1f}KB")

with gpu_memory_tracker():
    data = [i**2 for i in range(100000)]
```

---

## 6. What is the Global Interpreter Lock (GIL)?

**Answer:** The GIL is a **mutex** in CPython that allows only one thread to execute Python bytecode at a time, even on multi-core processors.

**Impact:**
- **CPU-bound tasks** — GIL is a bottleneck; use `multiprocessing` instead
- **I/O-bound tasks** — GIL is released during I/O; `threading` works fine

```python
import threading
import multiprocessing
import time

def cpu_intensive(n):
    return sum(i * i for i in range(n))

# Threading — limited by GIL for CPU work
def with_threads():
    threads = [threading.Thread(target=cpu_intensive, args=(10**7,)) for _ in range(4)]
    start = time.time()
    for t in threads: t.start()
    for t in threads: t.join()
    return time.time() - start

# Multiprocessing — bypasses GIL
def with_processes():
    start = time.time()
    with multiprocessing.Pool(4) as pool:
        pool.map(cpu_intensive, [10**7] * 4)
    return time.time() - start

# with_processes() is typically ~4x faster for CPU-bound work
```

**When the GIL is released:**
- File I/O, network I/O
- `time.sleep()`
- C extension code (NumPy operations, etc.)

---

## 7. Explain `asyncio` and async/await in Python.

**Answer:** `asyncio` provides a framework for writing **concurrent code** using coroutines, ideal for I/O-bound operations like API calls, database queries, and web scraping.

```python
import asyncio
import aiohttp

async def fetch_embedding(session, text):
    """Fetch embedding from an API asynchronously."""
    async with session.post(
        "https://api.openai.com/v1/embeddings",
        json={"input": text, "model": "text-embedding-3-small"},
        headers={"Authorization": "Bearer API_KEY"}
    ) as response:
        data = await response.json()
        return data["data"][0]["embedding"]

async def process_documents(documents):
    async with aiohttp.ClientSession() as session:
        tasks = [fetch_embedding(session, doc) for doc in documents]
        embeddings = await asyncio.gather(*tasks)
        return embeddings

# Run the async code
# asyncio.run(process_documents(["doc1", "doc2", "doc3"]))
```

**Key concepts:**
- `async def` — defines a coroutine
- `await` — suspends execution until the awaited coroutine completes
- `asyncio.gather()` — runs multiple coroutines concurrently
- `asyncio.create_task()` — schedules coroutine execution
- Event loop — manages and distributes execution of coroutines

---

## 8. What are metaclasses?

**Answer:** A metaclass is the **class of a class**. While a class defines how an instance behaves, a metaclass defines how a class behaves. The default metaclass is `type`.

```python
# type is the metaclass of all classes
class MyClass:
    pass

print(type(MyClass))      # <class 'type'>
print(type(MyClass()))    # <class '__main__.MyClass'>

# Creating a class dynamically using type
DynamicClass = type('DynamicClass', (object,), {'x': 10, 'greet': lambda self: "hi"})
```

**Custom metaclass example** — enforce that all methods have docstrings:

```python
class DocstringMeta(type):
    def __new__(mcs, name, bases, namespace):
        for key, value in namespace.items():
            if callable(value) and not key.startswith('_'):
                if not value.__doc__:
                    raise TypeError(f"Method '{key}' in '{name}' must have a docstring")
        return super().__new__(mcs, name, bases, namespace)

class MLModel(metaclass=DocstringMeta):
    def train(self, data):
        """Train the model on the given data."""
        pass

    def predict(self, X):
        """Make predictions."""
        pass

    # This would raise TypeError:
    # def evaluate(self, X, y):
    #     pass
```

---

## 9. What are descriptors in Python?

**Answer:** Descriptors are objects that define `__get__`, `__set__`, or `__delete__` methods. They customize attribute access on another class. Properties, classmethods, and staticmethods are all implemented using descriptors.

```python
class Hyperparameter:
    """A descriptor that validates hyperparameters."""
    def __init__(self, name, min_val=None, max_val=None):
        self.name = name
        self.min_val = min_val
        self.max_val = max_val

    def __set_name__(self, owner, name):
        self.storage_name = f"_{name}"

    def __get__(self, obj, objtype=None):
        if obj is None:
            return self
        return getattr(obj, self.storage_name, None)

    def __set__(self, obj, value):
        if self.min_val is not None and value < self.min_val:
            raise ValueError(f"{self.name} must be >= {self.min_val}")
        if self.max_val is not None and value > self.max_val:
            raise ValueError(f"{self.name} must be <= {self.max_val}")
        setattr(obj, self.storage_name, value)

class NeuralNetwork:
    learning_rate = Hyperparameter("learning_rate", min_val=0.0, max_val=1.0)
    epochs = Hyperparameter("epochs", min_val=1, max_val=10000)
    batch_size = Hyperparameter("batch_size", min_val=1, max_val=4096)

    def __init__(self, lr=0.001, epochs=100, batch_size=32):
        self.learning_rate = lr
        self.epochs = epochs
        self.batch_size = batch_size

nn = NeuralNetwork(lr=0.01, epochs=50)
# nn.learning_rate = 5.0  # ValueError: learning_rate must be <= 1.0
```

---

## 10. What is the Method Resolution Order (MRO)?

**Answer:** MRO defines the **order** in which Python searches classes when looking up a method in an inheritance hierarchy. Python uses the **C3 linearization** algorithm.

```python
class A:
    def method(self):
        return "A"

class B(A):
    def method(self):
        return "B"

class C(A):
    def method(self):
        return "C"

class D(B, C):
    pass

print(D.__mro__)
# (<class 'D'>, <class 'B'>, <class 'C'>, <class 'A'>, <class 'object'>)

d = D()
print(d.method())  # "B" — D -> B found first
```

**`super()` follows MRO**, not the parent class:

```python
class A:
    def method(self):
        print("A.method")

class B(A):
    def method(self):
        print("B.method")
        super().method()  # Calls C.method, not A.method!

class C(A):
    def method(self):
        print("C.method")
        super().method()

class D(B, C):
    def method(self):
        print("D.method")
        super().method()

D().method()
# D.method -> B.method -> C.method -> A.method
```

---

## 11. What are `__slots__` and when should you use them?

**Answer:** `__slots__` restricts instance attributes to a fixed set, replacing the default `__dict__` with a more memory-efficient structure.

```python
import sys

class WithDict:
    def __init__(self, x, y):
        self.x = x
        self.y = y

class WithSlots:
    __slots__ = ('x', 'y')
    def __init__(self, x, y):
        self.x = x
        self.y = y

a = WithDict(1, 2)
b = WithSlots(1, 2)

print(sys.getsizeof(a.__dict__))  # ~104 bytes
# b.__dict__  # AttributeError: no __dict__

# When creating millions of objects (e.g., data points in ML):
# WithSlots uses ~40-50% less memory per instance
```

**Use cases in AI/ML:** When creating millions of lightweight objects like tokens, data points, or graph nodes.

---

## 12. Explain the difference between `deepcopy` and `copy`.

**Answer:**

| Feature | `copy.copy()` (Shallow) | `copy.deepcopy()` (Deep) |
|---------|------------------------|--------------------------|
| Nested objects | Shares references | Creates independent copies |
| Performance | Faster | Slower |
| Memory | Less | More |

```python
import copy

original = {"model": "GPT", "params": [1, 2, 3], "config": {"lr": 0.01}}

shallow = copy.copy(original)
deep = copy.deepcopy(original)

original["params"].append(4)
original["config"]["lr"] = 0.1

print(shallow["params"])      # [1, 2, 3, 4] — shared reference!
print(shallow["config"]["lr"])  # 0.1 — shared reference!

print(deep["params"])          # [1, 2, 3] — independent copy
print(deep["config"]["lr"])    # 0.01 — independent copy
```

---

## 13. What are `dataclasses` and their advantages?

**Answer:** `dataclasses` (Python 3.7+) automatically generate `__init__`, `__repr__`, `__eq__`, and other methods based on class annotations.

```python
from dataclasses import dataclass, field
from typing import List, Optional

@dataclass
class ExperimentConfig:
    model_name: str
    learning_rate: float = 0.001
    batch_size: int = 32
    epochs: int = 100
    tags: List[str] = field(default_factory=list)
    description: Optional[str] = None

    def __post_init__(self):
        if self.learning_rate <= 0:
            raise ValueError("Learning rate must be positive")

# Auto-generated __init__, __repr__, __eq__
config1 = ExperimentConfig("ResNet50", learning_rate=0.01, tags=["vision", "benchmark"])
config2 = ExperimentConfig("ResNet50", learning_rate=0.01, tags=["vision", "benchmark"])

print(config1)       # ExperimentConfig(model_name='ResNet50', ...)
print(config1 == config2)  # True

# Frozen (immutable) dataclass
@dataclass(frozen=True)
class ModelVersion:
    name: str
    version: str
    checksum: str
```

---

## 14. How does Python's garbage collection work?

**Answer:** Python uses **two mechanisms** for memory management:

1. **Reference counting** — primary mechanism; frees memory when count drops to zero
2. **Generational garbage collector** — handles circular references

```python
import gc
import sys

# Reference counting
a = [1, 2, 3]
print(sys.getrefcount(a))  # 2 (a + function arg)

b = a  # refcount = 3
del b  # refcount = 2

# Circular reference — cannot be freed by refcount alone
class Node:
    def __init__(self):
        self.ref = None

n1 = Node()
n2 = Node()
n1.ref = n2
n2.ref = n1  # Circular!

del n1, n2  # Refcount won't reach 0
gc.collect()  # Generational GC handles this

# GC generations
print(gc.get_count())      # (count0, count1, count2)
print(gc.get_threshold())  # (700, 10, 10) — default thresholds
```

**Generational GC:**
- **Generation 0** — newly created objects; collected most frequently
- **Generation 1** — survived one collection
- **Generation 2** — long-lived objects; collected least frequently

---

## 15. Explain Python's type hinting system.

**Answer:** Type hints (PEP 484+) add optional static type annotations. They don't affect runtime but enable tools like `mypy`, IDE autocompletion, and code documentation.

```python
from typing import (
    List, Dict, Tuple, Optional, Union,
    Callable, Iterator, TypeVar, Generic, Protocol
)
import numpy as np

# Basic types
def preprocess(text: str, max_length: int = 512) -> List[int]:
    tokens: List[int] = [ord(c) for c in text[:max_length]]
    return tokens

# Optional and Union
def load_model(path: str, device: Optional[str] = None) -> Dict[str, np.ndarray]:
    ...

# Callable type
def apply_transform(
    data: List[float],
    transform: Callable[[float], float]
) -> List[float]:
    return [transform(x) for x in data]

# TypeVar for generics
T = TypeVar('T')

def first_element(items: List[T]) -> T:
    return items[0]

# Protocol (structural subtyping) — Python 3.8+
class Predictable(Protocol):
    def predict(self, X: np.ndarray) -> np.ndarray: ...
    def fit(self, X: np.ndarray, y: np.ndarray) -> None: ...

def evaluate_model(model: Predictable, X_test: np.ndarray):
    """Accepts any object with predict() and fit() methods."""
    return model.predict(X_test)
```

---

## 16. What are `__new__` vs `__init__`?

**Answer:**

| Aspect | `__new__` | `__init__` |
|--------|-----------|------------|
| Purpose | Creates the instance | Initializes the instance |
| Called | Before `__init__` | After `__new__` |
| Returns | The new instance | `None` |
| Use case | Custom creation, singletons, immutables | Setting attributes |

```python
class Singleton:
    _instance = None

    def __new__(cls, *args, **kwargs):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance

    def __init__(self, name="default"):
        self.name = name

a = Singleton("first")
b = Singleton("second")
print(a is b)       # True — same object
print(a.name)       # "second" — __init__ ran again
```

**Immutable subclassing pattern:**
```python
class PositiveInt(int):
    def __new__(cls, value):
        if value < 0:
            raise ValueError("Must be positive")
        return super().__new__(cls, value)
```

---

## 17. What is monkey patching?

**Answer:** Monkey patching is dynamically modifying a class or module at runtime. It's useful for testing/mocking but considered a code smell in production.

```python
# Monkey patching for testing
class ModelAPI:
    def predict(self, data):
        # Expensive API call
        return expensive_api_call(data)

# In tests:
def mock_predict(self, data):
    return [0.5] * len(data)

ModelAPI.predict = mock_predict  # Monkey patch

api = ModelAPI()
print(api.predict([1, 2, 3]))  # [0.5, 0.5, 0.5]

# Better alternative: use unittest.mock
from unittest.mock import patch

with patch.object(ModelAPI, 'predict', return_value=[0.5]):
    api = ModelAPI()
    print(api.predict([1, 2, 3]))
```

---

## 18. Explain `functools.lru_cache` and caching strategies.

**Answer:** `lru_cache` is a decorator that memoizes function results using a Least Recently Used cache. Ideal for **expensive, pure functions** with hashable arguments.

```python
from functools import lru_cache, cache  # cache is Python 3.9+

@lru_cache(maxsize=128)
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)

print(fibonacci(100))  # Instant, without cache would be exponential
print(fibonacci.cache_info())
# CacheInfo(hits=98, misses=101, maxsize=128, currsize=101)

# Caching expensive computations in ML
@lru_cache(maxsize=32)
def compute_similarity(text_a: str, text_b: str) -> float:
    """Cache repeated similarity computations."""
    # expensive embedding + cosine similarity
    return cosine_sim(get_embedding(text_a), get_embedding(text_b))

# Clear cache when needed
fibonacci.cache_clear()
```

---

## 19. What is the `__call__` method?

**Answer:** `__call__` makes an instance callable like a function. Useful for stateful functions, function objects, and ML model wrappers.

```python
class EarlyStopping:
    """Callable object to track training progress."""
    def __init__(self, patience=5, min_delta=0.001):
        self.patience = patience
        self.min_delta = min_delta
        self.best_loss = float('inf')
        self.counter = 0

    def __call__(self, val_loss):
        if val_loss < self.best_loss - self.min_delta:
            self.best_loss = val_loss
            self.counter = 0
            return False  # Continue training
        self.counter += 1
        return self.counter >= self.patience  # Stop if patience exceeded

early_stop = EarlyStopping(patience=3)

losses = [0.5, 0.4, 0.35, 0.36, 0.37, 0.38]
for epoch, loss in enumerate(losses):
    if early_stop(loss):
        print(f"Early stopping at epoch {epoch}")
        break
```

---

## 20. Explain `itertools` and common patterns for data processing.

**Answer:** `itertools` provides memory-efficient tools for working with iterators.

```python
import itertools

# chain — combine multiple iterables
train = [1, 2, 3]
val = [4, 5]
test = [6, 7]
all_data = list(itertools.chain(train, val, test))  # [1, 2, 3, 4, 5, 6, 7]

# islice — slice an iterator (useful for large files)
with open("large_dataset.csv") as f:
    first_100 = list(itertools.islice(f, 100))

# product — Cartesian product for hyperparameter grids
learning_rates = [0.001, 0.01, 0.1]
batch_sizes = [16, 32, 64]
for lr, bs in itertools.product(learning_rates, batch_sizes):
    print(f"lr={lr}, batch_size={bs}")  # 9 combinations

# combinations — feature selection
features = ['age', 'income', 'score', 'tenure']
for combo in itertools.combinations(features, 2):
    print(f"Feature pair: {combo}")

# groupby — group sorted data
data = [("cat", 0.9), ("dog", 0.8), ("cat", 0.7), ("dog", 0.6)]
data.sort(key=lambda x: x[0])
for key, group in itertools.groupby(data, key=lambda x: x[0]):
    items = list(group)
    print(f"{key}: {items}")

# accumulate — running totals (useful for cumulative metrics)
losses = [0.5, 0.4, 0.35, 0.32, 0.30]
running_avg = list(itertools.accumulate(
    losses, lambda a, b: (a + b) / 2
))
```

---

## 21. What are Abstract Base Classes (ABCs)?

**Answer:** ABCs define interfaces that subclasses **must** implement. They enforce a contract without providing full implementation.

```python
from abc import ABC, abstractmethod
from typing import List
import numpy as np

class BaseModel(ABC):
    """Abstract base class for all ML models."""

    @abstractmethod
    def fit(self, X: np.ndarray, y: np.ndarray) -> None:
        """Train the model."""
        ...

    @abstractmethod
    def predict(self, X: np.ndarray) -> np.ndarray:
        """Make predictions."""
        ...

    def score(self, X: np.ndarray, y: np.ndarray) -> float:
        """Default scoring — can be overridden."""
        predictions = self.predict(X)
        return np.mean(predictions == y)

class LinearModel(BaseModel):
    def fit(self, X, y):
        self.weights = np.linalg.lstsq(X, y, rcond=None)[0]

    def predict(self, X):
        return X @ self.weights

# BaseModel()  # TypeError: Can't instantiate abstract class
model = LinearModel()  # OK
```

---

## 22. What is `multiprocessing` and how does it bypass the GIL?

**Answer:** `multiprocessing` spawns **separate Python processes**, each with its own GIL and memory space. It achieves true parallelism for CPU-bound tasks.

```python
from multiprocessing import Pool, Process, Queue
import numpy as np

def process_chunk(chunk):
    """CPU-intensive processing of a data chunk."""
    return np.mean(np.array(chunk) ** 2)

# Using Pool for parallel map
data = [list(range(i, i + 1000)) for i in range(0, 10000, 1000)]

with Pool(processes=4) as pool:
    results = pool.map(process_chunk, data)
    print(results)

# Shared state with Queue
def worker(q, data):
    result = sum(data)
    q.put(result)

q = Queue()
processes = []
for chunk in data:
    p = Process(target=worker, args=(q, chunk))
    processes.append(p)
    p.start()

for p in processes:
    p.join()

results = [q.get() for _ in processes]
```

**Key considerations:**
- Process creation has higher overhead than threads
- Data must be **serializable** (pickle) to pass between processes
- Use `multiprocessing.shared_memory` for large arrays (Python 3.8+)

---

## 23. What are `__getattr__`, `__getattribute__`, and `__setattr__`?

**Answer:** These dunder methods control attribute access:

| Method | Called When |
|--------|------------|
| `__getattribute__` | Every attribute access (before lookup) |
| `__getattr__` | Only when normal lookup fails |
| `__setattr__` | Every attribute assignment |

```python
class LazyLoader:
    """Lazily load expensive resources on first access."""
    def __init__(self):
        self._cache = {}

    def __getattr__(self, name):
        if name.startswith('_'):
            raise AttributeError(name)
        if name not in self._cache:
            print(f"Loading {name}...")
            self._cache[name] = f"Loaded:{name}"
        return self._cache[name]

loader = LazyLoader()
print(loader.model)     # "Loading model..." → "Loaded:model"
print(loader.model)     # "Loaded:model" (cached via __getattr__ not called)
```

---

## 24. Explain `collections` module — key data structures.

**Answer:**

```python
from collections import (
    defaultdict, Counter, OrderedDict,
    namedtuple, deque, ChainMap
)

# defaultdict — auto-create missing keys
word_freq = defaultdict(int)
for word in ["the", "cat", "sat", "on", "the", "mat"]:
    word_freq[word] += 1

# Counter — count elements
labels = ["cat", "dog", "cat", "bird", "dog", "cat"]
counter = Counter(labels)
print(counter.most_common(2))  # [('cat', 3), ('dog', 2)]

# deque — efficient append/pop from both ends
recent_losses = deque(maxlen=10)  # Sliding window of last 10 losses
for loss in [0.5, 0.4, 0.35, 0.3]:
    recent_losses.append(loss)

# namedtuple — lightweight immutable objects
Prediction = namedtuple('Prediction', ['label', 'confidence', 'latency_ms'])
pred = Prediction('cat', 0.95, 12.3)
print(f"{pred.label}: {pred.confidence:.0%}")  # cat: 95%

# ChainMap — search multiple dicts
defaults = {"lr": 0.001, "epochs": 100}
overrides = {"lr": 0.01}
config = ChainMap(overrides, defaults)
print(config["lr"])      # 0.01 (from overrides)
print(config["epochs"])  # 100 (from defaults)
```

---

## 25. What is `threading` and when to use it vs `multiprocessing`?

**Answer:**

| Feature | `threading` | `multiprocessing` |
|---------|------------|-------------------|
| GIL | Limited by GIL | Bypasses GIL |
| Best for | I/O-bound tasks | CPU-bound tasks |
| Memory | Shared | Separate per process |
| Overhead | Low | High |
| Communication | Easy (shared vars) | IPC (Queue, Pipe) |

```python
import threading
from concurrent.futures import ThreadPoolExecutor
import requests

# I/O-bound: downloading files — perfect for threading
def download_file(url):
    response = requests.get(url)
    return len(response.content)

urls = ["https://example.com/data1", "https://example.com/data2"]

# ThreadPoolExecutor — high-level API
with ThreadPoolExecutor(max_workers=5) as executor:
    futures = [executor.submit(download_file, url) for url in urls]
    results = [f.result() for f in futures]

# Thread safety with locks
counter_lock = threading.Lock()
shared_counter = 0

def increment():
    global shared_counter
    with counter_lock:
        shared_counter += 1
```

---

## 26. Explain `weakref` and its use cases.

**Answer:** `weakref` creates references that **don't prevent garbage collection**. Useful for caches, observer patterns, and avoiding circular reference memory leaks.

```python
import weakref

class LargeModel:
    def __init__(self, name):
        self.name = name
        self.weights = [0.0] * 1000000  # Large data

# Strong reference keeps object alive
model = LargeModel("GPT")

# Weak reference doesn't prevent GC
weak_model = weakref.ref(model)
print(weak_model())          # <LargeModel object>
print(weak_model().name)     # "GPT"

del model
print(weak_model())          # None — garbage collected

# WeakValueDictionary for caches
model_cache = weakref.WeakValueDictionary()

def get_model(name):
    if name not in model_cache:
        model_cache[name] = LargeModel(name)
    return model_cache[name]
```

---

## 27. What are coroutines and how do they differ from generators?

**Answer:**

| Feature | Generator | Coroutine |
|---------|-----------|-----------|
| Declaration | `def` + `yield` | `async def` |
| Consumption | `next()` / `for` loop | `await` |
| Purpose | Produce values lazily | Async I/O operations |
| Scheduling | Manual iteration | Event loop |

```python
import asyncio

# Generator — produces values
def data_stream():
    for i in range(5):
        yield i * 10

# Coroutine — async operation
async def fetch_data(item_id):
    await asyncio.sleep(0.1)  # Simulates async I/O
    return {"id": item_id, "value": item_id * 10}

async def main():
    # Run multiple coroutines concurrently
    tasks = [fetch_data(i) for i in range(5)]
    results = await asyncio.gather(*tasks)
    print(results)

# asyncio.run(main())
```

---

## 28. What are `property` decorators?

**Answer:** `@property` creates managed attributes with getter/setter/deleter methods, allowing you to add validation and computed attributes while using attribute syntax.

```python
class Dataset:
    def __init__(self, data):
        self._data = data
        self._split_ratio = 0.8

    @property
    def size(self):
        """Read-only computed property."""
        return len(self._data)

    @property
    def split_ratio(self):
        return self._split_ratio

    @split_ratio.setter
    def split_ratio(self, value):
        if not 0.0 < value < 1.0:
            raise ValueError("Split ratio must be between 0 and 1")
        self._split_ratio = value

    @property
    def train_size(self):
        return int(self.size * self._split_ratio)

    @property
    def test_size(self):
        return self.size - self.train_size

ds = Dataset(list(range(1000)))
print(ds.size)        # 1000
print(ds.train_size)  # 800
ds.split_ratio = 0.7
print(ds.train_size)  # 700
# ds.size = 500       # AttributeError — read-only
```

---

## 29. How do you handle pickling and serialization?

**Answer:** Pickle serializes Python objects to byte streams. Customize with `__getstate__`/`__setstate__` or `__reduce__`.

```python
import pickle
import json

class TrainedModel:
    def __init__(self, name, weights, logger=None):
        self.name = name
        self.weights = weights
        self.logger = logger  # Not serializable

    def __getstate__(self):
        """Called during pickling — exclude non-serializable attrs."""
        state = self.__dict__.copy()
        del state['logger']  # Remove unpicklable object
        return state

    def __setstate__(self, state):
        """Called during unpickling — restore state."""
        self.__dict__.update(state)
        self.logger = None  # Re-initialize

model = TrainedModel("bert", [0.1, 0.2, 0.3], logger="<logger>")

# Serialize
data = pickle.dumps(model)
restored = pickle.loads(data)
print(restored.name)     # "bert"
print(restored.logger)   # None
```

**Security warning:** Never unpickle data from untrusted sources — it can execute arbitrary code.

---

## 30. What are `__enter__` and `__exit__` in context managers?

**Answer:**

- `__enter__` — called when entering the `with` block; returns the resource
- `__exit__` — called when leaving the `with` block; handles cleanup and exceptions

```python
class DatabaseConnection:
    def __init__(self, connection_string):
        self.connection_string = connection_string
        self.conn = None

    def __enter__(self):
        print(f"Connecting to {self.connection_string}")
        self.conn = {"status": "connected"}  # Simulated
        return self

    def __exit__(self, exc_type, exc_val, exc_tb):
        print("Closing connection")
        self.conn = None
        if exc_type is not None:
            print(f"Exception occurred: {exc_val}")
            return False  # Propagate exception
        return True

    def query(self, sql):
        return f"Results for: {sql}"

with DatabaseConnection("postgresql://localhost/ml_db") as db:
    results = db.query("SELECT * FROM experiments")
```

---

## 31. Explain `concurrent.futures` module.

**Answer:** `concurrent.futures` provides a high-level interface for asynchronous execution using thread or process pools.

```python
from concurrent.futures import (
    ThreadPoolExecutor, ProcessPoolExecutor,
    as_completed, wait
)
import time

def train_model(config):
    """Simulate model training."""
    time.sleep(config["epochs"] * 0.01)
    return {"config": config, "accuracy": 0.85 + config["lr"]}

configs = [
    {"lr": 0.001, "epochs": 10},
    {"lr": 0.01, "epochs": 5},
    {"lr": 0.1, "epochs": 3},
]

# Process results as they complete
with ProcessPoolExecutor(max_workers=3) as executor:
    future_to_config = {
        executor.submit(train_model, cfg): cfg for cfg in configs
    }

    for future in as_completed(future_to_config):
        config = future_to_config[future]
        try:
            result = future.result()
            print(f"lr={config['lr']}: accuracy={result['accuracy']:.3f}")
        except Exception as e:
            print(f"lr={config['lr']} failed: {e}")
```

---

## 32. What is `__init_subclass__` and how is it used?

**Answer:** `__init_subclass__` is a hook called when a class is subclassed. It's a simpler alternative to metaclasses for class registration or validation.

```python
class ModelRegistry:
    _registry = {}

    def __init_subclass__(cls, model_type=None, **kwargs):
        super().__init_subclass__(**kwargs)
        if model_type:
            ModelRegistry._registry[model_type] = cls
            cls.model_type = model_type

    @classmethod
    def create(cls, model_type, **kwargs):
        if model_type not in cls._registry:
            raise ValueError(f"Unknown model: {model_type}")
        return cls._registry[model_type](**kwargs)

class LinearRegression(ModelRegistry, model_type="linear"):
    def __init__(self, **kwargs):
        self.params = kwargs

class RandomForest(ModelRegistry, model_type="rf"):
    def __init__(self, **kwargs):
        self.params = kwargs

# Auto-registered!
model = ModelRegistry.create("linear", fit_intercept=True)
print(ModelRegistry._registry)  # {'linear': <class 'LinearRegression'>, 'rf': ...}
```

---

## 33. How does `enumerate`, `zip`, and `map` work internally?

**Answer:** These are **lazy iterators** that produce values on demand.

```python
# enumerate — adds index to iterable
labels = ["cat", "dog", "bird"]
for idx, label in enumerate(labels, start=1):
    print(f"{idx}: {label}")

# zip — pairs elements from multiple iterables
predictions = [0.9, 0.8, 0.7]
for label, score in zip(labels, predictions):
    print(f"{label}: {score}")

# zip_longest — handles uneven iterables
from itertools import zip_longest
a = [1, 2, 3]
b = [10, 20]
list(zip_longest(a, b, fillvalue=0))  # [(1,10), (2,20), (3,0)]

# map — apply function to each element
normalized = list(map(lambda x: x / max(predictions), predictions))

# They're all lazy — implementing enumerate:
def my_enumerate(iterable, start=0):
    count = start
    for item in iterable:
        yield count, item
        count += 1
```

---

## 34. What is the `walrus operator` (`:=`)?

**Answer:** The walrus operator (Python 3.8+) assigns values as part of an expression.

```python
# Without walrus
line = input()
while line != "quit":
    process(line)
    line = input()

# With walrus — cleaner
while (line := input()) != "quit":
    process(line)

# Useful in list comprehensions with filtering
import re
data = ["model_v1.pkl", "data.csv", "model_v2.pkl", "readme.txt"]
models = [
    match.group()
    for item in data
    if (match := re.search(r'model_v\d+', item))
]

# In data processing
values = [1, 5, 3, 8, 2, 9, 4]
filtered = [
    y for x in values
    if (y := x ** 2) > 10
]  # [25, 64, 81, 16]
```

---

## 35. Explain Python's `__mro_entries__` and how `typing` uses it.

**Answer:** `__mro_entries__` allows non-class objects to participate in class hierarchies. The `typing` module uses this to make `List[int]`, `Dict[str, int]`, etc., work as base classes.

```python
from typing import Generic, TypeVar

T = TypeVar('T')

class Repository(Generic[T]):
    def __init__(self):
        self.items: list[T] = []

    def add(self, item: T) -> None:
        self.items.append(item)

    def get_all(self) -> list[T]:
        return self.items

class ModelRepository(Repository["MLModel"]):
    def get_best(self):
        return max(self.items, key=lambda m: m.accuracy)
```

---

## 36. What is `struct`, `memoryview`, and binary data handling?

**Answer:**

```python
import struct

# struct — pack/unpack binary data
# Format: '>' big-endian, 'I' unsigned int, 'f' float, '10s' 10-char string
header = struct.pack('>If10s', 42, 3.14, b'model_data')
values = struct.unpack('>If10s', header)
print(values)  # (42, 3.140000104904175, b'model_data')

# memoryview — zero-copy access to buffer data
data = bytearray(b'\x00\x01\x02\x03\x04\x05\x06\x07')
view = memoryview(data)

# Slice without copying
chunk = view[2:6]
chunk[0] = 0xFF  # Modifies original data!
print(data)  # bytearray(b'\x00\x01\xff\x03\x04\x05\x06\x07')

# Useful for processing large binary files/tensors without copying
import numpy as np
arr = np.zeros((1000, 1000), dtype=np.float32)
view = memoryview(arr)
print(view.nbytes)  # 4000000 bytes, no copy made
```

---

## 37. Explain exception handling best practices.

**Answer:**

```python
# 1. Catch specific exceptions
try:
    result = perform_training()
except ValueError as e:
    print(f"Invalid input: {e}")
except RuntimeError as e:
    print(f"Training error: {e}")
except Exception as e:
    print(f"Unexpected error: {e}")
    raise  # Re-raise unexpected exceptions

# 2. Custom exception hierarchy
class MLError(Exception):
    """Base exception for ML pipeline."""
    pass

class DataValidationError(MLError):
    """Raised when input data is invalid."""
    def __init__(self, column, reason):
        self.column = column
        self.reason = reason
        super().__init__(f"Column '{column}': {reason}")

class ModelNotTrainedError(MLError):
    """Raised when predict() is called before fit()."""
    pass

# 3. Exception chaining
try:
    load_data("corrupt.csv")
except FileNotFoundError as e:
    raise DataValidationError("file", "not found") from e

# 4. else and finally
try:
    model = load_model("model.pkl")
except FileNotFoundError:
    model = train_new_model()
else:
    print("Model loaded successfully")  # Only runs if no exception
finally:
    cleanup_temp_files()  # Always runs
```

---

## 38. What is `functools.partial` and `functools.reduce`?

**Answer:**

```python
from functools import partial, reduce

# partial — freeze some function arguments
def train(model, data, lr=0.001, epochs=100):
    return f"Training {model} for {epochs} epochs at lr={lr}"

# Create specialized versions
train_fast = partial(train, lr=0.1, epochs=10)
train_bert = partial(train, "BERT")

print(train_fast("ResNet", "imagenet"))  # Training ResNet for 10 epochs at lr=0.1
print(train_bert("corpus", epochs=50))    # Training BERT for 50 epochs at lr=0.001

# reduce — cumulative operation on a sequence
numbers = [1, 2, 3, 4, 5]
product = reduce(lambda a, b: a * b, numbers)  # 120

# Combine multiple DataFrames
import pandas as pd
dfs = [pd.DataFrame({"a": [i]}) for i in range(3)]
combined = reduce(lambda a, b: pd.concat([a, b]), dfs)
```

---

## 39. How do you profile and optimize Python code?

**Answer:**

```python
# 1. timeit — micro-benchmarks
import timeit

time_list = timeit.timeit('[x**2 for x in range(1000)]', number=1000)
time_gen = timeit.timeit('list(x**2 for x in range(1000))', number=1000)
print(f"List comp: {time_list:.3f}s, Generator: {time_gen:.3f}s")

# 2. cProfile — function-level profiling
import cProfile

def process_data():
    data = [i**2 for i in range(100000)]
    sorted_data = sorted(data, reverse=True)
    return sum(sorted_data[:100])

cProfile.run('process_data()')

# 3. memory_profiler — line-by-line memory usage
# pip install memory-profiler
# @profile  # Use with: python -m memory_profiler script.py
# def memory_heavy():
#     big_list = [i for i in range(1000000)]
#     return sum(big_list)

# 4. sys.getsizeof for quick checks
import sys
print(sys.getsizeof([]))       # 56 bytes (empty list)
print(sys.getsizeof({}))       # 64 bytes (empty dict)
print(sys.getsizeof(set()))    # 216 bytes (empty set)

# 5. Optimization tips
# - Use built-in functions (they're C-optimized)
# - Use generators for large data
# - Use numpy for numerical operations
# - Use __slots__ for memory-heavy classes
# - Avoid global variable lookups in tight loops
# - Use collections.deque instead of list for queue operations
```

---

## 40. What is the `match` statement (Structural Pattern Matching)?

**Answer:** Python 3.10+ introduced `match`/`case` for structural pattern matching — far more powerful than switch/case in other languages.

```python
# Basic pattern matching
def handle_response(response):
    match response:
        case {"status": 200, "data": data}:
            return f"Success: {data}"
        case {"status": 404}:
            return "Not found"
        case {"status": status} if status >= 500:
            return f"Server error: {status}"
        case _:
            return "Unknown response"

# With classes
from dataclasses import dataclass

@dataclass
class TrainEvent:
    epoch: int
    loss: float

@dataclass
class EvalEvent:
    metric: str
    value: float

def log_event(event):
    match event:
        case TrainEvent(epoch=e, loss=l) if l < 0.01:
            print(f"Epoch {e}: Converged! Loss={l}")
        case TrainEvent(epoch=e, loss=l):
            print(f"Epoch {e}: Loss={l:.4f}")
        case EvalEvent(metric="accuracy", value=v) if v > 0.95:
            print(f"Excellent accuracy: {v:.2%}")
        case EvalEvent(metric=m, value=v):
            print(f"{m}: {v}")
        case _:
            print(f"Unknown event: {event}")

log_event(TrainEvent(10, 0.005))   # Epoch 10: Converged! Loss=0.005
log_event(EvalEvent("accuracy", 0.97))  # Excellent accuracy: 97.00%
```

---

*Total: 40 Questions covering decorators, generators, concurrency, metaclasses, type hints, memory management, profiling, and modern Python features — all with AI/ML-focused examples.*
