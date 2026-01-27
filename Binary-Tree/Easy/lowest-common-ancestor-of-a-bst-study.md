# Lowest Common Ancestor of a BST - Study Guide

## Problem Summary
- **Difficulty**: Easy
- **Category**: Binary-Tree
- **Tags**: Tree, BST, DFS, Binary Search Tree
- **LeetCode Link**: https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/

> **One-liner**: Find the deepest node in a BST that is an ancestor of both p and q, using the BST ordering property to guide the search.

## Key Intuition
In a BST, the LCA is the **split point** where p and q diverge into different subtrees. If both values are smaller than the current node, go left. If both are larger, go right. Otherwise, you have found the LCA. This works because BST ordering guarantees that once the values split, they cannot reconverge at a deeper node.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| Path comparison (ignore BST) | O(n) | O(n) | Works for any tree, wastes BST property |
| BST-guided iterative | O(h) | O(1) | Optimal, leverages BST ordering |
| BST-guided recursive | O(h) | O(h) | Clean code, uses call stack |

## Approach 1: Path Comparison
### Core Idea
Find paths from root to p and q, then compare paths to find the last common node.

### Code
```python
class Solution:
    def lowestCommonAncestor(self, root, p, q):
        def findPath(node, target, path):
            if not node:
                return False
            path.append(node)
            if node == target:
                return True
            if findPath(node.left, target, path) or findPath(node.right, target, path):
                return True
            path.pop()
            return False

        path_p, path_q = [], []
        findPath(root, p, path_p)
        findPath(root, q, path_q)

        lca = root
        for i in range(min(len(path_p), len(path_q))):
            if path_p[i] == path_q[i]:
                lca = path_p[i]
            else:
                break
        return lca
```

### Walkthrough
For BST `[6,2,8,0,4,7,9]`, p=2, q=8:
- Path to 2: [6, 2]
- Path to 8: [6, 8]
- Compare: index 0 both have 6, index 1 diverges (2 vs 8)
- LCA = 6

## Approach 2: BST-Guided Iterative (Optimal)
### Core Idea
Walk down from the root. At each node, use BST ordering to decide direction. When p and q split to different sides, you are at the LCA.

### Code
```python
class Solution:
    def lowestCommonAncestor(self, root, p, q):
        current = root
        while current:
            if p.val < current.val and q.val < current.val:
                current = current.left
            elif p.val > current.val and q.val > current.val:
                current = current.right
            else:
                return current
```

### Walkthrough
For BST `[6,2,8,0,4,7,9]`, p=2, q=8:
1. current=6: p(2) < 6, q(8) > 6 -> split! Return 6.

For p=2, q=4:
1. current=6: both 2 and 4 < 6 -> go left
2. current=2: p(2) == current -> split (2 is not < 2 and not > 2) -> Return 2.

## Complexity Analysis
- Iterative BST approach: O(h) time, O(1) space where h is tree height.
- For balanced BST: h = O(log n), so this runs in O(log n) time.
- For skewed BST: h = O(n), worst case.

## Common Mistakes
1. **Not using BST property**: Writing a general LCA solution when the BST structure gives a simpler approach.
2. **Forgetting a node can be its own ancestor**: The split condition naturally handles this (when current equals p or q, neither both-left nor both-right condition is true).
3. **Using strict less-than only**: The conditions should be `<` and `>` (not `<=` and `>=`), since when current equals p or q, we should return it.

## Interview Tips
- **How to communicate**: Immediately mention that this is a BST, so you can leverage ordering. Draw the tree and trace through the "split point" logic.
- **Clarifying questions to ask**: "Are p and q guaranteed to exist in the tree?" "Are all values unique?"
- **Optimization path**: Mention the general LCA approach first, then explain how the BST property simplifies it from O(n) to O(h) with O(1) space.
- **Follow-up variations**: "What if this were a general binary tree?" (LeetCode #236 - requires post-order traversal). "What if nodes had parent pointers?" (Intersection of two linked lists approach).

## Mnemonics / Memory Aids
- **"Split point"**: The LCA is where p and q "split" into different subtrees. Walk down the tree until they diverge.
- **"Both left, both right, or here"**: Three cases: both go left, both go right, or we found the answer.

## Self-Assessment Checklist
- [ ] Can I identify this problem type within 2 minutes?
- [ ] Can I code the optimal solution without looking at notes?
- [ ] Can I explain the time/space complexity clearly?
- [ ] Can I handle all edge cases?
- [ ] Can I discuss trade-offs between approaches?
