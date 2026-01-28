# Minimum Height Trees - Study Guide

## Problem Summary
- **Difficulty**: Medium
- **Category**: Graph
- **Tags**: Graph, BFS, Topological Sort, Tree
- **LeetCode Link**: https://leetcode.com/problems/minimum-height-trees/

> **One-liner**: Find the root(s) that produce the minimum height tree by iteratively peeling leaf nodes inward until 1 or 2 centroid nodes remain.

## Key Intuition
The roots of minimum height trees are the **centroids** of the tree. A leaf node is always a bad root choice because moving one step inward reduces the height. By repeatedly removing all leaf nodes (nodes with degree 1), we peel the tree from the outside in. The last 1 or 2 nodes remaining are the centroids -- the optimal roots.

This is similar to Kahn's algorithm but applied to an undirected tree. A tree always has either 1 or 2 centroids.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| BFS from Every Node | O(n^2) | O(n) | Simple but too slow for large n |
| Topological Leaf-Peeling | O(n) | O(n) | Optimal, elegant inward peeling |

## Approach 1: BFS from Every Node
### Core Idea
Try each node as root, compute tree height using BFS, return nodes with minimum height.

### Code
```python
from collections import deque, defaultdict

class Solution:
    def findMinHeightTrees(self, n: int, edges: List[List[int]]) -> List[int]:
        if n == 1:
            return [0]

        graph = defaultdict(list)
        for u, v in edges:
            graph[u].append(v)
            graph[v].append(u)

        def bfs_height(root):
            visited = {root}
            queue = deque([root])
            height = -1
            while queue:
                height += 1
                for _ in range(len(queue)):
                    node = queue.popleft()
                    for nei in graph[node]:
                        if nei not in visited:
                            visited.add(nei)
                            queue.append(nei)
            return height

        heights = [bfs_height(i) for i in range(n)]
        min_h = min(heights)
        return [i for i, h in enumerate(heights) if h == min_h]
```

### Walkthrough
For path graph 0-1-2-3-4:
- BFS from 0: height=4. From 1: height=3. From 2: height=2. From 3: height=3. From 4: height=4.
- Min height = 2 at node 2. Return [2].

## Approach 2: Topological Leaf-Peeling (Optimal)
### Core Idea
Repeatedly remove all leaves. When 2 or fewer nodes remain, those are the centroids.

### Code
```python
from collections import deque, defaultdict

class Solution:
    def findMinHeightTrees(self, n: int, edges: List[List[int]]) -> List[int]:
        if n <= 2:
            return list(range(n))

        graph = defaultdict(set)
        for u, v in edges:
            graph[u].add(v)
            graph[v].add(u)

        leaves = deque(node for node in range(n) if len(graph[node]) == 1)
        remaining = n

        while remaining > 2:
            remaining -= len(leaves)
            new_leaves = deque()
            for leaf in leaves:
                neighbor = graph[leaf].pop()
                graph[neighbor].remove(leaf)
                if len(graph[neighbor]) == 1:
                    new_leaves.append(neighbor)
            leaves = new_leaves

        return list(leaves)
```

### Walkthrough
Path graph 0-1-2-3-4 (5 nodes):
- Initial degrees: 0:1, 1:2, 2:2, 3:2, 4:1. Leaves = [0, 4]. remaining=5.
- Round 1: Remove 0 and 4. remaining=3. Node 1 becomes leaf (degree 1), node 3 becomes leaf (degree 1). leaves=[1, 3].
- Round 2: Remove 1 and 3. remaining=1. Node 2 becomes leaf. leaves=[2].
- remaining <= 2, return **[2]**.

Star graph (center=1, leaves=0,2,3):
- Leaves = [0, 2, 3]. remaining=4.
- Round 1: Remove 0, 2, 3. remaining=1. Node 1 has degree 0. leaves=[1].
- Return **[1]**.

## Complexity Analysis
- **Brute Force**: O(n^2) time -- BFS from each of n nodes.
- **Leaf-Peeling**: O(n) time -- each edge processed once during peeling. O(n) space for the graph and queues.

## Common Mistakes
1. **Forgetting n=1 or n=2 base cases**: n=1 returns [0]. n=2 returns [0, 1]. Both are trivial.
2. **Using a list instead of a set for the adjacency list**: Removal from a list is O(degree) vs O(1) for a set. Use sets for efficient removal.
3. **Not understanding why at most 2 centroids**: A tree has at most 2 centroids. If the longest path has odd length, there is 1 centroid. If even, there are 2.
4. **Confusing with Kahn's algorithm**: Kahn's is for directed graphs (in-degree). This is for undirected trees (degree), but the peeling logic is similar.

## Interview Tips
- **How to communicate**: "Leaves are never optimal roots because moving inward reduces height. I iteratively remove all leaves until 1-2 nodes remain. These centroids are the MHT roots."
- **Clarifying questions**: "Is the graph guaranteed to be a tree?" (Yes.) "Can n be 1?" (Yes.)
- **Key insight**: A tree has at most 2 centroids. They lie at the middle of the longest path (diameter).
- **Connection to tree diameter**: The centroids are the midpoint(s) of the tree's diameter path.

## Mnemonics / Memory Aids
- **"Peel the onion"**: Remove outer layers (leaves) until you reach the core (centroid).
- **"At most 2 centroids"**: A tree always has 1 or 2 centroids.
- **"Leaf = bad root"**: A leaf is always the worst possible root choice (maximizes height).

## Self-Assessment Checklist
- [ ] Can I explain why leaves are never MHT roots?
- [ ] Can I implement the leaf-peeling algorithm from memory?
- [ ] Can I handle edge cases (n=1, n=2)?
- [ ] Can I prove that a tree has at most 2 centroids?
- [ ] Can I relate this to Kahn's algorithm and topological sort?
