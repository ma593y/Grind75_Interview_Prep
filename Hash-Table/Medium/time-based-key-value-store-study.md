# Time Based Key-Value Store - Study Guide

## Problem Summary
> **One-liner**: Design a key-value store that associates timestamps with values and retrieves the most recent value at or before a given timestamp.

## Key Intuition

The critical insight is that timestamps are always strictly increasing per key, so the internal list is always sorted. This sorted property unlocks binary search for O(log n) lookups instead of O(n) linear scans.

## Approaches Overview

| Approach | set() | get() | Space | Key Idea |
|----------|-------|-------|-------|----------|
| Linear Search | O(1) | O(n) | O(n) | Scan list backwards |
| Binary Search | O(1) | O(log n) | O(n) | bisect on sorted timestamps |

## Approach 1: Linear Search

### Core Idea
Store (timestamp, value) pairs in a list. On get, walk backwards through the list to find the first timestamp <= query.

### Code

```python
from collections import defaultdict

class TimeMap:
    def __init__(self):
        self.store = defaultdict(list)

    def set(self, key: str, value: str, timestamp: int) -> None:
        self.store[key].append((timestamp, value))

    def get(self, key: str, timestamp: int) -> str:
        if key not in self.store:
            return ""
        for ts, val in reversed(self.store[key]):
            if ts <= timestamp:
                return val
        return ""
```

### Walkthrough
```
set("foo", "bar", 1)   -> store = {"foo": [(1, "bar")]}
set("foo", "bar2", 4)  -> store = {"foo": [(1, "bar"), (4, "bar2")]}
get("foo", 3)           -> scan backwards: (4,"bar2") -> 4 > 3, skip
                                            (1,"bar")  -> 1 <= 3, return "bar"
```

## Approach 2: Binary Search (Optimal)

### Core Idea
Since timestamps arrive in increasing order, the list per key is sorted. Use `bisect_right` to find the insertion index for the query timestamp, then look at the entry just before that index.

### Code

```python
from collections import defaultdict
import bisect

class TimeMap:
    def __init__(self):
        self.store = defaultdict(list)

    def set(self, key: str, value: str, timestamp: int) -> None:
        self.store[key].append((timestamp, value))

    def get(self, key: str, timestamp: int) -> str:
        if key not in self.store:
            return ""
        entries = self.store[key]
        idx = bisect.bisect_right(entries, (timestamp, chr(127)))
        if idx == 0:
            return ""
        return entries[idx - 1][1]
```

### Walkthrough
```
store["foo"] = [(1, "bar"), (4, "bar2")]

get("foo", 3):
  bisect_right for (3, chr(127)) in [(1,"bar"), (4,"bar2")]
  Tuples are compared element by element.
  (3, chr(127)) > (1, "bar") but (3, chr(127)) < (4, "bar2")
  insertion index = 1
  idx - 1 = 0 -> entries[0] = (1, "bar") -> return "bar"

get("foo", 5):
  bisect_right for (5, chr(127)) in [(1,"bar"), (4,"bar2")]
  (5, chr(127)) > both entries
  insertion index = 2
  idx - 1 = 1 -> entries[1] = (4, "bar2") -> return "bar2"
```

**Manual binary search alternative:**

```python
def get(self, key: str, timestamp: int) -> str:
    if key not in self.store:
        return ""
    entries = self.store[key]
    lo, hi = 0, len(entries) - 1
    result = ""
    while lo <= hi:
        mid = (lo + hi) // 2
        if entries[mid][0] <= timestamp:
            result = entries[mid][1]
            lo = mid + 1
        else:
            hi = mid - 1
    return result
```

## Complexity Analysis

| Operation | Linear Search | Binary Search |
|-----------|---------------|---------------|
| set() | O(1) | O(1) |
| get() | O(n) | O(log n) |
| Space | O(total entries) | O(total entries) |

## Common Mistakes

1. **Using `bisect_left` instead of `bisect_right`** -- we want the largest timestamp <= query, so `bisect_right` minus 1 gives the correct index.
2. **Forgetting the off-by-one** -- after `bisect_right`, the answer is at `idx - 1`, not `idx`.
3. **Not checking `idx == 0`** -- if the query timestamp is before all stored timestamps, there is no valid entry.
4. **Not leveraging the sorted guarantee** -- the problem states timestamps are strictly increasing, which makes binary search possible without explicit sorting.
5. **Using a wrong sentinel for tuple comparison** -- `chr(127)` ensures the binary search tuple compares as larger than any stored value string at the same timestamp.

## Interview Tips

- **State the sorted property early**: Mention that strictly increasing timestamps mean the list is inherently sorted -- this is the key insight that unlocks binary search.
- **Know both approaches**: Start with the linear search to show understanding, then optimize to binary search.
- **Mention `bisect` vs manual**: Show you know Python's `bisect` module but can also write binary search from scratch.
- **Design discussion**: This is a design problem -- discuss trade-offs like memory usage and whether to use a more complex structure (e.g., balanced BST) if timestamps were not guaranteed sorted.

## Mnemonics / Memory Aids

- **"Sorted for free"**: Timestamps come in order, so the list is always sorted without extra work.
- **"bisect_right minus one"**: The answer is always one step left of where the query would be inserted.
- **"Dict of sorted lists"**: The data structure is simply a dictionary where each value is a sorted list of (timestamp, value) tuples.

## Self-Assessment Checklist

- [ ] Can I explain why binary search works here (sorted timestamps)?
- [ ] Can I use `bisect_right` correctly with tuple comparisons?
- [ ] Can I implement manual binary search for this problem?
- [ ] Can I handle the edge case where the query timestamp is before all entries?
- [ ] Can I explain the difference between `bisect_left` and `bisect_right` in this context?
- [ ] Do I understand why `set()` is O(1) even though we are maintaining a sorted structure?
