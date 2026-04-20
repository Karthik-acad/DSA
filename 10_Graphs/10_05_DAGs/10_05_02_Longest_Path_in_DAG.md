
---

# Longest Path in DAG

In a general graph, the longest path problem is NP-hard. In a **DAG**, it can be solved in $O(V + E)$ using topological sort and dynamic programming.

> **Definition.** The _longest path_ in a DAG from source $s$ is the maximum-weight path from $s$ to any other vertex. It is computed by processing vertices in topological order and relaxing edges in the "maximize" direction instead of minimize.

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;

typedef pair<int,int> pii;

// Longest path from source in weighted DAG
// Time: O(V + E), Space: O(V)
vector<int> longestPath(int source, vector<vector<pii>>& adj,
                         vector<int>& topoOrder, int V) {
    vector<int> dist(V, INT_MIN);
    dist[source] = 0;

    for (int u : topoOrder) {
        if (dist[u] != INT_MIN) {
            for (auto [v, w] : adj[u]) {
                if (dist[u] + w > dist[v])
                    dist[v] = dist[u] + w;
            }
        }
    }
    return dist;
}

int main() {
    int V = 6;
    vector<vector<pii>> adj(V);
    adj[0].push_back({1,5}); adj[0].push_back({2,3});
    adj[1].push_back({3,6}); adj[1].push_back({2,2});
    adj[2].push_back({4,4}); adj[2].push_back({5,2}); adj[2].push_back({3,7});
    adj[3].push_back({4,-1}); adj[3].push_back({5,1});
    adj[4].push_back({5,-2});

    // Get topological order first
    vector<vector<int>> adjUnweighted(V);
    for (int u = 0; u < V; u++)
        for (auto [v,w] : adj[u])
            adjUnweighted[u].push_back(v);

    vector<bool> visited(V, false);
    vector<int> topoOrder;
    function<void(int)> dfs = [&](int u) {
        visited[u] = true;
        for (int v : adjUnweighted[u])
            if (!visited[v]) dfs(v);
        topoOrder.push_back(u);
    };
    for (int i = 0; i < V; i++)
        if (!visited[i]) dfs(i);
    reverse(topoOrder.begin(), topoOrder.end());

    auto dist = longestPath(1, adj, topoOrder, V);
    cout << "Longest from 1 to 5: " << dist[5] << endl;  // 9
    return 0;
}
```

**Complexity:** $O(V + E)$ — topological sort plus one pass over all edges.

---
