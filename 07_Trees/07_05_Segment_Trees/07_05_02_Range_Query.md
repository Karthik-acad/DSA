
---

# Segment Tree — Range Query

With the tree built, a range query $[l, r]$ traverses the tree — if a node's segment is fully within $[l, r]$, return its value. If fully outside, return the identity element. If partial overlap, recurse on both children.

> **Definition.** A _range query_ on a segment tree runs in $O(\log n)$ by visiting at most $4\log n$ nodes — each level contributes at most 4 nodes to the answer.

```cpp
    // Range sum query [l, r] — O(log n)
    int query(int node, int start, int end, int l, int r) {
        if (r < start || end < l)
            return 0;  // segment completely outside query range — identity for sum
        if (l <= start && end <= r)
            return tree[node];  // segment completely inside query range
        // Partial overlap — recurse on both halves
        int mid = (start + end) / 2;
        int leftSum  = query(2*node,   start, mid,   l, r);
        int rightSum = query(2*node+1, mid+1, end,   l, r);
        return leftSum + rightSum;
    }

public:
    int query(int l, int r) {
        return query(1, 0, n-1, l, r);
    }
```

---

## Why O(log n)?

At any level of the tree, at most 4 nodes are partially overlapping with the query range (2 on the left boundary, 2 on the right). All other nodes are either fully inside (return immediately) or fully outside (return immediately). Since there are $O(\log n)$ levels, total nodes visited is $O(\log n)$.

---
