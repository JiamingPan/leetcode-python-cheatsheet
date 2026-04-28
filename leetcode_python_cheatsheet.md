# LeetCode Python Cheat Sheet

This sheet is for pattern recognition, not memorizing problem numbers. The goal is to look at a problem, classify it fast, and drop in a clean Python template you can explain in an interview.

## Pattern Selection Flowchart

Read this top to bottom like a quick flowchart.

| If you see this | Try this first | Quick cue |
|---|---|---|
| Sorted array | Binary Search, Two Pointers | Search a boundary or shrink from both ends |
| All permutations / subsets / combinations | Backtracking | Try a choice, recurse, undo |
| Tree | DFS, BFS | DFS for subtree/path logic, BFS for levels |
| Graph or grid | DFS, BFS, Dijkstra, Topological Sort, Union Find | Traverse, shortest path, dependencies, or connectivity |
| Linked list | Dummy Node, Fast/Slow Pointers, Reversal | Pointer wiring is the whole problem |
| Recursion is banned | Stack | Simulate DFS or nested processing iteratively |
| Must solve in-place | Two Pointers, Swap-to-Index, Reverse Sections, Encode State In-Place | Reuse the array instead of extra memory |
| Maximum / minimum over many choices | DP, Greedy, Sliding Window, Binary Search on Answer | Optimize over states, choices, or feasible answers |
| Top / least K items | Heap, QuickSelect, Bucket Sort | Repeated best item vs one-shot kth |
| Common string / prefix search | HashMap, Trie | Exact lookup vs prefix lookup |
| Subarray / substring | Sliding Window, Prefix Sum + HashMap | Local valid window vs exact cumulative total |
| Else | HashMap / Set, then Sorting | Fast lookup or reveal order |

## Default Fallbacks

- `HashMap / Set`: good when you want `O(1)` average lookup, counting, grouping, or deduplication.
- `Sorting`: good when you want order, adjacency, intervals, or two pointers.
- Interview note: sorting is a standard fallback, but Python `list.sort()` is not strictly `O(1)` extra space.

## HashMap / Set

**Recognition**

- Need fast lookup, counting, grouping, or deduplication.
- Words like "seen", "frequency", "pair", "duplicate", or "same pattern" appear.

**Core Idea**

Trade memory for speed. Store what you need to ask repeatedly: "Have I seen this?", "How many times?", or "What shares this key?"

**Python Template**

```python
from collections import defaultdict

def solve(nums):
    freq = defaultdict(int)
    seen = set()

    for x in nums:
        freq[x] += 1
        if x in seen:
            pass
        seen.add(x)

    return freq, seen
```

**Typical Complexity**

- Time: `O(n)`
- Space: `O(n)`

**Common Mistakes**

- Using a list for membership checks.
- Counting when a set is enough.
- Forgetting keys must be hashable.

**One-Line Memory Rule**

If the same lookup happens again and again, store it in a `dict` or `set`.

## Two Pointers

**Recognition**

- Sorted array, palindrome, pair sum, partition, or in-place compaction.
- You can move one boundary based on what the other boundary sees.

**Core Idea**

Use two indices instead of nested loops. Each move should eliminate impossible answers.

**Python Template**

```python
def two_sum_sorted(nums, target):
    left, right = 0, len(nums) - 1

    while left < right:
        total = nums[left] + nums[right]
        if total == target:
            return [left, right]
        if total < target:
            left += 1
        else:
            right -= 1

    return [-1, -1]
```

**Typical Complexity**

- Time: `O(n)`
- Space: `O(1)`

**Common Mistakes**

- Using it on unsorted data without first sorting or justifying order.
- Moving both pointers when only one should move.
- Forgetting duplicate skipping in problems like `3Sum`.

**One-Line Memory Rule**

If order lets you discard one side at a time, use two pointers.

## Sliding Window

**Recognition**

- Subarray or substring with a local validity rule.
- Need longest, shortest, or count of a valid contiguous range.

**Core Idea**

Grow the right end, shrink the left end, and keep the window state updated incrementally.

**Python Template**

```python
from collections import defaultdict

def length_of_longest_substring(s):
    count = defaultdict(int)
    left = 0
    best = 0

    for right, ch in enumerate(s):
        count[ch] += 1

        while count[ch] > 1:
            count[s[left]] -= 1
            left += 1

        best = max(best, right - left + 1)

    return best
```

**Typical Complexity**

- Time: `O(n)`
- Space: `O(k)` for window state

**Common Mistakes**

- Using sliding window for exact-sum problems with negative numbers.
- Updating the answer before the window is valid again.
- Not writing down what "valid" means.

**One-Line Memory Rule**

Window problems are about maintaining validity, not recomputing it.

## Prefix Sum + HashMap

**Recognition**

- Exact subarray sum, count of subarrays, or range-total queries.
- Negative numbers exist, so shrinking a window is not reliable.

**Core Idea**

Turn a subarray condition into a difference between two prefix sums, then count earlier prefixes with a map.

**Python Template**

```python
from collections import defaultdict

def subarray_sum(nums, k):
    freq = defaultdict(int)
    freq[0] = 1
    prefix = 0
    ans = 0

    for x in nums:
        prefix += x
        ans += freq[prefix - k]
        freq[prefix] += 1

    return ans
```

**Typical Complexity**

- Time: `O(n)`
- Space: `O(n)`

**Common Mistakes**

- Forgetting `freq[0] = 1`.
- Using sliding window when negatives break monotonic behavior.
- Mixing "exactly k" with "at most k".

**One-Line Memory Rule**

Exact subarray total usually becomes prefix difference plus hashmap.

## Stack

**Recognition**

- Nested structure, matching pairs, undo-last, or iterative DFS.
- Recursion is banned but the recursive idea still fits.

**Core Idea**

Use LIFO order when the most recent unfinished work must be handled first.

**Python Template**

```python
def is_valid_parentheses(s):
    pairs = {')': '(', ']': '[', '}': '{'}
    stack = []

    for ch in s:
        if ch in '([{':
            stack.append(ch)
        else:
            if not stack or stack[-1] != pairs[ch]:
                return False
            stack.pop()

    return not stack
```

**Typical Complexity**

- Time: `O(n)`
- Space: `O(n)`

**Common Mistakes**

- Forgetting empty-stack checks.
- Using stack when a counter would be enough.
- Not realizing iterative DFS is just "recursion with your own stack".

**One-Line Memory Rule**

If the last unfinished thing should be solved first, use a stack.

## Monotonic Stack

**Recognition**

- Next greater/smaller, previous greater/smaller, span, or histogram boundaries.
- You need the nearest item with a monotonic relationship.

**Core Idea**

Keep the stack increasing or decreasing so each element is pushed once and popped once.

**Python Template**

```python
def next_greater_elements(nums):
    res = [-1] * len(nums)
    stack = []  # indices; values are decreasing on the stack

    for i, x in enumerate(nums):
        while stack and nums[stack[-1]] < x:
            res[stack.pop()] = x
        stack.append(i)

    return res
```

**Typical Complexity**

- Time: `O(n)`
- Space: `O(n)`

**Common Mistakes**

- Choosing the wrong monotonic direction.
- Storing values when indices are needed.
- Forgetting sentinel logic in histogram-style problems.

**One-Line Memory Rule**

Nearest greater or smaller usually means monotonic stack.

## Binary Search

**Recognition**

- Sorted input, sorted answer space, or first/last valid boundary.
- The condition is monotonic: once true, it stays true.

**Core Idea**

Search the boundary where the answer changes from impossible to possible.

**Python Template**

```python
def first_true(lo, hi, check):
    while lo < hi:
        mid = (lo + hi) // 2
        if check(mid):
            hi = mid
        else:
            lo = mid + 1
    return lo
```

**Typical Complexity**

- Time: `O(log n)` for direct search, or `O(log range * check_cost)` on answers
- Space: `O(1)`

**Common Mistakes**

- No monotonic condition.
- Infinite loops from wrong boundary updates.
- Confusing "searching an index" with "searching an answer".

**One-Line Memory Rule**

Binary search is about a monotonic boundary, not just sorted arrays.

## Intervals

**Recognition**

- Start/end ranges, overlap checks, scheduling, or merge decisions.
- The order of endpoints is more important than the original order.

**Core Idea**

Sort first, then sweep once while merging or counting overlaps.

**Python Template**

```python
def merge(intervals):
    intervals.sort(key=lambda x: x[0])
    merged = []

    for start, end in intervals:
        if not merged or merged[-1][1] < start:
            merged.append([start, end])
        else:
            merged[-1][1] = max(merged[-1][1], end)

    return merged
```

**Typical Complexity**

- Time: `O(n log n)`
- Space: `O(n)` for output

**Common Mistakes**

- Forgetting to sort first.
- Getting the overlap condition wrong for touching intervals.
- Solving an interval problem with nested loops before trying sorting.

**One-Line Memory Rule**

Intervals usually mean sort, then sweep.

## Heap / Priority Queue

**Recognition**

- Need repeated access to the current smallest/largest item.
- Need top `k`, least `k`, k-way merge, or best-first expansion.

**Core Idea**

A heap keeps the next best candidate cheap to insert and cheap to remove.

**Python Template**

```python
import heapq

def top_k_frequent(nums, k):
    freq = {}
    for x in nums:
        freq[x] = freq.get(x, 0) + 1

    heap = []
    for num, count in freq.items():
        heapq.heappush(heap, (count, num))
        if len(heap) > k:
            heapq.heappop(heap)

    return [num for count, num in heap]
```

**Typical Complexity**

- Time: `O(n log k)` for top-`k`
- Space: `O(n)` for counts, `O(k)` for the heap

**Common Mistakes**

- Forgetting Python `heapq` is a min-heap.
- Using a heap when you only need one kth answer once.
- Missing bucket sort as an option when frequencies are bounded.

**One-Line Memory Rule**

If you keep asking "what is the best item right now?", use a heap.

## QuickSelect

**Recognition**

- Need one kth smallest/largest answer from static data.
- Full sorting feels wasteful.

**Core Idea**

Partition like quicksort, but only recurse into the side containing the target index.

**Python Template**

```python
import random

def find_kth_largest(nums, k):
    target = len(nums) - k

    def partition(left, right, pivot_index):
        pivot = nums[pivot_index]
        nums[pivot_index], nums[right] = nums[right], nums[pivot_index]
        store = left

        for i in range(left, right):
            if nums[i] < pivot:
                nums[store], nums[i] = nums[i], nums[store]
                store += 1

        nums[store], nums[right] = nums[right], nums[store]
        return store

    left, right = 0, len(nums) - 1
    while left <= right:
        pivot_index = random.randint(left, right)
        pivot_index = partition(left, right, pivot_index)

        if pivot_index == target:
            return nums[pivot_index]
        if pivot_index < target:
            left = pivot_index + 1
        else:
            right = pivot_index - 1
```

**Typical Complexity**

- Average Time: `O(n)`
- Worst Time: `O(n^2)`
- Space: `O(1)` extra

**Common Mistakes**

- Forgetting kth-largest vs kth-smallest index conversion.
- Using it when you need stable ordering or repeated queries.
- Ignoring worst-case behavior in explanation.

**One-Line Memory Rule**

One kth-element query often means quickselect, not heap.

## In-Place Array Tricks

**Recognition**

- Must use `O(1)` extra space on an array.
- Values can be swapped to their "home" index or used to encode state.

**Core Idea**

Reuse the input array itself: place values where they belong, reverse sections, or encode extra information in signs or offsets.

**Python Template**

```python
def cyclic_sort(nums):
    i = 0

    while i < len(nums):
        j = nums[i] - 1
        if 1 <= nums[i] <= len(nums) and nums[i] != nums[j]:
            nums[i], nums[j] = nums[j], nums[i]
        else:
            i += 1

    return nums
```

**Typical Complexity**

- Time: `O(n)`
- Space: `O(1)`

**Common Mistakes**

- Infinite swap loops from bad guard conditions.
- Forgetting values may be out of range.
- Overwriting information you still need later.

**One-Line Memory Rule**

In-place array problems often mean "put each value where it belongs."

## Linked List

**Recognition**

- Pointer wiring is the hard part.
- Head deletion, cycle detection, middle finding, or reversal appears.

**Core Idea**

Simplify edge cases with a dummy node, fast/slow pointers, or iterative reversal.

**Python Template**

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

def reverse_list(head):
    prev = None
    curr = head

    while curr:
        nxt = curr.next
        curr.next = prev
        prev = curr
        curr = nxt

    return prev
```

**Typical Complexity**

- Time: `O(n)`
- Space: `O(1)`

**Common Mistakes**

- Losing `next` before rewiring.
- Forgetting a dummy node when the head may change.
- Comparing values when node identity matters.

**One-Line Memory Rule**

Most linked list bugs disappear if you save `next` and use a dummy head.

## Tree DFS / BFS

**Recognition**

- The input is a tree and each child subtree matters.
- You need depth, path info, levels, or subtree-combined answers.

**Core Idea**

Use DFS when each node needs information from children. Use BFS when you care about levels or shortest unweighted distance.

**Python Template**

```python
from collections import deque

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

def max_depth(root):
    if not root:
        return 0
    return 1 + max(max_depth(root.left), max_depth(root.right))

def level_order(root):
    if not root:
        return []

    q = deque([root])
    ans = []

    while q:
        level = []
        for _ in range(len(q)):
            node = q.popleft()
            level.append(node.val)
            if node.left:
                q.append(node.left)
            if node.right:
                q.append(node.right)
        ans.append(level)

    return ans
```

**Typical Complexity**

- Time: `O(n)`
- Space: `O(h)` recursion for DFS, `O(w)` queue for BFS

**Common Mistakes**

- Mixing "what the subtree returns" with "what the final answer stores".
- Forgetting base cases.
- Using BFS when a subtree recurrence is simpler.

**One-Line Memory Rule**

DFS solves subtree logic; BFS solves level logic.

## Graph / Grid DFS/BFS

**Recognition**

- Need traversal, component counting, reachability, or unweighted shortest path.
- The state is a cell, node, or coordinate with neighbors.

**Core Idea**

Model states as nodes and legal moves as edges. Use DFS for full exploration and BFS for shortest unweighted steps.

**Python Template**

```python
from collections import deque

def num_islands(grid):
    rows, cols = len(grid), len(grid[0])
    visited = set()

    def bfs(r, c):
        q = deque([(r, c)])
        visited.add((r, c))

        while q:
            x, y = q.popleft()
            for dx, dy in ((1, 0), (-1, 0), (0, 1), (0, -1)):
                nx, ny = x + dx, y + dy
                if 0 <= nx < rows and 0 <= ny < cols:
                    if grid[nx][ny] == '1' and (nx, ny) not in visited:
                        visited.add((nx, ny))
                        q.append((nx, ny))

    islands = 0
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1' and (r, c) not in visited:
                islands += 1
                bfs(r, c)

    return islands
```

**Typical Complexity**

- Time: `O(V + E)` or `O(rows * cols)` on a grid
- Space: `O(V)` for visited plus queue/stack

**Common Mistakes**

- Marking visited too late and enqueueing duplicates.
- Using DFS recursion when depth may overflow.
- Forgetting disconnected components.

**One-Line Memory Rule**

Graph/grid problems start with nodes, neighbors, and visited.

## Topological Sort

**Recognition**

- Directed dependencies: course order, build order, prerequisite order.
- Need an ordering that respects edges.

**Core Idea**

Nodes with indegree `0` are available now. Remove them layer by layer.

**Python Template**

```python
from collections import defaultdict, deque

def topo_sort(num_nodes, edges):
    graph = defaultdict(list)
    indegree = [0] * num_nodes

    for u, v in edges:
        graph[u].append(v)
        indegree[v] += 1

    q = deque([i for i in range(num_nodes) if indegree[i] == 0])
    order = []

    while q:
        node = q.popleft()
        order.append(node)
        for nei in graph[node]:
            indegree[nei] -= 1
            if indegree[nei] == 0:
                q.append(nei)

    return order if len(order) == num_nodes else []
```

**Typical Complexity**

- Time: `O(V + E)`
- Space: `O(V + E)`

**Common Mistakes**

- Using it on undirected graphs.
- Reversing edge direction accidentally.
- Forgetting that a short result means there was a cycle.

**One-Line Memory Rule**

Dependencies in a DAG usually mean indegree queue.

## Dijkstra

**Recognition**

- Weighted shortest path with non-negative edge costs.
- You want the cheapest path, not just any path.

**Core Idea**

Always expand the currently cheapest reachable state first with a min-heap.

**Python Template**

```python
import heapq
from collections import defaultdict

def dijkstra(n, edges, start):
    graph = defaultdict(list)
    for u, v, w in edges:
        graph[u].append((v, w))

    dist = [float('inf')] * n
    dist[start] = 0
    heap = [(0, start)]

    while heap:
        curr_dist, node = heapq.heappop(heap)
        if curr_dist > dist[node]:
            continue

        for nei, weight in graph[node]:
            new_dist = curr_dist + weight
            if new_dist < dist[nei]:
                dist[nei] = new_dist
                heapq.heappush(heap, (new_dist, nei))

    return dist
```

**Typical Complexity**

- Time: `O((V + E) log V)`
- Space: `O(V + E)`

**Common Mistakes**

- Using BFS on weighted edges.
- Forgetting stale heap entry checks.
- Using Dijkstra when negative weights exist.

**One-Line Memory Rule**

Non-negative weighted shortest path means Dijkstra.

## Union Find

**Recognition**

- Repeated connectivity checks with merges.
- Components change over time and you only care who is connected.

**Core Idea**

Store a representative for each component, compress paths during finds, and merge by rank or size.

**Python Template**

```python
parent = list(range(n))
rank = [0] * n

def find(x):
    if parent[x] != x:
        parent[x] = find(parent[x])
    return parent[x]

def union(a, b):
    ra, rb = find(a), find(b)
    if ra == rb:
        return False

    if rank[ra] < rank[rb]:
        parent[ra] = rb
    elif rank[ra] > rank[rb]:
        parent[rb] = ra
    else:
        parent[rb] = ra
        rank[ra] += 1

    return True
```

**Typical Complexity**

- Time: near `O(1)` amortized per operation, more precisely `O(alpha(n))`
- Space: `O(n)`

**Common Mistakes**

- Forgetting path compression.
- Recomputing connectivity from scratch every time.
- Using union find when you really need directed order instead of connectivity.

**One-Line Memory Rule**

Repeated merge-and-query connectivity suggests union find.

## Backtracking

**Recognition**

- Need all subsets, permutations, combinations, or valid constructions.
- The answer is built one choice at a time.

**Core Idea**

Choose, recurse, undo. The recursion tree is the search space.

**Python Template**

```python
def subsets(nums):
    ans = []
    path = []

    def dfs(i):
        if i == len(nums):
            ans.append(path[:])
            return

        path.append(nums[i])
        dfs(i + 1)
        path.pop()

        dfs(i + 1)

    dfs(0)
    return ans
```

**Typical Complexity**

- Time: often exponential, commonly `O(2^n)` or `O(n!)`
- Space: recursion depth plus output

**Common Mistakes**

- Forgetting to copy the current path.
- Forgetting to undo the last choice.
- Missing pruning opportunities.

**One-Line Memory Rule**

Backtracking is choose, recurse, unchoose.

## Dynamic Programming

**Recognition**

- Best answer over many choices with repeated subproblems.
- A brute-force recursion would recompute the same state again and again.

**Core Idea**

Define a state, define a transition, set the base case, then compute each state once.

**Python Template**

```python
def coin_change(coins, amount):
    dp = [amount + 1] * (amount + 1)
    dp[0] = 0

    for total in range(1, amount + 1):
        for coin in coins:
            if total - coin >= 0:
                dp[total] = min(dp[total], dp[total - coin] + 1)

    return dp[amount] if dp[amount] != amount + 1 else -1
```

**Typical Complexity**

- Time: number of states times transitions
- Space: number of stored states

**Common Mistakes**

- State definition is unclear.
- Wrong iteration order when compressing to 1D.
- Writing code before the recurrence is clear.

**One-Line Memory Rule**

DP is just cached recursion with a clean state definition.

## Greedy

**Recognition**

- A local choice looks safe and never needs to be undone.
- You can argue "stays ahead" or "exchange" informally.

**Core Idea**

Make the best immediate move only if you can justify that it cannot block the global optimum.

**Python Template**

```python
def can_jump(nums):
    farthest = 0

    for i, jump in enumerate(nums):
        if i > farthest:
            return False
        farthest = max(farthest, i + jump)

    return True
```

**Typical Complexity**

- Time: often `O(n)` after sorting if needed
- Space: often `O(1)`

**Common Mistakes**

- Calling something greedy without proof intuition.
- Ignoring a small counterexample.
- Using greedy when DP is needed to compare multiple futures.

**One-Line Memory Rule**

Greedy works only when the local best move is globally safe.

## Trie

**Recognition**

- Need prefix lookup, autocomplete, or prefix pruning.
- Many strings share prefixes and repeated prefix checks are expensive.

**Core Idea**

Store characters along paths so shared prefixes are reused.

**Python Template**

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_word = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root
        for ch in word:
            if ch not in node.children:
                node.children[ch] = TrieNode()
            node = node.children[ch]
        node.is_word = True

    def search(self, word):
        node = self.root
        for ch in word:
            if ch not in node.children:
                return False
            node = node.children[ch]
        return node.is_word

    def starts_with(self, prefix):
        node = self.root
        for ch in prefix:
            if ch not in node.children:
                return False
            node = node.children[ch]
        return True
```

**Typical Complexity**

- Insert/Search/Prefix Check: `O(length of word)`
- Space: `O(total characters stored)`

**Common Mistakes**

- Using a trie when a hashmap is enough.
- Forgetting the end-of-word marker.
- Building a trie when constraints are too small to justify it.

**One-Line Memory Rule**

Exact string lookup uses maps; shared prefix lookup suggests a trie.

## Design Problems

**Recognition**

- You must implement a class with required operation complexity.
- The main challenge is picking the right underlying data structures.

**Core Idea**

Translate each API call into primitives like array, hashmap, stack, heap, deque, linked list, or trie.

**Python Template**

```python
import random

class RandomizedSet:
    def __init__(self):
        self.nums = []
        self.pos = {}

    def insert(self, val: int) -> bool:
        if val in self.pos:
            return False
        self.pos[val] = len(self.nums)
        self.nums.append(val)
        return True

    def remove(self, val: int) -> bool:
        if val not in self.pos:
            return False

        idx = self.pos[val]
        last = self.nums[-1]
        self.nums[idx] = last
        self.pos[last] = idx

        self.nums.pop()
        del self.pos[val]
        return True

    def getRandom(self) -> int:
        return random.choice(self.nums)
```

**Typical Complexity**

- Insert/Remove/GetRandom: `O(1)` average
- Space: `O(n)`

**Common Mistakes**

- Ignoring the required complexity for each operation.
- Choosing the right behavior but the wrong underlying structure.
- Forgetting to clean stale metadata after swaps or deletes.

**One-Line Memory Rule**

Design problems are mostly data-structure matching problems.

## Mistake Log

### Sliding Window vs Prefix Sum

- Use sliding window for local window validity you can maintain incrementally.
- Use prefix sum for exact cumulative totals, especially when negatives exist.
- Memory rule: window for validity, prefix for exact total.

### Stack vs Counter

- Use stack when order or nesting matters.
- Use counter when only frequencies matter.
- Memory rule: nesting needs order, counts do not.

### BFS vs DFS for Shortest Path

- BFS gives shortest path in an unweighted graph.
- DFS explores deeply but does not guarantee shortest path.
- Memory rule: shortest unweighted path means BFS.

### Binary Search vs Two Pointers

- Binary search needs a monotonic condition.
- Two pointers needs a direct move rule based on the current pair/window.
- Memory rule: boundary search vs moving scan.

### DP vs Greedy

- Use DP when multiple futures must be compared.
- Use greedy only when the local move is provably safe.
- Memory rule: if the proof feels weak, it is probably not greedy.

### Heap vs QuickSelect

- Heap is better for repeated top-`k` maintenance or streaming data.
- Quickselect is better for one static kth query.
- Memory rule: repeated extraction means heap; one-shot selection means quickselect.

### Tree DFS Return Value vs Global Answer

- Return subtree information upward.
- Store the final cross-subtree answer separately when needed.
- Memory rule: ask whether the parent needs a value or the whole problem needs an answer.

## Final Review Rules

- Classify the pattern before thinking about a problem number.
- Write the invariant first if the pattern is not obvious.
- If stuck, ask which operation must be cheap: lookup, merge, connect, order, or optimize.
- If still stuck, default to `HashMap / Set` or sorting to expose structure.
