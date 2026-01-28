# Binary Tree Right Side View - Study Guide

## Problem Summary
- **Difficulty**: Medium
- **Category**: Binary-Tree
- **Tags**: Tree, BFS, DFS, Binary Tree, Queue
- **LeetCode Link**: https://leetcode.com/problems/binary-tree-right-side-view/

> **One-liner**: Return the values of nodes visible when looking at a binary tree from the right side, ordered top to bottom.

## Key Intuition
The right side view of a binary tree consists of the **last node at each level** when traversed left to right. BFS naturally groups nodes by level, so we can simply take the last element of each level. Alternatively, DFS visiting the right subtree first will encounter the rightmost node at each depth before any left-side nodes.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| BFS (Level Order) | O(n) | O(n) | Intuitive, directly maps to "last node per level" |
| DFS (Right-First) | O(n) | O(h) | Less space for deep narrow trees, slightly less intuitive |

## Approach 1: BFS (Level Order Traversal)
### Core Idea
Perform level-order traversal using a queue. For each level, the last node processed is the one visible from the right side.

### Code
```python
from collections import deque

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

### Walkthrough
Given tree `[1, 2, 3, null, 5, null, 4]`:
```
        1
       / \
      2   3
       \   \
        5   4
```
1. Queue: [1], level_size = 1 -> process 1 (last in level) -> result = [1]
2. Queue: [2, 3], level_size = 2 -> process 2 (not last), process 3 (last) -> result = [1, 3]
3. Queue: [5, 4], level_size = 2 -> process 5 (not last), process 4 (last) -> result = [1, 3, 4]

## Approach 2: DFS (Right-First Traversal) (Optimal)
### Core Idea
Visit the right subtree before the left subtree. Track the current depth. If `depth == len(result)`, this is the first (and thus rightmost) node we have seen at this depth.

### Code
```python
class Solution:
    def rightSideView(self, root: Optional[TreeNode]) -> List[int]:
        result = []

        def dfs(node, depth):
            if not node:
                return
            if depth == len(result):
                result.append(node.val)
            dfs(node.right, depth + 1)
            dfs(node.left, depth + 1)

        dfs(root, 0)
        return result
```

### Walkthrough
Given tree `[1, 2, 3, null, 5, null, 4]`:
1. dfs(1, 0): depth 0 == len([]) -> result = [1]
2. dfs(3, 1): depth 1 == len([1]) -> result = [1, 3]
3. dfs(4, 2): depth 2 == len([1, 3]) -> result = [1, 3, 4]
4. dfs(None, 3): return
5. dfs(None, 2): return (right child of 3)
6. dfs(2, 1): depth 1 != len([1, 3, 4]) (already have depth 1) -> skip
7. dfs(5, 2): depth 2 != len([1, 3, 4]) (already have depth 2) -> skip

## Complexity Analysis
- Both approaches: **O(n) time**, visiting each node exactly once.
- BFS: O(w) space for the queue, where w is the maximum width of the tree.
- DFS: O(h) space for the recursion stack, where h is the height of the tree.
- For balanced trees, w can be up to n/2, so DFS may use less space. For skewed trees, h = n, so both are O(n) worst case.

## Common Mistakes
1. **Assuming right side view = right subtree only**: Left nodes CAN appear in the right side view if the left subtree is deeper than the right subtree.
2. **Not capturing the last node per level in BFS**: Make sure to check `i == level_size - 1`, not `i == 0`.
3. **DFS visiting left before right**: The order matters -- visit right first so the rightmost node at each depth is seen first.
4. **Forgetting the empty tree check**: Return `[]` for a null root.

## Interview Tips
- **How to communicate**: "The right side view is the last node at each level. BFS naturally processes levels, so I take the last node per level. Alternatively, DFS visiting right-first records the first node at each new depth."
- **Clarifying questions**: "Can the tree be empty?" "Are values unique?"
- **Follow-up variations**: "What about the left side view?" (Same approach, take the first node per level or visit left-first in DFS.) "What about a top/bottom view?" (Different technique using horizontal distance.)
- **Why DFS can be optimal**: For deep narrow trees, the recursion stack is O(h) while the queue in BFS would hold O(w) nodes -- for balanced trees, w = n/2.

## Mnemonics / Memory Aids
- **"Right-first DFS, first at each depth"**: Visit right child first; the first node seen at each new depth is the answer.
- **"BFS = last per level"**: In level-order traversal, the rightmost node is simply the last one processed in each level.

## Self-Assessment Checklist
- [ ] Can I identify this as a level-order / tree traversal problem within 2 minutes?
- [ ] Can I code both BFS and DFS solutions without looking at notes?
- [ ] Can I explain why left-subtree nodes might appear in the right side view?
- [ ] Can I handle the empty tree edge case?
- [ ] Can I discuss the space trade-offs between BFS and DFS?
