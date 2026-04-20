
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