
---

# Largest Rectangle in Histogram

Given an array of bar heights representing a histogram, find the area of the largest rectangle that can be formed.

Example: heights $= [2, 1, 5, 6, 2, 3]$ → answer is $10$ (bars of height 5 and 6).

This is one of the most elegant stack problems. The key observation — for each bar $i$, the largest rectangle with height $h[i]$ extends as far left and right as bars are at least as tall as $h[i]$. In other words, it extends until it hits the nearest shorter bar on either side.

This is exactly PSE and NSE — for each bar, find its previous smaller and next smaller element. The width of the rectangle centered at $i$ is:

$$\text{width} = \text{NSE}[i] - \text{PSE}[i] - 1$$ $$\text{area} = h[i] \times \text{width}$$

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;

// Time: O(n), Space: O(n)
int largestRectangle(vector<int>& h) {
    int n = h.size();
    stack<int> st;
    int maxArea = 0;

    for (int i = 0; i <= n; i++) {
        int curr = (i == n) ? 0 : h[i];  // sentinel 0 at end flushes remaining stack

        while (!st.empty() && h[st.top()] > curr) {
            int height = h[st.top()]; st.pop();
            int width = st.empty() ? i : i - st.top() - 1;
            maxArea = max(maxArea, height * width);
        }
        st.push(i);
    }
    return maxArea;
}

int main() {
    vector<int> h = {2, 1, 5, 6, 2, 3};
    cout << largestRectangle(h) << endl;  // 10
    return 0;
}
```

The sentinel $0$ appended at the end forces all remaining elements in the stack to be popped and their rectangles computed — otherwise bars that were never popped (no shorter bar to their right) would be missed.

**Complexity:** Time $O(n)$, Space $O(n)$.

---
