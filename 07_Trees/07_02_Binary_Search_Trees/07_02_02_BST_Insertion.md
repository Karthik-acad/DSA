
---

# BST Insertion

Inserting into a BST is straightforward — follow the BST property to find where the new key belongs and place it there. The new node always becomes a **leaf**.

> **Rule.** To insert key $k$: start at the root. If $k <$ current node go left, if $k >$ current node go right. When you reach a null pointer, insert there.

```cpp
#include <iostream>
using namespace std;

// Recursive insert — returns new root
// Time: O(h), Space: O(h) for call stack
TreeNode* insert(TreeNode* root, int val) {
    if (!root) return new TreeNode(val);  // found the spot
    if (val < root->val)
        root->left = insert(root->left, val);
    else if (val > root->val)
        root->right = insert(root->right, val);
    // val == root->val — duplicate, ignore (or handle as needed)
    return root;
}

// Iterative insert — O(h) time, O(1) space
TreeNode* insertIter(TreeNode* root, int val) {
    TreeNode* newNode = new TreeNode(val);
    if (!root) return newNode;

    TreeNode* curr = root;
    TreeNode* parent = nullptr;

    while (curr) {
        parent = curr;
        if (val < curr->val) curr = curr->left;
        else if (val > curr->val) curr = curr->right;
        else return root;  // duplicate
    }

    if (val < parent->val) parent->left = newNode;
    else parent->right = newNode;
    return root;
}

int main() {
    TreeNode* root = nullptr;
    for (int x : {8, 3, 10, 1, 6, 14, 4, 7, 13})
        root = insert(root, x);
    // Inorder should print: 1 3 4 6 7 8 10 13 14
    return 0;
}
```

---

## Effect of Insertion Order on Tree Shape

|Insertion order|Tree shape|Height|
|---|---|---|
|Random|Roughly balanced|$O(\log n)$ expected|
|Sorted ascending|Right-skewed chain|$O(n)$|
|Sorted descending|Left-skewed chain|$O(n)$|
|Median first|Balanced|$O(\log n)$|

This sensitivity to insertion order is the fundamental weakness of plain BSTs — which balanced BSTs fix.

**Complexity:** $O(h)$ time where $h$ is tree height. $O(\log n)$ average for random data, $O(n)$ worst case.

---
