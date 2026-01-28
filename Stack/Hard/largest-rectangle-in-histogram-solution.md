# Largest Rectangle in Histogram

## Problem Information
- **Difficulty**: Hard
- **Category**: Stack
- **Tags**: Array, Stack, Monotonic Stack
- **LeetCode Link**: https://leetcode.com/problems/largest-rectangle-in-histogram/

## Problem Statement

Given an array of integers `heights` representing the histogram's bar height where the width of each bar is `1`, return the area of the largest rectangle in the histogram.

### Examples

**Example 1:**
```
Input: heights = [2,1,5,6,2,3]
Output: 10
Explanation: The largest rectangle has area = 10 (formed by heights[2]=5 and heights[3]=6,
spanning width 2 with height 5, giving 5 * 2 = 10).
```

**Example 2:**
```
Input: heights = [2,4]
Output: 4
Explanation: The largest rectangle is the single bar of height 4, area = 4 * 1 = 4.
```

### Constraints
- `1 <= heights.length <= 10^5`
- `0 <= heights[i] <= 10^4`

## Core Concept / Pattern

For each bar, the largest rectangle that uses that bar as its shortest bar extends left and right as far as all bars are at least as tall. The problem reduces to: for each bar `i`, find the nearest shorter bar to the left and the nearest shorter bar to the right. The rectangle's width is the distance between these two boundaries (exclusive), and the height is `heights[i]`.

A **monotonic stack** (maintaining indices in increasing order of height) efficiently finds these boundaries. When a bar is shorter than the stack's top, the top bar has found its right boundary. Its left boundary is the new stack top (or -1 if the stack is empty).

## Approach 1: Brute Force

### Intuition

For each bar, expand left and right while all bars are at least as tall. The area for bar `i` is `heights[i] * width`, where `width` is the total number of bars in the expanded range.

### Algorithm

1. For each index `i`:
   - Expand left while `heights[left] >= heights[i]`.
   - Expand right while `heights[right] >= heights[i]`.
   - Compute `area = heights[i] * (right - left + 1)`.
   - Track maximum area.
2. Return maximum area.

### Implementation

```python
class Solution:
    def largestRectangleArea(self, heights: list[int]) -> int:
        max_area = 0
        n = len(heights)

        for i in range(n):
            # Find left boundary
            left = i
            while left > 0 and heights[left - 1] >= heights[i]:
                left -= 1

            # Find right boundary
            right = i
            while right < n - 1 and heights[right + 1] >= heights[i]:
                right += 1

            # Compute area with heights[i] as the shortest bar
            area = heights[i] * (right - left + 1)
            max_area = max(max_area, area)

        return max_area
```

### Complexity Analysis
- **Time Complexity**: O(n^2) -- For each bar, the expansion can visit all other bars in the worst case (e.g., all bars the same height).
- **Space Complexity**: O(1) -- Only constant extra space.

### Why We Can Do Better

The repeated left/right expansion recalculates boundaries from scratch for each bar. A monotonic stack can find all left and right boundaries in a single pass, giving O(n) total time.

## Approach 2: Optimal Solution (Monotonic Stack)

### Intuition

Maintain a stack of indices in increasing order of height. When we encounter a bar shorter than the stack's top, the top bar's rectangle can no longer extend right -- its right boundary is the current index. Its left boundary is the element below it in the stack (or -1 if the stack is empty). We pop and compute the area for that bar.

After processing all bars, remaining bars in the stack extend all the way to the right end of the histogram.

### Algorithm

1. Initialize an empty stack and `max_area = 0`.
2. Iterate through each index `i` (and one extra index `n` with height 0 to flush the stack):
   - While the stack is non-empty and the current height is less than the height at the stack's top:
     - Pop the top index `h_index`.
     - The height of the rectangle is `heights[h_index]`.
     - The width extends from the new stack top + 1 to `i - 1`, so `width = i - stack[-1] - 1` (or `width = i` if the stack is empty).
     - Compute `area = heights[h_index] * width` and update `max_area`.
   - Push `i` onto the stack.
3. Return `max_area`.

### Implementation

```python
class Solution:
    def largestRectangleArea(self, heights: list[int]) -> int:
        stack = []  # Stack of indices
        max_area = 0
        n = len(heights)

        for i in range(n + 1):
            # Use height 0 for the sentinel at index n to flush remaining bars
            current_height = heights[i] if i < n else 0

            while stack and current_height < heights[stack[-1]]:
                h = heights[stack.pop()]
                # Width: from (stack top + 1) to (i - 1)
                w = i if not stack else i - stack[-1] - 1
                max_area = max(max_area, h * w)

            stack.append(i)

        return max_area
```

### Complexity Analysis
- **Time Complexity**: O(n) -- Each index is pushed onto and popped from the stack at most once, so the total number of operations is 2n.
- **Space Complexity**: O(n) -- The stack can hold up to n indices.

## Edge Cases

| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Single bar `[5]` | Returns `5` | Width is 1, height is 5 |
| All same height `[3,3,3]` | Returns `9` (3*3) | Rectangle spans entire histogram |
| Ascending order `[1,2,3,4]` | Returns `6` (3*2 at indices 2-3) | All bars flushed at the sentinel |
| Descending order `[4,3,2,1]` | Returns `6` (2*3 spanning indices 0-2) | Each bar immediately triggers pops |
| Contains zeros `[2,0,2]` | Returns `2` | Zero-height bars split the histogram |
| Two bars `[2,4]` | Returns `4` | Single tallest bar |
| All zeros `[0,0,0]` | Returns `0` | No rectangle possible |

## Related Problems

| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Maximal Rectangle (LeetCode #85) | Uses this as a subroutine on each row of a 2D matrix | Hard |
| Trapping Rain Water (LeetCode #42) | Similar monotonic stack technique on elevation data | Hard |
| Daily Temperatures (LeetCode #739) | Monotonic stack to find next greater element | Medium |
| Next Greater Element I (LeetCode #496) | Monotonic stack pattern | Easy |
