# Find All Anagrams in a String

## Problem Information
- **Difficulty**: Medium
- **Category**: Sliding Window
- **Tags**: Hash Table, String, Sliding Window
- **LeetCode Link**: https://leetcode.com/problems/find-all-anagrams-in-a-string/

## Problem Statement
Given two strings `s` and `p`, return an array of all the start indices of `p`'s anagrams in `s`. You may return the answer in any order.

An **anagram** is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once.

### Examples

**Example 1:**
```
Input: s = "cbaebabacd", p = "abc"
Output: [0, 6]
Explanation:
The substring with start index = 0 is "cba", which is an anagram of "abc".
The substring with start index = 6 is "bac", which is an anagram of "abc".
```

**Example 2:**
```
Input: s = "abab", p = "ab"
Output: [0, 1, 2]
Explanation:
The substring with start index = 0 is "ab", which is an anagram of "ab".
The substring with start index = 1 is "ba", which is an anagram of "ab".
The substring with start index = 2 is "ab", which is an anagram of "ab".
```

### Constraints
- `1 <= s.length, p.length <= 3 * 10^4`
- `s` and `p` consist of lowercase English letters.

## Core Concept / Pattern
This is a fixed-size sliding window problem. Since all anagrams of `p` have the same length, we slide a window of exactly `len(p)` across `s`. At each position, we check whether the character frequency in the window matches the frequency of `p`.

The key optimization is maintaining a running frequency count: as the window slides one position right, we add the new character entering the window and remove the character leaving the window, making each slide an O(1) operation.

## Approach 1: Brute Force

### Intuition
For every substring of `s` with length equal to `len(p)`, sort it and compare to the sorted version of `p`. If they match, the substring is an anagram.

### Algorithm
1. Sort `p`.
2. For each index `i` from `0` to `len(s) - len(p)`:
   - Extract substring `s[i:i+len(p)]`.
   - Sort it and compare to sorted `p`.
   - If equal, add `i` to results.

### Implementation
```python
def findAnagrams(s: str, p: str) -> list[int]:
    result = []
    p_sorted = sorted(p)
    p_len = len(p)

    for i in range(len(s) - p_len + 1):
        if sorted(s[i:i + p_len]) == p_sorted:
            result.append(i)

    return result
```

### Complexity Analysis
- **Time Complexity**: O((n - m) * m log m) - For each of the n - m + 1 positions, we sort a substring of length m.
- **Space Complexity**: O(m) - Space for the sorted substrings.

### Why We Can Do Better
Sorting each substring is expensive. Since we only need to compare character frequencies, we can maintain a frequency counter as the window slides, updating it in O(1) per step.

## Approach 2: Optimal Solution (Fixed-Size Sliding Window with Frequency Count)

### Intuition
Maintain two frequency counters: one for `p` (fixed) and one for the current window in `s` (updated as the window slides). Instead of comparing the full counters at every step, track a `matches` variable that counts how many of the 26 letters have equal frequency in both counters. When `matches == 26`, the window is an anagram.

### Algorithm
1. Build frequency counter `p_count` for `p`.
2. Build frequency counter `s_count` for the first window `s[0:len(p)]`.
3. Compute initial `matches` by comparing both counters across all 26 letters.
4. If `matches == 26`, add index `0` to results.
5. Slide the window from index `1` to `len(s) - len(p)`:
   - Add character entering the window: increment its count in `s_count`, update `matches`.
   - Remove character leaving the window: decrement its count in `s_count`, update `matches`.
   - If `matches == 26`, add the current start index to results.

### Implementation
```python
def findAnagrams(s: str, p: str) -> list[int]:
    if len(p) > len(s):
        return []

    p_count = [0] * 26
    s_count = [0] * 26
    result = []

    for ch in p:
        p_count[ord(ch) - ord('a')] += 1
    for ch in s[:len(p)]:
        s_count[ord(ch) - ord('a')] += 1

    matches = 0
    for i in range(26):
        if s_count[i] == p_count[i]:
            matches += 1

    if matches == 26:
        result.append(0)

    for i in range(1, len(s) - len(p) + 1):
        # Add new character entering the window
        idx_add = ord(s[i + len(p) - 1]) - ord('a')
        s_count[idx_add] += 1
        if s_count[idx_add] == p_count[idx_add]:
            matches += 1
        elif s_count[idx_add] == p_count[idx_add] + 1:
            matches -= 1

        # Remove character leaving the window
        idx_rem = ord(s[i - 1]) - ord('a')
        s_count[idx_rem] -= 1
        if s_count[idx_rem] == p_count[idx_rem]:
            matches += 1
        elif s_count[idx_rem] == p_count[idx_rem] - 1:
            matches -= 1

        if matches == 26:
            result.append(i)

    return result
```

### Complexity Analysis
- **Time Complexity**: O(n) - One pass through `s`. Each window slide is O(1) since we update two characters and check a constant number of conditions.
- **Space Complexity**: O(1) - Two fixed-size arrays of 26 elements each.

## Edge Cases

| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| `p` longer than `s` | Return `[]` | No window of length `len(p)` can fit in `s` |
| `s` equals `p` | Return `[0]` | The entire string is the only anagram |
| All same characters `s="aaaa"`, `p="aa"` | Return `[0, 1, 2]` | Every window matches |
| No anagrams exist | Return `[]` | Character sets do not match |
| `p` has length 1 | Return all indices where `s[i] == p[0]` | Degenerate case of single-char anagram |

## Related Problems

| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Longest Substring Without Repeating Characters (#3) | Variable-size sliding window | Medium |
| Minimum Window Substring (#76) | Sliding window with character counting | Hard |
| Permutation in String (#567) | Nearly identical: check if any permutation of p exists in s | Medium |
| Group Anagrams (#49) | Anagram grouping with frequency-based keys | Medium |
