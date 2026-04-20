
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
