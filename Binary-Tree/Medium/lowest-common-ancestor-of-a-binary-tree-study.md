# Lowest Common Ancestor of a Binary Tree - Study Guide

## Problem Summary
- **Difficulty**: Medium
- **Category**: Binary-Tree
- **Tags**: Tree, DFS, Recursion, Binary Tree
- **LeetCode Link**: https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/

> **One-liner**: Find the deepest node in a binary tree that is an ancestor of both given nodes p and q (a node can be its own ancestor).

## Key Intuition
Recursively search both subtrees. If one subtree contains p and the other contains q, the current node is the LCA. If only one side returns a result, the LCA is in that subtree. If the current node itself is p or q, return it immediately since it could be the LCA (the other node must be in its subtree given the problem guarantees).

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Path Finding | O(n) | O(n) | Two passes, stores paths explicitly |
| Recursive DFS | O(n) | O(h) | Single pass, elegant, minimal space |

## Approach 1: Path Finding
### Core Idea
Find the root-to-p path and root-to-q path. Walk both paths from root until they diverge. The last common node is the LCA.

### Code
```python
class Solution:
    def lowestCommonAncestor(self, root: TreeNode, p: TreeNode, q: TreeNode) -> TreeNode:
        def find_path(node, target, path):
            if not node:
                return False
            path.append(node)
            if node == target:
                return True
            if find_path(node.left, target, path) or find_path(node.right, target, path):
                return True
            path.pop()
            return False

        path_p, path_q = [], []
        find_path(root, p, path_p)
        find_path(root, q, path_q)

        lca = root
        for i in range(min(len(path_p), len(path_q))):
            if path_p[i] == path_q[i]:
                lca = path_p[i]
            else:
                break
        return lca
```

### Walkthrough
Given tree with root=3, p=5, q=4:
1. path_p = [3, 5]
2. path_q = [3, 5, 2, 4]
3. Compare: index 0: 3==3 (match), index 1: 5==5 (match), done
4. LCA = 5

## Approach 2: Recursive DFS (Optimal)
### Core Idea
At each node: if it is p or q, return it. Search left and right subtrees. If both return non-null, current node is the LCA. Otherwise, propagate whichever is non-null.

### Code
```python
class Solution:
    def lowestCommonAncestor(self, root: TreeNode, p: TreeNode, q: TreeNode) -> TreeNode:
        if not root or root == p or root == q:
            return root

        left = self.lowestCommonAncestor(root.left, p, q)
        right = self.lowestCommonAncestor(root.right, p, q)

        if left and right:
            return root
        return left if left else right
```

### Walkthrough
Given tree with root=3, p=5, q=1:
1. At node 3: search left and right
2. Left subtree (rooted at 5): node 5 == p -> return 5
3. Right subtree (rooted at 1): node 1 == q -> return 1
4. At node 3: left=5 (non-null), right=1 (non-null) -> both found! Return 3
5. LCA = 3

Given tree with root=3, p=5, q=4:
1. At node 3: search left and right
2. Left subtree (rooted at 5): node 5 == p -> return 5 immediately
3. Right subtree (rooted at 1): search for q=4, not found anywhere -> return None
4. At node 3: left=5, right=None -> return left = 5
5. LCA = 5 (node 4 is in 5's subtree, so 5 is the LCA)

## Complexity Analysis
- Both approaches: **O(n) time** in the worst case.
- Path finding: O(n) space for storing paths.
- Recursive DFS: **O(h) space** for recursion stack only.

## Common Mistakes
1. **Confusing with BST version**: In a general binary tree, you cannot use value comparisons to decide direction. You must search both subtrees.
2. **Not handling the case where one node is the ancestor**: When p is ancestor of q, the recursive approach correctly returns p at the first encounter.
3. **Checking values instead of node identity**: Compare `root == p` (identity), not `root.val == p.val` (value), to avoid false matches with duplicate values.

## Interview Tips
- **How to communicate**: Clearly distinguish this from the BST version. Explain the three return cases of the recursion.
- **Clarifying questions to ask**: "Is this a BST or general binary tree?" "Are p and q guaranteed to exist?" "Are values unique?"
- **Optimization path**: Start with path finding if needed for intuition, then refine to the single-pass recursive solution.
- **Follow-up variations**: "What if nodes have parent pointers?" (Walk up like linked list intersection.) "What if p or q might not exist?" (Need to verify both are found.)

## Mnemonics / Memory Aids
- **"Split means found"**: If left and right both return non-null, the nodes split here, so this is the LCA.
- **"Three lines of logic"**: Base case (null or match), recurse both sides, check split.

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?
