# Ransom Note

## Problem Information
- **Difficulty**: Easy
- **Category**: Hash Table
- **Tags**: Hash Table, String, Counting
- **LeetCode Link**: https://leetcode.com/problems/ransom-note/

## Problem Statement

Given two strings `ransomNote` and `magazine`, return `true` if `ransomNote` can be constructed by using the letters from `magazine` and `false` otherwise.

Each letter in `magazine` can only be used once in `ransomNote`.

### Examples

**Example 1:**
```
Input: ransomNote = "a", magazine = "b"
Output: false
```

**Example 2:**
```
Input: ransomNote = "aa", magazine = "ab"
Output: false
```

**Example 3:**
```
Input: ransomNote = "aa", magazine = "aab"
Output: true
```

### Constraints
- `1 <= ransomNote.length, magazine.length <= 10^5`
- `ransomNote` and `magazine` consist of lowercase English letters.

## Core Concept / Pattern

Count character frequencies in both strings. Every character required by the ransom note must appear at least as many times in the magazine. This is a classic frequency-counting / hash-map problem.

## Approach 1: Brute Force

### Intuition

For each character in the ransom note, search through the magazine for a matching character. If found, remove it from the magazine so it cannot be reused.

### Algorithm

1. Convert magazine to a list for mutability.
2. For each character in `ransomNote`:
   - Search for the character in the magazine list.
   - If found, remove it; otherwise return `False`.
3. Return `True` if all characters were found.

### Implementation

```python
class Solution:
    def canConstruct(self, ransomNote: str, magazine: str) -> bool:
        magazine_list = list(magazine)
        for ch in ransomNote:
            if ch in magazine_list:
                magazine_list.remove(ch)
            else:
                return False
        return True
```

### Complexity Analysis
- **Time Complexity**: O(n * m) where n = len(ransomNote) and m = len(magazine). For each character in the ransom note, we potentially scan the entire magazine list, and `list.remove()` is O(m).
- **Space Complexity**: O(m) for the magazine list copy.

### Why We Can Do Better

The brute force repeatedly scans the magazine list. By pre-counting character frequencies, we can answer each character lookup in O(1) time.

## Approach 2: Optimal Solution (Hash Map Counting)

### Intuition

Count the frequency of every character in the magazine using a hash map. Then iterate through the ransom note, decrementing counts. If any count drops below zero, the magazine does not have enough of that character.

### Algorithm

1. Build a frequency counter for the magazine.
2. For each character in `ransomNote`:
   - Decrement its count in the counter.
   - If the count goes below zero, return `False`.
3. Return `True`.

### Implementation

```python
from collections import Counter

class Solution:
    def canConstruct(self, ransomNote: str, magazine: str) -> bool:
        mag_count = Counter(magazine)
        for ch in ransomNote:
            if mag_count[ch] <= 0:
                return False
            mag_count[ch] -= 1
        return True
```

### Complexity Analysis
- **Time Complexity**: O(n + m) where n = len(ransomNote) and m = len(magazine). We iterate through the magazine once to build the counter and through the ransom note once to check.
- **Space Complexity**: O(1) since there are at most 26 lowercase English letters, the hash map has bounded size.

## Edge Cases

| Edge Case | Input | Expected Output | Why |
|-----------|-------|-----------------|-----|
| Empty ransom note | `ransomNote = "", magazine = "abc"` | `True` | No characters needed |
| Magazine too short | `ransomNote = "abc", magazine = "ab"` | `False` | Missing character 'c' |
| Same strings | `ransomNote = "abc", magazine = "abc"` | `True` | Exact match |
| Duplicate chars needed | `ransomNote = "aaa", magazine = "aaab"` | `True` | Enough duplicates |
| Duplicate chars short | `ransomNote = "aaa", magazine = "aa"` | `False` | Not enough 'a' characters |
| Single character | `ransomNote = "a", magazine = "a"` | `True` | Exact single match |

## Related Problems

| Problem | Difficulty | Why Related |
|---------|------------|-------------|
| [Valid Anagram (LeetCode #242)](https://leetcode.com/problems/valid-anagram/) | Easy | Same frequency counting pattern |
| [First Unique Character in a String (LeetCode #387)](https://leetcode.com/problems/first-unique-character-in-a-string/) | Easy | Character frequency counting |
| [Find the Difference (LeetCode #389)](https://leetcode.com/problems/find-the-difference/) | Easy | Comparing character frequencies |
| [Minimum Window Substring (LeetCode #76)](https://leetcode.com/problems/minimum-window-substring/) | Hard | Frequency-based substring matching |
