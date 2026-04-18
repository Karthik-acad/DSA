
---

# Previous Greater Element

For each element find the first element to its **left** that is strictly greater. If none, answer is $-1$.

Example: $[4, 5, 2, 10, 8]$ → $[-1, -1, 5, -1, 10]$

The change from NGE — traverse **right to left** instead of left to right, OR traverse left to right but record the stack top as the answer _before_ pushing rather than recording when popping.

The second approach is cleaner:

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;

// Time: O(n), Space: O(n)
vector<int> previousGreaterElement(vector<int>& arr) {
    int n = arr.size();
    vector<int> result(n, -1);
    stack<int> st;

    for (int i = 0; i < n; i++) {
        // Pop elements smaller than current — they cannot be PGE for current
        while (!st.empty() && arr[st.top()] <= arr[i])
            st.pop();
        // Stack top is now the previous greater element
        if (!st.empty())
            result[i] = arr[st.top()];
        st.push(i);
    }
    return result;
}

int main() {
    vector<int> arr = {4, 5, 2, 10, 8};
    vector<int> res = previousGreaterElement(arr);
    for (int x : res) cout << x << " ";  // -1 -1 5 -1 10
    return 0;
}
```

**Complexity:** Time $O(n)$, Space $O(n)$.

---
