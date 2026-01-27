# Flood Fill - Study Guide

## Problem Summary
- **Difficulty**: Easy
- **Category**: Graph
- **Tags**: BFS, DFS, Matrix, Recursion
- **LeetCode Link**: https://leetcode.com/problems/flood-fill/

> **One-liner**: Starting from a given pixel, change all connected same-color pixels to a new color using graph traversal.

## Key Intuition
The grid is an implicit graph where each cell connects to its 4-directional neighbors. Flood fill is simply a connected component traversal: start at a pixel, and visit every reachable pixel with the same original color, repainting as you go. The critical insight is to check whether the new color is the same as the original to avoid infinite loops.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| DFS (Recursive) | O(m * n) | O(m * n) stack | Simplest code, risk of stack overflow on large grids |
| BFS (Iterative) | O(m * n) | O(m * n) queue | No stack overflow risk, slightly more code |

## Approach 1: DFS (Recursive)
### Core Idea
Recursively paint the current cell and explore all 4 neighbors that match the original color. The base cases are: out of bounds, wrong color, or already painted.

### Code
```python
def floodFill(image: list[list[int]], sr: int, sc: int, color: int) -> list[list[int]]:
    original_color = image[sr][sc]
    if original_color == color:
        return image

    rows, cols = len(image), len(image[0])

    def dfs(r, c):
        image[r][c] = color
        for dr, dc in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
            nr, nc = r + dr, c + dc
            if 0 <= nr < rows and 0 <= nc < cols and image[nr][nc] == original_color:
                dfs(nr, nc)

    dfs(sr, sc)
    return image
```

### Walkthrough
Using `image = [[1,1,1],[1,1,0],[1,0,1]], sr=1, sc=1, color=2`:
1. Original color = 1, new color = 2 (different, so proceed).
2. `dfs(1,1)`: Paint (1,1) -> 2. Check neighbors: (0,1)=1, (2,1)=0, (1,0)=1, (1,2)=0.
3. `dfs(0,1)`: Paint (0,1) -> 2. Neighbors: (0,0)=1, (0,2)=1, (1,1)=2 (skip).
4. `dfs(0,0)`: Paint (0,0) -> 2. Neighbors: (1,0)=1.
5. `dfs(1,0)`: Paint (1,0) -> 2. Neighbors: (2,0)=1.
6. `dfs(2,0)`: Paint (2,0) -> 2. No more unvisited same-color neighbors.
7. Back to step 3: `dfs(0,2)`: Paint (0,2) -> 2.
8. Result: `[[2,2,2],[2,2,0],[2,0,1]]`.

## Approach 2: BFS (Iterative) (Optimal)
### Core Idea
Use a queue to process pixels level by level. Paint each pixel as you enqueue it (not when you dequeue) to avoid duplicates.

### Code
```python
from collections import deque

def floodFill(image: list[list[int]], sr: int, sc: int, color: int) -> list[list[int]]:
    original_color = image[sr][sc]
    if original_color == color:
        return image

    rows, cols = len(image), len(image[0])
    queue = deque([(sr, sc)])
    image[sr][sc] = color

    while queue:
        r, c = queue.popleft()
        for dr, dc in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
            nr, nc = r + dr, c + dc
            if 0 <= nr < rows and 0 <= nc < cols and image[nr][nc] == original_color:
                image[nr][nc] = color
                queue.append((nr, nc))

    return image
```

### Walkthrough
Same input as above:
1. Queue = [(1,1)], paint (1,1) -> 2.
2. Process (1,1): enqueue (0,1) and (1,0), paint them -> 2.
3. Process (0,1): enqueue (0,0) and (0,2), paint them -> 2.
4. Process (1,0): enqueue (2,0), paint it -> 2.
5. Process (0,0): no unvisited same-color neighbors.
6. Process (0,2): no unvisited same-color neighbors.
7. Process (2,0): no unvisited same-color neighbors.
8. Queue empty. Done.

## Complexity Analysis
Both approaches visit each cell at most once:
- **Time**: O(m * n) where m and n are grid dimensions.
- **Space**: O(m * n) for the recursion stack (DFS) or queue (BFS).

## Common Mistakes
1. **Forgetting the early return when original == new color**: This causes infinite recursion in DFS or infinite loop in BFS since painted cells still match the "original" color.
2. **Using a visited set when it is unnecessary**: Changing the color in-place already marks cells as visited. Adding a set wastes space.
3. **Checking bounds after accessing the array**: Always check `0 <= nr < rows` before `image[nr][nc]`.
4. **Painting when dequeuing instead of enqueuing (BFS)**: This leads to duplicate entries in the queue and wasted work.

## Interview Tips
- Start by clarifying: "So we need to change all 4-directionally connected pixels of the same original color?" This shows you understand the problem.
- Mention the edge case of `original_color == color` early -- interviewers love seeing you think about edge cases before coding.
- If asked "DFS or BFS?", explain both are O(m*n) but DFS is simpler to write recursively while BFS avoids stack overflow on large grids.
- The 4-direction array `[(0,1),(0,-1),(1,0),(-1,0)]` is a clean pattern -- mention you reuse it across grid problems.

## Mnemonics / Memory Aids
- **"Paint and Spread"**: Paint current cell, spread to neighbors. That is the entire algorithm.
- **"Same? Go. Different? Stop."**: At each neighbor, if same original color, continue; otherwise, stop.
- **"Check before you recurse"**: Always guard the early return for `original == color`.

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?
