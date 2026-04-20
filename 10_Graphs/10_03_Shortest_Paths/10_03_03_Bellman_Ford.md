
---

# Bellman-Ford Algorithm

Dijkstra fails on negative edge weights. **Bellman-Ford** handles them — and additionally detects negative cycles (cycles whose total weight is negative, making shortest paths undefined).

> **Definition.** _Bellman-Ford_ computes shortest paths from a single source by relaxing all edges $V-1$ times. If a further relaxation is possible after $V-1$ rounds, a negative cycle exists.

The key insight — in a graph with $V$ vertices, any shortest path (without cycles) has at most $V-1$ edges. Relaxing all edges once guarantees at least one more edge of the shortest path is correctly set. After $V-1$ relaxations, all shortest paths are correct.

$$\text{relax}(u, v, w): \text{if } dist[u] + w < dist[v], \text{ set } dist[v] = dist[u] + w$$

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;

struct Edge { int u, v, w; };

// Time: O(V * E), Space: O(V)
vector<int> bellmanFord(int source, vector<Edge>& edges, int V) {
    vector<int> dist(V, INT_MAX);
    dist[source] = 0;

    // Relax all edges V-1 times
    for (int i = 0; i < V - 1; i++) {
        for (auto& [u, v, w] : edges) {
            if (dist[u] != INT_MAX && dist[u] + w < dist[v])
                dist[v] = dist[u] + w;
        }
    }

    // V-th relaxation — if any improvement, negative cycle exists
    for (auto& [u, v, w] : edges) {
        if (dist[u] != INT_MAX && dist[u] + w < dist[v]) {
            cout << "Negative cycle detected!" << endl;
            return {};
        }
    }

    return dist;
}

int main() {
    int V = 5;
    vector<Edge> edges = {
        {0,1,6}, {0,2,7},
        {1,2,8}, {1,3,-4}, {1,4,5},
        {2,3,9}, {2,4,-3},
        {3,0,2}, {3,4,7},
        {4,3,2}
    };

    auto dist = bellmanFord(0, edges, V);
    for (int i = 0; i < V; i++)
        cout << "dist[" << i << "] = " << dist[i] << endl;
    return 0;
}
```

---

## Dijkstra vs Bellman-Ford

||Dijkstra|Bellman-Ford|
|---|---|---|
|Negative weights|No|Yes|
|Negative cycle detection|No|Yes|
|Time complexity|$O((V+E)\log V)$|$O(VE)$|
|Approach|Greedy|Dynamic programming|

Bellman-Ford is $O(VE)$ — much slower than Dijkstra's $O((V+E)\log V)$ for sparse graphs. Use Dijkstra when all weights are non-negative. Use Bellman-Ford when negative edges exist.

---
