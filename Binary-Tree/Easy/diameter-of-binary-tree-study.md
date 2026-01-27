# Diameter of Binary Tree - Study Guide

## Problem Summary
- **Difficulty**: Easy
- **Category**: Binary-Tree
- **Tags**: Tree, DFS, Recursion, Binary Tree
- **LeetCode Link**: https://leetcode.com/problems/diameter-of-binary-tree/

> **One-liner**: Find the length of the longest path (in edges) between any two nodes in a binary tree, which may or may not pass through the root.

## Key Intuition
The diameter through any node equals the sum of the heights of its left and right subtrees. The answer is the maximum of this value across all nodes. By computing height bottom-up, we can track the maximum diameter as a side effect in a single O(n) pass.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Brute Force (height per node) | O(n^2) | O(n) | Simple but redundant height calls |
| Bottom-Up DFS | O(n) | O(h) | Optimal single-pass, tracks global max |

## Approach 1: Brute Force
### Core Idea
For every node, compute the heights of its left and right subtrees independently, sum them to get the diameter through that node, and take the maximum across all nodes.

### Code
```python
class Solution:
    def diameterOfBinaryTree(self, root: Optional[TreeNode]) -> int:
        def height(node):
            if not node:
                return 0
            return 1 + max(height(node.left), height(node.right))

        def diameter(node):
            if not node:
                return 0
            through = height(node.left) + height(node.right)
            return max(through, diameter(node.left), diameter(node.right))

        return diameter(root)
```

### Walkthrough
Given tree `[1, 2, 3, 4, 5]`:
1. At node 1: height(left=2) = 2, height(right=3) = 1, diameter = 3
2. At node 2: height(left=4) = 1, height(right=5) = 1, diameter = 2
3. At node 3: height(left) = 0, height(right) = 0, diameter = 0
4. Maximum = 3

## Approach 2: Bottom-Up DFS (Optimal)
### Core Idea
Compute height bottom-up via DFS. At each node, update a global maximum with `left_height + right_height`. Return the height to the parent.

### Code
```python
class Solution:
    def diameterOfBinaryTree(self, root: Optional[TreeNode]) -> int:
        self.max_diameter = 0

        def dfs(node):
            if not node:
                return 0
            left = dfs(node.left)
            right = dfs(node.right)
            self.max_diameter = max(self.max_diameter, left + right)
            return 1 + max(left, right)

        dfs(root)
        return self.max_diameter
```

### Walkthrough
Given tree `[1, 2, 3, 4, 5]`:
1. DFS reaches node 4 (leaf): returns height 1, max_diameter = 0
2. DFS reaches node 5 (leaf): returns height 1, max_diameter = 0
3. At node 2: left=1, right=1, diameter_through = 2, max_diameter = 2, returns height 2
4. DFS reaches node 3 (leaf): returns height 1, max_diameter = 2
5. At node 1: left=2, right=1, diameter_through = 3, max_diameter = 3, returns height 3
6. Answer: 3

## Complexity Analysis
- **Brute Force**: O(n^2) time worst case, O(n) space.
- **Optimal**: O(n) time (each node visited once), O(h) space where h is tree height.

## Common Mistakes
1. **Returning height instead of edge count**: The diameter is measured in edges, not nodes. Height naturally counts edges if leaves return 0 and you add 1 per level (which our solution does correctly since we return `1 + max(left, right)`).
2. **Only checking diameter through the root**: The longest path might not go through the root. You must check every node.
3. **Confusing height and diameter**: The function returns height but updates diameter as a side effect. Keep these concepts separate.

## Interview Tips
- **How to communicate**: Clarify that diameter is the number of edges, not nodes. Draw the path to show it may not pass through root.
- **Clarifying questions to ask**: "Is the diameter measured in edges or nodes?" "Can the tree be empty?"
- **Optimization path**: Explain brute force first, identify the repeated height computation, then show how bottom-up DFS solves it in one pass.
- **Follow-up variations**: "What if you need the actual path, not just the length?" "What about the maximum path sum instead of length?"

## Mnemonics / Memory Aids
- **"Height up, diameter across"**: Height is what you return upward. Diameter is the left + right you check at each node.
- **"Track the max as a side effect"**: The DFS computes height, but the real answer accumulates in a separate variable.

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?
