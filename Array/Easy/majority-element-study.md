# Majority Element - Study Guide

## Problem Summary

> **One-liner**: Find the element that appears more than n/2 times in an array, guaranteed to exist.

## Key Intuition

The majority element appears more than half the time, so it cannot be "outvoted" by all other elements combined. The Boyer-Moore Voting Algorithm exploits this property by pairing majority-element occurrences against non-majority occurrences. Since the majority has strictly more than n/2 votes, it always survives the cancellation process. This gives us a beautiful O(n) time, O(1) space solution.

## Approaches Overview

| Approach | Time | Space | Pros | Cons |
|---|---|---|---|---|
| Hash Map Counting | O(n) | O(n) | Simple, intuitive | Extra space |
| Sorting | O(n log n) | O(1)* | Simple, no extra space if in-place | Slower, modifies array |
| Boyer-Moore Voting | O(n) | O(1) | Optimal time and space | Less intuitive |

## Approach 1: Hash Map Counting

### Core Idea

Count occurrences of each element. Return the one with count > n/2.

### Code

```python
from collections import Counter

def majorityElement(nums: list[int]) -> int:
    counts = Counter(nums)
    return max(counts.keys(), key=counts.get)
```

### Walkthrough

Given `nums = [2, 2, 1, 1, 1, 2, 2]`:

| Step | Element | Counts |
|---|---|---|
| 1 | 2 | `{2: 1}` |
| 2 | 2 | `{2: 2}` |
| 3 | 1 | `{2: 2, 1: 1}` |
| 4 | 1 | `{2: 2, 1: 2}` |
| 5 | 1 | `{2: 2, 1: 3}` |
| 6 | 2 | `{2: 3, 1: 3}` |
| 7 | 2 | `{2: 4, 1: 3}` |

Result: `2` has count 4 > 7//2 = 3. Return `2`.

## Approach 2: Boyer-Moore Voting Algorithm (Optimal)

### Core Idea

Maintain a candidate and a count. When count drops to zero, pick a new candidate. The majority element will always be the final candidate because it cannot be fully cancelled out.

### Code

```python
def majorityElement(nums: list[int]) -> int:
    candidate = None
    count = 0

    for num in nums:
        if count == 0:
            candidate = num
        count += 1 if num == candidate else -1

    return candidate
```

### Walkthrough

Given `nums = [2, 2, 1, 1, 1, 2, 2]`:

| Step | num | candidate | count (before) | Action | count (after) |
|---|---|---|---|---|---|
| 1 | 2 | 2 | 0 | count=0, pick 2, +1 | 1 |
| 2 | 2 | 2 | 1 | match, +1 | 2 |
| 3 | 1 | 2 | 2 | no match, -1 | 1 |
| 4 | 1 | 2 | 1 | no match, -1 | 0 |
| 5 | 1 | 1 | 0 | count=0, pick 1, +1 | 1 |
| 6 | 2 | 1 | 1 | no match, -1 | 0 |
| 7 | 2 | 2 | 0 | count=0, pick 2, +1 | 1 |

Final candidate: `2`. Return `2`.

## Complexity Analysis

| Approach | Time | Space | Notes |
|---|---|---|---|
| Hash Map | O(n) | O(n) | Simple but uses extra memory |
| Sorting | O(n log n) | O(1) | Element at index n//2 in sorted array |
| Boyer-Moore | O(n) | O(1) | Optimal -- answers the follow-up challenge |

## Common Mistakes

1. **Forgetting the count == 0 reset**: The algorithm only works if you reset the candidate when count hits zero.
2. **Not trusting the guarantee**: The problem guarantees a majority element exists. If it did not, you would need a second pass to verify the candidate.
3. **Confusing n/2 with n/3**: Majority Element II (n/3) requires tracking two candidates, not one.
4. **Incrementing before checking**: The order matters -- check if count is zero first, then update the count.

## Interview Tips

- **Lead with hash map**: Start by explaining the hash map approach as a clear baseline, then transition to Boyer-Moore as the optimization.
- **Explain the "voting" analogy**: Describe it as an election where votes cancel out. The majority party always wins because they have more than half the seats.
- **Know the follow-up**: Be prepared for "what if majority element is not guaranteed?" Answer: add a second verification pass over the array.
- **Connect to Majority Element II**: Show you know the generalization for n/3 using two candidates.

## Mnemonics / Memory Aids

- **"Survivor of cancellation"**: The majority element is the last one standing after all cancellations because it has more occurrences than all others combined.
- **"Middle of sorted"**: In a sorted array, the majority element always sits at the midpoint (`nums[n//2]`).
- **"Count to zero, start fresh"**: When the count hits zero, forget the old candidate and start fresh with the current element.

## Self-Assessment Checklist

- [ ] Can I implement Boyer-Moore Voting from memory?
- [ ] Can I explain why the final candidate is guaranteed to be the majority element?
- [ ] Can I trace through the algorithm on a sample input?
- [ ] Do I know what changes if the majority element is not guaranteed to exist?
- [ ] Can I extend the algorithm to Majority Element II (elements appearing > n/3 times)?
- [ ] Can I articulate why sorting gives the answer at index n//2?
