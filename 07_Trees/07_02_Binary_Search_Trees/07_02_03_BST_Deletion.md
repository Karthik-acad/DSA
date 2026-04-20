
---

# BST Deletion

Deletion is the most complex BST operation because removing a node must preserve the BST property. There are three cases depending on the number of children the node has.

> **Case 1 — Node is a leaf (no children):** Simply remove it.

> **Case 2 — Node has one child:** Replace the node with its child.

> **Case 3 — Node has two children:** Find the **inorder successor** (smallest node in right subtree) or **inorder predecessor** (largest in left subtree). Copy its value into the node to be deleted, then delete the successor/predecessor (which has at most one child — Case 1 or 2).

```cpp
// Find minimum node in a subtree
TreeNode* findMin(TreeNode* root) {
    while (root->left) root = root->left;
    return root;
}

// Recursive delete — Time: O(h), Space: O(h)
TreeNode* deleteNode(TreeNode* root, int val) {
    if (!root) return nullptr;

    if (val < root->val) {
        root->left = deleteNode(root->left, val);
    } else if (val > root->val) {
        root->right = deleteNode(root->right, val);
    } else {
        // Found the node to delete
        if (!root->left) {
            // Case 1 or 2 — no left child
            TreeNode* temp = root->right;
            delete root;
            return temp;
        } else if (!root->right) {
            // Case 2 — no right child
            TreeNode* temp = root->left;
            delete root;
            return temp;
        } else {
            // Case 3 — two children
            // Find inorder successor (min of right subtree)
            TreeNode* successor = findMin(root->right);
            root->val = successor->val;  // copy successor value
            // Delete successor from right subtree
            root->right = deleteNode(root->right, successor->val);
        }
    }
    return root;
}
```

---

## Visual Example

Delete 3 from:

```
        8
       / \
      3   10
     / \
    1   6
       / \
      4   7
```

Node 3 has two children → find inorder successor = 4 (minimum of right subtree of 3). Copy 4 into node 3's position. Delete 4 from right subtree (Case 1 — 4 is a leaf).

Result:

```
        8
       / \
      4   10
     / \
    1   6
         \
          7
```

BST property maintained — all nodes left of 4 are smaller, all right are larger. ✓

**Complexity:** $O(h)$ time where $h$ is tree height.

---
