# Balanced Binary Tree - Study Guide

## Problem Summary
- **Difficulty**: Easy
- **Category**: Binary-Tree
- **Tags**: Tree, DFS, Recursion, Binary Tree
- **LeetCode Link**: https://leetcode.com/problems/balanced-binary-tree/

> **One-liner**: Determine if a binary tree is height-balanced, meaning the depths of every node's two subtrees differ by at most one.

## Key Intuition
A tree is balanced if and only if for every node, the height difference between its left and right subtrees is at most 1. The brute force computes height at every node separately (redundant). The optimal approach combines height computation and balance checking in a single bottom-up DFS, using -1 as a sentinel to short-circuit when imbalance is detected.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Top-Down (Brute Force) | O(n log n) to O(n^2) | O(n) | Simple but redundant height calculations |
| Bottom-Up DFS (Optimal) | O(n) | O(h) | Single pass, early termination with sentinel |

## Approach 1: Top-Down (Brute Force)
### Core Idea
For each node, compute the height of left and right subtrees independently, check if the difference is at most 1, and recursively verify both subtrees are balanced. This re-computes heights many times.

### Code
```python
class Solution:
    def isBalanced(self, root: Optional[TreeNode]) -> bool:
        def height(node):
            if not node:
                return 0
            return 1 + max(height(node.left), height(node.right))

        if not root:
            return True
        return (abs(height(root.left) - height(root.right)) <= 1
                and self.isBalanced(root.left)
                and self.isBalanced(root.right))
```

### Walkthrough
Given tree `[3, 9, 20, null, null, 15, 7]`:
1. At node 3: height(left=9) = 1, height(right=20) = 2, diff = 1 -> OK
2. Recurse on node 9: no children, balanced trivially
3. Recurse on node 20: height(left=15) = 1, height(right=7) = 1, diff = 0 -> OK
4. All nodes pass -> return True

## Approach 2: Bottom-Up DFS (Optimal)
### Core Idea
Compute height bottom-up. If any subtree is unbalanced, return -1 as a sentinel. This avoids recomputing heights and allows early termination.

### Code
```python
class Solution:
    def isBalanced(self, root: Optional[TreeNode]) -> bool:
        def check(node):
            if not node:
                return 0
            left = check(node.left)
            if left == -1:
                return -1
            right = check(node.right)
            if right == -1:
                return -1
            if abs(left - right) > 1:
                return -1
            return 1 + max(left, right)

        return check(root) != -1
```

### Walkthrough
Given tree `[1, 2, 2, 3, 3, null, null, 4, 4]`:
1. Bottom-up: node 4 (left) -> returns 1, node 4 (right) -> returns 1
2. Node 3 (left): left=1, right=1, diff=0 -> returns 2
3. Node 3 (right): left=0, right=0, diff=0 -> returns 1
4. Node 2 (left): left=2, right=1, diff=1 -> returns 3
5. Node 2 (right): left=0, right=0, diff=0 -> returns 1
6. Node 1: left=3, right=1, diff=2 > 1 -> returns -1 (unbalanced!)
7. Result: -1 != -1 is False -> tree is not balanced

## Complexity Analysis
- **Top-Down**: O(n log n) time for balanced trees, O(n^2) for skewed trees. O(n) space.
- **Bottom-Up**: O(n) time, O(h) space where h is tree height. Each node visited exactly once.

## Common Mistakes
1. **Only checking the root node**: You must verify balance at every node, not just the root.
2. **Confusing height with depth**: Height is measured from a node downward; depth is from the root downward.
3. **Not using early termination**: Without the sentinel approach, you still visit all nodes even after finding an imbalance.
4. **Forgetting the empty tree case**: An empty tree is balanced by definition.

## Interview Tips
- **How to communicate**: Start by defining what "height-balanced" means. Draw a balanced and unbalanced example.
- **Clarifying questions to ask**: "Can the tree be empty?" "Should I return a boolean or modify the tree?"
- **Optimization path**: Start with the top-down brute force, explain the redundancy, then optimize to bottom-up with sentinel.
- **Follow-up variations**: "How would you rebalance an unbalanced BST?" (Leads to AVL/Red-Black trees.) "Can you find the minimum number of nodes to remove to make it balanced?"

## Mnemonics / Memory Aids
- **"Sentinel -1 means STOP"**: Whenever you see -1 coming back from a child, propagate it immediately.
- **"Bottom-up = single pass"**: Think of it as computing height on the way back up the recursion, checking balance as a side effect.

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?
