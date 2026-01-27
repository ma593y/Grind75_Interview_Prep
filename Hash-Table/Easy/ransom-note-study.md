# Ransom Note - Study Guide

## Problem Summary
> **One-liner**: Determine if a ransom note can be constructed from the letters available in a magazine (each letter used at most once).

## Key Intuition

This is fundamentally a "do I have enough of each resource?" problem. The magazine provides a pool of characters, and we need to verify the ransom note's demands can be met. Counting character frequencies transforms the problem into simple arithmetic comparisons.

## Approaches Overview

| Approach | Time | Space | Key Idea |
|----------|------|-------|----------|
| Brute Force (List Removal) | O(n * m) | O(m) | Search and remove each character |
| Hash Map Counting | O(n + m) | O(1)* | Count frequencies, then verify |
| Counter Subtraction | O(n + m) | O(1)* | Use Python Counter difference |

*O(1) because alphabet is bounded to 26 lowercase letters.

## Approach 1: Brute Force (List Removal)

### Core Idea
Convert the magazine to a mutable list. For each character in the ransom note, find and remove it from the list. If any character is missing, return False.

### Code

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

### Walkthrough
Using `ransomNote = "aa"`, `magazine = "aab"`:
1. `magazine_list = ['a', 'a', 'b']`
2. ch = 'a': found in list, remove -> `['a', 'b']`
3. ch = 'a': found in list, remove -> `['b']`
4. All characters found -> return `True`

## Approach 2: Hash Map Counting (Optimal)

### Core Idea
Count every character in the magazine up front. Then for each character the ransom note needs, decrement the count. If any count goes negative, the magazine does not have enough of that letter.

### Code

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

### Walkthrough
Using `ransomNote = "aa"`, `magazine = "aab"`:
1. `mag_count = {'a': 2, 'b': 1}`
2. ch = 'a': mag_count['a'] = 2 > 0, decrement -> `{'a': 1, 'b': 1}`
3. ch = 'a': mag_count['a'] = 1 > 0, decrement -> `{'a': 0, 'b': 1}`
4. All characters satisfied -> return `True`

**Alternative one-liner using Counter subtraction:**
```python
from collections import Counter

class Solution:
    def canConstruct(self, ransomNote: str, magazine: str) -> bool:
        return not (Counter(ransomNote) - Counter(magazine))
```
`Counter` subtraction removes keys where the result is zero or negative, so if nothing remains, the magazine covers all ransom note needs.

## Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Brute Force | O(n * m) | O(m) | `list.remove()` is O(m) per call |
| Hash Map | O(n + m) | O(1) | One pass each; bounded 26-char map |
| Counter Subtraction | O(n + m) | O(1) | Pythonic but same complexity |

## Common Mistakes

1. **Forgetting letters can only be used once** -- simply checking membership (`ch in magazine`) without tracking consumption leads to wrong answers.
2. **Counting ransom note instead of magazine** -- you should count the available supply (magazine), then check against demand (ransom note).
3. **Not handling duplicate characters** -- `ransomNote = "aaa"` with `magazine = "aa"` must return `False`.
4. **Using `set` instead of `Counter`** -- sets lose frequency information, only tracking presence/absence.

## Interview Tips

- **Clarify first**: Ask if characters are only lowercase English letters (constrains hash map size to 26).
- **Mention the O(1) space argument**: Since the alphabet is bounded, the hash map has constant size regardless of input length.
- **Know the one-liner**: `not (Counter(ransomNote) - Counter(magazine))` shows Python fluency but be ready to explain it.
- **Edge case awareness**: Mention empty ransom note (always True), magazine shorter than ransom note (quick False check).

## Mnemonics / Memory Aids

- **"Supply vs Demand"**: Magazine is supply, ransom note is demand. Count supply, then check if demand can be met.
- **"Count then Subtract"**: Two-step mental model -- build the inventory, then fulfill the order.

## Self-Assessment Checklist

- [ ] Can I explain why a set is not sufficient for this problem?
- [ ] Can I implement the Counter-based solution from memory?
- [ ] Can I articulate why space is O(1) despite using a hash map?
- [ ] Can I walk through an example with duplicate characters?
- [ ] Can I write the one-liner Counter subtraction variant?
- [ ] Do I know how to handle the edge case of an empty ransom note?
