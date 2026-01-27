# First Bad Version - Study Guide

## Problem Summary
- **Difficulty**: Easy
- **Category**: Binary Search
- **Tags**: Binary Search, Interactive
- **LeetCode Link**: https://leetcode.com/problems/first-bad-version/

> **One-liner**: Find the first version where `isBadVersion()` returns true in a sequence of n versions.

## Key Intuition
The version history is a monotonic boolean sequence: `[false, ..., false, true, ..., true]`. We need the leftmost `true`. Binary search on a boolean predicate finds this transition point in O(log n) calls. The key difference from standard binary search is that when `isBadVersion(mid)` is true, we keep `mid` as a candidate (`right = mid`) rather than excluding it.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Linear Scan | O(n) | O(1) | Simple but too many API calls |
| Binary Search | O(log n) | O(1) | Optimal -- minimizes API calls |

## Approach 1: Linear Scan
### Core Idea
Check every version from 1 to n, return the first one where `isBadVersion` is true.

### Code
```python
def firstBadVersion(n: int) -> int:
    for i in range(1, n + 1):
        if isBadVersion(i):
            return i
    return n
```

### Walkthrough
For n=5, bad=4:
- isBadVersion(1) -> false
- isBadVersion(2) -> false
- isBadVersion(3) -> false
- isBadVersion(4) -> true, return 4

## Approach 2: Binary Search (Optimal)
### Core Idea
Use `left < right` loop. If mid is bad, shrink right to mid (mid could be the answer). If mid is good, move left past mid. When left == right, that is the answer.

### Code
```python
def firstBadVersion(n: int) -> int:
    left, right = 1, n

    while left < right:
        mid = left + (right - left) // 2

        if isBadVersion(mid):
            right = mid
        else:
            left = mid + 1

    return left
```

### Walkthrough
For n=5, bad=4:
- left=1, right=5, mid=3: isBadVersion(3)=false, left=4
- left=4, right=5, mid=4: isBadVersion(4)=true, right=4
- left==right==4, return 4

## Complexity Analysis
| Approach | Time | Space | Explanation |
|----------|------|-------|-------------|
| Linear Scan | O(n) | O(1) | Checks each version |
| Binary Search | O(log n) | O(1) | Halves search space per step |

## Common Mistakes
1. **Using `left <= right` with `right = mid`**: This can cause an infinite loop. Use `left < right` when setting `right = mid`.
2. **Setting `right = mid - 1` when mid is bad**: This skips mid, which might be the first bad version.
3. **Integer overflow**: With n up to 2^31 - 1, `(left + right)` can overflow in some languages. Use `left + (right - left) // 2`.
4. **Confusing this with standard binary search template**: This is the "leftmost boundary" variant, not the "exact match" variant.

## Interview Tips
- **How to communicate**: "This is a monotonic predicate -- once a version is bad, all subsequent versions are bad. I can binary search for the transition point."
- **Clarifying questions to ask**: Is there always at least one bad version? Can n be very large (overflow concern)? Is the API call expensive (motivates minimizing calls)?
- **Optimization path**: "Linear scan is O(n) API calls. Since the bad/good sequence is monotonic, binary search finds the boundary in O(log n) calls."
- **Follow-up variations**: What if there could be multiple bad ranges (non-monotonic)? What if the API is unreliable and sometimes gives wrong answers?

## Mnemonics / Memory Aids
- **"Left Less-Than Right"**: Use `while left < right` (not `<=`) for boundary search.
- **"Bad? Keep it. Good? Skip it."**: If mid is bad, `right = mid` (keep). If good, `left = mid + 1` (skip).
- **"Converge to one"**: The loop ends when left == right, which is the answer.

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?
