# Binary Search

## Problem Information
- **Difficulty**: Easy
- **Category**: Binary Search
- **Tags**: Array, Binary Search
- **LeetCode Link**: https://leetcode.com/problems/binary-search/

## Problem Statement
Given an array of integers `nums` which is sorted in ascending order, and an integer `target`, write a function to search `target` in `nums`. If `target` exists, then return its index. Otherwise, return `-1`.

You must write an algorithm with `O(log n)` runtime complexity.

### Examples
**Example 1:**
```
Input: nums = [-1,0,3,5,9,12], target = 9
Output: 4
Explanation: 9 exists in nums and its index is 4.
```

**Example 2:**
```
Input: nums = [-1,0,3,5,9,12], target = 2
Output: -1
Explanation: 2 does not exist in nums so return -1.
```

### Constraints
- `1 <= nums.length <= 10^4`
- `-10^4 < nums[i], target < 10^4`
- All the integers in `nums` are unique.
- `nums` is sorted in ascending order.

## Core Concept / Pattern
Binary search is the foundational divide-and-conquer search algorithm. It works by repeatedly dividing the search interval in half. Because the array is sorted, we can compare the target with the middle element and eliminate half of the remaining elements in each step.

This problem is the purest form of binary search -- no tricks, no modifications. Mastering this template is essential because nearly every other binary search problem builds on this exact pattern.

## Approach 1: Brute Force (Linear Search)
### Intuition
The simplest approach is to scan every element in the array and check if it equals the target.

### Algorithm
1. Iterate through each element of the array.
2. If the current element equals the target, return the index.
3. If no element matches after the full scan, return -1.

### Implementation
```python
def search(nums: list[int], target: int) -> int:
    for i in range(len(nums)):
        if nums[i] == target:
            return i
    return -1
```

### Complexity Analysis
- **Time Complexity**: O(n) -- we may need to check every element.
- **Space Complexity**: O(1) -- no extra space used.

### Why We Can Do Better
We are not using the fact that the array is sorted. A sorted array allows us to eliminate half the search space at each step, yielding a logarithmic time algorithm.

## Approach 2: Optimal Solution (Binary Search)
### Intuition
Maintain two pointers, `left` and `right`, defining the current search range. Compute the midpoint and compare `nums[mid]` with `target`:
- If equal, we found the target.
- If `target < nums[mid]`, the target must be in the left half.
- If `target > nums[mid]`, the target must be in the right half.

### Algorithm
1. Initialize `left = 0` and `right = len(nums) - 1`.
2. While `left <= right`:
   a. Compute `mid = left + (right - left) // 2` (avoids integer overflow).
   b. If `nums[mid] == target`, return `mid`.
   c. If `nums[mid] < target`, set `left = mid + 1`.
   d. If `nums[mid] > target`, set `right = mid - 1`.
3. Return -1 if the loop exits without finding the target.

### Implementation
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

### Complexity Analysis
- **Time Complexity**: O(log n) -- the search space is halved each iteration.
- **Space Complexity**: O(1) -- only a constant number of variables.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Single element array, target present | Return 0 | Boundary condition for smallest input |
| Single element array, target absent | Return -1 | Must handle miss on smallest input |
| Target is the first element | Return 0 | Left boundary check |
| Target is the last element | Return `len(nums) - 1` | Right boundary check |
| Target smaller than all elements | Return -1 | `left` moves past `right` immediately or never |
| Target larger than all elements | Return -1 | `right` moves past `left` |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Search Insert Position (LC #35) | Same pattern but returns insertion point | Easy |
| First Bad Version (LC #278) | Binary search on boolean condition | Easy |
| Search in Rotated Sorted Array (LC #33) | Binary search with rotation twist | Medium |
| Find Minimum in Rotated Sorted Array (LC #153) | Binary search variant | Medium |
| Search a 2D Matrix (LC #74) | Binary search on 2D structure | Medium |
