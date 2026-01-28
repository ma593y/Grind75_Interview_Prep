# Spiral Matrix - Study Guide

## Problem Summary
- **Difficulty**: Medium
- **Category**: Array
- **Tags**: Array, Matrix, Simulation
- **LeetCode Link**: https://leetcode.com/problems/spiral-matrix/

> **One-liner**: Traverse a matrix in clockwise spiral order, returning elements as a list.

## Key Intuition
Think of the matrix as layers of an onion. Peel each layer by going right → down → left → up, then move to the inner layer. The trick is maintaining four boundaries (top, bottom, left, right) and shrinking them after each direction.

## Approaches Overview

| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Boundary Simulation | O(m×n) | O(1) | Clean and efficient |
| Direction Vectors | O(m×n) | O(m×n) | More flexible but uses more space |

## Approach 1: Boundary Simulation

### Core Idea
Maintain four boundaries and traverse in four directions, shrinking boundaries after each pass.

### Code
```python
class Solution:
    def spiralOrder(self, matrix: list[list[int]]) -> list[int]:
        result = []
        top, bottom = 0, len(matrix) - 1
        left, right = 0, len(matrix[0]) - 1

        while top <= bottom and left <= right:
            for col in range(left, right + 1):
                result.append(matrix[top][col])
            top += 1

            for row in range(top, bottom + 1):
                result.append(matrix[row][right])
            right -= 1

            if top <= bottom:
                for col in range(right, left - 1, -1):
                    result.append(matrix[bottom][col])
                bottom -= 1

            if left <= right:
                for row in range(bottom, top - 1, -1):
                    result.append(matrix[row][left])
                left += 1

        return result
```

### Walkthrough
For matrix `[[1,2,3],[4,5,6],[7,8,9]]`:
1. Right: [1,2,3], top becomes 1
2. Down: [6,9], right becomes 1
3. Left: [8,7], bottom becomes 1
4. Up: [4], left becomes 1
5. Right: [5], done
Result: [1,2,3,6,9,8,7,4,5]

## Approach 2: Direction Vectors (Optimal)

### Core Idea
Use direction vectors (right, down, left, up) and turn right when hitting boundary or visited cell.

### Code
```python
class Solution:
    def spiralOrder(self, matrix: list[list[int]]) -> list[int]:
        m, n = len(matrix), len(matrix[0])
        visited = [[False] * n for _ in range(m)]
        dirs = [(0, 1), (1, 0), (0, -1), (-1, 0)]

        result = []
        row = col = dir_idx = 0

        for _ in range(m * n):
            result.append(matrix[row][col])
            visited[row][col] = True

            nr, nc = row + dirs[dir_idx][0], col + dirs[dir_idx][1]
            if nr < 0 or nr >= m or nc < 0 or nc >= n or visited[nr][nc]:
                dir_idx = (dir_idx + 1) % 4

            row += dirs[dir_idx][0]
            col += dirs[dir_idx][1]

        return result
```

### Walkthrough
Start at (0,0), direction = right. Move right until boundary, turn down. Continue until all visited.

## Complexity Analysis
- **Time**: O(m × n) -- must visit each cell once
- **Space**: O(1) for boundary approach, O(m×n) for direction approach

## Common Mistakes
1. **Off-by-one errors**: Forgetting to check `top <= bottom` before left traversal
2. **Missing boundary checks**: Not checking if row/column still exists before traversing
3. **Infinite loops**: Not updating boundaries after each direction

## Interview Tips
- **How to communicate**: "I'll use four boundaries and traverse in four directions, shrinking boundaries after each pass"
- **Clarifying questions to ask**: "Can the matrix be empty? Can it be non-rectangular (it can't, but good to confirm)?"
- **Optimization path**: Start with boundary approach - it's O(1) space and cleaner
- **Follow-up variations**: "Generate spiral matrix" (Spiral Matrix II), "Start from center"

## Mnemonics / Memory Aids
**RDLU** = Right, Down, Left, Up (clockwise spiral)
**"Shrink after complete"** = Update boundary only after finishing that direction

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?
