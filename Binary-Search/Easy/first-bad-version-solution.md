# First Bad Version

## Problem Information
- **Difficulty**: Easy
- **Category**: Binary Search
- **Tags**: Binary Search, Interactive
- **LeetCode Link**: https://leetcode.com/problems/first-bad-version/

## Problem Statement
You are a product manager and currently leading a team to develop a new product. Unfortunately, the latest version of your product fails the quality check. Since each version is developed based on the previous version, all the versions after a bad version are also bad.

Suppose you have `n` versions `[1, 2, ..., n]` and you want to find out the first bad one, which causes all the following ones to be bad.

You are given an API `bool isBadVersion(version)` which returns whether `version` is bad. Implement a function to find the first bad version. You should minimize the number of calls to the API.

### Examples
**Example 1:**
```
Input: n = 5, bad = 4
Output: 4
Explanation:
call isBadVersion(3) -> false
call isBadVersion(5) -> true
call isBadVersion(4) -> true
So, 4 is the first bad version.
```

**Example 2:**
```
Input: n = 1, bad = 1
Output: 1
```

### Constraints
- `1 <= bad <= n <= 2^31 - 1`

## Core Concept / Pattern
This is a classic binary search problem on a boolean condition. The version sequence looks like `[false, false, ..., false, true, true, ..., true]` and we need to find the first `true`. This is the "find the leftmost boundary" variant of binary search.

The key insight is that we are not searching for a specific value but for a transition point where the predicate changes from false to true.

## Approach 1: Brute Force (Linear Scan)
### Intuition
Check each version starting from 1 until we find the first bad one.

### Algorithm
1. Iterate from version 1 to version n.
2. Call `isBadVersion(i)` for each version.
3. Return the first version where `isBadVersion` returns `true`.

### Implementation
```python
def firstBadVersion(n: int) -> int:
    for i in range(1, n + 1):
        if isBadVersion(i):
            return i
    return n  # Should not reach here given constraints
```

### Complexity Analysis
- **Time Complexity**: O(n) -- in the worst case, the first bad version is `n`.
- **Space Complexity**: O(1) -- no extra space.

### Why We Can Do Better
The boolean sequence is monotonic (all `false` then all `true`). This sorted structure lets us apply binary search to find the transition point in O(log n) API calls.

## Approach 2: Optimal Solution (Binary Search)
### Intuition
Use binary search to find the leftmost `true` in the boolean sequence. If `mid` is bad, the first bad version is at `mid` or to its left. If `mid` is not bad, the first bad version is to the right.

### Algorithm
1. Initialize `left = 1` and `right = n`.
2. While `left < right`:
   a. Compute `mid = left + (right - left) // 2`.
   b. If `isBadVersion(mid)` is `true`, set `right = mid` (mid could be the answer).
   c. If `isBadVersion(mid)` is `false`, set `left = mid + 1` (answer is after mid).
3. Return `left` (which equals `right` and is the first bad version).

### Implementation
```python
def firstBadVersion(n: int) -> int:
    left, right = 1, n

    while left < right:
        mid = left + (right - left) // 2

        if isBadVersion(mid):
            right = mid  # mid could be the first bad version
        else:
            left = mid + 1  # first bad version is after mid

    return left
```

### Complexity Analysis
- **Time Complexity**: O(log n) -- halving the search space each step.
- **Space Complexity**: O(1) -- only constant extra variables.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| n = 1, bad = 1 | Return 1 | Smallest possible input |
| First version is bad (bad = 1) | Return 1 | Left boundary |
| Last version is the only bad one | Return n | Right boundary |
| n = 2^31 - 1 | Must not overflow | Use `left + (right - left) // 2` |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Binary Search (LC #704) | Basic binary search template | Easy |
| Search Insert Position (LC #35) | Finding insertion boundary | Easy |
| Find Peak Element (LC #162) | Binary search on condition | Medium |
| Find First and Last Position (LC #34) | Left/right boundary search | Medium |
