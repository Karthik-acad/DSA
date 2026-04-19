Got it — plain text for everything inside the answer callouts, no `$` at all. Here are both quizzes corrected:

---

## Quiz — Time and Space Complexity

### Q1 — Identify the Complexity

What is the time complexity of the following?

```cpp
int x = arr[n/2];
```

- [x] $O(1)$
- [x] $O(n)$
- [x] $\Theta(1)$
- [x] $o(n)$

> [!answer]-
>  O(1), Theta(1), and o(n) are all correct. Direct index access is constant time regardless of n, so it is simultaneously bounded above by O(1), exactly Theta(1), and strictly slower than any O(n) function making it o(n) as well. Multiple answers can be correct — this is intentional.

---

### Q2 — Identify the Complexity

```cpp
for (int i = 0; i < n; i++)
    for (int j = i; j < n; j++)
        sum += arr[i] + arr[j];
```

Which of the following are true?

- [x] $O(n^2)$
- [x] $\Theta(n^2)$
- [x] $O(n^3)$
- [x] $o(n^3)$
- [x] $\Omega(n)$

> [!answer]- 
> All five are correct. The inner loop runs n-i times for each i, giving total iterations = n + (n-1) + ... + 1 = n(n+1)/2 = Theta(n^2). Since it is Theta(n^2), it is also O(n^2), O(n^3), o(n^3) (strictly slower than cubic), and Omega(n) (at least linear).

---

### Q3 — True or False

For any two functions $f(n)$ and $g(n)$, if $f(n) = O(g(n))$ then $g(n) = \Omega(f(n))$.

- [ ] True
- [ ] False

> [!answer]- 
> True. This follows directly from the definitions. If f(n) <= c * g(n) for all n >= n_0, then rearranging gives g(n) >= (1/c) * f(n), which is exactly the definition of g(n) = Omega(f(n)).

---

### Q4 — Find the Complexity

What is the time and space complexity of the following? Write your reasoning before revealing.

```cpp
void printPairs(vector<int>& arr) {
    int n = arr.size();
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            cout << arr[i] << ", " << arr[j] << endl;
}
```

> [!answer]- 
> Time: Theta(n^2) — two nested loops each running n times, every iteration does O(1) work. Space: O(1) auxiliary — only i, j, and n are used regardless of input size. The input array itself is not counted as auxiliary space.

---

### Q5 — Find the Complexity

```cpp
int mystery(int n) {
    if (n <= 1) return 1;
    return mystery(n/2) + mystery(n/2);
}
```

- What is the recurrence relation?
- Solve it using the Master Theorem.
- What is the time complexity?

> [!answer]- 
> Recurrence: T(n) = 2T(n/2) + O(1) Master Theorem: a = 2, b = 2, so n^(log_2 2) = n^1 = n. And f(n) = O(1) = O(n^(1 - epsilon)) for epsilon = 1 — Case 1 applies. Result: Theta(n) Intuitively — even though it looks like it doubles work each time, the subproblems shrink equally fast, so total work is linear.

---

### Q6 — Identify the Complexity

Insertion Sort on an already sorted array:

- [ ] $O(n^2)$
- [ ] $\Theta(n^2)$
- [ ] $\Theta(n)$
- [ ] $\Omega(n)$
- [ ] $o(n^2)$

> [!answer]- 
> O(n^2), Theta(n), Omega(n), and o(n^2) are all correct. On a sorted array, the inner loop never executes — each element is already in place, so only n comparisons are made total making it Theta(n) in this best case. It is still O(n^2) as a valid upper bound, and o(n^2) since it is strictly slower than quadratic here. Theta(n^2) is false — that only holds for the worst case.

---

### Q7 — Write it Yourself

Write a function that checks if an array has any duplicate elements. Analyse its time and space complexity. Can you do it in O(n) time? What is the space tradeoff?

> [!answer]- 
> Naive — O(n^2) time, O(1) space:
> 
> ```cpp
> bool hasDuplicate(vector<int>& arr) {
>     int n = arr.size();
>     for (int i = 0; i < n; i++)
>         for (int j = i+1; j < n; j++)
>             if (arr[i] == arr[j]) return true;
>     return false;
> }
> ```
> 
> Better — O(n) time, O(n) space:
> 
> ```cpp
> bool hasDuplicate(vector<int>& arr) {
>     unordered_set<int> seen;
>     for (int x : arr) {
>         if (seen.count(x)) return true;
>         seen.insert(x);
>     }
>     return false;
> }
> ```
> 
> The tradeoff is classic — you buy speed with memory. The hash set lets you check existence in O(1) average time, but now uses O(n) auxiliary space. This time-space tradeoff appears constantly in DSA.

---

## Quiz — Recursion and Recurrences

### Q1 — Identify the Complexity

```cpp
int fib(int n) {
    if (n <= 1) return n;
    return fib(n-1) + fib(n-2);
}
```

Which are true?

- [ ] $O(2^n)$
- [ ] $\Theta(2^n)$
- [ ] $O(n^2)$
- [ ] $\Omega(n)$

> [!answer]- 
> O(2^n) and Omega(n) are correct. The tight bound is actually Theta(phi^n) where phi = 1.618 (the golden ratio), making Theta(2^n) false though O(2^n) holds as a valid upper bound. O(n^2) is false — the growth is exponential not polynomial. Each call branches into two and the tree has depth n, giving roughly 2^n nodes total.

---

### Q2 — Trace the Recursion

What does the following print for n = 4? Trace it by hand first, then verify.

```cpp
void mystery(int n) {
    if (n == 0) return;
    mystery(n - 1);
    cout << n << " ";
}
```

> [!answer]- 
> Prints: 1 2 3 4 The recursive call happens before the print, so the function descends all the way to n=0 first, then prints on the way back up. This is the classic pattern of post-order execution in recursion — the work happens after the recursive call returns.

---

### Q3 — Apply the Master Theorem

Solve the following recurrences. Write your reasoning before revealing.

**a)** $T(n) = 3T(n/3) + O(n)$

**b)** $T(n) = 4T(n/2) + O(n)$

**c)** $T(n) = T(n/2) + O(1)$

> [!answer]- 
> a) a=3, b=3, n^(log_3 3) = n^1 = n. f(n) = O(n) = Theta(n) — Case 2. Result: Theta(n log n). This is essentially Merge Sort's recurrence.
> 
> b) a=4, b=2, n^(log_2 4) = n^2. f(n) = O(n) = O(n^(2-1)) — Case 1. Result: Theta(n^2). The recursive work dominates.
> 
> c) a=1, b=2, n^(log_2 1) = n^0 = 1. f(n) = O(1) = Theta(1) — Case 2. Result: Theta(log n). This is Binary Search.

---

### Q4 — Spot the Bug

What is wrong with this recursive function?

```cpp
int sum(int n) {
    return n + sum(n - 1);
}
```

> [!answer]- 
> Missing base case. There is no condition to stop the recursion — it will call itself with n=0, then n=-1, then n=-2 and so on forever until a stack overflow crashes the program. Fix:
> 
> ```cpp
> int sum(int n) {
>     if (n == 0) return 0;
>     return n + sum(n - 1);
> }
> ```

---

### Q5 — Write it Yourself

Write a recursive function to compute x^n. Then think — can you do it faster than O(n)? Hint: x^n = x^(n/2) * x^(n/2).

> [!answer]- 
> Naive — O(n):
> 
> ```cpp
> int power(int x, int n) {
>     if (n == 0) return 1;
>     return x * power(x, n - 1);
> }
> ```
> 
> Fast Exponentiation — O(log n):
> 
> ```cpp
> int power(int x, int n) {
>     if (n == 0) return 1;
>     int half = power(x, n / 2);
>     if (n % 2 == 0) return half * half;
>     else return x * half * half;
> }
> ```
> 
> Recurrence for fast version: T(n) = T(n/2) + O(1) = Theta(log n) by Master Theorem Case 2. This technique is called fast exponentiation or exponentiation by squaring and comes up repeatedly in number theory and competitive programming.

---

### Q6 — Identify the Pattern

Which execution model does this follow — is the work done before or after the recursive call, and what difference does it make?

```cpp
void mystery(int n) {
    if (n == 0) return;
    cout << n << " ";
    mystery(n - 1);
}
```

> [!answer]- 
> Work is done before the recursive call — this is pre-order execution. For n=4 it prints 4 3 2 1. Compare with Q2 where work was after the call (post-order) and printed 1 2 3 4. This distinction matters a lot in tree traversals — pre-order visits the node before its children, post-order visits after. You will see this again directly in [[07_01_02_Traversals_Inorder_Preorder_Postorder]].

---