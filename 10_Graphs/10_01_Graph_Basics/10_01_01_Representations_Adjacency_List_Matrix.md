
---

# Graph Representations — Adjacency List and Matrix

From our study of trees, we saw a hierarchical structure where each node has exactly one parent. A **graph** generalizes this — nodes can connect to any other nodes, with no hierarchy, no root, and cycles allowed.

> **Definition.** A _graph_ $G = (V, E)$ consists of a set of **vertices** $V$ and a set of **edges** $E$ where each edge connects a pair of vertices. $|V| = n$ is the number of vertices and $|E| = m$ is the number of edges.

Think of a road map — cities are vertices, roads are edges. Or a social network — people are vertices, friendships are edges. The graph is the most general data structure we have studied — trees, linked lists, and arrays are all special cases of graphs.

---

## Adjacency Matrix

Store a 2D array $A$ of size $n \times n$ where $A[i][j] = 1$ (or the edge weight) if there is an edge from $i$ to $j$, and 0 otherwise.

```cpp
#include <iostream>
#include <vector>
using namespace std;

class GraphMatrix {
    int n;
    vector<vector<int>> adj;

public:
    GraphMatrix(int n) : n(n), adj(n, vector<int>(n, 0)) {}

    void addEdge(int u, int v, int w = 1) {
        adj[u][v] = w;
        adj[v][u] = w;  // remove for directed graph
    }

    bool hasEdge(int u, int v) { return adj[u][v] != 0; }

    void print() {
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++)
                cout << adj[i][j] << " ";
            cout << endl;
        }
    }
};
```

**Space:** $O(n^2)$ — stores all $n^2$ possible edges regardless of how many actually exist.

---

## Adjacency List

For each vertex, store only the list of its actual neighbors.

```cpp
class GraphList {
    int n;
    vector<vector<pair<int,int>>> adj;  // adj[u] = {(v, weight), ...}

public:
    GraphList(int n) : n(n), adj(n) {}

    void addEdge(int u, int v, int w = 1) {
        adj[u].push_back({v, w});
        adj[v].push_back({u, w});  // remove for directed
    }

    vector<pair<int,int>>& neighbors(int u) { return adj[u]; }

    void print() {
        for (int u = 0; u < n; u++) {
            cout << u << ": ";
            for (auto [v, w] : adj[u])
                cout << "(" << v << "," << w << ") ";
            cout << endl;
        }
    }
};

int main() {
    GraphList g(5);
    g.addEdge(0, 1, 4);
    g.addEdge(0, 2, 1);
    g.addEdge(1, 3, 2);
    g.addEdge(2, 3, 5);
    g.addEdge(3, 4, 3);
    g.print();
    return 0;
}
```

**Space:** $O(n + m)$ — only stores actual edges.

---

## Comparison

||Adjacency Matrix|Adjacency List|
|---|---|---|
|Space|$O(n^2)$|$O(n + m)$|
|Edge existence check|$O(1)$|$O(\deg(u))$|
|Iterate all neighbors|$O(n)$|$O(\deg(u))$|
|Add edge|$O(1)$|$O(1)$|
|Best for|Dense graphs ($m \approx n^2$)|Sparse graphs ($m \ll n^2$)|

Most real-world graphs are **sparse** — a social network of a billion users doesn't have a trillion friendships. Adjacency list is almost always the right choice unless the graph is dense or you need $O(1)$ edge queries.

> **Definition.** A graph is _sparse_ if $m = O(n)$ or $m \ll n^2$. It is _dense_ if $m = \Theta(n^2)$.

---
