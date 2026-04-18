
---

# Previous Smaller Element

For each element find the first element to its **left** that is strictly smaller. If none, answer is $-1$.

Example: $[4, 5, 2, 10, 8]$ → $[-1, 4, -1, 2, 2]$

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;

// Time: O(n), Space: O(n)
vector<int> previousSmallerElement(vector<int>& arr) {
    int n = arr.size();
    vector<int> result(n, -1);
    stack<int> st;

    for (int i = 0; i < n; i++) {
        while (!st.empty() && arr[st.top()] >= arr[i])
            st.pop();
        if (!st.empty())
            result[i] = arr[st.top()];
        st.push(i);
    }
    return result;
}

int main() {
    vector<int> arr = {4, 5, 2, 10, 8};
    vector<int> res = previousSmallerElement(arr);
    for (int x : res) cout << x << " ";  // -1 4 -1 2 2
    return 0;
}
```

**Complexity:** Time $O(n)$, Space $O(n)$.

---

## The Four Variants — Pattern Summary

All four problems use the same monotonic stack template. The differences:

|Problem|Stack type|Traverse|Record when|
|---|---|---|---|
|NGE|Decreasing|Left to right|Popping|
|NSE|Increasing|Left to right|Popping|
|PGE|Decreasing|Left to right|Before pushing|
|PSE|Increasing|Left to right|Before pushing|

Once you see this table, none of the four need to be memorised separately — they are all the same code with two binary switches flipped.

---
