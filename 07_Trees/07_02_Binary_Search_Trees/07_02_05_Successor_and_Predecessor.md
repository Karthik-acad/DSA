
Here is `07_02_05_Successor_and_Predecessor.md`:

---

# Successor and Predecessor

> **Definition.** The _inorder successor_ of a node $v$ in a BST is the node with the smallest value greater than $v$'s value — the next node in inorder traversal.

> **Definition.** The _inorder predecessor_ of a node $v$ is the node with the largest value less than $v$'s value — the previous node in inorder traversal.

These are fundamental BST operations used in deletion, range queries, and ordered iteration.

---

## Finding the Successor

Two cases:

**Case 1 — Node has a right subtree:** The successor is the minimum node of the right subtree.

**Case 2 — Node has no right subtree:** The successor is the lowest ancestor for which the given node is in the left subtree — i.e., the first ancestor where we took a left turn going down.

```cpp
// Time: O(h), Space: O(1)
TreeNode* inorderSuccessor(TreeNode* root, TreeNode* target) {
    TreeNode* successor = nullptr;
    while (root) {
        if (target->val < root->val) {
            successor = root;     // root could be successor — went left
            root = root->left;
        } else {
            root = root->right;   // go right — successor must be there
        }
    }
    return successor;
}

// If we have the node directly (not just its value)
TreeNode* successorDirect(TreeNode* node) {
    if (node->right) {
        // Case 1 — min of right subtree
        TreeNode* curr = node->right;
        while (curr->left) curr = curr->left;
        return curr;
    }
    // Case 2 — need parent pointers or traverse from root
    return nullptr;  // requires root + target value version above
}
```

---

## Finding the Predecessor

Mirror image of successor.

```cpp
// Time: O(h), Space: O(1)
TreeNode* inorderPredecessor(TreeNode* root, TreeNode* target) {
    TreeNode* predecessor = nullptr;
    while (root) {
        if (target->val > root->val) {
            predecessor = root;   // root could be predecessor — went right
            root = root->right;
        } else {
            root = root->left;
        }
    }
    return predecessor;
}
```

---

## Complexity Summary

|Operation|Time|Space|
|---|---|---|
|Successor / Predecessor|$O(h)$|$O(1)$|

Where $h = O(\log n)$ for balanced BSTs and $O(n)$ worst case for skewed.

---

## Why These Matter

Successor and predecessor are used in:

- **BST deletion** — the inorder successor replaces the deleted node in Case 3
- **Floor and ceiling** — predecessor is floor, successor is ceiling for a given key
- **Range queries** — iterate from successor of lower bound to predecessor of upper bound
- **OrderedDict / TreeMap** — `lower_bound` and `upper_bound` in C++ `std::map` are essentially predecessor/successor queries

---
