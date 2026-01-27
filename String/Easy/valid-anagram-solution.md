# Valid Anagram

## Problem Information
- **Difficulty**: Easy
- **Category**: String
- **Tags**: Hash Table, String, Sorting
- **LeetCode Link**: https://leetcode.com/problems/valid-anagram/

## Problem Statement
Given two strings `s` and `t`, return `true` if `t` is an anagram of `s`, and `false` otherwise.

An anagram is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once.

### Examples
**Example 1:**
```
Input: s = "anagram", t = "nagaram"
Output: true
```

**Example 2:**
```
Input: s = "rat", t = "car"
Output: false
```

### Constraints
- `1 <= s.length, t.length <= 5 * 10^4`
- `s` and `t` consist of lowercase English letters.

**Follow up**: What if the inputs contain Unicode characters? How would you adapt your solution to such a case?

## Core Concept / Pattern
An anagram means both strings have exactly the same character frequencies. The problem reduces to comparing character frequency distributions. You can either sort both strings and compare (simple but slower), or use a hash map / frequency array to count characters (optimal). The hash map approach is the canonical solution for frequency-based string problems.

## Approach 1: Sorting
### Intuition
If two strings are anagrams, sorting them both will produce the same string. This is the simplest conceptual approach.

### Algorithm
1. If lengths differ, return `False`.
2. Sort both strings.
3. Compare the sorted results.

### Implementation
```python
def isAnagram(s: str, t: str) -> bool:
    if len(s) != len(t):
        return False
    return sorted(s) == sorted(t)
```

### Complexity Analysis
- **Time Complexity**: O(n log n) due to sorting, where n is the length of the strings.
- **Space Complexity**: O(n) for the sorted copies (O(1) if sorting in place with a mutable type).

### Why We Can Do Better
Sorting is O(n log n). We can achieve O(n) by counting character frequencies instead.

## Approach 2: Hash Map / Frequency Counter (Optimal)
### Intuition
Count the frequency of each character in `s`, then subtract the frequency of each character in `t`. If all counts reach zero, the strings are anagrams.

### Algorithm
1. If lengths differ, return `False`.
2. Create a frequency counter for `s`.
3. For each character in `t`, decrement its count.
4. Check if all counts are zero.

### Implementation
```python
def isAnagram(s: str, t: str) -> bool:
    if len(s) != len(t):
        return False
    count = {}
    for ch in s:
        count[ch] = count.get(ch, 0) + 1
    for ch in t:
        count[ch] = count.get(ch, 0) - 1
        if count[ch] < 0:
            return False
    return True
```

**Alternative using Counter:**
```python
from collections import Counter

def isAnagram(s: str, t: str) -> bool:
    return Counter(s) == Counter(t)
```

**Alternative using fixed-size array (for lowercase English only):**
```python
def isAnagram(s: str, t: str) -> bool:
    if len(s) != len(t):
        return False
    count = [0] * 26
    for ch in s:
        count[ord(ch) - ord('a')] += 1
    for ch in t:
        count[ord(ch) - ord('a')] -= 1
        if count[ord(ch) - ord('a')] < 0:
            return False
    return True
```

### Complexity Analysis
- **Time Complexity**: O(n) where n is the length of the strings. We iterate through each string once.
- **Space Complexity**: O(1) since we use at most 26 counters (for lowercase English letters). For Unicode, O(k) where k is the size of the character set.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Different lengths | Return `false` | Anagrams must use all letters exactly once |
| Single character strings | Compare directly | Simplest case |
| Identical strings | Return `true` | A string is an anagram of itself |
| Strings with repeated characters like `"aab"` vs `"aba"` | Return `true` | Must count frequencies, not just presence |
| Unicode characters (follow-up) | Use hash map instead of array | Fixed-size array only works for known character sets |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Group Anagrams (LeetCode #49) | Groups strings by anagram equivalence | Medium |
| Find All Anagrams in a String (LeetCode #438) | Sliding window with anagram check | Medium |
| Ransom Note (LeetCode #383) | Subset frequency check (related concept) | Easy |
