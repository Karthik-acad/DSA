
---

# Bridges and Articulation Points

> **Definition.** A _bridge_ is an edge in an undirected graph whose removal increases the number of connected components — it is critical for connectivity.

> **Definition.** An _articulation point_ (cut vertex) is a vertex whose removal increases the number of connected components.

Both are found using DFS with **low values** — the earliest discovery time reachable from a subtree.

```cpp
#include <iostream>
#include <vector>
using namespace std;

class BridgesAndAPs {
    int V, timer = 0;
    vector<vector<int>>& adj;
    vector<int> disc, low, parent;
    vector<bool> visited, isAP;
    vector<pair<int,int>> bridges;

    void dfs(int u) {
        visited[u] = true;
        disc[u] = low[u] = timer++;
        int children = 0;

        for (int v : adj[u]) {
            if (!visited[v]) {
                children++;
                parent[v] = u;
                dfs(v);

                low[u] = min(low[u], low[v]);

                // Articulation point conditions
                if (parent[u] == -1 && children > 1) isAP[u] = true;
                if (parent[u] != -1 && low[v] >= disc[u]) isAP[u] = true;

                // Bridge condition
                if (low[v] > disc[u])
                    bridges.push_back({u, v});

            } else if (v != parent[u]) {
                low[u] = min(low[u], disc[v]);
            }
        }
    }

public:
    BridgesAndAPs(vector<vector<int>>& adj, int V)
        : adj(adj), V(V), disc(V), low(V), parent(V,-1),
          visited(V,false), isAP(V,false) {}

    void find() {
        for (int i = 0; i < V; i++)
            if (!visited[i]) dfs(i);
    }

    void printBridges() {
        cout << "Bridges:" << endl;
        for (auto [u,v] : bridges)
            cout << u << "-" << v << endl;
    }

    void printAPs() {
        cout << "Articulation Points:" << endl;
        for (int i = 0; i < V; i++)
            if (isAP[i]) cout << i << endl;
    }
};

int main() {
    int V = 5;
    vector<vector<int>> adj(V);
    auto addEdge = [&](int u, int v) {
        adj[u].push_back(v); adj[v].push_back(u);
    };
    addEdge(1,0); addEdge(0,2); addEdge(2,1);
    addEdge(0,3); addEdge(3,4);

    BridgesAndAPs bap(adj, V);
    bap.find();
    bap.printBridges();  // 3-4, 0-3
    bap.printAPs();      // 0, 3
    return 0;
}
```

---

## Low Value Intuition

$\text{low}[u]$ = earliest discovery time reachable from the subtree rooted at $u$ via back edges.

- **Bridge** $(u,v)$: $\text{low}[v] > \text{disc}[u]$ — the subtree at $v$ cannot reach $u$ or earlier without the edge $(u,v)$
- **AP** (non-root): $\text{low}[v] \geq \text{disc}[u]$ — removing $u$ disconnects $v$'s subtree

**Complexity:** $O(V + E)$ — single DFS pass.

---
