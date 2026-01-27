# Grind 75 - Study Guide

## Pattern Progression

Study these patterns in order. Each builds on the previous:

1. **Array & Hashing** - Foundation for lookups and frequency counting
2. **Two Pointers** - Search space reduction on sorted data
3. **Sliding Window** - Builds on two pointers for subarray/substring problems
4. **Binary Search** - Divide and conquer on sorted data
5. **Stack** - LIFO processing, parsing, and monotonic patterns
6. **Linked List** - Pointer manipulation fundamentals
7. **Binary Tree** - Recursive thinking and tree traversals
8. **Graph** - Generalized tree traversal (BFS/DFS on arbitrary structures)
9. **Dynamic Programming** - Optimization via overlapping subproblems
10. **Backtracking** - Exhaustive search with pruning
11. **Heap** - Priority-based processing and top-K patterns
12. **Trie** - Specialized string/prefix structures

---

## Recommended Study Order

### Phase 1: Foundations (Easy problems)

Build core skills with the most fundamental problems first.

| Order | Problem | Category | Difficulty |
|-------|---------|----------|------------|
| 1 | Two Sum | Array | Easy |
| 2 | Contains Duplicate | Array | Easy |
| 3 | Valid Anagram | String | Easy |
| 4 | Ransom Note | Hash-Table | Easy |
| 5 | Valid Palindrome | String | Easy |
| 6 | Longest Palindrome | Hash-Table | Easy |
| 7 | Best Time to Buy and Sell Stock | Array | Easy |
| 8 | Majority Element | Array | Easy |
| 9 | Add Binary | String | Easy |
| 10 | Binary Search | Binary-Search | Easy |
| 11 | First Bad Version | Binary-Search | Easy |
| 12 | Valid Parentheses | Stack | Easy |
| 13 | Implement Queue using Stacks | Stack | Easy |
| 14 | Merge Two Sorted Lists | Linked-List | Easy |
| 15 | Linked List Cycle | Linked-List | Easy |
| 16 | Reverse Linked List | Linked-List | Easy |
| 17 | Middle of the Linked List | Linked-List | Easy |
| 18 | Maximum Depth of Binary Tree | Binary-Tree | Easy |
| 19 | Invert Binary Tree | Binary-Tree | Easy |
| 20 | Balanced Binary Tree | Binary-Tree | Easy |
| 21 | Diameter of Binary Tree | Binary-Tree | Easy |
| 22 | Lowest Common Ancestor of a BST | Binary-Tree | Easy |
| 23 | Flood Fill | Graph | Easy |
| 24 | Climbing Stairs | Dynamic-Programming | Easy |

### Phase 2: Core Patterns (Medium problems - Part 1)

Build on foundations with the core algorithmic patterns.

| Order | Problem | Category | Difficulty |
|-------|---------|----------|------------|
| 25 | Maximum Subarray | Array | Medium |
| 26 | Insert Interval | Array | Medium |
| 27 | Product of Array Except Self | Array | Medium |
| 28 | Merge Intervals | Array | Medium |
| 29 | Sort Colors | Array | Medium |
| 30 | Spiral Matrix | Array | Medium |
| 31 | String to Integer (atoi) | String | Medium |
| 32 | Longest Palindromic Substring | String | Medium |
| 33 | Time Based Key-Value Store | Hash-Table | Medium |
| 34 | LRU Cache | Hash-Table | Medium |
| 35 | 3Sum | Two-Pointers | Medium |
| 36 | Container With Most Water | Two-Pointers | Medium |
| 37 | Longest Substring Without Repeating Characters | Sliding-Window | Medium |
| 38 | Find All Anagrams in a String | Sliding-Window | Medium |
| 39 | Search in Rotated Sorted Array | Binary-Search | Medium |

### Phase 3: Core Patterns (Medium problems - Part 2)

Stack, tree, and graph mastery.

| Order | Problem | Category | Difficulty |
|-------|---------|----------|------------|
| 40 | Evaluate Reverse Polish Notation | Stack | Medium |
| 41 | Min Stack | Stack | Medium |
| 42 | Binary Tree Level Order Traversal | Binary-Tree | Medium |
| 43 | Validate Binary Search Tree | Binary-Tree | Medium |
| 44 | Lowest Common Ancestor of a Binary Tree | Binary-Tree | Medium |
| 45 | Binary Tree Right Side View | Binary-Tree | Medium |
| 46 | Construct Binary Tree from Preorder and Inorder | Binary-Tree | Medium |
| 47 | Kth Smallest Element in a BST | Binary-Tree | Medium |
| 48 | 01 Matrix | Graph | Medium |
| 49 | Clone Graph | Graph | Medium |
| 50 | Course Schedule | Graph | Medium |
| 51 | Number of Islands | Graph | Medium |
| 52 | Rotting Oranges | Graph | Medium |
| 53 | Accounts Merge | Graph | Medium |
| 54 | Minimum Height Trees | Graph | Medium |

### Phase 4: Advanced Patterns (Medium problems - Part 3)

DP, backtracking, heap, and trie patterns.

| Order | Problem | Category | Difficulty |
|-------|---------|----------|------------|
| 55 | Coin Change | Dynamic-Programming | Medium |
| 56 | Word Break | Dynamic-Programming | Medium |
| 57 | Partition Equal Subset Sum | Dynamic-Programming | Medium |
| 58 | Unique Paths | Dynamic-Programming | Medium |
| 59 | Combination Sum | Backtracking | Medium |
| 60 | Permutations | Backtracking | Medium |
| 61 | Subsets | Backtracking | Medium |
| 62 | Word Search | Backtracking | Medium |
| 63 | Letter Combinations of a Phone Number | Backtracking | Medium |
| 64 | K Closest Points to Origin | Heap | Medium |
| 65 | Task Scheduler | Heap | Medium |
| 66 | Implement Trie (Prefix Tree) | Trie | Medium |

### Phase 5: Hard Problems

The final challenge. Each builds on patterns from earlier phases.

| Order | Problem | Category | Difficulty | Prereqs |
|-------|---------|----------|------------|---------|
| 67 | Minimum Window Substring | Sliding-Window | Hard | Find All Anagrams |
| 68 | Trapping Rain Water | Two-Pointers | Hard | Container With Most Water |
| 69 | Largest Rectangle in Histogram | Stack | Hard | Min Stack |
| 70 | Serialize and Deserialize Binary Tree | Binary-Tree | Hard | Level Order Traversal |
| 71 | Find Median from Data Stream | Heap | Hard | K Closest Points |
| 72 | Merge k Sorted Lists | Heap | Hard | Merge Two Sorted Lists |
| 73 | Word Ladder | Graph | Hard | Course Schedule |
| 74 | Maximum Profit in Job Scheduling | Dynamic-Programming | Hard | Coin Change |
| 75 | Basic Calculator | String | Hard | Evaluate Reverse Polish Notation |

---

## Key Pattern Cheat Sheet

| Pattern | When to Use | Key Signal |
|---------|-------------|------------|
| Hash Map | Need O(1) lookup, counting frequencies | "find pair", "count", "duplicate" |
| Two Pointers | Sorted array, pair finding, partitioning | Sorted input, "pair", "three elements" |
| Sliding Window | Contiguous subarray/substring optimization | "substring", "subarray", "consecutive", "window" |
| Binary Search | Sorted data, minimize/maximize with monotonic condition | Sorted input, "minimum/maximum that satisfies" |
| Stack | Matching pairs, nearest greater/smaller, parsing | Parentheses, nested structure, "next greater" |
| BFS | Shortest path, level-by-level traversal | "shortest", "minimum steps", "level order" |
| DFS | Exhaustive search, path finding, tree traversal | "all paths", "exists path", tree problems |
| Dynamic Programming | Optimal substructure + overlapping subproblems | "minimum cost", "number of ways", "maximum profit" |
| Backtracking | Generate all combinations/permutations/subsets | "all combinations", "all permutations", "generate" |
| Heap | Top-K, merge sorted, streaming median | "kth largest", "top k", "merge k sorted" |
| Union Find | Connected components, grouping | "connected", "group", "merge accounts" |
| Trie | Prefix matching, autocomplete | "prefix", "starts with", "word dictionary" |
