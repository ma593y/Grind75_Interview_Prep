# Maximum Subarray - Study Guide

## Problem Summary

> **One-liner**: Find the contiguous subarray within an array that has the largest sum.

## Key Intuition

At each position in the array, you face a binary decision: either extend the current subarray by including this element, or start a brand new subarray beginning at this element. The deciding factor is simple -- if the accumulated sum so far is negative, it would only drag down any future subarray, so discard it and start fresh. This greedy decision at each step, combined with tracking the global maximum, is the essence of Kadane's Algorithm.

## Approaches Overview

| Approach | Time | Space | Pros | Cons |
|---|---|---|---|---|
| Brute Force | O(n^2) | O(1) | Easy to understand | Too slow for large inputs |
| Divide and Conquer | O(n log n) | O(log n) | Good for understanding recursion | More complex, not optimal |
| Kadane's Algorithm | O(n) | O(1) | Optimal, elegant, simple | Less intuitive at first |

## Approach 1: Brute Force

### Core Idea

Enumerate all possible subarrays by trying every (start, end) pair and compute each sum. Track the maximum.

### Code

```python
def maxSubArray(nums: list[int]) -> int:
    max_sum = float('-inf')
    n = len(nums)
    for i in range(n):
        current_sum = 0
        for j in range(i, n):
            current_sum += nums[j]
            max_sum = max(max_sum, current_sum)
    return max_sum
```

### Walkthrough

Given `nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]`:

- Starting at index 0: sums are -2, -1, -4, 0, -1, 1, 2, -3, 1.
- Starting at index 3: sums are 4, 3, 5, **6**, 1, 5.
- Maximum across all starts: **6** (subarray `[4, -1, 2, 1]`).

## Approach 2: Kadane's Algorithm (Optimal)

### Core Idea

Maintain a running sum (`current_sum`). At each element, decide whether to extend the existing subarray or start fresh. Update the global maximum after each decision.

**Decision rule**: `current_sum = max(nums[i], current_sum + nums[i])`

If `current_sum + nums[i] < nums[i]`, that means `current_sum < 0`, so starting fresh is better.

### Code

```python
def maxSubArray(nums: list[int]) -> int:
    current_sum = nums[0]
    max_sum = nums[0]

    for i in range(1, len(nums)):
        current_sum = max(nums[i], current_sum + nums[i])
        max_sum = max(max_sum, current_sum)

    return max_sum
```

### Walkthrough

Given `nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]`:

| Index | nums[i] | current_sum (before) | Decision | current_sum (after) | max_sum |
|---|---|---|---|---|---|
| 0 | -2 | -- | Initialize | -2 | -2 |
| 1 | 1 | -2 | max(1, -2+1) = max(1, -1) = 1 (restart) | 1 | 1 |
| 2 | -3 | 1 | max(-3, 1+(-3)) = max(-3, -2) = -2 (extend) | -2 | 1 |
| 3 | 4 | -2 | max(4, -2+4) = max(4, 2) = 4 (restart) | 4 | 4 |
| 4 | -1 | 4 | max(-1, 4+(-1)) = max(-1, 3) = 3 (extend) | 3 | 4 |
| 5 | 2 | 3 | max(2, 3+2) = max(2, 5) = 5 (extend) | 5 | 5 |
| 6 | 1 | 5 | max(1, 5+1) = max(1, 6) = 6 (extend) | 6 | **6** |
| 7 | -5 | 6 | max(-5, 6+(-5)) = max(-5, 1) = 1 (extend) | 1 | 6 |
| 8 | 4 | 1 | max(4, 1+4) = max(4, 5) = 5 (extend) | 5 | 6 |

Result: **6**

## Complexity Analysis

| Approach | Time | Space | Notes |
|---|---|---|---|
| Brute Force | O(n^2) | O(1) | Try all subarrays |
| Divide and Conquer | O(n log n) | O(log n) | Recursive splitting |
| Kadane's Algorithm | O(n) | O(1) | Single pass, optimal |

## Common Mistakes

1. **Initializing max_sum to 0**: If all elements are negative, the answer is negative. Initialize to `nums[0]` or `float('-inf')`.
2. **Not handling the "restart" case**: Simply accumulating without restarting when sum goes negative misses the optimal subarray.
3. **Returning current_sum instead of max_sum**: `current_sum` is the best subarray ending at the current position. `max_sum` is the global answer.
4. **Off-by-one in loop start**: If initializing `current_sum = nums[0]`, start the loop at index 1, not 0.
5. **Confusing subarray with subsequence**: A subarray must be contiguous. `[1, 3]` from `[1, 2, 3]` is a subsequence, not a subarray.

## Interview Tips

- **State the DP recurrence**: `dp[i] = max(nums[i], dp[i-1] + nums[i])`. This shows you understand the formal foundation.
- **Explain why greedy works**: The negative-sum prefix argument is clean and convincing.
- **Know the divide and conquer follow-up**: Interviewers may ask for it. The key is the crossing-subarray computation.
- **Track the actual subarray**: Be prepared to modify the algorithm to return the start and end indices, not just the sum.
- **Connect to stock problems**: Maximum subarray of daily price differences is equivalent to the best-time-to-buy-and-sell-stock problem.

## Mnemonics / Memory Aids

- **"Extend or Restart"**: At each element, ask: "Am I better off extending or restarting?" If the running sum is negative, restart.
- **"Negative baggage"**: A negative running sum is like carrying baggage -- drop it and start fresh.
- **"Two maxes"**: Always maintain two values: `current_sum` (local best ending here) and `max_sum` (global best seen so far).

## Self-Assessment Checklist

- [ ] Can I write Kadane's Algorithm from memory in under 2 minutes?
- [ ] Can I trace through the algorithm on an array with all negative numbers?
- [ ] Can I explain the DP recurrence relation formally?
- [ ] Can I modify the algorithm to return the actual subarray (start and end indices)?
- [ ] Can I implement the divide and conquer approach?
- [ ] Do I understand the connection to the stock buy/sell problem?
