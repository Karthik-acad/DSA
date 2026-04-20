
---

# Kruskal's Algorithm

Where Prim's grows the MST from a single vertex, **Kruskal's** builds it by sorting all edges and greedily adding the cheapest edge that does not create a cycle.

> **Definition.** _Kruskal's algorithm_ sorts all edges by weight, then adds each edge to the MST if it connects two previously disconnected components (does not form a cycle). Uses Union-Find to efficiently check connectivity.

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

struct Edge {
    int u, v, w;
    bool operator<(const Edge& other) const { return w < other.w; }
};

// Union-Find
struct UnionFind {
    vector<int> parent, rank;
    UnionFind(int n) : parent(n), rank(n, 0) {
        iota(parent.begin(), parent.end(), 0);
    }
    int find(int x) {
        if (parent[x] != x) parent[x] = find(parent[x]);
        return parent[x];
    }
    bool unite(int x, int y) {
        int px = find(x), py = find(y);
        if (px == py) return false;  // same component — would form cycle
        if (rank[px] < rank[py]) swap(px, py);
        parent[py] = px;
        if (rank[px] == rank[py]) rank[px]++;
        return true;
    }
};

// Time: O(E log E), Space: O(V)
int kruskals(vector<Edge>& edges, int V) {
    sort(edges.begin(), edges.end());  // sort by weight
    UnionFind uf(V);
    int mstWeight = 0, edgesAdded = 0;

    for (auto& [u, v, w] : edges) {
        if (uf.unite(u, v)) {
            mstWeight += w;
            edgesAdded++;
            if (edgesAdded == V - 1) break;  // MST complete
        }
    }
    return mstWeight;
}

int main() {
    vector<Edge> edges = {
        {0,1,2},{0,3,6},{1,2,3},{1,3,8},{1,4,5},{2,4,7},{3,4,9}
    };
    cout << "MST weight: " << kruskals(edges, 5) << endl;  // 16
    return 0;
}
```

---

## Prim's vs Kruskal's

||Prim's|Kruskal's|
|---|---|---|
|Approach|Grow tree from vertex|Sort edges globally|
|Data structure|Priority queue|Union-Find + sort|
|Time|$O((V+E)\log V)$|$O(E \log E)$|
|Best for|Dense graphs|Sparse graphs|
|Requires connected|Yes|No (finds MST forest)|

For dense graphs ($E \approx V^2$), $E\log E \approx V^2 \log V$ while Prim's is $O(V^2\log V)$ — similar. For sparse graphs ($E \approx V$), Kruskal's $O(E\log E) = O(V\log V)$ beats Prim's $O(V\log V)$ by constant factors.

---
