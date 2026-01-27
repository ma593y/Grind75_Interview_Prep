# Longest Substring Without Repeating Characters

## Problem Information
- **Difficulty**: Medium
- **Category**: Sliding Window
- **Tags**: Hash Table, String, Sliding Window
- **LeetCode Link**: https://leetcode.com/problems/longest-substring-without-repeating-characters/

## Problem Statement
Given a string `s`, find the length of the **longest substring** without repeating characters.

A **substring** is a contiguous non-empty sequence of characters within a string.

### Examples

**Example 1:**
```
Input: s = "abcabcbb"
Output: 3
Explanation: The answer is "abc", with the length of 3.
```

**Example 2:**
```
Input: s = "bbbbb"
Output: 1
Explanation: The answer is "b", with the length of 1.
```

**Example 3:**
```
Input: s = "pwwkew"
Output: 3
Explanation: The answer is "wke", with the length of 3.
Notice that the answer must be a substring, "pwke" is a subsequence and not a substring.
```

### Constraints
- `0 <= s.length <= 5 * 10^4`
- `s` consists of English letters, digits, symbols, and spaces.

## Core Concept / Pattern
This problem is a classic sliding window problem. The key insight is maintaining a window `[left, right]` that always contains unique characters. As we expand the window by moving `right`, if we encounter a duplicate character, we shrink from the `left` until the window is valid again.

The sliding window pattern works here because if a substring `s[left..right]` contains a duplicate, then any longer substring starting at `left` will also contain that duplicate. This monotonic property is what makes the sliding window approach efficient.

## Approach 1: Brute Force

### Intuition
Check every possible substring and determine if it contains all unique characters. Track the maximum length among all valid substrings.

### Algorithm
1. For each starting index `i`, iterate through all ending indices `j > i`.
2. For each substring `s[i..j]`, check if all characters are unique using a set.
3. Track the maximum length found.

### Implementation
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

### Complexity Analysis
- **Time Complexity**: O(n^2) - Two nested loops, each iterating up to n times.
- **Space Complexity**: O(min(n, m)) - Where m is the size of the character set. The set stores at most min(n, m) characters.

### Why We Can Do Better
The brute force rechecks many characters redundantly. When we find a duplicate at position `j` starting from `i`, we restart from `i+1` and recheck overlapping characters. A sliding window avoids this by only moving the left pointer forward when needed.

## Approach 2: Optimal Solution (Sliding Window with Hash Map)

### Intuition
Use two pointers to define a window. Maintain a hash map that stores the most recent index of each character. When a duplicate is found, jump the left pointer directly past the previous occurrence of that character, avoiding the need to shrink one step at a time.

### Algorithm
1. Initialize `left = 0`, `max_len = 0`, and a dictionary `char_index` mapping characters to their latest index.
2. Iterate `right` from `0` to `n - 1`:
   - If `s[right]` is in `char_index` and its stored index is `>= left`, move `left` to `char_index[s[right]] + 1`.
   - Update `char_index[s[right]] = right`.
   - Update `max_len = max(max_len, right - left + 1)`.
3. Return `max_len`.

### Implementation
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

### Complexity Analysis
- **Time Complexity**: O(n) - Each character is visited at most once by the `right` pointer. The `left` pointer only moves forward.
- **Space Complexity**: O(min(n, m)) - Where m is the size of the character set. The hash map stores at most min(n, m) entries.

## Edge Cases

| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Empty string `""` | Return `0` | No characters to form a substring |
| Single character `"a"` | Return `1` | Trivial case, window of size 1 |
| All identical characters `"aaaa"` | Return `1` | Every expansion causes a duplicate |
| All unique characters `"abcdef"` | Return `6` (length of string) | Entire string is the answer |
| Spaces and special characters `"a b c"` | Return `3` (`"a b"` or `" b "` etc.) | Spaces are valid characters |

## Related Problems

| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Longest Repeating Character Replacement (#424) | Sliding window with at most k replacements | Medium |
| Substring with Concatenation of All Words (#30) | Sliding window with word matching | Hard |
| Minimum Window Substring (#76) | Sliding window shrink/expand pattern | Hard |
| Find All Anagrams in a String (#438) | Fixed-size sliding window variant | Medium |
