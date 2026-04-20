
---

# BFS and Connected Components

We first saw BFS in [[04_05_02_BFS_Queue_Usage]] as a queue pattern on trees. Here we use it on general graphs — where the key difference is that graphs can have cycles, so we need a visited array to avoid infinite loops.

> **Definition.** _Breadth First Search_ explores a graph level by level from a source vertex — first all vertices at distance 1, then distance 2, and so on. It uses a queue and a visited array.

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

// BFS from source — Time: O(V + E), Space: O(V)
void bfs(int source, vector<vector<int>>& adj, int V) {
    vector<bool> visited(V, false);
    queue<int> q;

    visited[source] = true;
    q.push(source);

    while (!q.empty()) {
        int u = q.front(); q.pop();
        cout << u << " ";

        for (int v : adj[u]) {
            if (!visited[v]) {
                visited[v] = true;
                q.push(v);
            }
        }
    }
}
```

---

## BFS Shortest Path (Unweighted)

BFS naturally computes shortest paths in unweighted graphs — the level at which a vertex is first discovered equals its distance from the source.

```cpp
// Shortest distances from source — O(V + E)
vector<int> bfsDistances(int source, vector<vector<int>>& adj, int V) {
    vector<int> dist(V, -1);
    queue<int> q;
    dist[source] = 0;
    q.push(source);

    while (!q.empty()) {
        int u = q.front(); q.pop();
        for (int v : adj[u]) {
            if (dist[v] == -1) {
                dist[v] = dist[u] + 1;
                q.push(v);
            }
        }
    }
    return dist;
}

// Reconstruct shortest path from source to target
vector<int> shortestPath(int source, int target,
                          vector<vector<int>>& adj, int V) {
    vector<int> parent(V, -1);
    vector<bool> visited(V, false);
    queue<int> q;
    visited[source] = true;
    q.push(source);

    while (!q.empty()) {
        int u = q.front(); q.pop();
        if (u == target) break;
        for (int v : adj[u]) {
            if (!visited[v]) {
                visited[v] = true;
                parent[v] = u;
                q.push(v);
            }
        }
    }

    // Reconstruct path
    vector<int> path;
    for (int v = target; v != -1; v = parent[v])
        path.push_back(v);
    reverse(path.begin(), path.end());
    return path;
}
```

---

## Connected Components

A connected component is a maximal set of vertices such that there is a path between every pair. To find all components — run BFS from every unvisited vertex.

```cpp
// Find all connected components — O(V + E)
int connectedComponents(vector<vector<int>>& adj, int V) {
    vector<bool> visited(V, false);
    int components = 0;

    for (int i = 0; i < V; i++) {
        if (!visited[i]) {
            components++;
            // BFS from i labels all vertices in this component
            queue<int> q;
            visited[i] = true;
            q.push(i);
            while (!q.empty()) {
                int u = q.front(); q.pop();
                for (int v : adj[u])
                    if (!visited[v]) {
                        visited[v] = true;
                        q.push(v);
                    }
            }
        }
    }
    return components;
}

int main() {
    int V = 6;
    vector<vector<int>> adj(V);
    // Component 1: 0-1-2
    adj[0].push_back(1); adj[1].push_back(0);
    adj[1].push_back(2); adj[2].push_back(1);
    // Component 2: 3-4
    adj[3].push_back(4); adj[4].push_back(3);
    // Component 3: 5 (isolated)

    cout << connectedComponents(adj, V) << endl;  // 3
    return 0;
}
```

**Complexity:** $O(V + E)$ — each vertex and edge examined once.

---
