# Word Search - Study Guide

## Problem Summary
> **One-liner**: Determine if a word can be formed by traversing adjacent cells in a 2D character grid without reusing any cell.

## Key Intuition

This is a classic grid DFS problem with backtracking. From each cell matching the first character, explore all four directions trying to match subsequent characters. The critical part is marking cells as visited during exploration and unmarking them when backtracking, so other paths can use those cells.

## Approaches Overview

| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| DFS with Visited Set | O(M*N*4^L) | O(L) | Clear but slightly more overhead |
| DFS with In-Place Marking | O(M*N*3^L) | O(L) | Faster, modifies board temporarily |
| DFS + Frequency Pruning | O(M*N*3^L) | O(L) | Best practical performance |

## Approach 1: DFS with Visited Set

### Core Idea
Use a set to track visited cells. At each step, check bounds, character match, and visited status. Add the cell to the visited set before recursing, remove it after.

### Code

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
            for dr, dc in [(0,1),(0,-1),(1,0),(-1,0)]:
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

### Walkthrough
Using board = [["A","B"],["C","D"]], word = "ABDC":
```
Start at (0,0) 'A' matches word[0]
  visited = {(0,0)}
  Go right (0,1) 'B' matches word[1]
    visited = {(0,0),(0,1)}
    Go down (1,1) 'D' matches word[2]
      visited = {(0,0),(0,1),(1,1)}
      Go left (1,0) 'C' matches word[3]
        idx=4 == len(word) -> return True!
```

## Approach 2: DFS with In-Place Marking + Pruning (Optimal)

### Core Idea
Replace the current cell with '#' to mark it visited (saves space). Add character frequency pruning to eliminate impossible cases early. Optionally reverse the word if the last character is rarer than the first.

### Code

```python
from collections import Counter

class Solution:
    def exist(self, board: list[list[str]], word: str) -> bool:
        rows, cols = len(board), len(board[0])

        # Frequency pruning
        board_count = Counter()
        for r in range(rows):
            for c in range(cols):
                board_count[board[r][c]] += 1
        for ch, cnt in Counter(word).items():
            if board_count[ch] < cnt:
                return False

        # Reverse word if beneficial
        if board_count[word[0]] > board_count[word[-1]]:
            word = word[::-1]

        def dfs(r: int, c: int, idx: int) -> bool:
            if idx == len(word):
                return True
            if (r < 0 or r >= rows or c < 0 or c >= cols or
                    board[r][c] != word[idx]):
                return False

            temp = board[r][c]
            board[r][c] = '#'

            found = (dfs(r+1, c, idx+1) or dfs(r-1, c, idx+1) or
                     dfs(r, c+1, idx+1) or dfs(r, c-1, idx+1))

            board[r][c] = temp
            return found

        for r in range(rows):
            for c in range(cols):
                if board[r][c] == word[0] and dfs(r, c, 0):
                    return True
        return False
```

### Walkthrough
Using board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "SEE":
```
Frequency check: S=2, E=3 in board, S=1, E=2 in word -> OK
Try each cell matching 'S':
  (0,3) 'S'? No, word may be reversed. Let's say word stays "SEE"
  No match at (0,0)-(0,2)
  (0,3): board[0][3]='E' != 'S', skip
  (1,0): board[1][0]='S' matches!
    Mark '#', look for 'E' at idx=1
    neighbors: (2,0)='A' no, (0,0)='A' no, (1,1)='F' no -> fail, restore 'S'
  (1,3): board[1][3]='S' matches!
    Mark '#', look for 'E' at idx=1
    (1,2)='C' no, (0,3)='E' yes!
      Mark '#', look for 'E' at idx=2
      (0,2)='C' no, (1,3)='#' no
      Restore 'E' at (0,3)
    (2,3)='E' yes!
      Mark '#', look for 'E' at idx=2
      (2,2)='E' yes!
        idx=3 == len(word) -> return True!
```

## Complexity Analysis

| Aspect | Value |
|--------|-------|
| Time | O(M * N * 3^L) -- start from each cell, branch 3 ways at each step |
| Space | O(L) for recursion stack |
| Why 3^L not 4^L | We came from one direction, so only 3 new directions to explore |

## Common Mistakes

1. **Forgetting to restore the cell**: After recursing, you must set `board[r][c] = temp`. Without this, other paths cannot use that cell.
2. **Not checking all starting positions**: You must try starting DFS from every cell, not just the first match.
3. **Off-by-one in base case**: Return True when `idx == len(word)`, not when `idx == len(word) - 1`.
4. **Modifying board permanently**: If using in-place marking, make sure to restore even when returning True (though short-circuiting may skip restoration -- this is acceptable if the function returns immediately).
5. **Using BFS instead of DFS**: BFS does not naturally support backtracking for this problem.

## Interview Tips

- **Start with the brute force idea**: "I'll try DFS from each cell, exploring all 4 directions."
- **Mention in-place marking**: This shows you understand space optimization.
- **Discuss the pruning**: Character frequency check is a powerful optimization that can short-circuit impossible cases.
- **Know the complexity**: Explain why it is 3^L not 4^L (we don't go back the way we came).
- **Connect to Word Search II**: Mention that for multiple words, you'd use a Trie to share prefixes.

## Mnemonics / Memory Aids

- **"Mark, Explore, Restore"**: The three steps at each cell -- mark visited, explore neighbors, restore original value.
- **"3 not 4"**: From any cell (except the starting cell), you only have 3 new directions since you came from one.
- **"Frequency first"**: Always check if the board even has enough characters before starting DFS.

## Self-Assessment Checklist

- [ ] Can I write the DFS backtracking solution from scratch?
- [ ] Do I correctly handle the visited/unvisited marking?
- [ ] Can I explain the time complexity (M*N*3^L)?
- [ ] Do I know the in-place marking technique (replacing with '#')?
- [ ] Can I implement the frequency pruning optimization?
- [ ] Can I trace through a small example step by step?
- [ ] Do I understand why BFS doesn't work well for this problem?
