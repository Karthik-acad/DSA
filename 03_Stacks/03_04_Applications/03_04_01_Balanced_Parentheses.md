
---

# Balanced Parentheses

Given a string of brackets — `()`, `[]`, `{}` — determine if it is valid. Valid means every opening bracket has a corresponding closing bracket of the same type, in the correct order.

This is the classic introductory stack application. The LIFO property maps perfectly onto the nesting structure of brackets — the most recently opened bracket must be the next one closed.

> **Rule.** A bracket string is valid if and only if: every closing bracket matches the most recently seen unmatched opening bracket.

```cpp
#include <iostream>
#include <stack>
#include <string>
using namespace std;

// Time: O(n), Space: O(n)
bool isBalanced(const string& s) {
    stack<char> st;

    for (char c : s) {
        if (c == '(' || c == '[' || c == '{') {
            st.push(c);
        } else {
            if (st.empty()) return false;
            char top = st.top(); st.pop();
            if (c == ')' && top != '(') return false;
            if (c == ']' && top != '[') return false;
            if (c == '}' && top != '{') return false;
        }
    }
    return st.empty();  // all opened brackets must be closed
}

int main() {
    cout << isBalanced("({[]})") << endl;  // 1 (true)
    cout << isBalanced("({[})") << endl;   // 0 (false)
    cout << isBalanced("((())") << endl;   // 0 (false)
    return 0;
}
```

**Complexity:** Time $O(n)$, Space $O(n)$ — in the worst case (all opening brackets) the stack holds all $n$ characters.

---
