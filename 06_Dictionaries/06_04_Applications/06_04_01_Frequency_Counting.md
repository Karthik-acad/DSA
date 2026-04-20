
---

# Frequency Counting

One of the most natural uses of a hash table — count how many times each element appears in a collection. The key is the element, the value is its count.

> **Pattern.** _Frequency counting_ uses a hash map where keys are elements and values are their occurrence counts. Each element takes $O(1)$ to process, giving $O(n)$ total.

```cpp
#include <iostream>
#include <unordered_map>
#include <vector>
#include <string>
using namespace std;

// Count frequency of integers — O(n) time, O(k) space where k = distinct elements
unordered_map<int, int> countFreq(vector<int>& arr) {
    unordered_map<int, int> freq;
    for (int x : arr) freq[x]++;
    return freq;
}

// Find the most frequent element — O(n)
int mostFrequent(vector<int>& arr) {
    unordered_map<int, int> freq;
    for (int x : arr) freq[x]++;
    int maxFreq = 0, result = -1;
    for (auto& [k, v] : freq)
        if (v > maxFreq) { maxFreq = v; result = k; }
    return result;
}

// Find first non-repeating character — O(n)
char firstUnique(const string& s) {
    unordered_map<char, int> freq;
    for (char c : s) freq[c]++;
    for (char c : s)
        if (freq[c] == 1) return c;
    return '\0';
}

// Top k frequent elements — O(n log k)
// Uses hash map + min-heap of size k
#include <queue>
vector<int> topKFrequent(vector<int>& arr, int k) {
    unordered_map<int, int> freq;
    for (int x : arr) freq[x]++;

    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> minPQ;
    for (auto& [val, cnt] : freq) {
        minPQ.push({cnt, val});
        if (minPQ.size() > k) minPQ.pop();
    }

    vector<int> result;
    while (!minPQ.empty()) {
        result.push_back(minPQ.top().second);
        minPQ.pop();
    }
    return result;
}

int main() {
    vector<int> arr = {1, 3, 2, 1, 4, 1, 3, 2, 1};
    auto freq = countFreq(arr);
    for (auto& [k, v] : freq)
        cout << k << ": " << v << endl;

    cout << "Most frequent: " << mostFrequent(arr) << endl;  // 1

    string s = "leetcode";
    cout << "First unique: " << firstUnique(s) << endl;  // l

    auto topK = topKFrequent(arr, 2);
    cout << "Top 2: ";
    for (int x : topK) cout << x << " ";  // 1 3 (or 1 2)
    return 0;
}
```

---
