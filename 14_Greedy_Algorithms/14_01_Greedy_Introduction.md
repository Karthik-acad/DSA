
Now Greedy. Here is `14_01_Greedy_Introduction.md`:

---

# Greedy Introduction

Dynamic programming considers all possible choices and picks the best. **Greedy algorithms** make the locally optimal choice at each step without reconsidering — trusting that local optimality leads to global optimality.

> **Definition.** A _greedy algorithm_ makes the choice that looks best at the current moment — the locally optimal choice — and never backtracks. It is correct when the problem has the **greedy choice property** and **optimal substructure**.

> **Definition.** The _greedy choice property_ means a globally optimal solution can be reached by making locally optimal (greedy) choices. The choice made at each step need not depend on choices yet to be made.

The key question for any greedy algorithm — **can we prove that the greedy choice is safe?** The two standard proof techniques are:

**Exchange argument** — assume an optimal solution that differs from the greedy solution. Show you can exchange its first differing choice for the greedy choice without making the solution worse. Repeat until the optimal solution equals the greedy solution.

**Inductive proof** — show the greedy choice leaves a subproblem that is at least as good as any other choice.

---

## Greedy vs DP

||Greedy|DP|
|---|---|---|
|Choices|One at a time, never reconsidered|All subproblems considered|
|Correctness|Requires proof of greedy choice property|Always correct if recurrence is right|
|Time|Usually $O(n \log n)$ or better|Usually $O(n^2)$ or more|
|When|Greedy choice property holds|Overlapping subproblems, no greedy choice property|

Classic example — **0-1 Knapsack** has no greedy choice property (must use DP), but **fractional knapsack** does (greedy by value/weight ratio works).

---

Here is `14_02_Activity_Selection.md`:

---

# Activity Selection

Given $n$ activities each with a start time $s_i$ and finish time $f_i$, select the maximum number of non-overlapping activities.

> **Greedy choice:** always select the activity with the earliest finish time that doesn't conflict with already-selected activities.

**Proof (exchange argument):** Suppose an optimal solution does not include the activity $a_1$ with the earliest finish time. Since $a_1$ finishes before any other activity, we can replace the first selected activity in the optimal solution with $a_1$ — $a_1$ finishes no later, so it conflicts with no more future activities. The solution remains optimal. Hence we can always include $a_1$.

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

struct Activity { int start, finish, idx; };

// Time: O(n log n) for sort, O(n) selection
vector<int> activitySelection(vector<Activity>& acts) {
    sort(acts.begin(), acts.end(),
         [](const Activity& a, const Activity& b) {
             return a.finish < b.finish;  // sort by finish time
         });

    vector<int> selected;
    int lastFinish = -1;

    for (auto& a : acts) {
        if (a.start >= lastFinish) {  // no conflict
            selected.push_back(a.idx);
            lastFinish = a.finish;
        }
    }
    return selected;
}

int main() {
    vector<Activity> acts = {
        {1,2,0}, {3,4,1}, {0,6,2}, {5,7,3},
        {8,9,4}, {5,9,5}, {3,8,6}
    };
    auto sel = activitySelection(acts);
    cout << sel.size() << " activities selected: ";
    for (int i : sel) cout << i << " ";  // 3 activities
    return 0;
}
```

**Complexity:** $O(n \log n)$ dominated by sort.

---

Here is `14_03_Fractional_Knapsack.md`:

---

# Fractional Knapsack

Unlike 0-1 Knapsack where items are indivisible, in the fractional variant you can take any fraction of an item.

> **Greedy choice:** sort items by value-to-weight ratio in descending order. Take as much of each item as possible (or the entire item if it fits).

**Proof:** Suppose we take a fraction of item $j$ instead of item $i$ where $v_i/w_i > v_j/w_j$. Replacing an equal weight of $j$ with $i$ increases total value. So the greedy choice (highest ratio first) is optimal.

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

struct Item { int weight, value; double ratio; };

// Time: O(n log n), Space: O(1)
double fractionalKnapsack(vector<Item>& items, int W) {
    sort(items.begin(), items.end(),
         [](const Item& a, const Item& b) {
             return a.ratio > b.ratio;  // sort by value/weight descending
         });

    double totalValue = 0;
    int remaining = W;

    for (auto& item : items) {
        if (remaining <= 0) break;
        if (item.weight <= remaining) {
            totalValue += item.value;    // take full item
            remaining  -= item.weight;
        } else {
            totalValue += (double)remaining / item.weight * item.value;
            remaining = 0;              // take fraction
        }
    }
    return totalValue;
}

int main() {
    vector<Item> items = {
        {10, 60, 6.0}, {20, 100, 5.0}, {30, 120, 4.0}
    };
    cout << fractionalKnapsack(items, 50) << endl;  // 240.0
    return 0;
}
```

**Complexity:** $O(n \log n)$ for sort, $O(n)$ selection.

---

Here is `14_04_Huffman_Encoding.md`:

---

# Huffman Encoding

Given character frequencies, assign variable-length binary codes to minimize total encoded length. Frequent characters get shorter codes, rare characters get longer ones.

> **Greedy choice:** always merge the two nodes with the lowest frequencies — they will have the longest codes, so making them as deep as possible minimizes total encoded bits.

> **Definition.** A _Huffman tree_ is a full binary tree where each leaf represents a character. The code for each character is its path from root to leaf (0 = left, 1 = right). Huffman coding produces the optimal prefix-free code.

```cpp
#include <iostream>
#include <queue>
#include <unordered_map>
#include <string>
using namespace std;

struct HuffNode {
    char ch;
    int freq;
    HuffNode* left;
    HuffNode* right;
    HuffNode(char c, int f) : ch(c), freq(f), left(nullptr), right(nullptr) {}
};

struct Compare {
    bool operator()(HuffNode* a, HuffNode* b) { return a->freq > b->freq; }
};

// Build Huffman tree — Time: O(n log n)
HuffNode* buildTree(unordered_map<char,int>& freq) {
    priority_queue<HuffNode*, vector<HuffNode*>, Compare> minPQ;

    for (auto& [ch, f] : freq)
        minPQ.push(new HuffNode(ch, f));

    while (minPQ.size() > 1) {
        HuffNode* left  = minPQ.top(); minPQ.pop();
        HuffNode* right = minPQ.top(); minPQ.pop();
        HuffNode* merged = new HuffNode('\0', left->freq + right->freq);
        merged->left  = left;
        merged->right = right;
        minPQ.push(merged);
    }
    return minPQ.top();
}

// Generate codes by traversing tree
void generateCodes(HuffNode* root, string code,
                   unordered_map<char,string>& codes) {
    if (!root) return;
    if (!root->left && !root->right) {
        codes[root->ch] = code.empty() ? "0" : code;  // single character edge case
        return;
    }
    generateCodes(root->left,  code + "0", codes);
    generateCodes(root->right, code + "1", codes);
}

int main() {
    string text = "abracadabra";
    unordered_map<char,int> freq;
    for (char c : text) freq[c]++;

    HuffNode* root = buildTree(freq);
    unordered_map<char,string> codes;
    generateCodes(root, "", codes);

    cout << "Character codes:" << endl;
    for (auto& [ch, code] : codes)
        cout << ch << ": " << code << " (freq=" << freq[ch] << ")" << endl;

    // Compute encoded length
    int encodedLen = 0;
    for (auto& [ch, code] : codes)
        encodedLen += freq[ch] * code.size();
    cout << "Encoded length: " << encodedLen << " bits" << endl;
    cout << "Original (8-bit ASCII): " << text.size() * 8 << " bits" << endl;
    return 0;
}
```

---

## Optimality Proof Sketch

At the bottom of any optimal prefix-free code tree, there must be two leaves at maximum depth. We can always swap them so the two lowest-frequency characters are at the deepest level without increasing total cost. This justifies always merging the two minimum-frequency nodes.

**Complexity:** $O(n \log n)$ — $n$ insertions and $n-1$ merges each $O(\log n)$ in the min-heap.

---

Here is `14_05_Job_Scheduling.md`:

---

# Job Scheduling

Given $n$ jobs each with a deadline $d_i$ and profit $p_i$ (all taking unit time), schedule jobs to maximize profit. Only one job can run at a time and each job must finish by its deadline.

> **Greedy choice:** sort jobs by profit in descending order. For each job, assign it to the latest available time slot before its deadline.

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

struct Job { int id, deadline, profit; };

// Time: O(n^2), Space: O(n)
vector<int> jobScheduling(vector<Job>& jobs) {
    sort(jobs.begin(), jobs.end(),
         [](const Job& a, const Job& b) { return a.profit > b.profit; });

    int maxDeadline = 0;
    for (auto& j : jobs) maxDeadline = max(maxDeadline, j.deadline);

    vector<int> slot(maxDeadline + 1, -1);  // slot[t] = job assigned to time t
    vector<int> selected;

    for (auto& j : jobs) {
        // Find latest free slot before deadline
        for (int t = j.deadline; t >= 1; t--) {
            if (slot[t] == -1) {
                slot[t] = j.id;
                selected.push_back(j.id);
                break;
            }
        }
    }
    return selected;
}

// O(n log n) version using Union-Find — find the latest free slot efficiently
int findSlot(vector<int>& parent, int t) {
    if (parent[t] == t) return t;
    return parent[t] = findSlot(parent, parent[t]);
}

vector<int> jobSchedulingFast(vector<Job>& jobs, int maxDeadline) {
    sort(jobs.begin(), jobs.end(),
         [](const Job& a, const Job& b) { return a.profit > b.profit; });

    vector<int> parent(maxDeadline + 1);
    iota(parent.begin(), parent.end(), 0);  // parent[i] = i initially
    vector<int> selected;

    for (auto& j : jobs) {
        int slot = findSlot(parent, j.deadline);
        if (slot > 0) {
            selected.push_back(j.id);
            parent[slot] = slot - 1;  // mark slot as used, point to previous
        }
    }
    return selected;
}

int main() {
    vector<Job> jobs = {{1,4,20},{2,1,10},{3,1,40},{4,1,30}};
    auto result = jobScheduling(jobs);
    int profit = 0;
    for (int id : result) {
        for (auto& j : jobs) if (j.id == id) profit += j.profit;
    }
    cout << "Max profit: " << profit << endl;  // 60 (jobs 1 and 3)
    return 0;
}
```

**Complexity:** Naive $O(n^2)$, Union-Find version $O(n \log n)$.

---

Here is `14_QQ_Greedy_Quiz.md`:

---

## Quiz — Greedy Algorithms

**Q1 — Greedy vs DP** For each problem, identify whether greedy or DP is appropriate and why:

- Fractional Knapsack
- 0-1 Knapsack
- Activity Selection
- Longest Common Subsequence

> [!answer]- Answer Fractional Knapsack → Greedy. Can take fractions, so always taking highest value/weight ratio item is provably optimal. 0-1 Knapsack → DP. Items are indivisible. Greedy by ratio can fail: items [(weight=10,val=10),(w=6,val=7),(w=6,val=7)] with W=12. Greedy takes item 1 (ratio 1.0) leaving no room. DP takes items 2+3 for value 14. Activity Selection → Greedy. Earliest finish time greedy is provably optimal by exchange argument. LCS → DP. No greedy choice property — choosing locally "best" matching characters may miss longer subsequences.

**Q2 — Activity Selection by Hand** Activities: [(0,6),(1,4),(3,5),(3,9),(5,7),(5,9),(6,10),(8,11),(8,12),(2,14),(12,16)]. Select maximum non-overlapping activities using greedy.

> [!answer]- Answer Sort by finish time: (1,4),(3,5),(3,9),(0,6),(5,7),(5,9),(6,10),(8,11),(8,12),(2,14),(12,16). Select (1,4): lastFinish=4. Next (3,5): start=3<4 conflict skip? No wait 3<4=lastFinish, conflict. Actually start>=lastFinish is required. 3<4, conflict, skip. Hmm, let me redo. Actually (1,4) means start=1,finish=4. Select it, lastFinish=4. (3,5): start=3 < lastFinish=4, skip. (3,9): skip. (0,6): skip. (5,7): start=5 >= 4, select, lastFinish=7. (5,9): skip. (6,10): skip. (8,11): start=8>=7, select, lastFinish=11. (8,12): skip. (2,14): skip. (12,16): start=12>=11, select, lastFinish=16. Selected: 4 activities. Intervals: (1,4),(5,7),(8,11),(12,16).

**Q3 — Huffman Codes** Characters and frequencies: a=5, b=2, c=1, d=3, e=4. Build the Huffman tree and give each character's code.

> [!answer]- Answer Min-heap initially: c(1),b(2),d(3),e(4),a(5). Merge c(1)+b(2)=cb(3). Heap: cb(3),d(3),e(4),a(5). Merge cb(3)+d(3)=cbd(6). Heap: e(4),a(5),cbd(6). Merge e(4)+a(5)=ea(9). Heap: cbd(6),ea(9). Merge cbd(6)+ea(9)=root(15). Tree: root→left=cbd(6), right=ea(9). cbd→left=cb(3),right=d(3). cb→left=c(1),right=b(2). ea→left=e(4),right=a(5). Codes: c=000, b=001, d=01, e=10, a=11. Total bits = 5_2 + 4_2 + 3_2 + 2_3 + 1*3 = 10+8+6+6+3 = 33 bits.

**Q4 — Fractional Knapsack** Items: (w=10,v=60),(w=20,v=100),(w=30,v=120), W=50. Find optimal solution.

> [!answer]- Answer Ratios: 60/10=6, 100/20=5, 120/30=4. Sort: item1(r=6), item2(r=5), item3(r=4). Take all item1 (w=10): value=60, remaining=40. Take all item2 (w=20): value+=100, remaining=20. Take 20/30 of item3: value += 20/30 * 120 = 80, remaining=0. Total value = 60+100+80 = 240.

**Q5 — Write it Yourself** Given an array of non-negative integers representing coin denominations and a target, greedily make change. Does the greedy approach always work? Give a counterexample if not.

> [!answer]- Answer
> 
> ```cpp
> vector<int> greedyChange(vector<int>& coins, int target) {
>     sort(coins.rbegin(), coins.rend());  // largest first
>     vector<int> result;
>     for (int c : coins) {
>         while (target >= c) { result.push_back(c); target -= c; }
>     }
>     return result;
> }
> ```
> 
> Greedy does NOT always work. Counterexample: coins=[1,3,4], target=6. Greedy: take 4 (remaining 2), take 1, take 1 → 3 coins. Optimal: 3+3=2 coins. Greedy gives suboptimal answer. Greedy works for canonical coin systems like US coins (1,5,10,25) but fails for arbitrary denominations. DP (coin change) is required for the general case.

---

And finally `14_00_Greedy_Algorithms.md`:

---

# Greedy Algorithms

## Notes

- [[14_01_Greedy_Introduction]]
- [[14_02_Activity_Selection]]
- [[14_03_Fractional_Knapsack]]
- [[14_04_Huffman_Encoding]]
- [[14_05_Job_Scheduling]]

## Quiz

- [[14_QQ_Greedy_Quiz]]

---

## Quick Reference

### When Greedy Works

Both must hold:

- **Greedy choice property** — local optimal → global optimal
- **Optimal substructure** — optimal solution built from optimal subproblems

### Proof Techniques

**Exchange argument:** Assume any optimal solution. Show you can swap its first non-greedy choice for the greedy choice without getting worse. Conclude greedy is optimal.

### Key Problems

|Problem|Greedy strategy|Time|
|---|---|---|
|Activity Selection|Earliest finish time first|$O(n \log n)$|
|Fractional Knapsack|Highest value/weight ratio first|$O(n \log n)$|
|Huffman Encoding|Merge two lowest frequency nodes|$O(n \log n)$|
|Job Scheduling|Highest profit first, latest free slot|$O(n^2)$ / $O(n \log n)$|
|Dijkstra's (shortest path)|Nearest unvisited vertex|$O((V+E)\log V)$|
|Prim's (MST)|Minimum edge crossing cut|$O((V+E)\log V)$|
|Kruskal's (MST)|Minimum weight edge not forming cycle|$O(E \log E)$|

### Greedy vs DP

||Greedy|DP|
|---|---|---|
|0-1 Knapsack|✗|✓|
|Fractional Knapsack|✓|Overkill|
|Shortest path (non-negative)|✓ Dijkstra|Also works|
|Shortest path (negative edges)|✗|✓ Bellman-Ford|
|Coin change (arbitrary)|✗|✓|
|Activity selection|✓|Also works|

---

That completes the entire DSA vault baby! Every chapter from Foundations through Greedy is done — notes, quizzes, and summaries all in the same style throughout. 🎉