# Longest Palindrome - Study Guide

## Problem Summary
> **One-liner**: Given a string of letters, find the length of the longest palindrome you can build using those letters (each used at most as many times as it appears).

## Key Intuition

Palindromes are symmetric, so characters must appear in pairs (one for each side). The only exception is the single center character. Therefore: use all complete pairs + optionally one leftover character for the center.

## Approaches Overview

| Approach | Time | Space | Key Idea |
|----------|------|-------|----------|
| Brute Force (Permutations) | O(n!) | O(n) | Try all arrangements |
| Greedy Frequency Counting | O(n) | O(1)* | Count pairs + one center |
| Set-based (Track Odds) | O(n) | O(1)* | Toggle characters in a set |

*O(1) because the character set is bounded (52 letters).

## Approach 1: Greedy Frequency Counting

### Core Idea
Count character frequencies. Sum up all even contributions (`freq // 2 * 2`). If any character has an odd frequency, we can place one in the center (+1).

### Code

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

### Walkthrough
Using `s = "abccccdd"`:
1. `counts = {'a': 1, 'b': 1, 'c': 4, 'd': 2}`
2. 'a': freq=1 -> `1 // 2 * 2 = 0`, has_odd = True. length = 0
3. 'b': freq=1 -> `1 // 2 * 2 = 0`, has_odd = True. length = 0
4. 'c': freq=4 -> `4 // 2 * 2 = 4`. length = 4
5. 'd': freq=2 -> `2 // 2 * 2 = 2`. length = 6
6. has_odd is True -> length = 6 + 1 = **7**

Result: 7 (e.g., "dccaccd")

## Approach 2: Set-Based Tracking (Optimal)

### Core Idea
Use a set to track characters with "unpaired" occurrences. Every time we see a character already in the set, we have a pair -- remove it and add 2 to the length. At the end, if the set is non-empty, we can use one character as the center.

### Code

```python
class Solution:
    def longestPalindrome(self, s: str) -> int:
        unpaired = set()
        length = 0

        for ch in s:
            if ch in unpaired:
                unpaired.remove(ch)
                length += 2
            else:
                unpaired.add(ch)

        if unpaired:
            length += 1

        return length
```

### Walkthrough
Using `s = "abccccdd"`:
1. 'a': not in set -> unpaired = {'a'}, length = 0
2. 'b': not in set -> unpaired = {'a', 'b'}, length = 0
3. 'c': not in set -> unpaired = {'a', 'b', 'c'}, length = 0
4. 'c': in set -> remove, unpaired = {'a', 'b'}, length = 2
5. 'c': not in set -> unpaired = {'a', 'b', 'c'}, length = 2
6. 'c': in set -> remove, unpaired = {'a', 'b'}, length = 4
7. 'd': not in set -> unpaired = {'a', 'b', 'd'}, length = 4
8. 'd': in set -> remove, unpaired = {'a', 'b'}, length = 6
9. unpaired is non-empty -> length = 6 + 1 = **7**

## Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Brute Force | O(n!) | O(n) | Impractical |
| Frequency Counting | O(n) | O(1) | Counter bounded by 52 chars |
| Set-Based | O(n) | O(1) | Set bounded by 52 chars |

## Common Mistakes

1. **Forgetting the center character** -- returning only the sum of pairs misses the +1 for the center.
2. **Adding 1 for every odd-count character** -- only ONE character can occupy the center, not all odd-count characters.
3. **Ignoring case sensitivity** -- 'A' and 'a' are different characters per the problem statement.
4. **Using `freq // 2` instead of `freq // 2 * 2`** -- `freq // 2` gives the number of pairs, but each pair contributes 2 to the length.

## Interview Tips

- **Start by discussing palindrome properties**: Mention that palindromes are symmetric and each character (except possibly one center) must appear an even number of times.
- **Clarify case sensitivity**: The problem says 'Aa' is NOT a palindrome -- mention this upfront.
- **Two clean approaches**: The Counter approach is more intuitive to explain; the set approach is elegant and shows creative thinking.
- **Quick complexity argument**: Bounded character set means O(1) space regardless of string length.

## Mnemonics / Memory Aids

- **"Pairs + One Center"**: Every palindrome is made of pairs of characters plus at most one center character.
- **"Even floor, odd ceiling"**: For each character, use the even part (`freq // 2 * 2`), and remember you get one bonus center if any remainder exists.
- **"Toggle Set"**: Each character toggles in/out of the unpaired set; every toggle-out means we found a pair.

## Self-Assessment Checklist

- [ ] Can I explain why at most one odd-count character is allowed?
- [ ] Can I implement both the Counter and Set approaches from memory?
- [ ] Can I correctly handle mixed-case inputs?
- [ ] Can I trace through "abccccdd" and arrive at 7?
- [ ] Do I know the difference between `freq // 2` (pairs count) and `freq // 2 * 2` (character count)?
- [ ] Can I articulate why this is a greedy problem?
