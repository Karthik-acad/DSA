
---

# Skip Lists

Before hash tables became dominant, **skip lists** were a serious contender for dictionary implementations. They are elegant — a probabilistic data structure built on top of linked lists that achieves $O(\log n)$ average search, insert, and delete without the complexity of balanced trees.

> **Definition.** A _skip list_ is a layered linked list where each layer is a sparser version of the layer below. The bottom layer contains all elements in sorted order. Each higher layer contains a random subset of the elements below it, acting as an express lane for faster traversal.

Think of it like an express train system. The local train stops at every station (bottom layer). The express train skips most stations and only stops at major ones (higher layers). To reach your destination, you ride the express as far as possible, then switch to the local for the final stretch.

---

## Structure

A skip list with $n$ elements has:

- **Level 0** — all $n$ elements in sorted order (the full linked list)
- **Level 1** — approximately $n/2$ elements (every other node promoted)
- **Level 2** — approximately $n/4$ elements
- **Level $k$** — approximately $n/2^k$ elements
- **Maximum levels** — $O(\log n)$ expected

Each node is promoted to the next level with probability $p = 0.5$ (coin flip). This randomness is what gives skip lists their probabilistic guarantees.

```cpp
#include <iostream>
#include <vector>
#include <cstdlib>
#include <ctime>
#include <climits>
using namespace std;

const int MAX_LEVEL = 16;
const float P = 0.5;

struct SkipNode {
    int key;
    vector<SkipNode*> forward;  // forward[i] = next node at level i
    SkipNode(int k, int level) : key(k), forward(level + 1, nullptr) {}
};

class SkipList {
    SkipNode* header;
    int level;  // current maximum level

    int randomLevel() {
        int lvl = 0;
        while ((float)rand()/RAND_MAX < P && lvl < MAX_LEVEL)
            lvl++;
        return lvl;
    }

public:
    SkipList() : level(0) {
        header = new SkipNode(INT_MIN, MAX_LEVEL);
        srand(time(0));
    }

    // Search — O(log n) average
    bool search(int key) {
        SkipNode* curr = header;
        for (int i = level; i >= 0; i--) {
            while (curr->forward[i] && curr->forward[i]->key < key)
                curr = curr->forward[i];
        }
        curr = curr->forward[0];
        return curr && curr->key == key;
    }

    // Insert — O(log n) average
    void insert(int key) {
        vector<SkipNode*> update(MAX_LEVEL + 1);
        SkipNode* curr = header;

        // Find update positions at each level
        for (int i = level; i >= 0; i--) {
            while (curr->forward[i] && curr->forward[i]->key < key)
                curr = curr->forward[i];
            update[i] = curr;
        }

        int newLevel = randomLevel();
        if (newLevel > level) {
            for (int i = level + 1; i <= newLevel; i++)
                update[i] = header;
            level = newLevel;
        }

        SkipNode* newNode = new SkipNode(key, newLevel);
        for (int i = 0; i <= newLevel; i++) {
            newNode->forward[i] = update[i]->forward[i];
            update[i]->forward[i] = newNode;
        }
    }

    // Delete — O(log n) average
    void remove(int key) {
        vector<SkipNode*> update(MAX_LEVEL + 1);
        SkipNode* curr = header;

        for (int i = level; i >= 0; i--) {
            while (curr->forward[i] && curr->forward[i]->key < key)
                curr = curr->forward[i];
            update[i] = curr;
        }

        curr = curr->forward[0];
        if (!curr || curr->key != key) return;

        for (int i = 0; i <= level; i++) {
            if (update[i]->forward[i] != curr) break;
            update[i]->forward[i] = curr->forward[i];
        }
        delete curr;
    }

    void print() {
        for (int i = level; i >= 0; i--) {
            SkipNode* curr = header->forward[i];
            cout << "Level " << i << ": ";
            while (curr) { cout << curr->key << " "; curr = curr->forward[i]; }
            cout << endl;
        }
    }
};

int main() {
    SkipList sl;
    sl.insert(3); sl.insert(6); sl.insert(7);
    sl.insert(9); sl.insert(12); sl.insert(19);
    sl.print();
    cout << sl.search(6) << endl;   // 1 (true)
    cout << sl.search(15) << endl;  // 0 (false)
    sl.remove(6);
    cout << sl.search(6) << endl;   // 0 (false)
    return 0;
}
```

---

## Complexity Summary

|Operation|Average|Worst Case|
|---|---|---|
|Search|$O(\log n)$|$O(n)$|
|Insert|$O(\log n)$|$O(n)$|
|Delete|$O(\log n)$|$O(n)$|
|Space|$O(n)$ expected|$O(n \log n)$|

The worst case is theoretically $O(n)$ — if every coin flip promotes every element to every level (extremely unlikely). In practice with $p=0.5$, the expected number of comparisons for a search is $2\log n$.

---

## Skip List vs Balanced BST

||Skip List|Balanced BST (AVL/RB)|
|---|---|---|
|Search|$O(\log n)$ avg|$O(\log n)$ guaranteed|
|Insert|$O(\log n)$ avg|$O(\log n)$ guaranteed|
|Implementation|Simpler|Complex rotations|
|Ordering|Yes|Yes|
|Concurrent access|Easier to make thread-safe|Harder|
|Cache|Poor (pointers)|Poor (pointers)|

Skip lists were popularized by William Pugh in 1990. Redis uses skip lists internally for its sorted sets — a real production system choosing skip lists over balanced BSTs precisely because concurrent modification is simpler.

> 📖 Original paper: [Skip Lists — William Pugh, 1990](https://15721.courses.cs.cmu.edu/spring2018/papers/08-oltpindexes1/pugh-skiplists-cacm1990.pdf)

---
