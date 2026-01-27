# LRU Cache - Study Guide

## Problem Summary
> **One-liner**: Implement a cache that evicts the least recently used item when capacity is exceeded, with O(1) get and put operations.

## Key Intuition

The challenge is maintaining both fast lookups AND fast ordering. A hash map gives O(1) lookup but has no order. A linked list maintains order but has O(n) lookup. Combining them -- a hash map pointing to nodes in a doubly-linked list -- gives O(1) for everything.

## Approaches Overview

| Approach | get() | put() | Space | Key Idea |
|----------|-------|-------|-------|----------|
| OrderedDict | O(1) | O(1) | O(cap) | Python built-in handles both |
| HashMap + Doubly-Linked List | O(1) | O(1) | O(cap) | Manual implementation of the same idea |

## Approach 1: OrderedDict

### Core Idea
Python's `OrderedDict` is essentially a hash map + doubly-linked list under the hood. Use `move_to_end` to refresh access order and `popitem(last=False)` to evict the oldest entry.

### Code

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

### Walkthrough
```
capacity = 2
put(1,1): cache = {1:1}
put(2,2): cache = {1:1, 2:2}
get(1):   move 1 to end -> cache = {2:2, 1:1}, return 1
put(3,3): cache over capacity -> pop first (key=2)
          cache = {1:1, 3:3}
get(2):   not found -> return -1
```

## Approach 2: HashMap + Doubly-Linked List (Optimal)

### Core Idea
Build the OrderedDict yourself. A hash map provides O(1) key-to-node lookup. A doubly-linked list maintains usage order. Sentinel head/tail nodes eliminate edge cases for empty lists.

**Data Structure Visualization:**
```
head <-> [LRU node] <-> ... <-> [MRU node] <-> tail
             ^                       ^
             |                       |
         evict this           add new here
```

### Code

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
        self.cache = {}
        self.head = Node()
        self.tail = Node()
        self.head.next = self.tail
        self.tail.prev = self.head

    def _remove(self, node: Node) -> None:
        node.prev.next = node.next
        node.next.prev = node.prev

    def _add_to_tail(self, node: Node) -> None:
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
                lru = self.head.next
                self._remove(lru)
                del self.cache[lru.key]
```

### Walkthrough
```
capacity = 2

put(1, 1):
  Create Node(1,1), add to tail
  head <-> Node(1,1) <-> tail
  cache = {1: Node(1,1)}

put(2, 2):
  Create Node(2,2), add to tail
  head <-> Node(1,1) <-> Node(2,2) <-> tail
  cache = {1: Node(1,1), 2: Node(2,2)}

get(1):
  Found Node(1,1). Remove from list, add to tail.
  head <-> Node(2,2) <-> Node(1,1) <-> tail
  Return 1

put(3, 3):
  Create Node(3,3), add to tail
  head <-> Node(2,2) <-> Node(1,1) <-> Node(3,3) <-> tail
  Over capacity! Evict head.next = Node(2,2)
  head <-> Node(1,1) <-> Node(3,3) <-> tail
  cache = {1: Node(1,1), 3: Node(3,3)}
```

## Complexity Analysis

| Operation | Time | Space | Notes |
|-----------|------|-------|-------|
| get() | O(1) | - | Hash map lookup + list move |
| put() | O(1) | - | Hash map insert + list add + possible evict |
| Overall Space | - | O(capacity) | Hash map + linked list nodes |

## Common Mistakes

1. **Forgetting to store the key in the node** -- when evicting, you need the key to remove it from the hash map. Without the key in the node, you cannot do this in O(1).
2. **Not using sentinel nodes** -- without dummy head/tail, you need special cases for empty lists, single-element lists, etc.
3. **Forgetting to update the hash map on put for existing key** -- if the key already exists, you must update the value in the existing node, not create a duplicate.
4. **Forgetting to delete from hash map on eviction** -- removing the node from the linked list is not enough; you must also `del cache[key]`.
5. **Wrong eviction target** -- the LRU item is at `head.next`, not `tail.prev`.

## Interview Tips

- **Draw the data structure**: Sketch the doubly-linked list with head/tail sentinels and the hash map arrows pointing to nodes.
- **Explain sentinel nodes**: They eliminate all edge cases (empty list, single element) and simplify the code significantly.
- **Mention OrderedDict**: Show you know the Pythonic solution but pivot to the manual implementation to demonstrate deeper understanding.
- **Start with the two helper methods**: `_remove(node)` and `_add_to_tail(node)` are the building blocks. Get these right first, and `get`/`put` become trivial.
- **This is a top interview question**: It tests OOP design, data structure knowledge, and attention to edge cases simultaneously.

## Mnemonics / Memory Aids

- **"Hash for speed, list for order"**: The hash map handles fast lookups; the linked list handles the eviction order.
- **"Head is old, tail is new"**: Items near the head are least recently used (eviction candidates); items near the tail are most recently used.
- **"Remove then re-add"**: Every access (get or put of existing key) means remove the node from its current position and add it to the tail.
- **"Node needs its key"**: Always store the key inside the node -- you will need it during eviction.

## Self-Assessment Checklist

- [ ] Can I implement the Node class with key, val, prev, next from memory?
- [ ] Can I set up sentinel head and tail nodes correctly?
- [ ] Can I write `_remove()` and `_add_to_tail()` helper methods?
- [ ] Can I handle the eviction logic in `put()` including hash map cleanup?
- [ ] Can I explain why we need both a hash map AND a linked list?
- [ ] Can I trace through a full example showing the state of both data structures?
- [ ] Do I understand why `get()` also needs to update the linked list order?
