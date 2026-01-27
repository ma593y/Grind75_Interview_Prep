# Minimum Window Substring - Study Guide

## Problem Summary

> **One-liner**: Given strings `s` and `t`, find the smallest contiguous substring of `s` that contains all characters of `t` (including duplicates).

## Key Intuition

Use a **sliding window** with two pointers (`left` and `right`). Expand `right` to include characters until the window satisfies the requirement (contains all characters of `t`). Then shrink `left` to find the minimum valid window. The key optimization is tracking how many distinct characters have met their required frequency using a single counter, avoiding the need to compare full frequency maps on every step.

## Approaches Overview

| Approach | Time | Space | Key Idea |
|---|---|---|---|
| Brute Force (all substrings) | O(n^2 * m) | O(m) | Check every substring for containment |
| Sliding Window with Frequency Map | O(n + m) | O(m) | Expand/contract window, track satisfied character count |

Where `n = len(s)` and `m = len(t)`.

## Approach 1: Brute Force

### Core Idea

For every possible substring of `s`, check if it contains all characters of `t`. Track the shortest valid one.

### Code

```python
from collections import Counter

class Solution:
    def minWindow(self, s: str, t: str) -> str:
        if not t or not s:
            return ""

        t_count = Counter(t)
        best = ""

        for i in range(len(s)):
            for j in range(i + len(t), len(s) + 1):
                window = s[i:j]
                window_count = Counter(window)
                if all(window_count[c] >= t_count[c] for c in t_count):
                    if not best or len(window) < len(best):
                        best = window
                    break  # Expanding further from same i only makes it longer

        return best
```

### Walkthrough

For `s = "ADOBECODEBANC"`, `t = "ABC"`:
- Start at i=0: check substrings until one contains A, B, C. `"ADOBEC"` works (length 6).
- Continue for all starting positions. Eventually find `"BANC"` (length 4) as shortest.

This is O(n^2) at minimum, and O(n^2 * m) if we recount each time.

## Approach 2: Sliding Window (Optimal)

### Core Idea

Maintain a window `[left, right]` over `s`. Use a hash map `window_count` to track character frequencies in the current window. Use a variable `formed` to count how many unique characters in `t` have their required frequency met. Expand `right` until `formed == required`. Then shrink `left` while the window remains valid, updating the minimum window at each valid state.

### Code

```python
from collections import Counter, defaultdict

class Solution:
    def minWindow(self, s: str, t: str) -> str:
        if not t or not s:
            return ""

        t_count = Counter(t)
        required = len(t_count)  # Number of unique chars in t that must be present

        window_count = defaultdict(int)
        formed = 0  # How many unique chars in t are satisfied in current window

        left = 0
        min_len = float('inf')
        min_left = 0

        for right in range(len(s)):
            char = s[right]
            window_count[char] += 1

            # Check if this character's frequency requirement is now met
            if char in t_count and window_count[char] == t_count[char]:
                formed += 1

            # Contract the window from the left while it remains valid
            while formed == required:
                # Update answer
                window_size = right - left + 1
                if window_size < min_len:
                    min_len = window_size
                    min_left = left

                # Remove leftmost character
                left_char = s[left]
                window_count[left_char] -= 1
                if left_char in t_count and window_count[left_char] < t_count[left_char]:
                    formed -= 1
                left += 1

        return "" if min_len == float('inf') else s[min_left:min_left + min_len]
```

### Walkthrough

Example: `s = "ADOBECODEBANC"`, `t = "ABC"`

`t_count = {A:1, B:1, C:1}`, `required = 3`

| right | char | window_count (relevant) | formed | left | Action | Best |
|-------|------|------------------------|--------|------|--------|------|
| 0 | A | A:1 | 1 | 0 | Expand | - |
| 1 | D | A:1,D:1 | 1 | 0 | Expand | - |
| 2 | O | A:1,D:1,O:1 | 1 | 0 | Expand | - |
| 3 | B | A:1,B:1 | 2 | 0 | Expand | - |
| 4 | E | A:1,B:1,E:1 | 2 | 0 | Expand | - |
| 5 | C | A:1,B:1,C:1 | 3 | 0 | Valid! Record "ADOBEC" (len 6) | "ADOBEC" |
| | | | | | Contract: remove A, formed=2 | |
| 6 | O | ... | 2 | 1 | Expand | "ADOBEC" |
| 7 | D | ... | 2 | 1 | Expand | "ADOBEC" |
| 8 | E | ... | 2 | 1 | Expand | "ADOBEC" |
| 9 | B | A:1,B:1,C:1 | 3 | 1 | Valid! Contract until invalid | ... |
| 10 | A | ... | 3 | 5 | Valid! Keep contracting | "CODEBA" -> "ODEBA" ... |
| 11 | N | ... | ... | ... | Expand | ... |
| 12 | C | A:1,B:1,C:1 | 3 | 9 | Valid! Record "BANC" (len 4) | "BANC" |

Final answer: `"BANC"`

## Complexity Analysis

| Approach | Time | Space | Notes |
|---|---|---|---|
| Brute Force | O(n^2 * m) | O(m) | n^2 substrings, each checked against t |
| Sliding Window | O(n + m) | O(n + m) | Each char entered/left at most once; maps for s and t |

The sliding window is optimal because each character is added and removed from the window at most once, giving a total of at most `2n` operations.

## Common Mistakes

1. **Using `==` on full frequency maps each step.** Comparing entire maps is O(m) per step, degrading to O(nm). Use the `formed` counter instead.
2. **Forgetting to handle duplicates in `t`.** If `t = "AAB"`, you need `A:2, B:1`, not just presence of A and B.
3. **Off-by-one when extracting the result.** The window is `[left, right]` inclusive; the substring is `s[min_left:min_left + min_len]`.
4. **Not contracting aggressively enough.** The `while` loop (not `if`) must keep contracting as long as the window is valid.
5. **Incrementing `formed` incorrectly.** Only increment when `window_count[char]` **equals** `t_count[char]` (exactly meets requirement), not when it exceeds.

## Interview Tips

- **State the pattern upfront**: "This is a classic sliding window problem with a constraint to satisfy."
- **Explain the `formed` optimization**: This is what makes the solution O(n) instead of O(nm). Interviewers look for this.
- **Draw the window**: Sketch `s` with brackets showing `[left...right]` and mark which characters satisfy `t`.
- **Discuss the two-pointer invariant**: `right` only moves forward, `left` only moves forward. This guarantees O(n) total work.
- **Know the template**: This sliding window pattern (expand right, contract left while valid) applies to many problems. Mention Find All Anagrams, Longest Substring Without Repeating Characters, etc.

## Mnemonics / Memory Aids

- **"Expand, Satisfy, Contract, Record"** -- the four phases of each iteration.
- **"formed == required"** -- the condition that triggers contraction.
- **"Enter once, leave once"** -- why the sliding window is O(n). Each element is added and removed at most once.
- **"Count the unique, not the total"** -- `required` is the number of unique characters in `t`, not `len(t)`.

## Self-Assessment Checklist

- [ ] Can I write the sliding window solution from scratch in under 15 minutes?
- [ ] Can I explain why `formed` is incremented only when `window_count[char] == t_count[char]` (not `>=`)?
- [ ] Can I trace through `s = "ADOBECODEBANC"`, `t = "ABC"` showing the window at each step?
- [ ] Do I handle the case where `t` has duplicate characters (e.g., `t = "AA"`)?
- [ ] Can I explain why the time complexity is O(n + m) and not O(n * m)?
- [ ] Can I identify when to use sliding window vs. other techniques?
- [ ] Do I remember to return `""` when no valid window exists?
