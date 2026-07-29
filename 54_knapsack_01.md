# 0/1 Knapsack Problem (Dynamic Programming)

## 1. Introduction

The **0/1 Knapsack Problem** is one of the most fundamental combinatorial optimization problems in computer science and operation research. Given a set of $n$ items, each with a weight $w_i$ and a value $v_i$, along with a knapsack of maximum weight capacity $W$, the objective is to determine the subset of items to include in the knapsack such that the total value is maximized without exceeding the total weight capacity $W$.

The term **"0/1"** signifies the binary decision constraint: for each item, you must either take it in its entirety ($1$) or leave it completely ($0$). You cannot take a fractional part of an item or include the same item multiple times.

Mathematically, the problem is formulated as:
$$\text{Maximize } \sum_{i=1}^{n} v_i x_i \quad \text{subject to} \quad \sum_{i=1}^{n} w_i x_i \le W, \quad x_i \in \{0, 1\}$$

Brute-force evaluation explores all $2^n$ subsets, leading to an exponential time complexity of $O(2^n)$. Dynamic Programming solves the 0/1 Knapsack Problem in **pseudo-polynomial time** $O(n \cdot W)$ by breaking it down into overlapping subproblems and building a state transition matrix.

---

## 2. Why Use This Algorithm?

Searching through all possible combinations of $n$ items takes exponential time. For $n = 50$, $2^{50} \approx 1.12 \times 10^{15}$ operations—taking days to compute. Using Dynamic Programming reduces this to $O(n \cdot W)$. For $n = 50$ and capacity $W = 1000$, DP performs only $50,000$ operations, executing in under a millisecond.

**Benefits:**
- **Guaranteed Optimal Solution:** Unlike heuristic or greedy approaches, DP guarantees finding the exact global maximum value.
- **Pseudo-Polynomial Efficiency:** Operates efficiently in $O(n \cdot W)$ time when capacity $W$ is reasonably sized.
- **Space-Optimized Iteration:** Can be reduced from a 2D grid $O(n \cdot W)$ space to a single 1D array of size $O(W)$.
- **Foundation for Resource Allocation:** Forms the theoretical baseline for complex resource optimization algorithms across operations research and cloud scheduling.

---

## 3. Real-World Applications

- **Cargo Loading & Container Packing:** Selecting cargo containers to maximize shipping revenue given strict vehicle weight limits.
- **Financial Portfolio Optimization:** Selecting an optimal combination of investment projects to maximize profit within a fixed capital budget constraint.
- **Cloud Computing & Virtual Machine Allocation:** Allocating CPU/RAM intensive tasks to server clusters with limited capacity to maximize resource utilization or throughput.
- **Feature Selection in Machine Learning:** Choosing a subset of features that maximize model predictive power within a maximum memory or latency budget.
- **Bandwidth Allocation in Networks:** Allocating streaming data packages over limited channel bandwidths to maximize Quality of Service (QoS).

---

## 4. Prerequisites

Before learning the 0/1 Knapsack DP algorithm, you should be familiar with:
- **Recursion & Backtracking:** Understanding state trees and recursive decision choices (include vs. exclude).
- **2D Arrays & Grid Manipulation:** Matrix indexing and nested iteration.
- **Dynamic Programming Fundamentals:** Overlapping subproblems and optimal substructure principles.
- **Big-O & Pseudo-Polynomial Complexity:** Understanding why $O(n \cdot W)$ depends on numeric magnitude $W$.

---

## 5. Visualization

### Decision Tree (Include vs. Exclude for Item $i$)

```
                  Item i (Weight: w_i, Value: v_i), Remaining Capacity: W
                                     /             \
                                    /               \
                       Exclude Item i             Include Item i (if w_i <= W)
                           /                             \
                          /                               \
               Subproblem(i-1, W)                 Subproblem(i-1, W - w_i) + v_i
```

### 2D DP Table Layout ($dp[i][w]$)

```
Items \ Capacity (w)   0    1    2    3   ...   W
   [0] (No Items)      0    0    0    0   ...   0
   [1] (Item 1)        0    x    x    x   ...   x
   [2] (Item 2)        0    x    x    x   ...   x
   ...                ...  ...  ...  ...  ...  ...
   [n] (Item n)        0    x    x    x   ...  MAX_VALUE
```

### Mermaid Flowchart: 0/1 Knapsack State Decision

```mermaid
graph TD
    Start([Start: Process Item i with Capacity w]) --> CheckBase{Is i == 0 OR w == 0?}
    CheckBase -- Yes --> ReturnZero[Return 0]
    CheckBase -- No --> WeightCheck{Is weight[i-1] > w?}
    WeightCheck -- Yes --> ExcludeOnly[dp[i][w] = dp[i-1][w]]
    WeightCheck -- No --> CompareChoices["dp[i][w] = max( dp[i-1][w], dp[i-1][w - weight[i-1]] + value[i-1] )"]
    ExcludeOnly --> StoreResult[Store in DP Table]
    CompareChoices --> StoreResult
    StoreResult --> End([Return dp[n][W]])
    ReturnZero --> End
```

---

## 6. How It Works

The 0/1 Knapsack algorithm relies on two recursive choices for each item $i$ (from $1$ to $n$) and capacity $w$ (from $0$ to $W$):

1. **Option 1 (Exclude Item $i$):** The knapsack capacity remains $w$. The value is simply the optimal value achieved using the first $i-1$ items with capacity $w$:
   $$\text{Value}_{\text{exclude}} = dp[i-1][w]$$

2. **Option 2 (Include Item $i$):** Valid only if $w_i \le w$. The item consumes $w_i$ capacity, leaving $w - w_i$ capacity for the remaining $i-1$ items, while adding $v_i$ value:
   $$\text{Value}_{\text{include}} = dp[i-1][w - w_i] + v_i$$

### State Transition Formula:
$$dp[i][w] = \begin{cases} 
dp[i-1][w] & \text{if } w_i > w \\
\max(dp[i-1][w], \, dp[i-1][w - w_i] + v_i) & \text{if } w_i \le w 
\end{cases}$$

### 1D Space Optimization:
Notice that computing row $i$ only requires values from row $i-1$. By iterating capacity $j$ **backward** from $W$ down to $w_i$, we can update a single 1D array `dp[j]` in-place without overwriting values needed for the current step.

---

## 7. Step-by-Step Algorithm

### 1D Space-Optimized Approach:
1. Create a 1D DP array `dp` of size $W + 1$, initialized with all $0$s.
2. Outer Loop: Iterate item index $i$ from $0$ to $n-1$:
   a. Extract current item's weight $w_i = \text{weights}[i]$ and value $v_i = \text{values}[i]$.
   b. Inner Loop: Iterate capacity $j$ **backward** from $W$ down to $w_i$:
      - Update `dp[j] = max(dp[j], dp[j - w_i] + v_i)`.
3. After completing both loops, `dp[W]` contains the maximum total value. Return `dp[W]`.

---

## 8. Pseudocode

### Approach 1: 2D Bottom-Up Tabulation
```text
function knapsack2D(weights, values, n, W):
    create 2D array dp[n + 1][W + 1] initialized to 0

    for i from 1 to n:
        for w from 0 to W:
            if weights[i - 1] <= w:
                dp[i][w] = max(dp[i - 1][w], dp[i - 1][w - weights[i - 1]] + values[i - 1])
            else:
                dp[i][w] = dp[i - 1][w]

    return dp[n][W]
```

### Approach 2: 1D Space-Optimized Tabulation
```text
function knapsack1D(weights, values, n, W):
    create 1D array dp[W + 1] initialized to 0

    for i from 0 to n - 1:
        for j from W down to weights[i]:
            dp[j] = max(dp[j], dp[j - weights[i]] + values[i])

    return dp[W]
```

---

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>

int max(int a, int b) {
    return (a > b) ? a : b;
}

// 1D Space-Optimized 0/1 Knapsack
int knapsack_1d(int weights[], int values[], int n, int W) {
    int* dp = (int*)calloc(W + 1, sizeof(int));

    for (int i = 0; i < n; i++) {
        for (int j = W; j >= weights[i]; j--) {
            dp[j] = max(dp[j], dp[j - weights[i]] + values[i]);
        }
    }

    int result = dp[W];
    free(dp);
    return result;
}

// 2D Tabulation with Selected Items Reconstruction
void knapsack_with_reconstruction(int weights[], int values[], int n, int W) {
    int** dp = (int**)malloc((n + 1) * sizeof(int*));
    for (int i = 0; i <= n; i++) {
        dp[i] = (int*)calloc(W + 1, sizeof(int));
    }

    for (int i = 1; i <= n; i++) {
        for (int w = 0; w <= W; w++) {
            if (weights[i - 1] <= w) {
                dp[i][w] = max(dp[i - 1][w], dp[i - 1][w - weights[i - 1]] + values[i - 1]);
            } else {
                dp[i][w] = dp[i - 1][w];
            }
        }
    }

    printf("Max Value: %d\n", dp[n][W]);
    printf("Selected Items (0-indexed): ");

    int res = dp[n][W];
    int w = W;
    for (int i = n; i > 0 && res > 0; i--) {
        if (res != dp[i - 1][w]) {
            printf("%d ", i - 1);
            res -= values[i - 1];
            w -= weights[i - 1];
        }
    }
    printf("\n");

    for (int i = 0; i <= n; i++) free(dp[i]);
    free(dp);
}

int main() {
    int weights[] = {2, 3, 4, 5};
    int values[] = {3, 4, 5, 6};
    int n = 4;
    int W = 5;

    printf("1D Knapsack Max Value: %d\n", knapsack_1d(weights, values, n, W));
    knapsack_with_reconstruction(weights, values, n, W);

    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

class Knapsack {
public:
    // 1D Space-Optimized 0/1 Knapsack O(nW) Time, O(W) Space
    static int knapsack1D(const vector<int>& weights, const vector<int>& values, int W) {
        int n = weights.size();
        vector<int> dp(W + 1, 0);

        for (int i = 0; i < n; ++i) {
            for (int j = W; j >= weights[i]; --j) {
                dp[j] = max(dp[j], dp[j - weights[i]] + values[i]);
            }
        }
        return dp[W];
    }

    // Top-Down Memoization O(nW) Time, O(nW) Space
    static int knapsackMemoized(const vector<int>& weights, const vector<int>& values, int W) {
        int n = weights.size();
        vector<vector<int>> memo(n + 1, vector<int>(W + 1, -1));
        return memoHelper(weights, values, n, W, memo);
    }

private:
    static int memoHelper(const vector<int>& weights, const vector<int>& values, 
                           int i, int w, vector<vector<int>>& memo) {
        if (i == 0 || w == 0) return 0;
        if (memo[i][w] != -1) return memo[i][w];

        if (weights[i - 1] > w) {
            return memo[i][w] = memoHelper(weights, values, i - 1, w, memo);
        } else {
            int exclude = memoHelper(weights, values, i - 1, w, memo);
            int include = values[i - 1] + memoHelper(weights, values, i - 1, w - weights[i - 1], memo);
            return memo[i][w] = max(exclude, include);
        }
    }
};

int main() {
    vector<int> weights = {2, 3, 4, 5};
    vector<int> values = {3, 4, 5, 6};
    int W = 5;

    cout << "1D Knapsack Max Value: " << Knapsack::knapsack1D(weights, values, W) << endl;
    cout << "Memoized Max Value: " << Knapsack::knapsackMemoized(weights, values, W) << endl;

    return 0;
}
```

### Java
```java
import java.util.Arrays;

public class Knapsack01 {

    // 1D Space-Optimized 0/1 Knapsack
    public static int knapsack1D(int[] weights, int[] values, int W) {
        int n = weights.length;
        int[] dp = new int[W + 1];

        for (int i = 0; i < n; i++) {
            for (int j = W; j >= weights[i]; j--) {
                dp[j] = Math.max(dp[j], dp[j - weights[i]] + values[i]);
            }
        }
        return dp[W];
    }

    // 2D Tabulation with Solution Reconstruction
    public static int knapsack2D(int[] weights, int[] values, int W) {
        int n = weights.length;
        int[][] dp = new int[n + 1][W + 1];

        for (int i = 1; i <= n; i++) {
            for (int w = 0; w <= W; w++) {
                if (weights[i - 1] <= w) {
                    dp[i][w] = Math.max(dp[i - 1][w], dp[i - 1][w - weights[i - 1]] + values[i - 1]);
                } else {
                    dp[i][w] = dp[i - 1][w];
                }
            }
        }
        return dp[n][W];
    }

    public static void main(String[] args) {
        int[] weights = {2, 3, 4, 5};
        int[] values = {3, 4, 5, 6};
        int W = 5;

        System.out.println("1D Knapsack Max Value: " + knapsack1D(weights, values, W));
        System.out.println("2D Knapsack Max Value: " + knapsack2D(weights, values, W));
    }
}
```

### Python
```python
def knapsack_1d(weights: list[int], values: list[int], W: int) -> int:
    """1D Space-Optimized 0/1 Knapsack implementation."""
    n = len(weights)
    dp = [0] * (W + 1)

    for i in range(n):
        for j in range(W, weights[i] - 1, -1):
            dp[j] = max(dp[j], dp[j - weights[i]] + values[i])

    return dp[W]

def knapsack_memoization(weights: list[int], values: list[int], W: int) -> int:
    """Top-down recursive memoization for 0/1 Knapsack."""
    n = len(weights)
    memo = {}

    def helper(i: int, cap: int) -> int:
        if i < 0 or cap <= 0:
            return 0
        if (i, cap) in memo:
            return memo[(i, cap)]

        if weights[i] > cap:
            result = helper(i - 1, cap)
        else:
            exclude = helper(i - 1, cap)
            include = values[i] + helper(i - 1, cap - weights[i])
            result = max(exclude, include)

        memo[(i, cap)] = result
        return result

    return helper(n - 1, W)

if __name__ == "__main__":
    weights = [2, 3, 4, 5]
    values = [3, 4, 5, 6]
    W = 5

    print("1D Knapsack Max Value:", knapsack_1d(weights, values, W))
    print("Memoized Max Value:", knapsack_memoization(weights, values, W))
```

### JavaScript
```javascript
/**
 * 1D Space-Optimized 0/1 Knapsack
 * @param {number[]} weights 
 * @param {number[]} values 
 * @param {number} W 
 * @returns {number}
 */
function knapsack1D(weights, values, W) {
    const n = weights.length;
    const dp = new Array(W + 1).fill(0);

    for (let i = 0; i < n; i++) {
        for (let j = W; j >= weights[i]; j--) {
            dp[j] = Math.max(dp[j], dp[j - weights[i]] + values[i]);
        }
    }
    return dp[W];
}

/**
 * 2D Tabulation 0/1 Knapsack
 * @param {number[]} weights 
 * @param {number[]} values 
 * @param {number} W 
 * @returns {number}
 */
function knapsack2D(weights, values, W) {
    const n = weights.length;
    const dp = Array.from({ length: n + 1 }, () => new Array(W + 1).fill(0));

    for (let i = 1; i <= n; i++) {
        for (let w = 0; w <= W; w++) {
            if (weights[i - 1] <= w) {
                dp[i][w] = Math.max(dp[i - 1][w], dp[i - 1][w - weights[i - 1]] + values[i - 1]);
            } else {
                dp[i][w] = dp[i - 1][w];
            }
        }
    }
    return dp[n][W];
}

// Execution and testing
const weights = [2, 3, 4, 5];
const values = [3, 4, 5, 6];
const W = 5;

console.log("1D Knapsack Max Value:", knapsack1D(weights, values, W));
console.log("2D Knapsack Max Value:", knapsack2D(weights, values, W));
```

---

## 10. Code Explanation

1. **1D DP Array Initialization:** `dp` array of size `W + 1` initialized to zero. `dp[j]` represents the maximum value achievable for capacity `j`.
2. **Reverse Capacity Loop (`for j from W down to weights[i]`):** 
   - **Crucial Rule:** In 0/1 Knapsack, iterating backward ensures `dp[j - weights[i]]` references the value from the **previous item's step** (i.e. $i-1$).
   - If we iterated forward, `dp[j - weights[i]]` would contain an updated value from the **current item's step**, allowing the same item to be picked multiple times (converting it into Unbounded Knapsack).
3. **State Transition `max(dp[j], dp[j - w] + v)`:** Evaluates taking the item vs leaving it.
4. **Reconstruction Backtracking:** By tracing backwards from `dp[n][W]`, if `dp[i][w] != dp[i-1][w]`, it confirms item $i-1$ was selected. We record item $i-1$ and subtract its weight and value before continuing up row $i-1$.

---

## 11. Interactive Demo

An interactive 0/1 Knapsack Simulator includes:
- **Item Configurator:** Controls to add items with custom weights and values.
- **Knapsack Capacity Slider:** Adjust capacity $W$ dynamically.
- **Grid Animator:** Interactive 2D matrix view showing real-time updates of table cells with color indicators (Gray = Unprocessed, Yellow = Evaluating `Include vs Exclude`, Green = Final Optimal Cell).
- **Selected Backpack Content View:** Displays items placed inside the knapsack with a visual weight indicator bar.

---

## 12. Dry Run

### Sample Input:
- Items: $n = 4$
- Weights $w = [2, 3, 4, 5]$
- Values $v = [3, 4, 5, 6]$
- Capacity $W = 5$

### Step-by-Step 1D DP Array Evolution:

| Step / Item | Item $(w_i, v_i)$ | $j=0$ | $j=1$ | $j=2$ | $j=3$ | $j=4$ | $j=5$ | Action Taken |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---|
| **Init** | - | 0 | 0 | 0 | 0 | 0 | 0 | Array initialized to 0 |
| **Item 0** | $(2, 3)$ | 0 | 0 | **3** | **3** | **3** | **3** | `dp[2..5]` updated with $v_0=3$ |
| **Item 1** | $(3, 4)$ | 0 | 0 | 3 | **4** | **4** | **7** | `dp[5] = max(3, dp[2]+4) = 7`, `dp[3..4] = 4` |
| **Item 2** | $(4, 5)$ | 0 | 0 | 3 | 4 | **5** | 7 | `dp[4] = max(4, dp[0]+5) = 5`, `dp[5] = max(7, dp[1]+5) = 7` |
| **Item 3** | $(5, 6)$ | 0 | 0 | 3 | 4 | 5 | **7** | `dp[5] = max(7, dp[0]+6) = 7` |

**Final Result:** `dp[5]` = **7** (achieved by selecting Item 0 with weight 2, value 3 AND Item 1 with weight 3, value 4; Total weight = 5, Total value = 7).

---

## 13. Time & Space Complexity Analysis

| Approach | Best-Case Time | Average-Case Time | Worst-Case Time | Space Complexity | Notes |
|:---|:---:|:---:|:---:|:---:|:---|
| **Naive Recursion** | $O(2^n)$ | $O(2^n)$ | $O(2^n)$ | $O(n)$ | Call stack depth $n$; explores $2^n$ subsets |
| **Top-Down Memoization** | $O(n \cdot W)$ | $O(n \cdot W)$ | $O(n \cdot W)$ | $O(n \cdot W)$ | Uses map/hash grid + recursion stack |
| **2D Tabulation** | $O(n \cdot W)$ | $O(n \cdot W)$ | $O(n \cdot W)$ | $O(n \cdot W)$ | Explicit matrix; allows item reconstruction |
| **1D Space-Optimized** | $O(n \cdot W)$ | $O(n \cdot W)$ | $O(n \cdot W)$ | **$O(W)$** | **Optimal space bound for value calculation** |

> **Pseudo-Polynomial Complexity:** The time complexity $O(n \cdot W)$ is proportional to the numeric magnitude of capacity $W$, not the number of bits used to represent $W$. Therefore, 0/1 Knapsack is an **NP-complete** problem in general.

---

## 14. Advantages

- **Optimal Results:** Always finds the exact maximum value subset.
- **Pseudo-Polynomial Performance:** Highly efficient when $W$ is small to moderate.
- **Minimal Memory Requirement:** 1D array space optimization reduces memory footprint to $O(W)$.
- **Flexible Model:** Readily adapts to subset sum, equal partition, and constraint problems.

---

## 15. Disadvantages

- **NP-Complete in General:** If $W$ is exponentially large (e.g. $W = 2^{64}$), pseudo-polynomial DP becomes intractable.
- **Requires Integer Weights:** Standard DP grid indexing relies on discrete integer capacities $W$ and item weights $w_i$.
- **1D Array Cannot Reconstruct Items Directly:** Reconstructing exact item subsets requires storing the 2D grid $O(n \cdot W)$ or maintaining back-pointers.

---

## 16. Variations & Advanced Optimizations

1. **Unbounded Knapsack:** Each item can be picked unlimited times. Solved by iterating capacity $j$ **forward** from $w_i$ to $W$.
2. **Fractional Knapsack:** Items can be split into arbitrary fractions. Solved using a **Greedy Algorithm** in $O(n \log n)$ time by sorting by value-to-weight ratio $\frac{v_i}{w_i}$.
3. **Bounded Knapsack:** Item $i$ is available in $c_i$ copies. Optimized by binary decomposition into $O(n \log c \cdot W)$.
4. **Meet-in-the-Middle (for large $W$, small $n \le 40$):** Splits items into two halves, generates subset sums, and uses binary search in $O(2^{n/2} \cdot n)$ time.

---

## 17. Common Mistakes & Pitfalls

- **Iterating Capacity Forward in 1D DP:** Iterating $j$ from $w_i \to W$ causes the same item to be reused, silently turning 0/1 Knapsack into Unbounded Knapsack.
- **1-Based vs 0-Based Indexing Mismatch:** Array indexing off-by-one errors when matching item $i-1$ in input arrays to row $i$ in DP table.
- **Allocating Insufficient DP Array Size:** Allocating array of size $W$ instead of $W+1$, resulting in out-of-bounds crash when accessing `dp[W]`.
- **Large Capacity Memory Exhaustion:** Attempting to create a 2D array when $W = 10^9$ causes stack overflow / Out-Of-Memory exception.

---

## 18. Interview Questions

1. **Why does 1D space optimization require iterating the capacity backward in 0/1 Knapsack?**
   *Answer:* Iterating backward ensures that `dp[j - weight]` reads values computed in the previous item iteration ($i-1$), preventing an item from being reused multiple times.

2. **Is 0/1 Knapsack a Polynomial-Time algorithm?**
   *Answer:* No, it is pseudo-polynomial $O(n \cdot W)$ because $W$ depends on numeric input value, making it NP-complete.

3. **How can you reconstruct which items were selected in 0/1 Knapsack?**
   *Answer:* Using a 2D DP table, start at `dp[n][W]`. Compare `dp[i][w]` with `dp[i-1][w]`. If different, item $i-1$ was selected; subtract its weight/value and move to `dp[i-1][w - weight]`.

4. **What is the difference between 0/1 Knapsack and Fractional Knapsack?**
   *Answer:* In 0/1 Knapsack items cannot be split (solved via DP in $O(nW)$ time). In Fractional Knapsack items can be divided (solved via Greedy ratio sorting in $O(n \log n)$ time).

5. **How does 0/1 Knapsack relate to the Subset Sum problem?**
   *Answer:* Subset Sum is a special case of 0/1 Knapsack where item value equals item weight ($v_i = w_i$) and target capacity is target sum $S$.

6. **What approach should be used if $n \le 40$ but capacity $W = 10^{12}$?**
   *Answer:* Use **Meet-in-the-Middle**. Split items into two sets of 20, compute all subset sums for both, sort one half, and use binary search in $O(2^{n/2} \cdot n)$ time.

7. **What is the base case of 0/1 Knapsack?**
   *Answer:* `dp[0][w] = 0` (no items available) and `dp[i][0] = 0` (zero capacity remaining).

8. **How do you handle negative values or weights in Knapsack?**
   *Answer:* Standard DP table indexing fails with negative weights. Offset indexing or mapping states via Hash Maps is required.

9. **How would you solve Bounded Knapsack efficiently?**
   *Answer:* Use binary representation to decompose quantity $c_i$ into powers of 2 ($1, 2, 4, \dots$), converting it into a standard 0/1 Knapsack problem with $O(n \log c)$ items.

10. **Can 0/1 Knapsack be solved in $O(W)$ space while still reconstructing items?**
    *Answer:* Yes, using **Hirschberg's Algorithm** (Divide and Conquer with DP) in $O(n \cdot W)$ time and $O(W)$ space.

---

## 19. Practice Problems

### Easy
1. **GeeksforGeeks:** [0/1 Knapsack Problem](https://practice.geeksforgeeks.org/) - Standard 0/1 Knapsack formulation.
2. **LeetCode 416:** [Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/) - Transforming array partition into 0/1 Knapsack subset sum with target $W = \text{sum}/2$.

### Medium
3. **LeetCode 494:** [Target Sum](https://leetcode.com/problems/target-sum/) - Converting sign selection into 0/1 Knapsack subset problem.
4. **LeetCode 1049:** [Last Stone Weight II](https://leetcode.com/problems/last-stone-weight-ii/) - Minimizing absolute difference via subset sum knapsack.
5. **LeetCode 474:** [Ones and Zeroes](https://leetcode.com/problems/ones-and-zeroes/) - 2D Capacity Knapsack problem (constraints on both 0s and 1s).

### Hard
6. **LeetCode 879:** [Profitable Schemes](https://leetcode.com/problems/profitable-schemes/) - Multi-constraint 3D DP knapsack.
7. **Codeforces:** [Knapsack with Large Capacity](https://codeforces.com/) - Dual DP transformation ($dp[i][v] = \text{min weight}$ for fixed values when $W$ is large).

---

## 20. Related Algorithms

- **Unbounded Knapsack:** Iterates forward in 1D DP to allow unlimited item reuse.
- **Fractional Knapsack:** Greedy algorithm using value-to-weight ratio sorting.
- **Subset Sum Problem:** Special case of 0/1 Knapsack where $v_i = w_i$.
- **Coin Change Problem:** Unbounded knapsack variant targeting exact change formation.

---

## 21. Summary

The 0/1 Knapsack Problem is the benchmark optimization problem for Dynamic Programming. It enforces a strict binary decision for each item (take whole or leave completely). Using **1D space-optimized tabulation with a backward capacity loop**, we achieve optimal $O(n \cdot W)$ time and $O(W)$ space complexity.

---

## 22. Quiz

**Question 1:** What does "0/1" mean in the 0/1 Knapsack Problem?
- A) Values and weights must be 0 or 1
- B) You must either include an item completely (1) or exclude it completely (0)
- C) The algorithm runs in 0 or 1 seconds
- D) The capacity must be binary
- **Correct Answer:** B
- **Explanation:** Each item has a binary decision variable $x_i \in \{0, 1\}$.

**Question 2:** Why must the capacity loop run backward in 1D space-optimized 0/1 Knapsack?
- A) To sort items in descending order
- B) To ensure items are evaluated from highest value to lowest
- C) To prevent using the current item multiple times in the same step
- D) To optimize CPU cache memory
- **Correct Answer:** C
- **Explanation:** Running backward ensures `dp[j - w]` reads previous step state ($i-1$), keeping choices strictly binary.

**Question 3:** What is the time complexity of the 0/1 Knapsack DP algorithm?
- A) $O(n \log n)$
- B) $O(2^n)$
- C) $O(n \cdot W)$
- D) $O(n^2)$
- **Correct Answer:** C
- **Explanation:** The algorithm uses nested loops over $n$ items and $W$ capacities, taking $O(n \cdot W)$ time.

**Question 4:** What type of time complexity is $O(n \cdot W)$?
- A) Polynomial
- B) Logarithmic
- C) Pseudo-Polynomial
- D) Exponential
- **Correct Answer:** C
- **Explanation:** It is pseudo-polynomial because $W$ is a numeric value, not the input size in bits.

**Question 5:** Which algorithm solves Fractional Knapsack in $O(n \log n)$ time?
- A) Dynamic Programming
- B) Greedy Algorithm (sorting by value/weight ratio)
- C) Dijkstra's Algorithm
- D) Backtracking
- **Correct Answer:** B
- **Explanation:** Fractional items can be split, allowing a greedy ratio choice to guarantee optimal results.

**Question 6:** What is the space complexity of 1D optimized 0/1 Knapsack?
- A) $O(n \cdot W)$
- B) $O(n)$
- C) $O(W)$
- D) $O(1)$
- **Correct Answer:** C
- **Explanation:** It uses a single 1D array of size $W + 1$.

**Question 7:** If item $i$ has weight $w_i = 6$ and current capacity is $w = 4$, what is $dp[i][w]$?
- A) $v_i$
- B) $dp[i-1][w]$
- C) $dp[i-1][w - 6] + v_i$
- D) 0
- **Correct Answer:** B
- **Explanation:** Since $w_i > w$, the item cannot fit, so we must exclude it and take $dp[i-1][w]$.

**Question 8:** Which LeetCode problem is a direct variation of 0/1 Knapsack?
- A) Longest Common Subsequence
- B) Partition Equal Subset Sum (LeetCode 416)
- C) Merge K Sorted Lists
- D) Binary Tree Inorder Traversal
- **Correct Answer:** B
- **Explanation:** Partitioning an array into equal sum subsets maps directly to 0/1 Knapsack with capacity $W = \text{Sum}/2$.

**Question 9:** What happens if you iterate capacity forward ($w_i \to W$) in 1D Knapsack DP?
- A) The code crashes with an out-of-bounds error
- B) It calculates Unbounded Knapsack (allowing multiple item picks)
- C) It calculates Fractional Knapsack
- D) Time complexity becomes exponential
- **Correct Answer:** B
- **Explanation:** Forward iteration uses updated values from the current item step, allowing an item to be selected repeatedly.

**Question 10:** What approach is optimal if $n=30$ and $W = 10^{15}$?
- A) 1D Tabulation
- B) 2D Tabulation
- C) Meet-in-the-Middle ($O(2^{n/2} \cdot n)$)
- D) Fractional Ratio Sort
- **Correct Answer:** C
- **Explanation:** Since $W$ is massive, DP fails. However, $n=30$ is small enough for Meet-in-the-Middle in $2^{15} \times 30 \approx 10^6$ operations.
