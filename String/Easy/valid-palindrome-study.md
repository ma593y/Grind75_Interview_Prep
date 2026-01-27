# Valid Palindrome - Study Guide

## Problem Summary
- **Difficulty**: Easy
- **Category**: String
- **Tags**: Two Pointers, String
- **LeetCode Link**: https://leetcode.com/problems/valid-palindrome/

> **One-liner**: Determine if a string is a palindrome after removing non-alphanumeric characters and ignoring case.

## Key Intuition
The key insight is that palindrome checking is fundamentally a two-pointer problem. Instead of creating a cleaned copy of the string, you can use left and right pointers that skip over non-alphanumeric characters. This lets you check the palindrome property in O(1) space. Always remember: clean first (skip non-alnum), then compare case-insensitively.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Clean and Compare | O(n) | O(n) | Simple to write but uses extra space |
| Two Pointers | O(n) | O(1) | Optimal space, slightly more code |

## Approach 1: Clean and Compare
### Core Idea
Build a new string containing only lowercase alphanumeric characters, then compare it with its reverse.

### Code
```python
def isPalindrome(s: str) -> bool:
    cleaned = ''.join(ch.lower() for ch in s if ch.isalnum())
    return cleaned == cleaned[::-1]
```

### Walkthrough
Using `"A man, a plan, a canal: Panama"`:
1. Filter and lowercase: `"amanaplanacanalpanama"`
2. Reverse: `"amanaplanacanalpanama"`
3. They match, so return `True`.

## Approach 2: Two Pointers (Optimal)
### Core Idea
Use two pointers converging from both ends, skipping non-alphanumeric characters, and comparing lowercase versions of the characters.

### Code
```python
def isPalindrome(s: str) -> bool:
    left, right = 0, len(s) - 1
    while left < right:
        while left < right and not s[left].isalnum():
            left += 1
        while left < right and not s[right].isalnum():
            right -= 1
        if s[left].lower() != s[right].lower():
            return False
        left += 1
        right -= 1
    return True
```

### Walkthrough
Using `"race a car"`:
1. `left=0 ('r')`, `right=9 ('r')` -> match, move inward.
2. `left=1 ('a')`, `right=8 ('a')` -> match, move inward.
3. `left=2 ('c')`, `right=7 ('c')` -> match, move inward.
4. `left=3 ('e')`, `right=6 ('a')` (skip space at index 5) -> mismatch! Return `False`.

## Complexity Analysis
- **Two Pointers**: O(n) time, O(1) space. Each pointer moves at most n steps total.
- **Clean and Compare**: O(n) time, O(n) space. Building the cleaned string requires extra memory.

## Common Mistakes
1. **Forgetting to handle non-alphanumeric characters**: The problem says to ignore them, not just spaces.
2. **Case sensitivity**: Forgetting to convert to lowercase before comparing.
3. **Off-by-one in pointer movement**: Make sure `left < right` is checked in the inner while loops too.
4. **Using `isalpha()` instead of `isalnum()`**: Numbers are alphanumeric and must be included.

## Interview Tips
- **How to communicate**: Start by clarifying what counts as a palindrome (case-insensitive, alphanumeric only). Mention both approaches and why two pointers is better for space.
- **Clarifying questions to ask**: "Should I consider only ASCII characters?", "Are digits considered valid characters?", "Is an empty string a palindrome?"
- **Optimization path**: Start with the clean-and-compare approach, then optimize to two pointers for O(1) space.
- **Follow-up variations**: "What if you could remove at most one character?" (LeetCode #680), "What about palindrome in a linked list?" (LeetCode #234).

## Mnemonics / Memory Aids
- **"Skip and Compare"**: Two pointers skip junk, compare letters.
- **"Outside In"**: Start from both ends, meet in the middle.
- Think of it as two people reading the string from opposite ends, ignoring punctuation.

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?
