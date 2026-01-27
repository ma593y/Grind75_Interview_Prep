# Longest Palindromic Substring

## Problem Information
- **Difficulty**: Medium
- **Category**: String
- **Tags**: String, Dynamic Programming, Two Pointers
- **LeetCode Link**: https://leetcode.com/problems/longest-palindromic-substring/

## Problem Statement

Given a string `s`, return the longest palindromic substring in `s`.

A **palindrome** is a string that reads the same forward and backward.

### Examples

**Example 1:**
```
Input: s = "babad"
Output: "bab"
Explanation: "aba" is also a valid answer.
```

**Example 2:**
```
Input: s = "cbbd"
Output: "bb"
```

### Constraints

- `1 <= s.length <= 1000`
- `s` consists of only digits and English letters.

## Core Concept / Pattern

Every palindrome mirrors around its center. A palindrome of odd length has a single center character, while a palindrome of even length has a center between two characters. By **expanding around each possible center**, we can find all palindromic substrings in O(n^2) time and O(1) space.

## Approach 1: Brute Force

### Intuition

Check every possible substring and verify whether it is a palindrome. Track the longest one found.

### Algorithm

1. Generate all substrings using two nested loops (start index `i`, end index `j`).
2. For each substring `s[i:j+1]`, check if it reads the same forwards and backwards.
3. If it is a palindrome and longer than the current best, update the answer.

### Implementation

```python
class Solution:
    def longestPalindrome(self, s: str) -> str:
        n = len(s)
        best = ""

        for i in range(n):
            for j in range(i, n):
                substring = s[i:j + 1]
                if substring == substring[::-1] and len(substring) > len(best):
                    best = substring

        return best
```

### Complexity Analysis
- **Time Complexity**: O(n^3) -- O(n^2) substrings, each checked in O(n).
- **Space Complexity**: O(n) -- for the substring copy during palindrome check.

### Why We Can Do Better

Checking every substring is wasteful because palindromes have overlapping substructure. If `s[i+1:j]` is a palindrome and `s[i] == s[j]`, then `s[i:j+1]` is also a palindrome. We can exploit this with dynamic programming or center expansion.

## Approach 2: Expand Around Center (Optimal)

### Intuition

A palindrome mirrors around its center. There are `2n - 1` possible centers in a string of length `n` (each character is a center for odd-length palindromes, and each pair of adjacent characters is a center for even-length palindromes). For each center, expand outward as long as the characters on both sides match.

### Algorithm

1. For each index `i` from `0` to `n-1`:
   a. Expand around center `(i, i)` for odd-length palindromes.
   b. Expand around center `(i, i+1)` for even-length palindromes.
2. During expansion, move `left` pointer leftward and `right` pointer rightward while `s[left] == s[right]`.
3. Track the start index and length of the longest palindrome found.

### Implementation

```python
class Solution:
    def longestPalindrome(self, s: str) -> str:
        n = len(s)
        if n < 2:
            return s

        start = 0
        max_len = 1

        def expand(left: int, right: int) -> None:
            nonlocal start, max_len
            while left >= 0 and right < n and s[left] == s[right]:
                current_len = right - left + 1
                if current_len > max_len:
                    start = left
                    max_len = current_len
                left -= 1
                right += 1

        for i in range(n):
            expand(i, i)      # Odd-length palindromes
            expand(i, i + 1)  # Even-length palindromes

        return s[start:start + max_len]
```

### Complexity Analysis
- **Time Complexity**: O(n^2) -- each expansion takes up to O(n) and there are O(n) centers.
- **Space Complexity**: O(1) -- only a few integer variables are used (not counting the output string).

## Approach 3: Dynamic Programming

### Intuition

Use a 2D table `dp[i][j]` where `dp[i][j]` is `True` if `s[i:j+1]` is a palindrome. Build the table bottom-up from shorter substrings to longer ones.

### Algorithm

1. Every single character is a palindrome: `dp[i][i] = True`.
2. For length 2: `dp[i][i+1] = (s[i] == s[i+1])`.
3. For length `L` from 3 to `n`: `dp[i][j] = dp[i+1][j-1] and s[i] == s[j]`.
4. Track the longest palindromic substring encountered.

### Implementation

```python
class Solution:
    def longestPalindrome(self, s: str) -> str:
        n = len(s)
        if n < 2:
            return s

        dp = [[False] * n for _ in range(n)]
        start = 0
        max_len = 1

        # Base case: single characters
        for i in range(n):
            dp[i][i] = True

        # Base case: two characters
        for i in range(n - 1):
            if s[i] == s[i + 1]:
                dp[i][i + 1] = True
                start = i
                max_len = 2

        # Fill for lengths 3..n
        for length in range(3, n + 1):
            for i in range(n - length + 1):
                j = i + length - 1
                if s[i] == s[j] and dp[i + 1][j - 1]:
                    dp[i][j] = True
                    if length > max_len:
                        start = i
                        max_len = length

        return s[start:start + max_len]
```

### Complexity Analysis
- **Time Complexity**: O(n^2)
- **Space Complexity**: O(n^2) for the DP table.

## Edge Cases

| Edge Case | Input | Expected Output | Why It Matters |
|---|---|---|---|
| Single character | `"a"` | `"a"` | Minimal valid input |
| All same characters | `"aaaa"` | `"aaaa"` | Entire string is a palindrome |
| No palindrome > 1 | `"abcd"` | `"a"` (any single char) | Every single char is a palindrome |
| Even-length palindrome | `"cbbd"` | `"bb"` | Must handle even-length centers |
| Palindrome at end | `"abacdc"` | `"cdc"` | Not always at the start |
| Full string palindrome | `"racecar"` | `"racecar"` | Expand covers full string |

## Related Problems

| Problem | Difficulty | Key Similarity |
|---|---|---|
| [Palindromic Substrings (LC #647)](https://leetcode.com/problems/palindromic-substrings/) | Medium | Same expand-around-center technique, but count instead of longest |
| [Longest Palindromic Subsequence (LC #516)](https://leetcode.com/problems/longest-palindromic-subsequence/) | Medium | DP on palindromes, but subsequence not substring |
| [Valid Palindrome (LC #125)](https://leetcode.com/problems/valid-palindrome/) | Easy | Basic palindrome checking |
| [Shortest Palindrome (LC #214)](https://leetcode.com/problems/shortest-palindrome/) | Hard | Palindrome + KMP / string hashing |
| [Manacher's Algorithm](https://en.wikipedia.org/wiki/Manacher%27s_algorithm) | -- | O(n) algorithm for this problem (advanced) |
