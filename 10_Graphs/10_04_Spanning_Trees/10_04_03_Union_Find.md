
---

# Union-Find (Disjoint Set Union)

Union-Find was used in Kruskal's algorithm — here we study it properly as a standalone data structure.

> **Definition.** _Union-Find_ (Disjoint Set Union, DSU) maintains a collection of disjoint sets supporting two operations: **find** (which set does element $x$ belong to?) and **union** (merge the sets containing $x$ and $y$).

```cpp
#include <iostream>
#include <vector>
using namespace std;

class UnionFind {
    vector<int> parent, rank;
    int components;

public:
    UnionFind(int n) : parent(n), rank(n, 0), components(n) {
        iota(parent.begin(), parent.end(), 0);
    }

    // Find with path compression — O(alpha(n)) amortized
    int find(int x) {
        if (parent[x] != x)
            parent[x] = find(parent[x]);  // path compression
        return parent[x];
    }

    // Union by rank — O(alpha(n)) amortized
    bool unite(int x, int y) {
        int px = find(x), py = find(y);
        if (px == py) return false;
        if (rank[px] < rank[py]) swap(px, py);
        parent[py] = px;
        if (rank[px] == rank[py]) rank[px]++;
        components--;
        return true;
    }

    bool connected(int x, int y) { return find(x) == find(y); }
    int countComponents() { return components; }
};
```

---

## Path Compression + Union by Rank

Two optimizations together give nearly constant amortized time:

**Path compression** — when finding the root, make every node on the path point directly to the root. Future finds on the same path are $O(1)$.

**Union by rank** — always attach the smaller tree under the root of the larger tree. Keeps trees shallow.

Together: amortized $O(\alpha(n))$ per operation where $\alpha$ is the inverse Ackermann function — effectively $O(1)$ for all practical values of $n$ ($\alpha(n) \leq 4$ for $n < 10^{600}$).

---

## Applications

- **Kruskal's MST** — check if adding an edge creates a cycle
- **Connected components** — count components in $O((V+E)\alpha(V))$
- **Online connectivity** — dynamically add edges and query connectivity
- **Percolation** — grid connectivity problems

```cpp
int main() {
    UnionFind uf(6);
    uf.unite(0,1); uf.unite(1,2);
    uf.unite(3,4);
    cout << uf.countComponents() << endl;  // 3: {0,1,2}, {3,4}, {5}
    cout << uf.connected(0,2) << endl;     // 1
    cout << uf.connected(0,3) << endl;     // 0
    uf.unite(2,3);
    cout << uf.countComponents() << endl;  // 2: {0,1,2,3,4}, {5}
    return 0;
}
```

**Complexity:** $O(\alpha(n))$ per operation — essentially $O(1)$.

---
