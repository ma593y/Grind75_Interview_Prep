# Minimum Height Trees

## Problem Information
- **Difficulty**: Medium
- **Category**: Graph
- **Tags**: Graph, BFS, Topological Sort, Tree
- **LeetCode Link**: https://leetcode.com/problems/minimum-height-trees/

## Problem Statement
A tree is an undirected graph in which any two vertices are connected by exactly one path. In other words, any connected graph without simple cycles is a tree.

Given a tree of `n` nodes labelled from `0` to `n - 1`, and an array of `n - 1` edges where `edges[i] = [ai, bi]` indicates that there is an undirected edge between the two nodes `ai` and `bi` in the tree, you can choose any node of the tree as the root. When you select a node `x` as the root, the result tree has height `h`. Among all possible rooted trees, those with minimum height (i.e., `min(h)`) are called **minimum height trees** (MHTs).

Return a list of all MHTs' root labels. You can return the answer in any order.

### Examples
**Example 1:**
```
Input: n = 4, edges = [[1,0],[1,2],[1,3]]
Output: [1]
Explanation: Height when rooted at each node:
  Node 0: height 2    Node 1: height 1 (minimum)
  Node 2: height 2    Node 3: height 2

        0           1          2          3
        |         / | \        |          |
        1        0  2  3       1          1
       / \                    / \        / \
      2   3                  0   3      0   2
```

**Example 2:**
```
Input: n = 6, edges = [[3,0],[3,1],[3,2],[3,4],[5,4]]
Output: [3,4]

    0               4
    |              / \
    3---4---5     3   5
   /|            /|\
  1 2           0 1 2

Both node 3 and node 4 produce trees of height 2 (minimum).
```

### Constraints
- `1 <= n <= 2 * 10^4`
- `edges.length == n - 1`
- `0 <= ai, bi < n`
- `ai != bi`
- All the pairs `(ai, bi)` are distinct.
- The given input is **guaranteed** to be a tree.

## Core Concept / Pattern
The roots of minimum height trees are the **centroids** of the tree -- the 1 or 2 nodes that are most central. We can find them by repeatedly removing all leaf nodes (nodes with degree 1) from the outside inward, similar to peeling an onion. The last remaining 1 or 2 nodes are the centroids. This is analogous to Kahn's algorithm applied to an undirected tree.

## Approach 1: Brute Force (BFS from Every Node)
### Intuition
For each node, compute the height of the tree if that node were the root (using BFS or DFS). Return the node(s) with the minimum height.

### Algorithm
1. Build an adjacency list.
2. For each node, run BFS to find the height of the tree rooted at that node.
3. Track the minimum height and collect all nodes that achieve it.

### Implementation
```python
from typing import List
from collections import deque, defaultdict

class Solution:
    def findMinHeightTrees(self, n: int, edges: List[List[int]]) -> List[int]:
        if n == 1:
            return [0]

        graph = defaultdict(list)
        for u, v in edges:
            graph[u].append(v)
            graph[v].append(u)

        def bfs_height(root: int) -> int:
            visited = {root}
            queue = deque([root])
            height = -1
            while queue:
                height += 1
                for _ in range(len(queue)):
                    node = queue.popleft()
                    for neighbor in graph[node]:
                        if neighbor not in visited:
                            visited.add(neighbor)
                            queue.append(neighbor)
            return height

        min_height = float('inf')
        heights = []
        for node in range(n):
            h = bfs_height(node)
            heights.append(h)
            min_height = min(min_height, h)

        return [node for node, h in enumerate(heights) if h == min_height]
```

### Complexity Analysis
- **Time Complexity**: O(n^2). We run BFS (O(n)) from each of the n nodes.
- **Space Complexity**: O(n) for the BFS structures.

### Why We Can Do Better
Instead of trying every root, we can observe that the MHT roots are always the centroids of the tree. We can find them in O(n) by leaf-peeling.

## Approach 2: Optimal Solution (Topological Leaf-Peeling)
### Intuition
Leaves (degree-1 nodes) can never be MHT roots (except for trivial cases) because moving the root one step inward always reduces height. Repeatedly remove all current leaves (reduce their neighbors' degrees). The last 1 or 2 remaining nodes are the centroids.

### Algorithm
1. Build adjacency list and compute degrees.
2. Collect all initial leaves (degree 1).
3. While more than 2 nodes remain:
   - Remove all current leaves (decrement n by the number of leaves).
   - For each removed leaf, reduce its neighbor's degree. If the neighbor becomes a leaf, add it to the next batch.
4. Return the remaining nodes.

### Implementation
```python
from typing import List
from collections import deque, defaultdict

class Solution:
    def findMinHeightTrees(self, n: int, edges: List[List[int]]) -> List[int]:
        if n == 1:
            return [0]
        if n == 2:
            return [0, 1]

        graph = defaultdict(set)
        for u, v in edges:
            graph[u].add(v)
            graph[v].add(u)

        # Collect initial leaves
        leaves = deque()
        for node in range(n):
            if len(graph[node]) == 1:
                leaves.append(node)

        remaining = n

        while remaining > 2:
            remaining -= len(leaves)
            new_leaves = deque()

            for leaf in leaves:
                # Each leaf has exactly one neighbor
                neighbor = graph[leaf].pop()
                graph[neighbor].remove(leaf)
                if len(graph[neighbor]) == 1:
                    new_leaves.append(neighbor)

            leaves = new_leaves

        return list(leaves)
```

### Complexity Analysis
- **Time Complexity**: O(n). Each node and edge is processed at most once.
- **Space Complexity**: O(n) for the graph and the leaf queues.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| n = 1, no edges | Return [0] | Single node is trivially the root |
| n = 2, one edge | Return [0, 1] | Both nodes produce height 1 |
| Star graph (one center) | Return [center] | All leaves connect to one node |
| Path graph (line) | Return middle 1 or 2 nodes | Linear peeling from both ends |
| n = 3, path 0-1-2 | Return [1] | Middle node is centroid |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Course Schedule (#207) | Kahn's algorithm (similar leaf-peeling idea) | Medium |
| Longest Path in Tree (Tree Diameter) | Related centroid concept | -- |
| Sum of Distances in Tree (#834) | Tree centrality analysis | Hard |
| Find the Town Judge (#997) | Degree-based graph analysis | Easy |
