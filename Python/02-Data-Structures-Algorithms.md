# Data Structures & Algorithms in Python — Interview Questions & Answers

## 1. What are the time complexities of Python's built-in data structures?

**Answer:**

| Operation | List | Dict | Set | Deque |
|-----------|------|------|-----|-------|
| Access by index | O(1) | — | — | O(n) |
| Search | O(n) | O(1) avg | O(1) avg | O(n) |
| Insert at end | O(1)* | O(1) avg | O(1) avg | O(1) |
| Insert at beginning | O(n) | — | — | O(1) |
| Delete | O(n) | O(1) avg | O(1) avg | O(1) ends |
| Sort | O(n log n) | — | — | — |

*amortized

---

## 2. Implement a stack using Python.

**Answer:**

```python
# Using list
class Stack:
    def __init__(self):
        self._items = []
    
    def push(self, item):
        self._items.append(item)  # O(1)
    
    def pop(self):
        if self.is_empty():
            raise IndexError("Stack is empty")
        return self._items.pop()  # O(1)
    
    def peek(self):
        if self.is_empty():
            raise IndexError("Stack is empty")
        return self._items[-1]
    
    def is_empty(self):
        return len(self._items) == 0
    
    def __len__(self):
        return len(self._items)

# Using collections.deque (preferred for thread-safety)
from collections import deque
stack = deque()
stack.append(1)    # push
stack.pop()        # pop
```

---

## 3. Implement a queue using Python.

**Answer:**

```python
from collections import deque

class Queue:
    def __init__(self):
        self._items = deque()
    
    def enqueue(self, item):
        self._items.append(item)      # O(1)
    
    def dequeue(self):
        if self.is_empty():
            raise IndexError("Queue is empty")
        return self._items.popleft()   # O(1)
    
    def front(self):
        return self._items[0]
    
    def is_empty(self):
        return len(self._items) == 0

# Priority Queue (used in ML for beam search, A*, etc.)
import heapq

class PriorityQueue:
    def __init__(self):
        self._heap = []
    
    def push(self, priority, item):
        heapq.heappush(self._heap, (priority, item))
    
    def pop(self):
        return heapq.heappop(self._heap)
```

---

## 4. How do you implement a linked list in Python?

**Answer:**

```python
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None

class LinkedList:
    def __init__(self):
        self.head = None
    
    def append(self, data):
        new_node = Node(data)
        if not self.head:
            self.head = new_node
            return
        current = self.head
        while current.next:
            current = current.next
        current.next = new_node
    
    def prepend(self, data):
        new_node = Node(data)
        new_node.next = self.head
        self.head = new_node
    
    def delete(self, data):
        if not self.head:
            return
        if self.head.data == data:
            self.head = self.head.next
            return
        current = self.head
        while current.next and current.next.data != data:
            current = current.next
        if current.next:
            current.next = current.next.next
    
    def search(self, data):
        current = self.head
        while current:
            if current.data == data:
                return True
            current = current.next
        return False
    
    def reverse(self):
        prev = None
        current = self.head
        while current:
            next_node = current.next
            current.next = prev
            prev = current
            current = next_node
        self.head = prev
    
    def __iter__(self):
        current = self.head
        while current:
            yield current.data
            current = current.next
```

---

## 5. Implement binary search in Python.

**Answer:**

```python
def binary_search(arr, target):
    """Iterative binary search - O(log n)"""
    left, right = 0, len(arr) - 1
    while left <= right:
        mid = left + (right - left) // 2  # Avoid overflow
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1

def binary_search_recursive(arr, target, left=0, right=None):
    """Recursive binary search"""
    if right is None:
        right = len(arr) - 1
    if left > right:
        return -1
    mid = (left + right) // 2
    if arr[mid] == target:
        return mid
    elif arr[mid] < target:
        return binary_search_recursive(arr, target, mid + 1, right)
    else:
        return binary_search_recursive(arr, target, left, mid - 1)

# Using bisect module
import bisect
arr = [1, 3, 5, 7, 9]
idx = bisect.bisect_left(arr, 5)  # 2
bisect.insort(arr, 6)             # [1, 3, 5, 6, 7, 9]
```

---

## 6. Implement common sorting algorithms.

**Answer:**

```python
# Merge Sort - O(n log n) — stable
def merge_sort(arr):
    if len(arr) <= 1:
        return arr
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    return merge(left, right)

def merge(left, right):
    result = []
    i = j = 0
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    result.extend(left[i:])
    result.extend(right[j:])
    return result

# Quick Sort - O(n log n) average — in-place
def quick_sort(arr, low=0, high=None):
    if high is None:
        high = len(arr) - 1
    if low < high:
        pi = partition(arr, low, high)
        quick_sort(arr, low, pi - 1)
        quick_sort(arr, pi + 1, high)

def partition(arr, low, high):
    pivot = arr[high]
    i = low - 1
    for j in range(low, high):
        if arr[j] <= pivot:
            i += 1
            arr[i], arr[j] = arr[j], arr[i]
    arr[i + 1], arr[high] = arr[high], arr[i + 1]
    return i + 1
```

---

## 7. How do you implement a hash map from scratch?

**Answer:**

```python
class HashMap:
    def __init__(self, size=1000):
        self.size = size
        self.buckets = [[] for _ in range(size)]
    
    def _hash(self, key):
        return hash(key) % self.size
    
    def put(self, key, value):
        idx = self._hash(key)
        for i, (k, v) in enumerate(self.buckets[idx]):
            if k == key:
                self.buckets[idx][i] = (key, value)
                return
        self.buckets[idx].append((key, value))
    
    def get(self, key, default=None):
        idx = self._hash(key)
        for k, v in self.buckets[idx]:
            if k == key:
                return v
        return default
    
    def delete(self, key):
        idx = self._hash(key)
        for i, (k, v) in enumerate(self.buckets[idx]):
            if k == key:
                del self.buckets[idx][i]
                return True
        return False
```

---

## 8. Implement a binary tree with traversals.

**Answer:**

```python
class TreeNode:
    def __init__(self, val):
        self.val = val
        self.left = None
        self.right = None

class BinaryTree:
    def __init__(self):
        self.root = None
    
    def inorder(self, node):
        """Left → Root → Right"""
        if node:
            yield from self.inorder(node.left)
            yield node.val
            yield from self.inorder(node.right)
    
    def preorder(self, node):
        """Root → Left → Right"""
        if node:
            yield node.val
            yield from self.preorder(node.left)
            yield from self.preorder(node.right)
    
    def postorder(self, node):
        """Left → Right → Root"""
        if node:
            yield from self.postorder(node.left)
            yield from self.postorder(node.right)
            yield node.val
    
    def level_order(self, node):
        """BFS traversal"""
        if not node:
            return
        queue = deque([node])
        while queue:
            current = queue.popleft()
            yield current.val
            if current.left:
                queue.append(current.left)
            if current.right:
                queue.append(current.right)
```

---

## 9. What is a graph? How do you implement BFS and DFS?

**Answer:**

```python
from collections import defaultdict, deque

class Graph:
    def __init__(self):
        self.graph = defaultdict(list)
    
    def add_edge(self, u, v):
        self.graph[u].append(v)
    
    def bfs(self, start):
        """Breadth-First Search — O(V + E)"""
        visited = set()
        queue = deque([start])
        visited.add(start)
        result = []
        
        while queue:
            vertex = queue.popleft()
            result.append(vertex)
            for neighbor in self.graph[vertex]:
                if neighbor not in visited:
                    visited.add(neighbor)
                    queue.append(neighbor)
        return result
    
    def dfs(self, start, visited=None):
        """Depth-First Search — O(V + E)"""
        if visited is None:
            visited = set()
        visited.add(start)
        result = [start]
        for neighbor in self.graph[start]:
            if neighbor not in visited:
                result.extend(self.dfs(neighbor, visited))
        return result
    
    def has_cycle(self):
        """Detect cycle using DFS"""
        visited = set()
        rec_stack = set()
        
        def dfs_cycle(node):
            visited.add(node)
            rec_stack.add(node)
            for neighbor in self.graph[node]:
                if neighbor not in visited:
                    if dfs_cycle(neighbor):
                        return True
                elif neighbor in rec_stack:
                    return True
            rec_stack.remove(node)
            return False
        
        for node in self.graph:
            if node not in visited:
                if dfs_cycle(node):
                    return True
        return False
```

---

## 10. Implement dynamic programming: Fibonacci, Knapsack, LCS.

**Answer:**

```python
# Fibonacci — bottom-up DP
def fibonacci(n):
    if n <= 1:
        return n
    dp = [0] * (n + 1)
    dp[1] = 1
    for i in range(2, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    return dp[n]

# 0/1 Knapsack
def knapsack(weights, values, capacity):
    n = len(weights)
    dp = [[0] * (capacity + 1) for _ in range(n + 1)]
    
    for i in range(1, n + 1):
        for w in range(capacity + 1):
            if weights[i-1] <= w:
                dp[i][w] = max(
                    dp[i-1][w],
                    dp[i-1][w - weights[i-1]] + values[i-1]
                )
            else:
                dp[i][w] = dp[i-1][w]
    return dp[n][capacity]

# Longest Common Subsequence
def lcs(s1, s2):
    m, n = len(s1), len(s2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if s1[i-1] == s2[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    return dp[m][n]
```

---

## 11. Two Sum problem — multiple approaches.

**Answer:**

```python
# O(n) — Hash Map approach
def two_sum(nums, target):
    seen = {}
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
    return []

# O(n log n) — Two Pointer (sorted array)
def two_sum_sorted(nums, target):
    sorted_nums = sorted(enumerate(nums), key=lambda x: x[1])
    left, right = 0, len(sorted_nums) - 1
    while left < right:
        current_sum = sorted_nums[left][1] + sorted_nums[right][1]
        if current_sum == target:
            return [sorted_nums[left][0], sorted_nums[right][0]]
        elif current_sum < target:
            left += 1
        else:
            right -= 1
    return []
```

---

## 12. How do you find duplicates in a list?

**Answer:**

```python
# Method 1: Using set
def find_duplicates_set(lst):
    seen = set()
    duplicates = set()
    for item in lst:
        if item in seen:
            duplicates.add(item)
        seen.add(item)
    return list(duplicates)

# Method 2: Using Counter
from collections import Counter
def find_duplicates_counter(lst):
    return [item for item, count in Counter(lst).items() if count > 1]

# Method 3: Using list comprehension
def find_duplicates_comp(lst):
    return list(set(x for x in lst if lst.count(x) > 1))  # O(n²)
```

---

## 13. Implement a trie (prefix tree).

**Answer:**

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

class Trie:
    def __init__(self):
        self.root = TrieNode()
    
    def insert(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_end = True
    
    def search(self, word):
        node = self._find_node(word)
        return node is not None and node.is_end
    
    def starts_with(self, prefix):
        return self._find_node(prefix) is not None
    
    def _find_node(self, prefix):
        node = self.root
        for char in prefix:
            if char not in node.children:
                return None
            node = node.children[char]
        return node

# Use case: autocomplete, spell checking
trie = Trie()
for word in ["apple", "app", "application", "banana"]:
    trie.insert(word)
print(trie.starts_with("app"))  # True
print(trie.search("app"))       # True
```

---

## 14. Sliding window pattern examples.

**Answer:**

```python
# Maximum sum subarray of size k
def max_sum_subarray(arr, k):
    if len(arr) < k:
        return None
    window_sum = sum(arr[:k])
    max_sum = window_sum
    for i in range(k, len(arr)):
        window_sum += arr[i] - arr[i - k]
        max_sum = max(max_sum, window_sum)
    return max_sum

# Longest substring without repeating characters
def longest_unique_substring(s):
    char_index = {}
    max_len = 0
    start = 0
    for end, char in enumerate(s):
        if char in char_index and char_index[char] >= start:
            start = char_index[char] + 1
        char_index[char] = end
        max_len = max(max_len, end - start + 1)
    return max_len
```

---

## 15. How do you flatten a nested list?

**Answer:**

```python
# Recursive approach
def flatten(lst):
    result = []
    for item in lst:
        if isinstance(item, list):
            result.extend(flatten(item))
        else:
            result.append(item)
    return result

# Using generator
def flatten_gen(lst):
    for item in lst:
        if isinstance(item, list):
            yield from flatten_gen(item)
        else:
            yield item

# Using itertools
from itertools import chain
# For one level of nesting:
list(chain.from_iterable([[1, 2], [3, 4], [5]]))  # [1, 2, 3, 4, 5]

# Test
nested = [1, [2, 3], [4, [5, 6]], 7]
print(flatten(nested))          # [1, 2, 3, 4, 5, 6, 7]
print(list(flatten_gen(nested)))  # [1, 2, 3, 4, 5, 6, 7]
```

---

## 16. Implement LRU Cache.

**Answer:**

```python
from collections import OrderedDict

class LRUCache:
    def __init__(self, capacity):
        self.capacity = capacity
        self.cache = OrderedDict()
    
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

# Using functools
from functools import lru_cache

@lru_cache(maxsize=128)
def expensive_computation(n):
    return sum(i ** 2 for i in range(n))
```

---

## 17. What is the difference between array, list, and numpy array?

**Answer:**

```python
import array
import numpy as np

# Python list — heterogeneous, dynamic
py_list = [1, "hello", 3.14, [1, 2]]

# array.array — homogeneous, typed
arr = array.array('i', [1, 2, 3, 4])  # 'i' = signed int

# NumPy array — homogeneous, vectorized operations
np_arr = np.array([1, 2, 3, 4])
```

| Feature | list | array.array | numpy.ndarray |
|---------|------|-------------|---------------|
| Types | Mixed | Single type | Single type |
| Memory | High | Low | Low |
| Speed | Slow | Medium | Fast (vectorized) |
| Math ops | No | Basic | Full (broadcasting) |
| Use case | General | Type-specific storage | Scientific computing |

---

## 18. How do you implement memoization?

**Answer:**

```python
# Manual memoization with dict
def memoize(func):
    cache = {}
    def wrapper(*args):
        if args not in cache:
            cache[args] = func(*args)
        return cache[args]
    return wrapper

@memoize
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)

# Using functools.lru_cache
from functools import lru_cache

@lru_cache(maxsize=None)  # Unlimited cache
def factorial(n):
    return 1 if n <= 1 else n * factorial(n - 1)

# Check cache stats
print(factorial.cache_info())
# CacheInfo(hits=0, misses=11, maxsize=None, currsize=11)
```

---

## 19. Implement a min heap / max heap.

**Answer:**

```python
import heapq

# Min Heap (default in Python)
min_heap = []
heapq.heappush(min_heap, 5)
heapq.heappush(min_heap, 2)
heapq.heappush(min_heap, 8)
print(heapq.heappop(min_heap))  # 2 (smallest)

# Max Heap (negate values)
max_heap = []
heapq.heappush(max_heap, -5)
heapq.heappush(max_heap, -2)
heapq.heappush(max_heap, -8)
print(-heapq.heappop(max_heap))  # 8 (largest)

# Top K elements
def top_k_elements(nums, k):
    return heapq.nlargest(k, nums)

def bottom_k_elements(nums, k):
    return heapq.nsmallest(k, nums)

# Heap from list — O(n)
arr = [5, 3, 8, 1, 2]
heapq.heapify(arr)  # arr is now a min heap
```

---

## 20. How do you detect if a linked list has a cycle?

**Answer:**

```python
# Floyd's Cycle Detection (Tortoise and Hare)
def has_cycle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow is fast:
            return True
    return False

# Find the start of the cycle
def find_cycle_start(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow is fast:
            # Move one pointer to head
            slow = head
            while slow is not fast:
                slow = slow.next
                fast = fast.next
            return slow  # Start of cycle
    return None
```

---

## 21. Implement matrix operations without NumPy.

**Answer:**

```python
def matrix_multiply(A, B):
    """Multiply two matrices — O(n³)"""
    rows_A, cols_A = len(A), len(A[0])
    rows_B, cols_B = len(B), len(B[0])
    assert cols_A == rows_B
    
    result = [[0] * cols_B for _ in range(rows_A)]
    for i in range(rows_A):
        for j in range(cols_B):
            for k in range(cols_A):
                result[i][j] += A[i][k] * B[k][j]
    return result

def transpose(matrix):
    """Transpose a matrix"""
    return [list(row) for row in zip(*matrix)]

# Test
A = [[1, 2], [3, 4]]
B = [[5, 6], [7, 8]]
print(matrix_multiply(A, B))  # [[19, 22], [43, 50]]
print(transpose(A))           # [[1, 3], [2, 4]]
```

---

## 22. Explain and implement Dijkstra's algorithm.

**Answer:**

```python
import heapq

def dijkstra(graph, start):
    """
    Shortest path from start to all nodes — O((V + E) log V)
    graph: {node: [(neighbor, weight), ...]}
    """
    distances = {node: float('inf') for node in graph}
    distances[start] = 0
    pq = [(0, start)]
    visited = set()
    
    while pq:
        dist, node = heapq.heappop(pq)
        if node in visited:
            continue
        visited.add(node)
        
        for neighbor, weight in graph[node]:
            new_dist = dist + weight
            if new_dist < distances[neighbor]:
                distances[neighbor] = new_dist
                heapq.heappush(pq, (new_dist, neighbor))
    
    return distances

# Example
graph = {
    'A': [('B', 1), ('C', 4)],
    'B': [('C', 2), ('D', 5)],
    'C': [('D', 1)],
    'D': []
}
print(dijkstra(graph, 'A'))  # {'A': 0, 'B': 1, 'C': 3, 'D': 4}
```

---

## 23. How do you merge two sorted arrays?

**Answer:**

```python
def merge_sorted(arr1, arr2):
    result = []
    i = j = 0
    while i < len(arr1) and j < len(arr2):
        if arr1[i] <= arr2[j]:
            result.append(arr1[i])
            i += 1
        else:
            result.append(arr2[j])
            j += 1
    result.extend(arr1[i:])
    result.extend(arr2[j:])
    return result

# Using heapq for k-way merge
import heapq
def merge_k_sorted(arrays):
    return list(heapq.merge(*arrays))

print(merge_sorted([1, 3, 5], [2, 4, 6]))  # [1, 2, 3, 4, 5, 6]
```

---

## 24. Implement topological sort.

**Answer:**

```python
from collections import defaultdict, deque

def topological_sort(graph, num_nodes):
    """Kahn's algorithm (BFS-based) — O(V + E)"""
    in_degree = defaultdict(int)
    for node in graph:
        for neighbor in graph[node]:
            in_degree[neighbor] += 1
    
    queue = deque([n for n in range(num_nodes) if in_degree[n] == 0])
    result = []
    
    while queue:
        node = queue.popleft()
        result.append(node)
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    if len(result) != num_nodes:
        raise ValueError("Graph has a cycle!")
    return result
```

---

## 25. Implement a balanced parentheses checker.

**Answer:**

```python
def is_balanced(s):
    stack = []
    pairs = {')': '(', ']': '[', '}': '{'}
    
    for char in s:
        if char in '([{':
            stack.append(char)
        elif char in ')]}':
            if not stack or stack[-1] != pairs[char]:
                return False
            stack.pop()
    
    return len(stack) == 0

print(is_balanced("({[]})"))   # True
print(is_balanced("({[})"))    # False
print(is_balanced("((())"))    # False
```

---

## 26. How do you find the kth largest element?

**Answer:**

```python
import heapq

# Method 1: Using min-heap — O(n log k)
def kth_largest_heap(nums, k):
    return heapq.nlargest(k, nums)[-1]

# Method 2: Quickselect — O(n) average
def kth_largest_quickselect(nums, k):
    k = len(nums) - k  # Convert to kth smallest
    
    def quickselect(left, right):
        pivot = nums[right]
        p = left
        for i in range(left, right):
            if nums[i] <= pivot:
                nums[p], nums[i] = nums[i], nums[p]
                p += 1
        nums[p], nums[right] = nums[right], nums[p]
        
        if p == k:
            return nums[p]
        elif p < k:
            return quickselect(p + 1, right)
        else:
            return quickselect(left, p - 1)
    
    return quickselect(0, len(nums) - 1)

# Method 3: Sort — O(n log n)
def kth_largest_sort(nums, k):
    return sorted(nums, reverse=True)[k - 1]
```

---

## 27. Implement string reversal and palindrome check.

**Answer:**

```python
# String reversal
def reverse_string(s):
    return s[::-1]

# In-place reversal (list of chars)
def reverse_in_place(chars):
    left, right = 0, len(chars) - 1
    while left < right:
        chars[left], chars[right] = chars[right], chars[left]
        left += 1
        right -= 1

# Palindrome check
def is_palindrome(s):
    s = ''.join(c.lower() for c in s if c.isalnum())
    return s == s[::-1]

# Two-pointer palindrome
def is_palindrome_two_pointer(s):
    s = ''.join(c.lower() for c in s if c.isalnum())
    left, right = 0, len(s) - 1
    while left < right:
        if s[left] != s[right]:
            return False
        left += 1
        right -= 1
    return True

print(is_palindrome("A man, a plan, a canal: Panama"))  # True
```

---

## 28. How do you find the intersection of two arrays?

**Answer:**

```python
# Method 1: Set intersection — O(n + m)
def intersection(arr1, arr2):
    return list(set(arr1) & set(arr2))

# Method 2: Using Counter for duplicates
from collections import Counter
def intersection_with_dupes(arr1, arr2):
    c1, c2 = Counter(arr1), Counter(arr2)
    return list((c1 & c2).elements())

# Method 3: Two pointers (sorted arrays) — O(n + m)
def intersection_sorted(arr1, arr2):
    arr1.sort()
    arr2.sort()
    result = []
    i = j = 0
    while i < len(arr1) and j < len(arr2):
        if arr1[i] == arr2[j]:
            result.append(arr1[i])
            i += 1
            j += 1
        elif arr1[i] < arr2[j]:
            i += 1
        else:
            j += 1
    return result
```

---

## 29. Implement a basic calculator (expression parser).

**Answer:**

```python
def calculate(expression):
    """Evaluate arithmetic expression with +, -, *, /"""
    stack = []
    num = 0
    sign = '+'
    
    for i, char in enumerate(expression + '+'):
        if char.isdigit():
            num = num * 10 + int(char)
        elif char in '+-*/' or i == len(expression):
            if sign == '+':
                stack.append(num)
            elif sign == '-':
                stack.append(-num)
            elif sign == '*':
                stack.append(stack.pop() * num)
            elif sign == '/':
                stack.append(int(stack.pop() / num))
            sign = char
            num = 0
    
    return sum(stack)

print(calculate("3+2*2"))    # 7
print(calculate("14-3/2"))   # 13
```

---

## 30. Implement binary search tree operations.

**Answer:**

```python
class BSTNode:
    def __init__(self, val):
        self.val = val
        self.left = None
        self.right = None

class BST:
    def __init__(self):
        self.root = None
    
    def insert(self, val):
        self.root = self._insert(self.root, val)
    
    def _insert(self, node, val):
        if not node:
            return BSTNode(val)
        if val < node.val:
            node.left = self._insert(node.left, val)
        elif val > node.val:
            node.right = self._insert(node.right, val)
        return node
    
    def search(self, val):
        return self._search(self.root, val)
    
    def _search(self, node, val):
        if not node or node.val == val:
            return node
        if val < node.val:
            return self._search(node.left, val)
        return self._search(node.right, val)
    
    def find_min(self, node=None):
        node = node or self.root
        while node.left:
            node = node.left
        return node.val
    
    def find_max(self, node=None):
        node = node or self.root
        while node.right:
            node = node.right
        return node.val

    def delete(self, val):
        self.root = self._delete(self.root, val)
    
    def _delete(self, node, val):
        if not node:
            return None
        if val < node.val:
            node.left = self._delete(node.left, val)
        elif val > node.val:
            node.right = self._delete(node.right, val)
        else:
            if not node.left:
                return node.right
            if not node.right:
                return node.left
            # Node with two children
            successor = node.right
            while successor.left:
                successor = successor.left
            node.val = successor.val
            node.right = self._delete(node.right, successor.val)
        return node
```

---

## 31. How do you rotate an array?

**Answer:**

```python
# Method 1: Slicing — O(n) space
def rotate_right(arr, k):
    k = k % len(arr)
    return arr[-k:] + arr[:-k]

# Method 2: Reversal — O(1) space
def rotate_in_place(arr, k):
    k = k % len(arr)
    reverse(arr, 0, len(arr) - 1)
    reverse(arr, 0, k - 1)
    reverse(arr, k, len(arr) - 1)

def reverse(arr, start, end):
    while start < end:
        arr[start], arr[end] = arr[end], arr[start]
        start += 1
        end -= 1

# Method 3: collections.deque
from collections import deque
d = deque([1, 2, 3, 4, 5])
d.rotate(2)  # deque([4, 5, 1, 2, 3])
```

---

## 32. What is the difference between recursion and iteration?

**Answer:**

| Aspect | Recursion | Iteration |
|--------|-----------|-----------|
| Approach | Function calls itself | Loop-based |
| Memory | Uses call stack (risk of overflow) | Constant extra space |
| Readability | Often cleaner for tree/graph problems | Better for linear processes |
| Performance | Overhead from function calls | Generally faster |
| Termination | Base case | Loop condition |

```python
# Convert recursion to iteration using explicit stack
def factorial_recursive(n):
    if n <= 1: return 1
    return n * factorial_recursive(n - 1)

def factorial_iterative(n):
    result = 1
    for i in range(2, n + 1):
        result *= i
    return result

# Python's default recursion limit
import sys
print(sys.getrecursionlimit())  # 1000
sys.setrecursionlimit(10000)    # Increase if needed
```

---

## 33. How do you find all subsets of a set?

**Answer:**

```python
# Backtracking approach
def subsets(nums):
    result = []
    
    def backtrack(start, current):
        result.append(current[:])
        for i in range(start, len(nums)):
            current.append(nums[i])
            backtrack(i + 1, current)
            current.pop()
    
    backtrack(0, [])
    return result

# Bit manipulation approach
def subsets_bits(nums):
    n = len(nums)
    result = []
    for i in range(2 ** n):
        subset = [nums[j] for j in range(n) if i & (1 << j)]
        result.append(subset)
    return result

print(subsets([1, 2, 3]))
# [[], [1], [1,2], [1,2,3], [1,3], [2], [2,3], [3]]
```

---

## 34. Implement a union-find (disjoint set) data structure.

**Answer:**

```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n
        self.components = n
    
    def find(self, x):
        """Find with path compression"""
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    
    def union(self, x, y):
        """Union by rank"""
        px, py = self.find(x), self.find(y)
        if px == py:
            return False
        if self.rank[px] < self.rank[py]:
            px, py = py, px
        self.parent[py] = px
        if self.rank[px] == self.rank[py]:
            self.rank[px] += 1
        self.components -= 1
        return True
    
    def connected(self, x, y):
        return self.find(x) == self.find(y)

# Use case: detecting connected components, Kruskal's MST
uf = UnionFind(5)
uf.union(0, 1)
uf.union(2, 3)
print(uf.connected(0, 1))  # True
print(uf.connected(0, 2))  # False
print(uf.components)        # 3
```

---

## 35. Implement counting sort and radix sort.

**Answer:**

```python
def counting_sort(arr, max_val=None):
    """O(n + k) time, O(k) space — for small range integers"""
    if not arr:
        return arr
    max_val = max_val or max(arr)
    count = [0] * (max_val + 1)
    for num in arr:
        count[num] += 1
    result = []
    for i, c in enumerate(count):
        result.extend([i] * c)
    return result

def radix_sort(arr):
    """O(d * (n + k)) — for integers with d digits"""
    if not arr:
        return arr
    max_val = max(arr)
    exp = 1
    while max_val // exp > 0:
        arr = counting_sort_by_digit(arr, exp)
        exp *= 10
    return arr

def counting_sort_by_digit(arr, exp):
    output = [0] * len(arr)
    count = [0] * 10
    for num in arr:
        idx = (num // exp) % 10
        count[idx] += 1
    for i in range(1, 10):
        count[i] += count[i - 1]
    for num in reversed(arr):
        idx = (num // exp) % 10
        output[count[idx] - 1] = num
        count[idx] -= 1
    return output
```

---

## 36. How do you handle collisions in a hash table?

**Answer:**

Two main strategies:

**1. Chaining (Separate Chaining):**
```python
class HashTableChaining:
    def __init__(self, size=10):
        self.table = [[] for _ in range(size)]
    
    def put(self, key, value):
        idx = hash(key) % len(self.table)
        for i, (k, v) in enumerate(self.table[idx]):
            if k == key:
                self.table[idx][i] = (key, value)
                return
        self.table[idx].append((key, value))
```

**2. Open Addressing (Linear Probing):**
```python
class HashTableProbing:
    def __init__(self, size=10):
        self.size = size
        self.keys = [None] * size
        self.values = [None] * size
    
    def put(self, key, value):
        idx = hash(key) % self.size
        while self.keys[idx] is not None:
            if self.keys[idx] == key:
                self.values[idx] = value
                return
            idx = (idx + 1) % self.size
        self.keys[idx] = key
        self.values[idx] = value
```

---

## 37. Implement word frequency counter.

**Answer:**

```python
from collections import Counter
import re

def word_frequency(text):
    # Clean and split text
    words = re.findall(r'\b\w+\b', text.lower())
    return Counter(words)

def top_k_words(text, k):
    freq = word_frequency(text)
    return freq.most_common(k)

text = "the quick brown fox jumps over the lazy dog the fox"
print(word_frequency(text))
# Counter({'the': 3, 'fox': 2, 'quick': 1, ...})
print(top_k_words(text, 3))
# [('the', 3), ('fox', 2), ('quick', 1)]
```

---

## 38. How do you find the longest increasing subsequence?

**Answer:**

```python
# DP approach — O(n²)
def lis_dp(nums):
    if not nums:
        return 0
    dp = [1] * len(nums)
    for i in range(1, len(nums)):
        for j in range(i):
            if nums[j] < nums[i]:
                dp[i] = max(dp[i], dp[j] + 1)
    return max(dp)

# Binary search approach — O(n log n)
import bisect
def lis_binary_search(nums):
    tails = []
    for num in nums:
        pos = bisect.bisect_left(tails, num)
        if pos >= len(tails):
            tails.append(num)
        else:
            tails[pos] = num
    return len(tails)

print(lis_dp([10, 9, 2, 5, 3, 7, 101, 18]))  # 4: [2, 3, 7, 101]
```

---

## 39. Implement a circular queue.

**Answer:**

```python
class CircularQueue:
    def __init__(self, capacity):
        self.capacity = capacity
        self.queue = [None] * capacity
        self.head = 0
        self.tail = -1
        self.size = 0
    
    def enqueue(self, item):
        if self.is_full():
            raise OverflowError("Queue is full")
        self.tail = (self.tail + 1) % self.capacity
        self.queue[self.tail] = item
        self.size += 1
    
    def dequeue(self):
        if self.is_empty():
            raise IndexError("Queue is empty")
        item = self.queue[self.head]
        self.head = (self.head + 1) % self.capacity
        self.size -= 1
        return item
    
    def is_full(self):
        return self.size == self.capacity
    
    def is_empty(self):
        return self.size == 0
```

---

## 40. How do you implement a monotonic stack?

**Answer:**

```python
# Next Greater Element
def next_greater_element(nums):
    """For each element, find the next greater element to its right."""
    result = [-1] * len(nums)
    stack = []  # Stack of indices
    
    for i, num in enumerate(nums):
        while stack and nums[stack[-1]] < num:
            idx = stack.pop()
            result[idx] = num
        stack.append(i)
    
    return result

print(next_greater_element([4, 5, 2, 10, 8]))
# [5, 10, 10, -1, -1]

# Daily Temperatures
def daily_temperatures(temps):
    """Days until warmer temperature."""
    result = [0] * len(temps)
    stack = []
    
    for i, temp in enumerate(temps):
        while stack and temps[stack[-1]] < temp:
            idx = stack.pop()
            result[idx] = i - idx
        stack.append(i)
    
    return result
```
