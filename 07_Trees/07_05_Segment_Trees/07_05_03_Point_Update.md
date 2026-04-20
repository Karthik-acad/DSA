
---

# Segment Tree — Point Update

A point update changes one element of the array and propagates the change up the tree.

> **Definition.** A _point update_ changes the value at index $i$ and updates all ancestor nodes in $O(\log n)$ by recomputing their stored values bottom-up.

```cpp
    // Point update — set arr[idx] = val — O(log n)
    void update(int node, int start, int end, int idx, int val) {
        if (start == end) {
            tree[node] = val;  // leaf — update directly
        } else {
            int mid = (start + end) / 2;
            if (idx <= mid)
                update(2*node,   start, mid,   idx, val);
            else
                update(2*node+1, mid+1, end,   idx, val);
            tree[node] = tree[2*node] + tree[2*node+1];  // recompute internal node
        }
    }

public:
    void update(int idx, int val) {
        update(1, 0, n-1, idx, val);
    }
};

int main() {
    vector<int> arr = {1, 3, 2, 7, 9, 11};
    SegmentTree st(arr);

    cout << st.query(1, 4) << endl;  // 3+2+7+9 = 21
    cout << st.query(0, 5) << endl;  // 33

    st.update(3, 10);  // change arr[3] from 7 to 10
    cout << st.query(1, 4) << endl;  // 3+2+10+9 = 24
    cout << st.query(0, 5) << endl;  // 36
    return 0;
}
```

---

## Segment Tree vs Prefix Sum vs Fenwick Tree

||Prefix Sum|Segment Tree|Fenwick Tree|
|---|---|---|---|
|Build|$O(n)$|$O(n)$|$O(n \log n)$|
|Range query|$O(1)$|$O(\log n)$|$O(\log n)$|
|Point update|$O(n)$ rebuild|$O(\log n)$|$O(\log n)$|
|Space|$O(n)$|$O(4n)$|$O(n)$|
|Implementation|Simplest|Moderate|Simple|
|Range update|$O(n)$|$O(\log n)$*|$O(\log n)$*|

*with lazy propagation

Use prefix sums when the array is static. Use Fenwick tree when you need point updates and prefix queries (simpler code). Use segment tree when you need range updates, range queries, or more complex operations (min, max, GCD).

---

## Complexity Summary

|Operation|Time|Space|
|---|---|---|
|Build|$O(n)$|$O(4n)$|
|Range query|$O(\log n)$|$O(\log n)$ recursive stack|
|Point update|$O(\log n)$|$O(\log n)$ recursive stack|

---
