# Trapping Rain Water

## Problem Information
- **Difficulty**: Hard
- **Category**: Two Pointers
- **Tags**: Array, Two Pointers, Dynamic Programming, Stack, Monotonic Stack
- **LeetCode Link**: https://leetcode.com/problems/trapping-rain-water/

## Problem Statement

Given `n` non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it can trap after raining.

### Examples

**Example 1:**
```
Input: height = [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6
Explanation: The elevation map [0,1,0,2,1,0,1,3,2,1,2,1] can trap 6 units of rain water.
Water is trapped at indices 2 (1 unit), 4 (1 unit), 5 (2 units), 6 (1 unit), 9 (1 unit).
```

**Example 2:**
```
Input: height = [4,2,0,3,2,5]
Output: 9
Explanation: Water fills between the bars. Index 1 traps 2, index 2 traps 4, index 3 traps 1, index 4 traps 2.
```

### Constraints
- `n == height.length`
- `1 <= n <= 2 * 10^4`
- `0 <= height[i] <= 10^5`

## Core Concept / Pattern

The water trapped at any position `i` is determined by the formula: `water[i] = min(max_left[i], max_right[i]) - height[i]`. The water level at each bar is constrained by the shorter of the tallest bars to its left and right. If this level is above the bar's own height, water accumulates there.

The two-pointer approach computes this efficiently by maintaining running maximums from both ends. When we know the left max is smaller than the right max, we can safely compute the water at the left pointer (because the right side is guaranteed to have a bar at least as tall as the current right max). This eliminates the need to precompute arrays.

## Approach 1: Brute Force

### Intuition

For each bar, find the tallest bar to its left and the tallest bar to its right. The water at that position is `min(left_max, right_max) - height[i]`, but only if this value is positive.

### Algorithm

1. Initialize `total_water = 0`.
2. For each index `i` from `0` to `n-1`:
   - Scan left from `i` to find `left_max = max(height[0..i])`.
   - Scan right from `i` to find `right_max = max(height[i..n-1])`.
   - Add `min(left_max, right_max) - height[i]` to `total_water`.
3. Return `total_water`.

### Implementation

```python
class Solution:
    def trap(self, height: list[int]) -> int:
        n = len(height)
        total_water = 0

        for i in range(n):
            left_max = 0
            for j in range(i + 1):
                left_max = max(left_max, height[j])

            right_max = 0
            for j in range(i, n):
                right_max = max(right_max, height[j])

            total_water += min(left_max, right_max) - height[i]

        return total_water
```

### Complexity Analysis
- **Time Complexity**: O(n^2) -- For each of the n bars, we scan left and right to find maximums.
- **Space Complexity**: O(1) -- Only constant extra space is used.

### Why We Can Do Better

The repeated scanning to find left and right maximums is redundant. We can precompute these arrays in O(n) time, or better yet, use two pointers to compute everything in a single pass with O(1) space.

## Approach 2: Optimal Solution (Two Pointers)

### Intuition

Use two pointers starting at both ends. Maintain `left_max` and `right_max` as the maximum heights seen so far from the left and right sides respectively. At each step, process the side with the smaller maximum. If `left_max <= right_max`, we know the water at `left` is determined by `left_max` (since the right side has a bar at least as tall as `right_max`, which is >= `left_max`). Similarly for the right side.

### Algorithm

1. Initialize `left = 0`, `right = n - 1`, `left_max = 0`, `right_max = 0`, `total_water = 0`.
2. While `left < right`:
   - If `height[left] <= height[right]`:
     - If `height[left] >= left_max`, update `left_max = height[left]`.
     - Else, add `left_max - height[left]` to `total_water`.
     - Increment `left`.
   - Else:
     - If `height[right] >= right_max`, update `right_max = height[right]`.
     - Else, add `right_max - height[right]` to `total_water`.
     - Decrement `right`.
3. Return `total_water`.

### Implementation

```python
class Solution:
    def trap(self, height: list[int]) -> int:
        if not height:
            return 0

        left = 0
        right = len(height) - 1
        left_max = 0
        right_max = 0
        total_water = 0

        while left < right:
            if height[left] <= height[right]:
                if height[left] >= left_max:
                    left_max = height[left]
                else:
                    total_water += left_max - height[left]
                left += 1
            else:
                if height[right] >= right_max:
                    right_max = height[right]
                else:
                    total_water += right_max - height[right]
                right -= 1

        return total_water
```

### Complexity Analysis
- **Time Complexity**: O(n) -- Each element is visited exactly once as the two pointers converge.
- **Space Complexity**: O(1) -- Only a fixed number of variables are used.

## Edge Cases

| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Single bar | Returns 0 | Cannot trap water with one bar |
| Two bars | Returns 0 | No space between to hold water |
| All same height | Returns 0 | No valleys to trap water |
| Strictly ascending | Returns 0 | Water flows off the right side |
| Strictly descending | Returns 0 | Water flows off the left side |
| Empty array | Returns 0 | No bars at all |
| V-shaped (e.g., [3,0,3]) | Returns 3 | Simple valley traps water |
| All zeros | Returns 0 | No bars to contain water |

## Related Problems

| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Container With Most Water (LeetCode #11) | Two pointers on heights, but maximizes area between two lines | Medium |
| Largest Rectangle in Histogram (LeetCode #84) | Uses monotonic stack on bars, related geometric reasoning | Hard |
| Trapping Rain Water II (LeetCode #407) | 3D version using priority queue (BFS on a 2D grid) | Hard |
| Pour Water (LeetCode #755) | Simulates water pouring on elevation map | Medium |
