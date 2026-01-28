# Accounts Merge - Study Guide

## Problem Summary
- **Difficulty**: Medium
- **Category**: Graph
- **Tags**: Array, String, DFS, BFS, Union Find, Sorting
- **LeetCode Link**: https://leetcode.com/problems/accounts-merge/

> **One-liner**: Merge accounts that share common emails by finding connected components using Union Find or DFS, then output each group with sorted emails.

## Key Intuition
Two accounts belong to the same person if they share any email. This is transitive: if account A shares an email with B, and B shares with C, then A, B, and C all belong to the same person. This is exactly the connected components problem. Each email is a node, and emails in the same account are connected. We find all connected components and group the emails.

## Approaches Overview
| Approach | Time | Space | Trade-off |
|----------|------|-------|-----------|
| DFS on Email Graph | O(NK log NK) | O(NK) | Build explicit graph, straightforward DFS |
| Union Find | O(NK alpha(NK) + NK log NK) | O(NK) | No graph construction, elegant merging |

(N = number of accounts, K = max emails per account)

## Approach 1: DFS on Email Graph
### Core Idea
Build an adjacency list connecting emails within the same account. Run DFS from each unvisited email to collect connected components. Sort and format each component.

### Code
```python
from collections import defaultdict

class Solution:
    def accountsMerge(self, accounts: List[List[str]]) -> List[List[str]]:
        graph = defaultdict(set)
        email_to_name = {}

        for account in accounts:
            name = account[0]
            for email in account[1:]:
                graph[account[1]].add(email)
                graph[email].add(account[1])
                email_to_name[email] = name

        visited = set()
        result = []

        def dfs(email, component):
            visited.add(email)
            component.append(email)
            for neighbor in graph[email]:
                if neighbor not in visited:
                    dfs(neighbor, component)

        for email in email_to_name:
            if email not in visited:
                component = []
                dfs(email, component)
                component.sort()
                result.append([email_to_name[email]] + component)

        return result
```

### Walkthrough
Accounts: [["John","a@","b@"], ["John","a@","c@"], ["Mary","d@"]]
- Graph: a@<->b@, a@<->c@ (a@ connects both accounts)
- DFS from a@: visits a@, b@, c@ -> component = ["a@","b@","c@"]
- DFS from d@: visits d@ -> component = ["d@"]
- Result: [["John","a@","b@","c@"], ["Mary","d@"]]

## Approach 2: Union Find (Optimal)
### Core Idea
Assign each email a numeric ID. For each account, union the first email with all others. After processing, group emails by their root. Sort and prepend the name.

### Code
```python
from collections import defaultdict

class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]

    def union(self, x, y):
        px, py = self.find(x), self.find(y)
        if px == py:
            return
        if self.rank[px] < self.rank[py]:
            px, py = py, px
        self.parent[py] = px
        if self.rank[px] == self.rank[py]:
            self.rank[px] += 1

class Solution:
    def accountsMerge(self, accounts: List[List[str]]) -> List[List[str]]:
        email_to_id = {}
        email_to_name = {}
        next_id = 0

        for account in accounts:
            name = account[0]
            for email in account[1:]:
                if email not in email_to_id:
                    email_to_id[email] = next_id
                    next_id += 1
                email_to_name[email] = name

        uf = UnionFind(next_id)
        for account in accounts:
            first_id = email_to_id[account[1]]
            for email in account[2:]:
                uf.union(first_id, email_to_id[email])

        groups = defaultdict(list)
        for email, eid in email_to_id.items():
            groups[uf.find(eid)].append(email)

        return [[email_to_name[emails[0]]] + sorted(emails) for emails in groups.values()]
```

### Walkthrough
Accounts: [["John","a@","b@"], ["John","a@","c@"], ["Mary","d@"]]
- email_to_id: {a@:0, b@:1, c@:2, d@:3}
- Account 1: union(0,1) -> parent: [0,0,2,3]
- Account 2: union(0,2) -> parent: [0,0,0,3]
- Account 3: no unions needed (single email)
- Groups: {0: [a@, b@, c@], 3: [d@]}
- Result: [["John","a@","b@","c@"], ["Mary","d@"]]

## Complexity Analysis
- **Time**: O(NK * alpha(NK)) for union-find operations + O(NK * log(NK)) for sorting. Dominated by sorting.
- **Space**: O(NK) for all data structures.
- alpha(NK) is the inverse Ackermann function, effectively constant.

## Common Mistakes
1. **Assuming same name = same person**: The problem explicitly states different people can have the same name. Only shared emails indicate the same person.
2. **Not connecting all emails in an account**: Must union or connect ALL emails within each account, not just consecutive pairs.
3. **Forgetting to sort the output emails**: The problem requires emails in sorted order.
4. **Using email index instead of a global ID in Union Find**: Emails need globally unique IDs, not per-account indices.

## Interview Tips
- **How to communicate**: "This is a connected components problem. Emails in the same account are connected. Shared emails across accounts create transitive connections. I will use Union Find to merge accounts."
- **Clarifying questions**: "Can two people have the same name?" (Yes.) "Are emails unique within an account?" (Yes, implied.)
- **Why Union Find shines here**: It naturally handles the merging operation. As we process each account, we simply union all its emails together. The transitive merging happens automatically.
- **DFS vs Union Find**: Both work. DFS requires building an explicit graph. Union Find is more space-efficient and conceptually cleaner for "merge" operations.

## Mnemonics / Memory Aids
- **"Shared email = same person"**: The merging criterion is shared emails, not shared names.
- **"Union emails, Find roots, Group by root"**: The three steps of the Union Find approach.
- **"First email is the anchor"**: Union each account's first email with all others in that account.

## Self-Assessment Checklist
- [ ] Can I implement Union Find with path compression and union by rank from memory?
- [ ] Can I explain why this is a connected components problem?
- [ ] Can I handle the case where same-name accounts are different people?
- [ ] Can I trace through the union operations step by step?
- [ ] Can I discuss the DFS alternative and its trade-offs?
