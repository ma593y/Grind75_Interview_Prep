# Maximum Profit in Job Scheduling

## Problem Information
- **Difficulty**: Hard
- **Category**: Dynamic Programming
- **Tags**: Array, Binary Search, Dynamic Programming, Sorting
- **LeetCode Link**: https://leetcode.com/problems/maximum-profit-in-job-scheduling/

## Problem Statement
We have `n` jobs, where every job is scheduled to be done from `startTime[i]` to `endTime[i]`, obtaining a profit of `profit[i]`.

You're given the `startTime`, `endTime` and `profit` arrays. Return the maximum profit you can take such that there are no two jobs in the subset with overlapping time range.

If you choose a job that ends at time `X` you will be able to start another job that starts at time `X`.

### Examples
**Example 1:**
```
Input: startTime = [1,2,3,3], endTime = [3,4,5,6], profit = [50,10,40,70]
Output: 120
Explanation: The subset chosen is the first and fourth job.
Time range [1-3]+[3-6], we get profit of 50 + 70 = 120.
```

**Example 2:**
```
Input: startTime = [1,2,3,4,6], endTime = [3,5,10,6,9], profit = [20,20,100,70,60]
Output: 150
Explanation: The subset chosen is the first, fourth and fifth job.
Total profit is 20 + 70 + 60 = 150.
```

### Constraints
- `1 <= startTime.length == endTime.length == profit.length <= 5 * 10^4`
- `1 <= startTime[i] < endTime[i] <= 10^9`
- `1 <= profit[i] <= 10^4`

## Core Concept / Pattern
This is a classic weighted job scheduling problem. The key insight is to sort jobs by end time, then for each job, decide whether to include it or not. If included, we need to find the latest non-overlapping job using binary search. This combines DP with binary search for efficiency.

## Approach 1: Recursive with Memoization

### Intuition
For each job (sorted by end time), we have two choices: skip it or take it. If we take it, we add its profit and find the next non-overlapping job.

### Algorithm
1. Combine and sort jobs by end time
2. For each position, either skip current job or take it
3. If taking, binary search for the latest job that ends before current starts
4. Use memoization to avoid recomputation

### Implementation
```python
class Solution:
    def jobScheduling(self, startTime: list[int], endTime: list[int], profit: list[int]) -> int:
        import bisect

        jobs = sorted(zip(endTime, startTime, profit))
        n = len(jobs)

        # Extract end times for binary search
        end_times = [job[0] for job in jobs]

        memo = {}

        def dp(i):
            if i >= n:
                return 0
            if i in memo:
                return memo[i]

            # Option 1: Skip current job
            skip = dp(i + 1)

            # Option 2: Take current job
            end, start, prof = jobs[i]
            # Find rightmost job that ends <= current start
            j = bisect.bisect_right(end_times, start, 0, i) - 1
            take = prof + (dp(j + 1) if j >= 0 else 0)

            memo[i] = max(skip, take)
            return memo[i]

        return dp(0)
```

### Complexity Analysis
- **Time Complexity**: O(n log n) - sorting + n states × O(log n) binary search
- **Space Complexity**: O(n) - memoization table

### Why We Can Do Better
This is already efficient. We can also use bottom-up DP for better cache performance.

## Approach 2: Bottom-Up DP with Binary Search

### Intuition
Process jobs in order of end time. For each job, the maximum profit is either: (1) not taking this job (same as previous), or (2) taking this job + max profit from compatible jobs.

### Algorithm
1. Sort jobs by end time
2. dp[i] = maximum profit considering first i jobs
3. For job i: dp[i] = max(dp[i-1], profit[i] + dp[j]) where j is latest non-overlapping job
4. Use binary search to find j efficiently

### Implementation
```python
class Solution:
    def jobScheduling(self, startTime: list[int], endTime: list[int], profit: list[int]) -> int:
        import bisect

        jobs = sorted(zip(endTime, startTime, profit))
        n = len(jobs)

        # dp[i] = max profit considering first i jobs (1-indexed)
        dp = [0] * (n + 1)
        end_times = [job[0] for job in jobs]

        for i in range(1, n + 1):
            end, start, prof = jobs[i - 1]

            # Find latest job that ends <= start (binary search)
            j = bisect.bisect_right(end_times, start, 0, i - 1)

            # Max of: skip this job OR take this job + best compatible
            dp[i] = max(dp[i - 1], prof + dp[j])

        return dp[n]
```

### Complexity Analysis
- **Time Complexity**: O(n log n) - sorting + n iterations × O(log n) binary search
- **Space Complexity**: O(n) - DP array

## Edge Cases

| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Single job | Return its profit | Base case |
| All overlapping | Return max single profit | Cannot combine |
| No overlapping | Sum all profits | Can take all |
| Same end times | Handle correctly | Binary search edge case |

## Related Problems

| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Non-overlapping Intervals | Similar interval scheduling | Medium |
| Meeting Rooms II | Interval scheduling | Medium |
| Weighted Job Scheduling | Classic version | Hard |
