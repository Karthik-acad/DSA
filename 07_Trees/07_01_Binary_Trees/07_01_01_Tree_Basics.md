
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
