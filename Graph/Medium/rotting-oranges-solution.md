# Rotting Oranges

## Problem Information
- **Difficulty**: Medium
- **Category**: Graph
- **Tags**: Array, BFS, Matrix, Multi-Source BFS
- **LeetCode Link**: https://leetcode.com/problems/rotting-oranges/

## Problem Statement
You are given an `m x n` grid where each cell can have one of three values:
- `0` representing an empty cell,
- `1` representing a fresh orange,
- `2` representing a rotten orange.

Every minute, any fresh orange that is **4-directionally adjacent** to a rotten orange becomes rotten.

Return the minimum number of minutes that must elapse until no cell has a fresh orange. If this is impossible, return `-1`.

### Examples
**Example 1:**
```
Input: grid = [[2,1,1],[1,1,0],[0,1,1]]
Output: 4

Minute 0:  2 1 1    Minute 1:  2 2 1    Minute 2:  2 2 2
           1 1 0               2 1 0               2 2 0
           0 1 1               0 1 1               0 1 1

Minute 3:  2 2 2    Minute 4:  2 2 2
           2 2 0               2 2 0
           0 2 1               0 2 2
```

**Example 2:**
```
Input: grid = [[2,1,1],[0,1,1],[1,0,1]]
Output: -1
Explanation: The orange in the bottom left corner (row 2, column 0) is never reached.
```

**Example 3:**
```
Input: grid = [[0,2]]
Output: 0
Explanation: No fresh oranges at minute 0, so answer is 0.
```

### Constraints
- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 10`
- `grid[i][j]` is `0`, `1`, or `2`.

## Core Concept / Pattern
This is a **multi-source BFS** problem. All rotten oranges act simultaneously as BFS sources. Each BFS "level" represents one minute of elapsed time. We process all currently rotten oranges at each time step, rotting their fresh neighbors. The answer is the number of BFS levels needed. If any fresh orange remains unreachable, return -1.

## Approach 1: Brute Force (Simulation)
### Intuition
Simulate the process minute by minute. Each minute, scan the entire grid for rotten oranges and rot their fresh neighbors. Repeat until no more changes occur. Count the minutes.

### Algorithm
1. Each minute, iterate through every cell.
2. If a cell is rotten, check its 4 neighbors. Mark fresh neighbors as "newly rotten."
3. After scanning, update the grid with newly rotten oranges.
4. If no new oranges rotted, stop.
5. Check if any fresh orange remains.

### Implementation
```python
from typing import List

class Solution:
    def orangesRotting(self, grid: List[List[int]]) -> int:
        rows, cols = len(grid), len(grid[0])
        minutes = 0

        while True:
            newly_rotten = []
            for r in range(rows):
                for c in range(cols):
                    if grid[r][c] == 2:
                        for dr, dc in [(1,0),(-1,0),(0,1),(0,-1)]:
                            nr, nc = r + dr, c + dc
                            if 0 <= nr < rows and 0 <= nc < cols and grid[nr][nc] == 1:
                                newly_rotten.append((nr, nc))

            if not newly_rotten:
                break

            for r, c in newly_rotten:
                grid[r][c] = 2
            minutes += 1

        # Check if any fresh orange remains
        for r in range(rows):
            for c in range(cols):
                if grid[r][c] == 1:
                    return -1

        return minutes
```

### Complexity Analysis
- **Time Complexity**: O((m * n)^2) in the worst case. Each minute scans the entire grid, and there could be O(m * n) minutes.
- **Space Complexity**: O(m * n) for the newly_rotten list.

### Why We Can Do Better
Multi-source BFS processes each cell at most once, reducing the time complexity to O(m * n).

## Approach 2: Optimal Solution (Multi-Source BFS)
### Intuition
Start BFS from all rotten oranges simultaneously. Each BFS level corresponds to one minute. Track the number of fresh oranges. When BFS finishes, if fresh oranges remain, return -1.

### Algorithm
1. Count all fresh oranges. Add all rotten oranges to the queue.
2. BFS level by level. For each rotten orange, rot its fresh neighbors (decrement fresh count, add to queue).
3. Each level = 1 minute. Only increment minutes if fresh oranges were rotted.
4. If fresh count reaches 0, return minutes. Otherwise, return -1.

### Implementation
```python
from typing import List
from collections import deque

class Solution:
    def orangesRotting(self, grid: List[List[int]]) -> int:
        rows, cols = len(grid), len(grid[0])
        queue = deque()
        fresh = 0

        # Initialize: find all rotten oranges and count fresh ones
        for r in range(rows):
            for c in range(cols):
                if grid[r][c] == 2:
                    queue.append((r, c))
                elif grid[r][c] == 1:
                    fresh += 1

        if fresh == 0:
            return 0

        minutes = 0

        while queue:
            level_size = len(queue)
            for _ in range(level_size):
                r, c = queue.popleft()
                for dr, dc in [(1, 0), (-1, 0), (0, 1), (0, -1)]:
                    nr, nc = r + dr, c + dc
                    if 0 <= nr < rows and 0 <= nc < cols and grid[nr][nc] == 1:
                        grid[nr][nc] = 2
                        fresh -= 1
                        queue.append((nr, nc))

            if queue:  # only count if more oranges to process
                minutes += 1

        return minutes if fresh == 0 else -1
```

### Complexity Analysis
- **Time Complexity**: O(m * n). Each cell is visited at most once.
- **Space Complexity**: O(m * n) for the queue in the worst case (all cells are rotten).

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| No fresh oranges | Return 0 | Nothing to rot |
| No rotten oranges but fresh exist | Return -1 | Fresh oranges can never rot |
| All rotten | Return 0 | Already fully rotten |
| Fresh orange unreachable (blocked by 0s) | Return -1 | Not all oranges can be rotted |
| Single cell with fresh orange | Return -1 | No rotten orange to start |
| Single cell with rotten orange | Return 0 | No fresh oranges to rot |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Walls and Gates (#286) | Multi-source BFS on grid | Medium |
| 01 Matrix (#542) | Multi-source BFS for distances | Medium |
| Shortest Path in Binary Matrix (#1091) | BFS shortest path on grid | Medium |
| Number of Islands (#200) | Grid BFS/DFS traversal | Medium |
