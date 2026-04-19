
---
# Time and Space Complexity

From the previous note we understood that different data structures suit different problems. But that raises the next question — if two data structures both _solve_ the problem, how do we decide which one is _better_? The answer is efficiency. And to measure efficiency, we need a language to talk about it — that language is **complexity analysis**.

Think about this: you're looking for a friend's house in a new city. One way is to start from the first street and check every house one by one. Another way is to use a map and go directly. Both get you there — but one is clearly smarter. Complexity analysis is how we formally capture _how smart_ an approach is, and more importantly, _how it behaves as the city grows larger_.

We measure two kinds of cost:

> **Definition.** _Time Complexity_ is the number of elementary operations an algorithm performs, expressed as a function of the input size $n$. We assume each elementary operation takes a fixed, constant amount of time.

> **Definition.** _Space Complexity_ is the total memory an algorithm uses during its execution relative to input size $n$. This includes the **input space** (memory for the input itself) and **auxiliary space** (any extra memory the algorithm needs beyond the input).

In plain terms:

- **Time Complexity** — how many _steps_ does the algorithm take as $n$ grows?
- **Space Complexity** — how many _extra_ memory slots does it need to get the job done?

The reason we care about _growth_ and not raw time is that raw time depends on your machine, your compiler, your mood. Growth behavior is universal — an algorithm that does $n^2$ steps on your laptop does $n^2$ steps everywhere.

---
## Asymptotic Notation

We don't usually care about the exact step count — we care about the _shape_ of growth. For this we use asymptotic notation.

> **Definition.** **Big-O notation** $O(f(n))$ describes an _upper bound_ on the growth of an algorithm — the worst case. We say an algorithm is $O(f(n))$ if its step count grows no faster than $f(n)$ for large $n$.

The most common classes you'll encounter, from fastest to slowest growing:

| Notation      | Name         | Intuition                             |
| ------------- | ------------ | ------------------------------------- |
| $O(1)$        | Constant     | Same cost no matter how big $n$ is    |
| $O(\log n)$   | Logarithmic  | Cuts the problem in half each step    |
| $O(n)$        | Linear       | Touches each element once             |
| $O(n \log n)$ | Linearithmic | Linear but with a small multiplier    |
| $O(n^2)$      | Quadratic    | For each element, looks at all others |
| $O(2^n)$      | Exponential  | Tries every possible combination      |

There are two other notations worth knowing — $\Omega(f(n))$ (Omega) gives a _lower bound_ (best case), and $\Theta(f(n))$ (Theta) means the algorithm grows _exactly_ like $f(n)$ in both best and worst case. In practice, Big-O is what gets used most.

Here is the reformatted version — the key change is adding blank lines around every `$$` block so Obsidian doesn't merge them with surrounding prose:

---
### The Mathematics Behind the Notation

The intuition is clear, but here is what each notation precisely means — this is how mathematicians and algorithm designers formally define them.

> [!definition] O(g(n)) — Upper Bound $f(n) = O(g(n))$ if there exist positive constants $c$ and $n_0$ such that: $$f(n) \leq c \cdot g(n) \quad \text{for all } n \geq n_0$$ In other words, beyond some point $n_0$, $g(n)$ scaled by some constant $c$ is always an upper bound on $f(n)$.

> [!definition] Ω(g(n)) — Lower Bound $f(n) = \Omega(g(n))$ if there exist positive constants $c$ and $n_0$ such that: $$f(n) \geq c \cdot g(n) \quad \text{for all } n \geq n_0$$ Symmetric to Big-O — $g(n)$ scaled by $c$ is always a lower bound on $f(n)$ beyond $n_0$.

> [!definition] Θ(g(n)) — Tight Bound $f(n) = \Theta(g(n))$ if there exist positive constants $c_1$, $c_2$ and $n_0$ such that: $$c_1 \cdot g(n) \leq f(n) \leq c_2 \cdot g(n) \quad \text{for all } n \geq n_0$$ $f(n)$ is sandwiched between two scaled versions of $g(n)$ — it is simultaneously $O(g(n))$ and $\Omega(g(n))$.

> [!definition] o(g(n)) — Strict Upper Bound $f(n) = o(g(n))$ if for every positive constant $c$, there exists $n_0$ such that: $$f(n) < c \cdot g(n) \quad \text{for all } n \geq n_0$$

The difference between Big-O and small-o is subtle but important. Big-O says $f$ grows at most as fast as $g$ — they could grow at the same rate and it still holds. Small-o is strictly slower — $f$ becomes completely negligible compared to $g$ as $n \to \infty$. Formally this is equivalent to:

$$\lim_{n \to \infty} \frac{f(n)}{g(n)} = 0$$

A concrete example: $n = O(n^2)$ is true, and so is $n = o(n^2)$ because $n$ grows strictly slower than $n^2$. But $n^2 = O(n^2)$ is true while $n^2 = o(n^2)$ is false — they grow at the same rate so $n^2$ is not strictly slower than itself.

There is also small-omega ($\omega$) which is to $\Omega$ what small-o is to $O$ — strictly faster growth — but it is rarely used outside of theoretical proofs.

Summarising all five together:

|Notation|Intuition|Strict?|
|---|---|---|
|$O(g)$|$f$ grows at most as fast as $g$|No — equal growth allowed|
|$\Omega(g)$|$f$ grows at least as fast as $g$|No — equal growth allowed|
|$\Theta(g)$|$f$ grows at exactly the same rate as $g$|Both sides bounded|
|$o(g)$|$f$ grows strictly slower than $g$|Yes|
|$\omega(g)$|$f$ grows strictly faster than $g$|Yes|
```dataviewjs
const nValues = Array.from({length: 50}, (_, i) => i + 1);

const data = [
    {
        x: nValues,
        y: nValues.map(n => Math.log2(n)),
        name: 'O(log n)',
        mode: 'lines',
        line: { color: '#2ecc71', width: 3 }
    },
    {
        x: nValues,
        y: nValues,
        name: 'O(n)',
        mode: 'lines',
        line: { color: '#3498db', width: 3 }
    },
    {
        x: nValues,
        y: nValues.map(n => n * Math.log2(n)),
        name: 'O(n log n)',
        mode: 'lines',
        line: { color: '#9b59b6', width: 3 }
    },
    {
        x: nValues,
        y: nValues.map(n => n**2),
        name: 'O(n²)',
        mode: 'lines',
        line: { color: '#e74c3c', width: 3 }
    }
];

const layout = {
    title: 'Interactive Big O Complexity',
    paper_bgcolor: 'rgba(0,0,0,0)', // Transparent background for theme compatibility
    plot_bgcolor: '#fdfdfd',      // Clean light mode background
    xaxis: { title: 'Input Size (n)', gridcolor: '#eee' },
    yaxis: { title: 'Operations', gridcolor: '#eee' },
    hovermode: 'x unified',       // Shows all values when hovering over X-axis
    margin: { t: 50, b: 50, l: 50, r: 50 }
};

const config = {
    responsive: true,
    displaylogo: false,
    modeBarButtonsToRemove: ['lasso2d', 'select2d'] // Cleans up the toolbar
};

// Use the Plotly plugin's internal renderer
window.renderPlotly(this.container, data, layout, config);

```
---
## Seeing it in Code

The best way to build intuition is to look at real code and ask — _how does the number of steps grow with input size?_

```cpp
#include <iostream>
#include <vector>
using namespace std;

// O(1) — Constant Time
// No matter how large the shelf is, we jump directly to index 0.
// The number of steps does not change with n.
int getFirst(const vector<int>& shelf) {
    return shelf[0];
}

// O(n) — Linear Time
// In the worst case (item not on shelf, or at the very end),
// we scan every single item once. Steps grow proportionally with n.
// Space: O(1) — we only use one integer 'steps', regardless of shelf size.
void linearSearch(const vector<int>& shelf, int target) {
    int steps = 0;
    for (int id : shelf) {
        steps++;
        if (id == target) {
            cout << "Found after " << steps << " steps." << endl;
            return;
        }
    }
    cout << "Not found after " << steps << " steps." << endl;
}

// O(n^2) — Quadratic Time
// For every item on the shelf, we scan every other item.
// If n doubles, the work quadruples.
// Space: O(1) — no extra memory proportional to input.
void findDuplicates(const vector<int>& shelf) {
    int n = shelf.size();
    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            if (shelf[i] == shelf[j]) {
                cout << "Duplicate found: " << shelf[i] << endl;
            }
        }
    }
}

int main() {
    vector<int> shelf = {101, 202, 303, 404, 101};

    cout << "First item: " << getFirst(shelf) << endl;
    linearSearch(shelf, 404);
    findDuplicates(shelf);

    return 0;
}
```

Notice how the _structure_ of the loops tells you the complexity directly — one loop over $n$ elements is $O(n)$, a loop inside a loop is $O(n^2)$, no loop at all is $O(1)$. This pattern holds almost universally and is the first thing to look for when analyzing any piece of code (there are exceptions as you will see in future in [[03_04_08_Stock_Span_Problem]]). 

>  For interactive visualization of how these grow: [Big-O Cheat Sheet](https://www.bigocheatsheet.com/) 
>  For worked examples with proofs: [Algorithms by Jeff Erickson, Chapter 0](https://jeffe.cs.illinois.edu/teaching/algorithms/book/00-intro.pdf) — freely available, excellent resource.

---
