# Number of Islands - Study Guide

## Problem Summary
- **Difficulty**: Medium
- **Category**: Graph
- **Tags**: Array, DFS, BFS, Union Find, Matrix
- **LeetCode Link**: https://leetcode.com/problems/number-of-islands/

> **One-liner**: Count connected components of '1's in a 2D grid by iterating through cells and DFS/BFS-sinking each discovered island.

## Key Intuition
Each island is a connected component of '1' cells (connected horizontally or vertically). We count islands by scanning the grid. When we find a '1', we increment our count and "sink" the entire island (mark all its cells as '0') using DFS or BFS so we never count it again. The number of times we initiate a traversal equals the number of islands.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| BFS with Visited Set | O(m*n) | O(m*n) | Uses extra space for visited set |
| DFS with In-Place Marking | O(m*n) | O(m*n) stack | No extra data structure, modifies input |
| Union Find | O(m*n * alpha(m*n)) | O(m*n) | Best for dynamic connectivity queries |

## Approach 1: BFS with Visited Set
### Core Idea
Use a visited set to track explored cells. For each unvisited '1', BFS to mark all connected land cells as visited. Each BFS launch = one island.

### Code
```python
from collections import deque

class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        if not grid:
            return 0
        rows, cols = len(grid), len(grid[0])
        visited = set()
        islands = 0

        def bfs(r, c):
            queue = deque([(r, c)])
            visited.add((r, c))
            while queue:
                row, col = queue.popleft()
                for dr, dc in [(1,0),(-1,0),(0,1),(0,-1)]:
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

### Walkthrough
Grid:
```
1 1 0
1 0 0
0 0 1
```
1. (0,0)='1', not visited -> BFS: visits (0,0),(0,1),(1,0). islands=1.
2. (0,1) visited, (0,2)='0', (1,0) visited, (1,1)='0', (1,2)='0'.
3. (2,0)='0', (2,1)='0', (2,2)='1' not visited -> BFS: visits (2,2). islands=2.

## Approach 2: DFS with In-Place Marking (Optimal)
### Core Idea
No visited set needed. When DFS visits a '1', change it to '0' directly. This "sinks" the island so it is never counted again.

### Code
```python
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        if not grid:
            return 0
        rows, cols = len(grid), len(grid[0])
        islands = 0

        def dfs(r, c):
            if r < 0 or r >= rows or c < 0 or c >= cols or grid[r][c] != '1':
                return
            grid[r][c] = '0'
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

### Walkthrough
Grid:
```
1 1 0
0 0 1
```
1. (0,0)='1' -> dfs sinks (0,0) and (0,1). Grid becomes `0 0 0 / 0 0 1`. islands=1.
2. (0,1)='0', (0,2)='0', (1,0)='0', (1,1)='0'.
3. (1,2)='1' -> dfs sinks (1,2). islands=2. Final answer: **2**.

## Complexity Analysis
- **Time**: O(m * n) for all approaches. Every cell is visited at most once.
- **Space**: BFS uses O(min(m, n)) for the queue (the BFS frontier on a grid is bounded by the smaller dimension) plus O(m * n) for the visited set. DFS uses O(m * n) in the worst case for the recursion stack (e.g., all land). Union Find uses O(m * n) for the parent array.

## Common Mistakes
1. **Forgetting to mark cells before adding to queue (BFS)**: If you mark cells only when dequeuing, the same cell can be added to the queue multiple times, causing TLE.
2. **Checking only 2 directions instead of 4**: Must check up, down, left, right.
3. **Not handling the empty grid**: Always check `if not grid` first.
4. **Treating diagonal cells as connected**: The problem specifies horizontal and vertical adjacency only.
5. **Grid values are strings, not integers**: `grid[i][j]` is `'1'` (string), not `1` (int).

## Interview Tips
- **How to communicate**: "This is counting connected components on a grid. I will iterate through each cell, and when I find a '1', I DFS to sink the entire island and increment my count."
- **Clarifying questions**: "Can I modify the input grid?" (If yes, use in-place marking. If no, use a visited set.)
- **Common follow-ups**: "What if we cannot modify the grid?" (Use a visited set.) "What about Union Find?" (Good for dynamic connectivity.)
- **Pattern recognition**: Any problem about counting or finding connected regions in a grid is a connected components problem.

## Mnemonics / Memory Aids
- **"Scan, Sink, Count"**: Scan the grid, sink each island with DFS, count each sink operation.
- **"DFS = Depth-First Sinking"**: DFS explores and sinks the entire island.
- **"4 neighbors, not 8"**: Only up/down/left/right, never diagonal.

## Self-Assessment Checklist
- [ ] Can I identify this as a connected components problem immediately?
- [ ] Can I code the DFS in-place solution in under 5 minutes?
- [ ] Can I explain why BFS marking must happen at enqueue time, not dequeue time?
- [ ] Can I handle the edge case of an empty grid?
- [ ] Can I discuss the Union Find alternative and when it is preferable?
