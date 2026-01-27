# Binary Tree Right Side View

## Problem Information
- **Difficulty**: Medium
- **Category**: Binary-Tree
- **Tags**: Tree, BFS, DFS, Binary Tree, Queue
- **LeetCode Link**: https://leetcode.com/problems/binary-tree-right-side-view/

## Problem Statement
Given the `root` of a binary tree, imagine yourself standing on the **right side** of it. Return the values of the nodes you can see ordered from top to bottom.

### Examples
**Example 1:**
```
Input: root = [1,2,3,null,5,null,4]
Output: [1,3,4]

        1       <-- 1
       / \
      2   3     <-- 3
       \   \
        5   4   <-- 4
```

**Example 2:**
```
Input: root = [1,null,3]
Output: [1,3]
```

**Example 3:**
```
Input: root = []
Output: []
```

**Example 4:**
```
Input: root = [1,2,3,4]
Output: [1,3,4]

        1       <-- 1
       / \
      2   3     <-- 3
     /
    4           <-- 4 (visible because no right-side node at this level)
```

### Constraints
- The number of nodes in the tree is in the range `[0, 100]`.
- `-100 <= Node.val <= 100`

## Core Concept / Pattern
The right side view consists of the last node at each level when traversed left to right. This is a direct extension of level-order traversal (BFS), where we simply take the last element of each level. Alternatively, DFS can be used by visiting the right subtree first and recording the first node seen at each new depth.

## Approach 1: BFS (Level Order Traversal)
### Intuition
Perform a standard level-order traversal. For each level, the rightmost node is the one visible from the right side. Simply take the last element of each level.

### Algorithm
1. If root is `None`, return `[]`.
2. Use a queue for BFS.
3. For each level, process all nodes and record the last node's value.
4. Append the last value of each level to the result.

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
    def rightSideView(self, root: Optional[TreeNode]) -> List[int]:
        if not root:
            return []

        result = []
        queue = deque([root])

        while queue:
            level_size = len(queue)
            for i in range(level_size):
                node = queue.popleft()
                if i == level_size - 1:
                    result.append(node.val)
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)

        return result
```

### Complexity Analysis
- **Time Complexity**: O(n) where n is the number of nodes.
- **Space Complexity**: O(n) for the queue (worst case holds an entire level).

### Why We Can Do Better
BFS works perfectly. DFS is an alternative with potentially less space usage for deep, narrow trees (O(h) instead of O(w)).

## Approach 2: DFS (Right-First Traversal)
### Intuition
Visit the right subtree before the left subtree. At each depth, if we haven't seen a node at this depth before, it is the rightmost node. Track the current depth and record the first node encountered at each new depth level.

### Algorithm
1. Initialize a result list.
2. DFS with right child first, then left child.
3. If `depth == len(result)`, this is the first (rightmost) node at this depth. Append its value.
4. Return the result.

### Implementation
```python
from typing import Optional, List

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def rightSideView(self, root: Optional[TreeNode]) -> List[int]:
        result = []

        def dfs(node: Optional[TreeNode], depth: int) -> None:
            if not node:
                return
            if depth == len(result):
                result.append(node.val)
            dfs(node.right, depth + 1)
            dfs(node.left, depth + 1)

        dfs(root, 0)
        return result
```

### Complexity Analysis
- **Time Complexity**: O(n) where n is the number of nodes. Every node is visited.
- **Space Complexity**: O(h) where h is the height of the tree, for the recursion stack.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Empty tree | Return [] | Handle null root |
| Single node | Return [val] | Only one node visible |
| Left-skewed tree | All left nodes visible | No right children, but left nodes are rightmost at each level |
| Right-skewed tree | All right nodes visible | Straightforward case |
| Left subtree deeper than right | Deep left nodes visible | Important: left nodes can appear in right side view |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Binary Tree Level Order Traversal (#102) | Right side view is last element per level | Medium |
| Populating Next Right Pointers (#116) | Similar level-based traversal | Medium |
| Boundary of Binary Tree (#545) | More complex view problem | Medium |
| Find Bottom Left Tree Value (#513) | Left side view of last level | Medium |
