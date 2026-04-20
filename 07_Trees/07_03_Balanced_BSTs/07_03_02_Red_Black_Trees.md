
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
