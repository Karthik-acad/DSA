
---

# Shortest Path Tree

When Dijkstra runs from a source, it does not just compute distances — it implicitly builds a **shortest path tree**: a tree rooted at the source where the unique path from root to any vertex is the shortest path in the original graph.

> **Definition.** A _shortest path tree_ rooted at source $s$ is a spanning tree of the graph where the path from $s$ to any vertex $v$ in the tree is a shortest path in the original graph.

```cpp
// Dijkstra with parent tracking to reconstruct shortest path tree
// Time: O((V + E) log V), Space: O(V)
pair<vector<int>, vector<int>> dijkstraTree(int source,
                                             vector<vector<pii>>& adj, int V) {
    vector<int> dist(V, INT_MAX);
    vector<int> parent(V, -1);
    priority_queue<pii, vector<pii>, greater<pii>> pq;

    dist[source] = 0;
    pq.push({0, source});

    while (!pq.empty()) {
        auto [d, u] = pq.top(); pq.pop();
        if (d > dist[u]) continue;

        for (auto [v, w] : adj[u]) {
            if (dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                parent[v] = u;  // track how we reached v
                pq.push({dist[v], v});
            }
        }
    }
    return {dist, parent};
}

// Reconstruct path from source to target using parent array
vector<int> getPath(int target, vector<int>& parent) {
    vector<int> path;
    for (int v = target; v != -1; v = parent[v])
        path.push_back(v);
    reverse(path.begin(), path.end());
    return path;
}

int main() {
    int V = 5;
    vector<vector<pii>> adj(V);
    adj[0].push_back({1,4}); adj[0].push_back({2,1});
    adj[2].push_back({1,2}); adj[1].push_back({3,1});
    adj[3].push_back({4,3});

    auto [dist, parent] = dijkstraTree(0, adj, V);
    auto path = getPath(4, parent);
    cout << "Shortest path 0->4: ";
    for (int v : path) cout << v << " ";  // 0 2 1 3 4
    cout << "\nDistance: " << dist[4] << endl;  // 7
    return 0;
}
```

---

## Properties of Shortest Path Trees

- Every spanning tree of $n$ vertices has exactly $n-1$ edges
- The shortest path tree is not necessarily unique — multiple shortest paths may exist
- The tree encodes shortest paths to all vertices simultaneously — one Dijkstra run gives you everything

---
