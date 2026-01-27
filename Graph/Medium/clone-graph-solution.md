# Clone Graph

## Problem Information
- **Difficulty**: Medium
- **Category**: Graph
- **Tags**: DFS, BFS, Hash Map, Graph Traversal
- **LeetCode Link**: https://leetcode.com/problems/clone-graph/

## Problem Statement
Given a reference of a node in a **connected** undirected graph, return a **deep copy** (clone) of the graph.

Each node in the graph contains a value (`int`) and a list (`List[Node]`) of its neighbors.

```
class Node:
    def __init__(self, val = 0, neighbors = None):
        self.val = val
        self.neighbors = neighbors if neighbors is not None else []
```

The graph is represented in the test case using an adjacency list. An adjacency list is a collection of unordered lists used to represent a finite graph. Each list describes the set of neighbors of a node in the graph.

The given node will always be the first node with `val = 1`. You must return the copy of the given node as a reference to the cloned graph.

### Examples
**Example 1:**
```
Input: adjList = [[2,4],[1,3],[2,4],[1,3]]
Output: [[2,4],[1,3],[2,4],[1,3]]
Explanation: There are 4 nodes in the graph.
Node 1's neighbors are Node 2 and Node 4.
Node 2's neighbors are Node 1 and Node 3.
Node 3's neighbors are Node 2 and Node 4.
Node 4's neighbors are Node 1 and Node 3.
```

**Example 2:**
```
Input: adjList = [[]]
Output: [[]]
Explanation: The graph consists of a single node with no neighbors.
```

**Example 3:**
```
Input: adjList = []
Output: []
Explanation: The graph is empty (null input).
```

### Constraints
- The number of nodes in the graph is in the range `[0, 100]`.
- `1 <= Node.val <= 100`
- `Node.val` is unique for each node.
- There are no repeated edges and no self-loops in the graph.
- The Graph is connected and all nodes can be visited starting from the given node.

## Core Concept / Pattern
This is a graph traversal problem with a twist: you must create new nodes while traversing and correctly wire up neighbor pointers. The key challenge is handling cycles -- when you encounter a node you have already visited, you must reuse the clone you already created rather than creating a duplicate. A hash map from original node to cloned node serves as both a visited set and a lookup table.

## Approach 1: Brute Force (DFS without memoization)
### Intuition
A naive approach might try to clone nodes without tracking what has already been cloned. In a cyclic graph, this leads to infinite recursion. This approach is not truly viable but illustrates why memoization is essential.

### Algorithm
1. For each node, create a clone and recursively clone its neighbors.
2. Without a visited map, cycles cause infinite recursion.
3. This motivates adding a hash map to track cloned nodes.

### Implementation
```python
# This approach fails on cyclic graphs -- shown for educational purposes only
class Node:
    def __init__(self, val=0, neighbors=None):
        self.val = val
        self.neighbors = neighbors if neighbors is not None else []

def cloneGraph(node: 'Node') -> 'Node':
    if not node:
        return None
    # BUG: no visited tracking causes infinite loop on cycles
    clone = Node(node.val)
    for neighbor in node.neighbors:
        clone.neighbors.append(cloneGraph(neighbor))  # infinite recursion
    return clone
```

### Complexity Analysis
- **Time Complexity**: Does not terminate on cyclic graphs.
- **Space Complexity**: N/A (stack overflow).

### Why We Can Do Better
We need a hash map to remember which nodes have already been cloned. This transforms the approach into a correct DFS with O(V + E) time.

## Approach 2: DFS with Hash Map (Optimal)
### Intuition
Traverse the graph with DFS. For each node encountered, check if it has already been cloned (exists in the hash map). If yes, return the existing clone. If no, create a new clone, store it in the map, then recursively clone all neighbors.

### Algorithm
1. If the input node is `None`, return `None`.
2. Maintain a dictionary `cloned` mapping original nodes to their clones.
3. Define `dfs(node)`:
   - If `node` is in `cloned`, return `cloned[node]`.
   - Create a new `Node(node.val)` and store it in `cloned`.
   - For each neighbor, recursively call `dfs(neighbor)` and append the result to the clone's neighbors.
   - Return the clone.
4. Return `dfs(node)`.

### Implementation
```python
class Node:
    def __init__(self, val=0, neighbors=None):
        self.val = val
        self.neighbors = neighbors if neighbors is not None else []

def cloneGraph(node: 'Node') -> 'Node':
    if not node:
        return None

    cloned = {}

    def dfs(original):
        if original in cloned:
            return cloned[original]

        copy = Node(original.val)
        cloned[original] = copy

        for neighbor in original.neighbors:
            copy.neighbors.append(dfs(neighbor))

        return copy

    return dfs(node)
```

### Complexity Analysis
- **Time Complexity**: O(V + E) -- each node is visited once, each edge is traversed once.
- **Space Complexity**: O(V) -- hash map stores one entry per node, plus O(V) recursion stack.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Null input | Return None | Handle empty graph |
| Single node, no neighbors | Return clone of single node | Minimal graph |
| Two nodes connected to each other | Clone both with correct mutual references | Simple cycle |
| Fully connected graph | All clones correctly cross-reference | Maximum edges |
| Linear chain (A-B-C-D) | Clone chain with correct order | No cycles, tests basic traversal |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Copy List with Random Pointer (LC #138) | Same clone-with-map pattern, linked list | Medium |
| Clone Binary Tree with Random Pointer (LC #1485) | Tree version of clone pattern | Medium |
| Number of Connected Components (LC #323) | Graph traversal fundamentals | Medium |
| Course Schedule (LC #207) | Graph traversal with adjacency list | Medium |
