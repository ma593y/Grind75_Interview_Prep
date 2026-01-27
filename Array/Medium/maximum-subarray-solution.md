# Maximum Subarray

## Problem Information
- **Difficulty**: Medium
- **Category**: Array
- **Tags**: Array, Divide and Conquer, Dynamic Programming, Greedy
- **LeetCode Link**: https://leetcode.com/problems/maximum-subarray/

## Problem Statement

Given an integer array `nums`, find the subarray with the largest sum, and return its sum. A **subarray** is a contiguous non-empty sequence of elements within an array.

### Examples

**Example 1:**
```
Input: nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]
Output: 6
Explanation: The subarray [4, -1, 2, 1] has the largest sum 6.
```

**Example 2:**
```
Input: nums = [1]
Output: 1
Explanation: The subarray [1] has the largest sum 1.
```

**Example 3:**
```
Input: nums = [5, 4, -1, 7, 8]
Output: 23
Explanation: The subarray [5, 4, -1, 7, 8] (entire array) has the largest sum 23.
```

### Constraints

- `1 <= nums.length <= 10^5`
- `-10^4 <= nums[i] <= 10^4`

**Follow-up**: If you have figured out the O(n) solution, try coding another solution using the divide and conquer approach, which is more subtle.

## Core Concept / Pattern

This is the classic problem solved by **Kadane's Algorithm**. The key insight is a greedy/dynamic programming observation: at each position, the maximum subarray ending here is either the current element alone, or the current element added to the maximum subarray ending at the previous position. If the running sum becomes negative, it is better to start a new subarray from the current element, because a negative prefix can only decrease the sum.

Formally, define `dp[i]` as the maximum subarray sum ending at index `i`. Then `dp[i] = max(nums[i], dp[i-1] + nums[i])`. The answer is `max(dp[0], dp[1], ..., dp[n-1])`. Kadane's algorithm implements this without an explicit DP array.

## Approach 1: Brute Force

### Intuition

Try every possible subarray, compute its sum, and track the maximum. A subarray is defined by its start and end indices.

### Algorithm

1. For each starting index `i` from 0 to n-1:
2. For each ending index `j` from `i` to n-1:
3. Compute the sum of `nums[i..j]`.
4. Track the maximum sum seen.

### Implementation

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

### Complexity Analysis

- **Time Complexity**: O(n^2) -- Two nested loops, each iterating up to n times.
- **Space Complexity**: O(1) -- Only tracking two variables.

### Why We Can Do Better

We are recomputing overlapping subarray sums. Kadane's Algorithm eliminates this redundancy by maintaining a running sum and making a local decision at each element.

## Approach 2: Optimal Solution (Kadane's Algorithm)

### Intuition

At each index, we decide: should we extend the previous subarray, or start a new one from the current element? If the running sum so far is negative, extending it would only hurt us, so we restart. Otherwise, we extend. We keep track of the global maximum across all positions.

### Algorithm

1. Initialize `current_sum = nums[0]` and `max_sum = nums[0]`.
2. For each element from index 1 to n-1:
   - `current_sum = max(nums[i], current_sum + nums[i])`
   - `max_sum = max(max_sum, current_sum)`
3. Return `max_sum`.

### Implementation

```python
def maxSubArray(nums: list[int]) -> int:
    current_sum = nums[0]
    max_sum = nums[0]

    for i in range(1, len(nums)):
        current_sum = max(nums[i], current_sum + nums[i])
        max_sum = max(max_sum, current_sum)

    return max_sum
```

### Complexity Analysis

- **Time Complexity**: O(n) -- Single pass through the array.
- **Space Complexity**: O(1) -- Only two variables.

## Approach 3: Divide and Conquer

### Intuition

Split the array into two halves. The maximum subarray is either entirely in the left half, entirely in the right half, or it crosses the midpoint. Recursively solve the left and right halves, and compute the crossing sum by expanding from the midpoint in both directions.

### Implementation

```python
def maxSubArray(nums: list[int]) -> int:
    def helper(left: int, right: int) -> int:
        if left == right:
            return nums[left]

        mid = (left + right) // 2

        # Max subarray in left half
        left_max = helper(left, mid)
        # Max subarray in right half
        right_max = helper(mid + 1, right)

        # Max crossing subarray
        left_cross = float('-inf')
        current = 0
        for i in range(mid, left - 1, -1):
            current += nums[i]
            left_cross = max(left_cross, current)

        right_cross = float('-inf')
        current = 0
        for i in range(mid + 1, right + 1):
            current += nums[i]
            right_cross = max(right_cross, current)

        cross_max = left_cross + right_cross

        return max(left_max, right_max, cross_max)

    return helper(0, len(nums) - 1)
```

### Complexity Analysis

- **Time Complexity**: O(n log n) -- Recurrence T(n) = 2T(n/2) + O(n).
- **Space Complexity**: O(log n) -- Recursion stack depth.

## Edge Cases

| Edge Case | Input | Expected Output | Why It Matters |
|---|---|---|---|
| Single element | `[1]` | `1` | Base case |
| All negative | `[-3, -2, -1]` | `-1` | Must pick least negative |
| All positive | `[1, 2, 3]` | `6` | Entire array is the answer |
| Negative then positive | `[-1, 5]` | `5` | Restart is better than extending |
| Single negative | `[-1]` | `-1` | Must handle negative result |

## Related Problems

| Problem | Difficulty | How It Relates |
|---|---|---|
| [Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray/) | Medium | Similar DP pattern but with multiplication |
| [Maximum Sum Circular Subarray](https://leetcode.com/problems/maximum-sum-circular-subarray/) | Medium | Kadane's with wrap-around consideration |
| [Longest Turbulent Subarray](https://leetcode.com/problems/longest-turbulent-subarray/) | Medium | Similar "extend or restart" decision |
| [Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/) | Easy | Can be modeled as maximum subarray of differences |
| [Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/) | Medium | Prefix sums on subarrays |
