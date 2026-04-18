
---

# BFS Queue Usage

**Breadth First Search** is the most algorithmically significant application of queues. It explores a graph (or tree) level by level — first all nodes at distance 1 from the source, then distance 2, and so on. A queue is what enforces this order naturally.

> **Definition.** _Breadth First Search_ is a graph traversal algorithm that explores all nodes at the current depth before moving to nodes at the next depth, using a queue to maintain the order of exploration.

This note covers BFS as a queue pattern. The full graph theory treatment — connected components, shortest paths, and BFS on various graph types — is in [[10_02_01_BFS_and_Connected_Components]].

---

## BFS on a Tree

```cpp
#include <iostream>
#include <queue>
using namespace std;

struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

// Level order traversal — Time: O(n), Space: O(n)
void bfs(TreeNode* root) {
    if (!root) return;
    queue<TreeNode*> q;
    q.push(root);

    while (!q.empty()) {
        TreeNode* node = q.front(); q.pop();
        cout << node->val << " ";

        if (node->left)  q.push(node->left);
        if (node->right) q.push(node->right);
    }
}
```

---

## BFS on a Graph

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

// Time: O(V + E), Space: O(V)
void bfsGraph(int start, vector<vector<int>>& adj, int V) {
    vector<bool> visited(V, false);
    queue<int> q;

    visited[start] = true;
    q.push(start);

    while (!q.empty()) {
        int node = q.front(); q.pop();
        cout << node << " ";

        for (int neighbor : adj[node]) {
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                q.push(neighbor);
            }
        }
    }
}
```

---

## Why a Queue — Not a Stack

The visited array prevents revisiting nodes. But the _order_ of traversal is determined entirely by the data structure:

|Data Structure|Traversal order|Algorithm|
|---|---|---|
|Queue (FIFO)|Level by level, nearest first|BFS|
|Stack (LIFO)|Deep before wide, one branch at a time|DFS|

If you replaced the queue with a stack in the BFS code above, you would get DFS. The queue is what guarantees that nodes closer to the source are processed before nodes farther away — which is exactly what makes BFS the right tool for **shortest path** in unweighted graphs.

**Complexity:** Time $O(V + E)$ — each vertex is enqueued once ($O(V)$) and each edge is examined once ($O(E)$). Space $O(V)$ for the queue and visited array.

> 📖 For full BFS theory and shortest paths: [[10_02_01_BFS_and_Connected_Components]] 📖 For BFS visualized: [Visualgo — BFS/DFS](https://visualgo.net/en/dfsbfs)

---
