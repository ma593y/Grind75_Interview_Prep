# Minimum Window Substring

## Problem Information
- **Difficulty**: Hard
- **Category**: Sliding Window
- **Tags**: Hash Table, String, Sliding Window
- **LeetCode Link**: https://leetcode.com/problems/minimum-window-substring/

## Problem Statement
Given two strings `s` and `t` of lengths `m` and `n` respectively, return the **minimum window substring** of `s` such that every character in `t` (including duplicates) is included in the window. If there is no such substring, return the empty string `""`.

The testcases will be generated such that the answer is unique.

### Examples

**Example 1:**
```
Input: s = "ADOBECODEBANC", t = "ABC"
Output: "BANC"
Explanation: The minimum window substring "BANC" includes 'A', 'B', and 'C' from string t.
```

**Example 2:**
```
Input: s = "a", t = "a"
Output: "a"
Explanation: The entire string s is the minimum window.
```

**Example 3:**
```
Input: s = "a", t = "aa"
Output: ""
Explanation: Both 'a's from t must be included in the window.
Since the largest window of s only has one 'a', return empty string.
```

### Constraints
- `m == s.length`
- `n == t.length`
- `1 <= m, n <= 10^5`
- `s` and `t` consist of uppercase and lowercase English letters.

## Core Concept / Pattern
This is the quintessential variable-size sliding window problem. The idea is to expand the window by moving `right` until all characters of `t` are covered, then contract from the `left` to find the smallest valid window. We track how many required characters are satisfied using a `formed` counter.

The key insight is that once we have a valid window, we should try to shrink it from the left before expanding further. This two-pointer approach ensures we examine each character at most twice (once when expanding, once when contracting), yielding O(m) time.

## Approach 1: Brute Force

### Intuition
Check every possible substring of `s` and verify if it contains all characters of `t`. Among all valid substrings, return the shortest.

### Algorithm
1. For each pair `(i, j)` where `0 <= i <= j < m`, extract `s[i:j+1]`.
2. Check if the substring contains all characters of `t` with correct frequencies.
3. Track the shortest valid substring.

### Implementation
```python
from collections import Counter

def minWindow(s: str, t: str) -> str:
    t_count = Counter(t)
    min_len = float('inf')
    result = ""

    for i in range(len(s)):
        window_count = Counter()
        for j in range(i, len(s)):
            window_count[s[j]] += 1
            # Check if window contains all of t
            if all(window_count[ch] >= t_count[ch] for ch in t_count):
                if j - i + 1 < min_len:
                    min_len = j - i + 1
                    result = s[i:j + 1]
                break  # No need to expand further from this i

    return result
```

### Complexity Analysis
- **Time Complexity**: O(m^2 * n) - Two nested loops over `s`, with O(n) check for each window.
- **Space Complexity**: O(m + n) - Counters for the window and `t`.

### Why We Can Do Better
The brute force restarts the inner loop for each starting position. A sliding window avoids this by maintaining and updating the window state incrementally.

## Approach 2: Optimal Solution (Variable-Size Sliding Window)

### Intuition
Use two pointers `left` and `right` to define a window. Expand `right` to include more characters until all of `t` is covered. Then contract `left` to minimize the window while keeping it valid. Track the smallest valid window seen.

### Algorithm
1. Build frequency map `t_count` for `t`. Let `required` = number of unique characters in `t`.
2. Initialize `formed = 0` (number of unique characters in the current window matching their required frequency).
3. Use a `window_count` dictionary for the current window.
4. Expand `right`:
   - Add `s[right]` to `window_count`.
   - If `window_count[s[right]] == t_count[s[right]]`, increment `formed`.
5. While `formed == required`:
   - Update the result if the current window is smaller.
   - Remove `s[left]` from `window_count`. If its count drops below `t_count[s[left]]`, decrement `formed`.
   - Move `left` forward.
6. Return the smallest window found.

### Implementation
```python
from collections import Counter

def minWindow(s: str, t: str) -> str:
    if not s or not t or len(s) < len(t):
        return ""

    t_count = Counter(t)
    required = len(t_count)

    left = 0
    formed = 0
    window_count = {}

    # (window length, left index, right index)
    best = (float('inf'), 0, 0)

    for right in range(len(s)):
        char = s[right]
        window_count[char] = window_count.get(char, 0) + 1

        if char in t_count and window_count[char] == t_count[char]:
            formed += 1

        while formed == required:
            # Update best window
            if right - left + 1 < best[0]:
                best = (right - left + 1, left, right)

            # Contract from the left
            left_char = s[left]
            window_count[left_char] -= 1
            if left_char in t_count and window_count[left_char] < t_count[left_char]:
                formed -= 1
            left += 1

    return "" if best[0] == float('inf') else s[best[1]:best[2] + 1]
```

### Complexity Analysis
- **Time Complexity**: O(m + n) - Each character in `s` is visited at most twice (once by `right`, once by `left`). Building `t_count` is O(n).
- **Space Complexity**: O(m + n) - Hash maps for `t_count` and `window_count`.

## Edge Cases

| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| `t` longer than `s` | Return `""` | Impossible to contain all of `t` |
| `s == t` | Return `s` | Entire string is the window |
| `t` has duplicate characters | Must match exact counts | `t = "aa"` needs two 'a's in window |
| No valid window exists | Return `""` | `s` missing characters from `t` |
| Single character match | Return that character | `s = "a"`, `t = "a"` returns `"a"` |
| Case sensitivity | 'A' and 'a' are different | Problem uses both upper and lowercase |

## Related Problems

| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Longest Substring Without Repeating Characters (#3) | Variable-size sliding window, maximize | Medium |
| Find All Anagrams in a String (#438) | Fixed-size window with frequency matching | Medium |
| Substring with Concatenation of All Words (#30) | Window with word-level matching | Hard |
| Minimum Size Subarray Sum (#209) | Variable-size sliding window on integers | Medium |
