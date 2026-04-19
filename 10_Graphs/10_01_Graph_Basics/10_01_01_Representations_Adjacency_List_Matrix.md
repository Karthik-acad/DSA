Starting with Graphs.

Here is `10_01_01_Representations_Adjacency_List_Matrix.md`:

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

Here is `10_01_02_Directed_vs_Undirected_Weighted.md`:

---

# Directed vs Undirected, Weighted Graphs

> **Definition.** In an _undirected graph_, edges have no direction — edge $(u,v)$ means you can travel from $u$ to $v$ and from $v$ to $u$. The adjacency matrix is symmetric.

> **Definition.** In a _directed graph_ (digraph), edges have direction — edge $(u,v)$ means you can travel from $u$ to $v$ but not necessarily from $v$ to $u$. The adjacency matrix may be asymmetric.

> **Definition.** In a _weighted graph_, each edge has an associated numerical weight representing cost, distance, or capacity. In an unweighted graph all edges have equal weight (implicitly 1).

---

## Key Graph Properties

> **Definition.** The _degree_ of a vertex $v$ in an undirected graph is the number of edges incident to $v$. In a directed graph, **in-degree** is the number of incoming edges and **out-degree** is outgoing edges.

> **Definition.** A _path_ is a sequence of vertices where consecutive vertices are connected by edges. A _simple path_ visits each vertex at most once.

> **Definition.** A graph is _connected_ if there is a path between every pair of vertices. A directed graph is _strongly connected_ if there is a directed path from every vertex to every other vertex.

> **Definition.** A _cycle_ is a path that starts and ends at the same vertex. A graph with no cycles is **acyclic**.

---

## Handshaking Lemma

> **Theorem.** For any undirected graph, the sum of all vertex degrees equals twice the number of edges: $$\sum_{v \in V} \deg(v) = 2|E|$$

This follows because each edge contributes 1 to each of its two endpoints' degrees. An immediate consequence — the number of odd-degree vertices is always even.

---

## Special Graph Types

|Type|Definition|Example|
|---|---|---|
|Tree|Connected, acyclic, $n-1$ edges|File system|
|DAG|Directed, acyclic|Dependencies, schedules|
|Complete|Every pair connected|$K_n$, $m = n(n-1)/2$|
|Bipartite|Vertices split into two sets, edges only between sets|Job matching|
|Planar|Can be drawn without edge crossings|Road maps|

---

## Edge Counts

For simple graphs (no self-loops, no multiple edges):

- Undirected: $0 \leq m \leq n(n-1)/2$
- Directed: $0 \leq m \leq n(n-1)$
- Tree: $m = n-1$ always
- Connected graph: $m \geq n-1$

---

Here is `10_02_01_BFS_and_Connected_Components.md`:

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

Here is `10_02_02_DFS_Directed_and_Undirected.md`:

---

# DFS — Directed and Undirected

**Depth First Search** explores as far as possible along each branch before backtracking — using a stack (or recursion) instead of a queue. Where BFS gives shortest paths, DFS gives us structural information about the graph — cycles, connected components, topological order, SCCs.

> **Definition.** _Depth First Search_ from a source explores each neighbor recursively before moving to the next neighbor. It maintains a visited array and records discovery and finish times.

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Recursive DFS — Time: O(V + E), Space: O(V)
void dfs(int u, vector<vector<int>>& adj, vector<bool>& visited) {
    visited[u] = true;
    cout << u << " ";
    for (int v : adj[u])
        if (!visited[v])
            dfs(v, adj, visited);
}

// DFS with discovery and finish times — useful for topological sort, SCCs
void dfsTimer(int u, vector<vector<int>>& adj, vector<bool>& visited,
              vector<int>& disc, vector<int>& fin, int& timer) {
    visited[u] = true;
    disc[u] = timer++;
    for (int v : adj[u])
        if (!visited[v])
            dfsTimer(v, adj, visited, disc, fin, timer);
    fin[u] = timer++;
}
```

---

## DFS Edge Classification

During DFS on a directed graph, each edge $(u,v)$ falls into one of four categories:

> **Definition.** A _tree edge_ is an edge in the DFS tree — $v$ was first discovered via $u$.

> **Definition.** A _back edge_ $(u,v)$ goes to an ancestor of $u$ in the DFS tree — indicates a cycle.

> **Definition.** A _forward edge_ $(u,v)$ goes to a descendant that was already fully processed.

> **Definition.** A _cross edge_ $(u,v)$ goes to a vertex in a different DFS subtree.

```cpp
// Edge classification using colors: WHITE=0, GRAY=1, BLACK=2
// GRAY = currently in DFS stack (ancestor)
// BLACK = fully processed
enum Color { WHITE, GRAY, BLACK };

void dfsClassify(int u, vector<vector<int>>& adj, vector<Color>& color) {
    color[u] = GRAY;
    for (int v : adj[u]) {
        if (color[v] == WHITE) {
            cout << "Tree edge: " << u << "->" << v << endl;
            dfsClassify(v, adj, color);
        } else if (color[v] == GRAY) {
            cout << "Back edge: " << u << "->" << v << " (CYCLE)" << endl;
        } else {
            cout << "Forward/Cross edge: " << u << "->" << v << endl;
        }
    }
    color[u] = BLACK;
}
```

---

## Cycle Detection

**Undirected graph** — a back edge exists if DFS encounters an already-visited vertex that is not the parent.

```cpp
bool hasCycleUndirected(int u, int parent,
                         vector<vector<int>>& adj, vector<bool>& visited) {
    visited[u] = true;
    for (int v : adj[u]) {
        if (!visited[v]) {
            if (hasCycleUndirected(v, u, adj, visited)) return true;
        } else if (v != parent) {
            return true;  // back edge found
        }
    }
    return false;
}
```

**Directed graph** — a cycle exists if DFS encounters a GRAY vertex (currently on the stack).

```cpp
bool hasCycleDirected(int u, vector<vector<int>>& adj, vector<Color>& color) {
    color[u] = GRAY;
    for (int v : adj[u]) {
        if (color[v] == GRAY) return true;  // back edge = cycle
        if (color[v] == WHITE && hasCycleDirected(v, adj, color)) return true;
    }
    color[u] = BLACK;
    return false;
}
```

**Complexity:** $O(V + E)$ for both.

---

Here is `10_03_01_Dijkstras_Algorithm.md`:

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

Here is `10_03_02_Shortest_Path_Tree.md`:

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

Here is `10_03_03_Bellman_Ford.md`:

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

Here is `10_03_04_Floyd_Warshall.md`:

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

Here is `10_04_01_Prims_Algorithm.md`:

---

# Prim's Algorithm

Given a connected weighted undirected graph, a **minimum spanning tree (MST)** is a subset of edges that connects all vertices with minimum total weight and no cycles.

> **Definition.** A _Minimum Spanning Tree_ of a weighted connected undirected graph is a spanning tree with the minimum possible total edge weight. It has exactly $V-1$ edges.

**Prim's algorithm** builds the MST by greedily adding the minimum-weight edge that connects a tree vertex to a non-tree vertex — growing the MST one vertex at a time.

> **Definition.** _Prim's algorithm_ starts from an arbitrary vertex and repeatedly adds the minimum-weight edge connecting the current MST to a vertex not yet in the MST.

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <climits>
using namespace std;

typedef pair<int,int> pii;

// Time: O((V + E) log V), Space: O(V)
int prims(vector<vector<pii>>& adj, int V) {
    vector<int> key(V, INT_MAX);   // minimum weight edge to reach each vertex
    vector<bool> inMST(V, false);
    priority_queue<pii, vector<pii>, greater<pii>> pq;  // min-heap (weight, vertex)

    key[0] = 0;
    pq.push({0, 0});
    int mstWeight = 0;

    while (!pq.empty()) {
        auto [w, u] = pq.top(); pq.pop();

        if (inMST[u]) continue;
        inMST[u] = true;
        mstWeight += w;

        for (auto [v, weight] : adj[u]) {
            if (!inMST[v] && weight < key[v]) {
                key[v] = weight;
                pq.push({key[v], v});
            }
        }
    }
    return mstWeight;
}

int main() {
    int V = 5;
    vector<vector<pii>> adj(V);
    auto addEdge = [&](int u, int v, int w) {
        adj[u].push_back({v,w}); adj[v].push_back({u,w});
    };
    addEdge(0,1,2); addEdge(0,3,6);
    addEdge(1,2,3); addEdge(1,3,8); addEdge(1,4,5);
    addEdge(2,4,7); addEdge(3,4,9);

    cout << "MST weight: " << prims(adj, V) << endl;  // 16
    return 0;
}
```

---

## Why Prim's Works — Cut Property

> **Theorem (Cut Property).** For any cut of the graph (partition of vertices into two sets), the minimum weight edge crossing the cut belongs to some MST.

Prim's algorithm repeatedly applies this — the cut is always {vertices in MST} vs {vertices not in MST}, and it always picks the minimum crossing edge.

**Complexity:** $O((V+E)\log V)$ with a binary heap — identical to Dijkstra, and for the same reason (priority queue on edges).

---

Here is `10_04_02_Kruskals_Algorithm.md`:

---

# Kruskal's Algorithm

Where Prim's grows the MST from a single vertex, **Kruskal's** builds it by sorting all edges and greedily adding the cheapest edge that does not create a cycle.

> **Definition.** _Kruskal's algorithm_ sorts all edges by weight, then adds each edge to the MST if it connects two previously disconnected components (does not form a cycle). Uses Union-Find to efficiently check connectivity.

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

struct Edge {
    int u, v, w;
    bool operator<(const Edge& other) const { return w < other.w; }
};

// Union-Find
struct UnionFind {
    vector<int> parent, rank;
    UnionFind(int n) : parent(n), rank(n, 0) {
        iota(parent.begin(), parent.end(), 0);
    }
    int find(int x) {
        if (parent[x] != x) parent[x] = find(parent[x]);
        return parent[x];
    }
    bool unite(int x, int y) {
        int px = find(x), py = find(y);
        if (px == py) return false;  // same component — would form cycle
        if (rank[px] < rank[py]) swap(px, py);
        parent[py] = px;
        if (rank[px] == rank[py]) rank[px]++;
        return true;
    }
};

// Time: O(E log E), Space: O(V)
int kruskals(vector<Edge>& edges, int V) {
    sort(edges.begin(), edges.end());  // sort by weight
    UnionFind uf(V);
    int mstWeight = 0, edgesAdded = 0;

    for (auto& [u, v, w] : edges) {
        if (uf.unite(u, v)) {
            mstWeight += w;
            edgesAdded++;
            if (edgesAdded == V - 1) break;  // MST complete
        }
    }
    return mstWeight;
}

int main() {
    vector<Edge> edges = {
        {0,1,2},{0,3,6},{1,2,3},{1,3,8},{1,4,5},{2,4,7},{3,4,9}
    };
    cout << "MST weight: " << kruskals(edges, 5) << endl;  // 16
    return 0;
}
```

---

## Prim's vs Kruskal's

||Prim's|Kruskal's|
|---|---|---|
|Approach|Grow tree from vertex|Sort edges globally|
|Data structure|Priority queue|Union-Find + sort|
|Time|$O((V+E)\log V)$|$O(E \log E)$|
|Best for|Dense graphs|Sparse graphs|
|Requires connected|Yes|No (finds MST forest)|

For dense graphs ($E \approx V^2$), $E\log E \approx V^2 \log V$ while Prim's is $O(V^2\log V)$ — similar. For sparse graphs ($E \approx V$), Kruskal's $O(E\log E) = O(V\log V)$ beats Prim's $O(V\log V)$ by constant factors.

---

Here is `10_04_03_Union_Find.md`:

---

# Union-Find (Disjoint Set Union)

Union-Find was used in Kruskal's algorithm — here we study it properly as a standalone data structure.

> **Definition.** _Union-Find_ (Disjoint Set Union, DSU) maintains a collection of disjoint sets supporting two operations: **find** (which set does element $x$ belong to?) and **union** (merge the sets containing $x$ and $y$).

```cpp
#include <iostream>
#include <vector>
using namespace std;

class UnionFind {
    vector<int> parent, rank;
    int components;

public:
    UnionFind(int n) : parent(n), rank(n, 0), components(n) {
        iota(parent.begin(), parent.end(), 0);
    }

    // Find with path compression — O(alpha(n)) amortized
    int find(int x) {
        if (parent[x] != x)
            parent[x] = find(parent[x]);  // path compression
        return parent[x];
    }

    // Union by rank — O(alpha(n)) amortized
    bool unite(int x, int y) {
        int px = find(x), py = find(y);
        if (px == py) return false;
        if (rank[px] < rank[py]) swap(px, py);
        parent[py] = px;
        if (rank[px] == rank[py]) rank[px]++;
        components--;
        return true;
    }

    bool connected(int x, int y) { return find(x) == find(y); }
    int countComponents() { return components; }
};
```

---

## Path Compression + Union by Rank

Two optimizations together give nearly constant amortized time:

**Path compression** — when finding the root, make every node on the path point directly to the root. Future finds on the same path are $O(1)$.

**Union by rank** — always attach the smaller tree under the root of the larger tree. Keeps trees shallow.

Together: amortized $O(\alpha(n))$ per operation where $\alpha$ is the inverse Ackermann function — effectively $O(1)$ for all practical values of $n$ ($\alpha(n) \leq 4$ for $n < 10^{600}$).

---

## Applications

- **Kruskal's MST** — check if adding an edge creates a cycle
- **Connected components** — count components in $O((V+E)\alpha(V))$
- **Online connectivity** — dynamically add edges and query connectivity
- **Percolation** — grid connectivity problems

```cpp
int main() {
    UnionFind uf(6);
    uf.unite(0,1); uf.unite(1,2);
    uf.unite(3,4);
    cout << uf.countComponents() << endl;  // 3: {0,1,2}, {3,4}, {5}
    cout << uf.connected(0,2) << endl;     // 1
    cout << uf.connected(0,3) << endl;     // 0
    uf.unite(2,3);
    cout << uf.countComponents() << endl;  // 2: {0,1,2,3,4}, {5}
    return 0;
}
```

**Complexity:** $O(\alpha(n))$ per operation — essentially $O(1)$.

---

Here is `10_05_01_Topological_Sort.md`:

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

Here is `10_05_02_Longest_Path_in_DAG.md`:

---

# Longest Path in DAG

In a general graph, the longest path problem is NP-hard. In a **DAG**, it can be solved in $O(V + E)$ using topological sort and dynamic programming.

> **Definition.** The _longest path_ in a DAG from source $s$ is the maximum-weight path from $s$ to any other vertex. It is computed by processing vertices in topological order and relaxing edges in the "maximize" direction instead of minimize.

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;

typedef pair<int,int> pii;

// Longest path from source in weighted DAG
// Time: O(V + E), Space: O(V)
vector<int> longestPath(int source, vector<vector<pii>>& adj,
                         vector<int>& topoOrder, int V) {
    vector<int> dist(V, INT_MIN);
    dist[source] = 0;

    for (int u : topoOrder) {
        if (dist[u] != INT_MIN) {
            for (auto [v, w] : adj[u]) {
                if (dist[u] + w > dist[v])
                    dist[v] = dist[u] + w;
            }
        }
    }
    return dist;
}

int main() {
    int V = 6;
    vector<vector<pii>> adj(V);
    adj[0].push_back({1,5}); adj[0].push_back({2,3});
    adj[1].push_back({3,6}); adj[1].push_back({2,2});
    adj[2].push_back({4,4}); adj[2].push_back({5,2}); adj[2].push_back({3,7});
    adj[3].push_back({4,-1}); adj[3].push_back({5,1});
    adj[4].push_back({5,-2});

    // Get topological order first
    vector<vector<int>> adjUnweighted(V);
    for (int u = 0; u < V; u++)
        for (auto [v,w] : adj[u])
            adjUnweighted[u].push_back(v);

    vector<bool> visited(V, false);
    vector<int> topoOrder;
    function<void(int)> dfs = [&](int u) {
        visited[u] = true;
        for (int v : adjUnweighted[u])
            if (!visited[v]) dfs(v);
        topoOrder.push_back(u);
    };
    for (int i = 0; i < V; i++)
        if (!visited[i]) dfs(i);
    reverse(topoOrder.begin(), topoOrder.end());

    auto dist = longestPath(1, adj, topoOrder, V);
    cout << "Longest from 1 to 5: " << dist[5] << endl;  // 9
    return 0;
}
```

**Complexity:** $O(V + E)$ — topological sort plus one pass over all edges.

---

Here is `10_06_01_Strongly_Connected_Components.md`:

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

Here is `10_07_01_Bipartite_Check.md`:

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

Here is `10_07_02_Cycle_Detection.md`:

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

Here is `10_07_03_Bridges_and_Articulation_Points.md`:

---

# Bridges and Articulation Points

> **Definition.** A _bridge_ is an edge in an undirected graph whose removal increases the number of connected components — it is critical for connectivity.

> **Definition.** An _articulation point_ (cut vertex) is a vertex whose removal increases the number of connected components.

Both are found using DFS with **low values** — the earliest discovery time reachable from a subtree.

```cpp
#include <iostream>
#include <vector>
using namespace std;

class BridgesAndAPs {
    int V, timer = 0;
    vector<vector<int>>& adj;
    vector<int> disc, low, parent;
    vector<bool> visited, isAP;
    vector<pair<int,int>> bridges;

    void dfs(int u) {
        visited[u] = true;
        disc[u] = low[u] = timer++;
        int children = 0;

        for (int v : adj[u]) {
            if (!visited[v]) {
                children++;
                parent[v] = u;
                dfs(v);

                low[u] = min(low[u], low[v]);

                // Articulation point conditions
                if (parent[u] == -1 && children > 1) isAP[u] = true;
                if (parent[u] != -1 && low[v] >= disc[u]) isAP[u] = true;

                // Bridge condition
                if (low[v] > disc[u])
                    bridges.push_back({u, v});

            } else if (v != parent[u]) {
                low[u] = min(low[u], disc[v]);
            }
        }
    }

public:
    BridgesAndAPs(vector<vector<int>>& adj, int V)
        : adj(adj), V(V), disc(V), low(V), parent(V,-1),
          visited(V,false), isAP(V,false) {}

    void find() {
        for (int i = 0; i < V; i++)
            if (!visited[i]) dfs(i);
    }

    void printBridges() {
        cout << "Bridges:" << endl;
        for (auto [u,v] : bridges)
            cout << u << "-" << v << endl;
    }

    void printAPs() {
        cout << "Articulation Points:" << endl;
        for (int i = 0; i < V; i++)
            if (isAP[i]) cout << i << endl;
    }
};

int main() {
    int V = 5;
    vector<vector<int>> adj(V);
    auto addEdge = [&](int u, int v) {
        adj[u].push_back(v); adj[v].push_back(u);
    };
    addEdge(1,0); addEdge(0,2); addEdge(2,1);
    addEdge(0,3); addEdge(3,4);

    BridgesAndAPs bap(adj, V);
    bap.find();
    bap.printBridges();  // 3-4, 0-3
    bap.printAPs();      // 0, 3
    return 0;
}
```

---

## Low Value Intuition

$\text{low}[u]$ = earliest discovery time reachable from the subtree rooted at $u$ via back edges.

- **Bridge** $(u,v)$: $\text{low}[v] > \text{disc}[u]$ — the subtree at $v$ cannot reach $u$ or earlier without the edge $(u,v)$
- **AP** (non-root): $\text{low}[v] \geq \text{disc}[u]$ — removing $u$ disconnects $v$'s subtree

**Complexity:** $O(V + E)$ — single DFS pass.

---

Now the quiz. Here is `10_QQ_Graphs_Quiz.md`:

---

## Quiz — Graphs

**Q1 — Representation Choice** You have a social network with 1 million users and 10 million friendships. Which representation and why?

> [!answer]- Answer Adjacency list. With V=10^6 and E=10^7, an adjacency matrix would require 10^12 entries — 4 terabytes for integers. The adjacency list uses O(V+E) = O(11 million) space — entirely practical. Also E << V^2 (10^7 << 10^12) — this is a sparse graph. Adjacency matrix is only appropriate when E is close to V^2.

**Q2 — BFS Trace** Run BFS from vertex 0 on: 0: [1, 2], 1: [0, 3, 4], 2: [0, 4], 3: [1], 4: [1, 2] List the order vertices are visited and the distance of each from 0.

> [!answer]- Answer Queue trace: push 0, visited={0}. Pop 0, push 1,2: queue=[1,2], visited={0,1,2}. Pop 1, push 3,4: queue=[2,3,4]. Pop 2, 4 already visited: queue=[3,4]. Pop 3, 1 visited: queue=[4]. Pop 4, all visited: done. Order: 0,1,2,3,4. Distances: d[0]=0, d[1]=1, d[2]=1, d[3]=2, d[4]=2.

**Q3 — Dijkstra by Hand** Graph: 0→1 (weight 4), 0→2 (weight 1), 2→1 (weight 2), 1→3 (weight 1), 2→3 (weight 5). Find shortest paths from 0 to all vertices.

> [!answer]- Answer Init: dist=[0,INF,INF,INF]. PQ={(0,0)}. Pop (0,0): relax 1→dist[1]=4, relax 2→dist[2]=1. PQ={(1,2),(4,1)}. Pop (1,2): relax 1→dist[1]=min(4,1+2)=3 update, relax 3→dist[3]=6. PQ={(3,1),(4,1),(6,3)}. Pop (3,1): relax 3→dist[3]=min(6,3+1)=4 update. PQ={(4,1),(6,3),(4,3)}. Pop (4,1): stale (dist[1]=3 < 4), skip. Pop (4,3): dist[3]=4, no outgoing. Done. Final: dist=[0, 3, 1, 4]. Paths: 0→2 (cost 1), 0→2→1 (cost 3), 0→2→1→3 (cost 4).

**Q4 — Negative Edge** Why does Dijkstra fail on graphs with negative edges? Give a concrete counterexample.

> [!answer]- Answer Dijkstra finalizes a vertex's distance when it is popped from the priority queue, assuming no future path can be shorter. With negative edges this assumption breaks. Counterexample: 0→1 (weight 3), 0→2 (weight 2), 2→1 (weight -2). Dijkstra pops 2 (dist=2), then pops 1 with dist=3 and finalizes it. But the actual shortest path is 0→2→1 with cost 2+(-2)=0. Dijkstra gives wrong answer 3. Bellman-Ford correctly gives 0 by relaxing all edges V-1=2 times.

**Q5 — Topological Sort** Courses: A needs nothing, B needs A, C needs A, D needs B and C, E needs D. Give a valid topological order and the in-degrees of each course.

> [!answer]- Answer In-degrees: A=0, B=1(A), C=1(A), D=2(B,C), E=1(D). Kahn's: start with A (in-degree 0). Remove A, reduce B and C to in-degree 0. Process B or C (say B), reduce D to 1. Process C, reduce D to 0. Process D, reduce E to 0. Process E. Valid order: A, B, C, D, E (or A, C, B, D, E).

**Q6 — MST by Hand** Edges: (0,1,4), (0,2,3), (1,2,1), (1,3,2), (2,3,4), (3,4,2), (1,4,5). Find the MST using Kruskal's.

> [!answer]- Answer Sort edges: (1,2,1), (1,3,2), (3,4,2), (0,2,3), (0,1,4), (2,3,4), (1,4,5). Add (1,2,1): MST={1-2}. UF: {0},{1,2},{3},{4}. Add (1,3,2): MST={1-2,1-3}. UF: {0},{1,2,3},{4}. Add (3,4,2): MST={1-2,1-3,3-4}. UF: {0},{1,2,3,4}. Add (0,2,3): MST={1-2,1-3,3-4,0-2}. UF: {0,1,2,3,4}. 4 edges for 5 vertices — done. MST weight = 1+2+2+3 = 8.

**Q7 — SCC** Directed graph: 0→1, 1→2, 2→0, 1→3, 3→4, 4→5, 5→3, 6→5, 6→7, 7→6. Identify the SCCs.

> [!answer]- Answer SCC 1: {0,1,2} — 0→1→2→0 is a cycle. SCC 2: {3,4,5} — 3→4→5→3 is a cycle. SCC 3: {6,7} — 6→7→6 is a cycle. Note: 6 can reach {3,4,5} but {3,4,5} cannot reach 6, so they are separate SCCs. Every vertex in an SCC must be able to reach every other — cycles define SCCs.

**Q8 — Write it Yourself** Implement flood fill — given a 2D grid, a starting cell, and a new color, fill all connected cells of the same original color with the new color (like the paint bucket tool in image editors).

> [!answer]- Answer
> 
> ```cpp
> void floodFill(vector<vector<int>>& grid, int r, int c, int newColor) {
>     int oldColor = grid[r][c];
>     if (oldColor == newColor) return;
>     int rows = grid.size(), cols = grid[0].size();
>     queue<pair<int,int>> q;
>     q.push({r,c});
>     grid[r][c] = newColor;
>     int dr[] = {0,0,1,-1};
>     int dc[] = {1,-1,0,0};
>     while (!q.empty()) {
>         auto [row,col] = q.front(); q.pop();
>         for (int d = 0; d < 4; d++) {
>             int nr = row+dr[d], nc = col+dc[d];
>             if (nr>=0 && nr<rows && nc>=0 && nc<cols
>                 && grid[nr][nc] == oldColor) {
>                 grid[nr][nc] = newColor;
>                 q.push({nr,nc});
>             }
>         }
>     }
> }
> ```
> 
> This is BFS on a grid graph. Each cell is a vertex, adjacent cells (4-directional) are neighbors. Time: O(rows * cols), Space: O(rows * cols) for the queue.

---

And the index files. `10_00_Graphs.md`:

---

# Graphs

## Subfolders

- [[10_01_00_Graph_Basics]]
- [[10_02_00_Traversal]]
- [[10_03_00_Shortest_Paths]]
- [[10_04_00_Spanning_Trees]]
- [[10_05_00_DAGs]]
- [[10_06_00_SCCs]]
- [[10_07_00_Advanced]]

## Quiz

- [[10_QQ_Graphs_Quiz]]

---

## Quick Reference

### Graph Basics

$$G = (V, E), \quad |V| = n, \quad |E| = m$$ $$\sum_{v} \deg(v) = 2|E| \quad \text{(Handshaking Lemma)}$$

|Representation|Space|Edge check|Neighbor iteration|
|---|---|---|---|
|Adjacency Matrix|$O(n^2)$|$O(1)$|$O(n)$|
|Adjacency List|$O(n+m)$|$O(\deg)$|$O(\deg)$|

---

### Traversals

||BFS|DFS|
|---|---|---|
|Structure|Queue|Stack / recursion|
|Shortest path|Yes (unweighted)|No|
|Cycle detection|Yes|Yes|
|Topological sort|Kahn's|Finish-time stack|
|Time|$O(V+E)$|$O(V+E)$|

---

### Shortest Paths

|Algorithm|Source|Negative|Time|
|---|---|---|---|
|BFS|Single|No (unweighted)|$O(V+E)$|
|Dijkstra|Single|No|$O((V+E)\log V)$|
|Bellman-Ford|Single|Yes|$O(VE)$|
|Floyd-Warshall|All pairs|Yes|$O(V^3)$|

---

### MST

||Prim's|Kruskal's|
|---|---|---|
|Data structure|Priority queue|Sort + Union-Find|
|Time|$O((V+E)\log V)$|$O(E\log E)$|
|Best for|Dense|Sparse|

Cut property: minimum weight edge crossing any cut belongs to some MST.

---

### Key Formulas

$$\text{Bellman-Ford: relax all edges } V-1 \text{ times}$$ $$\text{Floyd-Warshall: } dp[i][j] = \min(dp[i][j],\ dp[i][k]+dp[k][j])$$ $$\text{Topo sort exists} \iff \text{DAG (no directed cycle)}$$ $$\text{Bipartite} \iff \text{no odd cycle} \iff \text{2-colorable}$$ $$\text{Bridge: } low[v] > disc[u], \quad \text{AP: } low[v] \geq disc[u]$$

---

Subfolder indexes:

`10_01_00_Graph_Basics.md`:

# Graph Basics

## Notes

- [[10_01_01_Representations_Adjacency_List_Matrix]]
- [[10_01_02_Directed_vs_Undirected_Weighted]]

`10_02_00_Traversal.md`:

# Graph Traversal

## Notes

- [[10_02_01_BFS_and_Connected_Components]]
- [[10_02_02_DFS_Directed_and_Undirected]]

`10_03_00_Shortest_Paths.md`:

# Shortest Paths

## Notes

- [[10_03_01_Dijkstras_Algorithm]]
- [[10_03_02_Shortest_Path_Tree]]
- [[10_03_03_Bellman_Ford]]
- [[10_03_04_Floyd_Warshall]]

`10_04_00_Spanning_Trees.md`:

# Spanning Trees

## Notes

- [[10_04_01_Prims_Algorithm]]
- [[10_04_02_Kruskals_Algorithm]]
- [[10_04_03_Union_Find]]

`10_05_00_DAGs.md`:

# DAGs

## Notes

- [[10_05_01_Topological_Sort]]
- [[10_05_02_Longest_Path_in_DAG]]

`10_06_00_SCCs.md`:

# Strongly Connected Components

## Notes

- [[10_06_01_Strongly_Connected_Components]]

`10_07_00_Advanced.md`:

# Advanced Graph Topics

## Notes

- [[10_07_01_Bipartite_Check]]
- [[10_07_02_Cycle_Detection]]
- [[10_07_03_Bridges_and_Articulation_Points]]

---

Now Pattern Matching. Here is `11_01_Naive_Pattern_Matching.md`:

---

# Naive Pattern Matching

Given a text $T$ of length $n$ and a pattern $P$ of length $m$, find all positions in $T$ where $P$ occurs. The naive approach tries every possible alignment.

> **Definition.** _Naive pattern matching_ slides the pattern across the text one position at a time, checking for a match at each position. It requires $O((n-m+1) \cdot m)$ comparisons in the worst case.

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

// Time: O((n-m+1)*m) worst, Space: O(1)
vector<int> naiveSearch(const string& text, const string& pattern) {
    int n = text.size(), m = pattern.size();
    vector<int> matches;

    for (int i = 0; i <= n - m; i++) {
        int j = 0;
        while (j < m && text[i+j] == pattern[j]) j++;
        if (j == m) matches.push_back(i);
    }
    return matches;
}

int main() {
    string text = "AABAACAADAABAABA";
    string pat  = "AABA";
    auto pos = naiveSearch(text, pat);
    for (int i : pos) cout << i << " ";  // 0 9 12
    return 0;
}
```

---

## When Naive is $O(nm)$

The worst case is when both text and pattern consist of repeated characters — e.g., text = "AAAAAAA" and pattern = "AAAB". Every position requires checking $m-1$ characters before failing. This motivates KMP.

**Complexity:** $O(nm)$ worst, $O(n)$ best (pattern not found early).

---

Here is `11_02_KMP_Algorithm.md`:

---

# KMP Algorithm

The naive algorithm wastes work — when a mismatch occurs, it discards all information about the characters already matched and starts fresh. **KMP (Knuth-Morris-Pratt)** avoids this by precomputing a **failure function** that tells us how far back to shift the pattern without rechecking already-matched characters.

> **Definition.** The _KMP failure function_ (also called the partial match table or $\pi$ array) for pattern $P$ gives, for each position $i$, the length of the longest proper prefix of $P[0..i]$ that is also a suffix.

> **Definition.** _KMP pattern matching_ uses the failure function to skip redundant comparisons after a mismatch — never moving the text pointer backward.

---

## Computing the Failure Function

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

// Failure function — O(m) time and space
vector<int> computeFailure(const string& pattern) {
    int m = pattern.size();
    vector<int> fail(m, 0);
    int j = 0;  // length of previous longest prefix-suffix

    for (int i = 1; i < m; i++) {
        while (j > 0 && pattern[i] != pattern[j])
            j = fail[j-1];  // fall back
        if (pattern[i] == pattern[j]) j++;
        fail[i] = j;
    }
    return fail;
}
```

Example: pattern = "AABAAB"

|Index|0|1|2|3|4|5|
|---|---|---|---|---|---|---|
|Char|A|A|B|A|A|B|
|fail|0|1|0|1|2|3|

$\text{fail}[5] = 3$ means "AAB" is both a prefix and suffix of "AABAAB".

---

## KMP Search

```cpp
// Time: O(n + m), Space: O(m)
vector<int> kmpSearch(const string& text, const string& pattern) {
    int n = text.size(), m = pattern.size();
    vector<int> fail = computeFailure(pattern);
    vector<int> matches;
    int j = 0;  // characters matched so far

    for (int i = 0; i < n; i++) {
        while (j > 0 && text[i] != pattern[j])
            j = fail[j-1];  // fall back using failure function
        if (text[i] == pattern[j]) j++;
        if (j == m) {
            matches.push_back(i - m + 1);
            j = fail[j-1];  // look for next match
        }
    }
    return matches;
}

int main() {
    string text    = "AABAACAADAABAABA";
    string pattern = "AABA";
    auto pos = kmpSearch(text, pattern);
    for (int i : pos) cout << i << " ";  // 0 9 12
    return 0;
}
```

---

## Why O(n + m)

The text pointer $i$ never moves backward. The pattern pointer $j$ can only decrease as much as it increases — total increases bounded by $n$, total decreases bounded by $n$. Combined with $O(m)$ preprocessing: $O(n+m)$ total.

**Complexity:** $O(n+m)$ time, $O(m)$ space.

---

Here is `11_03_Rabin_Karp.md`:

---

# Rabin-Karp Algorithm

KMP avoids redundant character comparisons using the failure function. **Rabin-Karp** takes a different approach — use **hashing** to quickly check if a window of the text matches the pattern. If hashes match, verify with a direct comparison.

> **Definition.** _Rabin-Karp_ computes a rolling hash of each window of the text and compares it to the pattern hash. If hashes match, it verifies character by character. The rolling hash updates in $O(1)$ per step.

The rolling hash is what makes this efficient — instead of recomputing the hash from scratch for each new window, subtract the contribution of the outgoing character and add the contribution of the incoming character.

$$h_{\text{new}} = (h_{\text{old}} - \text{text}[i] \cdot p^{m-1}) \cdot p + \text{text}[i+m]$$

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

// Time: O(n+m) average, O(nm) worst (hash collisions), Space: O(1)
vector<int> rabinKarp(const string& text, const string& pattern) {
    int n = text.size(), m = pattern.size();
    const long long MOD = 1e9 + 7, BASE = 31;
    vector<int> matches;

    // Compute BASE^(m-1) % MOD
    long long highPow = 1;
    for (int i = 0; i < m-1; i++) highPow = highPow * BASE % MOD;

    // Compute pattern hash and first window hash
    long long patHash = 0, winHash = 0;
    for (int i = 0; i < m; i++) {
        patHash = (patHash * BASE + pattern[i]) % MOD;
        winHash = (winHash * BASE + text[i])   % MOD;
    }

    for (int i = 0; i <= n - m; i++) {
        if (winHash == patHash) {
            // Hash match — verify to avoid false positives
            if (text.substr(i, m) == pattern)
                matches.push_back(i);
        }
        // Roll hash — remove text[i], add text[i+m]
        if (i < n - m) {
            winHash = (winHash - text[i] * highPow % MOD + MOD) % MOD;
            winHash = (winHash * BASE + text[i+m]) % MOD;
        }
    }
    return matches;
}

int main() {
    string text = "GEEKS FOR GEEKS";
    string pat  = "GEEKS";
    auto pos = rabinKarp(text, pat);
    for (int i : pos) cout << i << " ";  // 0 10
    return 0;
}
```

---

## Hash Collisions

A **spurious hit** (false positive) is when two different strings have the same hash. The verification step handles this — but if many spurious hits occur, the algorithm degrades to $O(nm)$. Using a large prime modulus makes collisions rare.

**Double hashing** — use two different hash functions. The probability of both giving a false positive simultaneously is negligible.

---

## KMP vs Rabin-Karp

||KMP|Rabin-Karp|
|---|---|---|
|Time|$O(n+m)$ always|$O(n+m)$ average|
|Space|$O(m)$|$O(1)$|
|Multiple patterns|Hard|Easy — hash all patterns|
|2D pattern matching|Hard|Possible|

Rabin-Karp shines for **multiple pattern matching** — hash all patterns into a set, then a single pass through the text checks all patterns simultaneously in $O(n)$ expected time.

---

Here is `11_04_Z_Algorithm.md`:

---

# Z Algorithm

The Z algorithm computes, for each position in a string, the length of the longest substring starting from that position that matches a prefix of the string. This single array unlocks efficient pattern matching and many string problems.

> **Definition.** For a string $S$ of length $n$, the _Z array_ $Z[i]$ is the length of the longest substring starting from $S[i]$ that is also a prefix of $S$. $Z[0]$ is undefined (or set to $n$).

For $S$ = "AABXAABAAB":

|Index|0|1|2|3|4|5|6|7|8|9|
|---|---|---|---|---|---|---|---|---|---|---|
|Char|A|A|B|X|A|A|B|A|A|B|
|Z|-|1|0|0|3|1|0|6|1|0|

$Z[7] = 6$ means $S[7..12]$ = "AABXAA"... wait, $S[7..9]$ = "AAB" and that matches $S[0..2]$ = "AAB" — so $Z[7] = 3$.

---

## Computing Z Array

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

// Time: O(n), Space: O(n)
vector<int> zFunction(const string& s) {
    int n = s.size();
    vector<int> z(n, 0);
    z[0] = n;
    int l = 0, r = 0;  // [l, r) = rightmost Z-box

    for (int i = 1; i < n; i++) {
        if (i < r)
            z[i] = min(r - i, z[i - l]);  // use previously computed info

        while (i + z[i] < n && s[z[i]] == s[i + z[i]])
            z[i]++;

        if (i + z[i] > r) { l = i; r = i + z[i]; }
    }
    return z;
}
```

The [l, r) window is the Z-box — the rightmost interval $[l, r)$ such that $s[l..r-1]$ is a prefix of $s$. When $i$ is inside the Z-box, we can initialize $z[i]$ from $z[i-l]$ to avoid redundant comparisons.

---

## Pattern Matching with Z Algorithm

Concatenate pattern + "$" + text (sentinel "$" ensures no Z value crosses the boundary), compute Z array, find positions where $Z[i] \geq m$.

```cpp
// Time: O(n + m), Space: O(n + m)
vector<int> zSearch(const string& text, const string& pattern) {
    string s = pattern + "$" + text;
    auto z = zFunction(s);
    int m = pattern.size();
    vector<int> matches;

    for (int i = m + 1; i < s.size(); i++)
        if (z[i] >= m)
            matches.push_back(i - m - 1);  // position in original text

    return matches;
}

int main() {
    string text    = "AABAACAADAABAABA";
    string pattern = "AABA";
    auto pos = zSearch(text, pattern);
    for (int i : pos) cout << i << " ";  // 0 9 12
    return 0;
}
```

---

## Z vs KMP

Both achieve $O(n+m)$ pattern matching. The Z algorithm is often considered simpler to implement and reason about. KMP's failure function has a deeper connection to the structure of the pattern, while Z values are more directly interpretable.

**Complexity:** $O(n)$ to build, $O(n+m)$ for pattern matching.

---

Here is `11_QQ_Pattern_Matching_Quiz.md`:

---

## Quiz — Pattern Matching

**Q1 — Naive Worst Case** What is the worst case input for naive pattern matching and why?

> [!answer]- Answer Text = "AAAAAAA...A" (n A's) and pattern = "AAA...AB" (m-1 A's followed by B). At every position i, the algorithm matches m-1 characters before failing on the last one. Total comparisons = (n-m+1)*(m-1) = O(nm). This motivates KMP — which would recognize after the first mismatch that the entire matched prefix "AAA...A" is both a prefix and suffix, avoiding re-scanning.

**Q2 — Failure Function** Compute the KMP failure function for pattern "ABCABCABC".

> [!answer]- Answer Index: 0 1 2 3 4 5 6 7 8 Pattern: A B C A B C A B C fail: 0 0 0 1 2 3 4 5 6 At index 3, 'A' matches pattern[0] so fail[3]=1. At index 4, 'B' matches pattern[1] so fail[4]=2. Continue: fail[5]=3, fail[6]=4 ("ABCA" is prefix=suffix "ABCA" of "ABCABC"), fail[7]=5, fail[8]=6.

**Q3 — KMP Trace** Use KMP to find pattern "ABA" in text "ABABABABAB". Show j values at each step.

> [!answer]- Answer fail for "ABA": [0, 0, 1]. i=0, A==A, j=1. i=1, B==B, j=2. i=2, A==A, j=3=m → match at 0, j=fail[2]=1. i=3, B==B, j=2. i=4, A==A, j=3=m → match at 2, j=fail[2]=1. i=5, B==B, j=2. i=6, A==A, j=3=m → match at 4, j=1. i=7, B==B, j=2. i=8, A==A, j=3=m → match at 6, j=1. Matches at positions 0, 2, 4, 6.

**Q4 — Z Array** Compute the Z array for "AABAABAAB".

> [!answer]- Answer S = A A B A A B A A B (length 9). Z[0] = 9 (whole string). Z[1]: s[1]='A'=s[0], s[2]='B'≠s[1]='A' → Z[1]=1. Z[2]: s[2]='B'≠s[0]='A' → Z[2]=0. Z[3]: s[3..8]="AABAAB", matches prefix "AABAA..." — s[3]='A'=s[0], s[4]='A'=s[1], s[5]='B'=s[2], s[6]='A'=s[3], s[7]='A'=s[4], s[8]='B'=s[5] → Z[3]=6. Z[4]=1 (within Z-box, use Z[1]=1, verify). Z[5]=0. Z[6]=3 (within Z-box, use Z[3] clipped to r-6=3). Z[7]=1. Z[8]=0. Z = [9, 1, 0, 6, 1, 0, 3, 1, 0].

**Q5 — Rabin-Karp** Why does Rabin-Karp degrade to O(nm) and how do you prevent it?

> [!answer]- Answer Rabin-Karp degrades when there are many spurious hits — positions where the hash matches but the characters don't. In the worst case (text = "AAAA...A", pattern = "AAA...A" with a different last char but same hash) every position is a hash match requiring O(m) verification. Total = O(nm). Prevention: use double hashing (two independent hash functions). A spurious hit in both simultaneously has probability ~1/MOD^2 ≈ 10^-18 — negligible. Also choose a large prime modulus and a good base.

**Q6 — Write it Yourself** Implement the Z algorithm from scratch and use it to count how many times pattern "ab" appears in text "ababab".

> [!answer]- Answer s = "ab$ababab". Z array computation: Z[0]=8 (length). Z[1]: 'b'≠'a' → 0. Z[2]: '$'≠'a' → 0. Z[3]: 'a'='a','b'='b','$'≠'a' → Z[3]=2=m → match at 3-3=0. Z[4]: use Z-box, Z[4]=1 (b≠a). Z[5]: 'a','b','a'... Z[5]=4? No — check s[5]='a'=s[0], s[6]='b'=s[1], s[7]='a'=s[2]='$'? No s[2]='$'≠s[7]='a'. Z[5]=2 → match at 5-3=2. Z[6]=1. Z[7]=2 → match at 7-3=4. Matches at positions 0, 2, 4 — 3 occurrences.

---

And `11_00_Pattern_Matching.md`:

---

# Pattern Matching

## Notes

- [[11_01_Naive_Pattern_Matching]]
- [[11_02_KMP_Algorithm]]
- [[11_03_Rabin_Karp]]
- [[11_04_Z_Algorithm]]

## Quiz

- [[11_QQ_Pattern_Matching_Quiz]]

---

## Quick Reference

### Algorithm Comparison

|Algorithm|Time|Space|Key idea|
|---|---|---|---|
|Naive|$O(nm)$|$O(1)$|Try every position|
|KMP|$O(n+m)$|$O(m)$|Failure function skips re-comparisons|
|Rabin-Karp|$O(n+m)$ avg|$O(1)$|Rolling hash|
|Z Algorithm|$O(n+m)$|$O(n+m)$|Z array prefix matching|

### KMP Failure Function

$\text{fail}[i]$ = length of longest proper prefix of $P[0..i]$ that is also a suffix.

On mismatch at position $j$ in pattern: set $j = \text{fail}[j-1]$ and retry.

### Rabin-Karp Rolling Hash

$$h_{\text{new}} = ((h_{\text{old}} - T[i] \cdot p^{m-1}) \cdot p + T[i+m]) \mod M$$

### Z Algorithm Pattern Matching

Concatenate $P + $ + T$, find positions where $Z[i] \geq m$.

$$Z[i] \geq m \implies \text{match at position } i - m - 1 \text{ in } T$$

---