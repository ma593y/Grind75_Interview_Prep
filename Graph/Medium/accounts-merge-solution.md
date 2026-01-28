# Accounts Merge

## Problem Information
- **Difficulty**: Medium
- **Category**: Graph
- **Tags**: Array, String, DFS, BFS, Union Find, Sorting
- **LeetCode Link**: https://leetcode.com/problems/accounts-merge/

## Problem Statement
Given a list of `accounts` where each element `accounts[i]` is a list of strings, where the first element `accounts[i][0]` is a name, and the rest of the elements are **emails** representing emails of the account.

Now, we would like to merge these accounts. Two accounts definitely belong to the same person if there is some common email to both accounts. Note that even if two accounts have the same name, they may belong to different people as people could have the same name. A person can have any number of accounts initially, but all of their accounts definitely have the same name.

After merging the accounts, return the accounts in the following format: the first element of each account is the name, and the rest of the elements are emails in **sorted order**. The accounts themselves can be returned in any order.

### Examples
**Example 1:**
```
Input: accounts = [
  ["John","johnsmith@mail.com","john_newyork@mail.com"],
  ["John","johnsmith@mail.com","john00@mail.com"],
  ["Mary","mary@mail.com"],
  ["John","johnnybravo@mail.com"]
]
Output: [
  ["John","john00@mail.com","john_newyork@mail.com","johnsmith@mail.com"],
  ["Mary","mary@mail.com"],
  ["John","johnnybravo@mail.com"]
]
Explanation: The first and second John's are the same person because they share "johnsmith@mail.com".
The third John is different because none of his emails match the others.
```

**Example 2:**
```
Input: accounts = [
  ["Gabe","Gabe0@m.co","Gabe3@m.co","Gabe1@m.co"],
  ["Kevin","Kevin3@m.co","Kevin5@m.co","Kevin0@m.co"],
  ["Ethan","Ethan5@m.co","Ethan4@m.co","Ethan0@m.co"],
  ["Hanzo","Hanzo3@m.co","Hanzo1@m.co","Hanzo0@m.co"],
  ["Fern","Fern5@m.co","Fern1@m.co","Fern0@m.co"]
]
Output: [
  ["Gabe","Gabe0@m.co","Gabe1@m.co","Gabe3@m.co"],
  ["Kevin","Kevin0@m.co","Kevin3@m.co","Kevin5@m.co"],
  ["Ethan","Ethan0@m.co","Ethan4@m.co","Ethan5@m.co"],
  ["Hanzo","Hanzo0@m.co","Hanzo1@m.co","Hanzo3@m.co"],
  ["Fern","Fern0@m.co","Fern1@m.co","Fern5@m.co"]
]
```

### Constraints
- `1 <= accounts.length <= 1000`
- `2 <= accounts[i].length <= 10`
- `1 <= accounts[i][j].length <= 30`
- `accounts[i][0]` consists of English letters.
- `accounts[i][j]` (for j > 0) is a valid email.

## Core Concept / Pattern
This is a connected components problem. Each email is a node. Emails within the same account are connected. If two accounts share an email, all their emails belong to the same person. We need to find connected components of emails and group them by person. Union Find is the most natural approach, though DFS on an adjacency list also works.

## Approach 1: Brute Force (DFS on Email Graph)
### Intuition
Build a graph where each email is a node and edges connect emails that appear in the same account. Then find connected components using DFS. Each component is one merged account.

### Algorithm
1. Build an adjacency list: for each account, connect the first email to every other email in that account.
2. Map each email to its account name.
3. DFS from each unvisited email to find all emails in the same connected component.
4. Sort the emails in each component and prepend the name.

### Implementation
```python
from typing import List
from collections import defaultdict

class Solution:
    def accountsMerge(self, accounts: List[List[str]]) -> List[List[str]]:
        # Build adjacency list
        graph = defaultdict(set)
        email_to_name = {}

        for account in accounts:
            name = account[0]
            first_email = account[1]
            for email in account[1:]:
                graph[first_email].add(email)
                graph[email].add(first_email)
                email_to_name[email] = name

        # DFS to find connected components
        visited = set()
        result = []

        def dfs(email: str, component: list) -> None:
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

### Complexity Analysis
- **Time Complexity**: O(N * K * log(N * K)) where N is the number of accounts and K is the maximum number of emails per account. The log factor comes from sorting.
- **Space Complexity**: O(N * K) for the graph, visited set, and result.

### Why We Can Do Better
DFS works but Union Find provides a cleaner abstraction for merging sets and is often preferred in interviews for this type of problem.

## Approach 2: Optimal Solution (Union Find)
### Intuition
Assign each email an ID. For each account, union the first email with every other email in the account. After processing all accounts, group emails by their root parent. Sort and format the result.

### Algorithm
1. Map each email to an integer ID and record email-to-name mapping.
2. For each account, union all emails (union the first email with each subsequent one).
3. After all unions, group emails by their root parent.
4. Sort each group, prepend the name, and return.

### Implementation
```python
from typing import List
from collections import defaultdict

class UnionFind:
    def __init__(self, n: int):
        self.parent = list(range(n))
        self.rank = [0] * n

    def find(self, x: int) -> int:
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])  # path compression
        return self.parent[x]

    def union(self, x: int, y: int) -> None:
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

        # Assign IDs and map emails to names
        for account in accounts:
            name = account[0]
            for email in account[1:]:
                if email not in email_to_id:
                    email_to_id[email] = next_id
                    next_id += 1
                email_to_name[email] = name

        # Union Find
        uf = UnionFind(next_id)

        for account in accounts:
            first_id = email_to_id[account[1]]
            for email in account[2:]:
                uf.union(first_id, email_to_id[email])

        # Group emails by root parent
        groups = defaultdict(list)
        for email, eid in email_to_id.items():
            root = uf.find(eid)
            groups[root].append(email)

        # Format result
        result = []
        for root, emails in groups.items():
            emails.sort()
            name = email_to_name[emails[0]]
            result.append([name] + emails)

        return result
```

### Complexity Analysis
- **Time Complexity**: O(N * K * alpha(N * K)) for union-find operations (nearly O(1) per operation) plus O(N * K * log(N * K)) for sorting. Overall dominated by sorting.
- **Space Complexity**: O(N * K) for the union-find structure, mappings, and result.

## Edge Cases
| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Single account | Return that account with sorted emails | No merging needed |
| Same name, no shared emails | Separate accounts | Same name does not mean same person |
| All accounts share an email | Merge into one account | Transitive merging |
| Chain of shared emails (A-B, B-C, C-D) | All merge into one | Tests transitive connectivity |
| Account with one email | Standalone account | Minimum account size |

## Related Problems
| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Number of Connected Components (#323) | Core concept of connected components | Medium |
| Redundant Connection (#684) | Union Find for cycle detection | Medium |
| Smallest String With Swaps (#1202) | Union Find to group indices | Medium |
| Similar String Groups (#839) | Group strings using Union Find | Hard |
