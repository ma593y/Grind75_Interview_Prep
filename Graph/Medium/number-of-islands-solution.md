# Number of Islands

## Problem Information
- **Difficulty**: Medium
- **Category**: Graph
- **Tags**: Array, DFS, BFS, Union Find, Matrix
- **LeetCode Link**: https://leetcode.com/problems/number-of-islands/

## Problem Statement
Given an `m x n` 2D binary grid `grid` which represents a map of `'1'`s (land) and `'0'`s (water), return the number of islands.

An **island** is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

### Examples
**Example 1:**
```
Input: grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
Output: 1
```

**Example 2:**
```
Input: grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
Output: 3
```

### Constraints
- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 300`
- `grid[i][j]` is `'0'` or `'1'`.

## Core Concept / Pattern
This is a connected components problem on a 2D grid. Each island is a connected component of `'1'` cells (connected horizontally or vertically). We can count connected components by iterating through the grid and launching a DFS/BFS from each unvisited `'1'` cell to mark the entire island as visited. Each launch increments the island count.

## Approach 1: Brute Force (BFS with Visited Set)
### Intuition
Iterate through every cell. When we find an unvisited `'1'`, start a BFS to visit all connected `'1'` cells (marking them as visited). Each BFS launch represents one new island.

### Algorithm
1. Create a visited set.
2. For each cell `(i, j)` in the grid:
   - If `grid[i][j] == '1'` and `(i, j)` not in visited, increment island count and BFS from `(i, j)`.
3. BFS: Use a queue. Add all unvisited neighboring `'1'` cells to the queue.
4. Return the island count.

### Implementation
```python
from typing import List
from collections import deque

class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        if not grid:
            return 0

        rows, cols = len(grid), len(grid[0])
        visited = set()
        islands = 0

        def bfs(r: int, c: int) -> None:
            queue = deque([(r, c)])
            visited.add((r, c))
            while queue:
                row, col = queue.popleft()
                for dr, dc in [(1, 0), (-1, 0), (0, 1), (0, -1)]:
                    nr, nc = row + dr, col + dc
                    if (0 <= nr < rows and 0 <= nc < cols and
                            grid[nr][nc] == '1' and (nr, nc) not in visited):
                        visited.add((nr, nc))
                        queue.append((nr, nc))

        for i in range(rows):
            for j in range(cols):
                if grid[i][j] == '1' and (i, j) not in visited:
                    bfs(i, j)
                    islands += 1

        return islands
```

### Complexity Analysis
- **Time Complexity**: O(m * n) where m is the number of rows and n is the number of columns. Each cell is visited at most once.
- **Space Complexity**: O(m * n) for the visited set and the BFS queue in the worst case.

### Why We Can Do Better
The visited set uses extra O(m * n) space. We can modify the grid in-place by changing visited `'1'` cells to `'0'` (or another marker), eliminating the need for a separate visited set.

## Approach 2: Optimal Solution (DFS with In-Place Marking)
### Intuition
Instead of maintaining a separate visited set, modify the grid in-place. When we visit a `'1'` cell, change it to `'0'` (sink it). This prevents revisiting. DFS is simpler to implement recursively and avoids the queue overhead.

### Algorithm
1. For each cell `(i, j)` in the grid:
   - If `grid[i][j] == '1'`, increment island count and DFS to sink the entire island.
2. DFS: Set `grid[i][j] = '0'`. Recursively visit all 4 neighbors that are `'1'`.
3. Return the island count.

### Implementation
```python
from typing import List

class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        if not grid:
            return 0

        rows, cols = len(grid), len(grid[0])
        islands = 0

        def dfs(r: int, c: int) -> None:
            if r < 0 or r >= rows or c < 0 or c >= cols or grid[r][c] != '1':
                return
            grid[r][c] = '0'  # sink the land
            dfs(r + 1, c)
            dfs(r - 1, c)
            dfs(r, c + 1)
            dfs(r, c - 1)

        for i in range(rows):
            for j in range(cols):
                if grid[i][j] == '1':
                    dfs(i, j)
                    islands += 1

        return islands
```

### Complexity Analysis
- **Time Complexity**: O(m * n). Each cell is visited at most once.
- **Space Complexity**: O(m * n) worst case for the recursion stack (when the entire grid is land). No extra data structures needed beyond the call stack.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Empty grid | Return 0 | Handle null input |
| All water | Return 0 | No islands exist |
| All land | Return 1 | Entire grid is one island |
| Single cell '1' | Return 1 | Smallest island |
| Single cell '0' | Return 0 | No island |
| Diagonal-only connection | Count as separate islands | Only horizontal/vertical connections count |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Max Area of Island (#695) | Same traversal, track area instead of count | Medium |
| Surrounded Regions (#130) | Connected components with boundary condition | Medium |
| Number of Distinct Islands (#694) | Track island shape during traversal | Medium |
| Rotting Oranges (#994) | Multi-source BFS on grid | Medium |
