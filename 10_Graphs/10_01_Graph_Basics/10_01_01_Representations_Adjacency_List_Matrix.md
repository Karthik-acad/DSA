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

Here is `10_05_01_Topological_Sort.md`:

Here is `10_05_02_Longest_Path_in_DAG.md`:

Here is `10_06_01_Strongly_Connected_Components.md`:

Here is `10_07_01_Bipartite_Check.md`:

Here is `10_07_02_Cycle_Detection.md`:

Here is `10_07_03_Bridges_and_Articulation_Points.md`:

Now the quiz. Here is `10_QQ_Graphs_Quiz.md`:

Now Pattern Matching. Here is `11_01_Naive_Pattern_Matching.md`:

Here is `11_02_KMP_Algorithm.md`:

Here is `11_03_Rabin_Karp.md`:

Here is `11_04_Z_Algorithm.md`:

Here is `11_QQ_Pattern_Matching_Quiz.md`:
