# Python Coding Challenges — Interview Questions & Answers

## 1. Reverse a string without using built-in reverse.

```python
def reverse_string(s: str) -> str:
    return s[::-1]

# Without slicing
def reverse_string_manual(s: str) -> str:
    chars = list(s)
    left, right = 0, len(chars) - 1
    while left < right:
        chars[left], chars[right] = chars[right], chars[left]
        left += 1
        right -= 1
    return ''.join(chars)

print(reverse_string("machine learning"))  # "gninrael enihcam"
```

---

## 2. Check if a string is a palindrome.

```python
def is_palindrome(s: str) -> bool:
    cleaned = ''.join(c.lower() for c in s if c.isalnum())
    return cleaned == cleaned[::-1]

print(is_palindrome("A man, a plan, a canal: Panama"))  # True
```

---

## 3. Find the first non-repeating character in a string.

```python
from collections import Counter

def first_unique_char(s: str) -> str:
    counts = Counter(s)
    for char in s:
        if counts[char] == 1:
            return char
    return ""

print(first_unique_char("aabbcdd"))  # "c"
```

---

## 4. Flatten a nested list of arbitrary depth.

```python
def flatten(lst):
    result = []
    for item in lst:
        if isinstance(item, (list, tuple)):
            result.extend(flatten(item))
        else:
            result.append(item)
    return result

# Using generator
def flatten_gen(lst):
    for item in lst:
        if isinstance(item, (list, tuple)):
            yield from flatten_gen(item)
        else:
            yield item

print(flatten([1, [2, [3, 4], 5], [6, 7]]))  # [1, 2, 3, 4, 5, 6, 7]
```

---

## 5. Implement a LRU Cache from scratch.

```python
from collections import OrderedDict

class LRUCache:
    def __init__(self, capacity: int):
        self.cache = OrderedDict()
        self.capacity = capacity

    def get(self, key):
        if key not in self.cache:
            return -1
        self.cache.move_to_end(key)
        return self.cache[key]

    def put(self, key, value):
        if key in self.cache:
            self.cache.move_to_end(key)
        self.cache[key] = value
        if len(self.cache) > self.capacity:
            self.cache.popitem(last=False)

cache = LRUCache(2)
cache.put(1, "a")
cache.put(2, "b")
print(cache.get(1))    # "a"
cache.put(3, "c")      # Evicts key 2
print(cache.get(2))    # -1
```

---

## 6. Find two numbers in a list that add up to a target.

```python
def two_sum(nums: list, target: int) -> tuple:
    seen = {}
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return (seen[complement], i)
        seen[num] = i
    return ()

print(two_sum([2, 7, 11, 15], 9))  # (0, 1)
```

---

## 7. Implement a decorator that caches results (memoization).

```python
def memoize(func):
    cache = {}
    def wrapper(*args):
        if args not in cache:
            cache[args] = func(*args)
        return cache[args]
    wrapper.cache = cache
    return wrapper

@memoize
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)

print(fibonacci(50))  # 12586269025 — instant
print(fibonacci.cache)  # Shows all cached values
```

---

## 8. Find the most frequent element in a list.

```python
from collections import Counter

def most_frequent(lst):
    if not lst:
        return None
    counter = Counter(lst)
    return counter.most_common(1)[0][0]

# Without Counter
def most_frequent_manual(lst):
    freq = {}
    max_count, result = 0, None
    for item in lst:
        freq[item] = freq.get(item, 0) + 1
        if freq[item] > max_count:
            max_count = freq[item]
            result = item
    return result

print(most_frequent([1, 3, 2, 1, 4, 1, 3]))  # 1
```

---

## 9. Merge two sorted lists into one sorted list.

```python
def merge_sorted(a: list, b: list) -> list:
    result = []
    i = j = 0
    while i < len(a) and j < len(b):
        if a[i] <= b[j]:
            result.append(a[i])
            i += 1
        else:
            result.append(b[j])
            j += 1
    result.extend(a[i:])
    result.extend(b[j:])
    return result

print(merge_sorted([1, 3, 5], [2, 4, 6]))  # [1, 2, 3, 4, 5, 6]
```

---

## 10. Implement a generator that reads a large file in chunks.

```python
def read_chunks(filepath, chunk_size=1024):
    """Memory-efficient file reader for large datasets."""
    with open(filepath, 'r') as f:
        while True:
            chunk = f.read(chunk_size)
            if not chunk:
                break
            yield chunk

def count_lines_large_file(filepath):
    """Count lines without loading entire file."""
    return sum(chunk.count('\n') for chunk in read_chunks(filepath))
```

---

## 11. Remove duplicates from a list while preserving order.

```python
def remove_duplicates(lst):
    seen = set()
    result = []
    for item in lst:
        if item not in seen:
            seen.add(item)
            result.append(item)
    return result

# Using dict.fromkeys (Python 3.7+ preserves insertion order)
def remove_duplicates_v2(lst):
    return list(dict.fromkeys(lst))

print(remove_duplicates([3, 1, 4, 1, 5, 9, 2, 6, 5]))  # [3, 1, 4, 5, 9, 2, 6]
```

---

## 12. Implement binary search.

```python
def binary_search(arr: list, target) -> int:
    left, right = 0, len(arr) - 1
    while left <= right:
        mid = (left + right) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1

print(binary_search([1, 3, 5, 7, 9, 11], 7))  # 3
```

---

## 13. Group anagrams together.

```python
from collections import defaultdict

def group_anagrams(words: list) -> list:
    groups = defaultdict(list)
    for word in words:
        key = ''.join(sorted(word))
        groups[key].append(word)
    return list(groups.values())

print(group_anagrams(["eat", "tea", "tan", "ate", "nat", "bat"]))
# [['eat', 'tea', 'ate'], ['tan', 'nat'], ['bat']]
```

---

## 14. Implement a stack using two queues.

```python
from collections import deque

class Stack:
    def __init__(self):
        self.q1 = deque()
        self.q2 = deque()

    def push(self, val):
        self.q2.append(val)
        while self.q1:
            self.q2.append(self.q1.popleft())
        self.q1, self.q2 = self.q2, self.q1

    def pop(self):
        return self.q1.popleft()

    def top(self):
        return self.q1[0]

    def is_empty(self):
        return len(self.q1) == 0

s = Stack()
s.push(1); s.push(2); s.push(3)
print(s.pop())  # 3
print(s.top())  # 2
```

---

## 15. Find the longest common prefix in a list of strings.

```python
def longest_common_prefix(strs: list) -> str:
    if not strs:
        return ""
    prefix = strs[0]
    for s in strs[1:]:
        while not s.startswith(prefix):
            prefix = prefix[:-1]
            if not prefix:
                return ""
    return prefix

print(longest_common_prefix(["flower", "flow", "flight"]))  # "fl"
```

---

## 16. Implement matrix multiplication without NumPy.

```python
def matrix_multiply(A, B):
    rows_A, cols_A = len(A), len(A[0])
    rows_B, cols_B = len(B), len(B[0])
    assert cols_A == rows_B, "Incompatible dimensions"

    result = [[0] * cols_B for _ in range(rows_A)]
    for i in range(rows_A):
        for j in range(cols_B):
            for k in range(cols_A):
                result[i][j] += A[i][k] * B[k][j]
    return result

A = [[1, 2], [3, 4]]
B = [[5, 6], [7, 8]]
print(matrix_multiply(A, B))  # [[19, 22], [43, 50]]
```

---

## 17. Write a context manager for timing code blocks.

```python
import time
from contextlib import contextmanager

@contextmanager
def timer(label="Block"):
    start = time.perf_counter()
    yield
    elapsed = time.perf_counter() - start
    print(f"{label}: {elapsed:.4f}s")

with timer("Data processing"):
    data = [x ** 2 for x in range(1000000)]
```

---

## 18. Implement a thread-safe singleton pattern.

```python
import threading

class Singleton:
    _instance = None
    _lock = threading.Lock()

    def __new__(cls, *args, **kwargs):
        if cls._instance is None:
            with cls._lock:
                if cls._instance is None:  # Double-checked locking
                    cls._instance = super().__new__(cls)
        return cls._instance

    def __init__(self, value=None):
        if not hasattr(self, '_initialized'):
            self.value = value
            self._initialized = True

a = Singleton("first")
b = Singleton("second")
print(a is b)       # True
print(a.value)      # "first"
```

---

## 19. Find all permutations of a string.

```python
def permutations(s: str) -> list:
    if len(s) <= 1:
        return [s]
    result = []
    for i, char in enumerate(s):
        remaining = s[:i] + s[i+1:]
        for perm in permutations(remaining):
            result.append(char + perm)
    return result

print(permutations("abc"))
# ['abc', 'acb', 'bac', 'bca', 'cab', 'cba']
```

---

## 20. Implement a producer-consumer pattern.

```python
import threading
import queue
import time
import random

def producer(q: queue.Queue, n_items: int):
    for i in range(n_items):
        item = f"item_{i}"
        q.put(item)
        print(f"Produced: {item}")
        time.sleep(random.uniform(0.01, 0.05))
    q.put(None)  # Sentinel

def consumer(q: queue.Queue):
    while True:
        item = q.get()
        if item is None:
            break
        print(f"Consumed: {item}")
        q.task_done()

q = queue.Queue(maxsize=5)
t1 = threading.Thread(target=producer, args=(q, 10))
t2 = threading.Thread(target=consumer, args=(q,))
t1.start(); t2.start()
t1.join(); t2.join()
```

---

## 21. Implement a trie (prefix tree).

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word: str):
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_end = True

    def search(self, word: str) -> bool:
        node = self._find(word)
        return node is not None and node.is_end

    def starts_with(self, prefix: str) -> bool:
        return self._find(prefix) is not None

    def _find(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                return None
            node = node.children[char]
        return node

trie = Trie()
trie.insert("apple")
print(trie.search("apple"))      # True
print(trie.search("app"))        # False
print(trie.starts_with("app"))   # True
```

---

## 22. Implement `map`, `filter`, `reduce` from scratch.

```python
def my_map(func, iterable):
    for item in iterable:
        yield func(item)

def my_filter(func, iterable):
    for item in iterable:
        if func(item):
            yield item

def my_reduce(func, iterable, initial=None):
    it = iter(iterable)
    if initial is None:
        accumulator = next(it)
    else:
        accumulator = initial
    for item in it:
        accumulator = func(accumulator, item)
    return accumulator

print(list(my_map(lambda x: x**2, [1, 2, 3])))        # [1, 4, 9]
print(list(my_filter(lambda x: x > 2, [1, 2, 3, 4]))) # [3, 4]
print(my_reduce(lambda a, b: a + b, [1, 2, 3, 4]))     # 10
```

---

## 23. Write a function to check balanced parentheses.

```python
def is_balanced(s: str) -> bool:
    stack = []
    pairs = {')': '(', '}': '{', ']': '['}
    for char in s:
        if char in '({[':
            stack.append(char)
        elif char in ')}]':
            if not stack or stack[-1] != pairs[char]:
                return False
            stack.pop()
    return len(stack) == 0

print(is_balanced("({[]})"))   # True
print(is_balanced("({[})"))    # False
```

---

## 24. Find the intersection of two lists.

```python
def intersection(a: list, b: list) -> list:
    return list(set(a) & set(b))

# Preserving order and duplicates
def intersection_ordered(a: list, b: list) -> list:
    from collections import Counter
    count_b = Counter(b)
    result = []
    for item in a:
        if count_b[item] > 0:
            result.append(item)
            count_b[item] -= 1
    return result

print(intersection([1, 2, 2, 3], [2, 2, 4]))          # [2]
print(intersection_ordered([1, 2, 2, 3], [2, 2, 4]))  # [2, 2]
```

---

## 25. Implement a simple data pipeline using generators.

```python
def read_data(source):
    """Stage 1: Read raw data."""
    for item in source:
        yield item

def clean_data(stream):
    """Stage 2: Clean and validate."""
    for item in stream:
        cleaned = item.strip().lower()
        if cleaned:
            yield cleaned

def transform_data(stream):
    """Stage 3: Transform data."""
    for item in stream:
        yield {"text": item, "length": len(item), "words": len(item.split())}

def batch_data(stream, batch_size=3):
    """Stage 4: Create batches."""
    batch = []
    for item in stream:
        batch.append(item)
        if len(batch) == batch_size:
            yield batch
            batch = []
    if batch:
        yield batch

# Compose the pipeline — memory efficient for any size
raw = ["  Hello World  ", "  Machine Learning  ", "", "  AI  ", "  Deep Learning  ", "  NLP  "]
pipeline = batch_data(transform_data(clean_data(read_data(raw))), batch_size=2)

for batch in pipeline:
    print(f"Batch ({len(batch)} items): {[b['text'] for b in batch]}")
```

---

## 26. Implement a simple rate limiter.

```python
import time
from collections import deque

class RateLimiter:
    def __init__(self, max_calls: int, period: float):
        self.max_calls = max_calls
        self.period = period
        self.calls = deque()

    def allow(self) -> bool:
        now = time.time()
        # Remove expired timestamps
        while self.calls and now - self.calls[0] > self.period:
            self.calls.popleft()

        if len(self.calls) < self.max_calls:
            self.calls.append(now)
            return True
        return False

limiter = RateLimiter(max_calls=3, period=1.0)
for i in range(5):
    print(f"Request {i}: {'Allowed' if limiter.allow() else 'Denied'}")
```

---

## 27. Detect a cycle in a linked list.

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

def has_cycle(head: ListNode) -> bool:
    """Floyd's Tortoise and Hare algorithm."""
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow is fast:
            return True
    return False
```

---

## 28. Implement `defaultdict` from scratch.

```python
class MyDefaultDict(dict):
    def __init__(self, default_factory=None, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.default_factory = default_factory

    def __missing__(self, key):
        if self.default_factory is None:
            raise KeyError(key)
        value = self.default_factory()
        self[key] = value
        return value

d = MyDefaultDict(list)
d["fruits"].append("apple")
d["fruits"].append("banana")
print(d)  # {'fruits': ['apple', 'banana']}
```

---

## 29. Write an async web scraper.

```python
import asyncio
import aiohttp

async def fetch_url(session, url):
    try:
        async with session.get(url, timeout=aiohttp.ClientTimeout(total=10)) as resp:
            return {"url": url, "status": resp.status, "size": len(await resp.text())}
    except Exception as e:
        return {"url": url, "error": str(e)}

async def scrape_urls(urls, max_concurrent=5):
    semaphore = asyncio.Semaphore(max_concurrent)

    async def limited_fetch(session, url):
        async with semaphore:
            return await fetch_url(session, url)

    async with aiohttp.ClientSession() as session:
        tasks = [limited_fetch(session, url) for url in urls]
        return await asyncio.gather(*tasks)

# asyncio.run(scrape_urls(["https://example.com", "https://python.org"]))
```

---

## 30. Implement a simple event system (Observer pattern).

```python
from collections import defaultdict
from typing import Callable

class EventEmitter:
    def __init__(self):
        self._listeners = defaultdict(list)

    def on(self, event: str, callback: Callable):
        self._listeners[event].append(callback)

    def off(self, event: str, callback: Callable):
        self._listeners[event].remove(callback)

    def emit(self, event: str, *args, **kwargs):
        for callback in self._listeners[event]:
            callback(*args, **kwargs)

# Usage — ML training events
emitter = EventEmitter()

def on_epoch_end(epoch, loss):
    print(f"Epoch {epoch}: loss={loss:.4f}")

def on_training_complete(metrics):
    print(f"Training done! Final metrics: {metrics}")

emitter.on("epoch_end", on_epoch_end)
emitter.on("training_complete", on_training_complete)

# Simulate training
for epoch in range(3):
    loss = 1.0 / (epoch + 1)
    emitter.emit("epoch_end", epoch, loss)
emitter.emit("training_complete", {"accuracy": 0.95})
```

---

*Total: 30 Coding Challenges covering data structures, algorithms, design patterns, concurrency, and practical Python patterns commonly asked in AI/ML interviews.*
