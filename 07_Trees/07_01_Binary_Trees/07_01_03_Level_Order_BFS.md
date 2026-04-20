
---

# Level Order Traversal — BFS

The three DFS traversals (inorder, preorder, postorder) all go deep before going wide. **Level order traversal** does the opposite — it visits all nodes at depth 0 first, then depth 1, then depth 2, and so on. This is exactly BFS applied to a tree.

> **Definition.** _Level order traversal_ visits nodes level by level, from left to right within each level, using a queue to maintain the order.

For the tree:

```
        1
       / \
      2   3
     / \   \
    4   5   6
```

Level order: 1, 2, 3, 4, 5, 6

---

## Basic Level Order

```cpp
#include <iostream>
#include <queue>
#include <vector>
using namespace std;

// Time: O(n), Space: O(w) where w = max width of tree
void levelOrder(TreeNode* root) {
    if (!root) return;
    queue<TreeNode*> q;
    q.push(root);
    while (!q.empty()) {
        TreeNode* node = q.front(); q.pop();
        cout << node->val << " ";
        if (node->left)  q.push(node->left);
        if (node->right) q.push(node->right);
    }
}
```

---

## Level-by-Level Separation

Often we need the result grouped by level — each level as its own list. The trick is to snapshot the queue size at the start of each level.

```cpp
// Returns list of levels — Time: O(n), Space: O(w)
vector<vector<int>> levelOrderGrouped(TreeNode* root) {
    vector<vector<int>> result;
    if (!root) return result;
    queue<TreeNode*> q;
    q.push(root);

    while (!q.empty()) {
        int levelSize = q.size();  // snapshot — all nodes at current level
        vector<int> level;

        for (int i = 0; i < levelSize; i++) {
            TreeNode* node = q.front(); q.pop();
            level.push_back(node->val);
            if (node->left)  q.push(node->left);
            if (node->right) q.push(node->right);
        }
        result.push_back(level);
    }
    return result;
}
```

---

## Applications of Level Order

**Zigzag level order** — alternate left-to-right and right-to-left per level. Use a deque and a direction flag.

**Right side view** — the last node in each level is visible from the right side.

**Maximum width** — the level with the most nodes.

**Connect level order siblings** — link each node to its next node at the same level.

```cpp
// Right side view — last node of each level — O(n)
vector<int> rightSideView(TreeNode* root) {
    vector<int> result;
    if (!root) return result;
    queue<TreeNode*> q;
    q.push(root);
    while (!q.empty()) {
        int sz = q.size();
        for (int i = 0; i < sz; i++) {
            TreeNode* node = q.front(); q.pop();
            if (i == sz - 1) result.push_back(node->val);  // last in level
            if (node->left)  q.push(node->left);
            if (node->right) q.push(node->right);
        }
    }
    return result;
}

// Maximum width — O(n)
int maxWidth(TreeNode* root) {
    if (!root) return 0;
    int maxW = 0;
    queue<TreeNode*> q;
    q.push(root);
    while (!q.empty()) {
        maxW = max(maxW, (int)q.size());
        int sz = q.size();
        for (int i = 0; i < sz; i++) {
            TreeNode* node = q.front(); q.pop();
            if (node->left)  q.push(node->left);
            if (node->right) q.push(node->right);
        }
    }
    return maxW;
}
```

---

## DFS vs BFS on Trees

||DFS (pre/in/post)|BFS (level order)|
|---|---|---|
|Data structure|Stack (implicit or explicit)|Queue|
|Space|$O(h)$ — tree height|$O(w)$ — tree width|
|Best for|Tree structure problems, path problems|Level-based problems, shortest path|
|Worst space|$O(n)$ skewed tree|$O(n)$ perfect tree (last level has $n/2$ nodes)|

> 📖 For BFS on general graphs: [[10_02_01_BFS_and_Connected_Components]]

---
