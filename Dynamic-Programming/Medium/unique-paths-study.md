# Unique Paths - Study Guide

## Problem Summary
- **Difficulty**: Medium
- **Category**: Dynamic Programming
- **Tags**: Math, Dynamic Programming, Combinatorics
- **LeetCode Link**: https://leetcode.com/problems/unique-paths/

> **One-liner**: Count the number of unique paths from the top-left to the bottom-right of an m x n grid, moving only right or down.

## Key Intuition
Every cell `(i, j)` can only be reached from `(i-1, j)` (above) or `(i, j-1)` (left). So `dp[i][j] = dp[i-1][j] + dp[i][j-1]`. The entire first row and first column have exactly 1 path each. This is essentially building Pascal's triangle on a grid.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Recursion | O(2^(m+n)) | O(m+n) | Exponential, many repeated subproblems |
| 2D DP | O(m*n) | O(m*n) | Optimal time, uses full grid |
| 1D DP (space-optimized) | O(m*n) | O(n) | Optimal time and space |
| Combinatorics | O(m+n) | O(1) | Mathematical formula |

## Approach 1: Recursion
### Core Idea
At each cell, branch right and down. Count all paths reaching the destination.

### Code
```python
def uniquePaths(m: int, n: int) -> int:
    def helper(row, col):
        if row == m - 1 and col == n - 1:
            return 1
        if row >= m or col >= n:
            return 0
        return helper(row + 1, col) + helper(row, col + 1)

    return helper(0, 0)
```

### Walkthrough
For `m = 3, n = 3`:
```
helper(0,0) = helper(1,0) + helper(0,1)
helper(1,0) = helper(2,0) + helper(1,1)
helper(2,0) = helper(3,0)[=0] + helper(2,1)
... eventually sums to 6
```

## Approach 2: 1D DP -- Space Optimized (Optimal)
### Core Idea
Maintain a single row. For each new row, update each cell by adding the value to its left. The "above" value is already stored from the previous iteration.

### Code
```python
def uniquePaths(m: int, n: int) -> int:
    dp = [1] * n

    for row in range(1, m):
        for col in range(1, n):
            dp[col] += dp[col - 1]

    return dp[n - 1]
```

### Walkthrough
For `m = 3, n = 3`:
```
Initial:     dp = [1, 1, 1]
Row 1:
  col=1: dp[1] = 1 + 1 = 2
  col=2: dp[2] = 1 + 2 = 3
  dp = [1, 2, 3]
Row 2:
  col=1: dp[1] = 2 + 1 = 3
  col=2: dp[2] = 3 + 3 = 6
  dp = [1, 3, 6]
```
Return dp[2] = 6.

## Complexity Analysis
| Approach | Time | Space | Explanation |
|----------|------|-------|-------------|
| Recursion | O(2^(m+n)) | O(m+n) | Exponential branching |
| 1D DP | O(m*n) | O(n) | One pass per row, single array |
| Combinatorics | O(m+n) | O(1) | C(m+n-2, m-1) |

## Common Mistakes
1. **Forgetting that first row/column are all 1s**: There is only one way to reach any cell in the first row or column.
2. **Off-by-one in loop bounds**: Rows iterate from 1 to m-1, columns from 1 to n-1.
3. **Confusing m and n**: m is rows, n is columns. Make sure the dp array has size n.
4. **Not considering the 1x1 grid**: When m=1 and n=1, the answer is 1 (already at destination).

## Interview Tips
- **How to communicate**: "Each cell can only come from above or left, so the number of paths is the sum of those two neighbors. I initialize the first row to all 1s and build row by row."
- **Clarifying questions to ask**: Can m or n be 0? (No, constraints say >= 1.) Are there obstacles? (No, that is Unique Paths II.)
- **Optimization path**: "Recursion is O(2^(m+n)). DP is O(m*n). Combinatorics gives O(m+n)."
- **Bonus**: Mention the combinatorial solution: the robot makes `m-1` down moves and `n-1` right moves in some order, so the answer is C(m+n-2, m-1).

## Mnemonics / Memory Aids
- **"Above plus left"**: `dp[i][j] = dp[i-1][j] + dp[i][j-1]`. That is the entire recurrence.
- **"First row, first col = all ones"**: Only one way to go straight right or straight down.
- **"Grid Pascal"**: The grid values form Pascal's triangle rotated 45 degrees.

## Self-Assessment Checklist
- [ ] Can I write the DP recurrence immediately?
- [ ] Can I implement the space-optimized 1D version?
- [ ] Can I trace through a 3x3 grid by hand?
- [ ] Can I explain the combinatorial alternative?
- [ ] Can I handle Unique Paths II (with obstacles) as a follow-up?
