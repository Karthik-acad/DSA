
---

# Prim's Algorithm

Given a connected weighted undirected graph, a **minimum spanning tree (MST)** is a subset of edges that connects all vertices with minimum total weight and no cycles.

> **Definition.** A _Minimum Spanning Tree_ of a weighted connected undirected graph is a spanning tree with the minimum possible total edge weight. It has exactly $V-1$ edges.

**Prim's algorithm** builds the MST by greedily adding the minimum-weight edge that connects a tree vertex to a non-tree vertex — growing the MST one vertex at a time.

> **Definition.** _Prim's algorithm_ starts from an arbitrary vertex and repeatedly adds the minimum-weight edge connecting the current MST to a vertex not yet in the MST.

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <climits>
using namespace std;

typedef pair<int,int> pii;

// Time: O((V + E) log V), Space: O(V)
int prims(vector<vector<pii>>& adj, int V) {
    vector<int> key(V, INT_MAX);   // minimum weight edge to reach each vertex
    vector<bool> inMST(V, false);
    priority_queue<pii, vector<pii>, greater<pii>> pq;  // min-heap (weight, vertex)

    key[0] = 0;
    pq.push({0, 0});
    int mstWeight = 0;

    while (!pq.empty()) {
        auto [w, u] = pq.top(); pq.pop();

        if (inMST[u]) continue;
        inMST[u] = true;
        mstWeight += w;

        for (auto [v, weight] : adj[u]) {
            if (!inMST[v] && weight < key[v]) {
                key[v] = weight;
                pq.push({key[v], v});
            }
        }
    }
    return mstWeight;
}

int main() {
    int V = 5;
    vector<vector<pii>> adj(V);
    auto addEdge = [&](int u, int v, int w) {
        adj[u].push_back({v,w}); adj[v].push_back({u,w});
    };
    addEdge(0,1,2); addEdge(0,3,6);
    addEdge(1,2,3); addEdge(1,3,8); addEdge(1,4,5);
    addEdge(2,4,7); addEdge(3,4,9);

    cout << "MST weight: " << prims(adj, V) << endl;  // 16
    return 0;
}
```

---

## Why Prim's Works — Cut Property

> **Theorem (Cut Property).** For any cut of the graph (partition of vertices into two sets), the minimum weight edge crossing the cut belongs to some MST.

Prim's algorithm repeatedly applies this — the cut is always {vertices in MST} vs {vertices not in MST}, and it always picks the minimum crossing edge.

**Complexity:** $O((V+E)\log V)$ with a binary heap — identical to Dijkstra, and for the same reason (priority queue on edges).

---
