
---

# Floyd-Warshall Algorithm

Dijkstra and Bellman-Ford compute shortest paths from a **single source** to all vertices. **Floyd-Warshall** computes shortest paths between **all pairs** of vertices simultaneously.

> **Definition.** _Floyd-Warshall_ computes all-pairs shortest paths using dynamic programming. $dp[i][j][k]$ = shortest path from $i$ to $j$ using only vertices ${0, 1, \ldots, k}$ as intermediate vertices.

The recurrence: $$dp[i][j][k] = \min(dp[i][j][k-1],\ dp[i][k][k-1] + dp[k][j][k-1])$$

Either vertex $k$ is not on the shortest path (use previous solution), or it is (go through $k$). The space can be reduced to 2D since $k$ is iterated in place.

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;

const int INF = 1e9;

// Time: O(V^3), Space: O(V^2)
vector<vector<int>> floydWarshall(vector<vector<int>>& graph, int V) {
    vector<vector<int>> dist = graph;

    // Initialize: no path = INF, self = 0
    for (int i = 0; i < V; i++) dist[i][i] = 0;

    // Try each vertex as intermediate
    for (int k = 0; k < V; k++)
        for (int i = 0; i < V; i++)
            for (int j = 0; j < V; j++)
                if (dist[i][k] != INF && dist[k][j] != INF)
                    dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j]);

    // Check for negative cycles — diagonal becomes negative
    for (int i = 0; i < V; i++)
        if (dist[i][i] < 0) {
            cout << "Negative cycle detected!" << endl;
            return {};
        }

    return dist;
}

int main() {
    int V = 4;
    vector<vector<int>> graph = {
        {0,   5,   INF, 10},
        {INF, 0,   3,   INF},
        {INF, INF, 0,   1},
        {INF, INF, INF, 0}
    };

    auto dist = floydWarshall(graph, V);
    for (int i = 0; i < V; i++) {
        for (int j = 0; j < V; j++)
            cout << (dist[i][j]==INF ? -1 : dist[i][j]) << "\t";
        cout << endl;
    }
    return 0;
}
```

---

## Comparison of Shortest Path Algorithms

|Algorithm|Source|Negative edges|Negative cycles|Time|
|---|---|---|---|---|
|BFS|Single|No (unweighted)|No|$O(V+E)$|
|Dijkstra|Single|No|No|$O((V+E)\log V)$|
|Bellman-Ford|Single|Yes|Detects|$O(VE)$|
|Floyd-Warshall|All pairs|Yes|Detects|$O(V^3)$|

Floyd-Warshall's $O(V^3)$ is practical for small dense graphs ($V \leq 500$). For larger sparse graphs, run Dijkstra from every vertex — $O(V(V+E)\log V)$ which beats $O(V^3)$ when $E \ll V^2$.

---
