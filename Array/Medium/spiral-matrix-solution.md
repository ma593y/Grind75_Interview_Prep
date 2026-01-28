# Spiral Matrix

## Problem Information
- **Difficulty**: Medium
- **Category**: Array
- **Tags**: Array, Matrix, Simulation
- **LeetCode Link**: https://leetcode.com/problems/spiral-matrix/

## Problem Statement
Given an `m x n` matrix, return all elements of the matrix in spiral order.

### Examples
**Example 1:**
```
Input: matrix = [[1,2,3],[4,5,6],[7,8,9]]
Output: [1,2,3,6,9,8,7,4,5]
```

**Example 2:**
```
Input: matrix = [[1,2,3,4],[5,6,7,8],[9,10,11,12]]
Output: [1,2,3,4,8,12,11,10,9,5,6,7]
```

### Constraints
- `m == matrix.length`
- `n == matrix[i].length`
- `1 <= m, n <= 10`
- `-100 <= matrix[i][j] <= 100`

## Core Concept / Pattern
This problem tests your ability to simulate a process with careful boundary management. The key insight is to maintain four boundaries (top, bottom, left, right) and traverse in four directions (right, down, left, up) while shrinking the boundaries after each direction is completed.

## Approach 1: Simulation with Boundaries

### Intuition
Traverse the matrix layer by layer, starting from the outermost layer and working inward. For each layer, traverse right along the top row, down along the right column, left along the bottom row, and up along the left column.

### Algorithm
1. Initialize four boundaries: top, bottom, left, right
2. While boundaries are valid:
   - Traverse right (left to right on top row), then increment top
   - Traverse down (top to bottom on right column), then decrement right
   - If top <= bottom: traverse left (right to left on bottom row), then decrement bottom
   - If left <= right: traverse up (bottom to top on left column), then increment left
3. Return the result list

### Implementation
```python
class Solution:
    def spiralOrder(self, matrix: list[list[int]]) -> list[int]:
        if not matrix or not matrix[0]:
            return []

        result = []
        top, bottom = 0, len(matrix) - 1
        left, right = 0, len(matrix[0]) - 1

        while top <= bottom and left <= right:
            # Traverse right
            for col in range(left, right + 1):
                result.append(matrix[top][col])
            top += 1

            # Traverse down
            for row in range(top, bottom + 1):
                result.append(matrix[row][right])
            right -= 1

            # Traverse left (if there's still a row)
            if top <= bottom:
                for col in range(right, left - 1, -1):
                    result.append(matrix[bottom][col])
                bottom -= 1

            # Traverse up (if there's still a column)
            if left <= right:
                for row in range(bottom, top - 1, -1):
                    result.append(matrix[row][left])
                left += 1

        return result
```

### Complexity Analysis
- **Time Complexity**: O(m × n) - we visit each element exactly once
- **Space Complexity**: O(1) - excluding the output array

### Why We Can Do Better
This is already optimal. We must visit every element at least once, giving us O(m × n) lower bound.

## Approach 2: Direction Vectors (Alternative)

### Intuition
Use direction vectors to control movement and turn right when hitting a boundary or visited cell.

### Algorithm
1. Define direction vectors for right, down, left, up
2. Start at (0, 0), move in current direction
3. When next cell is invalid (out of bounds or visited), turn right
4. Mark cells as visited using a set or by modifying the matrix
5. Continue until all cells are visited

### Implementation
```python
class Solution:
    def spiralOrder(self, matrix: list[list[int]]) -> list[int]:
        if not matrix or not matrix[0]:
            return []

        m, n = len(matrix), len(matrix[0])
        result = []
        visited = set()

        # Direction vectors: right, down, left, up
        directions = [(0, 1), (1, 0), (0, -1), (-1, 0)]
        dir_idx = 0
        row, col = 0, 0

        for _ in range(m * n):
            result.append(matrix[row][col])
            visited.add((row, col))

            # Try next position in current direction
            next_row = row + directions[dir_idx][0]
            next_col = col + directions[dir_idx][1]

            # If invalid, turn right
            if (next_row < 0 or next_row >= m or
                next_col < 0 or next_col >= n or
                (next_row, next_col) in visited):
                dir_idx = (dir_idx + 1) % 4
                next_row = row + directions[dir_idx][0]
                next_col = col + directions[dir_idx][1]

            row, col = next_row, next_col

        return result
```

### Complexity Analysis
- **Time Complexity**: O(m × n)
- **Space Complexity**: O(m × n) for the visited set

## Edge Cases

| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Single element | Return that element | Minimal input |
| Single row | Return row left to right | Tests horizontal traversal only |
| Single column | Return column top to bottom | Tests vertical traversal only |
| Empty matrix | Return empty list | Handle invalid input |

## Related Problems

| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Spiral Matrix II | Generate matrix in spiral order | Medium |
| Spiral Matrix III | Walk in spiral from starting point | Medium |
| Diagonal Traverse | Different traversal pattern | Medium |
