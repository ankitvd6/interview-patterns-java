---
title: Two Pointers Interview Notes
description: Two-pointer techniques for sorted arrays, pair search, sliding windows, palindromes, and in-place array operations.
section: Two Pointers
summary: Pattern guide for using two indexes to reduce brute force and preserve correctness.
category: DSA Pattern
tags: [two-pointers, arrays, strings]
order: 100
---
# Two Pointers Interview Notes: FAANG-Level Recall Sheet

Two Pointers is not one single pattern. It is a family of techniques where you use two indices or references to reduce brute-force search, often from `O(n^2)` to `O(n)` or `O(n log n)`.

> Maintain two positions that represent a useful relationship, then move one pointer at a time based on a rule that preserves correctness.

---

## 1. When to Think “Two Pointers”

Think Two Pointers when the problem involves:

### Sorted array or string

- Pair sum
- Triplets
- Closest sum
- Remove duplicates
- Merge arrays

### Find pairs, subarrays, or windows

- Sum equals target
- Count pairs
- Minimum or maximum length subarray
- Sliding window

### Palindrome or string comparison

- Valid palindrome
- Almost palindrome
- Reverse vowels
- Compare with backspaces

### In-place modification

- Remove element
- Move zeroes
- Sort colors
- Partition array

### Linked list fast/slow pointer

- Cycle detection
- Middle node
- Palindrome linked list
- Remove nth node from end

### Merging

- Merge sorted arrays
- Interval merging
- Merge two sorted streams

---

## 2. Major Two Pointer Patterns

---

## Pattern 1: Opposite-Direction Pointers

Use when the array/string is **sorted** or when comparing from both ends.

```java
int left = 0;
int right = arr.length - 1;

while (left < right) {
    int sum = arr[left] + arr[right];

    if (sum == target) {
        return true;
    } else if (sum < target) {
        left++;
    } else {
        right--;
    }
}
```

### Key Idea

If the array is sorted:

- Sum too small → move `left` rightward to increase sum.
- Sum too large → move `right` leftward to decrease sum.

### Common Problems

- Two Sum II
- 3Sum
- 4Sum
- Container With Most Water
- Valid Palindrome
- Trapping Rain Water
- Boats to Save People
- Squares of a Sorted Array

---

## Pattern 2: Same-Direction Pointers / Sliding Window

Use when finding a **contiguous subarray or substring**.

```java
int left = 0;

for (int right = 0; right < n; right++) {
    // include nums[right]

    while (windowIsInvalid()) {
        // remove nums[left]
        left++;
    }

    // update answer
}
```

### Key Idea

- `right` expands the window.
- `left` shrinks the window when the condition breaks.

### Common Problems

- Longest Substring Without Repeating Characters
- Minimum Size Subarray Sum
- Minimum Window Substring
- Max Consecutive Ones III
- Fruit Into Baskets
- Longest Repeating Character Replacement
- Subarrays with Product Less Than K

---

## Pattern 3: Slow-Fast Write Pointer

Use when one pointer scans and another builds or marks useful state.

```java
int slow = 0;

for (int fast = 0; fast < nums.length; fast++) {
    if (shouldKeep(nums[fast])) {
        nums[slow] = nums[fast];
        slow++;
    }
}

return slow;
```

### Common Problems

- Remove Duplicates from Sorted Array
- Remove Element
- Move Zeroes
- Partition Array
- Compress String
- In-place filtering

### Mental Model

- `fast` explores.
- `slow` marks where the next valid element should go.

---

## Pattern 4: Fast and Slow Linked List Pointers

Use when pointer speed gives structural information.

```java
ListNode slow = head;
ListNode fast = head;

while (fast != null && fast.next != null) {
    slow = slow.next;
    fast = fast.next.next;
}
```

### Common Problems

- Linked List Cycle
- Linked List Cycle II
- Middle of Linked List
- Palindrome Linked List
- Reorder List

### Mental Model

- `fast` moves 2 steps.
- `slow` moves 1 step.
- If there is a cycle, they eventually meet.
- If no cycle, `fast` reaches null.
- When `fast` reaches the end, `slow` is around the middle.

---

## Pattern 5: Partition Pointers

Use when the array must be rearranged around categories.

Classic example: Dutch National Flag.

```java
int low = 0;
int mid = 0;
int high = nums.length - 1;

while (mid <= high) {
    if (nums[mid] == 0) {
        swap(nums, low, mid);
        low++;
        mid++;
    } else if (nums[mid] == 1) {
        mid++;
    } else {
        swap(nums, mid, high);
        high--;
    }
}
```

### Important Detail

When swapping with `high`, do **not** increment `mid` immediately, because the value swapped from the right side has not been processed yet.

### Common Problems

- Sort Colors
- Partition Array by Pivot
- Move zeroes
- Segregate positives/negatives
- Quickselect partitioning

---

## Pattern 6: Merge Pointers

Use when combining two sorted arrays or lists.

```java
int i = 0;
int j = 0;

while (i < a.length && j < b.length) {
    if (a[i] <= b[j]) {
        // take a[i]
        i++;
    } else {
        // take b[j]
        j++;
    }
}

while (i < a.length) {
    // take remaining a[i]
    i++;
}

while (j < b.length) {
    // take remaining b[j]
    j++;
}
```

### Common Problems

- Merge Sorted Array
- Merge Two Sorted Lists
- Interval List Intersections
- Meeting Scheduler
- Merge Intervals variant
- Find common elements

---

## 3. Interview Decision Framework

### Question 1: Is the input sorted?

If yes, strongly consider:

```text
left = 0
right = n - 1
```

Move inward based on comparison.

Examples:

```text
Find pair with target sum.
Find triplets summing to zero.
Find closest pair.
Count pairs less than target.
```

---

### Question 2: Is the problem about contiguous subarray or substring?

If yes, think sliding window.

Examples:

```text
Longest substring...
Minimum subarray...
At most K...
Exactly K...
No duplicates...
Frequency constraints...
```

Template:

```java
for right in range:
    add right
    while invalid:
        remove left
        left++
    update answer
```

---

### Question 3: Is the problem asking to modify array in-place?

If yes, think slow-fast write pointer.

Examples:

```text
Remove duplicates
Remove elements
Move zeroes
Compress string
Keep only valid elements
```

Template:

```java
slow = 0
for fast:
    if valid:
        nums[slow++] = nums[fast]
```

---

### Question 4: Is the input a linked list?

If yes, think fast-slow pointers.

Examples:

```text
Cycle?
Middle?
Nth from end?
Palindrome?
```

Template:

```java
slow = head
fast = head
while fast != null && fast.next != null:
    slow = slow.next
    fast = fast.next.next
```

---

### Question 5: Are there multiple sorted inputs?

If yes, think merge pointers.

Examples:

```text
Merge arrays
Intersection
Meeting slots
Interval intersections
Common elements
```

Use one pointer per input.

---

## 4. Core FAANG-Level Two Pointer Problems

---

## A. Pair Sum in Sorted Array

```java
boolean twoSumSorted(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;

    while (left < right) {
        int sum = nums[left] + nums[right];

        if (sum == target) return true;
        if (sum < target) left++;
        else right--;
    }

    return false;
}
```

### Why It Works

Sorted order lets you eliminate one pointer move at a time.

---

## B. 3Sum

```java
List<List<Integer>> threeSum(int[] nums) {
    Arrays.sort(nums);
    List<List<Integer>> result = new ArrayList<>();

    for (int i = 0; i < nums.length - 2; i++) {
        if (i > 0 && nums[i] == nums[i - 1]) continue;

        int left = i + 1;
        int right = nums.length - 1;

        while (left < right) {
            int sum = nums[i] + nums[left] + nums[right];

            if (sum == 0) {
                result.add(Arrays.asList(nums[i], nums[left], nums[right]));

                left++;
                right--;

                while (left < right && nums[left] == nums[left - 1]) left++;
                while (left < right && nums[right] == nums[right + 1]) right--;

            } else if (sum < 0) {
                left++;
            } else {
                right--;
            }
        }
    }

    return result;
}
```

### Interviewer Focus Areas

- Sorting first.
- Skipping duplicate `i`.
- Skipping duplicate `left` and `right` after finding a valid triplet.
- Complexity: `O(n^2)` time, `O(1)` extra space excluding output.

---

## C. Container With Most Water

```java
int maxArea(int[] height) {
    int left = 0;
    int right = height.length - 1;
    int best = 0;

    while (left < right) {
        int width = right - left;
        int area = width * Math.min(height[left], height[right]);
        best = Math.max(best, area);

        if (height[left] < height[right]) {
            left++;
        } else {
            right--;
        }
    }

    return best;
}
```

### Key Intuition

Area is limited by the shorter wall.

Moving the taller wall cannot help because width decreases and height is still limited by the shorter one.

So always move the shorter pointer.

---

## D. Trapping Rain Water

```java
int trap(int[] height) {
    int left = 0;
    int right = height.length - 1;

    int leftMax = 0;
    int rightMax = 0;
    int water = 0;

    while (left < right) {
        if (height[left] < height[right]) {
            if (height[left] >= leftMax) {
                leftMax = height[left];
            } else {
                water += leftMax - height[left];
            }
            left++;
        } else {
            if (height[right] >= rightMax) {
                rightMax = height[right];
            } else {
                water += rightMax - height[right];
            }
            right--;
        }
    }

    return water;
}
```

### Key Intuition

Water at an index depends on:

```text
min(maxLeft, maxRight) - height[i]
```

The side with the smaller current boundary is safe to process.

---

## E. Longest Substring Without Repeating Characters

### Set-Based Version

```java
int lengthOfLongestSubstring(String s) {
    Set<Character> set = new HashSet<>();
    int left = 0;
    int best = 0;

    for (int right = 0; right < s.length(); right++) {
        char c = s.charAt(right);

        while (set.contains(c)) {
            set.remove(s.charAt(left));
            left++;
        }

        set.add(c);
        best = Math.max(best, right - left + 1);
    }

    return best;
}
```

### Better Index Map Version

```java
int lengthOfLongestSubstring(String s) {
    Map<Character, Integer> lastSeen = new HashMap<>();
    int left = 0;
    int best = 0;

    for (int right = 0; right < s.length(); right++) {
        char c = s.charAt(right);

        if (lastSeen.containsKey(c)) {
            left = Math.max(left, lastSeen.get(c) + 1);
        }

        lastSeen.put(c, right);
        best = Math.max(best, right - left + 1);
    }

    return best;
}
```

### Important Detail

Use:

```java
left = Math.max(left, lastSeen.get(c) + 1);
```

Not:

```java
left = lastSeen.get(c) + 1;
```

Because `left` should never move backward.

---

## F. Minimum Size Subarray Sum

Works when all numbers are positive.

```java
int minSubArrayLen(int target, int[] nums) {
    int left = 0;
    int sum = 0;
    int best = Integer.MAX_VALUE;

    for (int right = 0; right < nums.length; right++) {
        sum += nums[right];

        while (sum >= target) {
            best = Math.min(best, right - left + 1);
            sum -= nums[left];
            left++;
        }
    }

    return best == Integer.MAX_VALUE ? 0 : best;
}
```

### Important Caveat

Sliding window works cleanly here because all numbers are positive.

If negative numbers are allowed, increasing `right` does not always increase sum, and shrinking `left` does not always decrease sum. Then you may need prefix sum plus hashmap/deque.

---

## G. Move Zeroes

```java
void moveZeroes(int[] nums) {
    int slow = 0;

    for (int fast = 0; fast < nums.length; fast++) {
        if (nums[fast] != 0) {
            nums[slow] = nums[fast];
            slow++;
        }
    }

    while (slow < nums.length) {
        nums[slow] = 0;
        slow++;
    }
}
```

### Mental Model

First pass compacts non-zero values.
Second pass fills remaining positions with zero.

---

## H. Remove Duplicates from Sorted Array

```java
int removeDuplicates(int[] nums) {
    if (nums.length == 0) return 0;

    int slow = 1;

    for (int fast = 1; fast < nums.length; fast++) {
        if (nums[fast] != nums[fast - 1]) {
            nums[slow] = nums[fast];
            slow++;
        }
    }

    return slow;
}
```

### Variant: Allow At Most Two Duplicates

```java
int removeDuplicates(int[] nums) {
    int slow = 0;

    for (int fast = 0; fast < nums.length; fast++) {
        if (slow < 2 || nums[fast] != nums[slow - 2]) {
            nums[slow] = nums[fast];
            slow++;
        }
    }

    return slow;
}
```

### General Rule: Allow At Most K Duplicates

```java
if (slow < k || nums[fast] != nums[slow - k]) {
    nums[slow++] = nums[fast];
}
```

---

## 5. Important Sub-Patterns

---

## Sub-Pattern 1: Count Pairs Less Than Target

Sorted array.

```java
int countPairsLessThanTarget(int[] nums, int target) {
    Arrays.sort(nums);

    int left = 0;
    int right = nums.length - 1;
    int count = 0;

    while (left < right) {
        if (nums[left] + nums[right] < target) {
            count += right - left;
            left++;
        } else {
            right--;
        }
    }

    return count;
}
```

### Why `count += right - left`?

If `nums[left] + nums[right] < target`, then all these are valid:

```text
nums[left] + nums[left + 1]
nums[left] + nums[left + 2]
...
nums[left] + nums[right]
```

So there are `right - left` valid pairs.

---

## Sub-Pattern 2: Closest Sum

```java
int threeSumClosest(int[] nums, int target) {
    Arrays.sort(nums);
    int best = nums[0] + nums[1] + nums[2];

    for (int i = 0; i < nums.length - 2; i++) {
        int left = i + 1;
        int right = nums.length - 1;

        while (left < right) {
            int sum = nums[i] + nums[left] + nums[right];

            if (Math.abs(sum - target) < Math.abs(best - target)) {
                best = sum;
            }

            if (sum < target) {
                left++;
            } else if (sum > target) {
                right--;
            } else {
                return sum;
            }
        }
    }

    return best;
}
```

---

## Sub-Pattern 3: Palindrome With Skipping

Valid Palindrome II: can delete at most one character.

```java
boolean validPalindrome(String s) {
    int left = 0;
    int right = s.length() - 1;

    while (left < right) {
        if (s.charAt(left) != s.charAt(right)) {
            return isPalindrome(s, left + 1, right) ||
                   isPalindrome(s, left, right - 1);
        }

        left++;
        right--;
    }

    return true;
}

boolean isPalindrome(String s, int left, int right) {
    while (left < right) {
        if (s.charAt(left) != s.charAt(right)) {
            return false;
        }
        left++;
        right--;
    }

    return true;
}
```

### Important Idea

When mismatch happens, only two choices matter:

```text
skip left character
skip right character
```

Do not try all deletions.

---

## Sub-Pattern 4: Backspace String Compare

```java
boolean backspaceCompare(String s, String t) {
    int i = s.length() - 1;
    int j = t.length() - 1;

    while (i >= 0 || j >= 0) {
        i = getNextValidIndex(s, i);
        j = getNextValidIndex(t, j);

        if (i < 0 && j < 0) return true;
        if (i < 0 || j < 0) return false;
        if (s.charAt(i) != t.charAt(j)) return false;

        i--;
        j--;
    }

    return true;
}

int getNextValidIndex(String s, int index) {
    int skip = 0;

    while (index >= 0) {
        if (s.charAt(index) == '#') {
            skip++;
            index--;
        } else if (skip > 0) {
            skip--;
            index--;
        } else {
            break;
        }
    }

    return index;
}
```

### Mental Model

Process from the back because backspaces affect previous characters.

---

## 6. Sliding Window: Fixed vs Variable

---

## Fixed-Size Window

Use when the problem says:

```text
size k
exactly k elements
average of k
max sum of k
```

Template:

```java
int sum = 0;
int best = 0;

for (int right = 0; right < nums.length; right++) {
    sum += nums[right];

    if (right >= k) {
        sum -= nums[right - k];
    }

    if (right >= k - 1) {
        best = Math.max(best, sum);
    }
}
```

---

## Variable-Size Window

Use when the problem says:

```text
longest
shortest
at most k
at least target
no repeats
frequency condition
```

Template:

```java
int left = 0;

for (int right = 0; right < n; right++) {
    add(nums[right]);

    while (invalid()) {
        remove(nums[left]);
        left++;
    }

    updateAnswer();
}
```

---

## 7. At Most K vs Exactly K

This is a major FAANG trick.

```text
exactly K = atMost(K) - atMost(K - 1)
```

Example: subarrays with exactly K distinct integers.

```java
int subarraysWithKDistinct(int[] nums, int k) {
    return atMost(nums, k) - atMost(nums, k - 1);
}

int atMost(int[] nums, int k) {
    Map<Integer, Integer> freq = new HashMap<>();
    int left = 0;
    int count = 0;

    for (int right = 0; right < nums.length; right++) {
        freq.put(nums[right], freq.getOrDefault(nums[right], 0) + 1);

        while (freq.size() > k) {
            int leftVal = nums[left];
            freq.put(leftVal, freq.get(leftVal) - 1);

            if (freq.get(leftVal) == 0) {
                freq.remove(leftVal);
            }

            left++;
        }

        count += right - left + 1;
    }

    return count;
}
```

### Why `count += right - left + 1`?

For every valid window ending at `right`, all subarrays starting from:

```text
left, left + 1, ..., right
```

are valid.

So number of valid subarrays ending at `right` is:

```text
right - left + 1
```

---

## 8. Common Mistakes

### Mistake 1: Moving Both Pointers Unnecessarily

Usually move one pointer based on condition.

Wrong:

```java
if (sum < target) {
    left++;
    right--;
}
```

Correct:

```java
if (sum < target) {
    left++;
}
```

---

### Mistake 2: Forgetting Duplicate Handling in 3Sum

For unique triplets, skip duplicates at:

1. Main index `i`
2. `left` after finding valid triplet
3. `right` after finding valid triplet

---

### Mistake 3: Using Sliding Window With Negative Numbers

Sliding window depends on monotonic behavior.

Works well for:

```text
positive numbers
frequency constraints
distinct count
character windows
```

May fail for:

```text
subarray sum with negative numbers
```

Use prefix sum instead.

---

### Mistake 4: Off-by-One Window Size

Window size is:

```java
right - left + 1
```

Not:

```java
right - left
```

---

### Mistake 5: Moving `left` Backward

In substring problems with last seen index:

```java
left = Math.max(left, lastSeen.get(c) + 1);
```

Always ensure `left` only moves forward.

---

### Mistake 6: Not Checking Nulls in Linked List Fast Pointer

Correct:

```java
while (fast != null && fast.next != null)
```

Wrong:

```java
while (fast.next != null && fast != null)
```

The second one can throw `NullPointerException`.

---

## 9. How to Explain Correctness in Interviews

---

## For Sorted Pair Problems

Say:

> Because the array is sorted, when the current sum is too small, moving the right pointer left would only decrease the sum, so the only useful move is increasing the left pointer. Similarly, when the sum is too large, moving the left pointer right would only increase it, so we decrease the right pointer.

---

## For Sliding Window

Say:

> The right pointer expands the window until the constraint breaks. Then the left pointer shrinks the window until the constraint becomes valid again. Since each pointer only moves forward at most `n` times, the algorithm is linear.

---

## For Slow-Fast In-Place Problems

Say:

> The fast pointer scans every element. The slow pointer marks the next position where a valid element should be placed. Everything before slow is already correctly processed.

---

## For Linked List Cycle

Say:

> If there is no cycle, the fast pointer reaches null. If there is a cycle, the fast pointer gains one node on the slow pointer each iteration, so they must eventually meet inside the cycle.

---

## 10. Practice Ladder

---

## Level 1: Fundamentals

- [ ] Valid Palindrome
- [ ] Two Sum II
- [ ] Remove Duplicates from Sorted Array
- [ ] Remove Element
- [ ] Move Zeroes
- [ ] Merge Sorted Array
- [ ] Squares of a Sorted Array

---

## Level 2: Core FAANG Patterns

- [ ] 3Sum
- [ ] 3Sum Closest
- [ ] Container With Most Water
- [ ] Sort Colors
- [ ] Minimum Size Subarray Sum
- [ ] Longest Substring Without Repeating Characters
- [ ] Max Consecutive Ones III
- [ ] Fruit Into Baskets

---

## Level 3: Advanced Interview Patterns

- [ ] Trapping Rain Water
- [ ] Minimum Window Substring
- [ ] Subarrays with K Different Integers
- [ ] Longest Repeating Character Replacement
- [ ] Backspace String Compare
- [ ] Valid Palindrome II
- [ ] Linked List Cycle II
- [ ] Palindrome Linked List
- [ ] Reorder List

---

## 11. One-Page Mental Checklist

Before coding, ask:

```text
1. Is the array sorted?
   → left/right from ends.

2. Is the problem about contiguous subarray/substring?
   → sliding window.

3. Is the input linked list?
   → fast/slow pointers.

4. Is the problem asking in-place removal/rearrangement?
   → slow/fast write pointer.

5. Are there two sorted inputs?
   → merge pointers.

6. Are duplicates involved?
   → sort + skip duplicates carefully.

7. Are negative numbers involved in subarray sum?
   → beware sliding window; consider prefix sums.

8. Does it ask exactly K?
   → maybe atMost(K) - atMost(K - 1).

9. Does each pointer only move forward?
   → likely O(n).

10. What invariant am I maintaining?
   → say it out loud before coding.
```

---

## 12. Core Templates to Memorize

---

## Opposite Pointers

```java
int left = 0;
int right = n - 1;

while (left < right) {
    if (condition) {
        left++;
    } else {
        right--;
    }
}
```

---

## Sliding Window

```java
int left = 0;

for (int right = 0; right < n; right++) {
    add(right);

    while (invalid()) {
        remove(left);
        left++;
    }

    updateAnswer();
}
```

---

## Slow-Fast Write Pointer

```java
int slow = 0;

for (int fast = 0; fast < n; fast++) {
    if (valid(nums[fast])) {
        nums[slow] = nums[fast];
        slow++;
    }
}
```

---

## Linked List Fast-Slow

```java
ListNode slow = head;
ListNode fast = head;

while (fast != null && fast.next != null) {
    slow = slow.next;
    fast = fast.next.next;
}
```

---

## Merge Two Sorted Arrays

```java
int i = 0;
int j = 0;

while (i < a.length && j < b.length) {
    if (a[i] <= b[j]) {
        i++;
    } else {
        j++;
    }
}
```

---

## 13. Best Interview Habit

For every Two Pointer problem, explicitly state your invariant.

Examples:

```text
Everything before slow is valid.
Window [left, right] satisfies the constraint.
All pairs outside left/right have been safely eliminated.
leftMax and rightMax represent the best boundary seen so far.
```

If you can define the invariant, the code becomes much easier.

---

## Final Takeaway

For FAANG-style interviews, master these five categories deeply:

1. Sorted left/right pointer
2. Sliding window
3. Slow-fast in-place write pointer
4. Fast-slow linked list pointer
5. Merge/partition pointer

Most Two Pointer problems are variations of these with different invariants.
