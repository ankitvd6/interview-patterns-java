---
title: Trees Interview Pattern Atlas
description: Tree interview patterns covering traversal, DFS, BFS, BSTs, LCA, serialization, tree DP, tries, and advanced structures.
section: Trees
summary: A broad tree-pattern atlas for recognizing and solving tree, BST, trie, and tree-DP interview problems.
category: DSA Pattern
tags: [trees, bst, trie, java]
order: 90
---
# Trees Interview Pattern Atlas
## FAANG-Level Pattern Notebook

This notebook is designed as a practical interview-prep guide for Tree problems. It focuses on pattern recognition, reusable templates, edge cases, Java implementations, and how to reason during interviews.

---

## Table of Contents

1. Tree Problem Classification
2. Core Tree Traversals
3. Recursive DFS Mental Model
4. Iterative DFS Templates
5. BFS / Level Order Patterns
6. Top-Down DFS
7. Bottom-Up DFS
8. Height-Based Problems
9. Diameter Pattern
10. Maximum Path Sum Pattern
11. Root-to-Leaf Path Patterns
12. Prefix Sum on Trees
13. Lowest Common Ancestor Patterns
14. BST Patterns
15. Validate BST
16. Kth Smallest / Kth Largest
17. BST Iterator
18. Tree Construction
19. Serialization / Deserialization
20. Subtree and Tree Matching
21. Symmetry / Mirror Problems
22. Boundary / Views / Vertical Traversal
23. Complete Binary Tree Patterns
24. Tree DP
25. Trie Patterns
26. Segment Tree Patterns
27. Fenwick Tree Patterns
28. Euler Tour / Tree Flattening
29. Interview Debugging Checklist
30. FAANG Question Map
31. One-Page Revision Sheet

---

# 1. Tree Problem Classification

Before writing code, classify the problem.

Most Tree interview problems belong to one of these families:

| Category | Typical Signal | Common Technique |
|---|---|---|
| Traversal | Visit nodes, collect values | DFS / BFS |
| Level-based | Level, depth, row, nearest | BFS |
| Root-to-leaf | All paths, target sum | DFS + backtracking |
| Child-to-parent aggregation | Height, diameter, balanced | Bottom-up DFS |
| Ancestor information | Path prefix, bounds, depth | Top-down DFS |
| BST-specific | Sorted, kth, range, successor | Inorder / BST pruning |
| Common ancestor | LCA, distance between nodes | Recursive LCA |
| Construct tree | preorder/inorder/postorder | Recursion + index map |
| Longest path | diameter, max path sum | DFS with global answer |
| Tree DP | choose/skip, camera, rob | Return multiple states |
| Serialization | encode/decode | preorder with null markers |
| Subtree | same tree, contains tree | DFS matching / hashing |

Interview habit:

```text
First ask:
1. Is it binary tree or BST?
2. Does order matter?
3. Is the answer per level?
4. Does parent need child information?
5. Does child need ancestor information?
6. Is the path root-to-leaf or anywhere-to-anywhere?
```

---

# 2. Core Tree Traversals

## Preorder

```text
Root -> Left -> Right
```

Use when:

- Need to process parent before children
- Serialize tree
- Copy tree
- Prefix-style traversal
- Top-down DFS

Java:

```java
void preorder(TreeNode root) {
    if (root == null) return;

    process(root);
    preorder(root.left);
    preorder(root.right);
}
```

---

## Inorder

```text
Left -> Root -> Right
```

Use when:

- BST sorted order
- Validate BST
- Kth smallest
- Convert BST to sorted list
- Recover swapped BST

Java:

```java
void inorder(TreeNode root) {
    if (root == null) return;

    inorder(root.left);
    process(root);
    inorder(root.right);
}
```

---

## Postorder

```text
Left -> Right -> Root
```

Use when:

- Need child result before parent
- Height
- Diameter
- Balanced tree
- Delete tree
- Tree DP
- Max path sum

Java:

```java
void postorder(TreeNode root) {
    if (root == null) return;

    postorder(root.left);
    postorder(root.right);
    process(root);
}
```

---

# 3. Recursive DFS Mental Model

Almost every DFS tree solution can be described as:

```java
ReturnType dfs(TreeNode node, ExtraState stateFromParent) {
    if (node == null) {
        return baseCase;
    }

    // optional: use state from parent

    ReturnType left = dfs(node.left, updatedState);
    ReturnType right = dfs(node.right, updatedState);

    // combine child results
    return result;
}
```

## Three DFS Questions

Ask these before coding:

```text
1. What does dfs(node) mean?
2. What should dfs(null) return?
3. How do I combine left and right?
```

Example:

```text
height(node) = height of subtree rooted at node
height(null) = 0
height(node) = 1 + max(height(left), height(right))
```

---

# 4. Iterative DFS Templates

Recursive DFS is usually easiest, but sometimes interviewers ask iterative.

## Iterative Preorder

```java
List<Integer> preorderTraversal(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    if (root == null) return result;

    Stack<TreeNode> stack = new Stack<>();
    stack.push(root);

    while (!stack.isEmpty()) {
        TreeNode node = stack.pop();
        result.add(node.val);

        if (node.right != null) stack.push(node.right);
        if (node.left != null) stack.push(node.left);
    }

    return result;
}
```

Why push right first?

Because stack is LIFO, and left should be processed before right.

---

## Iterative Inorder

```java
List<Integer> inorderTraversal(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    Stack<TreeNode> stack = new Stack<>();

    TreeNode curr = root;

    while (curr != null || !stack.isEmpty()) {
        while (curr != null) {
            stack.push(curr);
            curr = curr.left;
        }

        curr = stack.pop();
        result.add(curr.val);
        curr = curr.right;
    }

    return result;
}
```

Most useful for BST Iterator and Kth Smallest.

---

## Iterative Postorder

```java
List<Integer> postorderTraversal(TreeNode root) {
    LinkedList<Integer> result = new LinkedList<>();
    if (root == null) return result;

    Stack<TreeNode> stack = new Stack<>();
    stack.push(root);

    while (!stack.isEmpty()) {
        TreeNode node = stack.pop();
        result.addFirst(node.val);

        if (node.left != null) stack.push(node.left);
        if (node.right != null) stack.push(node.right);
    }

    return result;
}
```

This uses modified preorder:

```text
Root -> Right -> Left
```

Then reverse to get:

```text
Left -> Right -> Root
```

---

# 5. BFS / Level Order Patterns

Use BFS when the problem asks about:

- Level order
- Minimum depth
- Nearest node
- Right side view
- Zigzag traversal
- Average per level
- Width of binary tree
- Distance K if graph conversion is involved

## Standard BFS Template

```java
List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> result = new ArrayList<>();
    if (root == null) return result;

    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);

    while (!queue.isEmpty()) {
        int size = queue.size();
        List<Integer> level = new ArrayList<>();

        for (int i = 0; i < size; i++) {
            TreeNode node = queue.poll();
            level.add(node.val);

            if (node.left != null) queue.offer(node.left);
            if (node.right != null) queue.offer(node.right);
        }

        result.add(level);
    }

    return result;
}
```

Complexity:

```text
Time: O(n)
Space: O(w), where w = maximum width of tree
```

---

## Right Side View

Pattern:

```text
BFS level order + take last node of each level
```

```java
List<Integer> rightSideView(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    if (root == null) return result;

    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);

    while (!queue.isEmpty()) {
        int size = queue.size();

        for (int i = 0; i < size; i++) {
            TreeNode node = queue.poll();

            if (i == size - 1) {
                result.add(node.val);
            }

            if (node.left != null) queue.offer(node.left);
            if (node.right != null) queue.offer(node.right);
        }
    }

    return result;
}
```

Alternative DFS:

```text
Visit right first. Add first node seen at each depth.
```

---

## Zigzag Level Order

```java
List<List<Integer>> zigzagLevelOrder(TreeNode root) {
    List<List<Integer>> result = new ArrayList<>();
    if (root == null) return result;

    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);

    boolean leftToRight = true;

    while (!queue.isEmpty()) {
        int size = queue.size();
        LinkedList<Integer> level = new LinkedList<>();

        for (int i = 0; i < size; i++) {
            TreeNode node = queue.poll();

            if (leftToRight) {
                level.addLast(node.val);
            } else {
                level.addFirst(node.val);
            }

            if (node.left != null) queue.offer(node.left);
            if (node.right != null) queue.offer(node.right);
        }

        result.add(level);
        leftToRight = !leftToRight;
    }

    return result;
}
```

---

# 6. Top-Down DFS

Top-down DFS means parent sends useful information to children.

Use this when the path/state depends on ancestors.

Examples:

- Path Sum
- Binary Tree Paths
- Validate BST using bounds
- Count Good Nodes
- Root-to-leaf path collection
- DFS with current depth

## Template

```java
void dfs(TreeNode node, State stateFromParent) {
    if (node == null) return;

    State nextState = update(stateFromParent, node);

    dfs(node.left, nextState);
    dfs(node.right, nextState);
}
```

---

## Count Good Nodes

A node is good if its value is greater than or equal to all values before it on the root-to-node path.

```java
int goodNodes(TreeNode root) {
    return dfs(root, Integer.MIN_VALUE);
}

int dfs(TreeNode node, int maxSoFar) {
    if (node == null) return 0;

    int count = node.val >= maxSoFar ? 1 : 0;
    maxSoFar = Math.max(maxSoFar, node.val);

    count += dfs(node.left, maxSoFar);
    count += dfs(node.right, maxSoFar);

    return count;
}
```

Recognition:

```text
"along the path from root"
"ancestor values"
"so far"
```

---

# 7. Bottom-Up DFS

Bottom-up DFS means child subtrees return information to the parent.

Use this when the answer at a node depends on left and right subtree results.

Examples:

- Height
- Diameter
- Balanced Binary Tree
- Max Path Sum
- Largest BST Subtree
- Binary Tree Cameras
- House Robber III

## Template

```java
ReturnType dfs(TreeNode node) {
    if (node == null) return base;

    ReturnType left = dfs(node.left);
    ReturnType right = dfs(node.right);

    return combine(node, left, right);
}
```

Recognition:

```text
"subtree"
"height"
"balanced"
"longest path"
"best result from children"
```

---

# 8. Height-Based Problems

Height is the most reusable building block.

```java
int height(TreeNode root) {
    if (root == null) return 0;

    int left = height(root.left);
    int right = height(root.right);

    return 1 + Math.max(left, right);
}
```

## Max Depth

Same as height.

```java
int maxDepth(TreeNode root) {
    if (root == null) return 0;
    return 1 + Math.max(maxDepth(root.left), maxDepth(root.right));
}
```

## Min Depth

Careful: null child should not count as depth 0 if the other child exists.

```java
int minDepth(TreeNode root) {
    if (root == null) return 0;

    if (root.left == null) return 1 + minDepth(root.right);
    if (root.right == null) return 1 + minDepth(root.left);

    return 1 + Math.min(minDepth(root.left), minDepth(root.right));
}
```

Common bug:

```java
return 1 + Math.min(leftDepth, rightDepth);
```

This fails when one child is null.

---

# 9. Diameter Pattern

Diameter means longest path between any two nodes.

The path may or may not pass through root.

## Key idea

At every node:

```text
candidate diameter = height(left) + height(right)
```

Java:

```java
class Solution {
    int diameter = 0;

    public int diameterOfBinaryTree(TreeNode root) {
        dfs(root);
        return diameter;
    }

    private int dfs(TreeNode node) {
        if (node == null) return 0;

        int left = dfs(node.left);
        int right = dfs(node.right);

        diameter = Math.max(diameter, left + right);

        return 1 + Math.max(left, right);
    }
}
```

Complexity:

```text
Time: O(n)
Space: O(h)
```

Recognition:

```text
"longest path between two nodes"
"number of edges on longest path"
```

---

# 10. Maximum Path Sum Pattern

Maximum Path Sum is like diameter, but with values and negative numbers.

Path can start and end anywhere.

## Key idea

At each node:

```text
Best path using node as highest/root split:
node.val + max(0, leftGain) + max(0, rightGain)

Return to parent:
node.val + max(leftGain, rightGain)
```

Java:

```java
class Solution {
    int answer = Integer.MIN_VALUE;

    public int maxPathSum(TreeNode root) {
        dfs(root);
        return answer;
    }

    private int dfs(TreeNode node) {
        if (node == null) return 0;

        int left = Math.max(0, dfs(node.left));
        int right = Math.max(0, dfs(node.right));

        answer = Math.max(answer, node.val + left + right);

        return node.val + Math.max(left, right);
    }
}
```

Why `Math.max(0, dfs(...))`?

Because a negative path should be dropped.

Common bug:

Returning `node.val + left + right` to parent. That creates a forked path, which is invalid because a parent can only extend one branch.

---

# 11. Root-to-Leaf Path Patterns

## Path Sum I

```java
boolean hasPathSum(TreeNode root, int targetSum) {
    if (root == null) return false;

    if (root.left == null && root.right == null) {
        return targetSum == root.val;
    }

    return hasPathSum(root.left, targetSum - root.val)
        || hasPathSum(root.right, targetSum - root.val);
}
```

---

## Path Sum II

Return all root-to-leaf paths whose sum equals target.

```java
List<List<Integer>> result = new ArrayList<>();

public List<List<Integer>> pathSum(TreeNode root, int targetSum) {
    dfs(root, targetSum, new ArrayList<>());
    return result;
}

private void dfs(TreeNode node, int remaining, List<Integer> path) {
    if (node == null) return;

    path.add(node.val);

    if (node.left == null && node.right == null && remaining == node.val) {
        result.add(new ArrayList<>(path));
    }

    dfs(node.left, remaining - node.val, path);
    dfs(node.right, remaining - node.val, path);

    path.remove(path.size() - 1);
}
```

Backtracking rule:

```text
Add before recursion.
Remove after recursion.
Copy before saving.
```

---

# 12. Prefix Sum on Trees

Used for Path Sum III.

Problem:

```text
Count paths that sum to target.
Path can start and end anywhere, but must go downward.
```

## Core idea

Maintain prefix sums from root to current node.

If currentSum - target existed before, then a path ending at current node sums to target.

```java
class Solution {
    int count = 0;

    public int pathSum(TreeNode root, int targetSum) {
        Map<Long, Integer> prefix = new HashMap<>();
        prefix.put(0L, 1);

        dfs(root, 0L, targetSum, prefix);
        return count;
    }

    private void dfs(TreeNode node, long currentSum, int target, Map<Long, Integer> prefix) {
        if (node == null) return;

        currentSum += node.val;

        count += prefix.getOrDefault(currentSum - target, 0);

        prefix.put(currentSum, prefix.getOrDefault(currentSum, 0) + 1);

        dfs(node.left, currentSum, target, prefix);
        dfs(node.right, currentSum, target, prefix);

        prefix.put(currentSum, prefix.get(currentSum) - 1);
    }
}
```

Recognition:

```text
"number of paths"
"path can start anywhere"
"sum equals target"
"downward path"
```

Why backtrack the map?

Because prefix sums should only represent the current root-to-node path, not sibling paths.

---

# 13. Lowest Common Ancestor Patterns

## LCA in Binary Tree

Memorize this.

```java
TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if (root == null || root == p || root == q) {
        return root;
    }

    TreeNode left = lowestCommonAncestor(root.left, p, q);
    TreeNode right = lowestCommonAncestor(root.right, p, q);

    if (left != null && right != null) {
        return root;
    }

    return left != null ? left : right;
}
```

Why it works:

```text
If p and q are split across left and right, current node is LCA.
If both are on one side, that side returns the LCA.
If current node is p or q, it can be ancestor.
```

---

## LCA in BST

Use ordering property.

```java
TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    while (root != null) {
        if (p.val < root.val && q.val < root.val) {
            root = root.left;
        } else if (p.val > root.val && q.val > root.val) {
            root = root.right;
        } else {
            return root;
        }
    }

    return null;
}
```

Complexity:

```text
Time: O(h)
Space: O(1)
```

---

## Distance Between Two Nodes

Pattern:

```text
distance(a, b) = depth(a) + depth(b) - 2 * depth(lca)
```

Use when asked:

- distance between nodes
- number of edges between nodes
- burn tree from target
- directions between two nodes

---

# 14. BST Patterns

BST property:

```text
left subtree values < node.val < right subtree values
```

Important:

The entire left subtree must be less than root, not just the left child.

## Search BST

```java
TreeNode searchBST(TreeNode root, int val) {
    while (root != null) {
        if (val < root.val) root = root.left;
        else if (val > root.val) root = root.right;
        else return root;
    }

    return null;
}
```

---

## Insert into BST

```java
TreeNode insertIntoBST(TreeNode root, int val) {
    if (root == null) return new TreeNode(val);

    if (val < root.val) {
        root.left = insertIntoBST(root.left, val);
    } else {
        root.right = insertIntoBST(root.right, val);
    }

    return root;
}
```

---

## Delete from BST

Cases:

1. Node has no child
2. Node has one child
3. Node has two children

For two children, replace with inorder successor.

```java
TreeNode deleteNode(TreeNode root, int key) {
    if (root == null) return null;

    if (key < root.val) {
        root.left = deleteNode(root.left, key);
    } else if (key > root.val) {
        root.right = deleteNode(root.right, key);
    } else {
        if (root.left == null) return root.right;
        if (root.right == null) return root.left;

        TreeNode successor = findMin(root.right);
        root.val = successor.val;
        root.right = deleteNode(root.right, successor.val);
    }

    return root;
}

TreeNode findMin(TreeNode node) {
    while (node.left != null) node = node.left;
    return node;
}
```

---

# 15. Validate BST

Use min/max bounds.

```java
boolean isValidBST(TreeNode root) {
    return dfs(root, Long.MIN_VALUE, Long.MAX_VALUE);
}

boolean dfs(TreeNode node, long min, long max) {
    if (node == null) return true;

    if (node.val <= min || node.val >= max) return false;

    return dfs(node.left, min, node.val)
        && dfs(node.right, node.val, max);
}
```

Common bug:

Only checking immediate children:

```java
node.left.val < node.val && node.right.val > node.val
```

This is insufficient.

Counterexample:

```text
    5
   / \
  1   7
     /
    4
```

4 is in the right subtree of 5 but less than 5, so invalid.

---

# 16. Kth Smallest / Kth Largest

BST inorder traversal gives sorted order.

## Kth Smallest

```java
class Solution {
    int count = 0;
    int answer = -1;

    public int kthSmallest(TreeNode root, int k) {
        inorder(root, k);
        return answer;
    }

    private void inorder(TreeNode node, int k) {
        if (node == null) return;

        inorder(node.left, k);

        count++;
        if (count == k) {
            answer = node.val;
            return;
        }

        inorder(node.right, k);
    }
}
```

For better early stopping:

```java
private void inorder(TreeNode node, int k) {
    if (node == null || count >= k) return;

    inorder(node.left, k);

    if (++count == k) {
        answer = node.val;
        return;
    }

    inorder(node.right, k);
}
```

## Kth Largest

Use reverse inorder:

```text
Right -> Root -> Left
```

---

# 17. BST Iterator

Design an iterator with:

```text
next() average O(1)
hasNext() O(1)
space O(h)
```

Use controlled inorder traversal.

```java
class BSTIterator {
    private Stack<TreeNode> stack = new Stack<>();

    public BSTIterator(TreeNode root) {
        pushLeft(root);
    }

    public int next() {
        TreeNode node = stack.pop();
        pushLeft(node.right);
        return node.val;
    }

    public boolean hasNext() {
        return !stack.isEmpty();
    }

    private void pushLeft(TreeNode node) {
        while (node != null) {
            stack.push(node);
            node = node.left;
        }
    }
}
```

---

# 18. Tree Construction

## Build from Preorder and Inorder

Facts:

```text
Preorder: Root Left Right
Inorder:  Left Root Right
```

Algorithm:

1. First preorder element is root.
2. Find root in inorder.
3. Left side of inorder is left subtree.
4. Right side of inorder is right subtree.
5. Recurse.

```java
class Solution {
    int preIndex = 0;
    Map<Integer, Integer> inorderIndex = new HashMap<>();

    public TreeNode buildTree(int[] preorder, int[] inorder) {
        for (int i = 0; i < inorder.length; i++) {
            inorderIndex.put(inorder[i], i);
        }

        return build(preorder, 0, inorder.length - 1);
    }

    private TreeNode build(int[] preorder, int left, int right) {
        if (left > right) return null;

        int rootVal = preorder[preIndex++];
        TreeNode root = new TreeNode(rootVal);

        int mid = inorderIndex.get(rootVal);

        root.left = build(preorder, left, mid - 1);
        root.right = build(preorder, mid + 1, right);

        return root;
    }
}
```

Complexity:

```text
Time: O(n)
Space: O(n)
```

---

## Build from Inorder and Postorder

Facts:

```text
Inorder:   Left Root Right
Postorder: Left Right Root
```

Root is at the end of postorder.

Build right subtree before left subtree when consuming postorder backwards.

```java
class Solution {
    int postIndex;
    Map<Integer, Integer> inorderIndex = new HashMap<>();

    public TreeNode buildTree(int[] inorder, int[] postorder) {
        postIndex = postorder.length - 1;

        for (int i = 0; i < inorder.length; i++) {
            inorderIndex.put(inorder[i], i);
        }

        return build(postorder, 0, inorder.length - 1);
    }

    private TreeNode build(int[] postorder, int left, int right) {
        if (left > right) return null;

        int rootVal = postorder[postIndex--];
        TreeNode root = new TreeNode(rootVal);

        int mid = inorderIndex.get(rootVal);

        root.right = build(postorder, mid + 1, right);
        root.left = build(postorder, left, mid - 1);

        return root;
    }
}
```

---

# 19. Serialization / Deserialization

Use preorder with null markers.

Example:

```text
1,2,#,#,3,#,#
```

## Serialize

```java
public String serialize(TreeNode root) {
    StringBuilder sb = new StringBuilder();
    serialize(root, sb);
    return sb.toString();
}

private void serialize(TreeNode node, StringBuilder sb) {
    if (node == null) {
        sb.append("#,");
        return;
    }

    sb.append(node.val).append(",");
    serialize(node.left, sb);
    serialize(node.right, sb);
}
```

## Deserialize

```java
public TreeNode deserialize(String data) {
    Queue<String> queue = new LinkedList<>(Arrays.asList(data.split(",")));
    return deserialize(queue);
}

private TreeNode deserialize(Queue<String> queue) {
    String value = queue.poll();

    if (value.equals("#")) return null;

    TreeNode node = new TreeNode(Integer.parseInt(value));
    node.left = deserialize(queue);
    node.right = deserialize(queue);

    return node;
}
```

Common follow-up:

Use BFS serialization if interviewer prefers level-order representation.

---

# 20. Subtree and Tree Matching

## Same Tree

```java
boolean isSameTree(TreeNode p, TreeNode q) {
    if (p == null && q == null) return true;
    if (p == null || q == null) return false;

    return p.val == q.val
        && isSameTree(p.left, q.left)
        && isSameTree(p.right, q.right);
}
```

---

## Subtree of Another Tree

```java
boolean isSubtree(TreeNode root, TreeNode subRoot) {
    if (root == null) return false;

    if (isSameTree(root, subRoot)) return true;

    return isSubtree(root.left, subRoot)
        || isSubtree(root.right, subRoot);
}
```

Complexity:

```text
Worst case: O(n * m)
```

Optimizations:

- Serialize both trees and use substring match with null markers
- Tree hashing
- Merkle hash

---

# 21. Symmetry / Mirror Problems

## Symmetric Tree

A tree is symmetric if left and right subtrees are mirrors.

```java
boolean isSymmetric(TreeNode root) {
    if (root == null) return true;
    return mirror(root.left, root.right);
}

boolean mirror(TreeNode a, TreeNode b) {
    if (a == null && b == null) return true;
    if (a == null || b == null) return false;

    return a.val == b.val
        && mirror(a.left, b.right)
        && mirror(a.right, b.left);
}
```

Recognition:

```text
mirror
symmetric
folded tree
```

---

## Invert Binary Tree

```java
TreeNode invertTree(TreeNode root) {
    if (root == null) return null;

    TreeNode left = invertTree(root.left);
    TreeNode right = invertTree(root.right);

    root.left = right;
    root.right = left;

    return root;
}
```

---

# 22. Boundary / Views / Vertical Traversal

## Left View / Right View

DFS pattern:

```text
Right view: visit root -> right -> left
First node seen at each depth
```

```java
List<Integer> result = new ArrayList<>();

void dfs(TreeNode node, int depth) {
    if (node == null) return;

    if (depth == result.size()) {
        result.add(node.val);
    }

    dfs(node.right, depth + 1);
    dfs(node.left, depth + 1);
}
```

For left view, visit left before right.

---

## Vertical Order Traversal

Track:

```text
row = depth
col = horizontal position
left child: col - 1
right child: col + 1
```

Use:

- TreeMap for sorted columns
- PriorityQueue or sorting for row/value ordering depending on problem

```java
class Tuple {
    TreeNode node;
    int row;
    int col;

    Tuple(TreeNode node, int row, int col) {
        this.node = node;
        this.row = row;
        this.col = col;
    }
}
```

High-level approach:

```text
BFS/DFS collect (col, row, value)
Sort by col, then row, then value
Group by col
```

Complexity:

```text
O(n log n)
```

---

# 23. Complete Binary Tree Patterns

## Count Complete Tree Nodes

Naive DFS is O(n). Optimized solution is O(log^2 n).

Idea:

If left height equals right height, tree is perfect:

```text
nodes = 2^height - 1
```

```java
int countNodes(TreeNode root) {
    if (root == null) return 0;

    int leftHeight = leftHeight(root);
    int rightHeight = rightHeight(root);

    if (leftHeight == rightHeight) {
        return (1 << leftHeight) - 1;
    }

    return 1 + countNodes(root.left) + countNodes(root.right);
}

int leftHeight(TreeNode node) {
    int h = 0;
    while (node != null) {
        h++;
        node = node.left;
    }
    return h;
}

int rightHeight(TreeNode node) {
    int h = 0;
    while (node != null) {
        h++;
        node = node.right;
    }
    return h;
}
```

---

# 24. Tree DP

Tree DP means each node returns multiple states.

## House Robber III

At each node:

```text
rob = node.val + left.notRob + right.notRob
notRob = max(left.rob, left.notRob) + max(right.rob, right.notRob)
```

```java
public int rob(TreeNode root) {
    int[] result = dfs(root);
    return Math.max(result[0], result[1]);
}

// result[0] = not rob this node
// result[1] = rob this node
private int[] dfs(TreeNode node) {
    if (node == null) return new int[]{0, 0};

    int[] left = dfs(node.left);
    int[] right = dfs(node.right);

    int rob = node.val + left[0] + right[0];
    int notRob = Math.max(left[0], left[1]) + Math.max(right[0], right[1]);

    return new int[]{notRob, rob};
}
```

---

## Binary Tree Cameras

States:

```text
0 = has camera
1 = covered without camera
2 = needs camera
```

```java
class Solution {
    int cameras = 0;

    public int minCameraCover(TreeNode root) {
        if (dfs(root) == 2) {
            cameras++;
        }
        return cameras;
    }

    private int dfs(TreeNode node) {
        if (node == null) return 1;

        int left = dfs(node.left);
        int right = dfs(node.right);

        if (left == 2 || right == 2) {
            cameras++;
            return 0;
        }

        if (left == 0 || right == 0) {
            return 1;
        }

        return 2;
    }
}
```

Recognition:

```text
"minimum number of devices"
"cover every node"
"choose nodes with constraints"
```

---

# 25. Trie Patterns

Trie is a tree over characters.

Use when:

- Prefix search
- Autocomplete
- Word dictionary
- Replace words
- Word search with dictionary
- Longest common prefix
- Design Add/Search Word

## Trie Node

```java
class TrieNode {
    TrieNode[] children = new TrieNode[26];
    boolean isWord;
}
```

## Insert

```java
class Trie {
    TrieNode root = new TrieNode();

    public void insert(String word) {
        TrieNode node = root;

        for (char c : word.toCharArray()) {
            int idx = c - 'a';

            if (node.children[idx] == null) {
                node.children[idx] = new TrieNode();
            }

            node = node.children[idx];
        }

        node.isWord = true;
    }
}
```

## Search

```java
public boolean search(String word) {
    TrieNode node = root;

    for (char c : word.toCharArray()) {
        int idx = c - 'a';

        if (node.children[idx] == null) return false;

        node = node.children[idx];
    }

    return node.isWord;
}
```

## StartsWith

```java
public boolean startsWith(String prefix) {
    TrieNode node = root;

    for (char c : prefix.toCharArray()) {
        int idx = c - 'a';

        if (node.children[idx] == null) return false;

        node = node.children[idx];
    }

    return true;
}
```

---

# 26. Segment Tree Patterns

Segment Trees are used for range queries with updates.

Use when:

```text
Range query + point/range update
```

Examples:

- Range sum query mutable
- Range minimum query
- Range maximum query
- Count smaller after self
- Calendar booking variants
- Sweep line with dynamic intervals

## Segment Tree for Range Sum

```java
class SegmentTree {
    int[] tree;
    int n;

    SegmentTree(int[] nums) {
        n = nums.length;
        tree = new int[4 * n];
        build(nums, 0, 0, n - 1);
    }

    void build(int[] nums, int node, int l, int r) {
        if (l == r) {
            tree[node] = nums[l];
            return;
        }

        int mid = l + (r - l) / 2;

        build(nums, 2 * node + 1, l, mid);
        build(nums, 2 * node + 2, mid + 1, r);

        tree[node] = tree[2 * node + 1] + tree[2 * node + 2];
    }

    int query(int node, int l, int r, int ql, int qr) {
        if (qr < l || r < ql) return 0;

        if (ql <= l && r <= qr) return tree[node];

        int mid = l + (r - l) / 2;

        return query(2 * node + 1, l, mid, ql, qr)
             + query(2 * node + 2, mid + 1, r, ql, qr);
    }

    void update(int node, int l, int r, int index, int value) {
        if (l == r) {
            tree[node] = value;
            return;
        }

        int mid = l + (r - l) / 2;

        if (index <= mid) {
            update(2 * node + 1, l, mid, index, value);
        } else {
            update(2 * node + 2, mid + 1, r, index, value);
        }

        tree[node] = tree[2 * node + 1] + tree[2 * node + 2];
    }
}
```

Complexity:

```text
Build: O(n)
Query: O(log n)
Update: O(log n)
Space: O(n)
```

---

# 27. Fenwick Tree Patterns

Fenwick Tree / Binary Indexed Tree is simpler than Segment Tree for prefix sums.

Use when:

```text
Need prefix sum queries and point updates
```

## Fenwick Tree

```java
class FenwickTree {
    int[] bit;
    int n;

    FenwickTree(int n) {
        this.n = n;
        bit = new int[n + 1];
    }

    void update(int index, int delta) {
        index++;

        while (index <= n) {
            bit[index] += delta;
            index += index & -index;
        }
    }

    int query(int index) {
        index++;

        int sum = 0;

        while (index > 0) {
            sum += bit[index];
            index -= index & -index;
        }

        return sum;
    }

    int rangeQuery(int left, int right) {
        return query(right) - query(left - 1);
    }
}
```

Common problems:

- Count smaller numbers after self
- Reverse pairs variants
- Dynamic prefix frequencies
- Coordinate compression + frequency counts

---

# 28. Euler Tour / Tree Flattening

Useful for advanced tree queries.

Idea:

Flatten subtree into a contiguous interval.

During DFS:

```text
tin[node] = time when entering node
tout[node] = time after processing subtree
```

Then:

```text
subtree of node = [tin[node], tout[node]]
```

Use cases:

- Subtree sum queries
- Ancestor checks
- Tree + Fenwick / Segment Tree
- Company hierarchy queries
- Dynamic subtree updates

Template:

```java
int timer = 0;
int[] tin;
int[] tout;
List<Integer>[] graph;

void dfs(int node, int parent) {
    tin[node] = timer++;

    for (int nei : graph[node]) {
        if (nei == parent) continue;
        dfs(nei, node);
    }

    tout[node] = timer - 1;
}
```

Ancestor check:

```java
boolean isAncestor(int u, int v) {
    return tin[u] <= tin[v] && tout[v] <= tout[u];
}
```

---

# 29. Interview Debugging Checklist

Use this checklist before submitting.

## Null Cases

```text
root == null
single node
missing left child
missing right child
```

## Recursion

```text
Did I define dfs meaning clearly?
Is base case correct?
Am I returning the correct value to parent?
Am I using global answer only when needed?
```

## Paths

```text
Root-to-leaf or any node to any node?
Can the path branch?
Can values be negative?
Should I drop negative gains?
```

## BST

```text
Did I use global min/max bounds?
Did I avoid checking only immediate children?
Did I use long bounds for Integer.MIN_VALUE / MAX_VALUE?
```

## Backtracking

```text
Did I remove from path after recursion?
Did I copy list before adding to result?
Did I decrement map count after recursion?
```

## BFS

```text
Did I capture level size before loop?
Am I adding children after processing current node?
Do I need first or last node of the level?
```

## Construction

```text
Which traversal gives root?
Did I build right before left for reversed postorder?
Did I use index map for O(n)?
```

---

# 30. FAANG Question Map

## Easy Must-Know

| Problem | Pattern |
|---|---|
| Maximum Depth of Binary Tree | Height DFS |
| Same Tree | Recursive matching |
| Symmetric Tree | Mirror recursion |
| Invert Binary Tree | Postorder swap |
| Balanced Binary Tree | Height with sentinel |
| Minimum Depth | BFS or careful DFS |
| Binary Tree Paths | DFS backtracking |

---

## Medium Must-Know

| Problem | Pattern |
|---|---|
| Binary Tree Level Order Traversal | BFS |
| Zigzag Level Order | BFS + deque |
| Right Side View | BFS last / DFS right-first |
| Validate BST | Min/max DFS |
| Kth Smallest in BST | Inorder |
| Lowest Common Ancestor | Recursive split |
| LCA in BST | BST pruning |
| Path Sum II | DFS backtracking |
| Path Sum III | Prefix sum |
| Construct Binary Tree from Preorder/Inorder | Recursion + map |
| Construct Binary Tree from Inorder/Postorder | Recursion + map |
| BST Iterator | Controlled inorder stack |
| Delete Node in BST | Successor replacement |
| Count Complete Tree Nodes | Perfect subtree height |
| Populating Next Right Pointers | BFS / pointer linking |

---

## Hard / Senior-Level

| Problem | Pattern |
|---|---|
| Binary Tree Maximum Path Sum | DFS gain + global answer |
| Binary Tree Cameras | Tree DP states |
| Serialize and Deserialize Binary Tree | Preorder null markers |
| Recover Binary Search Tree | Inorder anomaly detection |
| Vertical Order Traversal | Coordinate sorting |
| Amount of Time for Binary Tree to Be Infected | Graph BFS / LCA variant |
| Step-by-Step Directions from Node to Node | LCA + paths |
| House Robber III | Tree DP choose/skip |
| Count of Smaller Numbers After Self | Fenwick / Segment Tree |
| Range Sum Query Mutable | Segment Tree / Fenwick |
| Word Search II | Trie + DFS backtracking |

---

# 31. Pattern Recognition Cheat Sheet

| Phrase in Problem | Pattern to Use |
|---|---|
| "level" | BFS |
| "minimum depth" | BFS |
| "root to leaf" | DFS + backtracking |
| "all paths" | DFS + path list |
| "path sum anywhere downward" | Prefix sum |
| "longest path between nodes" | Diameter |
| "maximum path sum" | DFS gain |
| "lowest/common ancestor" | LCA |
| "distance between two nodes" | LCA + depths |
| "BST" | Use ordering |
| "kth smallest" | Inorder |
| "sorted order" | Inorder |
| "validate BST" | Min/max bounds |
| "construct from traversals" | Recursive split |
| "serialize" | Preorder + null marker |
| "subtree" | Same tree matching |
| "mirror/symmetric" | Pair recursion |
| "minimum devices/cameras" | Tree DP states |
| "range query with update" | Segment Tree / Fenwick |
| "prefix search" | Trie |
| "subtree query" | Euler tour |

---

# 32. Java TreeNode Definition

Most platforms give this:

```java
public class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;

    TreeNode() {}

    TreeNode(int val) {
        this.val = val;
    }

    TreeNode(int val, TreeNode left, TreeNode right) {
        this.val = val;
        this.left = left;
        this.right = right;
    }
}
```

---

# 33. Complexity Reference

| Pattern | Time | Space |
|---|---:|---:|
| DFS traversal | O(n) | O(h) |
| BFS traversal | O(n) | O(w) |
| BST search | O(h) | O(1) iterative |
| Validate BST | O(n) | O(h) |
| Kth smallest | O(h + k) | O(h) |
| Diameter | O(n) | O(h) |
| Max path sum | O(n) | O(h) |
| Build tree | O(n) | O(n) |
| Serialize / deserialize | O(n) | O(n) |
| Subtree naive | O(nm) | O(h) |
| Segment tree query/update | O(log n) | O(n) |
| Fenwick query/update | O(log n) | O(n) |
| Trie insert/search | O(L) | O(total chars) |

Where:

```text
n = number of nodes
h = height of tree
w = maximum width of tree
m = size of subtree pattern
L = word length
```

---

# 34. One-Page Revision Sheet

## Universal DFS

```java
ReturnType dfs(TreeNode node) {
    if (node == null) return base;

    ReturnType left = dfs(node.left);
    ReturnType right = dfs(node.right);

    return combine(node, left, right);
}
```

## Height

```java
int height(TreeNode node) {
    if (node == null) return 0;
    return 1 + Math.max(height(node.left), height(node.right));
}
```

## Diameter

```java
int ans = 0;

int dfs(TreeNode node) {
    if (node == null) return 0;

    int left = dfs(node.left);
    int right = dfs(node.right);

    ans = Math.max(ans, left + right);

    return 1 + Math.max(left, right);
}
```

## Max Path Sum

```java
int ans = Integer.MIN_VALUE;

int dfs(TreeNode node) {
    if (node == null) return 0;

    int left = Math.max(0, dfs(node.left));
    int right = Math.max(0, dfs(node.right));

    ans = Math.max(ans, node.val + left + right);

    return node.val + Math.max(left, right);
}
```

## LCA

```java
TreeNode lca(TreeNode root, TreeNode p, TreeNode q) {
    if (root == null || root == p || root == q) return root;

    TreeNode left = lca(root.left, p, q);
    TreeNode right = lca(root.right, p, q);

    if (left != null && right != null) return root;

    return left != null ? left : right;
}
```

## Validate BST

```java
boolean dfs(TreeNode node, long min, long max) {
    if (node == null) return true;

    if (node.val <= min || node.val >= max) return false;

    return dfs(node.left, min, node.val)
        && dfs(node.right, node.val, max);
}
```

## BFS

```java
Queue<TreeNode> q = new LinkedList<>();
q.offer(root);

while (!q.isEmpty()) {
    int size = q.size();

    for (int i = 0; i < size; i++) {
        TreeNode node = q.poll();

        if (node.left != null) q.offer(node.left);
        if (node.right != null) q.offer(node.right);
    }
}
```

## Backtracking

```java
path.add(node.val);

dfs(node.left, path);
dfs(node.right, path);

path.remove(path.size() - 1);
```

## Prefix Sum

```java
count += map.getOrDefault(currSum - target, 0);
map.put(currSum, map.getOrDefault(currSum, 0) + 1);

dfs(left);
dfs(right);

map.put(currSum, map.get(currSum) - 1);
```

## BST Inorder

```text
Left -> Root -> Right = sorted order
```

## Main Interview Rule

```text
If answer depends on children: bottom-up DFS.
If children need ancestor state: top-down DFS.
If answer is level-based: BFS.
If tree is BST: use ordering immediately.
```

---

# 35. Suggested Practice Plan

## Day 1: Traversal + Height

- Maximum Depth
- Minimum Depth
- Same Tree
- Invert Tree
- Symmetric Tree

## Day 2: BFS

- Level Order
- Zigzag Level Order
- Right Side View
- Average of Levels
- Binary Tree Level Order Bottom-Up

## Day 3: BST

- Validate BST
- Search BST
- Insert BST
- Kth Smallest
- BST Iterator

## Day 4: Paths

- Path Sum I
- Path Sum II
- Path Sum III
- Binary Tree Paths
- Sum Root to Leaf Numbers

## Day 5: LCA + Construction

- LCA Binary Tree
- LCA BST
- Construct from Preorder/Inorder
- Construct from Inorder/Postorder
- Serialize/Deserialize

## Day 6: Hard Patterns

- Diameter
- Maximum Path Sum
- House Robber III
- Binary Tree Cameras
- Vertical Order Traversal

## Day 7: Advanced Trees

- Trie
- Word Search II
- Segment Tree
- Fenwick Tree
- Euler Tour basics

---

# 36. Final Interview Advice

When stuck, say this out loud:

```text
I want to define what my recursive function returns.
For each node, I will solve the problem for the left and right subtrees,
then combine those results with the current node.
```

This framing works for most tree questions and shows structured thinking.

For senior-level interviews, always discuss:

- recursion depth and stack overflow risk
- whether iterative solution is required
- edge cases with null children
- BST invariant across entire subtree
- negative values in path problems
- whether paths can branch or must remain simple
- time and space complexity
- possible follow-up optimization
