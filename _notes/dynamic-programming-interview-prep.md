---
title: Dynamic Programming Interview Prep
description: FAANG-level dynamic programming patterns, states, recurrences, memoization, tabulation, and optimization techniques.
section: Dynamic Programming
summary: Recognition checklist, state design, recurrence patterns, and high-ROI DP templates for interviews.
category: DSA Pattern
tags: [dynamic-programming, java, faang]
order: 20
---
For FAANG-level interviews, the goal is **not memorizing 200 DP problems**. The goal is developing a systematic way to recognize and build DP solutions.

A strong candidate should be able to look at a new problem and think:

> "What's the state? What's the recurrence? What are the choices? Can I reduce dimensions? Can I optimize space?"

---

# 1. DP Recognition Checklist

A problem is likely DP if:

### ✅ Optimal Substructure
The answer can be built from smaller answers.

Examples:
- Longest Increasing Subsequence
- Coin Change
- Edit Distance

### ✅ Overlapping Subproblems
The same subproblem appears repeatedly.

Example:

```text
f(10)
 ├─ f(9)
 │   ├─ f(8)
 │   └─ f(7)
 └─ f(8)
```

`f(8)` is computed multiple times.

### Common Interview Keywords

- Count ways
- Minimum cost
- Maximum profit
- Longest
- Shortest
- Can we reach?
- Partition
- Subsequence
- Probability
- Expected value

Whenever you see these → think DP.

---

# 2. The Universal DP Framework

Every DP solution can be derived using:

## Step 1: Define State

Ask:

> "What information uniquely identifies a subproblem?"

Examples:

### Fibonacci

```text
dp[i]
```

Meaning:
Answer for first i numbers.

---

### House Robber

```text
dp[i]
```

Maximum money up to house i.

---

### LIS

```text
dp[i]
```

Longest increasing subsequence ending at i.

---

### Edit Distance

```text
dp[i][j]
```

Answer using first i chars of word1 and first j chars of word2.

---

## Step 2: Define Choices

What decisions exist?

### House Robber

At house i:

```text
Rob
Skip
```

---

### Knapsack

For item i:

```text
Take
Don't Take
```

---

### Stock Problems

At day i:

```text
Buy
Sell
Hold
```

---

## Step 3: Write Recurrence

Example:

House Robber

```text
dp[i] = max(
    dp[i-1],
    nums[i] + dp[i-2]
)
```

---

Knapsack

```text
dp[i][w] =
 max(
    skip,
    take
 )
```

---

Edit Distance

```text
Insert
Delete
Replace
```

Choose minimum.

---

## Step 4: Base Cases

Example:

```text
dp[0]
dp[1]
```

Without correct base cases DP fails.

---

## Step 5: Memoization or Tabulation

### Top Down

```python
@cache
def dfs(state):
```

Pros:
- Easier during interview
- Natural recursion

---

### Bottom Up

```python
for ...
```

Pros:
- Better optimization
- Avoid recursion depth

---

# 3. The 10 Most Important DP Patterns

Master these and you can solve ~80% of interview DP.

---

# Pattern 1: Linear DP

State depends on previous positions.

Examples:

- Fibonacci
- Climbing Stairs
- House Robber
- Min Cost Climbing Stairs

Template:

```python
dp[i] = f(dp[i-1], dp[i-2])
```

---

# Pattern 2: Knapsack DP

The king of interview DP.

### 0/1 Knapsack

Take item once.

State:

```python
dp[i][capacity]
```

Problems:

- Partition Equal Subset Sum
- Target Sum
- Last Stone Weight II

Template:

```python
take
not_take
```

---

### Unbounded Knapsack

Take unlimited times.

Problems:

- Coin Change
- Combination Sum IV

---

# Pattern 3: Subsequence DP

State:

```python
dp[i][j]
```

Compare two strings.

Problems:

- LCS
- Edit Distance
- Distinct Subsequences

Template:

```python
if match:
    ...
else:
    ...
```

---

# Pattern 4: Interval DP

State:

```python
dp[l][r]
```

Meaning answer within interval.

Problems:

- Burst Balloons
- Matrix Chain Multiplication
- Palindrome Partitioning

Template:

```python
for k in range(l,r):
```

Choose partition point.

---

# Pattern 5: Grid DP

State:

```python
dp[row][col]
```

Problems:

- Unique Paths
- Minimum Path Sum
- Cherry Pickup

Movement:

```python
up
left
down
right
```

---

# Pattern 6: Decision DP

Every state has choices.

Examples:

- House Robber
- Stock Problems
- Jump Game

Think:

```text
At this point what are my choices?
```

---

# Pattern 7: State Machine DP

Extremely common in FAANG.

State includes status.

Examples:

Stock Buy Sell

```text
day
holding?
transactions?
```

State:

```python
dp[day][holding][k]
```

Problems:

- Best Time to Buy and Sell Stock I-VI

---

# Pattern 8: Partition DP

Split array/string.

Examples:

- Word Break
- Palindrome Partitioning
- Partition Array for Maximum Sum

Template:

```python
for cut in range(...)
```

---

# Pattern 9: Bitmask DP

Usually:

```text
N <= 20
```

Huge clue.

State:

```python
dp[mask]
```

Problems:

- Traveling Salesman
- Assign Tasks

Complexity:

```text
O(2^N * N)
```

---

# Pattern 10: Digit DP

FAANG occasionally asks.

State:

```python
position
tight
leadingZero
```

Problems:

- Count numbers with property X

Very advanced.

---

# 4. Dimensional Thinking

One of the biggest interview skills.

## 1D DP

```python
dp[i]
```

Examples:

- House Robber
- LIS

---

## 2D DP

```python
dp[i][j]
```

Examples:

- LCS
- Edit Distance

---

## 3D DP

```python
dp[i][j][k]
```

Examples:

- Stock with K transactions
- Cherry Pickup

---

Rule:

> Number of variables needed to identify subproblem = DP dimensions.

---

# 5. Space Optimization Tricks

Interviewers love this.

---

### Fibonacci

From:

```python
O(n)
```

To:

```python
O(1)
```

Store only previous values.

---

### Grid DP

Instead of:

```python
m*n
```

Store one row:

```python
O(n)
```

---

### Knapsack

From:

```python
dp[i][w]
```

To:

```python
dp[w]
```

Reverse iterate.

---

# 6. The Most Important DP Questions

If you only do 20:

### Easy

- Climbing Stairs
- House Robber
- Min Cost Climbing Stairs

### Knapsack

- Coin Change
- Coin Change II
- Partition Equal Subset Sum
- Target Sum

### Subsequence

- LCS
- Edit Distance
- Distinct Subsequences

### LIS Family

- LIS
- Number of LIS

### Grid

- Unique Paths
- Minimum Path Sum

### Interval

- Burst Balloons
- Palindrome Partitioning II

### Stocks

- Stock I
- Stock II
- Stock with Cooldown
- Stock with Transaction Fee
- Stock IV

---

# 7. FAANG Interview Heuristic

When stuck, ask these 5 questions:

### 1

What are the choices?

```text
Take / Skip?
Buy / Sell?
Split / Don't Split?
```

---

### 2

What variables define the state?

```text
Index?
Capacity?
Remaining target?
Transactions left?
```

---

### 3

Can I express answer using smaller states?

```text
dp[current] = ...
```

---

### 4

What are base cases?

---

### 5

Can memoization solve it first?

Always start with:

```python
dfs(state)
```

Then optimize.

---

# DP Cheat Sheet

| Pattern | State |
|----------|---------|
| Linear | `dp[i]` |
| Grid | `dp[r][c]` |
| LCS/Edit Distance | `dp[i][j]` |
| Knapsack | `dp[i][cap]` |
| Interval | `dp[l][r]` |
| Stock | `dp[day][holding]` |
| Partition | `dp[i]` |
| Bitmask | `dp[mask]` |
| Digit DP | `dp[pos][tight]` |

---

For FAANG specifically, prioritize learning DP in this order:

**Linear DP → Knapsack → Grid DP → LCS/Edit Distance → LIS → Stock State Machines → Interval DP → Bitmask DP**

Once these patterns become instinctive, most interview DP questions stop looking like unique problems and start looking like slight variations of a handful of templates.
