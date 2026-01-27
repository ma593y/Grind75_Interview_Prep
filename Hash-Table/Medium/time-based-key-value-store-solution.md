# Time Based Key-Value Store

## Problem Information
- **Difficulty**: Medium
- **Category**: Hash Table
- **Tags**: Hash Table, Binary Search, String, Design
- **LeetCode Link**: https://leetcode.com/problems/time-based-key-value-store/

## Problem Statement

Design a time-based key-value data structure that can store multiple values for the same key at different time stamps and retrieve the key's value at a certain timestamp.

Implement the `TimeMap` class:
- `TimeMap()` Initializes the object of the data structure.
- `void set(String key, String value, int timestamp)` Stores the key `key` with the value `value` at the given time `timestamp`.
- `String get(String key, int timestamp)` Returns a value such that `set` was called previously, with `timestamp_prev <= timestamp`. If there are multiple such values, it returns the value associated with the largest `timestamp_prev`. If there are no values, it returns the empty string `""`.

### Examples

**Example 1:**
```
Input:
["TimeMap", "set", "get", "get", "set", "get", "get"]
[[], ["foo", "bar", 1], ["foo", 1], ["foo", 3], ["foo", "bar2", 4], ["foo", 4], ["foo", 5]]

Output:
[null, null, "bar", "bar", null, "bar2", "bar2"]

Explanation:
TimeMap timeMap = new TimeMap();
timeMap.set("foo", "bar", 1);
timeMap.get("foo", 1);  // return "bar"
timeMap.get("foo", 3);  // return "bar" (no entry at 3, largest <= 3 is at 1)
timeMap.set("foo", "bar2", 4);
timeMap.get("foo", 4);  // return "bar2"
timeMap.get("foo", 5);  // return "bar2"
```

### Constraints
- `1 <= key.length, value.length <= 100`
- `key` and `value` consist of lowercase English letters and digits.
- `1 <= timestamp <= 10^7`
- All the timestamps `timestamp` of `set` are strictly increasing.
- At most `2 * 10^5` calls will be made to `set` and `get`.

## Core Concept / Pattern

Since timestamps are strictly increasing for each key, the list of (timestamp, value) pairs per key is always sorted. This enables binary search on the `get` operation to find the largest timestamp that does not exceed the query timestamp.

## Approach 1: Brute Force (Linear Search)

### Intuition

Store values in a list per key. On `get`, scan the list backwards to find the most recent timestamp that is less than or equal to the query timestamp.

### Algorithm

1. Use a dictionary mapping each key to a list of `(timestamp, value)` tuples.
2. `set`: Append `(timestamp, value)` to the key's list.
3. `get`: Iterate the list in reverse and return the first value whose timestamp <= query timestamp.

### Implementation

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

### Complexity Analysis
- **Time Complexity**: `set` is O(1). `get` is O(n) in the worst case where n is the number of entries for that key.
- **Space Complexity**: O(total number of set calls) for storing all entries.

### Why We Can Do Better

Since timestamps are always inserted in increasing order, the list is sorted. We can use binary search to reduce `get` from O(n) to O(log n).

## Approach 2: Optimal Solution (Binary Search)

### Intuition

Because `set` is always called with strictly increasing timestamps, each key's list is sorted by timestamp. We can use binary search to find the rightmost timestamp <= the query timestamp.

### Algorithm

1. Use a dictionary mapping each key to a list of `(timestamp, value)` tuples.
2. `set`: Append `(timestamp, value)` -- maintains sorted order automatically.
3. `get`: Use `bisect_right` to find the insertion point for the query timestamp. The entry just before this insertion point (if it exists) has the largest timestamp <= query.

### Implementation

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
        # bisect_right finds the insertion point for (timestamp+1,)
        # so index-1 gives us the largest timestamp <= query
        idx = bisect.bisect_right(entries, (timestamp, chr(127)))
        if idx == 0:
            return ""
        return entries[idx - 1][1]
```

### Complexity Analysis
- **Time Complexity**: `set` is O(1). `get` is O(log n) where n is the number of entries for that key.
- **Space Complexity**: O(total number of set calls) for storing all entries.

## Edge Cases

| Edge Case | Scenario | Expected Output | Why |
|-----------|----------|-----------------|-----|
| Key not found | `get("missing", 1)` | `""` | Key was never set |
| Timestamp before all entries | `set("k","v",5)` then `get("k",3)` | `""` | No timestamp <= 3 |
| Exact timestamp match | `set("k","v",5)` then `get("k",5)` | `"v"` | Exact match found |
| Timestamp between entries | `set("k","a",1)`, `set("k","b",5)`, `get("k",3)` | `"a"` | Largest ts <= 3 is 1 |
| Timestamp after all entries | `set("k","v",1)` then `get("k",100)` | `"v"` | Most recent value |
| Multiple keys | Different keys have independent histories | Correct per key | Separate lists per key |

## Related Problems

| Problem | Difficulty | Why Related |
|---------|------------|-------------|
| [LRU Cache (LeetCode #146)](https://leetcode.com/problems/lru-cache/) | Medium | Design data structure with hash map |
| [Snapshot Array (LeetCode #1146)](https://leetcode.com/problems/snapshot-array/) | Medium | Versioned data with binary search |
| [Stock Price Fluctuation (LeetCode #2034)](https://leetcode.com/problems/stock-price-fluctuation/) | Medium | Time-based data structure design |
| [Search a 2D Matrix (LeetCode #74)](https://leetcode.com/problems/search-a-2d-matrix/) | Medium | Binary search application |
