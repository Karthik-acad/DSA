Stacks! Here is the structure:

**Notes:**

- 03_01_Stack_Introduction
- 03_02_Stack_Implementation
- 03_03_Monotonic_Stack

**Subfolder 03_04_Applications:**

- 03_04_01_Balanced_Parentheses
- 03_04_02_NGE_Next_Greater_Element
- 03_04_03_NSE_Next_Smaller_Element
- 03_04_04_PGE_Previous_Greater_Element
- 03_04_05_PSE_Previous_Smaller_Element
- 03_04_06_Largest_Rectangle_in_Histogram
- 03_04_07_Trapping_Rain_Water
- 03_04_08_Stock_Span_Problem
- 03_04_09_Infix_Prefix_Postfix_Conversion

**Then** quiz and summary.

This is the biggest chapter so far — especially the applications subfolder. Starting with `03_01_Stack_Introduction.md`:

---

# Stack Introduction

From our study of linked lists and arrays, we have seen data structures that let you access any element — you can go to index 5 of an array or traverse to any node. But many real problems do not need arbitrary access. They need something simpler and more disciplined — a structure where you can only work with the _most recently added_ element.

Think of a stack of plates. You add plates on top, and you take plates from the top. You never pull a plate from the middle — that would topple everything. The last plate you put on is the first one you take off. This is the **stack** data structure.

> **Definition.** A _stack_ is an Abstract Data Type that follows the **Last In First Out (LIFO)** principle — the last element inserted is the first one removed.

A stack supports exactly three core operations:

|Operation|Description|Complexity|
|---|---|---|
|`push(x)`|Insert element $x$ on top|$O(1)$|
|`pop()`|Remove and return the top element|$O(1)$|
|`peek()` / `top()`|Return top element without removing|$O(1)$|
|`isEmpty()`|Check if stack is empty|$O(1)$|

Everything is $O(1)$ — this is the power of the constraint. By restricting access to only the top, every operation is instant.

---

## Where Stacks Appear Naturally

The LIFO property is not arbitrary — it mirrors how many real processes work:

**Function call stack** — when `main` calls `foo` which calls `bar`, the most recent call (`bar`) must finish before `foo` can continue. The operating system literally uses a stack to track this — it is why infinite recursion causes a _stack overflow_.

**Undo/Redo** — every text editor maintains a stack of actions. Ctrl+Z pops the most recent action and reverses it.

**Browser history** — the back button pops the most recently visited page.

**Expression evaluation** — compilers use stacks to parse and evaluate arithmetic expressions.

All of these share the same pattern — _the most recently seen thing is the next thing to be processed_. Whenever you notice this pattern in a problem, a stack is likely the right tool.

> 📖 For how the call stack works at the hardware level: [CS:APP — Bryant and O'Hallaron, Chapter 3](http://csapp.cs.cmu.edu/)

---

Here is `03_02_Stack_Implementation.md`:

---

# Stack Implementation

The Stack ADT says what operations we need — `push`, `pop`, `peek`, `isEmpty`. Now we look at how to actually build it. There are two natural implementations — using an array and using a linked list. A third option is to use C++'s built-in `std::stack`.

---

## Implementation 1 — Using a Static Array

The simplest approach. Maintain an array and an integer `top` that tracks the index of the topmost element. Push increments `top` and writes, pop reads and decrements.

```cpp
#include <iostream>
using namespace std;

class Stack {
    int arr[1000];
    int top;
public:
    Stack() : top(-1) {}

    // O(1)
    void push(int x) {
        if (top == 999) { cout << "Stack overflow" << endl; return; }
        arr[++top] = x;
    }

    // O(1)
    int pop() {
        if (isEmpty()) { cout << "Stack underflow" << endl; return -1; }
        return arr[top--];
    }

    // O(1)
    int peek() {
        if (isEmpty()) return -1;
        return arr[top];
    }

    // O(1)
    bool isEmpty() { return top == -1; }

    // O(1)
    int size() { return top + 1; }
};
```

Simple and cache-friendly, but the fixed size is a hard constraint — you must know the maximum size upfront.

---

## Implementation 2 — Using a Dynamic Array (vector)

Same idea but using `vector<int>` instead of a fixed array — automatically resizes as needed.

```cpp
#include <iostream>
#include <vector>
using namespace std;

class Stack {
    vector<int> arr;
public:
    // O(1) amortized
    void push(int x) { arr.push_back(x); }

    // O(1)
    int pop() {
        if (isEmpty()) return -1;
        int val = arr.back();
        arr.pop_back();
        return val;
    }

    // O(1)
    int peek() {
        if (isEmpty()) return -1;
        return arr.back();
    }

    bool isEmpty() { return arr.empty(); }
    int size() { return arr.size(); }
};
```

---

## Implementation 3 — Using a Linked List

Each push creates a new node at the head, each pop removes the head. No size limit, no amortized cost — every operation is strictly $O(1)$.

```cpp
#include <iostream>
using namespace std;

struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class Stack {
    Node* top;
    int sz;
public:
    Stack() : top(nullptr), sz(0) {}

    // O(1)
    void push(int x) {
        Node* newNode = new Node(x);
        newNode->next = top;
        top = newNode;
        sz++;
    }

    // O(1)
    int pop() {
        if (isEmpty()) return -1;
        int val = top->data;
        Node* temp = top;
        top = top->next;
        delete temp;
        sz--;
        return val;
    }

    // O(1)
    int peek() {
        if (isEmpty()) return -1;
        return top->data;
    }

    bool isEmpty() { return top == nullptr; }
    int size() { return sz; }
};
```

---

## Implementation 4 — Using std::stack

In practice you will use C++'s built-in stack. It is a container adaptor — by default backed by `std::deque` but you can specify `std::vector` or `std::list`.

```cpp
#include <iostream>
#include <stack>
using namespace std;

int main() {
    stack<int> st;

    st.push(1);
    st.push(2);
    st.push(3);

    cout << st.top() << endl;   // 3
    st.pop();
    cout << st.top() << endl;   // 2
    cout << st.size() << endl;  // 2
    cout << st.empty() << endl; // 0 (false)

    return 0;
}
```

There are more implementations possible — two stacks using one array, stack using a queue — but the four above cover everything you need. The **vector-based** implementation is what we will primarily use going forward since it combines simplicity with no fixed size constraint.

---

## Implementation Comparison

||Static Array|Dynamic Array|Linked List|std::stack|
|---|---|---|---|---|
|Push|$O(1)$|$O(1)$ amortized|$O(1)$|$O(1)$ amortized|
|Pop|$O(1)$|$O(1)$|$O(1)$|$O(1)$|
|Size limit|Fixed|Dynamic|Dynamic|Dynamic|
|Cache friendly|Yes|Yes|No|Depends|
|Memory overhead|Wasted if underfilled|Small|One pointer per node|Depends|

> 📖 For std::stack internals: [CPP Reference — std::stack](https://en.cppreference.com/w/cpp/container/stack)

---

Here is `03_03_Monotonic_Stack.md`:

---

# Monotonic Stack

A **monotonic stack** is not a new data structure — it is a regular stack used with a specific discipline: you maintain the invariant that elements in the stack are always in either strictly increasing or strictly decreasing order from bottom to top.

Before pushing a new element, you pop everything that violates the order. This popping step is where the useful work happens — the moment an element gets popped, you have found its answer to some question about its relationship with the new element.

> **Definition.** A _monotonic stack_ is a stack where elements are maintained in monotonically increasing or decreasing order. Elements are popped when they would violate this order, and the act of popping establishes a relationship between the popped element and the element that caused the pop.

The key insight — each element is pushed once and popped at most once, so the total work across all operations is $O(n)$ even though there is a nested loop structure. This is the same amortized argument as the sliding window.

This is the technique that powers all the NGE, NSE, PGE, PSE problems in the Applications folder. The template is always the same — the specific problem determines whether you use an increasing or decreasing stack, and what you record when popping.

---

## The Template

```cpp
// Monotonically decreasing stack template
// Useful for: NGE, Stock Span, Histogram, Rain Water
vector<int> monoDecreasing(vector<int>& arr) {
    int n = arr.size();
    vector<int> result(n, -1);  // default answer
    stack<int> st;  // stores indices, not values

    for (int i = 0; i < n; i++) {
        // Pop all elements smaller than current
        while (!st.empty() && arr[st.top()] < arr[i]) {
            result[st.top()] = arr[i];  // arr[i] is the answer for popped element
            st.pop();
        }
        st.push(i);
    }
    return result;
}

// Monotonically increasing stack template
// Useful for: NSE, PSE
vector<int> monoIncreasing(vector<int>& arr) {
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
```

Notice we store **indices** in the stack, not values — this lets us record positions in the result array and also compute distances between elements.

> 📖 For a catalogue of monotonic stack problems: [LeetCode Monotonic Stack](https://leetcode.com/tag/monotonic-stack/)

---

Now the Applications. Here is `03_04_01_Balanced_Parentheses.md`:

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

Here is `03_04_02_NGE_Next_Greater_Element.md`:

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

Here is `03_04_03_NSE_Next_Smaller_Element.md`:

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

Here is `03_04_04_PGE_Previous_Greater_Element.md`:

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

Here is `03_04_05_PSE_Previous_Smaller_Element.md`:

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

Here is `03_04_06_Largest_Rectangle_in_Histogram.md`:

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

Here is `03_04_07_Trapping_Rain_Water.md`:

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

Here is `03_04_08_Stock_Span_Problem.md`:

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

**Complexity:** Time $O(n)$ amortized — this is the exception referenced in [[00_04_Time_and_Space_Complexity]]. The nested while loop looks like $O(n^2)$ but each element is pushed and popped at most once across all iterations, giving $O(n)$ total operations.

---

Here is `03_04_09_Infix_Prefix_Postfix_Conversion.md`:

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

Now here is `03_QQ_Stacks_Quiz.md`:

---

## Quiz — Stacks

**Q1 — Identify the Complexity**

```cpp
stack<int> st;
for (int i = 0; i < n; i++) st.push(i);
while (!st.empty()) st.pop();
```

- [ ] $O(n)$
- [ ] $O(n^2)$
- [ ] $\Theta(n)$
- [ ] $O(1)$

> [!answer]- Answer O(n) and Theta(n) are both correct. The for loop pushes n times and the while loop pops n times — total 2n operations each costing O(1), giving Theta(n). O(1) is false. O(n^2) is a valid upper bound but not tight.

---

**Q2 — Trace the Output**

```cpp
stack<int> st;
st.push(1); st.push(2); st.push(3);
cout << st.top(); st.pop();
st.push(4);
cout << st.top(); st.pop();
cout << st.top();
```

> [!answer]- Answer Prints: 3 4 2. After pushing 1,2,3 the top is 3 — printed and popped. Then 4 is pushed making top 4 — printed and popped. Now top is 2 — printed.

---

**Q3 — Balanced Parentheses** Which of the following are balanced?

- [ ] `{[()]}`
- [ ] `{[(])}`
- [ ] `(((`
- [ ] `()[]{}`

> [!answer]- Answer Only {[()]} and ()[]{} are balanced. {[(])} fails because ] closes before [ which was opened after [. ((( fails because no closing brackets. Trace with stack: for {[()]} push {, push [, push (, see ) matches ( pop, see ] matches [ pop, see } matches { pop — stack empty — valid.

---

**Q4 — NGE by hand** Find NGE for each element: $[3, 1, 4, 1, 5, 9, 2, 6]$

> [!answer]- Answer [4, 4, 5, 5, 9, -1, 6, -1] Trace: 3→first right greater is 4. 1→4. 4→5. 1→5. 5→9. 9→no greater→-1. 2→6. 6→no greater→-1.

---

**Q5 — Stock Span** Prices: $[10, 4, 5, 90, 120, 80]$. Compute the span for each day.

> [!answer]- Answer Spans: [1, 1, 2, 4, 5, 1]. Day 0: no previous, span=1. Day 1: 4<10, span=1. Day 2: 5>4 but <10, span=2. Day 3: 90>5,4,10 — all previous, span=4. Day 4: 120>all, span=5. Day 5: 80<120, span=1.

---

**Q6 — Spot the Bug**

```cpp
string infixToPostfix(const string& expr) {
    stack<char> st;
    string result;
    for (char c : expr) {
        if (isalnum(c)) result += c;
        else {
            while (!st.empty() && precedence(st.top()) >= precedence(c))
                result += st.top(); st.pop();
            st.push(c);
        }
    }
    while (!st.empty()) { result += st.top(); st.pop(); }
    return result;
}
```

> [!answer]- Answer Missing braces on the while loop body. The line result += st.top(); st.pop(); looks like two statements in the while body but only result += st.top() is in the loop — st.pop() executes once after the loop exits, regardless of how many times the loop ran. This means only one element gets popped even when multiple should be. Fix: wrap both statements in {}. Also no handling of parentheses — but that may be intentional if the input has no parentheses.

---

**Q7 — Write it Yourself** Implement a stack that supports push, pop, and getMin — all in $O(1)$. Hint: use two stacks.

> [!answer]- Answer
> 
> ```cpp
> class MinStack {
>     stack<int> main, minSt;
> public:
>     void push(int x) {
>         main.push(x);
>         if (minSt.empty() || x <= minSt.top())
>             minSt.push(x);
>     }
>     void pop() {
>         if (main.top() == minSt.top()) minSt.pop();
>         main.pop();
>     }
>     int top() { return main.top(); }
>     int getMin() { return minSt.top(); }
> };
> ```
> 
> The auxiliary minSt only pushes when a new minimum is seen. On pop, if the element being removed was the current minimum, pop it from minSt too. This way minSt.top() always holds the current minimum in O(1).

---

**Q8 — Trapping Rain Water** By hand — compute water trapped for heights $[3, 0, 2, 0, 4]$.

> [!answer]- Answer Total = 7. Position 0: height 3, no water (leftmost). Position 1: maxL=3, maxR=4, water = min(3,4)-0 = 3. Position 2: maxL=3, maxR=4, water = min(3,4)-2 = 1. Position 3: maxL=3, maxR=4, water = min(3,4)-0 = 3. Position 4: height 4, no water (rightmost). Total = 3+1+3 = 7.

---

And finally `03_00_Stacks.md`:

---

# Stacks

## Notes

- [[03_01_Stack_Introduction]]
- [[03_02_Stack_Implementation]]
- [[03_03_Monotonic_Stack]]

## Applications

- [[03_04_00_Applications]]

## Quiz

- [[03_QQ_Stacks_Quiz]]

---

## Quick Reference

### Stack ADT

> **Definition.** A stack is a LIFO (Last In First Out) data structure. The last element inserted is the first one removed.

|Operation|Complexity|
|---|---|
|push|$O(1)$|
|pop|$O(1)$|
|peek / top|$O(1)$|
|isEmpty|$O(1)$|

---

### Implementation Comparison

||Static Array|Dynamic Array|Linked List|
|---|---|---|---|
|Push|$O(1)$|$O(1)$ amortized|$O(1)$|
|Pop|$O(1)$|$O(1)$|$O(1)$|
|Size limit|Fixed|Dynamic|Dynamic|
|Cache|Good|Good|Poor|

---

### Monotonic Stack — The Four Variants

|Problem|Stack order|Pop condition|Record when|
|---|---|---|---|
|NGE|Decreasing|$arr[top] < curr$|On pop|
|NSE|Increasing|$arr[top] > curr$|On pop|
|PGE|Decreasing|$arr[top] \leq curr$|Before push|
|PSE|Increasing|$arr[top] \geq curr$|Before push|

$$\text{span}[i] = i - \text{index of PGE}[i]$$

$$\text{Rectangle area at } i = h[i] \times (\text{NSE}[i] - \text{PSE}[i] - 1)$$

$$\text{Water at } i = \min(\text{maxLeft}[i],\ \text{maxRight}[i]) - h[i]$$

---

### Operator Precedence for Infix Conversion

|Operator|Precedence|
|---|---|
|`^`|3 (highest)|
|`*`, `/`|2|
|`+`, `-`|1 (lowest)|

---