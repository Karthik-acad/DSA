
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
