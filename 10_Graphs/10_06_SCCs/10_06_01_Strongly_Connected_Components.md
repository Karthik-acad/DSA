
---

# Strongly Connected Components

In a directed graph, two vertices $u$ and $v$ are **strongly connected** if there is a directed path from $u$ to $v$ and from $v$ to $u$. **Kosaraju's algorithm** finds all SCCs in $O(V + E)$.

> **Definition.** A _Strongly Connected Component (SCC)_ is a maximal set of vertices such that every vertex is reachable from every other vertex in the set.

> **Definition.** _Kosaraju's algorithm_ uses two DFS passes: the first on the original graph to compute finish times, the second on the **transpose graph** (all edges reversed) in reverse finish order.

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;

// Pass 1 — DFS on original graph, push to stack by finish time
void dfs1(int u, vector<vector<int>>& adj,
          vector<bool>& visited, stack<int>& st) {
    visited[u] = true;
    for (int v : adj[u])
        if (!visited[v]) dfs1(v, adj, visited, st);
    st.push(u);  // push after all descendants finish
}

// Pass 2 — DFS on transpose graph
void dfs2(int u, vector<vector<int>>& trans,
          vector<bool>& visited, vector<int>& component) {
    visited[u] = true;
    component.push_back(u);
    for (int v : trans[u])
        if (!visited[v]) dfs2(v, trans, visited, component);
}

// Kosaraju's — Time: O(V + E), Space: O(V)
vector<vector<int>> kosaraju(vector<vector<int>>& adj, int V) {
    // Pass 1 — finish time order
    vector<bool> visited(V, false);
    stack<int> st;
    for (int i = 0; i < V; i++)
        if (!visited[i]) dfs1(i, adj, visited, st);

    // Build transpose graph
    vector<vector<int>> trans(V);
    for (int u = 0; u < V; u++)
        for (int v : adj[u])
            trans[v].push_back(u);

    // Pass 2 — DFS on transpose in reverse finish order
    fill(visited.begin(), visited.end(), false);
    vector<vector<int>> sccs;
    while (!st.empty()) {
        int u = st.top(); st.pop();
        if (!visited[u]) {
            vector<int> component;
            dfs2(u, trans, visited, component);
            sccs.push_back(component);
        }
    }
    return sccs;
}

int main() {
    int V = 8;
    vector<vector<int>> adj(V);
    adj[0].push_back(1); adj[1].push_back(2); adj[2].push_back(0);
    adj[1].push_back(3); adj[3].push_back(4); adj[4].push_back(5);
    adj[5].push_back(3); adj[6].push_back(5); adj[6].push_back(7);
    adj[7].push_back(6);

    auto sccs = kosaraju(adj, V);
    cout << sccs.size() << " SCCs:" << endl;
    for (auto& scc : sccs) {
        for (int v : scc) cout << v << " ";
        cout << endl;
    }
    return 0;
}
```

---

## Why Two Passes Work

The first DFS on the original graph gives vertices in reverse topological order of the **condensation** (DAG of SCCs). Running DFS on the transpose in this order ensures each DFS in pass 2 stays within one SCC — it cannot escape to another SCC because the transpose reverses all inter-SCC edges.

**Complexity:** $O(V + E)$ — two DFS passes, each $O(V+E)$.

---
