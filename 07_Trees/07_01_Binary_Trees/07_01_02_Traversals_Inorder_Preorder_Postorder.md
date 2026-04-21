
---

# Traversals — Inorder, Preorder, Postorder

To process all nodes in a binary tree we need to **traverse** it — visit every node exactly once in some defined order. There are three fundamental depth-first traversal orders, each defined by when the current node is processed relative to its subtrees.

> **Definition.** _Preorder traversal_ visits the current node **before** its subtrees: Root → Left → Right.

> **Definition.** _Inorder traversal_ visits the current node **between** its subtrees: Left → Root → Right.

> **Definition.** _Postorder traversal_ visits the current node **after** its subtrees: Left → Right → Root.

Think of it as three different ways to read a book — preorder reads the title before the chapters, inorder reads left chapter then title then right chapter, postorder reads all chapters before the title.

For this tree:

```
        1
       / \
      2   3
     / \
    4   5
```

- **Preorder:** 1, 2, 4, 5, 3
- **Inorder:** 4, 2, 5, 1, 3
- **Postorder:** 4, 5, 2, 3, 1

---

## Recursive Implementation

```cpp
#include <iostream>
using namespace std;

// All three: Time O(n), Space O(h) where h = tree height
// h = O(log n) balanced, O(n) skewed

void preorder(TreeNode* root) {
    if (!root) return;
    cout << root->val << " ";  // process BEFORE subtrees
    preorder(root->left);
    preorder(root->right);
}

void inorder(TreeNode* root) {
    if (!root) return;
    inorder(root->left);
    cout << root->val << " ";  // process BETWEEN subtrees
    inorder(root->right);
}

void postorder(TreeNode* root) {
    if (!root) return;
    postorder(root->left);
    postorder(root->right);
    cout << root->val << " ";  // process AFTER subtrees
}
```

---

## Iterative Implementation

The recursive versions use the call stack implicitly. The iterative versions make the stack explicit — useful when stack depth is a concern or when you need more control over traversal.

```cpp
#include <stack>
#include <vector>

// Iterative preorder — O(n) time, O(h) space
vector<int> preorderIter(TreeNode* root) {
    vector<int> result;
    if (!root) return result;
    stack<TreeNode*> st;
    st.push(root);
    while (!st.empty()) {
        TreeNode* node = st.top(); st.pop();
        result.push_back(node->val);
        // Push right first so left is processed first
        if (node->right) st.push(node->right);
        if (node->left)  st.push(node->left);
    }
    return result;
}

// Iterative inorder — O(n) time, O(h) space
vector<int> inorderIter(TreeNode* root) {
    vector<int> result;
    stack<TreeNode*> st;
    TreeNode* curr = root;
    while (curr || !st.empty()) {
        while (curr) {           // go as far left as possible
            st.push(curr);
            curr = curr->left;
        }
        curr = st.top(); st.pop();
        result.push_back(curr->val);
        curr = curr->right;      // now go right
    }
    return result;
}

// Iterative postorder — O(n) time, O(h) space
// Trick: reverse of (Root → Right → Left) = Left → Right → Root
vector<int> postorderIter(TreeNode* root) {
    vector<int> result;
    if (!root) return result;
    stack<TreeNode*> st;
    st.push(root);
    while (!st.empty()) {
        TreeNode* node = st.top(); st.pop();
        result.push_back(node->val);
        if (node->left)  st.push(node->left);   // left before right
        if (node->right) st.push(node->right);   // so right processed first
    }
    reverse(result.begin(), result.end());  // reverse gives postorder
    return result;
}
```

---

## When to Use Which

| Traversal | Use case                                              |
| --------- | ----------------------------------------------------- |
| Preorder  | Copy a tree, serialize a tree, prefix expression      |
| Inorder   | Get BST nodes in sorted order (very important)        |
| Postorder | Delete a tree, evaluate expression tree, bottom-up DP |

The inorder property for BSTs is critical — inorder traversal of a BST always gives nodes in sorted order. You will use this constantly in [[07_02_00_Binary_Search_Trees]].

---

## Complexity Summary

| |Time|Space (balanced)|Space (skewed)|
|---|---|---|---|
|Any DFS traversal|$O(n)$|$O(\log n)$|$O(n)$|

> 📖 For Morris traversal ($O(1)$ space inorder): [GeeksForGeeks — Morris Traversal](https://www.geeksforgeeks.org/inorder-tree-traversal-without-recursion-and-without-stack/)

---
