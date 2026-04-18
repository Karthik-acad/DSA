
---

# Next Smaller Element

Mirror image of NGE — for each element find the first element to its **right** that is strictly smaller. If none, answer is $-1$.

Example: $[4, 5, 2, 10, 8]$ → $[2, 2, -1, 8, -1]$

The only change from NGE — use an **increasing** stack instead of decreasing. Pop when the current element is _smaller_ than the top.

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;

// Time: O(n), Space: O(n)
vector<int> nextSmallerElement(vector<int>& arr) {
    int n = arr.size();
    vector<int> result(n, -1);
    stack<int> st;

    for (int i = 0; i < n; i++) {
        while (!st.empty() && arr[st.top()] > arr[i]) {
            result[st.top()] = arr[i];
            st.pop();
        }
        st.push(i);
    }
    return result;
}

int main() {
    vector<int> arr = {4, 5, 2, 10, 8};
    vector<int> res = nextSmallerElement(arr);
    for (int x : res) cout << x << " ";  // 2 2 -1 8 -1
    return 0;
}
```

**Complexity:** Time $O(n)$, Space $O(n)$.

---
