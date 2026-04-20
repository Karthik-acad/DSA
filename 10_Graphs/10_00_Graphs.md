
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
