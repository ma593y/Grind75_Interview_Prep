# Container With Most Water

## Problem Information
- **Difficulty**: Medium
- **Category**: Two Pointers
- **Tags**: Array, Two Pointers, Greedy
- **LeetCode Link**: https://leetcode.com/problems/container-with-most-water/

## Problem Statement

You are given an integer array `height` of length `n`. There are `n` vertical lines drawn such that the two endpoints of the `i`th line are `(i, 0)` and `(i, height[i])`.

Find two lines that together with the x-axis form a container, such that the container contains the most water.

Return the maximum amount of water a container can store.

**Notice** that you may not slant the container.

### Examples

**Example 1:**
```
Input: height = [1,8,6,2,5,4,8,3,7]
Output: 49
Explanation: The vertical lines are represented by array [1,8,6,2,5,4,8,3,7].
In this case, the max area of water the container can contain is 49 (between index 1 and index 8).
The width is 8 - 1 = 7, and the height is min(8, 7) = 7, so the area is 7 * 7 = 49.
```

**Example 2:**
```
Input: height = [1,1]
Output: 1
Explanation: The width is 1 and the height is min(1, 1) = 1, so the area is 1.
```

### Constraints
- `n == height.length`
- `2 <= n <= 10^5`
- `0 <= height[i] <= 10^4`

## Core Concept / Pattern

This problem uses the **two-pointer** technique to efficiently search for the pair of lines that maximize the area of water contained. The key insight is that the area between two lines is determined by the shorter line and the distance between them: `area = min(height[left], height[right]) * (right - left)`.

By starting with the widest possible container (pointers at both ends) and strategically moving the pointer pointing to the shorter line inward, we can guarantee we never miss the optimal solution. Moving the shorter line's pointer is the only way to potentially find a taller line that could increase the area, since moving the taller line's pointer can only decrease or maintain the limiting height while also decreasing the width.

## Approach 1: Brute Force

### Intuition

Check every possible pair of lines and compute the area for each pair. Track the maximum area found across all pairs.

### Algorithm

1. Initialize `max_area = 0`.
2. For each line `i` from `0` to `n-2`:
   - For each line `j` from `i+1` to `n-1`:
     - Compute `area = min(height[i], height[j]) * (j - i)`.
     - Update `max_area` if `area` is larger.
3. Return `max_area`.

### Implementation

```python
class Solution:
    def maxArea(self, height: list[int]) -> int:
        max_area = 0
        n = len(height)

        for i in range(n):
            for j in range(i + 1, n):
                width = j - i
                h = min(height[i], height[j])
                area = width * h
                max_area = max(max_area, area)

        return max_area
```

### Complexity Analysis
- **Time Complexity**: O(n^2) -- We check every pair of lines, resulting in n*(n-1)/2 pairs.
- **Space Complexity**: O(1) -- Only a constant amount of extra space is used.

### Why We Can Do Better

The brute force examines all O(n^2) pairs, but many of these pairs are clearly suboptimal. If we start from the widest container and use a greedy strategy to decide which pointer to move, we can reduce the search space to O(n) while guaranteeing we find the optimal answer.

## Approach 2: Optimal Solution (Two Pointers)

### Intuition

Start with the widest container by placing pointers at both ends of the array. The area is limited by the shorter of the two lines. Moving the pointer at the taller line inward would only reduce the width without any possibility of increasing the limiting height. Therefore, the only way to potentially find a larger area is to move the pointer at the shorter line inward, hoping to find a taller line.

### Algorithm

1. Initialize `left = 0`, `right = n - 1`, and `max_area = 0`.
2. While `left < right`:
   - Compute `area = min(height[left], height[right]) * (right - left)`.
   - Update `max_area` if `area` is larger.
   - If `height[left] < height[right]`, move `left` forward (increment).
   - Otherwise, move `right` backward (decrement).
3. Return `max_area`.

### Implementation

```python
class Solution:
    def maxArea(self, height: list[int]) -> int:
        left = 0
        right = len(height) - 1
        max_area = 0

        while left < right:
            width = right - left
            h = min(height[left], height[right])
            area = width * h
            max_area = max(max_area, area)

            if height[left] < height[right]:
                left += 1
            else:
                right -= 1

        return max_area
```

### Complexity Analysis
- **Time Complexity**: O(n) -- Each pointer moves at most n-1 times, and each step is O(1).
- **Space Complexity**: O(1) -- Only a constant number of variables are used.

## Edge Cases

| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Two elements only | Returns `min(h[0], h[1]) * 1` | Minimum valid input size |
| All heights equal | Returns `height[0] * (n - 1)` | Widest container is always optimal |
| All heights are 0 | Returns `0` | No water can be contained |
| Heights in ascending order | Optimal pair is not at the ends | Tests that pointer movement finds non-trivial solutions |
| Heights in descending order | Similar to ascending case | Ensures both pointers move correctly |
| One very tall line | Area limited by the shorter partner | Confirms the `min()` logic is correct |

## Related Problems

| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Trapping Rain Water (LeetCode #42) | Also uses two pointers on height array but sums water at each position | Hard |
| Two Sum II - Input Array Is Sorted (LeetCode #167) | Classic two-pointer pattern on sorted array | Medium |
| 3Sum (LeetCode #15) | Two-pointer technique inside an outer loop | Medium |
