
---

# Infix Prefix Postfix Conversion

Mathematical expressions can be written in three notations:

|Notation|Form|Example|
|---|---|---|
|**Infix**|Operator between operands|$A + B$|
|**Prefix**|Operator before operands|$+ A B$|
|**Postfix**|Operator after operands|$A B +$|

Infix is how humans write — but it requires parentheses and operator precedence rules to be unambiguous. Postfix and prefix are unambiguous without parentheses and are how computers evaluate expressions internally. Compilers convert infix to postfix before evaluation.

---

## Infix to Postfix (Shunting-Yard Algorithm)

Rules:

- Operand → directly to output
- Operator → pop stack while top has higher/equal precedence, then push
- `(` → push
- `)` → pop until `(`

```cpp
#include <iostream>
#include <stack>
#include <string>
using namespace std;

int precedence(char op) {
    if (op == '+' || op == '-') return 1;
    if (op == '*' || op == '/') return 2;
    if (op == '^') return 3;
    return 0;
}

// Time: O(n), Space: O(n)
string infixToPostfix(const string& expr) {
    stack<char> st;
    string result;

    for (char c : expr) {
        if (isalnum(c)) {
            result += c;
        } else if (c == '(') {
            st.push(c);
        } else if (c == ')') {
            while (!st.empty() && st.top() != '(') {
                result += st.top(); st.pop();
            }
            st.pop();  // pop '('
        } else {
            while (!st.empty() && precedence(st.top()) >= precedence(c))  {
                result += st.top(); st.pop();
            }
            st.push(c);
        }
    }
    while (!st.empty()) { result += st.top(); st.pop(); }
    return result;
}

int main() {
    cout << infixToPostfix("A+B*C") << endl;      // ABC*+
    cout << infixToPostfix("(A+B)*C") << endl;    // AB+C*
    cout << infixToPostfix("A+B*C-D/E") << endl;  // ABC*+DE/-
    return 0;
}
```

---

## Postfix Evaluation

Given a postfix expression, evaluate it using a stack — push operands, on operator pop two operands, compute, push result.

```cpp
// Time: O(n), Space: O(n)
int evalPostfix(const string& expr) {
    stack<int> st;
    for (char c : expr) {
        if (isdigit(c)) {
            st.push(c - '0');
        } else {
            int b = st.top(); st.pop();
            int a = st.top(); st.pop();
            if (c == '+') st.push(a + b);
            if (c == '-') st.push(a - b);
            if (c == '*') st.push(a * b);
            if (c == '/') st.push(a / b);
        }
    }
    return st.top();
}
```

**Complexity:** Both conversion and evaluation are $O(n)$ time, $O(n)$ space.

---
