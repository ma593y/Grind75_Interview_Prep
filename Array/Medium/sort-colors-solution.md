# Sort Colors

## Problem Information
- **Difficulty**: Medium
- **Category**: Array
- **Tags**: Array, Two Pointers, Sorting
- **LeetCode Link**: https://leetcode.com/problems/sort-colors/

## Problem Statement
Given an array `nums` with `n` objects colored red, white, or blue, sort them in-place so that objects of the same color are adjacent, with the colors in the order red, white, and blue.

We will use the integers `0`, `1`, and `2` to represent the color red, white, and blue, respectively.

You must solve this problem without using the library's sort function.

### Examples
**Example 1:**
```
Input: nums = [2,0,2,1,1,0]
Output: [0,0,1,1,2,2]
```

**Example 2:**
```
Input: nums = [2,0,1]
Output: [0,1,2]
```

### Constraints
- `n == nums.length`
- `1 <= n <= 300`
- `nums[i]` is either `0`, `1`, or `2`

**Follow up**: Could you come up with a one-pass algorithm using only constant extra space?

## Core Concept / Pattern
This is the Dutch National Flag problem, invented by Edsger Dijkstra. The key insight is to use three pointers to partition the array into three sections: 0s (red), 1s (white), and 2s (blue). This enables a single-pass O(n) solution with O(1) space.

## Approach 1: Counting Sort

### Intuition
Count occurrences of each color, then overwrite the array with the correct number of each color.

### Algorithm
1. Count 0s, 1s, and 2s
2. Overwrite array: first count[0] elements with 0, next count[1] with 1, rest with 2

### Implementation
```python
class Solution:
    def sortColors(self, nums: list[int]) -> None:
        count = [0, 0, 0]
        for num in nums:
            count[num] += 1

        idx = 0
        for color in range(3):
            for _ in range(count[color]):
                nums[idx] = color
                idx += 1
```

### Complexity Analysis
- **Time Complexity**: O(n) - two passes through the array
- **Space Complexity**: O(1) - only storing 3 counts

### Why We Can Do Better
This requires two passes. The follow-up asks for a one-pass solution.

## Approach 2: Dutch National Flag (One Pass)

### Intuition
Maintain three pointers: `low` (boundary of 0s), `mid` (current element), and `high` (boundary of 2s). Elements before `low` are 0s, elements after `high` are 2s, and elements between `low` and `mid` are 1s.

### Algorithm
1. Initialize: low = 0, mid = 0, high = n - 1
2. While mid <= high:
   - If nums[mid] == 0: swap with nums[low], increment both low and mid
   - If nums[mid] == 1: just increment mid
   - If nums[mid] == 2: swap with nums[high], decrement high (don't increment mid!)

### Implementation
```python
class Solution:
    def sortColors(self, nums: list[int]) -> None:
        low, mid, high = 0, 0, len(nums) - 1

        while mid <= high:
            if nums[mid] == 0:
                nums[low], nums[mid] = nums[mid], nums[low]
                low += 1
                mid += 1
            elif nums[mid] == 1:
                mid += 1
            else:  # nums[mid] == 2
                nums[mid], nums[high] = nums[high], nums[mid]
                high -= 1
                # Don't increment mid! The swapped element needs checking
```

### Complexity Analysis
- **Time Complexity**: O(n) - single pass
- **Space Complexity**: O(1) - only three pointers

## Edge Cases

| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| All same color | Array unchanged | No swaps needed |
| Already sorted | Array unchanged | Algorithm should handle efficiently |
| Reverse sorted | Full rearrangement | Tests all swap cases |
| Single element | Unchanged | Minimal input |

## Related Problems

| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Sort List | Sorting linked list | Medium |
| Wiggle Sort | Different partitioning | Medium |
| Move Zeroes | Two-way partition | Easy |
