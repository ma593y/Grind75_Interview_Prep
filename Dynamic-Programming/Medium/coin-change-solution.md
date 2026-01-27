# Coin Change

## Problem Information
- **Difficulty**: Medium
- **Category**: Dynamic Programming
- **Tags**: Array, Dynamic Programming, Breadth-First Search
- **LeetCode Link**: https://leetcode.com/problems/coin-change/

## Problem Statement
You are given an integer array `coins` representing coins of different denominations and an integer `amount` representing a total amount of money. Return the fewest number of coins that you need to make up that amount. If that amount of money cannot be made up by any combination of the coins, return `-1`.

You may assume that you have an infinite number of each kind of coin.

### Examples
**Example 1:**
```
Input: coins = [1, 5, 10, 25], amount = 30
Output: 2
Explanation: 5 + 25 = 30, which uses 2 coins.
```

**Example 2:**
```
Input: coins = [2], amount = 3
Output: -1
Explanation: 3 cannot be made with only coins of denomination 2.
```

**Example 3:**
```
Input: coins = [1], amount = 0
Output: 0
Explanation: No coins needed for amount 0.
```

### Constraints
- `1 <= coins.length <= 12`
- `1 <= coins[i] <= 2^31 - 1`
- `0 <= amount <= 10^4`

## Core Concept / Pattern
This is the classic unbounded knapsack / minimum coin problem. For each amount `a`, the minimum coins needed is `1 + min(dp[a - coin])` for all valid coins. We build the solution bottom-up from amount 0 to the target amount.

## Approach 1: Brute Force (Recursive)
### Intuition
Try every coin at each step and recursively solve for the remaining amount. Return the minimum across all choices.

### Algorithm
1. Base case: if `amount == 0`, return 0. If `amount < 0`, return -1.
2. For each coin, recursively solve `coinChange(amount - coin)`.
3. Track the minimum result across all coin choices.
4. If no coin leads to a valid solution, return -1.

### Implementation
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

### Complexity Analysis
- **Time Complexity**: O(S^n) where S is amount and n is number of coin denominations -- exponential branching.
- **Space Complexity**: O(S) -- recursion stack depth.

### Why We Can Do Better
Many subproblems overlap. For example, computing the answer for amount 5 is done repeatedly in different branches. Memoization or bottom-up DP eliminates this redundancy.

## Approach 2: Optimal Solution (Bottom-Up DP)
### Intuition
Build a table `dp` where `dp[i]` is the minimum coins needed to make amount `i`. For each amount from 1 to the target, try every coin and take the minimum.

### Algorithm
1. Create `dp` array of size `amount + 1`, initialized to `amount + 1` (an impossible value since you can never need more than `amount` coins with a denomination-1 coin).
2. Set `dp[0] = 0`.
3. For each amount `i` from 1 to `amount`:
   - For each coin in `coins`:
     - If `coin <= i`, update `dp[i] = min(dp[i], dp[i - coin] + 1)`.
4. Return `dp[amount]` if it is not `amount + 1`, else return -1.

### Implementation
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

### Complexity Analysis
- **Time Complexity**: O(S * n) where S is the amount and n is the number of coins.
- **Space Complexity**: O(S) for the dp array.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| amount = 0 | Return 0 | No coins needed |
| Single coin that divides amount | Return amount / coin | Simple division case |
| Single coin that does not divide amount | Return -1 | Impossible case |
| coins = [1] | Return amount | Always possible, one coin at a time |
| Very large coin values | Coins > amount are ignored | Must handle gracefully |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Coin Change II (LC #518) | Count combinations instead of minimum | Medium |
| Perfect Squares (LC #279) | Same pattern with square numbers as "coins" | Medium |
| Climbing Stairs (LC #70) | Count ways with step sizes 1 and 2 | Easy |
| Minimum Cost for Tickets (LC #983) | Similar DP with different "coins" | Medium |
| Combination Sum IV (LC #377) | Count permutations summing to target | Medium |
