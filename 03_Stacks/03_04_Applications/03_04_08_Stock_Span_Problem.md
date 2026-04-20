
---

# Stock Span Problem

Given an array of daily stock prices, for each day compute the **span** — the number of consecutive days immediately before (and including) today where the price was less than or equal to today's price.

Example: prices $= [100, 80, 60, 70, 60, 75, 85]$ → spans $= [1, 1, 1, 2, 1, 4, 6]$

For day 6 (price 85) — going back, 75, 60, 70, 60, 80 are all ≤ 85 except 80 wait — actually 80 > 85 is false, 80 < 85 so span is 6.

This is directly a **Previous Greater Element** problem in disguise. The span at index $i$ is:

$$\text{span}[i] = i - \text{index of previous greater element}$$

If there is no previous greater element, span $= i + 1$ (all days back to the start).

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;

// Time: O(n) amortized — each element pushed and popped at most once
// Note: although there is a while loop inside a for loop,
// the total number of pops across all iterations is at most n.
// This is the exception to the "nested loop = O(n^2)" rule
// mentioned in [[00_04_Time_and_Space_Complexity]]
// Space: O(n)
vector<int> stockSpan(vector<int>& prices) {
    int n = prices.size();
    vector<int> span(n);
    stack<int> st;  // stores indices

    for (int i = 0; i < n; i++) {
        while (!st.empty() && prices[st.top()] <= prices[i])
            st.pop();
        span[i] = st.empty() ? i + 1 : i - st.top();
        st.push(i);
    }
    return span;
}

int main() {
    vector<int> prices = {100, 80, 60, 70, 60, 75, 85};
    vector<int> span = stockSpan(prices);
    for (int x : span) cout << x << " ";  // 1 1 1 2 1 4 6
    return 0;
}
```

**Complexity:** Time $O(n)$ amortized — this is the exception referenced in [[00_02_Time_and_Space_Complexity]]. The nested while loop looks like $O(n^2)$ but each element is pushed and popped at most once across all iterations, giving $O(n)$ total operations.

---
