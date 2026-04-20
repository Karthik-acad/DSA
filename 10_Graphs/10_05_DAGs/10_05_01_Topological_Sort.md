
---

# Topological Sort

A **DAG** (Directed Acyclic Graph) naturally represents dependencies — task A must happen before task B. Topological sort gives a linear ordering of vertices such that for every directed edge $(u, v)$, $u$ appears before $v$.

> **Definition.** A _topological sort_ of a DAG is a linear ordering of all vertices such that for every directed edge $(u, v)$, vertex $u$ comes before $v$. It exists if and only if the graph has no directed cycle.

Think of course prerequisites — you must take Linear Algebra before Quantum Mechanics. Topological sort gives a valid course ordering.

---

## Algorithm 1 — Kahn's (BFS-based)

Use in-degrees. A vertex with in-degree 0 has no dependencies — it can be processed first. After processing it, reduce the in-degrees of its neighbors. Repeat.

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

// Time: O(V + E), Space: O(V)
vector<int> topoSortKahn(vector<vector<int>>& adj, int V) {
    vector<int> inDegree(V, 0);
    for (int u = 0; u < V; u++)
        for (int v : adj[u])
            inDegree[v]++;

    queue<int> q;
    for (int i = 0; i < V; i++)
        if (inDegree[i] == 0) q.push(i);

    vector<int> order;
    while (!q.empty()) {
        int u = q.front(); q.pop();
        order.push_back(u);
        for (int v : adj[u]) {
            if (--inDegree[v] == 0)
                q.push(v);
        }
    }

    if (order.size() != V) {
        cout << "Cycle detected — no topological order" << endl;
        return {};
    }
    return order;
}
```

---

## Algorithm 2 — DFS-based

Run DFS. When a vertex's DFS finishes (all descendants processed), push it to a stack. The stack's contents in reverse are the topological order.

```cpp
// Time: O(V + E), Space: O(V)
void dfsTopoHelper(int u, vector<vector<int>>& adj,
                   vector<bool>& visited, vector<int>& stack) {
    visited[u] = true;
    for (int v : adj[u])
        if (!visited[v])
            dfsTopoHelper(v, adj, visited, stack);
    stack.push_back(u);  // push after all descendants
}

vector<int> topoSortDFS(vector<vector<int>>& adj, int V) {
    vector<bool> visited(V, false);
    vector<int> stack;
    for (int i = 0; i < V; i++)
        if (!visited[i])
            dfsTopoHelper(i, adj, visited, stack);
    reverse(stack.begin(), stack.end());
    return stack;
}

int main() {
    int V = 6;
    vector<vector<int>> adj(V);
    adj[5].push_back(2); adj[5].push_back(0);
    adj[4].push_back(0); adj[4].push_back(1);
    adj[2].push_back(3); adj[3].push_back(1);

    auto order = topoSortKahn(adj, V);
    for (int v : order) cout << v << " ";  // one valid order
    return 0;
}
```

---

## Cycle Detection via Kahn's

Kahn's algorithm doubles as cycle detection — if the output has fewer than $V$ vertices, a cycle prevented some vertices from ever reaching in-degree 0.

**Complexity:** Both algorithms are $O(V + E)$.

---

