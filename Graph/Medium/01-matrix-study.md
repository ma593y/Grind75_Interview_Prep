# 01 Matrix - Study Guide

## Problem Summary
- **Difficulty**: Medium
- **Category**: Graph
- **Tags**: BFS, Matrix, Dynamic Programming, Multi-source BFS
- **LeetCode Link**: https://leetcode.com/problems/01-matrix/

> **One-liner**: Given a binary matrix, find the distance of the nearest `0` for each cell using multi-source BFS or DP.

## Key Intuition
Instead of searching from each `1` to the nearest `0` (expensive), flip the perspective: start BFS from ALL `0` cells simultaneously. This is **multi-source BFS** -- every `0` is a source at distance 0, and the BFS wavefront naturally computes the shortest distance to the nearest `0` for every `1` cell. Alternatively, a two-pass DP approach propagates minimum distances from top-left then bottom-right.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Brute Force BFS (per cell) | O((m*n)^2) | O(m * n) | Simple but extremely slow |
| Multi-source BFS | O(m * n) | O(m * n) | Optimal, clean BFS pattern |
| Dynamic Programming (2-pass) | O(m * n) | O(1) extra | No queue needed, in-place |

## Approach 1: Brute Force (BFS from each 1-cell)
### Core Idea
For every cell with value `1`, run a separate BFS to find the nearest `0`. Each BFS is O(m*n), so total is O((m*n)^2).

### Code
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

### Walkthrough
Using `mat = [[0,0,0],[0,1,0],[1,1,1]]`:
1. Cells (0,0), (0,1), (0,2), (1,0), (1,2) are `0` -- distance = 0.
2. Cell (1,1) is `1`: BFS finds (0,1) at distance 1. Result = 1.
3. Cell (2,0) is `1`: BFS finds (1,0) at distance 1. Result = 1.
4. Cell (2,1) is `1`: BFS finds (1,0) or (1,2) at distance 2. Result = 2.
5. Cell (2,2) is `1`: BFS finds (1,2) at distance 1. Result = 1.
6. Output: `[[0,0,0],[0,1,0],[1,2,1]]`.

## Approach 2: Multi-Source BFS (Optimal)
### Core Idea
Enqueue all `0` cells at distance 0. BFS processes cells level by level, and the first time a `1` cell is reached, that distance is guaranteed to be the shortest path to any `0`.

### Code
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

### Walkthrough
Same input `mat = [[0,0,0],[0,1,0],[1,1,1]]`:
1. Enqueue all `0` cells: (0,0), (0,1), (0,2), (1,0), (1,2). Mark `1` cells as -1.
2. Process (0,0): neighbor (1,0) is already 0, skip. No -1 neighbors.
3. Process (0,1): neighbor (1,1) is -1. Set dist[1][1] = 0+1 = 1. Enqueue (1,1).
4. Process (0,2): neighbor (1,2) is already 0, skip.
5. Process (1,0): neighbor (2,0) is -1. Set dist[2][0] = 0+1 = 1. Enqueue (2,0).
6. Process (1,2): neighbor (2,2) is -1. Set dist[2][2] = 0+1 = 1. Enqueue (2,2).
7. Process (1,1): neighbor (2,1) is -1. Set dist[2][1] = 1+1 = 2. Enqueue (2,1).
8. Process (2,0), (2,2), (2,1): no unvisited neighbors.
9. Output: `[[0,0,0],[0,1,0],[1,2,1]]`.

## Complexity Analysis
| Approach | Time | Space |
|----------|------|-------|
| Brute Force BFS | O((m*n)^2) | O(m * n) |
| Multi-source BFS | O(m * n) | O(m * n) |
| DP (2-pass) | O(m * n) | O(1) extra |

## Common Mistakes
1. **Starting BFS from `1` cells instead of `0` cells**: This leads to the brute force approach. Reverse the perspective.
2. **Not initializing `1` cells to infinity or -1**: Without a sentinel, you cannot tell if a cell has been visited.
3. **Using DFS instead of BFS**: DFS does not guarantee shortest path. BFS is required here.
4. **Forgetting to mark cells as visited before enqueuing**: This causes duplicate processing and incorrect distances.

## Interview Tips
- Immediately recognize the pattern: "nearest distance to any X" is multi-source BFS with all X as sources.
- Mention the DP alternative as a follow-up to show depth of knowledge.
- Explain why BFS guarantees shortest distance but DFS does not.
- Compare with Rotting Oranges (LC #994) -- same multi-source BFS pattern but with a time-step twist.

## Mnemonics / Memory Aids
- **"Zeros are springs, ones are dry land"**: Water (BFS wavefront) flows from all springs simultaneously.
- **"Reverse the search"**: Do not search from 1 to 0. Search from 0 to 1.
- **"Multi-source = multi-enqueue"**: Put all sources in the queue at the start, then run standard BFS.

## Self-Assessment Checklist
- [ ] Can I identify this as a multi-source BFS problem within 2 minutes?
- [ ] Can I write the multi-source BFS solution without notes?
- [ ] Can I explain why BFS gives shortest distances but DFS does not?
- [ ] Can I describe the DP alternative approach?
- [ ] Can I trace through a small example step by step?
