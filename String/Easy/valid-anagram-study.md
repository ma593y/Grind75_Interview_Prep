# Valid Anagram - Study Guide

## Problem Summary
- **Difficulty**: Easy
- **Category**: String
- **Tags**: Hash Table, String, Sorting
- **LeetCode Link**: https://leetcode.com/problems/valid-anagram/

> **One-liner**: Check if two strings have exactly the same character frequencies.

## Key Intuition
Two strings are anagrams if and only if they contain the same characters with the same frequencies. Rather than sorting (O(n log n)), you can count character occurrences in one string and verify against the other in O(n) time. The early termination trick -- returning false as soon as any count goes negative -- avoids unnecessary work.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Sorting | O(n log n) | O(n) | Simple but slower |
| Hash Map / Frequency Array | O(n) | O(1)* | Optimal, slightly more code |

*O(1) for fixed alphabet, O(k) for Unicode where k is character set size.

## Approach 1: Sorting
### Core Idea
Sort both strings; if the sorted versions are identical, they are anagrams.

### Code
```python
def isAnagram(s: str, t: str) -> bool:
    if len(s) != len(t):
        return False
    return sorted(s) == sorted(t)
```

### Walkthrough
Using `s = "anagram"`, `t = "nagaram"`:
1. Sort `s`: `"aaagnmr"`
2. Sort `t`: `"aaagnmr"`
3. They match, return `True`.

## Approach 2: Frequency Counter (Optimal)
### Core Idea
Count character frequencies in the first string, then decrement for each character in the second. If any count goes negative, it is not an anagram.

### Code
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

### Walkthrough
Using `s = "rat"`, `t = "car"`:
1. Count `s`: `{'r': 1, 'a': 1, 't': 1}`
2. Process `t`:
   - `'c'`: count becomes `{'r': 1, 'a': 1, 't': 1, 'c': -1}` -> negative! Return `False`.

## Complexity Analysis
- **Sorting**: O(n log n) time, O(n) space for sorted copies.
- **Frequency Counter**: O(n) time, O(1) space for lowercase English (26 possible chars). For Unicode, O(k) space.

## Common Mistakes
1. **Not checking lengths first**: If lengths differ, they cannot be anagrams. This is the cheapest check.
2. **Only checking character presence, not frequency**: `"aab"` and `"abb"` have the same characters but different frequencies.
3. **Using a set instead of a map**: Sets lose frequency information.
4. **Forgetting the Unicode follow-up**: In an interview, mention that a fixed array works for known alphabets, but a hash map generalizes to Unicode.

## Interview Tips
- **How to communicate**: State that anagram means same character frequencies. Mention both approaches and why hash map is better.
- **Clarifying questions to ask**: "Are the strings always lowercase English letters?", "Can the strings be empty?", "Should I handle Unicode?"
- **Optimization path**: Start with sorting (easy to explain), then improve to frequency counting. Mention the array optimization for fixed alphabets.
- **Follow-up variations**: "What if the input contains Unicode?" (use hash map). "What if you need to group anagrams?" (LeetCode #49 -- sort each string as a key).

## Mnemonics / Memory Aids
- **"Count Up, Count Down"**: Increment for `s`, decrement for `t`. If anything goes negative, bail out.
- **"Same letters, same counts"**: The definition of an anagram in one phrase.
- Think of anagram checking like a balance scale: add weights for `s`, remove for `t`. If balanced, it is an anagram.

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?
