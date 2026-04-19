
---
# Recursion and Recurrences

From our experience writing programs, we have already seen functions — a block of code you name and call whenever needed. Now imagine a function that, instead of calling some _other_ function to help it, calls _itself_. That is **recursion**.

It sounds circular at first — and it would be, if we weren't careful. The key insight is that each time a function calls itself, it does so on a _smaller_ version of the problem. Eventually the problem becomes so small that it can be answered directly, no further calls needed. That stopping point is called the **base case**.

> **Definition.** A _recursive algorithm_ is one that solves a problem by reducing it to one or more smaller instances of the same problem, until a base case is reached that can be solved directly.

Think of it like a stack of identical tasks. You're asked to sum the numbers 1 through 5. Instead of doing it all at once, you say — "I'll add 5 to whatever the sum of 1 through 4 is." Then the same logic applies to 1 through 4, and so on, until you reach "sum of just 1" which is obviously 1. Then the answers bubble back up.

$\sum{5} = $


Every recursive function has exactly two parts:

- **Base case** — the simplest input where you return directly without recursing
- **Recursive case** — where you call yourself on a smaller input and build the answer from it

If you forget the base case, the function calls itself forever — this is a **stack overflow**, and your program crashes.

```cpp
#include <iostream>
using namespace std;

// Sum of 1 to n
// Base case: n == 1, return 1
// Recursive case: n + sum(n-1)
// Time Complexity: O(n) — one call per value from n down to 1
// Space Complexity: O(n) — n stack frames live simultaneously in memory
int sum(int n) {
    if (n == 1) return 1;
    return n + sum(n - 1);
}

// Factorial of n
// Base case: n == 0, return 1
// Recursive case: n * factorial(n-1)
// Time Complexity: O(n)
// Space Complexity: O(n)
int factorial(int n) {
    if (n == 0) return 1;
    return n * factorial(n - 1);
}

int main() {
    cout << "Sum 1 to 5: " << sum(5) << endl;
    cout << "5! = " << factorial(5) << endl;
    return 0;
}
```

Notice that space complexity is $O(n)$ here — unlike the iterative version which is $O(1)$. This is because each recursive call is added to the **call stack** and stays there until the base case is hit. This hidden space cost is something to always keep in mind with recursion.

---
## Recurrence Relations

Once we start analyzing recursive algorithms, we need a way to express their time complexity mathematically. That tool is a **recurrence relation**.

> **Definition.** A _recurrence relation_ expresses the running time $T(n)$ of a recursive algorithm in terms of the running time on smaller inputs, plus the cost of the work done at the current level.

The general form looks like this:

$$T(n) = a \cdot T\left(\frac{n}{b}\right) + f(n)$$

Where $a$ is the number of recursive calls made, $n/b$ is the size of each subproblem, and $f(n)$ is the cost of work done outside the recursive calls.

For the sum example above — each call does $O(1)$ work and makes one call of size $n-1$, so:

$$T(n) = T(n-1) + O(1)$$

Expanding this out: $T(n) = T(n-2) + O(1) + O(1) = \ldots = O(n)$, which matches what we said.

---
## Solving Recurrences

There are three standard methods to solve recurrence relations.

**Substitution Method** — you guess the form of the answer and verify it using mathematical induction. Useful but requires intuition about what the answer should look like.

**Recursion Tree Method** — you draw out the recursive calls as a tree, compute the work done at each level, and sum across all levels. Very visual and good for building intuition before you can apply a formula directly.

**Master Theorem** — a direct formula for recurrences of the form $T(n) = a \cdot T(n/b) + f(n)$. It covers the majority of cases you will encounter in this course.

> **Master Theorem.** For $T(n) = a \cdot T(n/b) + f(n)$ where $a \geq 1$ and $b > 1$, compare $f(n)$ with $n^{\log_b a}$:
> 
> Case 1 — if $f(n) = O(n^{\log_b a - \epsilon})$ for some $\epsilon > 0$: $$T(n) = \Theta(n^{\log_b a})$$ Case 2 — if $f(n) = \Theta(n^{\log_b a})$: $$T(n) = \Theta(n^{\log_b a} \log n)$$ Case 3 — if $f(n) = \Omega(n^{\log_b a + \epsilon})$ for some $\epsilon > 0$: $$T(n) = \Theta(f(n))$$
> In plain terms — whichever grows faster between $f(n)$ and $n^{\log_b a}$ dominates. If they are equal, multiply by $\log n$.

A quick example — Merge Sort splits the array into 2 halves, recurses on both, then merges in $O(n)$

$$T(n) = 2T(n/2) + O(n)$$

Here $a = 2$, $b = 2$, so $n^{\log_2 2} = n^1 = n$. And $f(n) = O(n)$. They are equal — Case 2 applies:

$$T(n) = \Theta(n \log n)$$

Which is exactly the complexity of Merge Sort you will see when we get to [[08_03_Merge_Sort]].

>  For a visual walkthrough of recursion trees: [Visualgo Recursion](https://visualgo.net/en/recursion) 
>  For Master Theorem with worked examples: [Algorithms by Jeff Erickson, Chapter 1](https://jeffe.cs.illinois.edu/teaching/algorithms/book/01-recursion.pdf) — freely available.

---