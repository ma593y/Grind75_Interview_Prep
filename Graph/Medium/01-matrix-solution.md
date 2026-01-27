# 01 Matrix

## Problem Information
- **Difficulty**: Medium
- **Category**: Graph
- **Tags**: BFS, Matrix, Dynamic Programming, Multi-source BFS
- **LeetCode Link**: https://leetcode.com/problems/01-matrix/

## Problem Statement
Given an `m x n` binary matrix `mat`, return the distance of the nearest `0` for each cell.

The distance between two adjacent cells is `1`.

### Examples
**Example 1:**
```
Input: mat = [[0,0,0],[0,1,0],[0,0,0]]
Output: [[0,0,0],[0,1,0],[0,0,0]]
```

**Example 2:**
```
Input: mat = [[0,0,0],[0,1,0],[1,1,1]]
Output: [[0,0,0],[0,1,0],[1,2,1]]
```

### Constraints
- `m == mat.length`
- `n == mat[i].length`
- `1 <= m, n <= 10^4`
- `1 <= m * n <= 10^4`
- `mat[i][j]` is either `0` or `1`.
- There is at least one `0` in `mat`.

## Core Concept / Pattern
This problem asks for shortest distances from every cell to the nearest `0`. The key insight is to reverse the perspective: instead of running BFS from each `1` cell (expensive), start BFS simultaneously from ALL `0` cells. This is called **multi-source BFS**. All `0` cells are sources at distance 0, and BFS naturally computes the shortest distance to the nearest `0` for every other cell.

An alternative approach uses dynamic programming with two passes: top-left to bottom-right, then bottom-right to top-left. Each pass considers two of the four directions, and together they cover all paths.

## Approach 1: Brute Force (BFS from each 1-cell)
### Intuition
For every cell containing `1`, run a separate BFS to find the nearest `0`. This is correct but extremely slow for large grids.

### Algorithm
1. For each cell `(i, j)` where `mat[i][j] == 1`, run BFS to find the nearest `0`.
2. Record the BFS distance as the result for that cell.
3. Cells with `0` have distance `0`.

### Implementation
```python
from collections import deque

def updateMatrix(mat: list[list[int]]) -> list[list[int]]:
    rows, cols = len(mat), len(mat[0])
    result = [[0] * cols for _ in range(rows)]

    def bfs(start_r, start_c):
        queue = deque([(start_r, start_c, 0)])
        visited = set()
        visited.add((start_r, start_c))
        while queue:
            r, c, dist = queue.popleft()
            if mat[r][c] == 0:
                return dist
            for dr, dc in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
                nr, nc = r + dr, c + dc
                if 0 <= nr < rows and 0 <= nc < cols and (nr, nc) not in visited:
                    visited.add((nr, nc))
                    queue.append((nr, nc, dist + 1))
        return float('inf')

    for i in range(rows):
        for j in range(cols):
            if mat[i][j] == 1:
                result[i][j] = bfs(i, j)

    return result
```

### Complexity Analysis
- **Time Complexity**: O((m * n)^2) -- BFS from each of the O(m*n) cells, each BFS is O(m*n).
- **Space Complexity**: O(m * n) -- visited set per BFS call.

### Why We Can Do Better
We are repeating work by running independent BFS calls. Multi-source BFS from all `0` cells simultaneously computes all distances in a single pass.

## Approach 2: Multi-Source BFS (Optimal)
### Intuition
Enqueue all `0` cells at once with distance 0. BFS expands outward level by level, and the first time a `1` cell is reached, that is its shortest distance to any `0`. This is the classic multi-source BFS pattern.

### Algorithm
1. Create a result matrix initialized to `0`.
2. Initialize a queue with all `(i, j)` where `mat[i][j] == 0`.
3. Mark all `1` cells with distance `-1` (unvisited).
4. BFS: dequeue `(r, c)`, for each neighbor `(nr, nc)` that is unvisited, set `result[nr][nc] = result[r][c] + 1` and enqueue.
5. Return the result matrix.

### Implementation
```python
from collections import deque

def updateMatrix(mat: list[list[int]]) -> list[list[int]]:
    rows, cols = len(mat), len(mat[0])
    dist = [[0] * cols for _ in range(rows)]
    queue = deque()

    for i in range(rows):
        for j in range(cols):
            if mat[i][j] == 0:
                queue.append((i, j))
            else:
                dist[i][j] = -1  # unvisited

    while queue:
        r, c = queue.popleft()
        for dr, dc in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
            nr, nc = r + dr, c + dc
            if 0 <= nr < rows and 0 <= nc < cols and dist[nr][nc] == -1:
                dist[nr][nc] = dist[r][c] + 1
                queue.append((nr, nc))

    return dist
```

### Complexity Analysis
- **Time Complexity**: O(m * n) -- each cell is enqueued and dequeued exactly once.
- **Space Complexity**: O(m * n) -- for the queue and distance matrix.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| All cells are 0 | Return all zeros | No BFS expansion needed |
| Single 1 surrounded by 0s | Center cell = 1 | Basic distance calculation |
| Single row or column | Linear distances from nearest 0 | Tests 1D behavior |
| Large grid with one 0 in corner | Distances increase diagonally | Tests full grid traversal |
| Checkerboard pattern | All 1s have distance 1 | Every 1 is adjacent to a 0 |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Rotting Oranges (LC #994) | Multi-source BFS on grid | Medium |
| Shortest Path in Binary Matrix (LC #1091) | BFS shortest path on grid | Medium |
| Walls and Gates (LC #286) | Nearly identical multi-source BFS | Medium |
| As Far from Land as Possible (LC #1162) | Multi-source BFS, max distance | Medium |
