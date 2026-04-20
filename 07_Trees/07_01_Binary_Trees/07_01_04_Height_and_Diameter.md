
---

# Height and Diameter

Two of the most fundamental measurements of a binary tree — how tall is it, and what is the longest path through it?

---

## Height

> **Definition.** The _height_ of a binary tree is the number of edges on the longest path from the root to any leaf. An empty tree has height $-1$. A single node has height $0$.

The recursive structure is natural — the height of a tree is 1 plus the maximum height of its subtrees.

$$h(\text{root}) = 1 + \max(h(\text{left}),\ h(\text{right}))$$

```cpp
// Time: O(n), Space: O(h)
int height(TreeNode* root) {
    if (!root) return -1;  // empty tree
    return 1 + max(height(root->left), height(root->right));
}
```

---

## Diameter

> **Definition.** The _diameter_ of a binary tree is the length of the longest path between any two nodes. The path may or may not pass through the root.

This is trickier — the longest path either passes through the root (left height + right height + 2) or lies entirely within a subtree.

The naive approach computes height for every node separately — $O(n^2)$. The smart approach computes height and diameter simultaneously in a single pass — $O(n)$.

```cpp
int diameterHelper;  // global to track max

// Returns height, updates diameterHelper as side effect
// Time: O(n), Space: O(h)
int heightForDiameter(TreeNode* root) {
    if (!root) return -1;
    int leftH  = heightForDiameter(root->left);
    int rightH = heightForDiameter(root->right);

    // Path through this node = edges to leftmost leaf + edges to rightmost leaf
    int pathThrough = (leftH + 1) + (rightH + 1);
    diameterHelper = max(diameterHelper, pathThrough);

    return 1 + max(leftH, rightH);
}

int diameter(TreeNode* root) {
    diameterHelper = 0;
    heightForDiameter(root);
    return diameterHelper;
}
```

---

## Balanced Tree Check

Using the same single-pass idea — check balance while computing height. Return $-2$ as a sentinel to signal that a subtree is already unbalanced.

```cpp
// Returns height if balanced, -2 if unbalanced
// Time: O(n), Space: O(h)
int checkBalance(TreeNode* root) {
    if (!root) return -1;
    int leftH  = checkBalance(root->left);
    int rightH = checkBalance(root->right);
    if (leftH == -2 || rightH == -2) return -2;  // already unbalanced below
    if (abs(leftH - rightH) > 1)    return -2;  // unbalanced here
    return 1 + max(leftH, rightH);
}

bool isBalanced(TreeNode* root) {
    return checkBalance(root) != -2;
}
```

---

## Complexity Summary

|Problem|Naive|Optimal|
|---|---|---|
|Height|$O(n)$|$O(n)$|
|Diameter|$O(n^2)$|$O(n)$ single pass|
|Balance check|$O(n^2)$|$O(n)$ single pass|

The pattern — whenever a problem requires computing something at every node that depends on subtree information, try to compute it in a single bottom-up DFS pass rather than calling a helper repeatedly from the top.

---
