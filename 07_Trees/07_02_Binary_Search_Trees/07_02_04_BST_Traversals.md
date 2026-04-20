
Here is `07_02_04_BST_Traversals.md`:

---

# BST Traversals

BST traversals are the same three DFS traversals from [[07_01_02_Traversals_Inorder_Preorder_Postorder]] but with added meaning due to the BST property.

---

## Inorder — Sorted Output

The most important BST traversal. Inorder gives all elements in sorted ascending order.

```cpp
// Inorder of BST = sorted sequence
// Time: O(n), Space: O(h)
void inorder(TreeNode* root, vector<int>& result) {
    if (!root) return;
    inorder(root->left, result);
    result.push_back(root->val);
    inorder(root->right, result);
}
```

**Applications:**

- Convert BST to sorted array
- Find $k$-th smallest element
- Check if a binary tree is a valid BST
- Merge two BSTs

---

## Validate BST

A common mistake — checking that each node is greater than its left child and less than its right child is **not sufficient**. You must verify against the full range of valid values.

```cpp
// Wrong approach — does not check full range
bool isValidBSTWrong(TreeNode* root) {
    if (!root) return true;
    if (root->left && root->left->val >= root->val) return false;
    if (root->right && root->right->val <= root->val) return false;
    return isValidBSTWrong(root->left) && isValidBSTWrong(root->right);
}

// Correct approach — pass valid range down
// Time: O(n), Space: O(h)
bool isValidBST(TreeNode* root, long long minVal = LLONG_MIN, long long maxVal = LLONG_MAX) {
    if (!root) return true;
    if (root->val <= minVal || root->val >= maxVal) return false;
    return isValidBST(root->left,  minVal,    root->val) &&
           isValidBST(root->right, root->val, maxVal);
}
```

Why the wrong approach fails — consider:

```
    5
   / \
  1   4
     / \
    3   6
```

Node 4's children look locally valid (3 < 4 < 6), but 4 < 5 violates the BST property — it should be in the left subtree. The range check catches this.

---

## Kth Smallest Element

Using inorder traversal with an early exit:

```cpp
// Time: O(k + h), Space: O(h)
int kthSmallest(TreeNode* root, int k) {
    int count = 0, result = -1;
    function<void(TreeNode*)> inorder = [&](TreeNode* node) {
        if (!node || count >= k) return;
        inorder(node->left);
        if (++count == k) { result = node->val; return; }
        inorder(node->right);
    };
    inorder(root);
    return result;
}
```

---
