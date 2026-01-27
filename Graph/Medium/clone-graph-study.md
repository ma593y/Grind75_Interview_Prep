# Clone Graph - Study Guide

## Problem Summary
- **Difficulty**: Medium
- **Category**: Graph
- **Tags**: DFS, BFS, Hash Map, Graph Traversal
- **LeetCode Link**: https://leetcode.com/problems/clone-graph/

> **One-liner**: Deep copy an undirected graph by traversing it with DFS/BFS and using a hash map to track already-cloned nodes.

## Key Intuition
The core challenge is not traversal itself but handling **cycles**: when you encounter a node you have already visited, you must return the previously created clone instead of creating a new one. A hash map `{original_node: cloned_node}` serves double duty as both a visited set and a clone lookup table. This same pattern applies to cloning any data structure with cycles or shared references.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| DFS with Hash Map | O(V + E) | O(V) | Simple recursive code, risk of stack overflow on deep graphs |
| BFS with Hash Map | O(V + E) | O(V) | Iterative, no stack overflow risk |

## Approach 1: DFS with Hash Map
### Core Idea
Recursively traverse the graph. Before cloning a node, check if it already exists in the hash map. If yes, return the cached clone. If no, create the clone, cache it, then recursively clone all neighbors.

### Code
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

### Walkthrough
Using `adjList = [[2,4],[1,3],[2,4],[1,3]]` (nodes 1-2-3-4 in a cycle):
1. `dfs(1)`: Create clone of 1, store in map. Process neighbors [2, 4].
2. `dfs(2)`: Create clone of 2, store in map. Process neighbors [1, 3].
3. `dfs(1)`: Node 1 already in map -- return cached clone. (Cycle handled!)
4. `dfs(3)`: Create clone of 3, store in map. Process neighbors [2, 4].
5. `dfs(2)`: Already cloned -- return cached clone.
6. `dfs(4)`: Create clone of 4, store in map. Process neighbors [1, 3].
7. `dfs(1)`: Already cloned. `dfs(3)`: Already cloned.
8. Back to step 1: `dfs(4)` for node 1's second neighbor -- already cloned.
9. All clones have correct neighbor lists. Return clone of node 1.

## Approach 2: BFS with Hash Map (Optimal)
### Core Idea
Use a queue for iterative traversal. Clone each node when it is first discovered and add it to the map. When processing a node's neighbors, either retrieve the existing clone or create a new one.

### Code
```python
from collections import deque

class Node:
    def __init__(self, val=0, neighbors=None):
        self.val = val
        self.neighbors = neighbors if neighbors is not None else []

def cloneGraph(node: 'Node') -> 'Node':
    if not node:
        return None

    cloned = {node: Node(node.val)}
    queue = deque([node])

    while queue:
        current = queue.popleft()
        for neighbor in current.neighbors:
            if neighbor not in cloned:
                cloned[neighbor] = Node(neighbor.val)
                queue.append(neighbor)
            cloned[current].neighbors.append(cloned[neighbor])

    return cloned[node]
```

### Walkthrough
Same input (nodes 1-2-3-4):
1. Clone node 1, enqueue it. Map: {1: clone1}.
2. Dequeue 1. Neighbor 2: not cloned, create clone2, enqueue. Neighbor 4: not cloned, create clone4, enqueue. Wire clone1.neighbors = [clone2, clone4].
3. Dequeue 2. Neighbor 1: already cloned. Neighbor 3: not cloned, create clone3, enqueue. Wire clone2.neighbors = [clone1, clone3].
4. Dequeue 4. Neighbor 1: already cloned. Neighbor 3: already cloned. Wire clone4.neighbors = [clone1, clone3].
5. Dequeue 3. Neighbor 2: already cloned. Neighbor 4: already cloned. Wire clone3.neighbors = [clone2, clone4].
6. Queue empty. Return clone1.

## Complexity Analysis
| Approach | Time | Space |
|----------|------|-------|
| DFS | O(V + E) | O(V) for map + O(V) recursion stack |
| BFS | O(V + E) | O(V) for map + O(V) queue |

Both are optimal since every node and edge must be visited at least once.

## Common Mistakes
1. **Not caching the clone before recursing into neighbors**: If you create the clone but do not store it in the map before processing neighbors, a cycle will cause infinite recursion because the node will not be found in the map when revisited.
2. **Creating a new clone every time a node is encountered**: This produces duplicate nodes and breaks the graph structure.
3. **Confusing original nodes with cloned nodes**: Always use the original node as the map key and the clone as the value. Never mix them.
4. **Forgetting the null check**: The input can be `None` (empty graph).

## Interview Tips
- State the key insight early: "I need a map from original to clone to handle cycles."
- Mention that this pattern generalizes to cloning any structure with shared references (linked lists with random pointers, trees with parent pointers, etc.).
- If asked for iterative vs recursive, explain BFS avoids stack overflow on deep graphs.
- Time and space are both O(V + E) and O(V) respectively -- be ready to justify why.

## Mnemonics / Memory Aids
- **"Map before you recurse"**: Always store the clone in the map BEFORE processing neighbors, so cycles hit the cache.
- **"Clone = Visited"**: The hash map is your visited set. If a node is in the map, it has been cloned.
- **"Original -> Clone"**: The map always maps from the original node to its clone. Never the reverse.

## Self-Assessment Checklist
- [ ] Can I identify this as a graph cloning problem within 1 minute?
- [ ] Can I write the DFS solution with hash map from memory?
- [ ] Can I explain why the clone must be cached before recursing?
- [ ] Can I implement the BFS variant as well?
- [ ] Can I trace through a cyclic graph example without confusion?
