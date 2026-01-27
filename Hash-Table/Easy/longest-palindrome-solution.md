# Longest Palindrome

## Problem Information
- **Difficulty**: Easy
- **Category**: Hash Table
- **Tags**: Hash Table, String, Greedy
- **LeetCode Link**: https://leetcode.com/problems/longest-palindrome/

## Problem Statement

Given a string `s` which consists of lowercase or uppercase letters, return the length of the **longest palindrome** that can be built with those letters.

Letters are **case sensitive**, for example, `"Aa"` is not considered a palindrome.

### Examples

**Example 1:**
```
Input: s = "abccccdd"
Output: 7
Explanation: One longest palindrome that can be built is "dccaccd", whose length is 7.
```

**Example 2:**
```
Input: s = "a"
Output: 1
Explanation: The longest palindrome that can be built is "a", whose length is 1.
```

### Constraints
- `1 <= s.length <= 2000`
- `s` consists of lowercase **and/or** uppercase English letters only.

## Core Concept / Pattern

A palindrome is symmetric. Every character that appears an even number of times can be fully used. For characters appearing an odd number of times, we can use `count - 1` (the largest even portion). Additionally, exactly one character with an odd count can be placed in the center of the palindrome.

## Approach 1: Brute Force

### Intuition

Generate all possible subsets of characters, check if each can form a palindrome, and track the maximum length. This is computationally infeasible but conceptually straightforward.

### Algorithm

1. Generate all permutations of all subsets of characters.
2. Check each permutation to see if it is a palindrome.
3. Track the longest one found.

### Implementation

```python
from itertools import permutations

class Solution:
    def longestPalindrome(self, s: str) -> int:
        max_len = 0
        # This is conceptual only -- far too slow for real use
        for length in range(len(s), 0, -1):
            for perm in permutations(s, length):
                word = ''.join(perm)
                if word == word[::-1]:
                    return length
        return 0
```

### Complexity Analysis
- **Time Complexity**: O(n! * n) -- generating permutations is factorial; checking each is linear.
- **Space Complexity**: O(n) for each permutation string.

### Why We Can Do Better

Instead of checking permutations, we can reason about character frequencies directly. A palindrome uses pairs of characters symmetrically, so counting pairs is all we need.

## Approach 2: Optimal Solution (Greedy Frequency Counting)

### Intuition

Count the frequency of each character. For building a palindrome:
- Every pair of identical characters contributes 2 to the length.
- At most one character with an odd frequency can sit in the center, contributing 1.

So the answer is: (sum of all even contributions) + (1 if any character has an odd count).

### Algorithm

1. Count character frequencies using a hash map.
2. Initialize `length = 0` and `has_odd = False`.
3. For each frequency:
   - Add `freq // 2 * 2` to length (use all complete pairs).
   - If `freq` is odd, set `has_odd = True`.
4. If `has_odd`, add 1 to length (center character).
5. Return `length`.

### Implementation

```python
from collections import Counter

class Solution:
    def longestPalindrome(self, s: str) -> int:
        counts = Counter(s)
        length = 0
        has_odd = False

        for freq in counts.values():
            length += freq // 2 * 2
            if freq % 2 == 1:
                has_odd = True

        if has_odd:
            length += 1

        return length
```

### Complexity Analysis
- **Time Complexity**: O(n) where n = len(s). One pass to count frequencies, one pass over the (bounded) frequency map.
- **Space Complexity**: O(1) since there are at most 52 distinct characters (26 lowercase + 26 uppercase).

## Edge Cases

| Edge Case | Input | Expected Output | Why |
|-----------|-------|-----------------|-----|
| Single character | `"a"` | `1` | The character itself is a palindrome |
| All same characters | `"aaaa"` | `4` | All can be used as pairs |
| All unique characters | `"abcdef"` | `1` | Only one character can be the center |
| Mixed case | `"AaBb"` | `1` | Case sensitive; all have count 1, only one center |
| Even counts only | `"aabb"` | `4` | Full usage, no odd remainder |
| Large odd count | `"aaabb"` | `5` | 2 pairs of 'a' + 1 'b' center = `4 + 1 = 5` |

## Related Problems

| Problem | Difficulty | Why Related |
|---------|------------|-------------|
| [Palindrome Permutation (LeetCode #266)](https://leetcode.com/problems/palindrome-permutation/) | Easy | Check if any permutation forms a palindrome |
| [Valid Palindrome (LeetCode #125)](https://leetcode.com/problems/valid-palindrome/) | Easy | Basic palindrome validation |
| [Longest Palindromic Substring (LeetCode #5)](https://leetcode.com/problems/longest-palindromic-substring/) | Medium | Finding palindromes within a string |
| [Palindrome Pairs (LeetCode #336)](https://leetcode.com/problems/palindrome-pairs/) | Hard | Advanced palindrome construction |
