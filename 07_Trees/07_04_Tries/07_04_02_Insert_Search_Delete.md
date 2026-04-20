
---

# Trie — Insert, Search, Delete

```cpp
#include <iostream>
#include <string>
using namespace std;

class Trie {
    TrieNode* root;

public:
    Trie() : root(new TrieNode()) {}

    // Insert a word — O(L) where L = word length
    void insert(const string& word) {
        TrieNode* curr = root;
        for (char c : word) {
            if (!curr->children.count(c))
                curr->children[c] = new TrieNode();
            curr = curr->children[c];
            curr->count++;  // increment prefix count
        }
        curr->isEnd = true;
    }

    // Search for exact word — O(L)
    bool search(const string& word) {
        TrieNode* curr = root;
        for (char c : word) {
            if (!curr->children.count(c)) return false;
            curr = curr->children[c];
        }
        return curr->isEnd;
    }

    // Check if any word starts with prefix — O(L)
    bool startsWith(const string& prefix) {
        TrieNode* curr = root;
        for (char c : prefix) {
            if (!curr->children.count(c)) return false;
            curr = curr->children[c];
        }
        return true;
    }

    // Count words with given prefix — O(L)
    int countPrefix(const string& prefix) {
        TrieNode* curr = root;
        for (char c : prefix) {
            if (!curr->children.count(c)) return 0;
            curr = curr->children[c];
        }
        return curr->count;
    }

    // Delete a word — O(L)
    bool deleteWord(const string& word) {
        return deleteHelper(root, word, 0);
    }

private:
    bool deleteHelper(TrieNode* curr, const string& word, int idx) {
        if (idx == word.size()) {
            if (!curr->isEnd) return false;  // word not found
            curr->isEnd = false;
            return curr->children.empty();   // true if node can be deleted
        }
        char c = word[idx];
        if (!curr->children.count(c)) return false;
        bool shouldDelete = deleteHelper(curr->children[c], word, idx+1);
        if (shouldDelete) {
            curr->children[c]->count--;
            delete curr->children[c];
            curr->children.erase(c);
            return !curr->isEnd && curr->children.empty();
        }
        curr->children[c]->count--;
        return false;
    }
};

int main() {
    Trie t;
    t.insert("cat");
    t.insert("can");
    t.insert("car");
    t.insert("dog");

    cout << t.search("cat")      << endl;  // 1
    cout << t.search("ca")       << endl;  // 0 — prefix, not complete word
    cout << t.startsWith("ca")   << endl;  // 1
    cout << t.countPrefix("ca")  << endl;  // 3 (cat, can, car)

    t.deleteWord("cat");
    cout << t.search("cat")      << endl;  // 0
    cout << t.startsWith("ca")   << endl;  // 1 — can, car still there
    return 0;
}
```

---

## Complexity Summary

|Operation|Time|Space|
|---|---|---|
|Insert|$O(L)$|$O(L)$ new nodes|
|Search|$O(L)$|$O(1)$|
|Prefix check|$O(L)$|$O(1)$|
|Delete|$O(L)$|$O(1)$|
|Total space|—|$O(\sum L_i)$|

All operations are $O(L)$ — independent of how many words are stored. This is the trie's key advantage over hash maps for prefix queries.

---
