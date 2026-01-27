# Binary Search - Study Guide

## Problem Summary
- **Difficulty**: Easy
- **Category**: Binary Search
- **Tags**: Array, Binary Search
- **LeetCode Link**: https://leetcode.com/problems/binary-search/

> **One-liner**: Find the index of a target value in a sorted array, or return -1 if not found.

## Key Intuition
A sorted array lets us compare the target against the middle element and discard half the search space each time. This is the classic divide-and-conquer approach that reduces an O(n) linear scan to O(log n). The key invariant is that the target, if it exists, always lies within the `[left, right]` window.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Linear Scan | O(n) | O(1) | Simple but ignores sorted property |
| Binary Search | O(log n) | O(1) | Optimal -- exploits sorted order |

## Approach 1: Linear Scan
### Core Idea
Check every element one by one until the target is found or the array is exhausted.

### Code
```python
def search(nums: list[int], target: int) -> int:
    for i in range(len(nums)):
        if nums[i] == target:
            return i
    return -1
```

### Walkthrough
For `nums = [-1, 0, 3, 5, 9, 12]`, `target = 9`:
- i=0: -1 != 9, continue
- i=1: 0 != 9, continue
- i=2: 3 != 9, continue
- i=3: 5 != 9, continue
- i=4: 9 == 9, return 4

## Approach 2: Binary Search (Optimal)
### Core Idea
Maintain a `[left, right]` window. Compare the middle element with target to decide which half to keep. Repeat until found or the window is empty.

### Code
```python
def search(nums: list[int], target: int) -> int:
    left, right = 0, len(nums) - 1

    while left <= right:
        mid = left + (right - left) // 2

        if nums[mid] == target:
            return mid
        elif nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1

    return -1
```

### Walkthrough
For `nums = [-1, 0, 3, 5, 9, 12]`, `target = 9`:
- left=0, right=5, mid=2: nums[2]=3 < 9, so left=3
- left=3, right=5, mid=4: nums[4]=9 == 9, return 4

## Complexity Analysis
| Approach | Time | Space | Explanation |
|----------|------|-------|-------------|
| Linear Scan | O(n) | O(1) | Must check each element |
| Binary Search | O(log n) | O(1) | Halves search space each step |

## Common Mistakes
1. **Off-by-one errors**: Using `left < right` instead of `left <= right` misses the case where the target is the last remaining element.
2. **Integer overflow on mid**: `(left + right) // 2` can overflow in languages like Java/C++. Use `left + (right - left) // 2` instead.
3. **Not moving `left`/`right` past `mid`**: Writing `left = mid` instead of `left = mid + 1` causes an infinite loop.
4. **Forgetting the array is 0-indexed**: Returning positions instead of indices.

## Interview Tips
- **How to communicate**: Start by confirming the array is sorted and contains unique elements. Mention that sorted order implies binary search for O(log n).
- **Clarifying questions to ask**: Is the array sorted? Are elements unique? Can the array be empty? What should be returned if the target is not found?
- **Optimization path**: "Linear scan is O(n), but since the array is sorted, binary search gives us O(log n)."
- **Follow-up variations**: What if duplicates exist and you need the first/last occurrence? What if the array is rotated?

## Mnemonics / Memory Aids
- **"Left Less-or-Equal Right"**: The while condition is `left <= right` for inclusive bounds.
- **"Mid moves the boundary"**: After checking mid, always move `left` or `right` past mid (mid+1 or mid-1).
- **"SHE" -- Sorted, Halve, Eliminate**: Confirm sorted, halve at mid, eliminate one side.

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?
