
---

# Views of a Tree

A **view** of a binary tree is the set of nodes visible when the tree is observed from a particular direction. These are classic interview problems that test your understanding of traversals.

---

## Left View

Nodes visible from the left — the first node at each level.

```cpp
// Time: O(n), Space: O(h)
vector<int> leftView(TreeNode* root) {
    vector<int> result;
    if (!root) return result;
    queue<TreeNode*> q;
    q.push(root);
    while (!q.empty()) {
        int sz = q.size();
        for (int i = 0; i < sz; i++) {
            TreeNode* node = q.front(); q.pop();
            if (i == 0) result.push_back(node->val);  // first in level
            if (node->left)  q.push(node->left);
            if (node->right) q.push(node->right);
        }
    }
    return result;
}
```

---

## Right View

Nodes visible from the right — the last node at each level. Already shown in [[07_01_03_Level_Order_BFS]], included here for completeness.

```cpp
// Time: O(n), Space: O(h)
vector<int> rightView(TreeNode* root) {
    vector<int> result;
    if (!root) return result;
    queue<TreeNode*> q;
    q.push(root);
    while (!q.empty()) {
        int sz = q.size();
        for (int i = 0; i < sz; i++) {
            TreeNode* node = q.front(); q.pop();
            if (i == sz-1) result.push_back(node->val);  // last in level
            if (node->left)  q.push(node->left);
            if (node->right) q.push(node->right);
        }
    }
    return result;
}
```

---

## Top View

Nodes visible from above — for each horizontal distance from the root, the topmost node at that distance.

We assign a horizontal distance (HD) to each node: root = 0, left child = HD-1, right child = HD+1. For each HD, keep the node at the minimum depth.

```cpp
#include <map>

// Time: O(n log n), Space: O(n)
vector<int> topView(TreeNode* root) {
    if (!root) return {};
    map<int, int> hdToVal;  // HD → value of topmost node
    queue<pair<TreeNode*, int>> q;  // node, HD
    q.push({root, 0});

    while (!q.empty()) {
        auto [node, hd] = q.front(); q.pop();
        if (!hdToVal.count(hd))   // first time seeing this HD = topmost
            hdToVal[hd] = node->val;
        if (node->left)  q.push({node->left,  hd-1});
        if (node->right) q.push({node->right, hd+1});
    }

    vector<int> result;
    for (auto& [hd, val] : hdToVal) result.push_back(val);
    return result;
}
```

---

## Bottom View

Nodes visible from below — for each horizontal distance, the **bottommost** node. Same as top view but overwrite instead of skip.

```cpp
// Time: O(n log n), Space: O(n)
vector<int> bottomView(TreeNode* root) {
    if (!root) return {};
    map<int, int> hdToVal;
    queue<pair<TreeNode*, int>> q;
    q.push({root, 0});

    while (!q.empty()) {
        auto [node, hd] = q.front(); q.pop();
        hdToVal[hd] = node->val;  // always overwrite — last = bottommost
        if (node->left)  q.push({node->left,  hd-1});
        if (node->right) q.push({node->right, hd+1});
    }

    vector<int> result;
    for (auto& [hd, val] : hdToVal) result.push_back(val);
    return result;
}
```

---

## Complexity Summary

|View|Time|Space|
|---|---|---|
|Left / Right|$O(n)$|$O(w)$|
|Top / Bottom|$O(n \log n)$|$O(n)$|

Top and bottom views use $O(n \log n)$ because of the ordered map — using `unordered_map` for the HD mapping would give $O(n)$ but then you need to sort the HDs at the end anyway.

---
