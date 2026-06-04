---
title: Monotonic Stack Interview Notes
description: Monotonic stack patterns for next greater, next smaller, histogram, stock span, and subarray contribution problems.
section: Monotonic Stack
summary: Recognize nearest-greater/smaller signals and solve them with stack invariants.
category: DSA Pattern
tags: [monotonic-stack, stack, java]
order: 70
---
# Monotonic Stack Interview Notes

FAANG-level recall guide for recognizing, modeling, and solving most monotonic stack interview problems.

---

## 1. What Monotonic Stack Solves

A **monotonic stack** helps answer questions like:

> For each element, find the nearest previous or next element that is smaller or greater.

The word **nearest** is the key signal.

Common examples:

- Next greater element
- Next smaller element
- Previous greater element
- Previous smaller element
- Daily temperatures
- Stock span
- Largest rectangle in histogram
- Sum of subarray minimums / maximums
- Visibility problems
- Remove K digits

Complexity:

```text
Time:  O(n)
Space: O(n)
```

Each element is pushed once and popped at most once.

---

## 2. Core Mental Model

Maintain a stack of elements that are still waiting for an answer.

When a new element arrives, it may resolve answers for previous elements.

Example:

```text
nums = [2, 1, 5]

stack = [2, 1]
current = 5

5 is greater than 1, so 5 is the next greater element for 1.
5 is greater than 2, so 5 is the next greater element for 2.
```

So we pop while the current element satisfies the waiting condition.

---

## 3. Two Main Stack Types

## Monotonic Increasing Stack

Values increase from bottom to top.

```text
[1, 3, 5, 8]
```

Usually used to find:

```text
next smaller
previous smaller
```

When a smaller value appears, it breaks the increasing order and resolves answers.

## Monotonic Decreasing Stack

Values decrease from bottom to top.

```text
[9, 6, 4, 2]
```

Usually used to find:

```text
next greater
previous greater
```

When a greater value appears, it breaks the decreasing order and resolves answers.

---

## 4. Decision Table

| Problem asks for | Stack type | Pop condition |
|---|---|---|
| Next Greater Element | Decreasing stack | `while curr > stack.top()` |
| Next Greater or Equal | Strictly decreasing stack | `while curr >= stack.top()` |
| Next Smaller Element | Increasing stack | `while curr < stack.top()` |
| Next Smaller or Equal | Strictly increasing stack | `while curr <= stack.top()` |
| Previous Greater Element | Decreasing stack | Pop smaller elements before reading top |
| Previous Smaller Element | Increasing stack | Pop greater elements before reading top |

---

## 5. Template: Next Greater Element

For each index, find the next element to the right that is greater.

```java
int[] nextGreater(int[] nums) {
    int n = nums.length;
    int[] ans = new int[n];
    Arrays.fill(ans, -1);

    Deque<Integer> stack = new ArrayDeque<>(); // stores indices

    for (int i = 0; i < n; i++) {
        while (!stack.isEmpty() && nums[i] > nums[stack.peek()]) {
            int idx = stack.pop();
            ans[idx] = nums[i];
        }
        stack.push(i);
    }

    return ans;
}
```

Prefer storing **indices**, not values.

Indices are useful for:

- Distance
- Width
- Position
- Duplicate handling
- Updating the answer array

---

## 6. Template: Previous Smaller Element

For each index, find the previous element to the left that is smaller.

```java
int[] prevSmaller(int[] nums) {
    int n = nums.length;
    int[] ans = new int[n];
    Arrays.fill(ans, -1);

    Deque<Integer> stack = new ArrayDeque<>(); // increasing stack

    for (int i = 0; i < n; i++) {
        while (!stack.isEmpty() && nums[stack.peek()] >= nums[i]) {
            stack.pop();
        }

        if (!stack.isEmpty()) {
            ans[i] = nums[stack.peek()];
        }

        stack.push(i);
    }

    return ans;
}
```

Flow:

```text
1. Remove elements that cannot be previous smaller.
2. Stack top is the answer.
3. Push current index.
```

---

## 7. Next vs Previous Pattern

## For Next Greater / Smaller

Assign answers to popped elements.

```java
while (!stack.isEmpty() && nums[i] > nums[stack.peek()]) {
    ans[stack.pop()] = nums[i];
}
stack.push(i);
```

## For Previous Greater / Smaller

Clean the stack first, then top is the answer.

```java
while (!stack.isEmpty() && nums[stack.peek()] >= nums[i]) {
    stack.pop();
}

ans[i] = stack.isEmpty() ? -1 : nums[stack.peek()];
stack.push(i);
```

---

## 8. Duplicate Handling: `>` vs `>=`

This is one of the most common interview bugs.

If you need a **strictly smaller** previous element:

```java
while (!stack.isEmpty() && nums[stack.peek()] >= nums[i]) {
    stack.pop();
}
```

Why `>=`?

Because equal elements are not strictly smaller.

If you need a **smaller or equal** previous element:

```java
while (!stack.isEmpty() && nums[stack.peek()] > nums[i]) {
    stack.pop();
}
```

Because equal elements are allowed.

---

## 9. Duplicate Rule for Contribution Problems

For problems like:

- Sum of Subarray Minimums
- Sum of Subarray Maximums
- Largest Rectangle in Histogram

Duplicates must be handled carefully to avoid double counting.

For **sum of subarray minimums**, use either:

```text
previous strictly smaller
next smaller or equal
```

or:

```text
previous smaller or equal
next strictly smaller
```

Do not use strict on both sides or non-strict on both sides.

Core idea:

```text
Equal elements should be owned by exactly one side.
```

---

## 10. Pattern: Daily Temperatures

Question:

> For each day, how many days until a warmer temperature?

This is **next greater element distance**.

```java
int[] dailyTemperatures(int[] temperatures) {
    int n = temperatures.length;
    int[] ans = new int[n];
    Deque<Integer> stack = new ArrayDeque<>();

    for (int i = 0; i < n; i++) {
        while (!stack.isEmpty() && temperatures[i] > temperatures[stack.peek()]) {
            int prev = stack.pop();
            ans[prev] = i - prev;
        }
        stack.push(i);
    }

    return ans;
}
```

Pattern:

```text
Current element resolves previous unresolved smaller elements.
```

---

## 11. Pattern: Stock Span

Question:

> For each day, find how many consecutive previous days had price <= current price.

This is **previous greater element**.

```java
class StockSpanner {
    Deque<int[]> stack = new ArrayDeque<>();
    // each element: [price, span]

    public int next(int price) {
        int span = 1;

        while (!stack.isEmpty() && stack.peek()[0] <= price) {
            span += stack.pop()[1];
        }

        stack.push(new int[]{price, span});
        return span;
    }
}
```

Technique:

```text
Instead of storing every index, store compressed pairs: price + accumulated span.
```

This is useful in streaming problems.

---

## 12. Pattern: Largest Rectangle in Histogram

For every bar, find how far it can extend left and right while remaining the minimum height.

Use an increasing stack.

When current height is smaller than stack top, the popped bar cannot extend further right.

```java
int largestRectangleArea(int[] heights) {
    int n = heights.length;
    Deque<Integer> stack = new ArrayDeque<>();
    int maxArea = 0;

    for (int i = 0; i <= n; i++) {
        int currHeight = (i == n) ? 0 : heights[i];

        while (!stack.isEmpty() && currHeight < heights[stack.peek()]) {
            int height = heights[stack.pop()];
            int right = i;
            int left = stack.isEmpty() ? -1 : stack.peek();

            int width = right - left - 1;
            maxArea = Math.max(maxArea, height * width);
        }

        stack.push(i);
    }

    return maxArea;
}
```

Key formula:

```text
width = rightLessIndex - leftLessIndex - 1
area = height * width
```

The sentinel `i == n ? 0 : heights[i]` forces all remaining bars to be processed.

---

## 13. Pattern: Maximal Rectangle in Binary Matrix

This is a 2D extension of histogram.

For each row, convert matrix into histogram heights.

```text
1 0 1 1
1 1 1 1
1 1 1 0
```

Heights row by row:

```text
row 0: [1, 0, 1, 1]
row 1: [2, 1, 2, 2]
row 2: [3, 2, 3, 0]
```

For every row, run **Largest Rectangle in Histogram**.

```java
int maximalRectangle(char[][] matrix) {
    if (matrix.length == 0) return 0;

    int rows = matrix.length;
    int cols = matrix[0].length;
    int[] heights = new int[cols];

    int ans = 0;

    for (int r = 0; r < rows; r++) {
        for (int c = 0; c < cols; c++) {
            if (matrix[r][c] == '1') {
                heights[c]++;
            } else {
                heights[c] = 0;
            }
        }

        ans = Math.max(ans, largestRectangleArea(heights));
    }

    return ans;
}
```

---

## 14. Pattern: Sum of Subarray Minimums

Question:

> Sum the minimum value of every subarray.

Naive is `O(n^2)`. Monotonic stack makes it `O(n)`.

For every element `arr[i]`, calculate how many subarrays use it as the minimum.

```text
contribution = arr[i] * countLeft * countRight
```

Where:

```text
countLeft  = number of choices for left boundary
countRight = number of choices for right boundary
```

Use:

```text
previous less element
next less or equal element
```

```java
int sumSubarrayMins(int[] arr) {
    int n = arr.length;
    int mod = 1_000_000_007;

    int[] prevLess = new int[n];
    int[] nextLessOrEqual = new int[n];

    Deque<Integer> stack = new ArrayDeque<>();

    // Previous strictly less
    for (int i = 0; i < n; i++) {
        while (!stack.isEmpty() && arr[stack.peek()] >= arr[i]) {
            stack.pop();
        }
        prevLess[i] = stack.isEmpty() ? -1 : stack.peek();
        stack.push(i);
    }

    stack.clear();

    // Next less or equal
    for (int i = n - 1; i >= 0; i--) {
        while (!stack.isEmpty() && arr[stack.peek()] > arr[i]) {
            stack.pop();
        }
        nextLessOrEqual[i] = stack.isEmpty() ? n : stack.peek();
        stack.push(i);
    }

    long ans = 0;

    for (int i = 0; i < n; i++) {
        long left = i - prevLess[i];
        long right = nextLessOrEqual[i] - i;
        ans = (ans + arr[i] * left * right) % mod;
    }

    return (int) ans;
}
```

Key idea:

```text
arr[i] is minimum for all subarrays starting after prevLess[i]
and ending before nextLessOrEqual[i].
```

---

## 15. Pattern: Sum of Subarray Ranges

Question:

> Sum over all subarrays: max(subarray) - min(subarray)

Break it into:

```text
sum of all subarray maximums - sum of all subarray minimums
```

Use monotonic stack twice.

For minimum contribution:

```text
previous less
next less or equal
```

For maximum contribution:

```text
previous greater
next greater or equal
```

Formula:

```text
contribution = nums[i] * leftChoices * rightChoices
```

---

## 16. Pattern: Remove K Digits

Question:

> Remove k digits to make the smallest possible number.

This is a monotonic increasing stack.

You want smaller digits earlier.

```java
String removeKdigits(String num, int k) {
    Deque<Character> stack = new ArrayDeque<>();

    for (char c : num.toCharArray()) {
        while (!stack.isEmpty() && k > 0 && stack.peek() > c) {
            stack.pop();
            k--;
        }
        stack.push(c);
    }

    while (k > 0 && !stack.isEmpty()) {
        stack.pop();
        k--;
    }

    StringBuilder sb = new StringBuilder();

    while (!stack.isEmpty()) {
        sb.append(stack.removeLast());
    }

    while (sb.length() > 0 && sb.charAt(0) == '0') {
        sb.deleteCharAt(0);
    }

    return sb.length() == 0 ? "0" : sb.toString();
}
```

Pattern:

```text
Maintain increasing digits.
If a bigger digit appears before a smaller digit, remove the bigger digit.
```

---

## 17. Pattern: Trapping Rain Water with Stack

The stack solution uses a decreasing stack.

When current height is greater than the stack top, water may be trapped.

```java
int trap(int[] height) {
    int water = 0;
    Deque<Integer> stack = new ArrayDeque<>();

    for (int i = 0; i < height.length; i++) {
        while (!stack.isEmpty() && height[i] > height[stack.peek()]) {
            int bottom = stack.pop();

            if (stack.isEmpty()) break;

            int left = stack.peek();
            int width = i - left - 1;
            int boundedHeight = Math.min(height[left], height[i]) - height[bottom];

            water += width * boundedHeight;
        }

        stack.push(i);
    }

    return water;
}
```

Mental model:

```text
Stack keeps descending bars.
When a taller right wall appears, pop the valley bottom and compute trapped water.
```

---

## 18. Pattern: Asteroid Collision

This is not always called monotonic stack, but it is a stack simulation pattern.

```java
int[] asteroidCollision(int[] asteroids) {
    Deque<Integer> stack = new ArrayDeque<>();

    for (int asteroid : asteroids) {
        boolean alive = true;

        while (alive && asteroid < 0 && !stack.isEmpty() && stack.peek() > 0) {
            if (stack.peek() < -asteroid) {
                stack.pop();
            } else if (stack.peek() == -asteroid) {
                stack.pop();
                alive = false;
            } else {
                alive = false;
            }
        }

        if (alive) {
            stack.push(asteroid);
        }
    }

    int[] ans = new int[stack.size()];
    for (int i = ans.length - 1; i >= 0; i--) {
        ans[i] = stack.pop();
    }

    return ans;
}
```

Pattern:

```text
Use stack when only neighboring unresolved items can interact.
```

---

## 19. Circular Array Monotonic Stack

Example: **Next Greater Element II**

For circular arrays, iterate twice.

```java
int[] nextGreaterElements(int[] nums) {
    int n = nums.length;
    int[] ans = new int[n];
    Arrays.fill(ans, -1);

    Deque<Integer> stack = new ArrayDeque<>();

    for (int i = 0; i < 2 * n; i++) {
        int idx = i % n;

        while (!stack.isEmpty() && nums[idx] > nums[stack.peek()]) {
            ans[stack.pop()] = nums[idx];
        }

        if (i < n) {
            stack.push(idx);
        }
    }

    return ans;
}
```

Important:

```text
Only push indices during the first pass.
Use the second pass only to resolve answers.
```

---

## 20. How to Recognize Monotonic Stack Problems

Look for phrases like:

- Next greater
- Next smaller
- Previous greater
- Previous smaller
- Nearest greater
- Nearest smaller
- First greater to the right
- First smaller to the left
- Days until warmer temperature
- Stock span
- Largest rectangle
- Subarray minimums
- Subarray maximums
- Visible people
- Remove K digits
- Lexicographically smallest

Also notice constraints:

```text
n up to 10^5
Need O(n)
Naive O(n^2) scans left/right are too slow
```

---

## 21. Interview Decision Process

Before coding, ask these questions.

## Step 1: Am I looking left or right?

```text
Previous something -> usually scan left to right.
Next something -> scan left to right and resolve popped elements,
                  or scan right to left and answer directly.
```

## Step 2: Greater or smaller?

```text
Greater -> decreasing stack.
Smaller -> increasing stack.
```

## Step 3: Strict or non-strict?

```text
greater          -> >
greater or equal -> >=
smaller          -> <
smaller or equal -> <=
```

## Step 4: Do I need values or indices?

Prefer indices by default.

## Step 5: Is it circular?

Use a `2 * n` loop.

## Step 6: Is it contribution counting?

Use:

```text
leftChoices * rightChoices * nums[i]
```

---

## 22. Common Mistakes

## Mistake 1: Storing values instead of indices

This fails when duplicates exist or distance is needed.

Prefer:

```java
Deque<Integer> stack = new ArrayDeque<>(); // indices
```

## Mistake 2: Wrong duplicate handling

For strict previous smaller:

```java
while (arr[stack.peek()] >= arr[i]) pop;
```

For smaller or equal:

```java
while (arr[stack.peek()] > arr[i]) pop;
```

## Mistake 3: Forgetting sentinel in histogram

Either append a `0`, or loop with `i <= n`.

## Mistake 4: Using `Stack<Integer>` in Java

Prefer:

```java
Deque<Integer> stack = new ArrayDeque<>();
```

Use:

```java
stack.push(x);
stack.pop();
stack.peek();
```

## Mistake 5: Not clearing stack between passes

For contribution problems, you often need two separate passes.

```java
stack.clear();
```

---

## 23. Must-Practice Problems

1. Next Greater Element I
2. Next Greater Element II
3. Daily Temperatures
4. Online Stock Span
5. Largest Rectangle in Histogram
6. Maximal Rectangle
7. Sum of Subarray Minimums
8. Sum of Subarray Ranges
9. Remove K Digits
10. Trapping Rain Water
11. 132 Pattern
12. Number of Visible People in a Queue
13. Car Fleet
14. Asteroid Collision
15. Create Maximum Number

---

## 24. Quick Template Cheat Sheet

## Next Greater

```java
for (int i = 0; i < n; i++) {
    while (!st.isEmpty() && nums[i] > nums[st.peek()]) {
        ans[st.pop()] = nums[i];
    }
    st.push(i);
}
```

## Next Smaller

```java
for (int i = 0; i < n; i++) {
    while (!st.isEmpty() && nums[i] < nums[st.peek()]) {
        ans[st.pop()] = nums[i];
    }
    st.push(i);
}
```

## Previous Greater

```java
for (int i = 0; i < n; i++) {
    while (!st.isEmpty() && nums[st.peek()] <= nums[i]) {
        st.pop();
    }

    ans[i] = st.isEmpty() ? -1 : nums[st.peek()];
    st.push(i);
}
```

## Previous Smaller

```java
for (int i = 0; i < n; i++) {
    while (!st.isEmpty() && nums[st.peek()] >= nums[i]) {
        st.pop();
    }

    ans[i] = st.isEmpty() ? -1 : nums[st.peek()];
    st.push(i);
}
```

---

## 25. Interview Explanation One-Liner

> A monotonic stack keeps only the elements that are still useful candidates. Whenever the current element makes previous elements impossible candidates, we pop them and often resolve their answer immediately.

---

## 26. Pre-Coding Checklist

Before writing code, answer these six questions:

- Need next or previous?
- Need greater or smaller?
- Strict or equal allowed?
- Stack should be increasing or decreasing?
- Store index or value?
- Answer while popping or after popping?

Once you can answer these, most monotonic stack problems become mechanical.

---

## 27. Fast Recall Summary

| Signal | Technique |
|---|---|
| Nearest greater/smaller | Monotonic stack |
| Need distance | Store indices |
| Circular array | Loop `2 * n`, push only first pass |
| Histogram width | `rightLess - leftLess - 1` |
| Subarray contribution | `value * leftChoices * rightChoices` |
| Duplicates | Make exactly one side strict |
| Streaming span | Store compressed pair `[value, count]` |
| Java stack | Use `ArrayDeque`, not `Stack` |
