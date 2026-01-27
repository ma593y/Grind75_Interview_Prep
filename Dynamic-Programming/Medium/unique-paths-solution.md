# Unique Paths

## Problem Information
- **Difficulty**: Medium
- **Category**: Dynamic Programming
- **Tags**: Math, Dynamic Programming, Combinatorics
- **LeetCode Link**: https://leetcode.com/problems/unique-paths/

## Problem Statement
There is a robot on an `m x n` grid. The robot is initially located at the top-left corner (i.e., `grid[0][0]`). The robot tries to move to the bottom-right corner (i.e., `grid[m - 1][n - 1]`). The robot can only move either down or right at any point in time.

Given the two integers `m` and `n`, return the number of possible unique paths that the robot can take to reach the bottom-right corner.

### Examples
**Example 1:**
```
Input: m = 3, n = 7
Output: 28
```

**Example 2:**
```
Input: m = 3, n = 2
Output: 3
Explanation: From the top-left corner, there are a total of 3 ways to reach the bottom-right corner:
1. Right -> Down -> Down
2. Down -> Down -> Right
3. Down -> Right -> Down
```

### Constraints
- `1 <= m, n <= 100`

## Core Concept / Pattern
Each cell can only be reached from the cell above it or the cell to its left. Therefore `dp[i][j] = dp[i-1][j] + dp[i][j-1]`. The first row and first column all have exactly one path (straight right or straight down). This is a classic 2D DP problem, also solvable with combinatorics.

## Approach 1: Brute Force (Recursion)
### Intuition
At each cell, the robot can go right or down. Recursively count all paths from (0,0) to (m-1, n-1).

### Algorithm
1. Base case: if at destination `(m-1, n-1)`, return 1.
2. If out of bounds, return 0.
3. Return `paths(row+1, col) + paths(row, col+1)`.

### Implementation
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

### Complexity Analysis
- **Time Complexity**: O(2^(m+n)) -- binary branching at each step.
- **Space Complexity**: O(m + n) -- recursion depth.

### Why We Can Do Better
Many cells are visited repeatedly. DP stores each cell's result once for an O(m*n) solution.

## Approach 2: Optimal Solution (DP with Space Optimization)
### Intuition
We only need the previous row to compute the current row, so a single 1D array suffices. Each cell accumulates the value from the left (same row) and above (previous row, same position in the array before update).

### Algorithm
1. Create `dp` array of size `n`, initialized to 1 (first row has one path to each cell).
2. For each row from 1 to `m-1`:
   - For each column from 1 to `n-1`:
     - `dp[col] += dp[col - 1]` (dp[col] already holds the "above" value; dp[col-1] is the "left" value).
3. Return `dp[n - 1]`.

### Implementation
```python
def uniquePaths(m: int, n: int) -> int:
    dp = [1] * n

    for row in range(1, m):
        for col in range(1, n):
            dp[col] += dp[col - 1]

    return dp[n - 1]
```

### Complexity Analysis
- **Time Complexity**: O(m * n) -- fill every cell once.
- **Space Complexity**: O(n) -- single row maintained.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| m = 1 or n = 1 | Return 1 | Only one path (straight line) |
| m = 1, n = 1 | Return 1 | Already at destination |
| m = 100, n = 100 | Large number (correct) | Must handle large values |
| m = 2, n = 2 | Return 2 | Minimal non-trivial grid |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Unique Paths II (LC #63) | Adds obstacles to the grid | Medium |
| Minimum Path Sum (LC #64) | Minimize cost instead of count paths | Medium |
| Climbing Stairs (LC #70) | 1D version of path counting | Easy |
| Dungeon Game (LC #174) | Path optimization with constraints | Hard |
| Pascal's Triangle (LC #118) | Same combinatorial structure | Easy |
