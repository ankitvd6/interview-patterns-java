---
title: Prefix Sum Interview Notes
description: Prefix sum patterns for range queries, hashmap counts, subarray sums, 2D prefix sums, and difference arrays.
section: Prefix Sum
summary: Use prefix state and hashmaps to turn repeated range computations into fast lookups.
category: DSA Pattern
tags: [prefix-sum, arrays, hashmap]
order: 80
---
# Prefix Sum Interview Notes — FAANG Level

Prefix Sum is not just “sum from 0 to i.” In interviews, it is a **state-compression technique**: you convert repeated range computations into constant-time lookups, often using a hashmap.

---

## Core Idea

```text
prefix[i] = sum of elements from index 0 to i
sum(l..r) = prefix[r] - prefix[l - 1]
```

A cleaner 1-indexed prefix version:

```text
prefix[0] = 0
prefix[i + 1] = prefix[i] + nums[i]

sum(l..r) = prefix[r + 1] - prefix[l]
```

Prefer the second version during interviews because it avoids most edge cases.

---

## 1. Basic Range Sum Pattern

Use when the question asks many range-sum queries.

```python
prefix = [0] * (len(nums) + 1)

for i, x in enumerate(nums):
    prefix[i + 1] = prefix[i] + x

def range_sum(l, r):
    return prefix[r + 1] - prefix[l]
```

Typical problems:

- Range Sum Query - Immutable
- Find sum of subarray `[l, r]`
- Count ranges satisfying some property

Important interview habit: prefer `prefix` size `n + 1`.

---

## 2. Subarray Sum Equals K

This is the most important **Prefix Sum + HashMap** pattern.

### Key Identity

For subarray `j + 1 ... i`:

```text
prefix[i] - prefix[j] = k
```

So:

```text
prefix[j] = prefix[i] - k
```

While scanning, count how many previous prefix sums equal `current_prefix - k`.

```python
from collections import defaultdict

def subarraySum(nums, k):
    count = defaultdict(int)
    count[0] = 1

    prefix = 0
    ans = 0

    for x in nums:
        prefix += x
        ans += count[prefix - k]
        count[prefix] += 1

    return ans
```

### Why `count[0] = 1`?

It handles subarrays starting at index `0`.

Example:

```text
nums = [3], k = 3
prefix = 3
prefix - k = 0
```

We need to count that as one valid subarray.

### Common Trap

Do **not** use sliding window if numbers can be negative.

Sliding window works well for positive numbers, but prefix sum + hashmap works for positive, zero, and negative numbers.

---

## 3. Longest Subarray With Sum K

Same identity:

```text
prefix[i] - prefix[j] = k
prefix[j] = prefix[i] - k
```

But instead of counting occurrences, store the **first index** where each prefix sum appeared.

```python
def longest_subarray_sum_k(nums, k):
    first_seen = {0: -1}
    prefix = 0
    best = 0

    for i, x in enumerate(nums):
        prefix += x

        if prefix - k in first_seen:
            best = max(best, i - first_seen[prefix - k])

        if prefix not in first_seen:
            first_seen[prefix] = i

    return best
```

### Why Store First Occurrence?

For longest length, you want the earliest possible starting index.

---

## 4. Shortest Subarray With Sum At Least K

This is an advanced FAANG pattern.

When the array can contain negative numbers, normal sliding window fails.

Use:

```text
prefix sums + monotonic deque
```

Problem type:

```text
Find shortest subarray with sum >= K
```

### Core Idea

Let:

```text
sum(i..j-1) = prefix[j] - prefix[i]
```

We want:

```text
prefix[j] - prefix[i] >= K
```

Maintain a deque of candidate prefix indices with increasing prefix values.

```python
from collections import deque

def shortestSubarray(nums, k):
    n = len(nums)
    prefix = [0] * (n + 1)

    for i in range(n):
        prefix[i + 1] = prefix[i] + nums[i]

    dq = deque()
    ans = float("inf")

    for j in range(n + 1):
        while dq and prefix[j] - prefix[dq[0]] >= k:
            ans = min(ans, j - dq.popleft())

        while dq and prefix[j] <= prefix[dq[-1]]:
            dq.pop()

        dq.append(j)

    return ans if ans != float("inf") else -1
```

### Why Pop From Back?

If:

```text
prefix[j] <= prefix[dq[-1]]
```

then `j` is always better than `dq[-1]` because:

- it has a smaller or equal prefix sum
- it appears later, making future subarrays shorter

So the older index is useless.

---

## 5. Subarray Sum Divisible by K

This uses prefix sum modulo.

If:

```text
prefix[i] % k == prefix[j] % k
```

then:

```text
prefix[i] - prefix[j]
```

is divisible by `k`.

### Count Subarrays Divisible by K

```python
from collections import defaultdict

def subarraysDivByK(nums, k):
    count = defaultdict(int)
    count[0] = 1

    prefix = 0
    ans = 0

    for x in nums:
        prefix += x
        rem = prefix % k

        ans += count[rem]
        count[rem] += 1

    return ans
```

### Important Python / Java Note

Python’s `%` already gives non-negative remainders for positive `k`.

In Java/C++ you often need:

```java
rem = ((prefix % k) + k) % k;
```

Typical problems:

- Subarray Sums Divisible by K
- Continuous Subarray Sum
- Make Sum Divisible by P

---

## 6. Continuous Subarray Sum

Problem:

```text
Does there exist a subarray of length at least 2 whose sum is divisible by k?
```

Use earliest index of each remainder.

```python
def checkSubarraySum(nums, k):
    seen = {0: -1}
    prefix = 0

    for i, x in enumerate(nums):
        prefix += x
        rem = prefix % k

        if rem in seen:
            if i - seen[rem] >= 2:
                return True
        else:
            seen[rem] = i

    return False
```

Key difference from counting:

- For existence/longest, store earliest index.
- For counting, store frequency.

---

## 7. Binary Array Prefix Sum Tricks

Binary arrays often become prefix-sum problems.

### Count Subarrays With Equal 0s and 1s

Convert:

```text
0 -> -1
1 -> +1
```

Then the problem becomes:

```text
Find subarrays with sum 0
```

### Longest Equal 0s and 1s

```python
def findMaxLength(nums):
    first_seen = {0: -1}
    prefix = 0
    best = 0

    for i, x in enumerate(nums):
        prefix += 1 if x == 1 else -1

        if prefix in first_seen:
            best = max(best, i - first_seen[prefix])
        else:
            first_seen[prefix] = i

    return best
```

Pattern:

```text
balanced count problem -> convert one category to -1
```

Works for:

- Equal 0s and 1s
- Equal vowels/consonants
- Equal count of two symbols
- Balanced parentheses variants

---

## 8. Prefix Sum With Frequency of Characters

Use when the problem asks many substring character-count queries.

Example:

```text
Can substring s[l..r] be rearranged into a palindrome?
```

A string can form a palindrome if at most one character has odd frequency.

Use prefix counts or bitmask parity.

### Character Prefix Frequency

```python
def build_prefix_counts(s):
    n = len(s)
    prefix = [[0] * 26 for _ in range(n + 1)]

    for i, ch in enumerate(s):
        prefix[i + 1] = prefix[i][:]
        prefix[i + 1][ord(ch) - ord('a')] += 1

    return prefix

def get_count(prefix, l, r, c):
    idx = ord(c) - ord('a')
    return prefix[r + 1][idx] - prefix[l][idx]
```

---

## 9. Prefix XOR

XOR has a similar property to sum:

```text
xor(l..r) = prefix_xor[r + 1] ^ prefix_xor[l]
```

Because:

```text
a ^ a = 0
```

Typical problems:

- Count subarrays with XOR equal to K
- Maximum XOR subarray
- XOR queries of a subarray

### Count Subarrays With XOR K

```python
from collections import defaultdict

def count_xor_subarrays(nums, k):
    freq = defaultdict(int)
    freq[0] = 1

    px = 0
    ans = 0

    for x in nums:
        px ^= x
        ans += freq[px ^ k]
        freq[px] += 1

    return ans
```

Compare with sum:

```text
sum: prefix - old_prefix = k
xor: prefix ^ old_prefix = k
old_prefix = prefix ^ k
```

---

## 10. 2D Prefix Sum

Use for matrix sum queries.

### Formula

Build:

```text
prefix[r + 1][c + 1]
```

where it stores sum of rectangle from `(0,0)` to `(r,c)`.

```python
def build_2d_prefix(matrix):
    m, n = len(matrix), len(matrix[0])
    prefix = [[0] * (n + 1) for _ in range(m + 1)]

    for r in range(m):
        for c in range(n):
            prefix[r + 1][c + 1] = (
                matrix[r][c]
                + prefix[r][c + 1]
                + prefix[r + 1][c]
                - prefix[r][c]
            )

    return prefix
```

### Query Sum of Rectangle

Rectangle from `(r1, c1)` to `(r2, c2)` inclusive:

```python
def region_sum(prefix, r1, c1, r2, c2):
    return (
        prefix[r2 + 1][c2 + 1]
        - prefix[r1][c2 + 1]
        - prefix[r2 + 1][c1]
        + prefix[r1][c1]
    )
```

Remember inclusion-exclusion:

```text
total
- top
- left
+ overlap
```

Typical problems:

- Range Sum Query 2D - Immutable
- Maximal square variants
- Count submatrices with target sum
- Image/heatmap region queries

---

## 11. Count Submatrices With Target Sum

FAANG-level important.

Reduce 2D problem to many 1D prefix-sum problems.

For every pair of rows, compress the columns into a 1D array of column sums, then count subarrays with sum target.

```python
from collections import defaultdict

def numSubmatrixSumTarget(matrix, target):
    m, n = len(matrix), len(matrix[0])
    ans = 0

    for top in range(m):
        col_sums = [0] * n

        for bottom in range(top, m):
            for c in range(n):
                col_sums[c] += matrix[bottom][c]

            ans += count_subarrays_sum_k(col_sums, target)

    return ans

def count_subarrays_sum_k(nums, k):
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

Complexity:

```text
O(rows^2 * cols)
```

If columns are fewer than rows, you can instead fix pairs of columns.

---

## 12. Difference Array: Cousin of Prefix Sum

Difference array is the reverse of prefix sum.

Use when many range updates are needed.

Example:

```text
Add val to every element from l to r
```

Instead of updating all elements:

```text
diff[l] += val
diff[r + 1] -= val
```

Then prefix the diff array to recover final values.

```python
def range_addition(n, updates):
    diff = [0] * (n + 1)

    for l, r, val in updates:
        diff[l] += val
        if r + 1 < n:
            diff[r + 1] -= val

    arr = [0] * n
    running = 0

    for i in range(n):
        running += diff[i]
        arr[i] = running

    return arr
```

Typical problems:

- Range Addition
- Corporate Flight Bookings
- Car Pooling
- Meeting room occupancy
- Maximum population year

---

## 13. Difference Array for Interval Overlap

For interval problems:

```text
start -> +1
end -> -1
```

Then prefix tells active count.

Important distinction:

For half-open interval `[start, end)`:

```text
diff[start] += 1
diff[end] -= 1
```

For closed interval `[start, end]`:

```text
diff[start] += 1
diff[end + 1] -= 1
```

Common problems:

- Meeting Rooms II
- Car Pooling
- My Calendar
- Maximum population
- Minimum platforms

---

## 14. Prefix Sum + Binary Search

Use when prefix sums are monotonic.

This usually requires non-negative numbers.

Examples:

```text
Find minimum length subarray with sum >= target
Find random weighted index
Find kth element by cumulative frequency
```

### Weighted Random Pick

```python
import random
import bisect

class Solution:
    def __init__(self, w):
        self.prefix = []
        running = 0

        for weight in w:
            running += weight
            self.prefix.append(running)

        self.total = running

    def pickIndex(self):
        x = random.randint(1, self.total)
        return bisect.bisect_left(self.prefix, x)
```

Key insight:

```text
weights -> cumulative ranges
binary search finds the bucket
```

---

## 15. Prefix Min / Prefix Max

Sometimes the prefix value is not a sum.

You may need:

```text
prefix_min[i] = min(nums[0..i])
prefix_max[i] = max(nums[0..i])
suffix_min[i] = min(nums[i..n-1])
suffix_max[i] = max(nums[i..n-1])
```

Typical problems:

- Partition array
- Maximum profit
- Trapping rain water
- Shortest unsorted subarray
- Minimum removals
- Split array into valid parts

Example: max profit from stock:

```python
def maxProfit(prices):
    min_so_far = float("inf")
    best = 0

    for price in prices:
        best = max(best, price - min_so_far)
        min_so_far = min(min_so_far, price)

    return best
```

This is essentially prefix minimum.

---

## 16. Prefix Sum With Constraints on Length

Sometimes the valid subarray must have length:

```text
at least k
exactly k
at most k
```

### Exactly K

Use normal sliding sum or prefix difference:

```python
def max_sum_len_k(nums, k):
    prefix = [0]

    for x in nums:
        prefix.append(prefix[-1] + x)

    best = float("-inf")

    for i in range(k, len(prefix)):
        best = max(best, prefix[i] - prefix[i - k])

    return best
```

### At Least K

Often combine prefix sum with prefix minimum.

Example: maximum subarray sum with length at least `k`.

```python
def max_subarray_sum_at_least_k(nums, k):
    n = len(nums)
    prefix = [0] * (n + 1)

    for i in range(n):
        prefix[i + 1] = prefix[i] + nums[i]

    best = float("-inf")
    min_prefix = 0

    for r in range(k, n + 1):
        min_prefix = min(min_prefix, prefix[r - k])
        best = max(best, prefix[r] - min_prefix)

    return best
```

Idea:

```text
For each right endpoint r, choose smallest prefix before r-k.
```

---

## 17. Prefix Sum on Trees

Less common, but important.

For path-sum problems in binary trees, use prefix sum hashmap during DFS.

Problem:

```text
Count paths with sum target.
```

At each node:

```text
current_prefix += node.val
paths ending here = count[current_prefix - target]
```

Then recurse.

Backtrack after leaving node.

```python
from collections import defaultdict

def pathSum(root, targetSum):
    count = defaultdict(int)
    count[0] = 1

    def dfs(node, prefix):
        if not node:
            return 0

        prefix += node.val
        ans = count[prefix - targetSum]

        count[prefix] += 1
        ans += dfs(node.left, prefix)
        ans += dfs(node.right, prefix)
        count[prefix] -= 1

        return ans

    return dfs(root, 0)
```

Important: remove prefix sum while backtracking, otherwise paths from unrelated branches get mixed.

---

## 18. Prefix Sum on Graphs / DFS State

General idea:

```text
Maintain path state from root to current node.
Use hashmap/counts to answer questions about current path.
Backtrack when returning.
```

Similar to tree path sum.

Can be used for:

- Path sum
- Parity masks
- Ancestor-descendant constraints
- Pseudo-palindromic paths

---

## 19. Prefix Parity / Bitmask Pattern

Very powerful for strings.

Instead of storing full counts, store odd/even parity using bits.

Example:

```text
mask bit i = parity of character i count so far
```

For lowercase letters:

```python
mask ^= 1 << (ord(ch) - ord('a'))
```

Two substrings have same mask if all character counts between them are even.

### Wonderful Substrings Style

A substring is valid if:

- all counts even, or
- exactly one character has odd count

```python
from collections import defaultdict

def wonderfulSubstrings(word):
    freq = defaultdict(int)
    freq[0] = 1

    mask = 0
    ans = 0

    for ch in word:
        bit = ord(ch) - ord('a')
        mask ^= 1 << bit

        ans += freq[mask]

        for b in range(10):
            ans += freq[mask ^ (1 << b)]

        freq[mask] += 1

    return ans
```

Pattern:

```text
substring property based on parity -> prefix bitmask
```

---

## 20. Prefix Sum for Product Except Self

This is prefix/suffix multiplication.

```python
def productExceptSelf(nums):
    n = len(nums)
    ans = [1] * n

    prefix = 1
    for i in range(n):
        ans[i] = prefix
        prefix *= nums[i]

    suffix = 1
    for i in range(n - 1, -1, -1):
        ans[i] *= suffix
        suffix *= nums[i]

    return ans
```

Pattern:

```text
answer[i] = product before i * product after i
```

This is prefix/suffix aggregation, not prefix sum specifically.

---

## How to Recognize Prefix Sum in Interviews

Look for these phrases:

```text
subarray
substring
range sum
contiguous
sum equals k
sum divisible by k
number of subarrays
longest/shortest subarray
matrix region
range update
cumulative
balance/equal number of X and Y
```

Strong signal:

```text
Need to repeatedly compute sum/count over contiguous ranges.
```

Even stronger signal:

```text
Array has negative numbers, and sliding window seems tempting but unsafe.
```

---

## Choosing the Right Pattern

| Problem Type | Technique |
|---|---|
| Range sum query | Prefix array |
| Count subarrays with sum `k` | Prefix sum + frequency map |
| Longest subarray with sum `k` | Prefix sum + first index map |
| Shortest subarray with sum `>= k` and negatives | Prefix sum + monotonic deque |
| Sum divisible by `k` | Prefix modulo + hashmap |
| Equal 0s and 1s | Convert 0 to -1, then prefix |
| XOR subarray | Prefix XOR + hashmap |
| Matrix range sum | 2D prefix sum |
| Count target-sum submatrices | Compress rows/cols + 1D prefix |
| Range updates | Difference array |
| Weighted random pick | Prefix sum + binary search |
| Tree path sums | DFS prefix sum + backtracking |
| Palindrome/parity substrings | Prefix bitmask |

---

## Most Common Mistakes

### 1. Forgetting `count[0] = 1`

For count-subarray problems, initialize:

```python
freq[0] = 1
```

This handles subarrays starting from index `0`.

### 2. Updating Hashmap Too Early

Correct:

```python
ans += freq[prefix - k]
freq[prefix] += 1
```

Wrong:

```python
freq[prefix] += 1
ans += freq[prefix - k]
```

The wrong order may count empty subarrays.

### 3. Using Sliding Window With Negative Numbers

Sliding window requires monotonic behavior.

Works usually when:

```text
all numbers are positive / non-negative
```

Fails when negatives exist.

Example:

```text
[2, -1, 2], k = 3
```

Window expansion/shrinking is no longer predictable.

### 4. Confusing Count vs Longest

For counting:

```python
freq[prefix] += 1
```

For longest:

```python
if prefix not in first_seen:
    first_seen[prefix] = i
```

Counting needs all occurrences.

Longest needs earliest occurrence.

### 5. Modulo With Negative Numbers

In Java/C++:

```java
rem = ((prefix % k) + k) % k;
```

In Python, `prefix % k` is already normalized for positive `k`.

### 6. Off-by-One in Prefix Arrays

Use:

```python
prefix = [0] * (n + 1)
prefix[i + 1] = prefix[i] + nums[i]
sum(l, r) = prefix[r + 1] - prefix[l]
```

This avoids most bugs.

---

## Interview Mental Model

When you see a subarray sum problem, immediately ask:

```text
Do I need sum(l..r)?
Can I rewrite it as prefix[r] - prefix[l]?
Am I counting, maximizing length, minimizing length, or checking existence?
Are numbers negative?
Is modulo involved?
Is this really a range update problem?
```

Then choose:

```text
count      -> hashmap frequency
longest    -> hashmap earliest index
shortest   -> monotonic deque
divisible  -> prefix % k
2D         -> row/column compression or 2D prefix
updates    -> difference array
```

---

## Practice Order

Use this order to master the topic:

- [ ] Range Sum Query - Immutable
- [ ] Subarray Sum Equals K
- [ ] Continuous Subarray Sum
- [ ] Subarray Sums Divisible by K
- [ ] Contiguous Array
- [ ] Maximum Size Subarray Sum Equals K
- [ ] Shortest Subarray with Sum at Least K
- [ ] Range Addition
- [ ] Corporate Flight Bookings
- [ ] Range Sum Query 2D - Immutable
- [ ] Number of Submatrices That Sum to Target
- [ ] Path Sum III
- [ ] Count Number of Nice Subarrays
- [ ] Find Pivot Index
- [ ] Product of Array Except Self
- [ ] Random Pick with Weight
- [ ] Wonderful Substrings

---

## One-Line Summary

Prefix Sum is about turning:

```text
sum of range l..r
```

into:

```text
state_at_r - state_before_l
```

And most hard interview problems come from deciding what to store:

```text
frequency, earliest index, monotonic deque, modulo, bitmask, or 2D state.
```
