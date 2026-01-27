# LRU Cache

## Problem Information
- **Difficulty**: Medium
- **Category**: Hash Table
- **Tags**: Hash Table, Linked List, Design, Doubly-Linked List
- **LeetCode Link**: https://leetcode.com/problems/lru-cache/

## Problem Statement

Design a data structure that follows the constraints of a **Least Recently Used (LRU)** cache.

Implement the `LRUCache` class:
- `LRUCache(int capacity)` Initialize the LRU cache with **positive** size capacity.
- `int get(int key)` Return the value of the key if the key exists, otherwise return `-1`.
- `void put(int key, int value)` Update the value of the key if the key exists. Otherwise, add the key-value pair to the cache. If the number of keys exceeds the capacity from this operation, **evict** the least recently used key.

The functions `get` and `put` must each run in **O(1)** average time complexity.

### Examples

**Example 1:**
```
Input:
["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]
[[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]

Output:
[null, null, null, 1, null, -1, null, -1, 3, 4]

Explanation:
LRUCache lRUCache = new LRUCache(2);
lRUCache.put(1, 1);   // cache is {1=1}
lRUCache.put(2, 2);   // cache is {1=1, 2=2}
lRUCache.get(1);       // return 1, cache is {2=2, 1=1}
lRUCache.put(3, 3);   // evicts key 2, cache is {1=1, 3=3}
lRUCache.get(2);       // returns -1 (not found)
lRUCache.put(4, 4);   // evicts key 1, cache is {3=3, 4=4}
lRUCache.get(1);       // return -1 (not found)
lRUCache.get(3);       // return 3
lRUCache.get(4);       // return 4
```

### Constraints
- `1 <= capacity <= 3000`
- `0 <= key <= 10^4`
- `0 <= value <= 10^5`
- At most `2 * 10^5` calls will be made to `get` and `put`.

## Core Concept / Pattern

We need O(1) lookup (hash map) combined with O(1) ordering maintenance (doubly-linked list). The hash map provides instant access to nodes, while the doubly-linked list maintains the access order so we can quickly identify and evict the least recently used item.

## Approach 1: Brute Force (OrderedDict)

### Intuition

Python's `OrderedDict` maintains insertion order and supports `move_to_end()` in O(1). This provides a clean, high-level implementation.

### Algorithm

1. Use an `OrderedDict` as the cache.
2. `get`: If the key exists, move it to the end (most recent) and return the value.
3. `put`: If the key exists, move it to the end and update. If new, insert it. If capacity exceeded, remove the first (oldest) item.

### Implementation

```python
from collections import OrderedDict

class LRUCache:
    def __init__(self, capacity: int):
        self.cache = OrderedDict()
        self.capacity = capacity

    def get(self, key: int) -> int:
        if key not in self.cache:
            return -1
        self.cache.move_to_end(key)
        return self.cache[key]

    def put(self, key: int, value: int) -> None:
        if key in self.cache:
            self.cache.move_to_end(key)
        self.cache[key] = value
        if len(self.cache) > self.capacity:
            self.cache.popitem(last=False)
```

### Complexity Analysis
- **Time Complexity**: O(1) for both `get` and `put`.
- **Space Complexity**: O(capacity) for storing up to `capacity` entries.

### Why We Can Do Better

While `OrderedDict` is O(1) and correct, interviewers typically want to see you implement the underlying data structure yourself: a hash map combined with a doubly-linked list.

## Approach 2: Optimal Solution (Hash Map + Doubly-Linked List)

### Intuition

Use a hash map for O(1) key lookups and a doubly-linked list for O(1) insertion, deletion, and reordering. Sentinel head and tail nodes simplify edge-case handling by eliminating null checks.

### Algorithm

1. Create a `Node` class with `key`, `value`, `prev`, `next`.
2. Maintain a doubly-linked list with dummy `head` and `tail` nodes.
3. Hash map maps `key -> Node`.
4. `get(key)`: Look up the node, move it to the tail (most recent), return value.
5. `put(key, value)`: If exists, update value and move to tail. If new, create node, add to tail, add to map. If over capacity, remove node after head (LRU), delete from map.

### Implementation

```python
class Node:
    def __init__(self, key: int = 0, val: int = 0):
        self.key = key
        self.val = val
        self.prev = None
        self.next = None

class LRUCache:
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cache = {}  # key -> Node

        # Dummy head and tail
        self.head = Node()
        self.tail = Node()
        self.head.next = self.tail
        self.tail.prev = self.head

    def _remove(self, node: Node) -> None:
        """Remove a node from the doubly-linked list."""
        node.prev.next = node.next
        node.next.prev = node.prev

    def _add_to_tail(self, node: Node) -> None:
        """Add a node right before the tail (most recently used)."""
        prev_node = self.tail.prev
        prev_node.next = node
        node.prev = prev_node
        node.next = self.tail
        self.tail.prev = node

    def get(self, key: int) -> int:
        if key not in self.cache:
            return -1
        node = self.cache[key]
        self._remove(node)
        self._add_to_tail(node)
        return node.val

    def put(self, key: int, value: int) -> None:
        if key in self.cache:
            node = self.cache[key]
            node.val = value
            self._remove(node)
            self._add_to_tail(node)
        else:
            node = Node(key, value)
            self.cache[key] = node
            self._add_to_tail(node)
            if len(self.cache) > self.capacity:
                # Evict LRU: node right after head
                lru = self.head.next
                self._remove(lru)
                del self.cache[lru.key]

```

### Complexity Analysis
- **Time Complexity**: O(1) for both `get` and `put`. Hash map lookup is O(1); linked list operations are O(1).
- **Space Complexity**: O(capacity) for the hash map and linked list nodes.

## Edge Cases

| Edge Case | Scenario | Expected | Why |
|-----------|----------|----------|-----|
| Capacity of 1 | Put two items | First is evicted | Only room for one |
| Get missing key | `get(999)` | `-1` | Key never inserted |
| Update existing key | `put(1,1)` then `put(1,2)` | Value updates to 2 | No new entry created |
| Get refreshes order | `put(1,1)`, `put(2,2)`, `get(1)`, `put(3,3)` | Key 2 evicted (not 1) | get(1) made key 1 most recent |
| Sequential evictions | Fill to capacity, then keep putting new keys | Oldest evicted each time | FIFO-like when no gets |

## Related Problems

| Problem | Difficulty | Why Related |
|---------|------------|-------------|
| [LFU Cache (LeetCode #460)](https://leetcode.com/problems/lfu-cache/) | Hard | Cache eviction with frequency tracking |
| [Design HashMap (LeetCode #706)](https://leetcode.com/problems/design-hashmap/) | Easy | Hash map design fundamentals |
| [All O(1) Data Structure (LeetCode #432)](https://leetcode.com/problems/all-oone-data-structure/) | Hard | O(1) operations with linked list + hash map |
| [Design Linked List (LeetCode #707)](https://leetcode.com/problems/design-linked-list/) | Medium | Doubly-linked list implementation |
