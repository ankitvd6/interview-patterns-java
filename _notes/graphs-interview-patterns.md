---
title: Graphs Interview Patterns
description: Graph modeling, traversal, shortest path, union find, topological sort, and advanced graph interview patterns.
section: Graphs
summary: Model graphs clearly, pick the right traversal, and recognize the core patterns behind FAANG graph problems.
category: DSA Pattern
tags: [graphs, bfs, dfs, java]
order: 30
---
For FAANG-level interviews, Graphs are less about memorizing hundreds of problems and more about recognizing **which graph model + traversal pattern** fits the problem.

A strong graph interview framework is:

> **Model -> Traverse -> Optimize**

If you master ~15 patterns, you'll solve 80-90% of graph questions.

---

# 1. Graph Recognition Pattern

Many graph questions don't mention graphs.

Learn to recognize hidden graphs:

| Problem Statement | Graph Interpretation |
|---|---|
| Cities connected by roads | Graph |
| Users connected by friendships | Graph |
| Course prerequisites | Directed Graph |
| Dependencies between services | Directed Graph |
| Word Ladder | Implicit Graph |
| Grid with moves | Graph |
| Islands in matrix | Graph |
| Flights between airports | Weighted Graph |
| Transform A to B | State Space Graph |

---

# 2. Graph Representation

## Adjacency List

Most common representation.

```java
Map<Integer, List<Integer>> graph = new HashMap<>();
```

Time and space:

```text
Space = O(V + E)
```

Use for almost everything.

## Adjacency Matrix

```java
int[][] graph = new int[n][n];
```

Use when:

```text
Dense graph
Need O(1) edge lookup
```

---

# 3. DFS Pattern

## Template

```java
void dfs(int node) {
    visited.add(node);

    for (int nei : graph.get(node)) {
        if (!visited.contains(nei)) {
            dfs(nei);
        }
    }
}
```

## When to Use DFS

- Connected Components
- Cycle Detection
- Topological Sort
- Tree Problems
- Backtracking

Examples:

```text
Number of Provinces
Number of Islands
Friend Circles
```

---

# 4. BFS Pattern

## Template

```java
Queue<Integer> q = new LinkedList<>();

q.offer(start);
visited.add(start);

while (!q.isEmpty()) {
    int node = q.poll();

    for (int nei : graph.get(node)) {
        if (!visited.contains(nei)) {
            visited.add(nei);
            q.offer(nei);
        }
    }
}
```

## When BFS is Preferred

Whenever you hear:

```text
Shortest path
Minimum steps
Fewest moves
Minimum operations
```

In an unweighted graph:

```text
BFS = shortest path
```

Examples:

```text
Word Ladder
Open Lock
Rotting Oranges
Shortest Path in Binary Matrix
```

---

# 5. Multi-Source BFS

Huge FAANG favorite.

Instead of:

```java
queue.add(source);
```

Add **all sources**:

```java
for (all sources) {
    queue.add(source);
}
```

Examples:

```text
Rotting Oranges
Walls and Gates
01 Matrix
Nearest Exit
```

---

# 6. Cycle Detection

## Undirected Graph

Use parent tracking.

```java
boolean dfs(node, parent)
```

If visited neighbor is not parent:

```text
Cycle found
```

## Directed Graph

Use 3 states.

```java
0 = unvisited
1 = visiting
2 = visited
```

If you encounter:

```java
state[neighbor] == 1
```

Cycle exists.

Questions:

```text
Course Schedule
Alien Dictionary
Dependency Graphs
```

---

# 7. Topological Sort

Extremely important.

Used whenever you see:

```text
Dependencies
Prerequisites
Build order
Task scheduling
```

## DFS Topological Sort

```text
postorder traversal
reverse result
```

## Kahn's Algorithm

Preferred for many interview problems.

Compute indegrees:

```java
indegree[node]
```

Push nodes where:

```text
indegree == 0
```

Then process queue.

Questions:

```text
Course Schedule
Course Schedule II
Alien Dictionary
Build Systems
```

---

# 8. Union Find / Disjoint Set Union

One of FAANG's favorite graph tools.

Supports:

```text
Connected?
Merge?
```

efficiently.

Operations:

```java
find(x)
union(x, y)
```

Optimizations:

```text
Path Compression
Union by Rank
```

Questions:

```text
Number of Provinces
Redundant Connection
Accounts Merge
Graph Valid Tree
```

Complexity:

```text
Almost O(1)
```

---

# 9. Dijkstra

Weighted graph shortest path.

Recognize:

```text
Minimum cost
Cheapest route
Weighted edges
Positive weights
```

Template idea:

```java
PriorityQueue<Pair>
```

Store:

```java
(distance, node)
```

Questions:

```text
Network Delay Time
Cheapest Flights
Path with Minimum Effort
```

Complexity:

```text
O(E log V)
```

---

# 10. Bellman-Ford

Use when:

```text
Negative weights
```

exist.

Questions:

```text
Currency Exchange
Negative Cost Paths
```

Complexity:

```text
O(VE)
```

Rare in interviews.

---

# 11. Floyd-Warshall

All-pairs shortest path.

```text
Every node to every node
```

Complexity:

```text
O(N^3)
```

Usually asked only by senior interviewers.

---

# 12. Minimum Spanning Tree

Goal:

```text
Connect everything
Minimum cost
```

Keywords:

```text
Connect all cities
Minimum cable
Network setup
```

Algorithms:

## Kruskal

Sort edges.

Use Union Find.

## Prim

Use Priority Queue.

Grow MST.

Questions:

```text
Connecting Cities With Minimum Cost
Min Cost Network
```

---

# 13. Bipartite Graph

Very common.

Observation:

Can color graph using 2 colors.

```java
color[node]
```

If adjacent nodes have same color:

```text
Not Bipartite
```

Questions:

```text
Possible Bipartition
Graph Coloring
Matching Problems
```

---

# 14. Grid Graph Pattern

Probably the most frequent graph interview category.

Directions:

```java
int[][] dirs = {
    {1, 0},
    {-1, 0},
    {0, 1},
    {0, -1}
};
```

Usually solved via:

```text
DFS
BFS
Union Find
```

Questions:

```text
Number of Islands
Flood Fill
Surrounded Regions
Pacific Atlantic
Rotting Oranges
```

---

# 15. Backtracking on Graphs

When problem asks:

```text
All paths
Enumerate routes
Generate combinations
```

Template:

```java
path.add(node);

dfs(neighbor);

path.removeLast();
```

Questions:

```text
All Paths Source to Target
Word Search
N Queens
Hamiltonian Path
```

---

# 16. DAG Dynamic Programming

Very common in FAANG.

Instead of traversing repeatedly:

```java
memo[node]
```

Store answer.

Questions:

```text
Longest Path in DAG
Course Completion Count
Path Counting
```

---

# 17. Graph Interview Decision Tree

```text
Graph?
|
+-- Need traversal?
|   +-- DFS
|   +-- BFS
|
+-- Shortest Path?
|   +-- Unweighted -> BFS
|   +-- Positive Weights -> Dijkstra
|   +-- Negative Weights -> Bellman-Ford
|
+-- Dependency Ordering?
|   +-- Topological Sort
|
+-- Connectivity?
|   +-- DFS/BFS
|   +-- Union Find
|
+-- Minimum Cost Connect All?
|   +-- MST
|
+-- Two Group Partition?
|   +-- Bipartite
|
+-- Grid?
    +-- DFS
    +-- BFS
    +-- Union Find
```

---

# Most Important LeetCode Problems

## DFS/BFS

- 200 Number of Islands
- 695 Max Area of Island
- 733 Flood Fill
- 994 Rotting Oranges

## Topological Sort

- 207 Course Schedule
- 210 Course Schedule II
- 269 Alien Dictionary

## Union Find

- 547 Number of Provinces
- 684 Redundant Connection
- 721 Accounts Merge

## Dijkstra

- 743 Network Delay Time
- 787 Cheapest Flights Within K Stops
- 1631 Path With Minimum Effort

## BFS Shortest Path

- 127 Word Ladder
- 752 Open the Lock
- 1091 Shortest Path in Binary Matrix

## Advanced

- 1584 Min Cost to Connect Points
- 785 Is Graph Bipartite
- 329 Longest Increasing Path in Matrix
- 1192 Critical Connections

---

# Highest ROI Topics for FAANG L5-Level Interviews

1. DFS/BFS mastery
2. Topological Sort
3. Union Find
4. Dijkstra
5. Grid Graphs
6. Multi-source BFS
7. Cycle Detection
8. Bipartite Graphs
9. MST
10. Tarjan: Bridges and Articulation Points

If you can solve these 25-30 representative problems from memory and explain the pattern selection clearly, you'll be prepared for the vast majority of graph rounds at Meta, Google, Amazon, Uber, Airbnb, DoorDash, and similar companies.
