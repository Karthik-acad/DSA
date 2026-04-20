
---

# Binary Search on Answer

Binary search is not just for searching sorted arrays — it is a powerful technique for optimization problems where you need to find the minimum or maximum value satisfying some condition. Instead of searching for a value _in_ an array, you binary search over the _answer space_.

> **Definition.** _Binary search on answer_ applies binary search to the range of possible answers $[\text{lo}, \text{hi}]$. At each step it checks whether a candidate answer $\text{mid}$ is feasible. The search narrows the range based on the feasibility check.

The key insight — the feasibility function must be **monotone**: if answer $x$ is feasible, then all answers $> x$ (or $< x$) are also feasible. This creates the sorted-like property that binary search needs.

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

// Example 1 — Minimum capacity to ship packages in D days
// Packages have weights. We need minimum ship capacity such that
// all packages can be shipped in at most D days.
// Feasibility: can we ship everything within D days at capacity C?
bool canShip(vector<int>& weights, int D, int capacity) {
    int days = 1, load = 0;
    for (int w : weights) {
        if (load + w > capacity) { days++; load = 0; }
        load += w;
    }
    return days <= D;
}

// Time: O(n log(sum - max)) where sum = total weight, max = max single weight
int shipWithinDays(vector<int>& weights, int D) {
    int lo = *max_element(weights.begin(), weights.end());  // min possible capacity
    int hi = 0; for (int w : weights) hi += w;              // max possible capacity

    while (lo < hi) {
        int mid = lo + (hi - lo) / 2;
        if (canShip(weights, D, mid)) hi = mid;  // mid works, try smaller
        else lo = mid + 1;                        // mid too small, try larger
    }
    return lo;
}

// Example 2 — Find square root (integer)
int mySqrt(int x) {
    if (x < 2) return x;
    long long lo = 1, hi = x / 2;
    while (lo < hi) {
        long long mid = lo + (hi - lo + 1) / 2;  // upper mid
        if (mid * mid <= x) lo = mid;
        else hi = mid - 1;
    }
    return lo;
}

// Example 3 — Koko eating bananas
// n piles of bananas, h hours. Find minimum eating speed k such that
// Koko can finish all bananas in h hours.
bool canFinish(vector<int>& piles, int h, int k) {
    int hours = 0;
    for (int p : piles)
        hours += (p + k - 1) / k;  // ceil(p/k)
    return hours <= h;
}

int minEatingSpeed(vector<int>& piles, int h) {
    int lo = 1, hi = *max_element(piles.begin(), piles.end());
    while (lo < hi) {
        int mid = lo + (hi - lo) / 2;
        if (canFinish(piles, h, mid)) hi = mid;
        else lo = mid + 1;
    }
    return lo;
}

int main() {
    vector<int> weights = {1,2,3,4,5,6,7,8,9,10};
    cout << shipWithinDays(weights, 5) << endl;  // 15

    cout << mySqrt(8) << endl;  // 2

    vector<int> piles = {3,6,7,11};
    cout << minEatingSpeed(piles, 8) << endl;  // 4
    return 0;
}
```

---

## The Template

Binary search on answer always follows this pattern:

```
lo = minimum possible answer
hi = maximum possible answer

while lo < hi:
    mid = lo + (hi - lo) / 2
    if feasible(mid):
        hi = mid        # mid works, search left (find minimum)
    else:
        lo = mid + 1    # mid too small, search right

return lo
```

For finding maximum feasible answer, flip the condition.

---

## Complexity

Each binary search on answer does $O(\log(\text{hi} - \text{lo}))$ iterations, each calling the feasibility check. If the check costs $O(f(n))$:

$$T = O(f(n) \cdot \log(\text{hi} - \text{lo}))$$

---

