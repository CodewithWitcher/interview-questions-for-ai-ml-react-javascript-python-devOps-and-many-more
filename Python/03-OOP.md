# Object-Oriented Programming in Python — Interview Questions & Answers

## 1. What are the four pillars of OOP?

**Answer:**

1. **Encapsulation** — Bundling data and methods together, restricting direct access
2. **Abstraction** — Hiding complex implementation, exposing only interfaces
3. **Inheritance** — Deriving new classes from existing ones
4. **Polymorphism** — Same interface, different implementations

```python
from abc import ABC, abstractmethod

# Abstraction
class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

# Inheritance + Encapsulation
class Circle(Shape):
    def __init__(self, radius):
        self.__radius = radius  # Private attribute
    
    @property
    def radius(self):
        return self.__radius
    
    # Polymorphism
    def area(self):
        return 3.14159 * self.__radius ** 2

class Rectangle(Shape):
    def __init__(self, w, h):
        self.__w, self.__h = w, h
    
    def area(self):
        return self.__w * self.__h

shapes = [Circle(5), Rectangle(3, 4)]
for s in shapes:
    print(s.area())  # Polymorphic call
```

---

## 2. What is the difference between `__init__` and `__new__`?

**Answer:**

- `__new__` creates the instance (allocates memory) — called **before** `__init__`
- `__init__` initializes the instance (sets attributes)

```python
class Singleton:
    _instance = None
    
    def __new__(cls, *args, **kwargs):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance
    
    def __init__(self, value=None):
        self.value = value

a = Singleton(1)
b = Singleton(2)
print(a is b)      # True — same instance
print(a.value)     # 2 — __init__ ran twice
```

---

## 3. Explain Python's name mangling and access modifiers.

**Answer:**

Python uses naming conventions (no true access modifiers):

| Convention | Meaning | Access |
|-----------|---------|--------|
| `name` | Public | Accessible everywhere |
| `_name` | Protected (convention) | Accessible, but "internal use" |
| `__name` | Private (name mangled) | Mangled to `_ClassName__name` |
| `__name__` | Dunder/magic | Special Python methods |

```python
class MyClass:
    def __init__(self):
        self.public = "anyone"
        self._protected = "internal"
        self.__private = "mangled"

obj = MyClass()
print(obj.public)           # "anyone"
print(obj._protected)       # "internal" — accessible but discouraged
# print(obj.__private)      # AttributeError!
print(obj._MyClass__private)  # "mangled" — name mangling
```

---

## 4. What are class methods, static methods, and instance methods?

**Answer:**

```python
class MyClass:
    class_var = 0
    
    def instance_method(self):
        """Accesses instance (self) and class."""
        return f"instance: {self}"
    
    @classmethod
    def class_method(cls):
        """Accesses class (cls) but not instance."""
        cls.class_var += 1
        return f"class: {cls.class_var}"
    
    @staticmethod
    def static_method(x, y):
        """No access to instance or class. Utility function."""
        return x + y
```

| Type | First arg | Can access instance? | Can access class? | Use case |
|------|-----------|---------------------|-------------------|----------|
| Instance | `self` | Yes | Yes | Most methods |
| Class | `cls` | No | Yes | Factory methods, class state |
| Static | None | No | No | Utility functions |

---

## 5. Explain Python's MRO (Method Resolution Order).

**Answer:**

MRO determines the order in which base classes are searched when calling a method. Python uses the **C3 Linearization** algorithm.

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

d = D()
print(d.method())  # "B"
print(D.__mro__)
# (D, B, C, A, object)

# Using super() follows MRO
class B(A):
    def method(self):
        return "B -> " + super().method()

class C(A):
    def method(self):
        return "C -> " + super().method()

class D(B, C):
    def method(self):
        return "D -> " + super().method()

print(D().method())  # "D -> B -> C -> A"
```

---

## 6. What are Python descriptors?

**Answer:**

Descriptors are objects that define `__get__`, `__set__`, or `__delete__` — they customize attribute access.

```python
class Validator:
    def __init__(self, min_val, max_val):
        self.min_val = min_val
        self.max_val = max_val
    
    def __set_name__(self, owner, name):
        self.name = name
    
    def __get__(self, obj, objtype=None):
        return getattr(obj, f'_{self.name}', None)
    
    def __set__(self, obj, value):
        if not self.min_val <= value <= self.max_val:
            raise ValueError(f"{self.name} must be between {self.min_val} and {self.max_val}")
        setattr(obj, f'_{self.name}', value)

class Student:
    grade = Validator(0, 100)
    age = Validator(5, 25)
    
    def __init__(self, name, grade, age):
        self.name = name
        self.grade = grade  # Uses descriptor __set__
        self.age = age

s = Student("Alice", 95, 20)
# s.grade = 150  # ValueError!
```

---

## 7. What is the difference between shallow and deep copy?

**Answer:**

```python
import copy

original = [[1, 2, 3], [4, 5, 6]]

# Shallow copy — new outer list, same inner lists
shallow = copy.copy(original)
shallow[0][0] = 99
print(original[0][0])  # 99 — inner list is shared!

# Deep copy — completely independent copy
original = [[1, 2, 3], [4, 5, 6]]
deep = copy.deepcopy(original)
deep[0][0] = 99
print(original[0][0])  # 1 — unaffected
```

| Aspect | Shallow Copy | Deep Copy |
|--------|-------------|-----------|
| Nested objects | References shared | Fully cloned |
| Performance | Faster | Slower |
| Method | `copy.copy()`, `list()`, `[:]` | `copy.deepcopy()` |
| Use case | Flat structures | Nested/complex objects |

---

## 8. Explain `__slots__` and when to use them.

**Answer:**

`__slots__` restricts instance attributes and eliminates `__dict__`, saving memory.

```python
class WithDict:
    def __init__(self, x, y):
        self.x = x
        self.y = y

class WithSlots:
    __slots__ = ('x', 'y')
    def __init__(self, x, y):
        self.x = x
        self.y = y

import sys
a = WithDict(1, 2)
b = WithSlots(1, 2)
print(sys.getsizeof(a.__dict__))  # 104 bytes (dict overhead)
# b has no __dict__

# Cannot add arbitrary attributes
# b.z = 3  # AttributeError!
```

**When to use:** Classes with many instances (e.g., millions of data points in ML), when memory matters.

---

## 9. What are metaclasses?

**Answer:**

A metaclass is a class whose instances are classes. `type` is the default metaclass.

```python
# Classes are instances of type
print(type(int))    # <class 'type'>
print(type(str))    # <class 'type'>

# Custom metaclass
class SingletonMeta(type):
    _instances = {}
    
    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            cls._instances[cls] = super().__call__(*args, **kwargs)
        return cls._instances[cls]

class Database(metaclass=SingletonMeta):
    def __init__(self):
        self.connection = "connected"

db1 = Database()
db2 = Database()
print(db1 is db2)  # True

# Metaclass for automatic registration
class PluginMeta(type):
    registry = {}
    def __new__(mcs, name, bases, namespace):
        cls = super().__new__(mcs, name, bases, namespace)
        if bases:  # Don't register the base class
            PluginMeta.registry[name] = cls
        return cls

class Plugin(metaclass=PluginMeta):
    pass

class CSVPlugin(Plugin):
    pass

class JSONPlugin(Plugin):
    pass

print(PluginMeta.registry)  # {'CSVPlugin': <class...>, 'JSONPlugin': <class...>}
```

---

## 10. Explain the property decorator.

**Answer:**

`@property` creates managed attributes with getter/setter/deleter.

```python
class Temperature:
    def __init__(self, celsius=0):
        self._celsius = celsius
    
    @property
    def celsius(self):
        return self._celsius
    
    @celsius.setter
    def celsius(self, value):
        if value < -273.15:
            raise ValueError("Temperature below absolute zero!")
        self._celsius = value
    
    @property
    def fahrenheit(self):
        return self._celsius * 9/5 + 32
    
    @fahrenheit.setter
    def fahrenheit(self, value):
        self.celsius = (value - 32) * 5/9

t = Temperature(100)
print(t.fahrenheit)  # 212.0
t.fahrenheit = 32
print(t.celsius)     # 0.0
```

---

## 11. What are abstract base classes (ABCs)?

**Answer:**

```python
from abc import ABC, abstractmethod

class MLModel(ABC):
    @abstractmethod
    def fit(self, X, y):
        """Train the model."""
        pass
    
    @abstractmethod
    def predict(self, X):
        """Make predictions."""
        pass
    
    def score(self, X, y):
        """Default scoring method."""
        predictions = self.predict(X)
        return sum(p == a for p, a in zip(predictions, y)) / len(y)

class LinearRegression(MLModel):
    def fit(self, X, y):
        print("Training linear regression...")
    
    def predict(self, X):
        return [0] * len(X)

# model = MLModel()  # TypeError: Can't instantiate abstract class
model = LinearRegression()
model.fit([1, 2], [3, 4])  # Works
```

---

## 12. Explain the difference between composition and inheritance.

**Answer:**

```python
# Inheritance — "is-a" relationship
class Animal:
    def speak(self):
        pass

class Dog(Animal):  # Dog IS-A Animal
    def speak(self):
        return "Woof!"

# Composition — "has-a" relationship (PREFERRED)
class Engine:
    def start(self):
        return "Engine started"

class Car:
    def __init__(self):
        self.engine = Engine()  # Car HAS-A Engine
    
    def start(self):
        return self.engine.start()
```

| Aspect | Inheritance | Composition |
|--------|-----------|-------------|
| Relationship | "is-a" | "has-a" |
| Coupling | Tight | Loose |
| Flexibility | Less flexible | More flexible |
| Reusability | Hierarchy-bound | Mix and match |
| Python idiom | Use sparingly | **Preferred** |

> **"Favor composition over inheritance"** — Gang of Four

---

## 13. What is duck typing?

**Answer:**

"If it walks like a duck and quacks like a duck, it's a duck." — Objects are identified by behavior, not type.

```python
class Duck:
    def quack(self):
        return "Quack!"

class Person:
    def quack(self):
        return "I'm quacking like a duck!"

def make_it_quack(thing):
    # No type checking — just call the method
    return thing.quack()

print(make_it_quack(Duck()))    # "Quack!"
print(make_it_quack(Person()))  # "I'm quacking like a duck!"

# Real-world example: file-like objects
class StringBuffer:
    def __init__(self):
        self.data = []
    
    def write(self, text):
        self.data.append(text)
    
    def read(self):
        return ''.join(self.data)

# Works anywhere a "file-like" object is expected
buf = StringBuffer()
buf.write("Hello")
```

---

## 14. Explain Python data classes.

**Answer:**

```python
from dataclasses import dataclass, field
from typing import List

@dataclass
class Point:
    x: float
    y: float

@dataclass(frozen=True)  # Immutable
class FrozenPoint:
    x: float
    y: float

@dataclass(order=True)  # Enables comparison
class Student:
    sort_index: float = field(init=False, repr=False)
    name: str
    grade: float
    courses: List[str] = field(default_factory=list)
    
    def __post_init__(self):
        self.sort_index = self.grade

# Auto-generated __init__, __repr__, __eq__
p1 = Point(1.0, 2.0)
p2 = Point(1.0, 2.0)
print(p1 == p2)    # True (auto __eq__)
print(p1)          # Point(x=1.0, y=2.0) (auto __repr__)

s1 = Student("Alice", 95.0)
s2 = Student("Bob", 87.0)
print(s1 > s2)     # True (order=True)
```

---

## 15. What are mixins?

**Answer:**

Mixins are classes that provide methods to other classes through multiple inheritance, without being a standalone base.

```python
class JSONMixin:
    def to_json(self):
        import json
        return json.dumps(self.__dict__)

class LogMixin:
    def log(self, message):
        print(f"[{self.__class__.__name__}] {message}")

class TimestampMixin:
    def get_timestamp(self):
        from datetime import datetime
        return datetime.now().isoformat()

class User(JSONMixin, LogMixin, TimestampMixin):
    def __init__(self, name, email):
        self.name = name
        self.email = email

user = User("Alice", "alice@example.com")
print(user.to_json())       # {"name": "Alice", "email": "alice@example.com"}
user.log("User created")    # [User] User created
print(user.get_timestamp()) # 2024-01-15T10:30:00
```

---

## 16. Explain the `__call__` method.

**Answer:**

`__call__` makes instances callable like functions — useful for stateful functions and ML.

```python
class Multiplier:
    def __init__(self, factor):
        self.factor = factor
    
    def __call__(self, x):
        return x * self.factor

double = Multiplier(2)
print(double(5))   # 10
print(double(10))  # 20

# ML use case: Custom loss function
class WeightedLoss:
    def __init__(self, weights):
        self.weights = weights
    
    def __call__(self, predictions, targets):
        errors = [(p - t) ** 2 * w 
                  for p, t, w in zip(predictions, targets, self.weights)]
        return sum(errors) / len(errors)

loss_fn = WeightedLoss([1.0, 2.0, 1.0])
loss = loss_fn([1, 2, 3], [1.1, 2.2, 2.8])
```

---

## 17. What are context managers and the `__enter__`/`__exit__` protocol?

**Answer:**

```python
class DatabaseConnection:
    def __init__(self, db_name):
        self.db_name = db_name
        self.connection = None
    
    def __enter__(self):
        print(f"Connecting to {self.db_name}")
        self.connection = f"connection_to_{self.db_name}"
        return self
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        print(f"Closing connection to {self.db_name}")
        self.connection = None
        # Return True to suppress exceptions
        return False

with DatabaseConnection("mydb") as db:
    print(f"Using {db.connection}")
# Auto-closes after the block

# Using contextlib
from contextlib import contextmanager

@contextmanager
def timer():
    import time
    start = time.time()
    yield
    elapsed = time.time() - start
    print(f"Elapsed: {elapsed:.4f}s")

with timer():
    sum(range(1000000))
```

---

## 18. What is multiple inheritance and the diamond problem?

**Answer:**

```python
class A:
    def greet(self):
        return "Hello from A"

class B(A):
    def greet(self):
        return "Hello from B"

class C(A):
    def greet(self):
        return "Hello from C"

class D(B, C):  # Diamond inheritance
    pass

d = D()
print(d.greet())    # "Hello from B" — follows MRO
print(D.__mro__)    # D → B → C → A → object

# Cooperative inheritance with super()
class A:
    def greet(self):
        return ["A"]

class B(A):
    def greet(self):
        return ["B"] + super().greet()

class C(A):
    def greet(self):
        return ["C"] + super().greet()

class D(B, C):
    def greet(self):
        return ["D"] + super().greet()

print(D().greet())  # ['D', 'B', 'C', 'A']
```

---

## 19. Implement common design patterns in Python.

**Answer:**

```python
# Observer Pattern
class EventEmitter:
    def __init__(self):
        self._listeners = {}
    
    def on(self, event, callback):
        self._listeners.setdefault(event, []).append(callback)
    
    def emit(self, event, *args):
        for callback in self._listeners.get(event, []):
            callback(*args)

# Factory Pattern
class ModelFactory:
    _models = {}
    
    @classmethod
    def register(cls, name):
        def decorator(model_cls):
            cls._models[name] = model_cls
            return model_cls
        return decorator
    
    @classmethod
    def create(cls, name, **kwargs):
        if name not in cls._models:
            raise ValueError(f"Unknown model: {name}")
        return cls._models[name](**kwargs)

@ModelFactory.register("linear")
class LinearModel:
    def __init__(self, **kwargs):
        self.params = kwargs

@ModelFactory.register("tree")
class TreeModel:
    def __init__(self, **kwargs):
        self.params = kwargs

model = ModelFactory.create("linear", lr=0.01)

# Strategy Pattern
class Sorter:
    def __init__(self, strategy=None):
        self.strategy = strategy or sorted
    
    def sort(self, data):
        return self.strategy(data)

sorter = Sorter(strategy=lambda x: sorted(x, reverse=True))
print(sorter.sort([3, 1, 4, 1, 5]))  # [5, 4, 3, 1, 1]
```

---

## 20. How do you make a class iterable?

**Answer:**

```python
class Range:
    def __init__(self, start, end):
        self.start = start
        self.end = end
    
    def __iter__(self):
        self.current = self.start
        return self
    
    def __next__(self):
        if self.current >= self.end:
            raise StopIteration
        value = self.current
        self.current += 1
        return value

for i in Range(1, 5):
    print(i)  # 1, 2, 3, 4

# Using __getitem__ (older protocol)
class Indexable:
    def __init__(self, data):
        self.data = data
    
    def __getitem__(self, index):
        return self.data[index]
    
    def __len__(self):
        return len(self.data)

items = Indexable([10, 20, 30])
for item in items:
    print(item)  # 10, 20, 30
```

---

## 21. What are dunder (magic) methods?

**Answer:**

```python
class Vector:
    def __init__(self, x, y):
        self.x, self.y = x, y
    
    # String representations
    def __repr__(self):
        return f"Vector({self.x}, {self.y})"
    
    def __str__(self):
        return f"({self.x}, {self.y})"
    
    # Arithmetic
    def __add__(self, other):
        return Vector(self.x + other.x, self.y + other.y)
    
    def __mul__(self, scalar):
        return Vector(self.x * scalar, self.y * scalar)
    
    def __rmul__(self, scalar):
        return self.__mul__(scalar)
    
    # Comparison
    def __eq__(self, other):
        return self.x == other.x and self.y == other.y
    
    def __lt__(self, other):
        return abs(self) < abs(other)
    
    # Absolute value (magnitude)
    def __abs__(self):
        return (self.x ** 2 + self.y ** 2) ** 0.5
    
    # Boolean
    def __bool__(self):
        return abs(self) > 0
    
    # Hashing (required for use in sets/dicts)
    def __hash__(self):
        return hash((self.x, self.y))

v1 = Vector(3, 4)
v2 = Vector(1, 2)
print(v1 + v2)      # (4, 6)
print(3 * v1)       # (9, 12)
print(abs(v1))      # 5.0
```

---

## 22. Explain `__getattr__`, `__getattribute__`, and `__setattr__`.

**Answer:**

```python
class SmartDict:
    def __init__(self, data=None):
        # Must use __dict__ directly to avoid recursion
        self.__dict__['_data'] = data or {}
    
    def __getattr__(self, name):
        """Called when normal attribute lookup fails."""
        if name in self._data:
            return self._data[name]
        raise AttributeError(f"No attribute '{name}'")
    
    def __setattr__(self, name, value):
        """Called on every attribute assignment."""
        if name.startswith('_'):
            super().__setattr__(name, value)
        else:
            self._data[name] = value

d = SmartDict({"x": 1, "y": 2})
print(d.x)    # 1 (via __getattr__)
d.z = 3       # Stored in _data (via __setattr__)
```

---

## 23. What is method chaining? How do you implement it?

**Answer:**

```python
class QueryBuilder:
    def __init__(self):
        self._table = None
        self._conditions = []
        self._limit = None
        self._order = None
    
    def select(self, table):
        self._table = table
        return self  # Return self for chaining
    
    def where(self, condition):
        self._conditions.append(condition)
        return self
    
    def order_by(self, column):
        self._order = column
        return self
    
    def limit(self, n):
        self._limit = n
        return self
    
    def build(self):
        query = f"SELECT * FROM {self._table}"
        if self._conditions:
            query += " WHERE " + " AND ".join(self._conditions)
        if self._order:
            query += f" ORDER BY {self._order}"
        if self._limit:
            query += f" LIMIT {self._limit}"
        return query

query = (QueryBuilder()
    .select("users")
    .where("age > 18")
    .where("active = true")
    .order_by("name")
    .limit(10)
    .build())
print(query)
# SELECT * FROM users WHERE age > 18 AND active = true ORDER BY name LIMIT 10
```

---

## 24. What is the Liskov Substitution Principle in Python?

**Answer:**

Subtypes must be substitutable for their base types without breaking behavior.

```python
# VIOLATION — Rectangle/Square problem
class Rectangle:
    def __init__(self, w, h):
        self._w, self._h = w, h
    
    @property
    def width(self):
        return self._w
    
    @width.setter
    def width(self, value):
        self._w = value
    
    @property
    def height(self):
        return self._h
    
    @height.setter
    def height(self, value):
        self._h = value
    
    def area(self):
        return self._w * self._h

class Square(Rectangle):  # Violates LSP!
    @Rectangle.width.setter
    def width(self, value):
        self._w = self._h = value
    
    @Rectangle.height.setter
    def height(self, value):
        self._w = self._h = value

# CORRECT — use composition or separate hierarchy
class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

class Rectangle(Shape):
    def __init__(self, w, h): self._w, self._h = w, h
    def area(self): return self._w * self._h

class Square(Shape):
    def __init__(self, side): self._side = side
    def area(self): return self._side ** 2
```

---

## 25. Explain `__contains__`, `__len__`, and `__getitem__`.

**Answer:**

```python
class Dataset:
    def __init__(self, data):
        self._data = data
    
    def __len__(self):
        """len(dataset)"""
        return len(self._data)
    
    def __getitem__(self, index):
        """dataset[0], dataset[1:3], enables iteration"""
        if isinstance(index, slice):
            return Dataset(self._data[index])
        return self._data[index]
    
    def __contains__(self, item):
        """item in dataset"""
        return item in self._data
    
    def __repr__(self):
        return f"Dataset({self._data})"

ds = Dataset([10, 20, 30, 40, 50])
print(len(ds))       # 5
print(ds[2])         # 30
print(ds[1:3])       # Dataset([20, 30])
print(30 in ds)      # True
for item in ds:      # Iteration via __getitem__
    print(item)
```

---

## 26. What are class decorators?

**Answer:**

```python
def add_repr(cls):
    """Class decorator that adds __repr__."""
    def __repr__(self):
        attrs = ', '.join(f'{k}={v!r}' for k, v in self.__dict__.items())
        return f'{cls.__name__}({attrs})'
    cls.__repr__ = __repr__
    return cls

@add_repr
class User:
    def __init__(self, name, age):
        self.name = name
        self.age = age

print(User("Alice", 30))  # User(name='Alice', age=30)

# Decorator with arguments
def retry(max_attempts=3):
    def decorator(cls):
        original_init = cls.__init__
        def new_init(self, *args, **kwargs):
            for attempt in range(max_attempts):
                try:
                    original_init(self, *args, **kwargs)
                    return
                except Exception as e:
                    if attempt == max_attempts - 1:
                        raise
        cls.__init__ = new_init
        return cls
    return decorator
```

---

## 27. How does garbage collection work with circular references?

**Answer:**

```python
import gc

class Node:
    def __init__(self, name):
        self.name = name
        self.ref = None

# Circular reference
a = Node("A")
b = Node("B")
a.ref = b
b.ref = a

# Reference counting alone can't collect these
del a
del b
# Objects still in memory — reference count is 1 (from each other)

# Python's cyclic GC handles this
gc.collect()  # Forces garbage collection

# Weak references avoid circular reference issues
import weakref

class Cache:
    def __init__(self):
        self._cache = weakref.WeakValueDictionary()
    
    def get(self, key):
        return self._cache.get(key)
    
    def set(self, key, value):
        self._cache[key] = value
```

---

## 28. What is the difference between `is` and `==`?

**Answer:**

```python
# == compares values (calls __eq__)
# is compares identity (same object in memory)

a = [1, 2, 3]
b = [1, 2, 3]
print(a == b)   # True — same values
print(a is b)   # False — different objects

# Small integer caching (-5 to 256)
x = 256
y = 256
print(x is y)   # True — cached

x = 257
y = 257
print(x is y)   # False — not cached (may vary by implementation)

# String interning
s1 = "hello"
s2 = "hello"
print(s1 is s2)  # True — interned

# Always use 'is' for None
value = None
if value is None:    # Correct
    pass
if value == None:    # Works but not Pythonic
    pass
```

---

## 29. What are Protocols (structural subtyping)?

**Answer:**

```python
from typing import Protocol, runtime_checkable

@runtime_checkable
class Drawable(Protocol):
    def draw(self) -> str:
        ...

class Circle:
    def draw(self) -> str:
        return "Drawing circle"

class Square:
    def draw(self) -> str:
        return "Drawing square"

class NotDrawable:
    pass

def render(shape: Drawable):
    return shape.draw()

# No inheritance needed — just implement the method
print(render(Circle()))    # Works!
print(isinstance(Circle(), Drawable))  # True (runtime_checkable)
print(isinstance(NotDrawable(), Drawable))  # False
```

Protocols enable duck typing with type checker support (PEP 544).

---

## 30. Implement the Builder pattern.

**Answer:**

```python
class NeuralNetworkBuilder:
    def __init__(self):
        self._layers = []
        self._optimizer = "adam"
        self._loss = "mse"
        self._learning_rate = 0.001
    
    def add_layer(self, units, activation="relu"):
        self._layers.append({"units": units, "activation": activation})
        return self
    
    def optimizer(self, name):
        self._optimizer = name
        return self
    
    def loss(self, name):
        self._loss = name
        return self
    
    def learning_rate(self, lr):
        self._learning_rate = lr
        return self
    
    def build(self):
        return {
            "layers": self._layers,
            "optimizer": self._optimizer,
            "loss": self._loss,
            "learning_rate": self._learning_rate
        }

model = (NeuralNetworkBuilder()
    .add_layer(128, "relu")
    .add_layer(64, "relu")
    .add_layer(10, "softmax")
    .optimizer("adam")
    .loss("categorical_crossentropy")
    .learning_rate(0.001)
    .build())
```

---

## 31. What is the `__init_subclass__` hook?

**Answer:**

```python
class PluginBase:
    _plugins = {}
    
    def __init_subclass__(cls, plugin_name=None, **kwargs):
        super().__init_subclass__(**kwargs)
        name = plugin_name or cls.__name__.lower()
        PluginBase._plugins[name] = cls
    
    @classmethod
    def get_plugin(cls, name):
        return cls._plugins[name]

class CSVLoader(PluginBase, plugin_name="csv"):
    def load(self, path):
        return f"Loading CSV from {path}"

class JSONLoader(PluginBase, plugin_name="json"):
    def load(self, path):
        return f"Loading JSON from {path}"

loader = PluginBase.get_plugin("csv")()
print(loader.load("data.csv"))
print(PluginBase._plugins)  # {'csv': <class 'CSVLoader'>, 'json': <class 'JSONLoader'>}
```

---

## 32. Explain `__class_getitem__` and generic classes.

**Answer:**

```python
from typing import TypeVar, Generic

T = TypeVar('T')

class TypedList(Generic[T]):
    def __init__(self):
        self._items: list[T] = []
    
    def add(self, item: T):
        self._items.append(item)
    
    def get(self, index: int) -> T:
        return self._items[index]

# Usage with type hints
int_list: TypedList[int] = TypedList()
int_list.add(42)

# Custom __class_getitem__
class Matrix:
    def __class_getitem__(cls, params):
        rows, cols = params
        return type(f'Matrix{rows}x{cols}', (cls,), {'rows': rows, 'cols': cols})

M = Matrix[3, 3]
print(M.rows, M.cols)  # 3 3
```

---

## 33. What is the SOLID principle applied to Python?

**Answer:**

| Principle | Meaning | Python Example |
|-----------|---------|----------------|
| **S**ingle Responsibility | One class, one purpose | Separate `DataLoader` from `DataProcessor` |
| **O**pen/Closed | Open for extension, closed for modification | Use ABC + inheritance |
| **L**iskov Substitution | Subtypes must be substitutable | Protocol-based design |
| **I**nterface Segregation | No forced method implementation | Multiple small ABCs/Protocols |
| **D**ependency Inversion | Depend on abstractions | Inject dependencies via `__init__` |

```python
# Dependency Inversion
class Storage(Protocol):
    def save(self, data: dict) -> None: ...

class FileStorage:
    def save(self, data: dict):
        print(f"Saving to file: {data}")

class S3Storage:
    def save(self, data: dict):
        print(f"Saving to S3: {data}")

class DataPipeline:
    def __init__(self, storage: Storage):  # Depends on abstraction
        self.storage = storage
    
    def process_and_save(self, data):
        processed = {k: v.upper() for k, v in data.items()}
        self.storage.save(processed)

pipeline = DataPipeline(FileStorage())  # Or S3Storage()
```

---

## 34. How do you implement `__eq__` and `__hash__` correctly?

**Answer:**

```python
class Employee:
    def __init__(self, emp_id, name, department):
        self.emp_id = emp_id
        self.name = name
        self.department = department
    
    def __eq__(self, other):
        if not isinstance(other, Employee):
            return NotImplemented
        return self.emp_id == other.emp_id
    
    def __hash__(self):
        # Must be consistent with __eq__
        # Only hash immutable, equality-defining attributes
        return hash(self.emp_id)

e1 = Employee(1, "Alice", "Eng")
e2 = Employee(1, "Alice", "HR")
print(e1 == e2)              # True (same emp_id)
print({e1, e2})              # Only one in set
print({e1: "senior"})        # Can use as dict key
```

**Rules:**
- If `a == b`, then `hash(a) == hash(b)` (MUST)
- If `hash(a) == hash(b)`, `a == b` is NOT guaranteed (collisions OK)
- If you define `__eq__`, you MUST define `__hash__` (or set `__hash__ = None`)

---

## 35. What is monkey patching?

**Answer:**

Dynamically modifying classes or modules at runtime.

```python
# Monkey patching a class
class Calculator:
    def add(self, a, b):
        return a + b

def multiply(self, a, b):
    return a * b

Calculator.multiply = multiply  # Add method at runtime

calc = Calculator()
print(calc.multiply(3, 4))  # 12

# Monkey patching for testing
import some_module

def mock_api_call():
    return {"status": "mocked"}

# In tests
original = some_module.api_call
some_module.api_call = mock_api_call
# ... run tests ...
some_module.api_call = original  # Restore

# Better: use unittest.mock
from unittest.mock import patch

@patch('some_module.api_call', return_value={"status": "mocked"})
def test_something(mock_call):
    result = some_module.api_call()
    assert result["status"] == "mocked"
```

**Warning:** Monkey patching is generally discouraged in production — use dependency injection instead.
