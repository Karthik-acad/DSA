
---

# Cycle Detection

Cycle detection was introduced in [[10_02_02_DFS_Directed_and_Undirected]]. This note consolidates all methods with full implementations.

---

## Undirected Graph — DFS

A back edge (visiting an already-visited non-parent vertex) indicates a cycle.

```cpp
// Time: O(V + E), Space: O(V)
bool hasCycleUndirected(vector<vector<int>>& adj, int V) {
    vector<bool> visited(V, false);
    function<bool(int,int)> dfs = [&](int u, int parent) -> bool {
        visited[u] = true;
        for (int v : adj[u]) {
            if (!visited[v]) {
                if (dfs(v, u)) return true;
            } else if (v != parent) {
                return true;  // back edge
            }
        }
        return false;
    };
    for (int i = 0; i < V; i++)
        if (!visited[i] && dfs(i, -1)) return true;
    return false;
}
```

---

## Directed Graph — DFS with Colors

A GRAY vertex (currently on the recursion stack) means a back edge = cycle.

```cpp
// Time: O(V + E), Space: O(V)
bool hasCycleDirected(vector<vector<int>>& adj, int V) {
    vector<int> color(V, 0);  // 0=white, 1=gray, 2=black
    function<bool(int)> dfs = [&](int u) -> bool {
        color[u] = 1;  // gray — on stack
        for (int v : adj[u]) {
            if (color[v] == 1) return true;  // back edge
            if (color[v] == 0 && dfs(v)) return true;
        }
        color[u] = 2;  // black — done
        return false;
    };
    for (int i = 0; i < V; i++)
        if (color[i] == 0 && dfs(i)) return true;
    return false;
}
```

---

## Undirected Graph — Union-Find

An alternative to DFS — add edges one by one. If an edge connects two already-connected vertices, it creates a cycle.

```cpp
// Time: O(E * alpha(V)), Space: O(V)
bool hasCycleUnionFind(vector<pair<int,int>>& edges, int V) {
    UnionFind uf(V);
    for (auto [u, v] : edges)
        if (!uf.unite(u, v)) return true;  // already connected
    return false;
}
```

---
