
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
