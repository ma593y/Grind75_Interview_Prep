# Flood Fill

## Problem Information
- **Difficulty**: Easy
- **Category**: Graph
- **Tags**: BFS, DFS, Matrix, Recursion
- **LeetCode Link**: https://leetcode.com/problems/flood-fill/

## Problem Statement
An image is represented by an `m x n` integer grid `image` where `image[i][j]` represents the pixel value of the image.

You are also given three integers `sr`, `sc`, and `color`. You should perform a **flood fill** on the image starting from the pixel `image[sr][sc]`.

To perform a flood fill, consider the starting pixel, plus any pixels connected **4-directionally** to the starting pixel of the same color as the starting pixel, plus any pixels connected 4-directionally to those pixels (also with the same color), and so on. Replace the color of all of the aforementioned pixels with `color`.

Return the modified image after performing the flood fill.

### Examples
**Example 1:**
```
Input: image = [[1,1,1],[1,1,0],[1,0,1]], sr = 1, sc = 1, color = 2
Output: [[2,2,2],[2,2,0],[2,0,1]]
Explanation: From the center of the image with position (sr, sc) = (1, 1), all pixels connected
by a path of the same color as the starting pixel (i.e., the pixels with value 1) are colored
with the new color. Note the bottom corner is not colored 2, because it is not 4-directionally
connected to the starting pixel.
```

**Example 2:**
```
Input: image = [[0,0,0],[0,0,0]], sr = 0, sc = 0, color = 0
Output: [[0,0,0],[0,0,0]]
Explanation: The starting pixel is already colored 0, so no changes are made to the image.
```

### Constraints
- `m == image.length`
- `n == image[i].length`
- `1 <= m, n <= 50`
- `0 <= image[i][j], color < 2^16`
- `0 <= sr < m`
- `0 <= sc < n`

## Core Concept / Pattern
Flood Fill is the classic introduction to graph traversal on a 2D grid. The grid itself is an implicit graph where each cell is a node and edges connect to 4-directional neighbors. The problem asks us to visit all cells reachable from a starting cell that share the same original color, and change them to a new color.

This can be solved with either DFS (recursive or iterative) or BFS. DFS is more natural here because the recursive solution maps directly to the "flood" metaphor -- paint the current cell, then recursively paint all valid neighbors. The key edge case is when the starting pixel already has the target color, which would cause infinite recursion without a guard.

## Approach 1: DFS (Recursive)
### Intuition
Starting from the given pixel, recursively visit all 4-directional neighbors that have the same original color. Change each visited pixel to the new color. The recursion naturally explores the entire connected component.

### Algorithm
1. Record the original color of `image[sr][sc]`.
2. If the original color equals the new color, return immediately (no work needed and avoids infinite recursion).
3. Define a recursive function `dfs(r, c)`:
   - Set `image[r][c] = color`.
   - For each 4-directional neighbor `(nr, nc)`, if in bounds and `image[nr][nc] == original_color`, call `dfs(nr, nc)`.
4. Call `dfs(sr, sc)` and return the image.

### Implementation
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

### Complexity Analysis
- **Time Complexity**: O(m * n) -- in the worst case, every pixel has the same color and we visit all of them.
- **Space Complexity**: O(m * n) -- recursion stack depth in the worst case (e.g., a snaking path through the entire grid).

### Why We Can Do Better
This recursive DFS solution is already optimal in time complexity. However, for very large grids, the recursion depth could cause a stack overflow. An iterative BFS approach avoids this issue.

## Approach 2: BFS (Iterative)
### Intuition
Use a queue to perform a breadth-first traversal from the starting pixel. This avoids deep recursion and processes pixels level by level (by distance from the start).

### Algorithm
1. Record the original color. If it equals the new color, return immediately.
2. Initialize a queue with `(sr, sc)`.
3. Change `image[sr][sc]` to the new color.
4. While the queue is not empty:
   - Dequeue `(r, c)`.
   - For each 4-directional neighbor, if in bounds and still the original color, change to new color and enqueue.
5. Return the image.

### Implementation
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

### Complexity Analysis
- **Time Complexity**: O(m * n) -- each pixel visited at most once.
- **Space Complexity**: O(m * n) -- queue can hold all pixels in the worst case, but avoids recursion stack issues.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| New color equals original color | Return image unchanged immediately | Prevents infinite recursion/loop |
| Single pixel image | Color that one pixel and return | Boundary check for minimal input |
| Starting pixel has no same-color neighbors | Only starting pixel changes | Ensures we don't over-expand |
| Entire grid is one color | All pixels change to new color | Tests full grid traversal |
| Starting pixel is at a corner | Only explores 2 initial directions | Tests bounds checking |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Number of Islands (LC #200) | Same grid traversal, counting components | Medium |
| Surrounded Regions (LC #130) | Flood fill from borders | Medium |
| Max Area of Island (LC #695) | DFS/BFS on grid with area tracking | Medium |
| Island Perimeter (LC #463) | Grid traversal with boundary counting | Easy |
