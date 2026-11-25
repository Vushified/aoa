# C++ Algorithm Implementations

## 1. Recurrence Relations — Recursion Tree Method

```cpp
#include <iostream>
#include <cmath>
#include <string>
using namespace std;

enum FType { ZERO, CONSTANT, LOG_N, N, N_LOG_N, N2, N3 };

double f(FType type, double n) {
    if (n <= 0) return 0;
    switch (type) {
        case ZERO:    return 0;
        case CONSTANT:return 1;
        case LOG_N:   return log2(n);
        case N:       return n;
        case N_LOG_N: return n * log2(n);
        case N2:      return n * n;
        case N3:      return n * n * n;
    }
    return 0;
}

string fName(FType type) {
    switch (type) {
        case ZERO:    return "0";
        case CONSTANT:return "1 (constant)";
        case LOG_N:   return "log(n)";
        case N:       return "n";
        case N_LOG_N: return "n*log(n)";
        case N2:      return "n^2";
        case N3:      return "n^3";
    }
    return "";
}

void printTree(int a, int b, FType type, double n, int level, int maxLevel) {
    if (n < 1 || level > maxLevel) return;
    for (int i = 0; i < level; i++) cout << "  ";
    cout << "Level " << level << ": T(" << (int)n << ")";
    cout << "  [" << a << " subproblems, f(n) = " << f(type, n) << "]" << endl;
    if (n <= 1) return;
    for (int i = 0; i < a; i++)
        printTree(a, b, type, n / b, level + 1, maxLevel);
}

double totalCost(int a, int b, FType type, double n) {
    if (n < 1) return 0;
    return f(type, n) + a * totalCost(a, b, type, n / b);
}

int main() {
    int a, b, n;
    cout << "Recursion Tree Method: T(n) = aT(n/b) + f(n)" << endl;
    cout << "Enter a (number of subproblems): ";
    cin >> a;
    cout << "Enter b (divisor of n):          ";
    cin >> b;
    cout << "Enter n (input size):             ";
    cin >> n;

    cout << "\nSelect f(n):" << endl;
    cout << "  0 -> 0" << endl;
    cout << "  1 -> 1 (constant)" << endl;
    cout << "  2 -> log(n)" << endl;
    cout << "  3 -> n" << endl;
    cout << "  4 -> n*log(n)" << endl;
    cout << "  5 -> n^2" << endl;
    cout << "  6 -> n^3" << endl;
    cout << "Choice: ";
    int choice;
    cin >> choice;
    FType type = static_cast<FType>(choice);

    int maxLevel = (int)(log((double)n) / log((double)b)) + 1;

    cout << "\n--- Recursion Tree for T(n) = " << a << "T(n/" << b
         << ") + " << fName(type) << " ---\n" << endl;

    printTree(a, b, type, n, 0, maxLevel);

    double cost = totalCost(a, b, type, n);
    double logba = log((double)a) / log((double)b);

    cout << "\n--- Analysis ---" << endl;
    cout << "Tree depth:       log_" << b << "(" << n << ") = " << maxLevel << endl;
    cout << "Nodes at depth d: " << a << "^d" << endl;
    cout << "log_" << b << "(" << a << ") = " << logba << endl;
    cout << "Total cost:       " << cost << endl;

    cout << "\n--- Complexity (Master Method cross-check) ---" << endl;
    if (type == ZERO || type == CONSTANT) {
        cout << "f(n) = O(1), which is O(n^0)" << endl;
        if (logba > 0)
            cout << "T(n) = Theta(n^" << logba << ")" << endl;
        else
            cout << "T(n) = Theta(log n)" << endl;
    } else if (type == LOG_N) {
        cout << "f(n) = log(n) = O(n^eps) for small eps" << endl;
        if (logba > 0)
            cout << "T(n) = Theta(n^" << logba << ")" << endl;
        else
            cout << "T(n) = Theta(log^2 n)" << endl;
    } else if (type == N) {
        if (logba < 1)
            cout << "Case 3: T(n) = Theta(n)" << endl;
        else if (logba == 1)
            cout << "Case 2: T(n) = Theta(n log n)" << endl;
        else
            cout << "Case 1: T(n) = Theta(n^" << logba << ")" << endl;
    } else if (type == N_LOG_N) {
        if (logba < 1)
            cout << "Case 3: T(n) = Theta(n log n)" << endl;
        else if (logba == 1)
            cout << "Case 2: T(n) = Theta(n log^2 n)" << endl;
        else
            cout << "Case 1: T(n) = Theta(n^" << logba << ")" << endl;
    } else if (type == N2) {
        if (logba < 2)
            cout << "Case 3: T(n) = Theta(n^2)" << endl;
        else if (logba == 2)
            cout << "Case 2: T(n) = Theta(n^2 log n)" << endl;
        else
            cout << "Case 1: T(n) = Theta(n^" << logba << ")" << endl;
    } else if (type == N3) {
        if (logba < 3)
            cout << "Case 3: T(n) = Theta(n^3)" << endl;
        else if (logba == 3)
            cout << "Case 2: T(n) = Theta(n^3 log n)" << endl;
        else
            cout << "Case 1: T(n) = Theta(n^" << logba << ")" << endl;
    }

    return 0;
}
```

---

## 2. Recurrence Relations — Master Method

```cpp
#include <iostream>
#include <cmath>
using namespace std;

void masterMethod(int a, int b, double k) {
    double logba = log((double)a) / log((double)b);
    cout << "a = " << a << ", b = " << b << ", k = " << k << endl;
    cout << "log_b(a) = " << logba << endl;
    if (logba > k)
        cout << "Case 1: T(n) = O(n^" << logba << ")" << endl;
    else if (logba == k)
        cout << "Case 2: T(n) = O(n^" << k << " * log n)" << endl;
    else
        cout << "Case 3: T(n) = O(n^" << k << ")" << endl;
}

int main() {
    int a, b;
    double k;
    cout << "Enter a (number of subproblems): ";
    cin >> a;
    cout << "Enter b (factor by which n shrinks): ";
    cin >> b;
    cout << "Enter k (exponent in f(n) = n^k): ";
    cin >> k;
    masterMethod(a, b, k);
    return 0;
}
```

---

## 3. B-Tree Implementation

```cpp
#include <iostream>
using namespace std;

const int T = 3;

struct BTreeNode {
    int keys[2 * T - 1];
    BTreeNode* children[2 * T];
    int n;
    bool leaf;
    BTreeNode(bool leaf) : n(0), leaf(leaf) {
        for (int i = 0; i < 2 * T; i++) children[i] = nullptr;
    }
};

void traverse(BTreeNode* root) {
    if (!root) return;
    int i;
    for (i = 0; i < root->n; i++) {
        if (!root->leaf) traverse(root->children[i]);
        cout << root->keys[i] << " ";
    }
    if (!root->leaf) traverse(root->children[i]);
}

BTreeNode* search(BTreeNode* root, int k) {
    int i = 0;
    while (i < root->n && k > root->keys[i]) i++;
    if (i < root->n && root->keys[i] == k) return root;
    if (root->leaf) return nullptr;
    return search(root->children[i], k);
}

void splitChild(BTreeNode* x, int i) {
    BTreeNode* y = x->children[i];
    BTreeNode* z = new BTreeNode(y->leaf);
    z->n = T - 1;
    for (int j = 0; j < T - 1; j++) z->keys[j] = y->keys[j + T];
    if (!y->leaf)
        for (int j = 0; j < T; j++) z->children[j] = y->children[j + T];
    y->n = T - 1;
    for (int j = x->n; j >= i + 1; j--) x->children[j + 1] = x->children[j];
    x->children[i + 1] = z;
    for (int j = x->n - 1; j >= i; j--) x->keys[j + 1] = x->keys[j];
    x->keys[i] = y->keys[T - 1];
    x->n++;
}

void insertNonFull(BTreeNode* x, int k) {
    int i = x->n - 1;
    if (x->leaf) {
        while (i >= 0 && x->keys[i] > k) {
            x->keys[i + 1] = x->keys[i];
            i--;
        }
        x->keys[i + 1] = k;
        x->n++;
    } else {
        while (i >= 0 && x->keys[i] > k) i--;
        if (x->children[i + 1]->n == 2 * T - 1) {
            splitChild(x, i + 1);
            if (x->keys[i + 1] < k) i++;
        }
        insertNonFull(x->children[i + 1], k);
    }
}

BTreeNode* insert(BTreeNode* root, int k) {
    if (!root) {
        root = new BTreeNode(true);
        root->keys[0] = k;
        root->n = 1;
        return root;
    }
    if (root->n == 2 * T - 1) {
        BTreeNode* s = new BTreeNode(false);
        s->children[0] = root;
        splitChild(s, 0);
        insertNonFull(s, k);
        return s;
    }
    insertNonFull(root, k);
    return root;
}

int main() {
    BTreeNode* root = nullptr;
    int keys[] = {10, 20, 5, 6, 12, 30, 7, 17};
    for (int k : keys) root = insert(root, k);
    cout << "B-Tree traversal: ";
    traverse(root);
    cout << endl;
    int key = 6;
    BTreeNode* res = search(root, key);
    cout << "Search " << key << ": " << (res ? "Found" : "Not found") << endl;
    return 0;
}
```

---

## 4. B+ Tree Implementation

```cpp
#include <iostream>
#include <vector>
using namespace std;

const int ORDER = 4;

struct BPlusNode {
    vector<int> keys;
    vector<BPlusNode*> children;
    BPlusNode* next;
    bool leaf;
    BPlusNode(bool leaf) : leaf(leaf), next(nullptr) {}
};

BPlusNode* root = nullptr;

void insertLeaf(BPlusNode* node, int key) {
    auto it = lower_bound(node->keys.begin(), node->keys.end(), key);
    node->keys.insert(it, key);
}

void printLeaves(BPlusNode* root) {
    BPlusNode* curr = root;
    while (curr && !curr->leaf) curr = curr->children[0];
    while (curr) {
        for (int k : curr->keys) cout << k << " ";
        curr = curr->next;
    }
    cout << endl;
}

pair<int, BPlusNode*> insertHelper(BPlusNode* node, int key);

pair<int, BPlusNode*> splitLeaf(BPlusNode* leaf) {
    BPlusNode* newLeaf = new BPlusNode(true);
    int mid = leaf->keys.size() / 2;
    newLeaf->keys.assign(leaf->keys.begin() + mid, leaf->keys.end());
    leaf->keys.resize(mid);
    newLeaf->next = leaf->next;
    leaf->next = newLeaf;
    return {newLeaf->keys[0], newLeaf};
}

pair<int, BPlusNode*> splitInternal(BPlusNode* node) {
    BPlusNode* newNode = new BPlusNode(false);
    int mid = node->keys.size() / 2;
    int pushUp = node->keys[mid];
    newNode->keys.assign(node->keys.begin() + mid + 1, node->keys.end());
    newNode->children.assign(node->children.begin() + mid + 1, node->children.end());
    node->keys.resize(mid);
    node->children.resize(mid + 1);
    return {pushUp, newNode};
}

pair<int, BPlusNode*> insertHelper(BPlusNode* node, int key) {
    if (node->leaf) {
        insertLeaf(node, key);
        if ((int)node->keys.size() >= ORDER)
            return splitLeaf(node);
        return {-1, nullptr};
    }
    int i = 0;
    while (i < (int)node->keys.size() && key >= node->keys[i]) i++;
    auto [pushKey, newChild] = insertHelper(node->children[i], key);
    if (newChild) {
        node->keys.insert(node->keys.begin() + i, pushKey);
        node->children.insert(node->children.begin() + i + 1, newChild);
        if ((int)node->keys.size() >= ORDER)
            return splitInternal(node);
    }
    return {-1, nullptr};
}

void insert(int key) {
    if (!root) {
        root = new BPlusNode(true);
        root->keys.push_back(key);
        return;
    }
    auto [pushKey, newChild] = insertHelper(root, key);
    if (newChild) {
        BPlusNode* newRoot = new BPlusNode(false);
        newRoot->keys.push_back(pushKey);
        newRoot->children.push_back(root);
        newRoot->children.push_back(newChild);
        root = newRoot;
    }
}

bool search(BPlusNode* node, int key) {
    if (node->leaf) {
        return find(node->keys.begin(), node->keys.end(), key) != node->keys.end();
    }
    int i = 0;
    while (i < (int)node->keys.size() && key >= node->keys[i]) i++;
    return search(node->children[i], key);
}

int main() {
    int keys[] = {5, 15, 25, 35, 45, 10, 20, 30, 40, 50};
    for (int k : keys) insert(k);
    cout << "B+ Tree leaf traversal: ";
    printLeaves(root);
    int key = 25;
    cout << "Search " << key << ": " << (search(root, key) ? "Found" : "Not found") << endl;
    return 0;
}
```

---

## 5. Binary Search Algorithm

```cpp
#include <iostream>
using namespace std;

int binarySearch(int arr[], int n, int target) {
    int low = 0, high = n - 1;
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (arr[mid] == target) return mid;
        else if (arr[mid] < target) low = mid + 1;
        else high = mid - 1;
    }
    return -1;
}

int main() {
    int arr[] = {2, 5, 8, 12, 16, 23, 38, 45, 56, 72};
    int n = sizeof(arr) / sizeof(arr[0]);
    int target;
    cout << "Array: ";
    for (int i = 0; i < n; i++) cout << arr[i] << " ";
    cout << "\nEnter target to search: ";
    cin >> target;
    int result = binarySearch(arr, n, target);
    if (result != -1)
        cout << "Element found at index " << result << endl;
    else
        cout << "Element not found" << endl;
    return 0;
}
```

---

## 6. Merge Sort

```cpp
#include <iostream>
using namespace std;

void merge(int arr[], int left, int mid, int right) {
    int n1 = mid - left + 1, n2 = right - mid;
    int L[n1], R[n2];
    for (int i = 0; i < n1; i++) L[i] = arr[left + i];
    for (int j = 0; j < n2; j++) R[j] = arr[mid + 1 + j];
    int i = 0, j = 0, k = left;
    while (i < n1 && j < n2) {
        if (L[i] <= R[j]) arr[k++] = L[i++];
        else arr[k++] = R[j++];
    }
    while (i < n1) arr[k++] = L[i++];
    while (j < n2) arr[k++] = R[j++];
}

void mergeSort(int arr[], int left, int right) {
    if (left < right) {
        int mid = left + (right - left) / 2;
        mergeSort(arr, left, mid);
        mergeSort(arr, mid + 1, right);
        merge(arr, left, mid, right);
    }
}

int main() {
    int arr[] = {38, 27, 43, 3, 9, 82, 10};
    int n = sizeof(arr) / sizeof(arr[0]);
    cout << "Before sorting: ";
    for (int x : arr) cout << x << " ";
    mergeSort(arr, 0, n - 1);
    cout << "\nAfter sorting: ";
    for (int x : arr) cout << x << " ";
    cout << endl;
    return 0;
}
```

---

## 7. Quick Sort

```cpp
#include <iostream>
using namespace std;

int partition(int arr[], int low, int high) {
    int pivot = arr[high];
    int i = low - 1;
    for (int j = low; j < high; j++) {
        if (arr[j] <= pivot) {
            i++;
            swap(arr[i], arr[j]);
        }
    }
    swap(arr[i + 1], arr[high]);
    return i + 1;
}

void quickSort(int arr[], int low, int high) {
    if (low < high) {
        int pi = partition(arr, low, high);
        quickSort(arr, low, pi - 1);
        quickSort(arr, pi + 1, high);
    }
}

int main() {
    int arr[] = {64, 34, 25, 12, 22, 11, 90};
    int n = sizeof(arr) / sizeof(arr[0]);
    cout << "Before sorting: ";
    for (int x : arr) cout << x << " ";
    quickSort(arr, 0, n - 1);
    cout << "\nAfter sorting: ";
    for (int x : arr) cout << x << " ";
    cout << endl;
    return 0;
}
```

---

## 8. Fractional Knapsack — Greedy Algorithm

```cpp
#include <iostream>
#include <algorithm>
using namespace std;

struct Item {
    double weight, value, ratio;
};

bool cmp(Item a, Item b) {
    return a.ratio > b.ratio;
}

double fractionalKnapsack(Item items[], int n, double capacity) {
    sort(items, items + n, cmp);
    double totalValue = 0.0;
    for (int i = 0; i < n; i++) {
        if (capacity <= 0) break;
        if (items[i].weight <= capacity) {
            totalValue += items[i].value;
            capacity -= items[i].weight;
        } else {
            totalValue += items[i].value * (capacity / items[i].weight);
            capacity = 0;
        }
    }
    return totalValue;
}

int main() {
    int n;
    double capacity;
    cout << "Enter number of items: ";
    cin >> n;
    cout << "Enter knapsack capacity: ";
    cin >> capacity;
    Item items[n];
    for (int i = 0; i < n; i++) {
        cout << "Enter weight and value of item " << i + 1 << ": ";
        cin >> items[i].weight >> items[i].value;
        items[i].ratio = items[i].value / items[i].weight;
    }
    cout << "Maximum value: " << fractionalKnapsack(items, n, capacity) << endl;
    return 0;
}
```

---

## 9. Job Sequencing with Deadlines — Greedy Algorithm

```cpp
#include <iostream>
#include <algorithm>
using namespace std;

struct Job {
    int id, deadline, profit;
};

bool cmp(Job a, Job b) {
    return a.profit > b.profit;
}

void jobSequencing(Job jobs[], int n) {
    sort(jobs, jobs + n, cmp);
    int maxDeadline = 0;
    for (int i = 0; i < n; i++)
        maxDeadline = max(maxDeadline, jobs[i].deadline);
    int slot[maxDeadline + 1];
    fill(slot, slot + maxDeadline + 1, -1);
    int totalProfit = 0, jobCount = 0;
    cout << "Selected jobs: ";
    for (int i = 0; i < n; i++) {
        for (int j = jobs[i].deadline; j > 0; j--) {
            if (slot[j] == -1) {
                slot[j] = jobs[i].id;
                totalProfit += jobs[i].profit;
                jobCount++;
                cout << "J" << jobs[i].id << " ";
                break;
            }
        }
    }
    cout << "\nTotal jobs: " << jobCount << ", Total profit: " << totalProfit << endl;
}

int main() {
    Job jobs[] = {{1,2,100},{2,1,19},{3,2,27},{4,1,25},{5,3,15}};
    int n = sizeof(jobs) / sizeof(jobs[0]);
    jobSequencing(jobs, n);
    return 0;
}
```

---

## 10. 0/1 Knapsack — Dynamic Programming

```cpp
#include <iostream>
using namespace std;

int knapsack(int W, int wt[], int val[], int n) {
    int dp[n + 1][W + 1];
    for (int i = 0; i <= n; i++) {
        for (int w = 0; w <= W; w++) {
            if (i == 0 || w == 0)
                dp[i][w] = 0;
            else if (wt[i - 1] <= w)
                dp[i][w] = max(val[i - 1] + dp[i - 1][w - wt[i - 1]], dp[i - 1][w]);
            else
                dp[i][w] = dp[i - 1][w];
        }
    }
    return dp[n][W];
}

int main() {
    int val[] = {60, 100, 120};
    int wt[]  = {10, 20, 30};
    int W = 50;
    int n = sizeof(val) / sizeof(val[0]);
    cout << "Maximum value in Knapsack: " << knapsack(W, wt, val, n) << endl;
    return 0;
}
```

---

## 11. Longest Common Subsequence — Dynamic Programming

```cpp
#include <iostream>
#include <string>
using namespace std;

int lcs(string X, string Y) {
    int m = X.size(), n = Y.size();
    int dp[m + 1][n + 1];
    for (int i = 0; i <= m; i++) {
        for (int j = 0; j <= n; j++) {
            if (i == 0 || j == 0)
                dp[i][j] = 0;
            else if (X[i - 1] == Y[j - 1])
                dp[i][j] = 1 + dp[i - 1][j - 1];
            else
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
        }
    }
    int len = dp[m][n];
    string result(len, ' ');
    int i = m, j = n;
    while (i > 0 && j > 0) {
        if (X[i - 1] == Y[j - 1]) {
            result[--len] = X[i - 1];
            i--; j--;
        } else if (dp[i - 1][j] > dp[i][j - 1]) {
            i--;
        } else {
            j--;
        }
    }
    cout << "LCS: " << result << endl;
    return dp[m][n];
}

int main() {
    string X = "AGGTAB", Y = "GXTXAYB";
    cout << "String 1: " << X << "\nString 2: " << Y << endl;
    cout << "LCS Length: " << lcs(X, Y) << endl;
    return 0;
}
```
