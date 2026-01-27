# Best Time to Buy and Sell Stock

## Problem Information
- **Difficulty**: Easy
- **Category**: Array
- **Tags**: Array, Dynamic Programming, Greedy
- **LeetCode Link**: https://leetcode.com/problems/best-time-to-buy-and-sell-stock/

## Problem Statement
You are given an array `prices` where `prices[i]` is the price of a given stock on the `i`th day.

You want to maximize your profit by choosing a single day to buy one stock and choosing a different day in the future to sell that stock.

Return the maximum profit you can achieve from this transaction. If you cannot achieve any profit, return `0`.

### Examples
**Example 1:**
```
Input: prices = [7, 1, 5, 3, 6, 4]
Output: 5
Explanation: Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6 - 1 = 5.
Note that buying on day 2 and selling on day 1 is not allowed because you must buy before you sell.
```

**Example 2:**
```
Input: prices = [7, 6, 4, 3, 1]
Output: 0
Explanation: No transactions are done because prices only decrease. Maximum profit is 0.
```

### Constraints
- 1 <= prices.length <= 10^5
- 0 <= prices[i] <= 10^4

## Core Concept / Pattern
This problem tests the ability to track a running minimum while computing a running maximum difference. The fundamental insight is that for each day, the best profit achievable by selling on that day depends only on the minimum price seen so far. This is a classic example of a single-pass greedy algorithm.

It also connects to Kadane's algorithm if you think of it as finding the maximum subarray of daily price changes. Both perspectives yield the same O(n) solution.

## Approach 1: Brute Force

### Intuition
Check every possible pair of buy and sell days. For each buy day, look at all future sell days and compute the profit. Track the maximum profit seen.

### Algorithm
1. Initialize `max_profit = 0`.
2. For each day `i` (buy day), iterate through each day `j > i` (sell day).
3. Compute `profit = prices[j] - prices[i]`.
4. Update `max_profit = max(max_profit, profit)`.
5. Return `max_profit`.

### Implementation
```python
def maxProfit(prices):
    max_profit = 0
    for i in range(len(prices)):
        for j in range(i + 1, len(prices)):
            profit = prices[j] - prices[i]
            max_profit = max(max_profit, profit)
    return max_profit
```

### Complexity Analysis
- **Time Complexity**: O(n^2) -- Two nested loops over the prices array.
- **Space Complexity**: O(1) -- Only a few variables used.

### Why We Can Do Better
We do not need to compare every pair. If we track the minimum price seen so far, then for each day we only need one subtraction to know the best profit from selling on that day. This eliminates the inner loop entirely.

## Approach 2: Optimal Solution (Single Pass / Greedy)

### Intuition
As we scan left to right, we maintain the lowest price seen so far. At each day, the maximum profit from selling on that day is `current_price - min_price_so_far`. We update the global maximum profit accordingly.

### Algorithm
1. Initialize `min_price = infinity` and `max_profit = 0`.
2. Iterate through each price in the array.
3. If the current price is less than `min_price`, update `min_price`.
4. Otherwise, compute `profit = current_price - min_price` and update `max_profit` if this profit is larger.
5. Return `max_profit`.

### Implementation
```python
def maxProfit(prices):
    min_price = float('inf')
    max_profit = 0
    for price in prices:
        if price < min_price:
            min_price = price
        else:
            max_profit = max(max_profit, price - min_price)
    return max_profit
```

### Complexity Analysis
- **Time Complexity**: O(n) -- Single pass through the array.
- **Space Complexity**: O(1) -- Only two variables used.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Prices always decrease `[5,4,3,2,1]` | Return `0` | No profitable transaction exists |
| Single element `[5]` | Return `0` | Cannot buy and sell on the same day |
| Two elements, increasing `[1,5]` | Return `4` | Simplest profitable case |
| Two elements, decreasing `[5,1]` | Return `0` | Simplest non-profitable case |
| All same prices `[3,3,3,3]` | Return `0` | No profit possible |
| Minimum at the end `[3,2,1]` | Return `0` | Best buy day is last day, but cannot sell after |
| Multiple peaks and valleys | Return max single-transaction profit | Must find global best, not first profit |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Best Time to Buy and Sell Stock II (LeetCode #122) | Extension: unlimited transactions allowed | Medium |
| Best Time to Buy and Sell Stock III (LeetCode #123) | Extension: at most two transactions | Hard |
| Best Time to Buy and Sell Stock IV (LeetCode #188) | Extension: at most k transactions | Hard |
| Maximum Subarray (LeetCode #53) | Related: can be reformulated as max subarray of daily changes | Medium |
| Best Time to Buy and Sell Stock with Cooldown (LeetCode #309) | Extension: cooldown period between transactions | Medium |
