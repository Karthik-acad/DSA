
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