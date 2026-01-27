# Search in Rotated Sorted Array

## Problem Information
- **Difficulty**: Medium
- **Category**: Binary Search
- **Tags**: Array, Binary Search
- **LeetCode Link**: https://leetcode.com/problems/search-in-rotated-sorted-array/

## Problem Statement
There is an integer array `nums` sorted in ascending order (with distinct values). Prior to being passed to your function, `nums` is possibly rotated at an unknown pivot index `k` (`1 <= k < nums.length`) such that the resulting array is `[nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]` (0-indexed).

Given the array `nums` after the possible rotation and an integer `target`, return the index of `target` if it is in `nums`, or `-1` if it is not in `nums`.

You must write an algorithm with `O(log n)` runtime complexity.

### Examples
**Example 1:**
```
Input: nums = [4,5,6,7,0,1,2], target = 0
Output: 4
```

**Example 2:**
```
Input: nums = [4,5,6,7,0,1,2], target = 3
Output: -1
```

**Example 3:**
```
Input: nums = [1], target = 0
Output: -1
```

### Constraints
- `1 <= nums.length <= 5000`
- `-10^4 <= nums[i] <= 10^4`
- All values of `nums` are unique.
- `nums` is an ascending array that is possibly rotated.

## Core Concept / Pattern
The array was originally sorted but then rotated. This means at any point, at least one half of the array (split at mid) is guaranteed to be sorted. We can determine which half is sorted by comparing `nums[left]` with `nums[mid]`, and then check whether the target lies within the sorted half to decide which direction to search.

This problem teaches the critical skill of adapting binary search when the standard sorted assumption is partially broken.

## Approach 1: Brute Force (Linear Search)
### Intuition
Simply scan every element to find the target.

### Algorithm
1. Iterate through the array.
2. Return the index if the element matches the target.
3. Return -1 if not found.

### Implementation
```python
def search(nums: list[int], target: int) -> int:
    for i in range(len(nums)):
        if nums[i] == target:
            return i
    return -1
```

### Complexity Analysis
- **Time Complexity**: O(n) -- scan the entire array.
- **Space Complexity**: O(1) -- no extra space.

### Why We Can Do Better
Even though the array is rotated, it still has a structural property we can exploit: one half is always sorted. This lets us use a modified binary search.

## Approach 2: Optimal Solution (Modified Binary Search)
### Intuition
At each step, determine which half is sorted. If the target falls within the sorted half's range, search that half. Otherwise, search the other half.

### Algorithm
1. Initialize `left = 0`, `right = len(nums) - 1`.
2. While `left <= right`:
   a. Compute `mid = left + (right - left) // 2`.
   b. If `nums[mid] == target`, return `mid`.
   c. If the left half is sorted (`nums[left] <= nums[mid]`):
      - If `nums[left] <= target < nums[mid]`, search left: `right = mid - 1`.
      - Else search right: `left = mid + 1`.
   d. Else the right half is sorted:
      - If `nums[mid] < target <= nums[right]`, search right: `left = mid + 1`.
      - Else search left: `right = mid - 1`.
3. Return -1.

### Implementation
```python
def search(nums: list[int], target: int) -> int:
    left, right = 0, len(nums) - 1

    while left <= right:
        mid = left + (right - left) // 2

        if nums[mid] == target:
            return mid

        # Left half is sorted
        if nums[left] <= nums[mid]:
            if nums[left] <= target < nums[mid]:
                right = mid - 1
            else:
                left = mid + 1
        # Right half is sorted
        else:
            if nums[mid] < target <= nums[right]:
                left = mid + 1
            else:
                right = mid - 1

    return -1
```

### Complexity Analysis
- **Time Complexity**: O(log n) -- halving search space each step.
- **Space Complexity**: O(1) -- constant extra space.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Single element, target present | Return 0 | Smallest input |
| Single element, target absent | Return -1 | Smallest miss |
| No rotation (already sorted) | Works correctly | Must handle unrotated input |
| Target at pivot point | Found correctly | Rotation boundary |
| Target is first or last element | Found correctly | Boundary elements |
| Array of length 2 | Works correctly | Minimal rotation |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Binary Search (LC #704) | Base binary search template | Easy |
| Find Minimum in Rotated Sorted Array (LC #153) | Find pivot instead of target | Medium |
| Search in Rotated Sorted Array II (LC #81) | Allows duplicates | Medium |
| Find Peak Element (LC #162) | Binary search with comparison | Medium |
