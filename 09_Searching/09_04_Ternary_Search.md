
---

# Ternary Search

Binary search finds a value in a sorted array. **Ternary search** finds the maximum or minimum of a **unimodal function** — one that strictly increases then strictly decreases (or vice versa) with a single peak.

> **Definition.** _Ternary search_ finds the maximum of a unimodal function $f$ on interval $[lo, hi]$ by dividing the interval into three parts and eliminating the third that cannot contain the maximum.

At each step, compute $m_1 = lo + (hi-lo)/3$ and $m_2 = hi - (hi-lo)/3$:

- If $f(m_1) < f(m_2)$ → maximum is in $[m_1, hi]$, eliminate left third
- If $f(m_1) > f(m_2)$ → maximum is in $[lo, m_2]$, eliminate right third

```cpp
#include <iostream>
#include <functional>
using namespace std;

// Find maximum of unimodal function on [lo, hi]
// Time: O(log(hi - lo)), Space: O(1)
double ternarySearch(double lo, double hi, function<double(double)> f) {
    while (hi - lo > 1e-9) {
        double m1 = lo + (hi - lo) / 3;
        double m2 = hi - (hi - lo) / 3;
        if (f(m1) < f(m2)) lo = m1;
        else hi = m2;
    }
    return (lo + hi) / 2;
}

// For integer domains — O(log n)
int ternarySearchInt(int lo, int hi, function<int(int)> f) {
    while (hi - lo > 2) {
        int m1 = lo + (hi - lo) / 3;
        int m2 = hi - (hi - lo) / 3;
        if (f(m1) < f(m2)) lo = m1;
        else hi = m2;
    }
    // Check remaining candidates
    int best = lo;
    for (int i = lo+1; i <= hi; i++)
        if (f(i) > f(best)) best = i;
    return best;
}

int main() {
    // Find maximum of -(x-3)^2 + 9 — peak at x=3
    auto f = [](double x) { return -(x-3)*(x-3) + 9; };
    double peak = ternarySearch(0, 10, f);
    cout << "Peak at x = " << peak << endl;  // ~3.0
    cout << "f(peak) = " << f(peak) << endl;  // ~9.0
    return 0;
}
```

---

## Ternary vs Binary Search

||Binary Search|Ternary Search|
|---|---|---|
|Input requirement|Sorted array|Unimodal function|
|Eliminates per step|Half|One-third|
|Iterations|$O(\log_2 n)$|$O(\log_{3/2} n) \approx 2.4\log_2 n$|
|Use case|Finding value|Finding max/min of unimodal function|

Interestingly, ternary search is slightly _slower_ than binary search asymptotically — eliminating a third versus a half means more iterations. For unimodal functions on a discrete domain, you can often use binary search on the derivative instead (checking if $f(m) < f(m+1)$) to achieve the same $O(\log n)$ with fewer constant factors.

---

## Complexity Summary

|Case|Time|Space|
|---|---|---|
|All cases|$O(\log n)$ or $O(\log(\epsilon^{-1}))$ for continuous|$O(1)$|

---
