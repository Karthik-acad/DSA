
---

# Subarray Sum Problems

Hash maps unlock a powerful technique for subarray problems — instead of nested loops to try every subarray, we use prefix sums combined with a hash map to find subarrays satisfying a condition in $O(n)$.

The core idea — if $\text{prefixSum}[j] - \text{prefixSum}[i] = \text{target}$, then the subarray from $i+1$ to $j$ sums to target. So we ask: for the current prefix sum, has a prefix sum of $\text{current} - \text{target}$ been seen before?

> **Pattern.** Store prefix sums in a hash map as you scan. At each position, check if $\text{prefixSum} - \text{target}$ exists in the map. If yes, a valid subarray ends here.

```cpp
#include <iostream>
#include <unordered_map>
#include <vector>
using namespace std;

// Count subarrays with sum = target — O(n), O(n)
int subarraySum(vector<int>& arr, int target) {
    unordered_map<int, int> prefixCount;
    prefixCount[0] = 1;  // empty prefix
    int sum = 0, count = 0;

    for (int x : arr) {
        sum += x;
        // If (sum - target) was seen before, those are valid subarrays
        if (prefixCount.count(sum - target))
            count += prefixCount[sum - target];
        prefixCount[sum]++;
    }
    return count;
}

// Longest subarray with sum = k — O(n), O(n)
int longestSubarraySum(vector<int>& arr, int k) {
    unordered_map<int, int> firstSeen;
    firstSeen[0] = -1;  // prefix sum 0 seen before index 0
    int sum = 0, maxLen = 0;

    for (int i = 0; i < arr.size(); i++) {
        sum += arr[i];
        if (firstSeen.count(sum - k))
            maxLen = max(maxLen, i - firstSeen[sum - k]);
        if (!firstSeen.count(sum))
            firstSeen[sum] = i;  // only store first occurrence
    }
    return maxLen;
}

// Check if subarray with sum = 0 exists — O(n)
bool zeroSumSubarray(vector<int>& arr) {
    unordered_map<int, bool> seen;
    seen[0] = true;
    int sum = 0;
    for (int x : arr) {
        sum += x;
        if (seen[sum]) return true;
        seen[sum] = true;
    }
    return false;
}

int main() {
    vector<int> arr = {1, 2, 3, -3, 1, 1, 1, 4, 2, -3};
    cout << subarraySum(arr, 3) << endl;        // count of subarrays summing to 3
    cout << longestSubarraySum(arr, 3) << endl; // length of longest such subarray

    vector<int> arr2 = {4, 2, -3, 1, 6};
    cout << zeroSumSubarray(arr2) << endl;  // 1 (true — subarray [2,-3,1])
    return 0;
}
```

---
