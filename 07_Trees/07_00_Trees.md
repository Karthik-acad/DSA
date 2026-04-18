This is the biggest chapter. Starting with Binary Trees.

Here is `07_01_01_Tree_Basics.md`:

---

# Tree Basics

From our study of linked lists, we know nodes connected by pointers in a linear chain. A **tree** generalizes this — instead of each node pointing to just one next node, each node can point to multiple children. The result is a hierarchical structure rather than a linear one.

> **Definition.** A _tree_ is a connected, acyclic undirected graph. In the rooted version — which is what we almost always mean in DSA — one node is designated the **root**, and every other node has exactly one parent. Nodes with no children are called **leaves**.

Think of a company org chart. The CEO is at the top (root). Each manager has a set of direct reports (children). Every employee except the CEO has exactly one boss (parent). No cycles — you cannot be your own ancestor.

---

## Terminology

> **Definition.** The **degree** of a node is the number of its children.

> **Definition.** The **depth** of a node is the number of edges from the root to that node. The root has depth 0.

> **Definition.** The **height** of a node is the number of edges on the longest path from that node to a leaf. A leaf has height 0.

> **Definition.** The **height of a tree** is the height of its root — the longest path from root to any leaf.

> **Definition.** A **subtree** rooted at node $v$ consists of $v$ and all its descendants.

> **Definition.** An **ancestor** of node $v$ is any node on the path from the root to $v$. A **descendant** of $v$ is any node reachable from $v$ going downward.

```
            1          ← root, depth 0, height 3
          /   \
        2       3      ← depth 1
       / \     / \
      4   5   6   7    ← depth 2
     / \
    8   9              ← depth 3, leaves
```

In this tree:

- Height = 3 (longest path root → 1 → 2 → 4 → 8)
- Node 2 has degree 2, height 2, depth 1
- Nodes 5, 6, 7, 8, 9 are leaves
- Subtree rooted at 2 contains {2, 4, 5, 8, 9}

---

## Binary Trees

The most important special case — a tree where every node has **at most two children**, called left and right.

> **Definition.** A _binary tree_ is a tree where each node has at most two children — a left child and a right child.

```cpp
#include <iostream>
using namespace std;

struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};
```

---

## Special Types of Binary Trees

> **Definition.** A _full binary tree_ is one where every node has either 0 or 2 children — no node has exactly 1 child.

> **Definition.** A _complete binary tree_ is one where all levels are completely filled except possibly the last, which is filled from left to right. (This is what heaps use.)

> **Definition.** A _perfect binary tree_ is one where all internal nodes have exactly 2 children and all leaves are at the same depth.

> **Definition.** A _balanced binary tree_ is one where for every node, the heights of its left and right subtrees differ by at most 1.

|Type|Property|Example|
|---|---|---|
|Full|Every node has 0 or 2 children|Expression trees|
|Complete|Last level filled left to right|Binary heap|
|Perfect|All leaves at same depth|—|
|Balanced|Height difference $\leq 1$ at every node|AVL tree|

---

## Key Formulas for Binary Trees

For a perfect binary tree with $n$ nodes and height $h$:

$$n = 2^{h+1} - 1$$ $$h = \lfloor \log_2 n \rfloor$$

Number of leaves in a perfect binary tree:

$$\text{leaves} = 2^h = \lceil n/2 \rceil$$

For a complete binary tree stored as an array (0-indexed):

$$\text{parent}(i) = \lfloor (i-1)/2 \rfloor$$ $$\text{left}(i) = 2i+1, \quad \text{right}(i) = 2i+2$$

These are exactly the heap formulas from [[05_02_Binary_Heaps_Min_Max]] — a heap is just a complete binary tree stored as an array.

---

## Node Count Properties

> **Theorem.** In any binary tree, the number of leaves $L$ and the number of internal nodes with 2 children $I_2$ satisfy: $$L = I_2 + 1$$

This is a useful sanity check when working with binary trees by hand.

> 📖 For tree theory foundations: [MIT 6.006 Lecture Notes — Trees](https://ocw.mit.edu/courses/6-006-introduction-to-algorithms-spring-2020/pages/lecture-notes/)

---

Here is `07_01_02_Traversals_Inorder_Preorder_Postorder.md`:

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

|Traversal|Use case|
|---|---|
|Preorder|Copy a tree, serialize a tree, prefix expression|
|Inorder|Get BST nodes in sorted order (very important)|
|Postorder|Delete a tree, evaluate expression tree, bottom-up DP|

The inorder property for BSTs is critical — inorder traversal of a BST always gives nodes in sorted order. You will use this constantly in [[07_02_00_Binary_Search_Trees]].

---

## Complexity Summary

||Time|Space (balanced)|Space (skewed)|
|---|---|---|---|
|Any DFS traversal|$O(n)$|$O(\log n)$|$O(n)$|

> 📖 For Morris traversal ($O(1)$ space inorder): [GeeksForGeeks — Morris Traversal](https://www.geeksforgeeks.org/inorder-tree-traversal-without-recursion-and-without-stack/)

---

Here is `07_01_03_Level_Order_BFS.md`:

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

Here is `07_01_04_Height_and_Diameter.md`:

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

Here is `07_01_05_Views_of_Tree.md`:

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

Now BSTs. Here is `07_02_01_BST_Introduction.md`:

---

# BST Introduction

A plain binary tree has no rules about where values go — the left child could be larger or smaller than the parent. A **Binary Search Tree** adds one rule that unlocks efficient search.

> **Definition.** A _Binary Search Tree_ is a binary tree where for every node $v$:
> 
> - All values in $v$'s **left subtree** are **strictly less** than $v$'s value
> - All values in $v$'s **right subtree** are **strictly greater** than $v$'s value
> - Both subtrees are themselves BSTs

This single invariant is what gives BSTs their power. To search for a value, you compare with the current node and go left or right — eliminating half the remaining tree at each step. It is binary search applied to a tree structure.

```
        8
       / \
      3   10
     / \    \
    1   6    14
       / \   /
      4   7 13
```

This is a valid BST. Verify: every value in the left subtree of 8 is less than 8, every value in the right subtree is greater.

---

## The BST Property and Inorder Traversal

> **Theorem.** Inorder traversal of a BST always yields values in **sorted ascending order**.

For the tree above: 1, 3, 4, 6, 7, 8, 10, 13, 14 — perfectly sorted. This is not a coincidence — it follows directly from the BST property. The left subtree contains all smaller elements, so they are visited first, then the current node, then the larger right subtree.

This property is used constantly — it converts a BST into a sorted sequence in $O(n)$.

---

## Average vs Worst Case

The efficiency of BST operations depends entirely on the **height** of the tree:

|Tree shape|Height|Search / Insert / Delete|
|---|---|---|
|Balanced|$O(\log n)$|$O(\log n)$|
|Completely skewed|$O(n)$|$O(n)$|

A skewed BST (inserting sorted data into a plain BST) degenerates into a linked list:

```
1
 \
  2
   \
    3
     \
      4
```

This is why balanced BSTs (AVL, Red-Black) exist — they maintain $O(\log n)$ height by rebalancing on insert and delete.

---

## C++ STL

`std::set` and `std::map` are both backed by red-black trees — balanced BSTs. They guarantee $O(\log n)$ for all operations and maintain sorted order.

```cpp
#include <set>
#include <map>
using namespace std;

set<int> s;
s.insert(5); s.insert(3); s.insert(7);
for (int x : s) cout << x << " ";  // 3 5 7 — sorted

map<string, int> m;
m["b"] = 2; m["a"] = 1;
for (auto& [k,v] : m) cout << k << " ";  // a b — sorted by key
```

> 📖 For BST theory: [MIT 6.006 Lecture Notes — BSTs](https://ocw.mit.edu/courses/6-006-introduction-to-algorithms-spring-2020/pages/lecture-notes/)

---

Here is `07_02_02_BST_Insertion.md`:

---

# BST Insertion

Inserting into a BST is straightforward — follow the BST property to find where the new key belongs and place it there. The new node always becomes a **leaf**.

> **Rule.** To insert key $k$: start at the root. If $k < $ current node go left, if $k > $ current node go right. When you reach a null pointer, insert there.

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

Here is `07_02_03_BST_Deletion.md`:

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

Now Balanced BSTs. Here is `07_03_01_2_4_Trees.md`:

---

# 2-4 Trees

Plain BSTs degenerate to $O(n)$ when data is inserted in sorted order. **2-4 trees** (also called 2-3-4 trees) are a type of balanced search tree that guarantees $O(\log n)$ height by allowing nodes to hold multiple keys.

> **Definition.** A _2-4 tree_ is a balanced search tree where every node has between 2 and 4 children (and between 1 and 3 keys), and all leaves are at the same depth.

The three types of nodes:

- **2-node:** 1 key, 2 children — like a BST node
- **3-node:** 2 keys, 3 children
- **4-node:** 3 keys, 4 children

```
    [10 | 20]          ← 3-node: keys 10 and 20, three children
    /    |    \
  [5]  [15]  [25 | 30]
```

For a 3-node with keys $[a, b]$: left child has values $< a$, middle has $a < x < b$, right has values $> b$.

---

## Guaranteed Balance

> **Theorem.** A 2-4 tree with $n$ keys has height exactly $\Theta(\log n)$.

**Proof sketch:** Every path from root to leaf has the same length (all leaves at same depth). The minimum branching factor is 2, so $n \geq 2^h$ → $h \leq \log n$. The maximum branching factor is 4, so $n \leq 4^h$ → $h \geq \log_4 n$. Both bounds give $h = \Theta(\log n)$.

---

## Insertion — Split on the Way Down

When inserting, if a 4-node is encountered, **split** it — promote its middle key to the parent and create two 2-nodes from the sides.

```
Insert 15 into:
    [10 | 20 | 30]    ← 4-node, split first
         ↓
    [20]              ← promote middle
   /    \
 [10]  [30]
         
Now insert 15 → goes right of 10, left of 20:
    [20]
   /    \
 [10|15]  [30]
```

Splitting guarantees the parent always has room — since we split 4-nodes on the way down, by the time we reach the insertion point the parent is never a 4-node.

**Time:** $O(\log n)$ — at most $O(\log n)$ splits, each $O(1)$.

---

## Why Study 2-4 Trees?

2-4 trees are the conceptual foundation of **Red-Black trees**. Every 2-4 tree has a direct correspondence to a red-black tree — 2-nodes become black nodes, 3-nodes and 4-nodes become black nodes with red children. Understanding 2-4 trees makes red-black tree operations intuitive rather than mysterious.

|2-4 Tree operation|Red-Black tree equivalent|
|---|---|
|Split a 4-node|Color flip|
|Merge nodes|Rotation|
|All leaves same depth|Black-height property|

> 📖 Original paper: [Symmetric Binary B-Trees — Bayer, 1972](https://link.springer.com/article/10.1007/BF00289496) 📖 For 2-4 to RB correspondence: [CLRS Chapter 13](https://mitpress.mit.edu/9780262046305/introduction-to-algorithms/)

---

Here is `07_03_02_Red_Black_Trees.md`:

---

# Red-Black Trees

Red-Black trees are the most widely used balanced BST in practice. They are what powers `std::map`, `std::set`, Java's `TreeMap`, and many other standard library implementations. Where AVL trees optimize for lookup speed, red-black trees optimize for insertion and deletion speed.

> **Definition.** A _Red-Black tree_ is a BST where each node is colored either red or black, satisfying the following properties:
> 
> 1. Every node is red or black
> 2. The root is black
> 3. Every leaf (null pointer) is black
> 4. If a node is red, both its children are black (**no two consecutive reds**)
> 5. For any node, all paths from that node to its descendant null leaves contain the **same number of black nodes** (black-height)

These five properties together guarantee that the longest path (alternating red-black) is at most twice the shortest path (all black) — ensuring $O(\log n)$ height.

$$h \leq 2\log_2(n+1)$$

---

## Red-Black vs AVL

||Red-Black|AVL|
|---|---|---|
|Height bound|$\leq 2\log_2(n+1)$|$\leq 1.44\log_2 n$|
|Lookup|Slightly slower (more height)|Faster|
|Insert/Delete|Faster (fewer rotations)|Slower (more rotations)|
|Rebalancing|At most 3 rotations|$O(\log n)$ rotations|
|Use case|Frequent inserts/deletes|Frequent lookups|

---

## Insertion

New nodes are always inserted as **red** (adding a red node violates fewer properties than black — it only potentially breaks property 4). Then we fix violations through rotations and recoloring.

There are 6 cases (symmetric pairs of 3):

**Case 1 — Uncle is red:** Recolor parent and uncle black, grandparent red. Move violation up.

**Case 2 — Uncle is black, triangle:** Rotate parent to make it a line case.

**Case 3 — Uncle is black, line:** Rotate grandparent, swap colors of parent and grandparent.

```cpp
// Full Red-Black tree implementation is lengthy.
// In practice always use std::map or std::set.
// The key insight for exams:

// Insertion: O(log n), at most 2 rotations
// Deletion: O(log n), at most 3 rotations
// All operations: O(log n) guaranteed

#include <map>
#include <set>
using namespace std;

int main() {
    // Red-Black tree under the hood
    map<int, string> rb;
    rb[3] = "three";
    rb[1] = "one";
    rb[4] = "four";

    // O(log n) guaranteed for all operations
    rb.insert({2, "two"});
    rb.erase(3);
    auto it = rb.find(1);

    for (auto& [k,v] : rb)
        cout << k << ": " << v << endl;  // sorted order
    return 0;
}
```

---

## Black-Height and the Height Bound

> **Definition.** The _black-height_ $bh(v)$ of a node $v$ is the number of black nodes on any path from $v$ (exclusive) to a leaf.

By property 5, black-height is well-defined — all paths have the same number of black nodes. The height bound follows:

$$\text{Minimum nodes at black-height } k = 2^k - 1 \text{ (all black, perfect tree)}$$ $$n \geq 2^{bh} - 1 \implies bh \leq \log_2(n+1)$$ $$h \leq 2 \cdot bh \leq 2\log_2(n+1)$$

> 📖 For full insertion/deletion cases: [CLRS Chapter 13](https://mitpress.mit.edu/9780262046305/introduction-to-algorithms/)

---

Now AVL Trees. Here is `07_03_03_01_AVL_Introduction.md`:

---

# AVL Introduction

AVL trees were the first self-balancing BST ever invented, proposed by Adelson-Velsky and Landis in 1962. Where red-black trees allow the longest path to be twice the shortest, AVL trees enforce a stricter balance — height difference of at most 1 at every node.

> **Definition.** An _AVL tree_ is a BST where for every node, the heights of its left and right subtrees differ by at most 1. This difference is called the **balance factor**.

$$\text{balance factor}(v) = h(\text{left subtree}) - h(\text{right subtree})$$

For a valid AVL tree, $\text{balance factor}(v) \in {-1, 0, 1}$ for all nodes $v$.

---

## Height Guarantee

> **Theorem.** An AVL tree with $n$ nodes has height $h \leq 1.44 \log_2 n$.

**Proof sketch:** Let $N(h)$ be the minimum number of nodes in an AVL tree of height $h$. The worst case AVL tree has one subtree of height $h-1$ and one of height $h-2$:

$$N(h) = N(h-1) + N(h-2) + 1$$

This recurrence is similar to Fibonacci — $N(h) \approx \phi^h / \sqrt{5}$ where $\phi = 1.618$. Inverting: $h \leq 1.44 \log_2 n$.

---

## Node Structure

```cpp
struct AVLNode {
    int val;
    AVLNode* left;
    AVLNode* right;
    int height;  // extra field compared to plain BST
    AVLNode(int x) : val(x), left(nullptr), right(nullptr), height(0) {}
};

int getHeight(AVLNode* node) {
    return node ? node->height : -1;
}

int getBalance(AVLNode* node) {
    return node ? getHeight(node->left) - getHeight(node->right) : 0;
}

void updateHeight(AVLNode* node) {
    node->height = 1 + max(getHeight(node->left), getHeight(node->right));
}
```

---

## When Rebalancing is Needed

After any insertion or deletion, we walk back up the tree updating heights. If any node has $|\text{balance factor}| > 1$, we apply a **rotation** to restore balance. There are four cases — LL, RR, LR, RL — covered in [[07_03_03_02_Rotations_LL_RR_LR_RL]].

|Imbalance type|Balance factor|Child balance factor|Fix|
|---|---|---|---|
|LL|$+2$|$\geq 0$|Single right rotation|
|RR|$-2$|$\leq 0$|Single left rotation|
|LR|$+2$|$< 0$|Left rotate child, then right rotate|
|RL|$-2$|$> 0$|Right rotate child, then left rotate|

---

## AVL vs Plain BST

||Plain BST|AVL Tree|
|---|---|---|
|Height|$O(n)$ worst|$O(\log n)$ guaranteed|
|Search|$O(n)$ worst|$O(\log n)$|
|Insert|$O(n)$ worst|$O(\log n)$|
|Delete|$O(n)$ worst|$O(\log n)$|
|Extra space|None|Height field per node|

> 📖 Original paper: [An Algorithm for the Organization of Information — Adelson-Velsky and Landis, 1962](https://zhjwpku.com/assets/pdf/AED2-10-avl-paper.pdf)

---

Here is `07_03_03_02_Rotations_LL_RR_LR_RL.md`:

---

# Rotations — LL, RR, LR, RL

Rotations are the fundamental operation used to restore AVL balance after insertion or deletion. A rotation restructures a subtree while preserving the BST property.

> **Definition.** A _rotation_ is a local restructuring of three nodes that changes the tree's shape while maintaining the inorder traversal order (BST property).

---

## Right Rotation (fixes LL imbalance)

**LL imbalance** — a node $z$ has balance factor $+2$ and its left child $y$ has balance factor $\geq 0$ — the imbalance is in the Left-Left direction.

```
        z  (+2)                y
       / \                    / \
      y  (+1) T4    →        x   z
     / \                    / \ / \
    x   T3                 T1 T2 T3 T4
   / \
  T1  T2
```

```cpp
AVLNode* rightRotate(AVLNode* z) {
    AVLNode* y = z->left;
    AVLNode* T3 = y->right;

    // Perform rotation
    y->right = z;
    z->left = T3;

    // Update heights — z first since it is now lower
    updateHeight(z);
    updateHeight(y);

    return y;  // y is new root of this subtree
}
```

---

## Left Rotation (fixes RR imbalance)

**RR imbalance** — balance factor $-2$, right child balance $\leq 0$.

```
    z  (-2)                    y
   / \                        / \
  T1   y  (-1)    →          z   x
      / \                   / \ / \
     T2   x               T1 T2 T3 T4
         / \
        T3  T4
```

```cpp
AVLNode* leftRotate(AVLNode* z) {
    AVLNode* y = z->right;
    AVLNode* T2 = y->left;

    y->left = z;
    z->right = T2;

    updateHeight(z);
    updateHeight(y);

    return y;
}
```

---

## Left-Right Rotation (fixes LR imbalance)

**LR imbalance** — balance factor $+2$, left child balance $-1$. Left-Right means the imbalance is at the left child's right.

Fix: left rotate the left child first (converts to LL), then right rotate the root.

```
    z (+2)          z (+2)          x
   / \              / \            / \
  y  (-1) T4  →  x   T4   →     y   z
 / \             / \            / \ / \
T1  x           y  T3          T1 T2 T3 T4
   / \         / \
  T2  T3      T1  T2
```

```cpp
AVLNode* leftRightRotate(AVLNode* z) {
    z->left = leftRotate(z->left);   // left rotate left child
    return rightRotate(z);            // right rotate root
}
```

---

## Right-Left Rotation (fixes RL imbalance)

**RL imbalance** — balance factor $-2$, right child balance $+1$.

Fix: right rotate the right child first (converts to RR), then left rotate the root.

```cpp
AVLNode* rightLeftRotate(AVLNode* z) {
    z->right = rightRotate(z->right);  // right rotate right child
    return leftRotate(z);               // left rotate root
}
```

---

## Full AVL Insert

```cpp
AVLNode* insert(AVLNode* root, int val) {
    // Standard BST insert
    if (!root) return new AVLNode(val);
    if (val < root->val) root->left  = insert(root->left,  val);
    else if (val > root->val) root->right = insert(root->right, val);
    else return root;  // duplicate

    // Update height
    updateHeight(root);

    // Check balance and fix
    int balance = getBalance(root);

    // LL
    if (balance > 1 && getBalance(root->left) >= 0)
        return rightRotate(root);
    // RR
    if (balance < -1 && getBalance(root->right) <= 0)
        return leftRotate(root);
    // LR
    if (balance > 1 && getBalance(root->left) < 0)
        return leftRightRotate(root);
    // RL
    if (balance < -1 && getBalance(root->right) > 0)
        return rightLeftRotate(root);

    return root;
}

int main() {
    AVLNode* root = nullptr;
    for (int x : {10, 20, 30, 40, 50, 25})
        root = insert(root, x);
    // Tree stays balanced despite sorted-ish input
    return 0;
}
```

---

## Rotation Summary

|Imbalance|Condition|Fix|Rotations|
|---|---|---|---|
|LL|$bf = +2$, left child $bf \geq 0$|Right rotate|1|
|RR|$bf = -2$, right child $bf \leq 0$|Left rotate|1|
|LR|$bf = +2$, left child $bf < 0$|Left-Right rotate|2|
|RL|$bf = -2$, right child $bf > 0$|Right-Left rotate|2|

Each rotation is $O(1)$ — just pointer reassignments. After insertion, at most $O(\log n)$ rotations may be needed walking back up. After deletion, similarly $O(\log n)$.

---

Now Tries. Here is `07_04_01_Trie_Introduction.md`:

---

# Trie Introduction

Imagine storing a dictionary of words and wanting to answer questions like "does this word exist?", "how many words start with this prefix?", "what words can complete this prefix?". A hash table can answer the first in $O(L)$ where $L$ is word length, but struggles with prefix queries. A **trie** handles all of these naturally.

> **Definition.** A _trie_ (pronounced "try", from re**trie**val) is a tree where each node represents a character, and each path from the root to a marked node represents a stored string. All strings sharing a common prefix share a common path from the root.

Think of it like a file system — directories represent prefixes, and files represent complete words. All files in the same directory share the same path prefix.

```
Words: ["cat", "can", "car", "dog"]

        root
       /    \
      c      d
      |      |
      a      o
    / | \    |
   t  n  r   g
```

Each node stores one character. The root represents the empty string. A word is stored by following its characters from root to a marked endpoint.

---

## Node Structure

```cpp
#include <iostream>
#include <unordered_map>
using namespace std;

struct TrieNode {
    unordered_map<char, TrieNode*> children;
    bool isEnd = false;  // marks end of a complete word
    int count = 0;       // optional: number of words passing through this node
};
```

Using `unordered_map` instead of a fixed array of 26 saves space for sparse alphabets. For pure lowercase English, a `TrieNode* children[26]` array is faster.

---

## Key Properties

- **Prefix sharing** — common prefixes stored once, not duplicated
- **Search time** — $O(L)$ for a word of length $L$, independent of number of words stored
- **Space** — $O(\text{total characters across all words})$ in the worst case (no shared prefixes)
- **Ordered** — children can be stored in sorted order for lexicographic traversal

> 📖 For trie applications in autocomplete systems: [Engineering Search at Scale — Google](https://research.google/pubs/pub37043/)

---

Here is `07_04_02_Insert_Search_Delete.md`:

---

# Trie — Insert, Search, Delete

```cpp
#include <iostream>
#include <string>
using namespace std;

class Trie {
    TrieNode* root;

public:
    Trie() : root(new TrieNode()) {}

    // Insert a word — O(L) where L = word length
    void insert(const string& word) {
        TrieNode* curr = root;
        for (char c : word) {
            if (!curr->children.count(c))
                curr->children[c] = new TrieNode();
            curr = curr->children[c];
            curr->count++;  // increment prefix count
        }
        curr->isEnd = true;
    }

    // Search for exact word — O(L)
    bool search(const string& word) {
        TrieNode* curr = root;
        for (char c : word) {
            if (!curr->children.count(c)) return false;
            curr = curr->children[c];
        }
        return curr->isEnd;
    }

    // Check if any word starts with prefix — O(L)
    bool startsWith(const string& prefix) {
        TrieNode* curr = root;
        for (char c : prefix) {
            if (!curr->children.count(c)) return false;
            curr = curr->children[c];
        }
        return true;
    }

    // Count words with given prefix — O(L)
    int countPrefix(const string& prefix) {
        TrieNode* curr = root;
        for (char c : prefix) {
            if (!curr->children.count(c)) return 0;
            curr = curr->children[c];
        }
        return curr->count;
    }

    // Delete a word — O(L)
    bool deleteWord(const string& word) {
        return deleteHelper(root, word, 0);
    }

private:
    bool deleteHelper(TrieNode* curr, const string& word, int idx) {
        if (idx == word.size()) {
            if (!curr->isEnd) return false;  // word not found
            curr->isEnd = false;
            return curr->children.empty();   // true if node can be deleted
        }
        char c = word[idx];
        if (!curr->children.count(c)) return false;
        bool shouldDelete = deleteHelper(curr->children[c], word, idx+1);
        if (shouldDelete) {
            curr->children[c]->count--;
            delete curr->children[c];
            curr->children.erase(c);
            return !curr->isEnd && curr->children.empty();
        }
        curr->children[c]->count--;
        return false;
    }
};

int main() {
    Trie t;
    t.insert("cat");
    t.insert("can");
    t.insert("car");
    t.insert("dog");

    cout << t.search("cat")      << endl;  // 1
    cout << t.search("ca")       << endl;  // 0 — prefix, not complete word
    cout << t.startsWith("ca")   << endl;  // 1
    cout << t.countPrefix("ca")  << endl;  // 3 (cat, can, car)

    t.deleteWord("cat");
    cout << t.search("cat")      << endl;  // 0
    cout << t.startsWith("ca")   << endl;  // 1 — can, car still there
    return 0;
}
```

---

## Complexity Summary

|Operation|Time|Space|
|---|---|---|
|Insert|$O(L)$|$O(L)$ new nodes|
|Search|$O(L)$|$O(1)$|
|Prefix check|$O(L)$|$O(1)$|
|Delete|$O(L)$|$O(1)$|
|Total space|—|$O(\sum L_i)$|

All operations are $O(L)$ — independent of how many words are stored. This is the trie's key advantage over hash maps for prefix queries.

---

Here is `07_04_03_Pattern_Matching_with_Tries.md`:

---

# Pattern Matching with Tries

Tries shine when you need to match multiple patterns against a text simultaneously. Instead of running each pattern separately, you build a trie of all patterns and traverse the text once.

> **Definition.** _Multi-pattern matching_ using a trie checks all patterns simultaneously by traversing the trie with each character of the text, finding all patterns that appear as substrings.

---

## Dictionary Matching

Given a set of pattern words and a text, find all occurrences of any pattern word starting at each position.

```cpp
// Naive: O(n * m * L) where n=text length, m=patterns, L=avg pattern length
// Trie: O(n * L_max + total pattern length)

#include <iostream>
#include <vector>
#include <string>
using namespace std;

// For each position in text, check if any pattern starts there
vector<pair<int,string>> findPatterns(const string& text,
                                       const vector<string>& patterns) {
    Trie trie;
    for (auto& p : patterns) trie.insert(p);

    vector<pair<int,string>> result;
    for (int i = 0; i < text.size(); i++) {
        // Try to match starting at position i
        TrieNode* curr = trie.root;  // need access to root
        for (int j = i; j < text.size(); j++) {
            char c = text[j];
            if (!curr->children.count(c)) break;
            curr = curr->children[c];
            if (curr->isEnd)
                result.push_back({i, text.substr(i, j-i+1)});
        }
    }
    return result;
}
```

For more advanced multi-pattern matching (finding all occurrences efficiently), the **Aho-Corasick algorithm** extends the trie with failure links — similar to KMP's failure function but for multiple patterns. It achieves $O(n + \sum L_i + \text{matches})$ total — linear in the text length regardless of the number of patterns. This is covered in [[11_02_KMP_Algorithm]] context.

---

## Longest Word in Dictionary Formed by Other Words

```cpp
// Find longest word that can be built one char at a time using other words
// Time: O(sum of word lengths), Space: O(sum of word lengths)
string longestWord(vector<string>& words) {
    Trie trie;
    for (auto& w : words) trie.insert(w);

    string result = "";
    // DFS through trie, only going through nodes that are word endings
    function<void(TrieNode*, string)> dfs = [&](TrieNode* node, string curr) {
        if (curr.size() > result.size()) result = curr;
        for (auto& [c, child] : node->children) {
            if (child->isEnd)
                dfs(child, curr + c);
        }
    };
    dfs(trie.root, "");  // need root access
    return result;
}
```

---

Now Segment Trees. Here is `07_05_01_Build.md`:

---

# Segment Tree — Build

From our study of prefix sums in [[01_02_Prefix_Sums]], we know that prefix sums allow $O(1)$ range queries but $O(n)$ updates. What if we need both fast queries and fast updates? A **segment tree** gives $O(\log n)$ for both.

> **Definition.** A _segment tree_ is a binary tree where each node stores information about a contiguous subarray (segment) of the original array. The root covers the entire array, and each node's children cover the left and right halves of its segment.

For array $[1, 3, 2, 7, 9, 11]$:

```
              [0,5] = 33
             /          \
        [0,2]=6         [3,5]=27
        /    \           /    \
    [0,1]=4  [2,2]=2  [3,4]=16  [5,5]=11
    /    \             /    \
[0,0]=1 [1,1]=3   [3,3]=7  [4,4]=9
```

Each node stores the sum of its segment. Leaf nodes store individual elements.

---

## Array Representation

Like heaps, segment trees are stored as arrays (1-indexed for clean formulas):

$$\text{left child}(i) = 2i$$ $$\text{right child}(i) = 2i+1$$ $$\text{parent}(i) = \lfloor i/2 \rfloor$$

For an array of size $n$, allocate $4n$ space for the segment tree array (safe upper bound).

```cpp
#include <iostream>
#include <vector>
using namespace std;

class SegmentTree {
    int n;
    vector<int> tree;  // 1-indexed, size 4n

    // Build — O(n)
    void build(vector<int>& arr, int node, int start, int end) {
        if (start == end) {
            tree[node] = arr[start];  // leaf
        } else {
            int mid = (start + end) / 2;
            build(arr, 2*node,   start, mid);
            build(arr, 2*node+1, mid+1, end);
            tree[node] = tree[2*node] + tree[2*node+1];  // internal node = sum of children
        }
    }

public:
    SegmentTree(vector<int>& arr) {
        n = arr.size();
        tree.resize(4 * n);
        build(arr, 1, 0, n-1);
    }
```

---

## Why 4n Space?

For $n$ elements, the segment tree has at most $2 \cdot 2^{\lceil \log_2 n \rceil} - 1$ nodes — at most $4n$ for the next power of 2. Allocating $4n$ is a safe upper bound.

**Build complexity:** $O(n)$ — each of the $2n-1$ nodes is filled exactly once.

---

Here is `07_05_02_Range_Query.md`:

---

# Segment Tree — Range Query

With the tree built, a range query $[l, r]$ traverses the tree — if a node's segment is fully within $[l, r]$, return its value. If fully outside, return the identity element. If partial overlap, recurse on both children.

> **Definition.** A _range query_ on a segment tree runs in $O(\log n)$ by visiting at most $4\log n$ nodes — each level contributes at most 4 nodes to the answer.

```cpp
    // Range sum query [l, r] — O(log n)
    int query(int node, int start, int end, int l, int r) {
        if (r < start || end < l)
            return 0;  // segment completely outside query range — identity for sum
        if (l <= start && end <= r)
            return tree[node];  // segment completely inside query range
        // Partial overlap — recurse on both halves
        int mid = (start + end) / 2;
        int leftSum  = query(2*node,   start, mid,   l, r);
        int rightSum = query(2*node+1, mid+1, end,   l, r);
        return leftSum + rightSum;
    }

public:
    int query(int l, int r) {
        return query(1, 0, n-1, l, r);
    }
```

---

## Why O(log n)?

At any level of the tree, at most 4 nodes are partially overlapping with the query range (2 on the left boundary, 2 on the right). All other nodes are either fully inside (return immediately) or fully outside (return immediately). Since there are $O(\log n)$ levels, total nodes visited is $O(\log n)$.

---

Here is `07_05_03_Point_Update.md`:

---

# Segment Tree — Point Update

A point update changes one element of the array and propagates the change up the tree.

> **Definition.** A _point update_ changes the value at index $i$ and updates all ancestor nodes in $O(\log n)$ by recomputing their stored values bottom-up.

```cpp
    // Point update — set arr[idx] = val — O(log n)
    void update(int node, int start, int end, int idx, int val) {
        if (start == end) {
            tree[node] = val;  // leaf — update directly
        } else {
            int mid = (start + end) / 2;
            if (idx <= mid)
                update(2*node,   start, mid,   idx, val);
            else
                update(2*node+1, mid+1, end,   idx, val);
            tree[node] = tree[2*node] + tree[2*node+1];  // recompute internal node
        }
    }

public:
    void update(int idx, int val) {
        update(1, 0, n-1, idx, val);
    }
};

int main() {
    vector<int> arr = {1, 3, 2, 7, 9, 11};
    SegmentTree st(arr);

    cout << st.query(1, 4) << endl;  // 3+2+7+9 = 21
    cout << st.query(0, 5) << endl;  // 33

    st.update(3, 10);  // change arr[3] from 7 to 10
    cout << st.query(1, 4) << endl;  // 3+2+10+9 = 24
    cout << st.query(0, 5) << endl;  // 36
    return 0;
}
```

---

## Segment Tree vs Prefix Sum vs Fenwick Tree

||Prefix Sum|Segment Tree|Fenwick Tree|
|---|---|---|---|
|Build|$O(n)$|$O(n)$|$O(n \log n)$|
|Range query|$O(1)$|$O(\log n)$|$O(\log n)$|
|Point update|$O(n)$ rebuild|$O(\log n)$|$O(\log n)$|
|Space|$O(n)$|$O(4n)$|$O(n)$|
|Implementation|Simplest|Moderate|Simple|
|Range update|$O(n)$|$O(\log n)$*|$O(\log n)$*|

*with lazy propagation

Use prefix sums when the array is static. Use Fenwick tree when you need point updates and prefix queries (simpler code). Use segment tree when you need range updates, range queries, or more complex operations (min, max, GCD).

---

## Complexity Summary

|Operation|Time|Space|
|---|---|---|
|Build|$O(n)$|$O(4n)$|
|Range query|$O(\log n)$|$O(\log n)$ recursive stack|
|Point update|$O(\log n)$|$O(\log n)$ recursive stack|

---

Now the quiz. Here is `07_QQ_Trees_Quiz.md`:

---

## Quiz — Trees

**Q1 — Tree Properties** For the tree:

```
        10
       /  \
      5    15
     / \     \
    3   7     20
```

What is: height, number of leaves, depth of node 7, is it a valid BST?

> [!answer]- Answer Height = 2 (root to leaf 3, 7, or 20). Leaves = 3 (nodes 3, 7, 20). Depth of 7 = 2 (path: 10→5→7). Valid BST = yes. Verify: 5 < 10 and all left subtree values (3,5,7) < 10. 15 > 10 and all right subtree values (15,20) > 10. 3 < 5 < 7. 15 < 20.

---

**Q2 — Traversals** Give inorder, preorder, and postorder traversal of:

```
        1
       / \
      2   3
     /   / \
    4   5   6
```

> [!answer]- Answer Inorder (L-Root-R): 4, 2, 1, 5, 3, 6. Preorder (Root-L-R): 1, 2, 4, 3, 5, 6. Postorder (L-R-Root): 4, 2, 5, 6, 3, 1.

---

**Q3 — BST Operations** Insert 8, 3, 10, 1, 6, 14, 4, 7 into an empty BST. Draw the resulting tree. What does inorder traversal give?

> [!answer]- Answer Tree: 8 /  
> 3 10 / \  
> 1 6 14 /  
> 4 7 Inorder: 1, 3, 4, 6, 7, 8, 10, 14 — sorted. This confirms the BST property.

---

**Q4 — BST Deletion** Delete node 3 from the BST in Q3. Show the result.

> [!answer]- Answer Node 3 has two children (1 and 6). Find inorder successor = 4 (min of right subtree of 3). Copy 4 into node 3's position, delete 4 from right subtree (4 is a leaf). Result: 8 /  
> 4 10 / \  
> 1 6 14  
> 7

---

**Q5 — AVL Rotations** Insert 30, 20, 10 into an empty AVL tree. What rotation is performed and what does the tree look like after?

> [!answer]- Answer Insert 30: tree=[30]. Insert 20: tree=[30, 20, _]. Insert 10: balance at 30 = +2, left child balance = +1 → LL imbalance → right rotation. Before rotation: 30(+2) → 20(+1) → 10. After right rotation at 30: 20 /  
> 10 30 Balance factors: 10=0, 30=0, 20=0. Valid AVL tree.

---

**Q6 — Segment Tree** Array $[2, 4, 5, 7, 8, 9]$. Build a segment tree and answer: sum of [1,4], sum of [0,5]. Then update index 3 to value 1 and re-answer sum of [1,4].

> [!answer]- Answer Build: root covers [0,5]=35. Left [0,2]=11, right [3,5]=24. Leaves: 2,4,5,7,8,9. Query [1,4]: nodes covering [1,1]=4, [2,2]=5, [3,4]=15 → sum=24. Wait — [3,4]=7+8=15. Total=4+5+15=24. Query [0,5]: root = 35. Update index 3 to 1: change 7→1. Affected nodes: leaf [3,3], parent [3,4]=1+8=9, parent [3,5]=9+9=18, root [0,5]=11+18=29. New query [1,4]: 4+5+1+8=18.

---

**Q7 — Trie** Insert "app", "apple", "application", "apt" into a trie. Answer: does "app" exist as a complete word? How many words start with "ap"? What happens if you delete "app" — does "apple" still exist?

> [!answer]- Answer "app" exists = yes (isEnd=true at the 'p' node of app). Words starting with "ap" = 4 (all four words share prefix "ap"). After deleting "app": the node for the second 'p' in "app" has isEnd set to false, but the node is NOT deleted because it still has children ('l' for apple and application, 't' for... wait "apt" branches at 'p' → 't' not through "app"'s second p). "app" node still has children ('l' for apple/application) so the node stays. "apple" still exists — its path is root→a→p→p→l→e and that path is intact.

---

**Q8 — Red-Black Properties** Which of the following violate red-black tree properties? (R=red, B=black)

```
Tree A:        B(10)
              /     \
           R(5)     R(15)
           /
        R(3)

Tree B:        B(10)
              /     \
           R(5)     B(15)
          /   \
        B(3)  B(7)
```

> [!answer]- Answer Tree A violates property 4 — R(5) has a red child R(3), two consecutive reds. Invalid. Tree B is valid. Check all properties: root B(10) ✓. R(5)'s children B(3) and B(7) are both black ✓. B(15) is black with no children (null leaves) ✓. Black heights: path to any null leaf from root = 2 black nodes ✓. Valid red-black tree.

---

And finally the index files. Here is `07_00_Trees.md`:

---

# Trees

## Subfolders

- [[07_01_00_Binary_Trees]]
- [[07_02_00_Binary_Search_Trees]]
- [[07_03_00_Balanced_BSTs]]
- [[07_04_00_Tries]]
- [[07_05_00_Segment_Trees]]

## Quiz

- [[07_QQ_Trees_Quiz]]

---

## Quick Reference

### Tree Terminology

|Term|Definition|
|---|---|
|Degree|Number of children|
|Depth of node|Edges from root to node|
|Height of node|Edges from node to deepest leaf|
|Height of tree|Height of root|

For perfect binary tree with height $h$: $$n = 2^{h+1}-1, \quad h = \lfloor \log_2 n \rfloor, \quad \text{leaves} = 2^h$$

---

### Traversals

|Traversal|Order|Use case|
|---|---|---|
|Preorder|Root → L → R|Copy tree, serialize|
|Inorder|L → Root → R|Sorted output from BST|
|Postorder|L → R → Root|Delete tree, evaluate expression|
|Level order|Level by level|Shortest path, level problems|

**Time:** $O(n)$ all. **Space:** $O(h)$ DFS, $O(w)$ BFS.

---

### BST Operations

|Operation|Average|Worst (skewed)|
|---|---|---|
|Search|$O(\log n)$|$O(n)$|
|Insert|$O(\log n)$|$O(n)$|
|Delete|$O(\log n)$|$O(n)$|

Inorder successor = min of right subtree (if exists), else lowest ancestor where node is in left subtree.

---

### Balanced BST Comparison

||AVL|Red-Black|2-4 Tree|
|---|---|---|---|
|Height bound|$1.44\log n$|$2\log n$|$\log n$|
|Insert rotations|$O(\log n)$|$\leq 2$|Splits|
|Delete rotations|$O(\log n)$|$\leq 3$|Merges|
|Best for|Lookups|Insert/Delete|Conceptual basis|

---

### AVL Balance Factors and Rotations

$$bf(v) = h(\text{left}) - h(\text{right}) \in {-1, 0, 1}$$

|Imbalance|$bf$|Child $bf$|Fix|
|---|---|---|---|
|LL|$+2$|$\geq 0$|Right rotate|
|RR|$-2$|$\leq 0$|Left rotate|
|LR|$+2$|$< 0$|Left-Right rotate|
|RL|$-2$|$> 0$|Right-Left rotate|

---

### Segment Tree

$$\text{left}(i) = 2i, \quad \text{right}(i) = 2i+1, \quad \text{parent}(i) = \lfloor i/2 \rfloor$$

|Operation|Time|Space|
|---|---|---|
|Build|$O(n)$|$O(4n)$|
|Query|$O(\log n)$|—|
|Update|$O(\log n)$|—|

---

### Trie

|Operation|Time|
|---|---|
|Insert / Search / Delete|$O(L)$ where $L$ = word length|
|Prefix count|$O(L)$|

Total space = $O(\sum L_i)$ across all inserted words.

---

Here are the subfolder index files:

`07_01_00_Binary_Trees.md`:

---

# Binary Trees

## Notes

- [[07_01_01_Tree_Basics]]
- [[07_01_02_Traversals_Inorder_Preorder_Postorder]]
- [[07_01_03_Level_Order_BFS]]
- [[07_01_04_Height_and_Diameter]]
- [[07_01_05_Views_of_Tree]]

---

`07_02_00_Binary_Search_Trees.md`:

---

# Binary Search Trees

## Notes

- [[07_02_01_BST_Introduction]]
- [[07_02_02_BST_Insertion]]
- [[07_02_03_BST_Deletion]]
- [[07_02_04_BST_Traversals]]
- [[07_02_05_Successor_and_Predecessor]]

---

`07_03_00_Balanced_BSTs.md`:

---

# Balanced BSTs

## Notes

- [[07_03_01_2_4_Trees]]
- [[07_03_02_Red_Black_Trees]]

## Subfolders

- [[07_03_03_00_AVL_Trees]]

---

`07_03_03_00_AVL_Trees.md`:

---

# AVL Trees

## Notes

- [[07_03_03_01_AVL_Introduction]]
- [[07_03_03_02_Rotations_LL_RR_LR_RL]]

---

`07_04_00_Tries.md`:

---

# Tries

## Notes

- [[07_04_01_Trie_Introduction]]
- [[07_04_02_Insert_Search_Delete]]
- [[07_04_03_Pattern_Matching_with_Tries]]

---

`07_05_00_Segment_Trees.md`:

---

# Segment Trees

## Notes

- [[07_05_01_Build]]
- [[07_05_02_Range_Query]]
- [[07_05_03_Point_Update]]

---