
---

# Next Greater Element

Given an array, for each element find the first element to its **right** that is strictly greater. If no such element exists, the answer is $-1$.

Example: $[4, 5, 2, 10, 8]$ → $[5, 10, 10, -1, -1]$

The brute force checks every element to the right for each position — $O(n^2)$. The monotonic stack brings this to $O(n)$.

**The idea** — traverse left to right maintaining a decreasing stack. When a new element is larger than the stack top, the stack top has found its NGE — it is the current element. Pop it and record the answer. Repeat until the stack top is larger or the stack is empty, then push the current element.

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;

// Time: O(n), Space: O(n)
vector<int> nextGreaterElement(vector<int>& arr) {
    int n = arr.size();
    vector<int> result(n, -1);
    stack<int> st;  // stores indices

    for (int i = 0; i < n; i++) {
        while (!st.empty() && arr[st.top()] < arr[i]) {
            result[st.top()] = arr[i];
            st.pop();
        }
        st.push(i);
    }
    // elements remaining in stack have no NGE — result stays -1
    return result;
}

int main() {
    vector<int> arr = {4, 5, 2, 10, 8};
    vector<int> res = nextGreaterElement(arr);
    for (int x : res) cout << x << " ";  // 5 10 10 -1 -1
    return 0;
}
```

**Why it works** — when element at index $i$ is popped because $arr[i] < arr[j]$, $arr[j]$ is the first element to the right of $i$ that is greater (nothing between them caused a pop of $i$ earlier). Each element pushed and popped once — $O(n)$ total.

---
