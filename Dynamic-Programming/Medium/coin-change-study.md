# Coin Change - Study Guide

## Problem Summary
- **Difficulty**: Medium
- **Category**: Dynamic Programming
- **Tags**: Array, Dynamic Programming, Breadth-First Search
- **LeetCode Link**: https://leetcode.com/problems/coin-change/

> **One-liner**: Find the minimum number of coins (with unlimited supply) needed to make a target amount, or return -1 if impossible.

## Key Intuition
For any amount `a`, the minimum coins needed is `1 + min(dp[a - coin])` across all coin denominations. By building from amount 0 upward, each subproblem is solved exactly once. This is the unbounded knapsack pattern applied to minimization.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Brute Force Recursion | O(S^n) | O(S) | Exponential, repeated subproblems |
| Top-Down Memoization | O(S * n) | O(S) | Recursive with cache |
| Bottom-Up DP | O(S * n) | O(S) | Iterative, optimal |

## Approach 1: Brute Force Recursion
### Core Idea
Try every coin denomination, recursively solve for the remaining amount, and take the minimum.

### Code
```python
def coinChange(coins: list[int], amount: int) -> int:
    def helper(remaining):
        if remaining == 0:
            return 0
        if remaining < 0:
            return float('inf')

        min_coins = float('inf')
        for coin in coins:
            result = helper(remaining - coin)
            min_coins = min(min_coins, result + 1)

        return min_coins

    result = helper(amount)
    return result if result != float('inf') else -1
```

### Walkthrough
For `coins = [1, 5]`, `amount = 6`:
- helper(6) tries coin 1 -> helper(5), coin 5 -> helper(1)
- helper(5) tries coin 5 -> helper(0) = 0, so returns 1
- helper(1) tries coin 1 -> helper(0) = 0, so returns 1
- helper(6): min(helper(5)+1, helper(1)+1) = min(2, 2) = 2

## Approach 2: Bottom-Up DP (Optimal)
### Core Idea
Build a `dp` array where `dp[i]` = minimum coins for amount `i`. Initialize with an impossible value, set `dp[0] = 0`, and fill left to right.

### Code
```python
def coinChange(coins: list[int], amount: int) -> int:
    dp = [amount + 1] * (amount + 1)
    dp[0] = 0

    for i in range(1, amount + 1):
        for coin in coins:
            if coin <= i:
                dp[i] = min(dp[i], dp[i - coin] + 1)

    return dp[amount] if dp[amount] <= amount else -1
```

### Walkthrough
For `coins = [1, 5]`, `amount = 7`:
```
dp[0] = 0
dp[1] = dp[0]+1 = 1         (use coin 1)
dp[2] = dp[1]+1 = 2         (use coin 1)
dp[3] = dp[2]+1 = 3         (use coin 1)
dp[4] = dp[3]+1 = 4         (use coin 1)
dp[5] = min(dp[4]+1, dp[0]+1) = min(5, 1) = 1  (use coin 5)
dp[6] = min(dp[5]+1, dp[1]+1) = min(2, 2) = 2  (use coin 5 or coin 1)
dp[7] = min(dp[6]+1, dp[2]+1) = min(3, 3) = 3  (use coin 1 or coin 5)
```
Result: 3 coins (5 + 1 + 1)

## Complexity Analysis
| Approach | Time | Space | Explanation |
|----------|------|-------|-------------|
| Brute Force | O(S^n) | O(S) | Exponential branching |
| Bottom-Up DP | O(S * n) | O(S) | S = amount, n = number of coins |

## Common Mistakes
1. **Greedy does not work**: Taking the largest coin first does not guarantee the minimum count (e.g., coins=[1,3,4], amount=6: greedy gives 4+1+1=3 coins, but 3+3=2 coins is better).
2. **Wrong initialization**: Using 0 or infinity instead of `amount + 1`. Using `float('inf')` works but `amount + 1` is cleaner for the final check.
3. **Forgetting dp[0] = 0**: The base case is essential -- zero coins needed for amount zero.
4. **Not checking if solution exists**: Must return -1 when `dp[amount]` still holds the impossible sentinel value.

## Interview Tips
- **How to communicate**: Start by noting that greedy fails, then explain the DP recurrence: "for each amount, the answer is 1 plus the minimum answer for amount minus each coin."
- **Clarifying questions to ask**: Can amount be 0? Are all coin values positive? Can coin values be larger than amount?
- **Optimization path**: "Recursive solution has exponential time. Memoization brings it to O(S*n). Bottom-up DP avoids recursion overhead."
- **Follow-up variations**: Coin Change II (count combinations), coins with limited supply, minimum coins with exact change constraint.

## Mnemonics / Memory Aids
- **"dp[i] = 1 + min(dp[i - coin])"**: The core recurrence. Each amount builds on a smaller solved amount.
- **"Fill from zero"**: Start at dp[0]=0 and work up to dp[amount].
- **"Greedy fails, DP prevails"**: Remember that the greedy approach (always pick largest coin) does not always give the optimal answer.

## Self-Assessment Checklist
- [ ] Can I explain why greedy does not work for this problem?
- [ ] Can I write the bottom-up DP solution from memory?
- [ ] Can I trace through an example step by step?
- [ ] Can I handle the impossible case correctly?
- [ ] Can I extend this to count combinations (Coin Change II)?
