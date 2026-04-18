
---

# 00_Foundations

## Files
- [[00_XX_00_Foundations_Files]]
## Notes
- [[00_01_Data_Structures_and_Abstract_Data_types]]
- [[00_02_Time_and_Space_Complexity]]
- [[00_03_Recursion_and_Recurrences]]
## Quiz
- [[00_QQ_Foundations_Quiz]]
---
## Quick Reference

### Data Structures and ADTs

> **Definition.** A _data type_ defines what kind of data is stored and what operations are valid on it.

> **Definition.** A _data structure_ is a way of organising and storing a collection of data in memory such that it can be accessed and modified efficiently.

> **Definition.** An _Abstract Data Type (ADT)_ defines a data structure by its behavior — the set of operations and their expected results — independent of any implementation.

| Term           | What it answers                               |
| -------------- | --------------------------------------------- |
| Data Type      | What kind is this single value?               |
| Data Structure | How do I organise many values in memory?      |
| ADT            | What operations do I need, regardless of how? |

---
### Asymptotic Notation

> **Definition.** $f(n) = O(g(n))$ if there exist positive constants $c$ and $n_0$ such that: $$f(n) \leq c \cdot g(n) \quad \text{for all } n \geq n_0$$

> **Definition.** $f(n) = \Omega(g(n))$ if there exist positive constants $c$ and $n_0$ such that: $$f(n) \geq c \cdot g(n) \quad \text{for all } n \geq n_0$$

> **Definition.** $f(n) = \Theta(g(n))$ if there exist positive constants $c_1$, $c_2$ and $n_0$ such that: $$c_1 \cdot g(n) \leq f(n) \leq c_2 \cdot g(n) \quad \text{for all } n \geq n_0$$

> **Definition.** $f(n) = o(g(n))$ if for every positive constant $c$, there exists $n_0$ such that: $$f(n) < c \cdot g(n) \quad \text{for all } n \geq n_0$$ Equivalently: $\lim_{n \to \infty} \frac{f(n)}{g(n)} = 0$

|Notation|Meaning|Strict?|
|---|---|---|
|$O(g)$|$f$ grows at most as fast as $g$|No|
|$\Omega(g)$|$f$ grows at least as fast as $g$|No|
|$\Theta(g)$|$f$ grows exactly like $g$|Both sides|
|$o(g)$|$f$ grows strictly slower than $g$|Yes|
|$\omega(g)$|$f$ grows strictly faster than $g$|Yes|

|Notation|Name|Example|
|---|---|---|
|$O(1)$|Constant|Array index access|
|$O(\log n)$|Logarithmic|Binary Search|
|$O(n)$|Linear|Linear Search|
|$O(n \log n)$|Linearithmic|Merge Sort|
|$O(n^2)$|Quadratic|Insertion Sort worst case|
|$O(2^n)$|Exponential|Naive Fibonacci|

**Simplification rules:**
- Drop all lower order terms: $3n^2 + 5n + 2 \rightarrow O(n^2)$
- Drop all constant coefficients: $7n \rightarrow O(n)$
- Keep only the dominant term: $n^2 + 2^n \rightarrow O(2^n)$

---
### Recurrences and Master Theorem

> **Definition.** A _recurrence relation_ expresses $T(n)$ in terms of $T$ on smaller inputs plus the cost of work done at the current level.

General form:
$$T(n) = a \cdot T\left(\frac{n}{b}\right) + f(n)$$

|Variable|Meaning|
|---|---|
|$a$|Number of recursive calls|
|$b$|Factor by which input shrinks|
|$f(n)$|Cost of work outside recursive calls|

**Master Theorem** — compare $f(n)$ with $n^{\log_b a}$:

|Case|Condition|Result|
|---|---|---|
|Case 1|$f(n) = O(n^{\log_b a - \epsilon})$|$T(n) = \Theta(n^{\log_b a})$|
|Case 2|$f(n) = \Theta(n^{\log_b a})$|$T(n) = \Theta(n^{\log_b a} \log n)$|
|Case 3|$f(n) = \Omega(n^{\log_b a + \epsilon})$|$T(n) = \Theta(f(n))$|

Common recurrences:

|Recurrence|Algorithm|Result|
|---|---|---|
|$T(n) = T(n/2) + O(1)$|Binary Search|$\Theta(\log n)$|
|$T(n) = T(n-1) + O(1)$|Linear recursion|$\Theta(n)$|
|$T(n) = 2T(n/2) + O(n)$|Merge Sort|$\Theta(n \log n)$|
|$T(n) = 2T(n-1) + O(1)$|Naive Fibonacci|$\Theta(2^n)$|
|$T(n) = T(n/2) + O(n)$|—|$\Theta(n)$|

---