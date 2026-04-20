
---

# Dijkstra's Algorithm

BFS finds shortest paths in unweighted graphs by counting hops. For weighted graphs, hop count is meaningless — a path with 3 edges might be shorter than one with 2 if the weights differ. **Dijkstra's algorithm** extends BFS to weighted graphs using a priority queue.

> **Definition.** _Dijkstra's algorithm_ finds the shortest path from a source vertex to all other vertices in a graph with non-negative edge weights. It greedily selects the unvisited vertex with the smallest known distance at each step.

The greedy choice is safe because all edge weights are non-negative — once a vertex's shortest distance is finalized, no future path can improve it.

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <climits>
using namespace std;

typedef pair<int,int> pii;  // (distance, vertex)

// Time: O((V + E) log V), Space: O(V)
vector<int> dijkstra(int source, vector<vector<pii>>& adj, int V) {
    vector<int> dist(V, INT_MAX);
    priority_queue<pii, vector<pii>, greater<pii>> pq;  // min-heap

    dist[source] = 0;
    pq.push({0, source});

    while (!pq.empty()) {
        auto [d, u] = pq.top(); pq.pop();

        // Skip if we already found a better path to u
        if (d > dist[u]) continue;

        for (auto [v, w] : adj[u]) {
            if (dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                pq.push({dist[v], v});
            }
        }
    }
    return dist;
}

int main() {
    int V = 5;
    vector<vector<pii>> adj(V);
    adj[0].push_back({1, 4}); adj[1].push_back({0, 4});
    adj[0].push_back({2, 1}); adj[2].push_back({0, 1});
    adj[2].push_back({1, 2}); adj[1].push_back({2, 2});
    adj[1].push_back({3, 1}); adj[3].push_back({1, 1});
    adj[2].push_back({3, 5}); adj[3].push_back({2, 5});
    adj[3].push_back({4, 3}); adj[4].push_back({3, 3});

    auto dist = dijkstra(0, adj, V);
    for (int i = 0; i < V; i++)
        cout << "dist[" << i << "] = " << dist[i] << endl;
    return 0;
}
```

---

## Why Non-Negative Weights Only

If any edge has negative weight, the greedy assumption breaks — a longer path might become shorter after adding a negative edge. Consider:

```
0 --5--> 1 --(-6)--> 2
0 --2-------------- > 2
```

Dijkstra might finalize dist[2] = 2 before discovering the path 0→1→2 = 5-6 = -1.

For graphs with negative edges, use **Bellman-Ford** (see [[10_03_03_Bellman_Ford]]).

---

## Complexity

Using a binary heap priority queue: $O((V + E) \log V)$

Using a Fibonacci heap (theoretical): $O(E + V \log V)$

In practice, the binary heap version is used.

---
