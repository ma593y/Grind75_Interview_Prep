# Binary Tree Level Order Traversal

## Problem Information
- **Difficulty**: Medium
- **Category**: Binary-Tree
- **Tags**: Tree, BFS, Binary Tree, Queue
- **LeetCode Link**: https://leetcode.com/problems/binary-tree-level-order-traversal/

## Problem Statement
Given the `root` of a binary tree, return the level order traversal of its nodes' values (i.e., from left to right, level by level).

### Examples
**Example 1:**
```
Input: root = [3,9,20,null,null,15,7]
Output: [[3],[9,20],[15,7]]

      3
     / \
    9  20
      /  \
     15   7
```

**Example 2:**
```
Input: root = [1]
Output: [[1]]
```

**Example 3:**
```
Input: root = []
Output: []
```

### Constraints
- The number of nodes in the tree is in the range `[0, 2000]`.
- `-1000 <= Node.val <= 1000`

## Core Concept / Pattern
Level-order traversal is the canonical BFS problem on trees. We use a queue to process nodes level by level. The key technique is to record the queue size at the start of each level, then process exactly that many nodes before moving to the next level. This separates nodes into their respective levels.

## Approach 1: Recursive DFS
### Intuition
We can also solve this with DFS by passing the current depth as a parameter. Each depth corresponds to a level in the result. We add values to the appropriate sub-list based on depth.

### Algorithm
1. Maintain a result list of lists.
2. DFS with the current node and depth.
3. If the depth equals the length of the result list, append a new empty list.
4. Append the current node's value to `result[depth]`.
5. Recurse on left child, then right child, with `depth + 1`.

### Implementation
```python
from typing import Optional, List

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        result = []

        def dfs(node: Optional[TreeNode], depth: int) -> None:
            if not node:
                return
            if depth == len(result):
                result.append([])
            result[depth].append(node.val)
            dfs(node.left, depth + 1)
            dfs(node.right, depth + 1)

        dfs(root, 0)
        return result
```

### Complexity Analysis
- **Time Complexity**: O(n) where n is the number of nodes.
- **Space Complexity**: O(h) for the recursion stack plus O(n) for the result. Overall O(n).

### Why We Can Do Better
The DFS approach works but is less intuitive for a level-order problem. BFS naturally processes nodes level by level, making the code more readable and the intent clearer.

## Approach 2: Iterative BFS (Optimal)
### Intuition
Use a queue (FIFO). Start with the root in the queue. For each level, record the queue size, process exactly that many nodes (adding their values to the current level list), and enqueue their children for the next level.

### Algorithm
1. If root is `None`, return an empty list.
2. Initialize a queue with the root.
3. While the queue is not empty:
   - Record `level_size = len(queue)`.
   - Create a `level` list.
   - Process `level_size` nodes: dequeue, add value to `level`, enqueue children.
   - Append `level` to the result.
4. Return the result.

### Implementation
```python
from typing import Optional, List
from collections import deque

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if not root:
            return []

        result = []
        queue = deque([root])

        while queue:
            level_size = len(queue)
            level = []

            for _ in range(level_size):
                node = queue.popleft()
                level.append(node.val)

                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)

            result.append(level)

        return result
```

### Complexity Analysis
- **Time Complexity**: O(n) where n is the number of nodes. Every node is visited exactly once.
- **Space Complexity**: O(n) for the queue (at most holds one level of nodes) plus O(n) for the result.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Empty tree | Return [] | Handle null root |
| Single node | Return [[val]] | One level with one node |
| Left-skewed tree | Each level has one node | [[1],[2],[3],...] |
| Perfect binary tree | Levels double in size | Tests queue capacity |
| Only left children | Single-element levels | Asymmetric tree |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Binary Tree Zigzag Level Order Traversal (#103) | Level-order with alternating direction | Medium |
| Binary Tree Right Side View (#199) | Last element of each level | Medium |
| Average of Levels in Binary Tree (#637) | Average instead of list per level | Easy |
| Binary Tree Level Order Traversal II (#107) | Bottom-up level order | Medium |
| N-ary Tree Level Order Traversal (#429) | Generalized to N children | Medium |
