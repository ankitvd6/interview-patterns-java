---
title: Intervals Interview Notes
description: FAANG-level interval patterns covering overlap, merging, gaps, meeting rooms, sweep line, and booking problems.
section: Intervals
summary: A compact playbook for overlap checks, merge logic, sweep line, and interval scheduling problems.
category: DSA Pattern
tags: [intervals, sweep-line, java]
order: 50
---
# Intervals Interview Notes — FAANG-Level Playbook

This page is a compact recall guide for solving most interview problems related to **Intervals**.

---

## 1. Core Mental Model

An interval is usually represented as:

```text
[start, end]
```

Most interval problems ask about one or more of these ideas:

- Overlap
- Gaps
- Containment
- Ordering
- Maximum concurrency
- Minimum removals
- Dynamic booking or querying

### Universal Overlap Check

For closed intervals `[a, b]` and `[c, d]`:

```text
They overlap if:
a <= d && c <= b
```

For intervals sorted by start:

```text
current overlaps previous if:
current.start <= previous.end
```

For non-overlap:

```text
current.start > previous.end
```

For half-open intervals `[start, end)`:

```text
overlap if:
current.start < previous.end
```

This distinction is especially important in meeting room and calendar problems.

---

## 2. Pattern: Sort by Start, Then Merge

Use this when the problem asks for:

- Merge intervals
- Insert interval
- Employee free time
- Calendar availability
- Union of intervals
- Covered time

### Java Template

```java
Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));

List<int[]> result = new ArrayList<>();

for (int[] interval : intervals) {
    if (result.isEmpty() || interval[0] > result.get(result.size() - 1)[1]) {
        result.add(interval);
    } else {
        result.get(result.size() - 1)[1] =
            Math.max(result.get(result.size() - 1)[1], interval[1]);
    }
}
```

### Key Idea

After sorting by start, compare the current interval only with the **last merged interval**.

### Example Problems

- Merge Intervals
- Insert Interval
- Employee Free Time
- Summary Ranges
- Meeting Scheduler

---

## 3. Pattern: Sort by Start + Scan for Conflicts

Use this when the problem asks:

- Can a person attend all meetings?
- Does any overlap exist?
- Are bookings valid?
- Find conflicting intervals

### Java Template

```java
Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));

for (int i = 1; i < intervals.length; i++) {
    if (intervals[i][0] < intervals[i - 1][1]) {
        return false;
    }
}

return true;
```

For closed intervals, use:

```java
intervals[i][0] <= intervals[i - 1][1]
```

For meeting rooms, intervals are usually half-open, so `[1, 5]` and `[5, 10]` do **not** conflict.

---

## 4. Pattern: Min-Heap by End Time

Use this when asked for:

- Minimum meeting rooms
- Maximum simultaneous intervals
- Resource allocation
- Active intervals

### Java Template

```java
Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));

PriorityQueue<Integer> minHeap = new PriorityQueue<>();

for (int[] interval : intervals) {
    if (!minHeap.isEmpty() && minHeap.peek() <= interval[0]) {
        minHeap.poll();
    }

    minHeap.offer(interval[1]);
}

return minHeap.size();
```

### Why It Works

The heap stores the end times of currently active intervals.

If the earliest-ending interval ends before the current interval starts, we can reuse that room or resource.

### Important Variant

If multiple intervals may have ended before the current one, use:

```java
while (!minHeap.isEmpty() && minHeap.peek() <= interval[0]) {
    minHeap.poll();
}
```

Use `while` when the active set must be fully accurate.

Use `if` when only one reusable resource is needed for the current interval.

### Example Problems

- Meeting Rooms II
- Minimum Platforms
- Car Pooling
- Process Scheduling

---

## 5. Pattern: Sweep Line / Timeline Events

Use this when asked for:

- Maximum overlap
- Number of active intervals at any time
- Capacity exceeded or not
- Range additions
- Booking calendars
- Count intervals covering a point

Convert intervals into events:

```text
start -> +1
end   -> -1
```

### Java Template

```java
List<int[]> events = new ArrayList<>();

for (int[] interval : intervals) {
    events.add(new int[]{interval[0], 1});
    events.add(new int[]{interval[1], -1});
}

events.sort((a, b) -> {
    if (a[0] != b[0]) return Integer.compare(a[0], b[0]);
    return Integer.compare(a[1], b[1]);
});

int active = 0;
int maxActive = 0;

for (int[] event : events) {
    active += event[1];
    maxActive = Math.max(maxActive, active);
}

return maxActive;
```

### Tie-Breaking Rule

For half-open meeting intervals `[start, end)`:

```text
end event should come before start event at the same time
```

So sort:

```text
(time, -1) before (time, +1)
```

For closed intervals `[start, end]`, start may need to come before end.

### TreeMap Version

```java
TreeMap<Integer, Integer> map = new TreeMap<>();

for (int[] interval : intervals) {
    map.put(interval[0], map.getOrDefault(interval[0], 0) + 1);
    map.put(interval[1], map.getOrDefault(interval[1], 0) - 1);
}

int active = 0;
int maxActive = 0;

for (int delta : map.values()) {
    active += delta;
    maxActive = Math.max(maxActive, active);
}
```

### Example Problems

- Meeting Rooms II
- Car Pooling
- My Calendar I / II / III
- Corporate Flight Bookings
- Range Addition
- Maximum Population Year

---

## 6. Pattern: Two Pointers Over Sorted Interval Lists

Use this when asked for:

- Interval intersections
- Common free time
- Meeting availability between two people
- Comparing calendars

### Java Template

```java
int i = 0, j = 0;
List<int[]> result = new ArrayList<>();

while (i < A.length && j < B.length) {
    int start = Math.max(A[i][0], B[j][0]);
    int end = Math.min(A[i][1], B[j][1]);

    if (start <= end) {
        result.add(new int[]{start, end});
    }

    if (A[i][1] < B[j][1]) {
        i++;
    } else {
        j++;
    }
}
```

For half-open intervals, intersection exists if:

```java
start < end
```

not:

```java
start <= end
```

### Key Idea

The interval that ends earlier cannot contribute to future intersections, so move that pointer.

### Example Problems

- Interval List Intersections
- Meeting Scheduler
- Employee Free Time
- Common Availability

---

## 7. Pattern: Greedy Sort by End Time

Use this when asked for:

- Remove minimum intervals to avoid overlap
- Maximum number of non-overlapping intervals
- Minimum arrows to burst balloons
- Activity selection

### Java Template: Maximum Non-Overlapping Intervals

```java
Arrays.sort(intervals, (a, b) -> Integer.compare(a[1], b[1]));

int count = 0;
int lastEnd = Integer.MIN_VALUE;

for (int[] interval : intervals) {
    if (interval[0] >= lastEnd) {
        count++;
        lastEnd = interval[1];
    }
}

return count;
```

### Minimum Removals

```java
return intervals.length - count;
```

### Why Sort by End?

Choosing the interval that ends earliest leaves the most room for future intervals.

### Example Problems

- Non-overlapping Intervals
- Minimum Number of Arrows to Burst Balloons
- Maximum Length of Pair Chain
- Activity Selection

---

## 8. Pattern: Sort by Start, Keep Smallest End

This is useful for removal or overlap detection problems.

### Minimum Removals to Make Intervals Non-Overlapping

```java
Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));

int removals = 0;
int prevEnd = intervals[0][1];

for (int i = 1; i < intervals.length; i++) {
    if (intervals[i][0] < prevEnd) {
        removals++;
        prevEnd = Math.min(prevEnd, intervals[i][1]);
    } else {
        prevEnd = intervals[i][1];
    }
}

return removals;
```

### Key Idea

When two intervals overlap, remove the one with the larger end because it blocks more future intervals.

---

## 9. Pattern: Prefix Sum / Difference Array

Use this when:

- Coordinates are bounded
- There are many range updates
- You need coverage counts after updates
- You need capacity checks over a discrete range

### Java Template

```java
int[] diff = new int[n + 1];

for (int[] interval : intervals) {
    int start = interval[0];
    int end = interval[1];
    int value = interval[2];

    diff[start] += value;

    if (end + 1 < diff.length) {
        diff[end + 1] -= value;
    }
}

int running = 0;

for (int i = 0; i < n; i++) {
    running += diff[i];
    // running is value at i
}
```

For half-open intervals `[start, end)`:

```java
diff[start] += value;
diff[end] -= value;
```

### Example Problems

- Car Pooling
- Corporate Flight Bookings
- Range Addition
- Maximum Population Year

---

## 10. Pattern: Binary Search Over Intervals

Use this when intervals are sorted and you need:

- Next interval
- Right interval
- Insertion position
- Interval lookup
- Calendar booking

### Example: Find Right Interval

```java
int[][] starts = new int[n][2];

for (int i = 0; i < n; i++) {
    starts[i][0] = intervals[i][0];
    starts[i][1] = i;
}

Arrays.sort(starts, (a, b) -> Integer.compare(a[0], b[0]));

for (int i = 0; i < n; i++) {
    int target = intervals[i][1];
    int idx = lowerBound(starts, target);
    result[i] = idx == n ? -1 : starts[idx][1];
}
```

```java
private int lowerBound(int[][] arr, int target) {
    int left = 0, right = arr.length;

    while (left < right) {
        int mid = left + (right - left) / 2;

        if (arr[mid][0] < target) {
            left = mid + 1;
        } else {
            right = mid;
        }
    }

    return left;
}
```

### Example Problems

- Find Right Interval
- My Calendar I
- Data Stream as Disjoint Intervals
- Range Module

---

## 11. Pattern: TreeMap for Dynamic Interval Problems

Use this when intervals are inserted, removed, or queried dynamically.

Common operations:

```java
floorKey(x)
ceilingKey(x)
lowerKey(x)
higherKey(x)
```

### My Calendar I Template

```java
TreeMap<Integer, Integer> calendar = new TreeMap<>();

public boolean book(int start, int end) {
    Integer prev = calendar.floorKey(start);
    if (prev != null && calendar.get(prev) > start) {
        return false;
    }

    Integer next = calendar.ceilingKey(start);
    if (next != null && next < end) {
        return false;
    }

    calendar.put(start, end);
    return true;
}
```

### Why It Works

To insert `[start, end)`, only the immediate previous and next intervals can overlap if the map is sorted by start.

---

## 12. Boundary Rules

This is where many interview bugs happen.

### Closed Intervals

```text
[1, 3] and [3, 5] overlap
```

Overlap check:

```java
aStart <= bEnd && bStart <= aEnd
```

Merge condition:

```java
current.start <= last.end
```

### Half-Open Intervals

```text
[1, 3) and [3, 5) do not overlap
```

Overlap check:

```java
aStart < bEnd && bStart < aEnd
```

Merge/conflict condition:

```java
current.start < last.end
```

Most meeting and calendar problems use half-open intervals.

---

## 13. Choosing the Right Pattern Quickly

| Problem asks | Likely pattern |
|---|---|
| Merge overlapping ranges | Sort by start + merge |
| Insert a new interval | Merge around inserted interval |
| Check if any overlap exists | Sort by start + compare adjacent |
| Minimum rooms/resources | Min-heap or sweep line |
| Maximum simultaneous intervals | Sweep line |
| Remove minimum intervals | Greedy by end time |
| Maximum non-overlapping intervals | Greedy by end time |
| Intersections between two lists | Two pointers |
| Range updates | Difference array / prefix sum |
| Dynamic booking | TreeMap |
| Find next compatible interval | Binary search |
| Capacity exceeded over trips | Sweep line / difference array |
| Common free time | Merge busy intervals, then find gaps |

---

## 14. Classic FAANG-Level Problems to Master

Prioritize these:

1. Merge Intervals
2. Insert Interval
3. Meeting Rooms I
4. Meeting Rooms II
5. Non-overlapping Intervals
6. Minimum Number of Arrows to Burst Balloons
7. Interval List Intersections
8. Employee Free Time
9. My Calendar I
10. My Calendar II
11. My Calendar III
12. Car Pooling
13. Range Addition
14. Corporate Flight Bookings
15. Find Right Interval
16. Data Stream as Disjoint Intervals
17. Range Module
18. Maximum Population Year
19. Meeting Scheduler
20. Partition Labels — conceptually interval merging

---

## 15. Interview Thought Process

When you see an interval problem, ask yourself:

```text
1. Are intervals static or dynamic?
2. Are they sorted already?
3. Are intervals closed or half-open?
4. Do I need overlap, gaps, intersections, or max concurrency?
5. Am I minimizing removals or maximizing selected intervals?
6. Are coordinates bounded enough for prefix sum?
7. Do I need online insertion/query behavior?
```

Then map it:

```text
static + overlap/merge        -> sort by start
static + intersections        -> two pointers
static + min removals         -> greedy by end
static + max overlap          -> sweep line / heap
bounded coordinates           -> difference array
dynamic insert/query          -> TreeMap
next interval lookup          -> binary search
```

---

## 16. Reusable Java Snippets

### Half-Open Overlap Helper

```java
private boolean overlaps(int[] a, int[] b) {
    return a[0] < b[1] && b[0] < a[1];
}
```

### Closed Interval Overlap Helper

```java
private boolean overlapsClosed(int[] a, int[] b) {
    return a[0] <= b[1] && b[0] <= a[1];
}
```

### Merge Helper

```java
private int[] merge(int[] a, int[] b) {
    return new int[]{
        Math.min(a[0], b[0]),
        Math.max(a[1], b[1])
    };
}
```

### Safe Comparator

Avoid this:

```java
(a, b) -> a[0] - b[0]
```

Use this:

```java
(a, b) -> Integer.compare(a[0], b[0])
```

Reason: subtraction can overflow.

---

## 17. Common Mistakes

1. Using `<=` instead of `<` for meeting-room conflicts.
2. Sorting by start when greedy by end is required.
3. Forgetting tie-breaking in sweep line.
4. Merging with the original previous interval instead of the last merged interval.
5. Using `int` when counts or coordinates may need `long`.
6. Not handling empty input.
7. Assuming intervals are already sorted.
8. Not clarifying whether `[1, 3]` and `[3, 5]` overlap.
9. In heap problems, forgetting to remove ended intervals.
10. In TreeMap problems, checking all intervals instead of only neighbors.

---

## 18. One-Line Recall Cheat Sheet

```text
Merge? Sort by start.
Conflict? Sort by start, compare adjacent.
Min rooms? Heap by end or sweep line.
Max overlap? Sweep line.
Remove overlaps? Sort by end.
Intersections? Two pointers.
Range updates? Difference array.
Dynamic intervals? TreeMap.
Next interval? Binary search.
```

---

## Final Interview Reminder

For FAANG-style interviews, the most important skill is identifying the pattern quickly.

Once you classify the problem as one of these:

- Merge
- Sweep line
- Greedy
- Heap
- Two pointers
- TreeMap
- Binary search
- Difference array

most interval questions become a matter of applying the right template and handling boundary conditions carefully.
