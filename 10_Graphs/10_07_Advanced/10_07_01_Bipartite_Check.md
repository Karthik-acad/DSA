
---

# Bipartite Check

> **Definition.** A graph is _bipartite_ if its vertices can be divided into two disjoint sets $A$ and $B$ such that every edge connects a vertex in $A$ to a vertex in $B$ — no edges within the same set.

> **Theorem.** A graph is bipartite if and only if it contains no odd-length cycle.

BFS (or DFS) with 2-coloring checks bipartiteness in $O(V+E)$ — try to color the graph with two colors such that no two adjacent vertices share a color.

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

// Time: O(V + E), Space: O(V)
bool isBipartite(vector<vector<int>>& adj, int V) {
    vector<int> color(V, -1);

    for (int start = 0; start < V; start++) {
        if (color[start] != -1) continue;
        queue<int> q;
        color[start] = 0;
        q.push(start);

        while (!q.empty()) {
            int u = q.front(); q.pop();
            for (int v : adj[u]) {
                if (color[v] == -1) {
                    color[v] = 1 - color[u];  // opposite color
                    q.push(v);
                } else if (color[v] == color[u]) {
                    return false;  // same color = odd cycle = not bipartite
                }
            }
        }
    }
    return true;
}

int main() {
    int V = 4;
    vector<vector<int>> adj(V);
    adj[0].push_back(1); adj[1].push_back(0);
    adj[1].push_back(2); adj[2].push_back(1);
    adj[2].push_back(3); adj[3].push_back(2);
    adj[3].push_back(0); adj[0].push_back(3);
    cout << isBipartite(adj, V) << endl;  // 1 (square — bipartite)

    adj[0].push_back(2); adj[2].push_back(0);
    cout << isBipartite(adj, V) << endl;  // 0 (triangle added — not bipartite)
    return 0;
}
```

---
