
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
