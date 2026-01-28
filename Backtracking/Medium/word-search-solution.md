# Word Search

## Problem Information
- **Difficulty**: Medium
- **Category**: Backtracking
- **Tags**: Array, Backtracking, Matrix, DFS
- **LeetCode Link**: https://leetcode.com/problems/word-search/

## Problem Statement

Given an `m x n` grid of characters `board` and a string `word`, return `true` if `word` exists in the grid.

The word can be constructed from letters of sequentially adjacent cells, where adjacent cells are horizontally or vertically neighboring. The same letter cell may not be used more than once.

### Examples

**Example 1:**
```
Input: board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"
Output: true
```

**Example 2:**
```
Input: board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "SEE"
Output: true
```

**Example 3:**
```
Input: board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCB"
Output: false
```

### Constraints
- `m == board.length`
- `n = board[i].length`
- `1 <= m, n <= 6`
- `1 <= word.length <= 15`
- `board` and `word` consist of only lowercase and uppercase English letters.

## Core Concept / Pattern

This is a **DFS backtracking** problem on a 2D grid. Starting from each cell that matches the first character of the word, we perform a depth-first search exploring all four directions. We mark cells as visited to avoid reuse, and unmark them when backtracking.

## Approach 1: Brute Force (Generate All Paths)

### Intuition
Generate all possible paths of length equal to the word in the grid and check if any matches the word. This is essentially what DFS does, but without pruning it explores far too many paths.

### Algorithm
1. For each cell, try all possible paths of the word's length.
2. Check if any path spells out the word.

### Implementation

```python
class Solution:
    def exist(self, board: list[list[str]], word: str) -> bool:
        rows, cols = len(board), len(board[0])

        def dfs(r: int, c: int, idx: int, visited: set) -> bool:
            if idx == len(word):
                return True

            if (r < 0 or r >= rows or c < 0 or c >= cols or
                    (r, c) in visited or board[r][c] != word[idx]):
                return False

            visited.add((r, c))
            for dr, dc in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
                if dfs(r + dr, c + dc, idx + 1, visited):
                    return True
            visited.remove((r, c))
            return False

        for r in range(rows):
            for c in range(cols):
                if dfs(r, c, 0, set()):
                    return True
        return False
```

### Complexity Analysis
- **Time Complexity**: O(M * N * 4^L) where M x N is the board size and L is the word length. Each cell can branch into 4 directions.
- **Space Complexity**: O(L) for recursion stack and visited set.

### Why We Can Do Better
We can optimize by modifying the board in-place instead of using a separate visited set, and by adding pruning (e.g., checking character frequency).

## Approach 2: Optimal Solution (DFS with In-Place Marking + Pruning)

### Intuition
Instead of maintaining a separate visited set, temporarily modify the board cell to a special character (like '#') to mark it as visited. This saves space overhead. Additionally, we can add a frequency pruning check: before starting, verify that the board contains enough of each character needed by the word.

### Algorithm
1. **Pruning**: Count character frequencies in the board. If any character in the word appears more times than in the board, return False immediately.
2. **Optimization**: If the last character of the word is more common than the first, reverse the word (reduces branching).
3. For each cell matching `word[0]`, run DFS:
   - If index reaches word length, return True.
   - Check bounds, character match, and visited status.
   - Mark cell as visited (set to '#').
   - Recurse in all 4 directions.
   - Restore the cell (backtrack).
4. Return True if any DFS succeeds, False otherwise.

### Implementation

```python
from collections import Counter

class Solution:
    def exist(self, board: list[list[str]], word: str) -> bool:
        rows, cols = len(board), len(board[0])

        # Pruning: check if board has enough characters
        board_count = Counter()
        for r in range(rows):
            for c in range(cols):
                board_count[board[r][c]] += 1

        word_count = Counter(word)
        for ch, cnt in word_count.items():
            if board_count[ch] < cnt:
                return False

        # Optimization: reverse word if last char is less frequent
        if board_count[word[0]] > board_count[word[-1]]:
            word = word[::-1]

        def dfs(r: int, c: int, idx: int) -> bool:
            if idx == len(word):
                return True

            if (r < 0 or r >= rows or c < 0 or c >= cols or
                    board[r][c] != word[idx]):
                return False

            # Mark as visited
            temp = board[r][c]
            board[r][c] = '#'

            # Explore all 4 directions
            found = (dfs(r + 1, c, idx + 1) or
                     dfs(r - 1, c, idx + 1) or
                     dfs(r, c + 1, idx + 1) or
                     dfs(r, c - 1, idx + 1))

            # Restore (backtrack)
            board[r][c] = temp
            return found

        for r in range(rows):
            for c in range(cols):
                if board[r][c] == word[0] and dfs(r, c, 0):
                    return True

        return False
```

### Complexity Analysis
- **Time Complexity**: O(M * N * 3^L) -- from each cell we branch into at most 3 new directions (we came from one direction, so we don't go back). L is the word length.
- **Space Complexity**: O(L) for the recursion stack. No extra visited set needed.

## Edge Cases

| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Single cell board, single char word | True if they match | Simplest case |
| Word longer than total cells | False (pruning catches this) | Impossible to form |
| Word uses same cell twice | False | Same cell cannot be reused |
| All cells are the same character | Must still track visited cells | Tests backtracking correctness |
| Word not present at all | False | Character frequency pruning helps |

## Related Problems

| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Word Search II (LC #212) | Find multiple words using Trie + DFS | Hard |
| Surrounded Regions (LC #130) | Grid DFS with boundary conditions | Medium |
| Number of Islands (LC #200) | Grid DFS without backtracking | Medium |
| Path with Maximum Gold (LC #1219) | Grid backtracking for max value | Medium |
