# Min Stack

## Problem Information
- **Difficulty**: Medium
- **Category**: Stack
- **Tags**: Stack, Design
- **LeetCode Link**: https://leetcode.com/problems/min-stack/

## Problem Statement

Design a stack that supports push, pop, top, and retrieving the minimum element in constant time.

Implement the `MinStack` class:
- `MinStack()` initializes the stack object.
- `void push(int val)` pushes the element `val` onto the stack.
- `void pop()` removes the element on the top of the stack.
- `int top()` gets the top element of the stack.
- `int getMin()` retrieves the minimum element in the stack.

You must implement a solution with `O(1)` time complexity for each function.

### Examples

**Example 1:**
```
Input:
["MinStack","push","push","push","getMin","pop","top","getMin"]
[[],[-2],[0],[-3],[],[],[],[]]
Output: [null,null,null,null,-3,null,0,-2]

Explanation:
MinStack minStack = new MinStack();
minStack.push(-2);   // stack: [-2], min: -2
minStack.push(0);    // stack: [-2, 0], min: -2
minStack.push(-3);   // stack: [-2, 0, -3], min: -3
minStack.getMin();   // return -3
minStack.pop();      // stack: [-2, 0], min: -2
minStack.top();      // return 0
minStack.getMin();   // return -2
```

### Constraints
- `-2^31 <= val <= 2^31 - 1`
- Methods `pop`, `top` and `getMin` operations will always be called on **non-empty** stacks.
- At most `3 * 10^4` calls will be made to `push`, `pop`, `top`, and `getMin`.

## Core Concept / Pattern

The challenge is maintaining the minimum in O(1) time as elements are pushed and popped. A regular stack does not track the minimum. The key insight is that the minimum can only change when we push or pop elements. By storing the minimum alongside each element (or in a parallel stack), we can retrieve the current minimum at any time by looking at the top of the auxiliary structure.

Each entry in the stack records not just the value but also what the minimum was at that point in time. When we pop, the previous minimum is automatically restored because it is stored with the element below.

## Approach 1: Brute Force (Scan on getMin)

### Intuition

Use a regular stack for push/pop/top. For `getMin`, scan through the entire stack to find the minimum element.

### Algorithm

1. `push(val)`: Append `val` to the stack.
2. `pop()`: Remove the last element.
3. `top()`: Return the last element.
4. `getMin()`: Return `min(stack)` by scanning all elements.

### Implementation

```python
class MinStack:
    def __init__(self):
        self.stack = []

    def push(self, val: int) -> None:
        self.stack.append(val)

    def pop(self) -> None:
        self.stack.pop()

    def top(self) -> int:
        return self.stack[-1]

    def getMin(self) -> int:
        return min(self.stack)
```

### Complexity Analysis
- **Time Complexity**: O(1) for push, pop, and top. O(n) for getMin.
- **Space Complexity**: O(n) for storing elements.

### Why We Can Do Better

Scanning the entire stack for every `getMin` call is O(n). By maintaining a parallel min-tracking structure, we can achieve O(1) for all operations.

## Approach 2: Optimal Solution (Two Stacks)

### Intuition

Maintain a second stack (`min_stack`) that tracks the minimum at each level of the main stack. When pushing, the new minimum is `min(val, current_min)`. When popping, both stacks pop together, automatically restoring the previous minimum.

### Algorithm

1. `push(val)`: Push `val` onto the main stack. Push `min(val, min_stack[-1])` onto the min stack (or just `val` if the min stack is empty).
2. `pop()`: Pop from both stacks.
3. `top()`: Return the top of the main stack.
4. `getMin()`: Return the top of the min stack.

### Implementation

```python
class MinStack:
    def __init__(self):
        self.stack = []
        self.min_stack = []

    def push(self, val: int) -> None:
        self.stack.append(val)
        if self.min_stack:
            self.min_stack.append(min(val, self.min_stack[-1]))
        else:
            self.min_stack.append(val)

    def pop(self) -> None:
        self.stack.pop()
        self.min_stack.pop()

    def top(self) -> int:
        return self.stack[-1]

    def getMin(self) -> int:
        return self.min_stack[-1]
```

### Complexity Analysis
- **Time Complexity**: O(1) for all operations -- each operation performs a constant number of stack operations.
- **Space Complexity**: O(n) -- Both stacks store n elements in the worst case.

## Edge Cases

| Edge Case | Expected Behavior | Why It Matters |
|-----------|-------------------|----------------|
| Push duplicate minimums | Both copies tracked correctly | Popping one still leaves the other as minimum |
| Push ascending values | Min stays as the first element | min_stack top does not change |
| Push descending values | Min updates every time | min_stack top changes on each push |
| Single element | All operations return that element | Simplest stack state |
| Negative values | Handled correctly | val range includes negatives |
| Push same value repeatedly | Min is that value, all pops maintain it | Tests duplicate handling |
| Alternating push and pop | Min updates correctly on both operations | Tests synchronization of both stacks |

## Related Problems

| Problem | Relationship | Difficulty |
|---------|-------------|------------|
| Max Stack (LeetCode #716) | Similar but tracks maximum; also supports popMax | Hard |
| Implement Stack using Queues (LeetCode #225) | Stack design problem | Easy |
| Min Queue | Extending the min-tracking idea to a queue | -- |
| Sliding Window Maximum (LeetCode #239) | Tracks extremes in a sliding window using deque | Hard |
