# Climbing Stairs

## Problem Information
- **Difficulty**: Easy
- **Category**: Dynamic Programming
- **Tags**: Math, Dynamic Programming, Memoization
- **LeetCode Link**: https://leetcode.com/problems/climbing-stairs/

## Problem Statement
You are climbing a staircase. It takes `n` steps to reach the top. Each time you can either climb `1` or `2` steps. In how many distinct ways can you climb to the top?

### Examples
**Example 1:**
```
Input: n = 2
Output: 2
Explanation: There are two ways to climb to the top.
1. 1 step + 1 step
2. 2 steps
```

**Example 2:**
```
Input: n = 3
Output: 3
Explanation: There are three ways to climb to the top.
1. 1 step + 1 step + 1 step
2. 1 step + 2 steps
3. 2 steps + 1 step
```

### Constraints
- `1 <= n <= 45`

## Core Concept / Pattern
This is the classic introduction to dynamic programming. The number of ways to reach step `n` depends only on the number of ways to reach step `n-1` (take 1 step) and step `n-2` (take 2 steps). This recurrence relation is identical to the Fibonacci sequence: `dp[n] = dp[n-1] + dp[n-2]`.

## Approach 1: Brute Force (Recursion)
### Intuition
At each step, we have two choices: climb 1 step or climb 2 steps. We can recursively try both and sum the results.

### Algorithm
1. Base case: If `n <= 1`, return 1 (one way to stay or take one step).
2. Recursively return `climb(n-1) + climb(n-2)`.

### Implementation
```python
def climbStairs(n: int) -> int:
    if n <= 1:
        return 1
    return climbStairs(n - 1) + climbStairs(n - 2)
```

### Complexity Analysis
- **Time Complexity**: O(2^n) -- each call branches into two, creating an exponential tree.
- **Space Complexity**: O(n) -- recursion stack depth.

### Why We Can Do Better
There is massive overlapping computation. `climbStairs(3)` is computed many times in the recursive tree. We can cache results or build the solution bottom-up.

## Approach 2: Optimal Solution (Bottom-Up DP with Space Optimization)
### Intuition
Since `dp[i]` only depends on `dp[i-1]` and `dp[i-2]`, we do not need an entire array. We can maintain just two variables and iterate from the bottom up, identical to computing Fibonacci numbers iteratively.

### Algorithm
1. Initialize `prev2 = 1` (ways to reach step 0) and `prev1 = 1` (ways to reach step 1).
2. For each step from 2 to n:
   a. `current = prev1 + prev2`
   b. Update `prev2 = prev1`, `prev1 = current`.
3. Return `prev1`.

### Implementation
```python
def climbStairs(n: int) -> int:
    if n <= 1:
        return 1

    prev2, prev1 = 1, 1

    for i in range(2, n + 1):
        current = prev1 + prev2
        prev2 = prev1
        prev1 = current

    return prev1
```

### Complexity Analysis
- **Time Complexity**: O(n) -- single pass from 2 to n.
- **Space Complexity**: O(1) -- only two variables maintained.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| n = 1 | Return 1 | Only one way (single step) |
| n = 2 | Return 2 | Two ways: 1+1 or 2 |
| n = 45 | Return 1836311903 | Maximum constraint, must not overflow or TLE |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Fibonacci Number (LC #509) | Identical recurrence relation | Easy |
| Min Cost Climbing Stairs (LC #746) | Adds cost dimension to climbing | Easy |
| House Robber (LC #198) | Similar 1D DP recurrence | Medium |
| Decode Ways (LC #91) | Similar branching choices at each position | Medium |
| Unique Paths (LC #62) | 2D version of counting paths | Medium |
