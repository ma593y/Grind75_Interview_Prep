# Trapping Rain Water - Study Guide

## Problem Summary
- **Difficulty**: Hard / **Category**: Two Pointers / **Tags**: Array, Two Pointers, Dynamic Programming, Stack / **LeetCode Link**: https://leetcode.com/problems/trapping-rain-water/

> **One-liner**: Given an elevation map of bars, compute the total units of rainwater that can be trapped between the bars.

## Key Intuition

Water at any index `i` equals `min(max_left, max_right) - height[i]`. The water level is bounded by the shorter of the tallest walls on each side. The two-pointer technique lets us determine which side is the bottleneck: if `left_max <= right_max`, the water at the left pointer is fully determined by `left_max` because the right side is guaranteed to have at least `right_max` height. This avoids precomputing arrays and solves the problem in a single pass.

## Approaches Overview

| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Brute Force (scan left/right per bar) | O(n^2) | O(1) | Simple but too slow |
| Prefix Max Arrays (DP) | O(n) | O(n) | Fast but uses extra arrays |
| Two Pointers | O(n) | O(1) | Optimal time and space |
| Monotonic Stack | O(n) | O(n) | Alternative O(n), good for follow-ups |

## Approach 1: Prefix Max Arrays (DP)

### Core Idea
Precompute `left_max[i]` = max height from index 0 to i, and `right_max[i]` = max height from index i to n-1. Then water at each index is `min(left_max[i], right_max[i]) - height[i]`.

### Code

```python
class Solution:
    def trap(self, height: list[int]) -> int:
        if not height:
            return 0

        n = len(height)
        left_max = [0] * n
        right_max = [0] * n

        left_max[0] = height[0]
        for i in range(1, n):
            left_max[i] = max(left_max[i - 1], height[i])

        right_max[n - 1] = height[n - 1]
        for i in range(n - 2, -1, -1):
            right_max[i] = max(right_max[i + 1], height[i])

        total_water = 0
        for i in range(n):
            total_water += min(left_max[i], right_max[i]) - height[i]

        return total_water
```

### Walkthrough
Using `height = [0,1,0,2,1,0,1,3,2,1,2,1]`:
- `left_max  = [0,1,1,2,2,2,2,3,3,3,3,3]`
- `right_max = [3,3,3,3,3,3,3,3,2,2,2,1]`
- Water at index 2: min(1,3) - 0 = 1
- Water at index 5: min(2,3) - 0 = 2
- Sum all positions: 0+0+1+0+1+2+1+0+0+1+0+0 = 6

## Approach 2: Two Pointers (Optimal)

### Core Idea
Use two pointers from both ends. Track `left_max` and `right_max`. Process the side with the smaller current height. If `height[left] <= height[right]`, the water at `left` is determined by `left_max` because the right side has at least `right_max >= height[right] >= height[left]`. This guarantees correctness without needing arrays.

### Code

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

### Walkthrough
Using `height = [0,1,0,2,1,0,1,3,2,1,2,1]`:
- `left=0, right=11`: h[0]=0 <= h[11]=1. left_max=0, no water. left=1.
- `left=1, right=11`: h[1]=1 <= h[11]=1. left_max=1. left=2.
- `left=2, right=11`: h[2]=0 <= h[11]=1. water += 1-0=1. left=3.
- `left=3, right=11`: h[3]=2 > h[11]=1. right_max=1. right=10.
- `left=3, right=10`: h[3]=2 <= h[10]=2. left_max=2. left=4.
- `left=4, right=10`: h[4]=1 <= h[10]=2. water += 2-1=1. left=5.
- `left=5, right=10`: h[5]=0 <= h[10]=2. water += 2-0=2. left=6.
- `left=6, right=10`: h[6]=1 <= h[10]=2. water += 2-1=1. left=7.
- `left=7, right=10`: h[7]=3 > h[10]=2. right_max=2. right=9.
- `left=7, right=9`: h[7]=3 > h[9]=1. water += 2-1=1. right=8.
- `left=7, right=8`: h[7]=3 > h[8]=2. right_max=2, no water. right=7.
- Total: 1+1+2+1+1+0 = 6.

## Complexity Analysis

- **Time**: O(n) -- Each pointer moves inward one step at a time; total steps equal n.
- **Space**: O(1) -- Only four integer variables are maintained.

## Common Mistakes

1. **Forgetting to include the bar itself in the max**: `left_max` must include `height[left]` itself (the bar can be part of its own boundary). The code handles this by updating `left_max` before computing water.
2. **Adding negative water**: If `min(left_max, right_max) < height[i]`, the bar is taller than the water level. Always ensure you only add non-negative contributions (the algorithm naturally avoids this by updating max first).
3. **Processing both pointers each iteration**: Only one pointer should move per iteration. Moving both can cause skipped positions.
4. **Confusing with Container With Most Water**: That problem finds max area between two lines; this one sums water above every bar. They use similar techniques but solve different problems.

## Interview Tips

- **How to communicate**: Start by explaining the per-bar water formula `min(left_max, right_max) - height[i]`. Show the DP approach with prefix arrays first, then optimize to two pointers. The key insight to explain is *why* processing the smaller side is safe.
- **Clarifying questions to ask**: "Can heights be zero?" (Yes). "Can the array be empty?" (Constraint says n >= 1). "Are all values non-negative?" (Yes).
- **Optimization path**: Brute force O(n^2) -> DP with prefix arrays O(n) time O(n) space -> Two pointers O(n) time O(1) space. Each step has a clear motivation.
- **Follow-up variations**: "What about a 2D grid?" leads to Trapping Rain Water II (#407) using a priority queue. "What if bars have different widths?" changes the area calculation.

## Mnemonics / Memory Aids

- **"Water finds its level"**: At each bar, water rises to `min(left_max, right_max)` -- it is bounded by the shorter surrounding wall.
- **"Trust the taller side"**: When `left_max < right_max`, you can safely compute water on the left because the right side is at least as tall. Process the side you can trust.
- **"Two walls, one pool"**: Think of each position as a pool between its tallest left and right walls.

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?
