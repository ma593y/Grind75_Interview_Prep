# Best Time to Buy and Sell Stock - Study Guide

## Problem Summary
- **Difficulty**: Easy
- **Category**: Array
- **Tags**: Array, Dynamic Programming, Greedy
- **LeetCode Link**: https://leetcode.com/problems/best-time-to-buy-and-sell-stock/

> **One-liner**: Find the maximum profit from a single buy-sell transaction where buy must come before sell.

## Key Intuition
To maximize profit, you want to buy at the lowest price and sell at the highest price that comes after it. As you scan left to right, keep track of the minimum price seen so far. At each position, the best you can do is sell at the current price minus that minimum. This single-pass greedy approach works because the optimal buy point for any sell day is always the global minimum up to that day.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Brute Force (all pairs) | O(n^2) | O(1) | Simple but too slow for large inputs |
| Single Pass (track min) | O(n) | O(1) | Optimal in both time and space |

## Approach 1: Brute Force
### Core Idea
Try every pair of (buy_day, sell_day) where sell_day > buy_day and return the maximum profit.

### Code
```python
def maxProfit(prices):
    max_profit = 0
    for i in range(len(prices)):
        for j in range(i + 1, len(prices)):
            profit = prices[j] - prices[i]
            max_profit = max(max_profit, profit)
    return max_profit
```

### Walkthrough
```
prices = [7, 1, 5, 3, 6, 4]

i=0 (buy@7): sell@1=-6, sell@5=-2, sell@3=-4, sell@6=-1, sell@4=-3 --> best=0
i=1 (buy@1): sell@5=4, sell@3=2, sell@6=5, sell@4=3 --> best=5
i=2 (buy@5): sell@3=-2, sell@6=1, sell@4=-1 --> best=5
...
Answer: 5
```

## Approach 2: Single Pass / Track Minimum (Optimal)
### Core Idea
Maintain the minimum price seen so far. At each day, compute the profit of selling today and update the maximum profit. This works because the best buy price for any sell day is always the lowest price seen before it.

### Code
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

### Walkthrough
```
prices = [7, 1, 5, 3, 6, 4]

price=7: min_price=7, max_profit=0
price=1: min_price=1, max_profit=0  (new min found)
price=5: profit=5-1=4, max_profit=4
price=3: profit=3-1=2, max_profit=4
price=6: profit=6-1=5, max_profit=5
price=4: profit=4-1=3, max_profit=5

Answer: 5
```

## Complexity Analysis
- **Time**: O(n) -- Single traversal of the array with constant work per element.
- **Space**: O(1) -- Only two variables (`min_price` and `max_profit`) regardless of input size.

## Common Mistakes
1. **Allowing sell before buy**: Forgetting the constraint that the sell day must come after the buy day. The left-to-right scan with running minimum naturally enforces this.
2. **Returning negative profit**: When prices only decrease, the answer should be `0`, not a negative number. Initializing `max_profit = 0` handles this.
3. **Updating min and profit in wrong order**: It is fine to update min_price first -- if the current price is a new minimum, we would not want to sell on the same day anyway (profit would be 0).
4. **Overcomplicating with DP**: This problem can be solved with full DP arrays but that is unnecessary. Two variables suffice.

## Interview Tips
- **How to communicate**: "I need to find the maximum difference prices[j] - prices[i] where j > i. The key observation is that for each selling day, the best buying day is simply the minimum price seen before it."
- **Clarifying questions to ask**:
  - Can I only make one transaction? (Yes for this version)
  - What if no profit is possible? (Return 0)
  - Can prices be zero? (Yes, per constraints)
  - Is the array guaranteed to be non-empty? (Yes, length >= 1)
- **Optimization path**: "Brute force checks all O(n^2) pairs. But I notice that for any sell day, the optimal buy day is the minimum price before it. So I can track the running minimum in a single pass, giving O(n) time and O(1) space."
- **Follow-up variations**:
  - What if you can make multiple transactions? (Greedy: sum all upward moves)
  - What if at most k transactions? (DP with k states)
  - What if there is a cooldown? (DP with states for hold/sold/cooldown)

## Mnemonics / Memory Aids
- **"Track the valley, sell at the peak"**: Always remember the lowest point and calculate profit from there.
- **"min so far, max profit so far"**: Just two variables, both updated greedily.
- **"Left to right guarantees order"**: Scanning left to right naturally enforces the buy-before-sell constraint.

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?
