# Find All Anagrams in a String - Study Guide

## Problem Summary
- **Difficulty**: Medium
- **Category**: Sliding Window
- **Tags**: Hash Table, String, Sliding Window
- **LeetCode Link**: https://leetcode.com/problems/find-all-anagrams-in-a-string/

> **One-liner**: Find all start indices in `s` where a substring of length `len(p)` is an anagram of `p`.

## Key Intuition
Because all anagrams of `p` have the exact same length, this is a fixed-size sliding window problem. Instead of re-counting character frequencies from scratch for each window position, we can incrementally update the count by adding the new character and removing the old one. Tracking a `matches` variable (how many of the 26 letters have equal counts) lets us check for anagram matches in O(1).

## Approaches Overview

| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Brute Force (sort each window) | O((n-m) * m log m) | O(m) | Simple but very slow |
| Counter comparison per window | O(n * 26) | O(1) | Good but redundant comparisons |
| Sliding Window + matches count | O(n) | O(1) | Optimal, constant-time per slide |

## Approach 1: Brute Force (Sort Comparison)

### Core Idea
For each window of size `len(p)` in `s`, sort the window and compare to sorted `p`.

### Code
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

### Walkthrough
Using `s = "cbaebabacd"`, `p = "abc"`, `p_sorted = "abc"`:
- i=0: sorted("cba") = "abc" == "abc" -> add 0
- i=1: sorted("bae") = "abe" != "abc" -> skip
- ...
- i=6: sorted("bac") = "abc" == "abc" -> add 6
- Result: [0, 6]

## Approach 2: Sliding Window + Matches (Optimal)

### Core Idea
Use two frequency arrays of size 26. Track how many letters have equal frequency across both arrays (`matches`). When `matches == 26`, the window is an anagram. Update matches incrementally as the window slides.

### Code
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

    matches = sum(1 for i in range(26) if s_count[i] == p_count[i])

    if matches == 26:
        result.append(0)

    for i in range(1, len(s) - len(p) + 1):
        # Add entering character
        idx = ord(s[i + len(p) - 1]) - ord('a')
        s_count[idx] += 1
        if s_count[idx] == p_count[idx]:
            matches += 1
        elif s_count[idx] == p_count[idx] + 1:
            matches -= 1

        # Remove leaving character
        idx = ord(s[i - 1]) - ord('a')
        s_count[idx] -= 1
        if s_count[idx] == p_count[idx]:
            matches += 1
        elif s_count[idx] == p_count[idx] - 1:
            matches -= 1

        if matches == 26:
            result.append(i)

    return result
```

### Walkthrough
Using `s = "cbaebabacd"`, `p = "abc"`:
```
p_count: a=1, b=1, c=1
Initial window "cba": s_count: a=1, b=1, c=1 -> matches=26 -> add 0
Slide to "bae": remove 'c' (matches drops), add 'e' -> matches!=26
Slide to "aeb": remove 'b', add 'b' -> still not 26
...
Slide to "bac": s_count: a=1, b=1, c=1 -> matches=26 -> add 6
```
Result: [0, 6]

## Complexity Analysis
- **Optimal Time**: O(n) where n = len(s). Each slide updates exactly 2 characters and checks constant conditions.
- **Optimal Space**: O(1). Two arrays of fixed size 26 regardless of input size.

## Common Mistakes
1. **Comparing full frequency arrays every slide**: Using `s_count == p_count` at each position is O(26) per step. Using the `matches` variable makes it O(1).
2. **Off-by-one in the sliding window range**: The last valid start index is `len(s) - len(p)`, not `len(s) - 1`.
3. **Incorrect matches update logic**: When a count changes, it can either gain or lose a match. You must check both the "became equal" and "was equal but no longer" cases.
4. **Forgetting the edge case `len(p) > len(s)`**: Must return empty list immediately.

## Interview Tips
- **How to communicate**: State upfront that anagrams have the same length, so this is a fixed-size sliding window. Explain the frequency-counting approach before coding.
- **Clarifying questions to ask**: Are characters lowercase only? Can `p` be longer than `s`? Can `p` be empty?
- **Optimization path**: Brute force sort -> frequency comparison per window -> incremental matches counter.
- **Follow-up variations**: What if you need to find anagrams of multiple patterns? What about case-insensitive matching?

## Mnemonics / Memory Aids
- **"Fixed window, sliding count"**: The window size never changes; only the frequency counts slide.
- **"26 matches = anagram"**: When all 26 letters have matching frequencies, you have an anagram.
- **"Add right, remove left"**: Each slide adds the new rightmost character and removes the old leftmost character.

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?
