---
title: Kadane Algorithm Interview Notes
description: Kadane patterns for maximum subarray, circular subarrays, product variants, and dynamic programming extensions.
section: Kadane Algorithm
summary: Recall guide for contiguous subarray optimization and Kadane-style state transitions.
category: DSA Pattern
tags: [kadane, arrays, dynamic-programming]
order: 60
---
# Kadane Algorithm Interview Notes

> FAANG-level recall guide for Kadane patterns, variants, and interview techniques.

---

## 1. Core Kadane: What You Must Remember

Kadane solves:

> **Maximum sum contiguous subarray**

The key idea:

```text
At every index i, decide:
Either extend the previous subarray
or start fresh at i.
```

```java
curr = Math.max(nums[i], curr + nums[i]);
best = Math.max(best, curr);
```

### Java Template

```java
int maxSubArray(int[] nums) {
    int curr = nums[0];
    int best = nums[0];

    for (int i = 1; i < nums.length; i++) {
        curr = Math.max(nums[i], curr + nums[i]);
        best = Math.max(best, curr);
    }

    return best;
}
```

### Interview Invariant

At index `i`:

```text
curr = best subarray sum ending exactly at i
best = best subarray sum seen anywhere so far
```

That invariant is the heart of Kadane.

---

## 2. Pattern: Maximum Subarray Sum

Classic problem.

```text
nums = [-2,1,-3,4,-1,2,1,-5,4]
answer = 6
subarray = [4,-1,2,1]
```

Use normal Kadane.

Important edge case:

```text
[-5, -2, -8]
answer = -2
```

Do **not** initialize `curr = 0` unless empty subarray is allowed.

For interviews, usually empty subarray is **not allowed**, so initialize with:

```java
int curr = nums[0];
int best = nums[0];
```

---

## 3. Pattern: Return Actual Subarray Indices

Sometimes they ask for start and end indices.

```java
int[] maxSubArrayIndices(int[] nums) {
    int curr = nums[0];
    int best = nums[0];

    int start = 0;
    int bestStart = 0;
    int bestEnd = 0;

    for (int i = 1; i < nums.length; i++) {
        if (nums[i] > curr + nums[i]) {
            curr = nums[i];
            start = i;
        } else {
            curr += nums[i];
        }

        if (curr > best) {
            best = curr;
            bestStart = start;
            bestEnd = i;
        }
    }

    return new int[]{bestStart, bestEnd, best};
}
```

Technique:

```text
When starting fresh, update tentative start.
When best improves, freeze bestStart and bestEnd.
```

---

## 4. Pattern: Minimum Subarray Sum

Same idea, but reverse the comparison.

```java
int minSubArray(int[] nums) {
    int curr = nums[0];
    int best = nums[0];

    for (int i = 1; i < nums.length; i++) {
        curr = Math.min(nums[i], curr + nums[i]);
        best = Math.min(best, curr);
    }

    return best;
}
```

Useful for:

```text
maximum circular subarray
maximum sum after removing one segment
minimum loss segment
```

---

## 5. Pattern: Maximum Circular Subarray Sum

Problem:

```text
Subarray can wrap around from end to beginning.
```

Example:

```text
nums = [5, -3, 5]
answer = 10
subarray = [5] + [5]
```

Trick:

```text
max circular sum = total sum - minimum subarray sum
```

But handle all-negative arrays carefully.

```java
int maxCircularSubarraySum(int[] nums) {
    int total = 0;

    int maxCurr = nums[0], maxBest = nums[0];
    int minCurr = nums[0], minBest = nums[0];

    total += nums[0];

    for (int i = 1; i < nums.length; i++) {
        int x = nums[i];
        total += x;

        maxCurr = Math.max(x, maxCurr + x);
        maxBest = Math.max(maxBest, maxCurr);

        minCurr = Math.min(x, minCurr + x);
        minBest = Math.min(minBest, minCurr);
    }

    if (maxBest < 0) {
        return maxBest;
    }

    return Math.max(maxBest, total - minBest);
}
```

Why all-negative matters:

```text
nums = [-3, -2, -5]
total - minSubarray = 0, but empty subarray is not allowed.
Correct answer = -2.
```

---

## 6. Pattern: Maximum Product Subarray

This is Kadane-like but not identical.

Because a negative number can turn the minimum product into maximum product.

Maintain both:

```text
maxEndingHere
minEndingHere
```

```java
int maxProduct(int[] nums) {
    int maxHere = nums[0];
    int minHere = nums[0];
    int best = nums[0];

    for (int i = 1; i < nums.length; i++) {
        int x = nums[i];

        if (x < 0) {
            int temp = maxHere;
            maxHere = minHere;
            minHere = temp;
        }

        maxHere = Math.max(x, maxHere * x);
        minHere = Math.min(x, minHere * x);

        best = Math.max(best, maxHere);
    }

    return best;
}
```

Interview insight:

```text
For sum, bad prefix can be dropped.
For product, negative bad prefix may become useful later.
```

---

## 7. Pattern: Maximum Subarray With One Deletion

Problem:

```text
Find max subarray sum after deleting at most one element.
```

Example:

```text
[1, -2, 0, 3]
answer = 4
delete -2, subarray becomes [1,0,3]
```

Use two states:

```text
noDel = max sum ending here with no deletion
oneDel = max sum ending here with one deletion already used
```

Transition:

```text
oneDel = max(oneDel + x, noDel)
noDel = max(noDel + x, x)
```

Why `oneDel = noDel`?

Because deleting current `x` means the best sum ending before `x` becomes the answer ending here with one deletion.

```java
int maximumSumWithOneDeletion(int[] arr) {
    int noDel = arr[0];
    int oneDel = 0;
    int best = arr[0];

    for (int i = 1; i < arr.length; i++) {
        int x = arr[i];

        oneDel = Math.max(oneDel + x, noDel);
        noDel = Math.max(noDel + x, x);

        best = Math.max(best, Math.max(noDel, oneDel));
    }

    return best;
}
```

For stricter handling of all-negative arrays, this template is commonly accepted for “at most one deletion” where the resulting subarray must be non-empty.

---

## 8. Pattern: Maximum Subarray With At Most `k` Deletions or Changes

When the problem says:

```text
at most k removals
at most k changes
at most k bad elements
```

Think:

```text
Kadane + DP states
```

Generic form:

```text
dp[j] = best subarray sum ending here using j operations
```

For one deletion, we had two states.

For `k` deletions:

```java
int maxSubarrayWithKDeletions(int[] nums, int k) {
    int n = nums.length;
    int NEG_INF = Integer.MIN_VALUE / 4;

    int[] dp = new int[k + 1];
    Arrays.fill(dp, NEG_INF);

    dp[0] = nums[0];

    int best = nums[0];

    for (int i = 1; i < n; i++) {
        int x = nums[i];

        for (int j = k; j >= 0; j--) {
            int keep = dp[j] + x;
            int startNew = x;

            int deleteCurrent = NEG_INF;
            if (j > 0) {
                deleteCurrent = dp[j - 1];
            }

            dp[j] = Math.max(Math.max(keep, startNew), deleteCurrent);
            best = Math.max(best, dp[j]);
        }
    }

    return best;
}
```

Important interview pattern:

```text
Kadane tracks one state.
When operations are allowed, track multiple Kadane states.
```

---

## 9. Pattern: Maximum Alternating Subarray Sum

Variant:

```text
Choose a contiguous subarray.
Sum signs alternate: + - + - ...
```

Use two states:

```text
plus = best alternating sum ending here where current element is added
minus = best alternating sum ending here where current element is subtracted
```

```java
long maxAlternatingSubarraySum(int[] nums) {
    long plus = nums[0];
    long minus = Long.MIN_VALUE / 4;
    long best = nums[0];

    for (int i = 1; i < nums.length; i++) {
        int x = nums[i];

        long newPlus = Math.max((long)x, minus + x);
        long newMinus = plus - x;

        plus = newPlus;
        minus = newMinus;

        best = Math.max(best, plus);
    }

    return best;
}
```

Pattern:

```text
When the meaning of “extend” depends on previous state, keep separate states.
```

---

## 10. Pattern: Maximum Subarray Sum With Length Constraints

Kadane alone works when there is no length restriction.

If the problem says:

```text
length at least k
length exactly k
length at most k
```

Think prefix sums.

### Exactly `k`

Use sliding window.

```java
int maxSumExactlyK(int[] nums, int k) {
    int sum = 0;
    int best = Integer.MIN_VALUE;

    for (int i = 0; i < nums.length; i++) {
        sum += nums[i];

        if (i >= k) {
            sum -= nums[i - k];
        }

        if (i >= k - 1) {
            best = Math.max(best, sum);
        }
    }

    return best;
}
```

### At Least `k`

Combine prefix sums and minimum prefix.

For subarray `j..i`:

```text
sum = prefix[i + 1] - prefix[j]
```

Need:

```text
i - j + 1 >= k
j <= i - k + 1
```

Track the minimum prefix eligible so far.

```java
int maxSumAtLeastK(int[] nums, int k) {
    int n = nums.length;
    int[] prefix = new int[n + 1];

    for (int i = 0; i < n; i++) {
        prefix[i + 1] = prefix[i] + nums[i];
    }

    int best = Integer.MIN_VALUE;
    int minPrefix = 0;

    for (int end = k; end <= n; end++) {
        minPrefix = Math.min(minPrefix, prefix[end - k]);
        best = Math.max(best, prefix[end] - minPrefix);
    }

    return best;
}
```

Recognition rule:

```text
Kadane = flexible length.
Prefix sums = length constraints or range-sum constraints.
```

---

## 11. Pattern: Maximum Subarray Sum No Larger Than `K`

Problem:

```text
Find max subarray sum <= K
```

Kadane does **not** directly work.

Use prefix sums + balanced tree.

For each prefix sum `curr`, need previous prefix `prev` such that:

```text
curr - prev <= K
prev >= curr - K
```

So find smallest prefix >= `curr - K`.

```java
int maxSubarrayNoMoreThanK(int[] nums, int k) {
    TreeSet<Integer> set = new TreeSet<>();
    set.add(0);

    int prefix = 0;
    int best = Integer.MIN_VALUE;

    for (int x : nums) {
        prefix += x;

        Integer prev = set.ceiling(prefix - k);
        if (prev != null) {
            best = Math.max(best, prefix - prev);
        }

        set.add(prefix);
    }

    return best;
}
```

This appears in harder problems like:

```text
Max Sum of Rectangle No Larger Than K
```

---

## 12. Pattern: 2D Kadane — Maximum Sum Rectangle

Given a matrix, find max-sum submatrix.

Compress rows or columns and apply 1D Kadane.

```java
int maxSumSubmatrix(int[][] matrix) {
    int rows = matrix.length;
    int cols = matrix[0].length;
    int best = Integer.MIN_VALUE;

    for (int top = 0; top < rows; top++) {
        int[] colSum = new int[cols];

        for (int bottom = top; bottom < rows; bottom++) {
            for (int c = 0; c < cols; c++) {
                colSum[c] += matrix[bottom][c];
            }

            best = Math.max(best, kadane(colSum));
        }
    }

    return best;
}

int kadane(int[] nums) {
    int curr = nums[0];
    int best = nums[0];

    for (int i = 1; i < nums.length; i++) {
        curr = Math.max(nums[i], curr + nums[i]);
        best = Math.max(best, curr);
    }

    return best;
}
```

Complexity:

```text
O(rows^2 * cols)
```

If columns are fewer than rows, compress columns instead:

```text
O(min(R, C)^2 * max(R, C))
```

---

## 13. Pattern: 2D Max Rectangle No Larger Than `K`

This is a very FAANG-style hard variant.

Use:

```text
row/column compression + prefix sum + TreeSet
```

For each compressed 1D array, solve max subarray sum no more than `k`.

```java
int maxSumSubmatrixNoMoreThanK(int[][] matrix, int k) {
    int rows = matrix.length;
    int cols = matrix[0].length;
    int best = Integer.MIN_VALUE;

    for (int left = 0; left < cols; left++) {
        int[] rowSum = new int[rows];

        for (int right = left; right < cols; right++) {
            for (int r = 0; r < rows; r++) {
                rowSum[r] += matrix[r][right];
            }

            best = Math.max(best, maxSubarrayNoMoreThanK(rowSum, k));

            if (best == k) {
                return k;
            }
        }
    }

    return best;
}

int maxSubarrayNoMoreThanK(int[] nums, int k) {
    TreeSet<Integer> set = new TreeSet<>();
    set.add(0);

    int prefix = 0;
    int best = Integer.MIN_VALUE;

    for (int x : nums) {
        prefix += x;

        Integer prev = set.ceiling(prefix - k);
        if (prev != null) {
            best = Math.max(best, prefix - prev);
        }

        set.add(prefix);
    }

    return best;
}
```

Recognition:

```text
2D + max sum rectangle = compression + Kadane
2D + max sum rectangle <= K = compression + TreeSet
```

---

## 14. Pattern: Stock Problems as Kadane

Best Time to Buy and Sell Stock I:

```text
max profit = max difference prices[j] - prices[i], j > i
```

This can be seen as Kadane over daily differences.

```text
prices = [7,1,5,3,6,4]
diffs  = [-6,4,-2,3,-2]
max subarray diff = 5
```

Simpler version:

```java
int maxProfit(int[] prices) {
    int minPrice = prices[0];
    int best = 0;

    for (int price : prices) {
        minPrice = Math.min(minPrice, price);
        best = Math.max(best, price - minPrice);
    }

    return best;
}
```

Conceptually:

```text
Stock I is Kadane on adjacent differences.
```

For multiple transactions, cooldowns, or fees, it becomes state DP, not pure Kadane.

---

## 15. Pattern: Maximum Consecutive Ones After Flipping

This is Kadane-like but usually solved with sliding window.

Example:

```text
Max consecutive 1s after flipping at most k zeros.
```

Recognition:

```text
Binary array + at most k bad elements = sliding window
```

```java
int longestOnes(int[] nums, int k) {
    int left = 0;
    int zeros = 0;
    int best = 0;

    for (int right = 0; right < nums.length; right++) {
        if (nums[right] == 0) {
            zeros++;
        }

        while (zeros > k) {
            if (nums[left] == 0) {
                zeros--;
            }
            left++;
        }

        best = Math.max(best, right - left + 1);
    }

    return best;
}
```

How it relates:

```text
Kadane maximizes sum.
Sliding window maximizes length under a constraint.
```

---

## 16. Prefix Sum vs Kadane vs Sliding Window

### Use Kadane When

```text
Need max/min contiguous subarray sum
No fixed length constraint
No upper bound like <= K
One-pass local decision works
```

### Use Prefix Sums When

```text
Need exact range sums
Need length constraints
Need sum equals K
Need sum <= K
Need count of subarrays
Need 2D submatrix sums
```

### Use Sliding Window When

```text
All numbers are non-negative
Or constraint is monotonic, such as at most K zeros
```

### Use DP States When

```text
Allowed deletion/change/flip/operation
State of operation matters
```

---

## 17. Kadane Decision Framework for Interviews

### Question 1: Is it contiguous?

If no, Kadane probably does not apply.

```text
Subarray = contiguous -> Kadane possible
Subsequence = not necessarily contiguous -> DP/greedy often
```

### Question 2: Is it maximizing/minimizing sum/product?

```text
max sum -> Kadane
min sum -> reverse Kadane
max product -> Kadane with max/min states
```

### Question 3: Are there circular or wraparound constraints?

```text
max circular = max(normalKadane, total - minKadane)
```

### Question 4: Are operations allowed?

```text
one deletion -> two states
k deletions -> dp[k + 1]
one modification -> two states
k modifications -> dp[k + 1]
```

### Question 5: Are there length constraints?

```text
exactly k -> sliding window
at least k -> prefix min or Kadane + window
at most k -> prefix/deque depending on numbers
```

### Question 6: Is there a bound like sum <= K?

```text
Use prefix sums + TreeSet
```

### Question 7: Is it 2D?

```text
Compress rows/columns -> solve 1D version
```

---

## 18. Common FAANG-Style Kadane Problems

| Problem | Pattern |
|---|---|
| Maximum Subarray | Basic Kadane |
| Maximum Product Subarray | Max/min product states |
| Maximum Sum Circular Subarray | Total - min subarray |
| Maximum Subarray Sum with One Deletion | Two Kadane states |
| Best Time to Buy and Sell Stock | Kadane on differences |
| Max Sum Rectangle in Matrix | 2D compression + Kadane |
| Max Sum Rectangle No Larger Than K | 2D compression + TreeSet |
| Maximum Alternating Subarray Sum | Two states |
| Maximum Subarray with Length at Least K | Prefix sums |
| Longest Turbulent Subarray | Kadane-like local states |
| Max Consecutive Ones III | Sliding window cousin |
| Maximum Score of Good Subarray | Usually two pointers or monotonic stack |

---

## 19. Common Mistakes

### Mistake 1: Initializing `best = 0`

Wrong when all numbers are negative.

Bad:

```java
int curr = 0;
int best = 0;
```

For non-empty subarray, use:

```java
int curr = nums[0];
int best = nums[0];
```

### Mistake 2: Confusing Subarray and Subsequence

```text
Subarray = contiguous
Subsequence = can skip elements
```

Kadane is for subarrays.

### Mistake 3: Circular Subarray All-Negative Bug

```text
total - minSubarray gives 0
```

But empty subarray is invalid.

Always handle:

```java
if (maxBest < 0) return maxBest;
```

### Mistake 4: Trying Kadane for `sum <= K`

Kadane finds unrestricted max sum. With an upper bound, local greedy breaks.

Use:

```text
prefix sums + TreeSet
```

### Mistake 5: Forgetting Product Needs Minimum State

For product:

```text
negative * negative = positive
```

So track both max and min.

---

## 20. Mental Templates to Memorize

### Basic Max Kadane

```java
curr = Math.max(x, curr + x);
best = Math.max(best, curr);
```

### Basic Min Kadane

```java
curr = Math.min(x, curr + x);
best = Math.min(best, curr);
```

### Circular

```java
answer = Math.max(maxSubarray, total - minSubarray);
```

Except all-negative arrays.

### Product

```java
if (x < 0) swap(maxHere, minHere);
maxHere = Math.max(x, maxHere * x);
minHere = Math.min(x, minHere * x);
```

### One Deletion

```java
oneDel = Math.max(oneDel + x, noDel);
noDel = Math.max(noDel + x, x);
```

### Prefix + TreeSet for `<= K`

```java
prev = set.ceiling(prefix - k);
best = Math.max(best, prefix - prev);
```

### 2D

```text
Fix two boundaries.
Compress into 1D.
Apply Kadane or TreeSet variant.
```

---

## 21. Best Way to Explain Kadane in an Interview

Use this explanation:

> At each index, I maintain the best subarray sum that must end at this index. For the current element, I either extend the previous subarray or start a new subarray here. I keep a global maximum over all ending positions. This works because any negative or harmful prefix should be discarded once starting fresh is better.

Then write:

```java
curr = Math.max(nums[i], curr + nums[i]);
best = Math.max(best, curr);
```

That explanation is crisp and interviewer-friendly.

---

## 22. Interview Recall Map

Remember this:

```text
Kadane = best ending here
```

Then branch:

| Situation | Technique |
|---|---|
| Max sum | Normal Kadane |
| Min sum | Reverse Kadane |
| Circular | Total - min subarray |
| Product | maxHere + minHere |
| One deletion | noDel + oneDel |
| k operations | dp[j] |
| Length constraint | Prefix sums / sliding window |
| Sum <= K | Prefix sums + TreeSet |
| 2D matrix | Compress + 1D solution |
| Stock buy/sell once | Kadane on differences |
| Binary flip constraints | Sliding window |

---

## Final Memory Hook

```text
Kadane is not just an algorithm.
It is a pattern:

"Best answer ending here" + "global best so far".
```

Once a problem modifies the rules, modify the state:

```text
Deletion? Add deletion state.
Product? Add min state.
Circular? Add min subarray.
2D? Compress first.
Bounded by K? Use prefix + TreeSet.
Length constrained? Use prefix/window.
```
