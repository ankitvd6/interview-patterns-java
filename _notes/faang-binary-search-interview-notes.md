---
title: Binary Search Master Notes
description: Java-focused binary search patterns for exact match, lower bound, upper bound, and search-on-answer problems.
section: Binary Search
summary: Core mental model, templates, pointer rules, and FAANG-style binary search patterns in Java.
category: DSA Pattern
tags: [binary-search, java, faang]
order: 10
---

# FAANG Interview Prep – Binary Search Master Notes (Java Edition)

## Core Mental Model

Binary Search is **not about sorted arrays**.

It is about searching a **monotonic search space**:

```text
FFFFFTTTTT
```

or

```text
TTTTTFFFFF
```

Find the transition point.

### Golden Questions

1. What is the search space?
2. What is the monotonic predicate?
3. Am I looking for:
   - exact match?
   - first valid?
   - last valid?
   - minimum feasible?
   - maximum feasible?

---

# Exact Match vs Lower Bound vs Upper Bound

## Exact Match

Question:

> Does target exist?

Invariant:

> If target exists, it is inside [left, right]

Template:

```java
while (left <= right) {
    int mid = left + (right - left) / 2;

    if (arr[mid] == target) return mid;

    if (arr[mid] < target) {
        left = mid + 1;
    } else {
        right = mid - 1;
    }
}
```

Key Insight:

When comparing:

```java
arr[mid] < target
```

`mid` can never be the answer.

Discard it.

---

## Lower Bound (First True)

Question:

> Find first position where condition becomes true.

Pattern:

```text
F F F F T T T
        ^
```

Template:

```java
while (left < right) {

    int mid = left + (right - left) / 2;

    if (condition(mid)) {
        right = mid;
    } else {
        left = mid + 1;
    }
}
```

Key Insight:

If condition is true:

```java
right = mid;
```

because **mid may still be the answer**.

Never do:

```java
right = mid - 1;
```

for lower bound.

---

## Upper Bound (Last True)

Question:

> Find last valid position.

Pattern:

```text
T T T T F F F
      ^
```

Template:

```java
while (left < right) {

    int mid = left + (right - left + 1) / 2;

    if (condition(mid)) {
        left = mid;
    } else {
        right = mid - 1;
    }
}
```

Key Insight:

If condition is true:

```java
left = mid;
```

because mid may still be answer.

Right-biased mid prevents infinite loops.

---

# The Most Important Binary Search Rule

Pointer movement depends on one question:

> Can mid still be the answer?

If NO:

```java
left = mid + 1;
right = mid - 1;
```

If YES:

```java
left = mid;
right = mid;
```

(depending on search direction)

This explains every binary search variation.

---

# Binary Search on Answer

Most FAANG binary search problems are actually:

> Find minimum X such that condition(X) is true

or

> Find maximum X such that condition(X) is true

Template:

```java
while (left < right) {

    int mid = left + (right - left) / 2;

    if (feasible(mid)) {
        right = mid;
    } else {
        left = mid + 1;
    }
}
```

Examples:

- Koko Eating Bananas
- Capacity to Ship Packages
- Split Array Largest Sum
- Minimum Days to Make Bouquets
- Aggressive Cows
- Painter's Partition

---

# Binary Search Pattern Summary

| Pattern | Key Idea |
|----------|----------|
| Exact Search | Find exact value |
| Lower Bound | First true |
| Upper Bound | Last true |
| Answer Search | Binary search over values |
| Rotated Array | One half always sorted |
| Peak Finding | Move toward increasing slope |
| Infinite Array | Exponential expansion |
| Continuous Search | Binary search on doubles |

---

# Most Important Binary Search Problems

## Tier 1

- LC 35 – Search Insert Position
- LC 34 – Find First and Last Position
- LC 278 – First Bad Version
- LC 875 – Koko Eating Bananas
- LC 1011 – Ship Packages Within D Days
- LC 33 – Search in Rotated Sorted Array
- LC 162 – Find Peak Element

## Tier 2

- LC 410 – Split Array Largest Sum
- LC 1482 – Minimum Days to Make Bouquets
- LC 1552 – Magnetic Force Between Two Balls
- LC 153 – Find Minimum in Rotated Sorted Array
- LC 69 – Sqrt(x)

## Tier 3

- LC 4 – Median of Two Sorted Arrays
- LC 719 – K-th Smallest Pair Distance
- LC 378 – K-th Smallest Element in Sorted Matrix

---

# Post-Binary Search Roadmap

Recommended order:

```text
Binary Search ✅
↓
Sliding Window
↓
Prefix Sum
↓
Kadane
↓
Two Pointers
↓
Intervals
↓
Monotonic Stack
↓
Heap
↓
Trees
↓
Graphs
↓
Dynamic Programming
```

---

# Why Sliding Window Next?

Higher interview ROI than Kadane.

Signals:

```text
contiguous
substring
subarray
window
longest
shortest
at most K
exactly K
```

Usually ⇒ Sliding Window

---

# Sliding Window Core Problems

1. LC 121 – Best Time to Buy and Sell Stock
2. LC 643 – Maximum Average Subarray I
3. LC 3 – Longest Substring Without Repeating Characters
4. LC 567 – Permutation in String
5. LC 438 – Find All Anagrams in a String
6. LC 1004 – Max Consecutive Ones III
7. LC 424 – Longest Repeating Character Replacement
8. LC 209 – Minimum Size Subarray Sum
9. LC 76 – Minimum Window Substring

---

# Prefix Sum / Kadane Set

10. LC 53 – Maximum Subarray
11. LC 560 – Subarray Sum Equals K
12. LC 974 – Subarray Sums Divisible by K
13. LC 238 – Product Except Self
14. LC 918 – Maximum Sum Circular Subarray

---

# Final Binary Search Interview Checklist

Before coding:

### Step 1
Is there a monotonic property?

### Step 2
Define search space.

### Step 3
Define:

```java
boolean feasible(int x)
```

### Step 4
Determine:

- exact match?
- first valid?
- last valid?
- minimum feasible?
- maximum feasible?

### Step 5
Choose template.

---

# Most Important Sentence

> Pointer movement depends on whether mid can still be the answer.

If this is understood deeply, binary search no longer requires memorization.
