
---
# Prefix Sums

Consider this problem — you have an array of numbers and you are asked repeatedly: **what is the sum of elements from index $l$ to index $r$?** The naive approach scans from $l$ to $r$ each time, costing $O(n)$ per query. If you have $q$ queries, that is $O(nq)$ total — expensive.

**Prefix sums** is a preprocessing technique that brings each query down to  $O(1)$ at the cost of $O(n)$ preprocessing and $O(n)$ extra space.

The idea is simple — build an auxiliary array $P$ where $P[i]$ stores the sum of all elements from index $0$ to index $i$:

$$P[i]=A[0]+A[1]+…+A[i]$$

Then the sum from $l$ to $r$ is just:

$$sum(l,r)=P[r]−P[l−1]$$

Think of it like a running total on a receipt. Instead of re-adding every time, you just look at two totals and subtract.

> **Definition.** A *prefix sum array* $P$ of array $A$ is defined as $$P[i]=\sum_{k=0}^{i} A[j]$$$ A range sum query $(l,r)$ is then answered in $O(1)$ as $$ P[r]−P[l−1]$$

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Build prefix sum array
// Time: O(n), Space: O(n)
vector<int> buildPrefix(const vector<int>& A) {
    int n = A.size();
    vector<int> P(n);
    P[0] = A[0];
    for (int i = 1; i < n; i++)
        P[i] = P[i-1] + A[i];
    return P;
}

// Range sum query [l, r]
// Time: O(1)
int query(const vector<int>& P, int l, int r) {
    if (l == 0) return P[r];
    return P[r] - P[l-1];
}

int main() {
    vector<int> A = {3, 1, 4, 1, 5, 9, 2, 6};
    vector<int> P = buildPrefix(A);

    cout << query(P, 1, 4) << endl;  // 1+4+1+5 = 11
    cout << query(P, 0, 7) << endl;  // entire array = 31

    return 0;
}
```

---

## 2D Prefix Sums

The same idea extends to 2D arrays — useful for rectangular submatrix sum queries. For a matrix $M$:

$$P[i][j]=M[i][j]+P[i−1][j]+P[i][j−1]−P[i−1][j−1]$$

The subtraction removes the double-counted overlap. A query for rectangle $(r1,c1)(r_1, c_1) (r1​,c1​)\space to \space(r2,c2)(r_2, c_2) (r2​,c2​):$

$$sum=P[r2][c2]−P[r1−1][c2]−P[r2][c1−1]+P[r1−1][c1−1]$$

---

## Complexity Summary

| Operation          | Time           | Space  |
| ------------------ | -------------- | ------ |
| Build prefix array | $O(N)$         | $O(N)$ |
| Range sum query    | $O(1)$         | —      |
| Update an element  | $O(N)$ rebuild | —      |

The weakness — if the array is updated frequently, the prefix array must be rebuilt each time costing $O(n)$. For dynamic updates with range queries, a **Fenwick Tree** or **Segment Tree** is the right tool (see [[07_05_00_Segment_Trees]]).

>  For 2D prefix sums with problems: [CP Algorithms — Prefix Sums](https://cp-algorithms.com/algebra/prefix-sums.html)