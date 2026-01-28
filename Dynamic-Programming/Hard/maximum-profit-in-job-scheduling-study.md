# Maximum Profit in Job Scheduling - Study Guide

## Problem Summary
- **Difficulty**: Hard
- **Category**: Dynamic Programming
- **Tags**: Array, Binary Search, Dynamic Programming, Sorting
- **LeetCode Link**: https://leetcode.com/problems/maximum-profit-in-job-scheduling/

> **One-liner**: Find maximum profit from non-overlapping jobs using DP with binary search for efficient lookups.

## Key Intuition
Sort jobs by end time. For each job, you either skip it or take it. If you take it, you need to find the latest non-overlapping job efficiently (binary search). The DP recurrence is: `dp[i] = max(dp[i-1], profit[i] + dp[last_compatible])`.

## Approaches Overview

| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Recursive + Memo | O(n log n) | O(n) | Intuitive but recursion overhead |
| Bottom-Up DP | O(n log n) | O(n) | Better cache performance |

## Approach 1: Recursive + Memoization

### Core Idea
For each job, choose to skip or take. If taking, binary search for last compatible job.

### Code
```python
def jobScheduling(self, startTime, endTime, profit):
    import bisect
    jobs = sorted(zip(endTime, startTime, profit))
    end_times = [j[0] for j in jobs]
    memo = {}

    def dp(i):
        if i >= len(jobs):
            return 0
        if i in memo:
            return memo[i]
        skip = dp(i + 1)
        end, start, prof = jobs[i]
        j = bisect.bisect_right(end_times, start, 0, i) - 1
        take = prof + (dp(j + 1) if j >= 0 else 0)
        memo[i] = max(skip, take)
        return memo[i]

    return dp(0)
```

### Walkthrough
Jobs sorted by end: [(3,1,50), (4,2,10), (5,3,40), (6,3,70)]
- dp(0): skip=dp(1) or take=50+0=50
- Continue recursively, memoizing results

## Approach 2: Bottom-Up DP (Optimal)

### Core Idea
Process jobs in end-time order. dp[i] = max profit using first i jobs.

### Code
```python
def jobScheduling(self, startTime, endTime, profit):
    import bisect
    jobs = sorted(zip(endTime, startTime, profit))
    n = len(jobs)
    dp = [0] * (n + 1)
    end_times = [j[0] for j in jobs]

    for i in range(1, n + 1):
        end, start, prof = jobs[i - 1]
        j = bisect.bisect_right(end_times, start, 0, i - 1)
        dp[i] = max(dp[i - 1], prof + dp[j])

    return dp[n]
```

### Walkthrough
Jobs: [(3,1,50), (4,2,10), (5,3,40), (6,3,70)]
- i=1: job (3,1,50), no prior compatible, dp[1]=max(0, 50)=50
- i=2: job (4,2,10), prior: j=0, dp[2]=max(50, 10+0)=50
- i=3: job (5,3,40), prior: j=1, dp[3]=max(50, 40+50)=90
- i=4: job (6,3,70), prior: j=1, dp[4]=max(90, 70+50)=120

## Complexity Analysis
- **Time**: O(n log n) -- sorting + n binary searches
- **Space**: O(n) -- DP array and job storage

## Common Mistakes
1. **Wrong binary search bounds**: Must search only among jobs before current
2. **Off-by-one in bisect**: `bisect_right` returns insertion point, may need adjustment
3. **Not sorting by end time**: Essential for the DP to work correctly
4. **Forgetting edge case**: When no compatible prior job exists

## Interview Tips
- **How to communicate**: "This is weighted job scheduling. I'll sort by end time, then use DP with binary search to find compatible jobs."
- **Clarifying questions to ask**: "Can jobs have the same start/end time? Is profit always positive?"
- **Optimization path**: Explain brute force O(2^n) → recursive O(n²) → binary search O(n log n)
- **Follow-up variations**: "What if we need to return the actual jobs selected?"

## Mnemonics / Memory Aids
**"End, Search, Max"**:
1. Sort by **End** time
2. Binary **Search** for compatible job
3. **Max** of skip vs take

**Key recurrence**: `dp[i] = max(skip_current, take_current + best_compatible)`

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?
