
---

# Trapping Rain Water

Given an array of bar heights, compute how much water can be trapped between the bars after rain.

Example: heights $= [0,1,0,2,1,0,1,3,2,1,2,1]$ → answer is $6$.

The water trapped at any position $i$ is determined by the shortest of the tallest bars to its left and right, minus the height of the bar at $i$:

$$\text{water}[i] = \min(\text{maxLeft}[i],\ \text{maxRight}[i]) - h[i]$$

There are three approaches — prefix arrays ($O(n)$ time, $O(n)$ space), two pointers ($O(n)$ time, $O(1)$ space), and monotonic stack ($O(n)$ time, $O(n)$ space). We show all three.

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;

// Approach 1 — Prefix Arrays
// Time: O(n), Space: O(n)
int trapPrefix(vector<int>& h) {
    int n = h.size();
    vector<int> maxL(n), maxR(n);

    maxL[0] = h[0];
    for (int i = 1; i < n; i++)
        maxL[i] = max(maxL[i-1], h[i]);

    maxR[n-1] = h[n-1];
    for (int i = n-2; i >= 0; i--)
        maxR[i] = max(maxR[i+1], h[i]);

    int water = 0;
    for (int i = 0; i < n; i++)
        water += min(maxL[i], maxR[i]) - h[i];
    return water;
}

// Approach 2 — Two Pointers
// Time: O(n), Space: O(1)
int trapTwoPointers(vector<int>& h) {
    int left = 0, right = h.size() - 1;
    int maxL = 0, maxR = 0, water = 0;

    while (left < right) {
        if (h[left] < h[right]) {
            if (h[left] >= maxL) maxL = h[left];
            else water += maxL - h[left];
            left++;
        } else {
            if (h[right] >= maxR) maxR = h[right];
            else water += maxR - h[right];
            right--;
        }
    }
    return water;
}

// Approach 3 — Monotonic Stack
// Time: O(n), Space: O(n)
int trapStack(vector<int>& h) {
    int n = h.size(), water = 0;
    stack<int> st;

    for (int i = 0; i < n; i++) {
        while (!st.empty() && h[st.top()] < h[i]) {
            int bottom = h[st.top()]; st.pop();
            if (st.empty()) break;
            int width = i - st.top() - 1;
            int bounded = min(h[st.top()], h[i]) - bottom;
            water += bounded * width;
        }
        st.push(i);
    }
    return water;
}

int main() {
    vector<int> h = {0,1,0,2,1,0,1,3,2,1,2,1};
    cout << trapPrefix(h) << endl;      // 6
    cout << trapTwoPointers(h) << endl; // 6
    cout << trapStack(h) << endl;       // 6
    return 0;
}
```

The two pointer approach is the most elegant — it works by observing that water at any position is limited by the _shorter_ side, so you always process the shorter side first.

**Complexity:** All three are $O(n)$ time. Two pointers is $O(1)$ space.

---
