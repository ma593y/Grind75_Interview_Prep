# Climbing Stairs - Study Guide

## Problem Summary
- **Difficulty**: Easy
- **Category**: Dynamic Programming
- **Tags**: Math, Dynamic Programming, Memoization
- **LeetCode Link**: https://leetcode.com/problems/climbing-stairs/

> **One-liner**: Count the number of distinct ways to climb `n` stairs when you can take 1 or 2 steps at a time (Fibonacci in disguise).

## Key Intuition
To reach step `n`, you must have come from step `n-1` (took 1 step) or step `n-2` (took 2 steps). So the total ways to reach step `n` is the sum of ways to reach those two preceding steps. This is exactly the Fibonacci recurrence: `f(n) = f(n-1) + f(n-2)`.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Naive Recursion | O(2^n) | O(n) | Simple but exponential due to recomputation |
| Memoized Recursion | O(n) | O(n) | Eliminates recomputation with cache |
| Bottom-Up DP (optimized) | O(n) | O(1) | Optimal -- iterative with two variables |

## Approach 1: Naive Recursion
### Core Idea
Recursively explore both choices (1 step or 2 steps) and sum all paths that reach the top.

### Code
```python
def climbStairs(n: int) -> int:
    if n <= 1:
        return 1
    return climbStairs(n - 1) + climbStairs(n - 2)
```

### Walkthrough
For `n = 4`:
```
                    f(4)
                  /      \
              f(3)        f(2)
             /    \       /   \
          f(2)   f(1)  f(1)  f(0)
         /   \     1     1     1
      f(1)  f(0)
        1     1
```
f(4) = f(3) + f(2) = 3 + 2 = 5

## Approach 2: Bottom-Up DP with Space Optimization (Optimal)
### Core Idea
Since each step only depends on the two previous values, iterate from the bottom up maintaining just two variables.

### Code
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

### Walkthrough
For `n = 5`:
- Start: prev2=1, prev1=1
- i=2: current=2, prev2=1, prev1=2
- i=3: current=3, prev2=2, prev1=3
- i=4: current=5, prev2=3, prev1=5
- i=5: current=8, prev2=5, prev1=8
- Return 8

## Complexity Analysis
| Approach | Time | Space | Explanation |
|----------|------|-------|-------------|
| Naive Recursion | O(2^n) | O(n) | Exponential branching, stack depth n |
| Memoized Recursion | O(n) | O(n) | Each subproblem solved once, cache + stack |
| Bottom-Up DP | O(n) | O(1) | Single pass, only two variables |

## Common Mistakes
1. **Forgetting base cases**: Not handling `n = 0` or `n = 1` before the loop.
2. **Off-by-one in loop range**: The loop should go from 2 to `n` inclusive.
3. **Wrong variable update order**: Updating `prev2` before using it to compute `current`.
4. **Not recognizing the Fibonacci pattern**: Spending too long trying to derive the recurrence from scratch.

## Interview Tips
- **How to communicate**: Immediately connect this to Fibonacci. Say "the number of ways to reach step n is the sum of ways to reach n-1 and n-2."
- **Clarifying questions to ask**: Can n be 0? Is n always positive? (Constraints say 1 <= n <= 45.)
- **Optimization path**: "Start with recursion O(2^n), add memoization for O(n) time/space, then optimize space to O(1) by keeping only two variables."
- **Follow-up variations**: What if you can take 1, 2, or 3 steps? (Generalize to `dp[i] = dp[i-1] + dp[i-2] + dp[i-3]`.) What about with costs (Min Cost Climbing Stairs)?

## Mnemonics / Memory Aids
- **"Fib Stairs"**: Climbing stairs = Fibonacci. f(n) = f(n-1) + f(n-2).
- **"Two vars, one loop"**: The optimal solution needs only `prev2`, `prev1`, and a `for` loop.
- **"From where did I come?"**: To reach step n, I came from n-1 or n-2. Sum both.

## Self-Assessment Checklist
- [ ] Can I identify this as a Fibonacci/DP problem within 1 minute?
- [ ] Can I write the O(1) space solution without notes?
- [ ] Can I explain the recurrence relation clearly?
- [ ] Can I generalize to k steps (1, 2, ..., k)?
- [ ] Can I trace through an example without errors?
