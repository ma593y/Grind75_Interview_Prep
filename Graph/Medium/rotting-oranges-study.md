# Rotting Oranges - Study Guide

## Problem Summary
- **Difficulty**: Medium
- **Category**: Graph
- **Tags**: Array, BFS, Matrix, Multi-Source BFS
- **LeetCode Link**: https://leetcode.com/problems/rotting-oranges/

> **One-liner**: Find the minimum time for all fresh oranges to rot using multi-source BFS from all initially rotten oranges simultaneously.

## Key Intuition
All rotten oranges spread rot simultaneously, making this a **multi-source BFS** problem. Each BFS "level" represents one minute. We start from all rotten oranges at once and expand outward. The depth of the BFS (number of levels) gives the answer. If any fresh orange is unreachable, we return -1.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Simulation (minute by minute scan) | O((m*n)^2) | O(m*n) | Simple but slow, rescans entire grid each minute |
| Multi-Source BFS | O(m*n) | O(m*n) | Optimal, each cell processed once |

## Approach 1: Simulation
### Core Idea
Each minute, scan the entire grid. For every rotten orange, rot its fresh neighbors. Repeat until no changes. Count minutes.

### Code
```python
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
        return -1 if any(grid[r][c] == 1 for r in range(rows) for c in range(cols)) else minutes
```

### Walkthrough
Grid `[[2,1,1],[1,1,0],[0,1,1]]`:
- Minute 1: (0,0) rots (0,1) and (1,0). Grid: `2 2 1 / 2 1 0 / 0 1 1`
- Minute 2: New rotten cells rot (0,2) and (1,1). Grid: `2 2 2 / 2 2 0 / 0 1 1`
- Minute 3: (1,1) rots (2,1). Grid: `2 2 2 / 2 2 0 / 0 2 1`
- Minute 4: (2,1) rots (2,2). Grid: all rotten. Answer: **4**

## Approach 2: Multi-Source BFS (Optimal)
### Core Idea
Initialize BFS queue with all rotten oranges. Track fresh count. Process level by level (each level = 1 minute). When a fresh orange is rotted, decrement fresh count and add to queue. Final answer is the number of levels if all fresh oranges are rotted.

### Code
```python
from collections import deque

class Solution:
    def orangesRotting(self, grid: List[List[int]]) -> int:
        rows, cols = len(grid), len(grid[0])
        queue = deque()
        fresh = 0

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
                for dr, dc in [(1,0),(-1,0),(0,1),(0,-1)]:
                    nr, nc = r + dr, c + dc
                    if 0 <= nr < rows and 0 <= nc < cols and grid[nr][nc] == 1:
                        grid[nr][nc] = 2
                        fresh -= 1
                        queue.append((nr, nc))
            if queue:
                minutes += 1

        return minutes if fresh == 0 else -1
```

### Walkthrough
Grid `[[2,1,1],[1,1,0],[0,1,1]]`:
- Initial: queue=[(0,0)], fresh=6
- Level 0 (minute 0->1): process (0,0), rot (0,1) and (1,0). fresh=4. queue=[(0,1),(1,0)]. minutes=1.
- Level 1 (minute 1->2): process (0,1)->(0,2), (1,0)->(1,1). fresh=2. minutes=2.
- Level 2 (minute 2->3): process (0,2)->nothing new, (1,1)->(2,1). fresh=1. minutes=3.
- Level 3 (minute 3->4): process (2,1)->(2,2). fresh=0. minutes=4.
- fresh==0, return **4**.

## Complexity Analysis
- **Simulation**: O((m*n)^2) time -- rescans grid each minute, up to O(m*n) minutes.
- **Multi-Source BFS**: O(m*n) time -- each cell enqueued and processed at most once. O(m*n) space for the queue.

## Common Mistakes
1. **Off-by-one in minutes counting**: The answer is the number of BFS levels minus 1 (the initial level is time 0). Alternatively, only increment minutes when new oranges are rotted.
2. **Forgetting the "all already rotten" case**: If fresh == 0 initially, return 0 immediately.
3. **Not checking if fresh oranges remain**: After BFS, if `fresh > 0`, some oranges are unreachable -- return -1.
4. **Single-source BFS instead of multi-source**: Must start from ALL rotten oranges, not just one.

## Interview Tips
- **How to communicate**: "This is multi-source BFS. All rotten oranges spread simultaneously, so I initialize the queue with all of them. Each BFS level is one minute."
- **Clarifying questions**: "Can there be no rotten oranges?" "Can there be no fresh oranges?" "What if the grid is empty?"
- **Key insight to highlight**: Multi-source BFS is just regular BFS but with multiple starting points in the initial queue.
- **Pattern recognition**: Whenever a problem says "minimum time for something to spread from multiple sources," think multi-source BFS.

## Mnemonics / Memory Aids
- **"All rotten start together"**: Initialize BFS with ALL rotten oranges, not just one.
- **"Levels = Minutes"**: Each BFS level corresponds to one unit of time.
- **"Count fresh, check at end"**: Track fresh count during BFS; if still positive at the end, return -1.

## Self-Assessment Checklist
- [ ] Can I recognize this as a multi-source BFS problem?
- [ ] Can I set up multi-source BFS correctly (all sources in initial queue)?
- [ ] Can I count BFS levels without off-by-one errors?
- [ ] Can I handle the edge case of no fresh oranges (return 0)?
- [ ] Can I determine when to return -1 (unreachable fresh oranges)?
