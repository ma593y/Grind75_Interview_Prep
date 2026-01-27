# Invert Binary Tree - Study Guide

## Problem Summary
- **Difficulty**: Easy
- **Category**: Binary-Tree
- **Tags**: Tree, DFS, BFS, Recursion, Binary Tree
- **LeetCode Link**: https://leetcode.com/problems/invert-binary-tree/

> **One-liner**: Swap the left and right children of every node in a binary tree.

## Key Intuition
Inverting a binary tree means creating a mirror image. At every node, swap its left and right subtrees. This naturally lends itself to recursion: invert the root's children, then recursively invert each subtree. The base case is a null node, which returns null.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Recursive DFS | O(n) | O(h) | Elegant but uses call stack |
| Iterative BFS | O(n) | O(n) | Avoids deep recursion, uses queue |

## Approach 1: Recursive DFS
### Core Idea
At each node, swap left and right, then recurse on both children. The recursion naturally handles every node in the tree.

### Code
```python
class Solution:
    def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root:
            return None
        root.left, root.right = root.right, root.left
        self.invertTree(root.left)
        self.invertTree(root.right)
        return root
```

### Walkthrough
Given tree `[4, 2, 7, 1, 3, 6, 9]`:
1. At node 4: swap children -> left=7, right=2
2. Recurse left (node 7): swap children -> left=9, right=6
3. Recurse on 9 and 6 (leaf nodes, no children to swap)
4. Recurse right (node 2): swap children -> left=3, right=1
5. Recurse on 3 and 1 (leaf nodes, no children to swap)
6. Result: `[4, 7, 2, 9, 6, 3, 1]`

## Approach 2: Iterative BFS (Optimal)
### Core Idea
Use a queue for level-order traversal. At each node dequeued, swap its children and enqueue non-null children.

### Code
```python
from collections import deque

class Solution:
    def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root:
            return None
        queue = deque([root])
        while queue:
            node = queue.popleft()
            node.left, node.right = node.right, node.left
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        return root
```

### Walkthrough
Queue processing order:
1. Queue: [4] -> Swap 4's children (2<->7), enqueue 7 and 2
2. Queue: [7, 2] -> Swap 7's children (6<->9), enqueue 9 and 6
3. Queue: [2, 9, 6] -> Swap 2's children (1<->3), enqueue 3 and 1
4. Queue: [9, 6, 3, 1] -> All are leaves, nothing to swap

## Complexity Analysis
- Both approaches visit every node exactly once: **O(n) time**.
- Recursive DFS uses O(h) stack space (h = tree height).
- Iterative BFS uses O(w) queue space (w = max width of tree, up to n/2).

## Common Mistakes
1. **Forgetting the base case**: Not checking for `None` leads to attribute errors.
2. **Only swapping values, not subtrees**: You must swap the entire subtree references, not just node values.
3. **Swapping after recursion on original children**: If you recurse before swapping, you process the wrong subtrees. (Post-order also works, but be consistent.)

## Interview Tips
- **How to communicate**: Start by clarifying what "invert" means (mirror/swap children). Draw before and after diagrams.
- **Clarifying questions to ask**: "Should I modify the tree in place or return a new tree?" "Can the tree be empty?"
- **Optimization path**: Start with recursive DFS (simplest to explain), then mention BFS as an alternative that avoids stack overflow for deep trees.
- **Follow-up variations**: "What if you need to check if two trees are inverses of each other?" (Leads to Symmetric Tree problem.) "Can you do it iteratively?"

## Mnemonics / Memory Aids
- **"Swap and recurse"**: The entire algorithm in three words - swap the children, then recurse.
- **Mirror metaphor**: Think of placing a mirror at the root - everything flips left to right.

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?
