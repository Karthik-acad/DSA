
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
