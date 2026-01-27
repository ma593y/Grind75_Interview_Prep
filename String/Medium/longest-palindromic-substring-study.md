# Longest Palindromic Substring - Study Guide

## Problem Summary

> **One-liner**: Given a string, find and return the longest contiguous substring that reads the same forwards and backwards.

## Key Intuition

Every palindrome has a **center**. For odd-length palindromes, the center is a single character; for even-length palindromes, the center is between two characters. There are `2n - 1` possible centers in a string of length `n`. By expanding outward from each center while characters match, you can find the longest palindrome in O(n^2) time with O(1) space.

## Approaches Overview

| Approach | Time | Space | Pros | Cons |
|---|---|---|---|---|
| Brute Force (all substrings) | O(n^3) | O(n) | Simple to understand | Far too slow for n=1000 |
| Dynamic Programming | O(n^2) | O(n^2) | Systematic, builds on subproblems | Uses extra memory for DP table |
| Expand Around Center | O(n^2) | O(1) | Optimal practical solution, minimal space | Worst case still quadratic |
| Manacher's Algorithm | O(n) | O(n) | Theoretically optimal | Complex to implement, rarely expected |

## Approach 1: Dynamic Programming

### Core Idea

Build a 2D boolean table `dp[i][j]` indicating whether `s[i..j]` is a palindrome. A substring `s[i..j]` is a palindrome if `s[i] == s[j]` and the inner substring `s[i+1..j-1]` is also a palindrome. Fill the table from shorter substrings to longer ones.

### Code

```python
class Solution:
    def longestPalindrome(self, s: str) -> str:
        n = len(s)
        if n < 2:
            return s

        dp = [[False] * n for _ in range(n)]
        start, max_len = 0, 1

        for i in range(n):
            dp[i][i] = True

        for i in range(n - 1):
            if s[i] == s[i + 1]:
                dp[i][i + 1] = True
                start, max_len = i, 2

        for length in range(3, n + 1):
            for i in range(n - length + 1):
                j = i + length - 1
                if s[i] == s[j] and dp[i + 1][j - 1]:
                    dp[i][j] = True
                    if length > max_len:
                        start, max_len = i, length

        return s[start:start + max_len]
```

### Walkthrough

Example: `s = "babad"`

Build the DP table (showing True entries only):

| i\j | 0(b) | 1(a) | 2(b) | 3(a) | 4(d) |
|-----|------|------|------|------|------|
| 0(b) | T | | T | | |
| 1(a) | | T | | T | |
| 2(b) | | | T | | |
| 3(a) | | | | T | |
| 4(d) | | | | | T |

- `dp[0][2] = True` because `s[0]=='b'==s[2]` and `dp[1][1]==True` -> palindrome "bab"
- `dp[1][3] = True` because `s[1]=='a'==s[3]` and `dp[2][2]==True` -> palindrome "aba"
- Longest has length 3: either "bab" or "aba".

## Approach 2: Expand Around Center (Optimal)

### Core Idea

Iterate through each of the `2n-1` possible palindrome centers. For each center, expand outward comparing characters. Track the longest palindrome found.

### Code

```python
class Solution:
    def longestPalindrome(self, s: str) -> str:
        n = len(s)
        if n < 2:
            return s

        start, max_len = 0, 1

        def expand(left: int, right: int) -> None:
            nonlocal start, max_len
            while left >= 0 and right < n and s[left] == s[right]:
                if right - left + 1 > max_len:
                    start = left
                    max_len = right - left + 1
                left -= 1
                right += 1

        for i in range(n):
            expand(i, i)      # Odd-length palindromes
            expand(i, i + 1)  # Even-length palindromes

        return s[start:start + max_len]
```

### Walkthrough

Example: `s = "cbbd"`

| Center | Type | Expansion | Palindrome Found | Length |
|--------|------|-----------|------------------|--------|
| i=0 (c) | Odd | "c" | "c" | 1 |
| i=0,1 (c,b) | Even | c != b, stop | -- | 0 |
| i=1 (b) | Odd | "b" | "b" | 1 |
| i=1,2 (b,b) | Even | s[1]==s[2], expand; s[0]!=s[3], stop | "bb" | 2 |
| i=2 (b) | Odd | "b" | "b" | 1 |
| i=2,3 (b,d) | Even | b != d, stop | -- | 0 |
| i=3 (d) | Odd | "d" | "d" | 1 |

Longest: `"bb"` with length 2.

## Complexity Analysis

| Approach | Time | Space | Notes |
|---|---|---|---|
| Brute Force | O(n^3) | O(n) | Check all O(n^2) substrings, each O(n) |
| Dynamic Programming | O(n^2) | O(n^2) | Fill n x n table |
| Expand Around Center | O(n^2) | O(1) | Best practical approach |
| Manacher's Algorithm | O(n) | O(n) | Theoretical best, complex to code |

## Common Mistakes

1. **Forgetting even-length palindromes.** Only expanding from single centers misses palindromes like `"bb"` or `"abba"`.
2. **Off-by-one in substring extraction.** After tracking `start` and `max_len`, the result is `s[start:start+max_len]`, not `s[start:max_len]`.
3. **Returning length instead of the substring itself.** The problem asks for the actual substring, not its length.
4. **DP table fill order.** You must fill by increasing substring length, not by row then column, or the inner subproblem may not be solved yet.
5. **Not handling single-character input.** A string of length 1 is itself a palindrome.

## Interview Tips

- **Start with the center-expansion approach.** It is the best balance of simplicity, time complexity, and space complexity. Interviewers rarely expect Manacher's.
- **Explain the `2n-1` centers.** Clearly state that odd-length palindromes have `n` centers and even-length palindromes have `n-1` centers.
- **Know the DP approach too.** Some interviewers specifically ask for a DP solution to test that skill. Be ready to pivot.
- **Mention Manacher's.** Even if you do not code it, mentioning O(n) Manacher's algorithm shows deep knowledge.
- **Use a helper function.** Extracting the expand logic into a helper makes the code cleaner and easier to reason about.

## Mnemonics / Memory Aids

- **"Center, Expand, Track"** -- the three-step pattern for the optimal approach.
- **2n-1 centers** -- odd centers at each index, even centers between each pair.
- **"Palindrome = Mirror"** -- always think about the center/mirror point.
- **DP recurrence: "Outer matches + inner is palindrome"** -- `s[i]==s[j] and dp[i+1][j-1]`.

## Self-Assessment Checklist

- [ ] Can I write the expand-around-center solution from scratch in under 10 minutes?
- [ ] Can I explain why there are `2n-1` centers?
- [ ] Can I trace through `"cbbd"` and `"babad"` step by step?
- [ ] Can I write the DP solution and explain the table fill order?
- [ ] Do I handle both odd and even length palindromes correctly?
- [ ] Can I explain the time/space tradeoff between DP and center-expansion?
- [ ] Can I describe Manacher's algorithm at a high level?
