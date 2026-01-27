# Longest Substring Without Repeating Characters - Study Guide

## Problem Summary
- **Difficulty**: Medium
- **Category**: Sliding Window
- **Tags**: Hash Table, String, Sliding Window
- **LeetCode Link**: https://leetcode.com/problems/longest-substring-without-repeating-characters/

> **One-liner**: Find the length of the longest substring in which every character appears at most once.

## Key Intuition
The sliding window works because once a duplicate is found, no valid longer substring can start at the same left position. By tracking each character's last-seen index, we can jump the left pointer directly past the duplicate instead of sliding one position at a time. This "jump optimization" turns the naive O(n^2) approach into O(n).

## Approaches Overview

| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Brute Force (check all substrings) | O(n^2) | O(min(n, m)) | Simple but slow |
| Sliding Window + Hash Map | O(n) | O(min(n, m)) | Optimal, one pass |

## Approach 1: Brute Force

### Core Idea
For every starting index, expand rightward while characters are unique. Track the maximum valid window length.

### Code
```python
def lengthOfLongestSubstring(s: str) -> int:
    n = len(s)
    max_len = 0
    for i in range(n):
        seen = set()
        for j in range(i, n):
            if s[j] in seen:
                break
            seen.add(s[j])
            max_len = max(max_len, j - i + 1)
    return max_len
```

### Walkthrough
Using `s = "abcabcbb"`:
- `i=0`: seen = {a, b, c}, hit 'a' duplicate at j=3, max_len = 3
- `i=1`: seen = {b, c, a}, hit 'b' duplicate at j=4, max_len = 3
- `i=2`: seen = {c, a, b}, hit 'c' duplicate at j=5, max_len = 3
- Continues but never exceeds 3. Return 3.

## Approach 2: Sliding Window + Hash Map (Optimal)

### Core Idea
Maintain a window `[left, right]` where all characters are unique. Use a hash map to store each character's most recent index. When `s[right]` was last seen at an index >= `left`, jump `left` past that index.

### Code
```python
def lengthOfLongestSubstring(s: str) -> int:
    char_index = {}
    left = 0
    max_len = 0

    for right in range(len(s)):
        if s[right] in char_index and char_index[s[right]] >= left:
            left = char_index[s[right]] + 1
        char_index[s[right]] = right
        max_len = max(max_len, right - left + 1)

    return max_len
```

### Walkthrough
Using `s = "abcabcbb"`:
```
right=0, char='a': char_index={'a':0}, left=0, window="a",    max_len=1
right=1, char='b': char_index={'a':0,'b':1}, left=0, window="ab",   max_len=2
right=2, char='c': char_index={'a':0,'b':1,'c':2}, left=0, window="abc",  max_len=3
right=3, char='a': 'a' seen at 0 >= left(0), left=1. char_index={'a':3,'b':1,'c':2}, window="bca", max_len=3
right=4, char='b': 'b' seen at 1 >= left(1), left=2. char_index={'a':3,'b':4,'c':2}, window="cab", max_len=3
right=5, char='c': 'c' seen at 2 >= left(2), left=3. char_index={'a':3,'b':4,'c':5}, window="abc", max_len=3
right=6, char='b': 'b' seen at 4 >= left(3), left=5. char_index={'a':3,'b':6,'c':5}, window="cb", max_len=3
right=7, char='b': 'b' seen at 6 >= left(5), left=7. char_index={'a':3,'b':7,'c':5}, window="b", max_len=3
```
Return 3.

## Complexity Analysis
- **Optimal Time**: O(n) - Single pass through the string, each character visited once.
- **Optimal Space**: O(min(n, m)) - Hash map bounded by the smaller of string length or charset size.

## Common Mistakes
1. **Forgetting the `>= left` check**: Without checking that the stored index is within the current window, you might jump `left` backward, which is incorrect.
2. **Using a set instead of a map**: A set-based approach requires incrementally moving `left` (still O(n) but with a higher constant factor). The map approach allows jumping directly.
3. **Off-by-one on left pointer**: When a duplicate of `s[right]` is found at index `k`, the new `left` should be `k + 1`, not `k`.
4. **Not updating the map after the duplicate check**: Always update the character's index regardless of whether a duplicate was found.

## Interview Tips
- **How to communicate**: Start by stating the problem is about finding the longest window with unique characters. Mention the sliding window pattern immediately.
- **Clarifying questions to ask**: What characters can appear (ASCII, Unicode)? Can the string be empty? Is it case-sensitive?
- **Optimization path**: Start by describing the brute force O(n^2) approach, then explain how the sliding window reduces it to O(n) by avoiding redundant checks.
- **Follow-up variations**: What if we need the actual substring, not just its length? What if we allow at most k repeats? (leads to LeetCode #424)

## Mnemonics / Memory Aids
- **"Slide and Jump"**: The window slides right one step at a time, but the left pointer can jump forward when a duplicate is found.
- **"Map stores last seen"**: Remember that the hash map always holds the most recent index of each character, which enables the jump.
- **"Window is always valid"**: At every step, the window `[left, right]` contains no duplicates.

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?
