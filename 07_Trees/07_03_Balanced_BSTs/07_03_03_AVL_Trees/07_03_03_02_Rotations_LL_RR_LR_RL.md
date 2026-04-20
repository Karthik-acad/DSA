
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
