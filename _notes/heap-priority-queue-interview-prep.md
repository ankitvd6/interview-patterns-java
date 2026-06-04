---
title: Heap and Priority Queue Interview Prep
description: Core heap patterns for top K, kth element, merging, scheduling, running median, and greedy optimization problems.
section: Heap / Priority Queue
summary: Heap mental models, Java priority queue templates, and the interview patterns where heaps pay off.
category: DSA Pattern
tags: [heap, priority-queue, java]
order: 40
---
# Heap / Priority Queue Interview Prep — FAANG-Level Patterns

> Goal: Recall the core heap patterns and techniques that help solve most heap-related interview problems quickly.

---

## 1. Mental Model

A **heap** is a tree-based data structure usually implemented as an array.

- **Min-heap:** smallest element is always at the top.
- **Max-heap:** largest element is always at the top.
- Main operations:
  - `push`: `O(log n)`
  - `pop`: `O(log n)`
  - `peek`: `O(1)`
  - build heap from array: `O(n)`

A heap is not fully sorted. It only guarantees that the top element is the current min or max.

### When to Think “Heap”

Use a heap when the problem asks for:

- top `K`
- kth smallest / kth largest
- repeatedly get min or max
- merge sorted things
- process events by earliest/latest time
- maintain a running median
- optimize with greedy choices
- schedule tasks by priority
- find shortest path / minimum cost expansion

---

## 2. Core Heap Patterns

## Pattern 1: Top K Elements

### Use When

You need:

- K largest
- K smallest
- K most frequent
- K closest
- K highest scores

### Key Technique

Use a heap of size `K`, not necessarily all `N` elements.

### For K Largest

Use a **min-heap of size K**.

Why? The smallest among the current top K stays at the top. If a new number is larger, remove the smallest and insert the new number.

```python
import heapq

def k_largest(nums, k):
    heap = []
    for x in nums:
        heapq.heappush(heap, x)
        if len(heap) > k:
            heapq.heappop(heap)
    return heap
```

Time: `O(n log k)`  
Space: `O(k)`

### For K Smallest

Use a **max-heap of size K**. In Python, simulate max-heap using negatives.

```python
import heapq

def k_smallest(nums, k):
    heap = []
    for x in nums:
        heapq.heappush(heap, -x)
        if len(heap) > k:
            heapq.heappop(heap)
    return [-x for x in heap]
```

### Common Problems

- Kth Largest Element in an Array
- Top K Frequent Elements
- K Closest Points to Origin
- Find K Pairs with Smallest Sums
- K Closest Elements

### Interview Tip

If the input is huge or streaming, prefer heap of size `K`.  
If you need all elements sorted, heap is not always the best option.

---

## Pattern 2: Kth Smallest / Kth Largest

### Option A: Heap of Size K

For kth largest, maintain a min-heap of size `K`.

```python
import heapq

def find_kth_largest(nums, k):
    heap = []
    for x in nums:
        heapq.heappush(heap, x)
        if len(heap) > k:
            heapq.heappop(heap)
    return heap[0]
```

Time: `O(n log k)`  
Space: `O(k)`

### Option B: Heapify Entire Array

For kth smallest, heapify and pop `k` times.

```python
import heapq

def kth_smallest(nums, k):
    heapq.heapify(nums)
    ans = None
    for _ in range(k):
        ans = heapq.heappop(nums)
    return ans
```

Time: `O(n + k log n)`  
Space: `O(1)` if mutation allowed

### Option C: Quickselect

For kth problems, quickselect is often better average-case `O(n)`, but heap is safer and easier to implement.

### Interview Tip

Mention tradeoff:

- Heap: predictable, simple, `O(n log k)`
- Quickselect: average `O(n)`, worst-case `O(n^2)` unless randomized or median-of-medians

---

## Pattern 3: Two Heaps

### Use When

You need to maintain two partitions dynamically:

- running median
- sliding window median
- split smaller half and larger half

### Key Technique

Use:

- max-heap for lower half
- min-heap for upper half

Python max-heap uses negatives.

### Running Median Template

```python
import heapq

class MedianFinder:
    def __init__(self):
        self.small = []  # max-heap via negatives
        self.large = []  # min-heap

    def addNum(self, num: int) -> None:
        heapq.heappush(self.small, -num)

        # Ensure every element in small <= every element in large
        if self.small and self.large and -self.small[0] > self.large[0]:
            val = -heapq.heappop(self.small)
            heapq.heappush(self.large, val)

        # Balance sizes
        if len(self.small) > len(self.large) + 1:
            val = -heapq.heappop(self.small)
            heapq.heappush(self.large, val)
        elif len(self.large) > len(self.small):
            val = heapq.heappop(self.large)
            heapq.heappush(self.small, -val)

    def findMedian(self) -> float:
        if len(self.small) > len(self.large):
            return -self.small[0]
        return (-self.small[0] + self.large[0]) / 2
```

### Invariants

Always say these in interviews:

1. `small` contains the smaller half.
2. `large` contains the larger half.
3. `len(small) == len(large)` or `len(small) == len(large) + 1`.
4. `max(small) <= min(large)`.

### Common Problems

- Find Median from Data Stream
- Sliding Window Median
- IPO / Maximize Capital variants

---

## Pattern 4: Merge K Sorted Lists / Arrays

### Use When

You have multiple sorted sources and need a sorted merged result.

### Key Technique

Push the first element from each list into a min-heap. Each heap entry should include enough metadata to find the next element.

### Template

```python
import heapq

def merge_k_sorted_arrays(arrays):
    heap = []
    result = []

    for i, arr in enumerate(arrays):
        if arr:
            heapq.heappush(heap, (arr[0], i, 0))

    while heap:
        value, arr_idx, elem_idx = heapq.heappop(heap)
        result.append(value)

        next_idx = elem_idx + 1
        if next_idx < len(arrays[arr_idx]):
            heapq.heappush(heap, (arrays[arr_idx][next_idx], arr_idx, next_idx))

    return result
```

Time: `O(N log K)` where `N` is total elements and `K` is number of lists.  
Space: `O(K)`.

### Common Problems

- Merge K Sorted Lists
- Kth Smallest Element in a Sorted Matrix
- Smallest Range Covering Elements from K Lists

### Interview Tip

Do not push all elements into the heap unless necessary. Push only one candidate per list.

---

## Pattern 5: Heap for Sorted Matrix / Grid Expansion

### Use When

Rows and/or columns are sorted.

Examples:

- kth smallest in sorted matrix
- find smallest pairs
- expand candidates in increasing order

### Technique

Start with the smallest candidate, then push valid neighbors.

For a row-column sorted matrix, common choices:

- Push first element of each row.
- Or push `(0, 0)` and expand right/down with a visited set.

### Template: Kth Smallest in Sorted Matrix

```python
import heapq

def kth_smallest_matrix(matrix, k):
    n = len(matrix)
    heap = []

    for r in range(min(n, k)):
        heapq.heappush(heap, (matrix[r][0], r, 0))

    val = None
    for _ in range(k):
        val, r, c = heapq.heappop(heap)
        if c + 1 < len(matrix[0]):
            heapq.heappush(heap, (matrix[r][c + 1], r, c + 1))

    return val
```

Time: `O(k log min(n, k))`.

### Alternative

For sorted matrix kth smallest, binary search on answer can be better: `O(n log value_range)`.

---

## Pattern 6: Greedy with Heap

### Use When

You repeatedly choose the best available option:

- minimum cost
- maximum profit
- earliest deadline
- shortest duration
- largest available capital gain

### Key Idea

Sort by one dimension, heap by another.

This is very common in FAANG interviews.

### Example: Meeting Rooms II

Sort meetings by start time. Use min-heap of end times.

```python
import heapq

def min_meeting_rooms(intervals):
    intervals.sort(key=lambda x: x[0])
    heap = []

    for start, end in intervals:
        if heap and heap[0] <= start:
            heapq.heappop(heap)
        heapq.heappush(heap, end)

    return len(heap)
```

### Example: IPO

Sort projects by required capital. Use max-heap for profit among affordable projects.

```python
import heapq

def find_maximized_capital(k, w, profits, capital):
    projects = sorted(zip(capital, profits))
    i = 0
    max_profit_heap = []

    for _ in range(k):
        while i < len(projects) and projects[i][0] <= w:
            heapq.heappush(max_profit_heap, -projects[i][1])
            i += 1

        if not max_profit_heap:
            break

        w += -heapq.heappop(max_profit_heap)

    return w
```

### Common Problems

- Meeting Rooms II
- Minimum Number of Refueling Stops
- IPO
- Course Schedule III
- Task Scheduler
- Reorganize String
- Maximum Performance of a Team
- Minimum Cost to Hire K Workers

---

## Pattern 7: Scheduling and Intervals

### Use When

Problems involve:

- rooms
- CPUs
- servers
- meeting conflicts
- event timelines
- task processing

### Common Heap Choice

- Min-heap by end time for active intervals.
- Min-heap by available time for machines or servers.
- Max-heap by frequency for task rearrangement.

### Meeting Rooms Template

```python
import heapq

def rooms_required(intervals):
    intervals.sort()
    active = []

    for start, end in intervals:
        while active and active[0] <= start:
            heapq.heappop(active)
        heapq.heappush(active, end)

    return len(active)
```

Use `while` if multiple ended intervals can be removed. Use `if` if only one room reuse is enough for the exact problem.

### CPU Task Scheduling Template

```python
import heapq

def get_order(tasks):
    indexed = sorted((enqueue, process, i) for i, (enqueue, process) in enumerate(tasks))
    result = []
    heap = []
    time = 0
    i = 0

    while i < len(indexed) or heap:
        if not heap and time < indexed[i][0]:
            time = indexed[i][0]

        while i < len(indexed) and indexed[i][0] <= time:
            enqueue, process, idx = indexed[i]
            heapq.heappush(heap, (process, idx))
            i += 1

        process, idx = heapq.heappop(heap)
        time += process
        result.append(idx)

    return result
```

---

## Pattern 8: Frequency-Based Heap

### Use When

You need to process elements by frequency:

- top K frequent
- reorganize string
- task scheduler
- least number of unique integers after removals

### Top K Frequent Template

```python
import heapq
from collections import Counter

def top_k_frequent(nums, k):
    count = Counter(nums)
    heap = []

    for num, freq in count.items():
        heapq.heappush(heap, (freq, num))
        if len(heap) > k:
            heapq.heappop(heap)

    return [num for freq, num in heap]
```

### Reorganize String Template

```python
import heapq
from collections import Counter

def reorganize_string(s):
    count = Counter(s)
    heap = [(-freq, ch) for ch, freq in count.items()]
    heapq.heapify(heap)

    result = []
    prev_freq, prev_ch = 0, ""

    while heap:
        freq, ch = heapq.heappop(heap)
        result.append(ch)

        if prev_freq < 0:
            heapq.heappush(heap, (prev_freq, prev_ch))

        freq += 1
        prev_freq, prev_ch = freq, ch

    ans = "".join(result)
    return ans if len(ans) == len(s) else ""
```

---

## Pattern 9: Dijkstra / Shortest Path Heap

### Use When

You need shortest or minimum cost path in a weighted graph with non-negative weights.

### Key Technique

Use a min-heap of `(distance_so_far, node)`.

### Template

```python
import heapq
from collections import defaultdict

def dijkstra(n, edges, source):
    graph = defaultdict(list)
    for u, v, w in edges:
        graph[u].append((v, w))

    dist = [float("inf")] * n
    dist[source] = 0
    heap = [(0, source)]

    while heap:
        d, node = heapq.heappop(heap)

        if d > dist[node]:
            continue

        for nei, weight in graph[node]:
            nd = d + weight
            if nd < dist[nei]:
                dist[nei] = nd
                heapq.heappush(heap, (nd, nei))

    return dist
```

### Common Problems

- Network Delay Time
- Path With Minimum Effort
- Cheapest Flights Within K Stops
- Swim in Rising Water
- Minimum Cost to Reach Destination

### Interview Tip

Say clearly: Dijkstra works when edge weights are non-negative.

---

## Pattern 10: Lazy Deletion Heap

### Use When

You need to delete arbitrary elements from a heap, but heap does not support efficient removal.

Common in:

- sliding window median
- max sliding window with heap
- frequency updates

### Technique

Do not delete immediately. Mark deleted values in a hashmap. When a deleted value reaches heap top, pop it.

### Template Idea

```python
from collections import defaultdict
import heapq

delayed = defaultdict(int)

# Mark value for deletion
delayed[x] += 1

# Clean heap top
def prune(heap):
    while heap:
        x = heap[0]
        real_x = -x  # if max-heap using negatives
        if delayed[real_x] > 0:
            delayed[real_x] -= 1
            heapq.heappop(heap)
        else:
            break
```

### Interview Tip

Explain why lazy deletion is needed: removing a non-top heap element is `O(n)`, which ruins complexity.

---

## 3. Python Heap Tips

Python's `heapq` is a min-heap.

### Max-Heap

```python
heapq.heappush(heap, -x)
x = -heapq.heappop(heap)
```

### Tuples in Heap

Python compares tuple elements left to right.

```python
heapq.heappush(heap, (priority, tie_breaker, value))
```

Use a tie breaker when objects are not directly comparable.

```python
heapq.heappush(heap, (priority, index, node))
```

### Heapify

```python
heapq.heapify(arr)  # O(n)
```

### Push then Pop Efficiently

```python
heapq.heappushpop(heap, x)
```

This is more efficient than push followed by pop.

### Pop then Push Efficiently

```python
heapq.heapreplace(heap, x)
```

Be careful: it pops first even if `x` is smaller/larger.

---

## 4. How to Choose Min-Heap vs Max-Heap

| Problem asks for | Usually use |
|---|---|
| K largest | Min-heap of size K |
| K smallest | Max-heap of size K |
| Always smallest next | Min-heap |
| Always largest next | Max-heap |
| Running median | Max-heap + min-heap |
| Merge sorted lists | Min-heap |
| Earliest ending meeting | Min-heap |
| Most frequent first | Max-heap |
| Cheapest path | Min-heap |

---

## 5. Interview Decision Framework

When you see a heap problem, ask:

1. Do I need the smallest/largest repeatedly?
2. Is only top K needed instead of full sorting?
3. Is the input streaming?
4. Do I need to merge sorted sources?
5. Am I choosing the best available candidate greedily?
6. Are there dynamic insertions and deletions?
7. Do I need two halves of data?
8. Is this actually a graph shortest-path problem?

If yes to any of these, heap is likely useful.

---

## 6. Common Mistakes

### Mistake 1: Sorting When Heap Is Better

Sorting costs `O(n log n)`.  
Top K with heap costs `O(n log k)`.

### Mistake 2: Using Heap When Quickselect Is Better

For kth largest in a static array, quickselect may be expected. Mention it as an alternative.

### Mistake 3: Forgetting Tie Breakers

If heap entries contain custom objects, Python may fail when priorities tie.

Bad:

```python
(priority, node)
```

Better:

```python
(priority, unique_id, node)
```

### Mistake 4: Removing Arbitrary Heap Elements Directly

Heap does not support efficient arbitrary deletion. Use lazy deletion.

### Mistake 5: Wrong Heap Size

For top K problems, always maintain heap size exactly `K` after each iteration when possible.

### Mistake 6: Confusing K Largest with Max-Heap

For K largest, using a max-heap of all elements works but costs more. A min-heap of size K is usually better.

---

## 7. Complexity Cheat Sheet

| Operation | Complexity |
|---|---:|
| Peek min/max | `O(1)` |
| Push | `O(log n)` |
| Pop | `O(log n)` |
| Heapify array | `O(n)` |
| Top K using size-K heap | `O(n log k)` |
| Merge K sorted lists | `O(N log K)` |
| Dijkstra | `O((V + E) log V)` |

---

## 8. FAANG-Level Problem List by Pattern

### Easy / Warm-Up

- Last Stone Weight
- Kth Largest Element in a Stream
- Relative Ranks

### Top K

- Kth Largest Element in an Array
- Top K Frequent Elements
- K Closest Points to Origin
- Find K Closest Elements
- Sort Characters by Frequency

### Two Heaps

- Find Median from Data Stream
- Sliding Window Median
- IPO

### Merge K Sorted

- Merge K Sorted Lists
- Kth Smallest Element in a Sorted Matrix
- Find K Pairs with Smallest Sums
- Smallest Range Covering Elements from K Lists

### Greedy + Heap

- Meeting Rooms II
- Minimum Number of Refueling Stops
- Course Schedule III
- Task Scheduler
- Reorganize String
- Maximum Performance of a Team
- Minimum Cost to Hire K Workers

### Graph + Heap

- Network Delay Time
- Path With Minimum Effort
- Swim in Rising Water
- Cheapest Flights Within K Stops
- The Maze II

### Advanced

- Sliding Window Median
- Design Twitter
- Exam Room
- Minimum Interval to Include Each Query
- Process Tasks Using Servers

---

## 9. Deep Pattern Examples

## Example 1: Top K Frequent Elements

### Thought Process

1. Count frequencies.
2. Keep a min-heap of size K based on frequency.
3. If heap grows beyond K, remove the least frequent.
4. Remaining elements are top K frequent.

```python
from collections import Counter
import heapq

def top_k_frequent(nums, k):
    freq = Counter(nums)
    heap = []

    for num, count in freq.items():
        heapq.heappush(heap, (count, num))
        if len(heap) > k:
            heapq.heappop(heap)

    return [num for count, num in heap]
```

### Complexity

`O(n log k)` time, `O(n)` for frequency map and `O(k)` heap.

---

## Example 2: K Closest Points to Origin

### Thought Process

1. Distance can be compared using squared distance.
2. Keep max-heap of size K.
3. If heap size exceeds K, remove farthest point.

```python
import heapq

def k_closest(points, k):
    heap = []

    for x, y in points:
        dist = x * x + y * y
        heapq.heappush(heap, (-dist, x, y))
        if len(heap) > k:
            heapq.heappop(heap)

    return [[x, y] for dist, x, y in heap]
```

---

## Example 3: Meeting Rooms II

### Thought Process

1. Sort meetings by start time.
2. Track active meeting end times in min-heap.
3. If earliest meeting ended before current starts, reuse room.
4. Heap size is number of rooms currently needed.

```python
import heapq

def min_meeting_rooms(intervals):
    if not intervals:
        return 0

    intervals.sort(key=lambda x: x[0])
    rooms = []

    for start, end in intervals:
        if rooms and rooms[0] <= start:
            heapq.heappop(rooms)
        heapq.heappush(rooms, end)

    return len(rooms)
```

---

## Example 4: Merge K Sorted Lists

### Thought Process

1. Add first node from each list to heap.
2. Pop smallest node.
3. Add its next node.
4. Continue until heap is empty.

```python
import heapq

class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

def merge_k_lists(lists):
    heap = []

    for i, node in enumerate(lists):
        if node:
            heapq.heappush(heap, (node.val, i, node))

    dummy = ListNode()
    cur = dummy
    counter = len(lists)

    while heap:
        val, _, node = heapq.heappop(heap)
        cur.next = node
        cur = cur.next

        if node.next:
            counter += 1
            heapq.heappush(heap, (node.next.val, counter, node.next))

    return dummy.next
```

### Why Tie Breaker Is Needed

If two nodes have the same value, Python tries to compare `ListNode` objects. A unique index prevents that.

---

## Example 5: Minimum Number of Refueling Stops

### Thought Process

1. Travel as far as possible.
2. Add all reachable stations to a max-heap by fuel amount.
3. When stuck, refuel from the largest previous station.
4. This greedy choice maximizes future reach.

```python
import heapq

def min_refuel_stops(target, startFuel, stations):
    max_heap = []
    fuel = startFuel
    stops = 0
    i = 0

    while fuel < target:
        while i < len(stations) and stations[i][0] <= fuel:
            heapq.heappush(max_heap, -stations[i][1])
            i += 1

        if not max_heap:
            return -1

        fuel += -heapq.heappop(max_heap)
        stops += 1

    return stops
```

---

## 10. Heap vs Other Techniques

| Technique | Use When |
|---|---|
| Heap | Repeated min/max, top K, dynamic priority |
| Sorting | Need complete order or one-time ordering |
| Quickselect | Static kth element, average `O(n)` desired |
| Balanced BST / SortedList | Need ordered insert/delete/search by rank |
| Monotonic deque | Sliding window max/min in `O(n)` |
| Binary search | Answer space is monotonic |
| BFS | Unweighted shortest path |
| Dijkstra heap | Weighted non-negative shortest path |

---

## 11. Must-Know Heap Invariants

For every heap solution, define your invariant.

Examples:

- Top K largest: heap stores the largest K elements seen so far.
- Meeting rooms: heap stores end times of currently active meetings.
- Median: max-heap stores lower half, min-heap stores upper half.
- Merge K lists: heap stores the next smallest candidate from each list.
- Dijkstra: heap stores candidate shortest distances not yet finalized.

Interviewers love invariant clarity.

---

## 12. How to Explain a Heap Solution in Interviews

Use this structure:

1. **Observation:** We repeatedly need the min/max/top K.
2. **Data structure:** A heap gives efficient access to that priority.
3. **Invariant:** State exactly what the heap stores.
4. **Algorithm:** Walk through sorted/input iteration.
5. **Complexity:** Explain time and space.
6. **Edge cases:** Empty input, duplicates, ties, K > N, negative values.

Example explanation:

> We need the kth largest element, but not the full sorted order. I’ll maintain a min-heap of size K. The heap contains the K largest values seen so far, and the smallest among them is at the root. For every new value, I push it into the heap. If the heap exceeds size K, I pop the smallest. At the end, the root is the kth largest.

---

## 13. Edge Cases Checklist

Before finalizing, check:

- Empty input
- `k == 0`
- `k == 1`
- `k == len(nums)`
- duplicates
- negative numbers
- ties in priority
- custom objects in heap
- streaming input
- mutable input allowed or not
- very large `n`
- heap cleanup needed for deleted/stale entries

---

## 14. Practice Plan

### Day 1: Basics and Top K

- Kth Largest Element in an Array
- Top K Frequent Elements
- K Closest Points to Origin
- Kth Largest in Stream

### Day 2: Merge K Sorted

- Merge K Sorted Lists
- Kth Smallest in Sorted Matrix
- Find K Pairs with Smallest Sums

### Day 3: Two Heaps

- Find Median from Data Stream
- Sliding Window Median

### Day 4: Greedy + Heap

- Meeting Rooms II
- IPO
- Minimum Number of Refueling Stops
- Course Schedule III

### Day 5: Scheduling/Frequency

- Task Scheduler
- Reorganize String
- Process Tasks Using Servers

### Day 6: Graph + Heap

- Network Delay Time
- Path With Minimum Effort
- Swim in Rising Water

### Day 7: Mixed Mock Round

Pick 4 problems:

1. One top K
2. One two-heaps
3. One greedy heap
4. One Dijkstra heap

Solve each with full verbal explanation.

---

## 15. Final Cheat Sheet

### Top K

- K largest → min-heap size K
- K smallest → max-heap size K

### Median

- lower half → max-heap
- upper half → min-heap
- balance sizes

### Merge K

- heap stores one candidate per list
- push next from same source after pop

### Intervals

- sort by start
- heap by end

### Greedy

- sort by availability
- heap by best reward/cost among available choices

### Dijkstra

- min-heap by distance
- skip stale entries

### Lazy Deletion

- mark deleted in hashmap
- clean only when value reaches heap top

---

## 16. One-Line Pattern Recognition

- “K largest/smallest” → size-K heap
- “Median stream” → two heaps
- “Merge sorted lists” → min-heap with source index
- “Earliest available room/server” → min-heap by end/available time
- “Most frequent / highest priority next” → max-heap
- “Can choose among currently available options” → sort + heap
- “Shortest weighted path” → Dijkstra + min-heap
- “Sliding window with deletion” → heap + lazy deletion, or monotonic deque if only max/min

---

## 17. Personal Interview Reminder

For FAANG-level interviews, do not just code the heap solution. Explain:

- why heap is the right structure
- what the heap contains
- why the invariant stays true
- why the greedy choice is safe, if applicable
- time and space complexity
- alternatives and tradeoffs

This is often the difference between a working solution and a strong interview performance.
