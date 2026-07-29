# Rod Cutting Problem (Dynamic Programming)

## 1. Introduction

The **Rod Cutting Problem** is a classic optimization problem introduced in *Introduction to Algorithms* (CLRS). Given a rod of length $n$ inches and an array of prices $price[]$ where $price[i]$ denotes the selling price of a piece of length $i+1$ (for $1 \le i \le n$), the objective is to determine the maximum revenue obtainable by cutting up the rod and selling the pieces.

For example, a rod of length $n = 4$ can be sold whole or cut into smaller pieces ($1+1+1+1$, $2+2$, $3+1$, etc.). Depending on the price table, selling smaller pieces can yield a higher combined revenue than selling the single uncut rod.

Because each of the $n-1$ possible cut locations can either be cut or left uncut, there are $2^{n-1}$ possible ways to cut a rod of length $n$. Naive recursive exploration requires exponential time $O(2^n)$. Dynamic Programming solves the Rod Cutting Problem efficiently in **$O(n^2)$ time** and **$O(n)$ space**.

The Rod Cutting Problem is a direct instance of the **Unbounded Knapsack Problem**, where the total rod length $n$ acts as the knapsack capacity $W$, piece lengths $1 \dots n$ act as item weights, and prices act as item values.

---

## 2. Why Use This Algorithm?

A naive recursive approach explores an exponential number of choices ($2^{n-1}$). For a rod of length $n = 30$, brute force checks $2^{29} \approx 536,870,912$ cut combinations.

Using Dynamic Programming reduces this to $O(n^2)$ operations. For $n = 30$, DP performs fewer than $900$ operations, executing in under a microsecond.

**Benefits:**
- **Guaranteed Maximum Revenue:** Computes the exact global maximum profit across all cut combinations.
- **Low Memory Footprint:** Requires only a 1D DP table of size $n+1$ ($O(n)$ space).
- **Exact Cut Reconstruction:** Easily tracks the exact sequence of piece lengths that yield maximum revenue.
- **Adaptability:** Readily accounts for fixed cutting costs or material wastage per cut.

---

## 3. Real-World Applications

- **Manufacturing & Industrial Cutting Stock:** Cutting raw materials (steel pipes, timber logs, aluminum extrusions, sheet metal, paper rolls) into ordered lengths to maximize market value.
- **Memory Block Management in OS:** Allocating contiguous memory blocks of variable sizes to incoming system processes to maximize memory utility.
- **Media Content Editing:** Slicing raw video/audio footage into monetizable clip lengths to maximize ad revenue per minute.
- **Cable & Textile Manufacturing:** Cutting fiber optic cables or fabric rolls into customized customer order lengths.

---

## 4. Prerequisites

Before learning the Rod Cutting algorithm, you should be familiar with:
- **1D Array Tabulation:** Storing state lookups sequentially.
- **Unbounded Knapsack Concept:** Allowing multiple pieces of the same length to be used.
- **Basic Recursion & Subproblem Overlapping:** Recognizing how optimal sub-solutions combine.

---

## 5. Visualization

### Cut Choices for Rod of Length $n = 4$ ($2^{4-1} = 8$ Combinations)

```
[--------------------]  Length 4 (Uncut)  -> Price: price[4]
[----------][--------]  Length 2 + 2      -> Price: price[2] + price[2]
[----][--------------]  Length 1 + 3      -> Price: price[1] + price[3]
[--------------][----]  Length 3 + 1      -> Price: price[3] + price[1]
[----][----][--------]  Length 1 + 1 + 2  -> Price: price[1] + price[1] + price[2]
[----][--------][----]  Length 1 + 2 + 1  -> Price: price[1] + price[2] + price[1]
[--------][----][----]  Length 2 + 1 + 1  -> Price: price[2] + price[1] + price[1]
[----][----][----][--]  Length 1 + 1 + 1 + 1 -> Price: 4 * price[1]
```

### Mermaid Flowchart: Rod Cutting DP State Decision

```mermaid
graph TD
    Start([Start: Rod of Length n, price array]) --> InitDP[Initialize dp array of size n + 1 with 0]
    InitDP --> BaseSet[dp[0] = 0]
    BaseSet --> OuterLoop[Loop j from 1 to n]
    OuterLoop --> InnerLoop[Loop i from 1 to j]
    InnerLoop --> StateUpdate["dp[j] = max(dp[j], price[i - 1] + dp[j - i])"]
    StateUpdate --> NextInner{i < j?}
    NextInner -- Yes --> InnerLoop
    NextInner -- No --> NextOuter{j < n?}
    NextOuter -- Yes --> OuterLoop
    NextOuter -- No --> ReturnResult[Return dp[n]]
    ReturnResult --> End([End])
```

---

## 6. How It Works

To find the maximum revenue $r_n$ for a rod of length $n$, we consider making a first cut of length $i$ ($1 \le i \le n$), leaving a remaining rod of length $n - i$. The total revenue from this choice is:
$$\text{Revenue} = \text{price}[i] + r_{n-i}$$

Since we want to maximize revenue, the state transition recurrence relation is:
$$r_n = \max_{1 \le i \le n} \left( \text{price}[i] + r_{n-i} \right)$$

### Base Case:
- $r_0 = 0$ (A rod of length 0 yields 0 revenue).

### Approaches:

1. **Top-Down (Memoization):** Recurse down from $n$. For each call $n$, loop $i$ from $1$ to $n$, compute $\text{price}[i] + \text{cut}(n-i)$, and memoize $r_n$.
2. **Bottom-Up (Tabulation):** Build an array `dp` of size $n+1$. For each rod length $j$ from $1$ to $n$, evaluate all initial cut lengths $i$ from $1$ to $j$ and store `dp[j] = max(dp[j], price[i-1] + dp[j-i])`.

---

## 7. Step-by-Step Algorithm

### 1D Bottom-Up Tabulation with Piece Reconstruction:
1. Create a 1D array `dp` of size $n + 1$, initialized with $0$.
2. Create a 1D array `parent` of size $n + 1$, initialized with $0$ (to track optimal piece cuts).
3. Set base case: `dp[0] = 0`.
4. Outer Loop $j$ from $1$ to $n$ (current rod length being solved):
   - Inner Loop $i$ from $1$ to $j$ (first cut length):
     - If $\text{price}[i - 1] + dp[j - i] > dp[j]$:
       - Update `dp[j] = price[i - 1] + dp[j - i]`.
       - Update `parent[j] = i`.
5. `dp[n]` contains the maximum revenue.
6. **Reconstruct Cuts:** While $n > 0$, print piece length `parent[n]`, and update $n = n - parent[n]$.

---

## 8. Pseudocode

```text
function rodCutting(price, n):
    create array dp of size (n + 1) filled with 0
    create array parent of size (n + 1) filled with 0

    dp[0] = 0

    for j from 1 to n:
        maxVal = -INFINITY
        for i from 1 to j:
            if price[i - 1] + dp[j - i] > maxVal:
                maxVal = price[i - 1] + dp[j - i]
                parent[j] = i
        dp[j] = maxVal

    print "Max Revenue:", dp[n]
    
    print "Optimal Piece Cuts:"
    temp = n
    while temp > 0:
        print parent[temp]
        temp = temp - parent[temp]

    return dp[n]
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

// Rod Cutting Tabulation with Reconstruction
int rodCutting(int price[], int n) {
    int* dp = (int*)calloc(n + 1, sizeof(int));
    int* parent = (int*)calloc(n + 1, sizeof(int));

    dp[0] = 0;

    for (int j = 1; j <= n; j++) {
        int max_val = -1;
        for (int i = 1; i <= j; i++) {
            if (price[i - 1] + dp[j - i] > max_val) {
                max_val = price[i - 1] + dp[j - i];
                parent[j] = i;
            }
        }
        dp[j] = max_val;
    }

    int maxRevenue = dp[n];

    printf("Max Revenue: %d\n", maxRevenue);
    printf("Optimal Piece Lengths: ");
    int temp = n;
    while (temp > 0) {
        printf("%d ", parent[temp]);
        temp -= parent[temp];
    }
    printf("\n");

    free(dp);
    free(parent);
    return maxRevenue;
}

int main() {
    int price[] = {1, 5, 8, 9, 10, 17, 17, 20};
    int n = 8;

    rodCutting(price, n);
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <climits>

using namespace std;

class RodCutting {
public:
    // Tabulation O(n^2) Time, O(n) Space
    static int get組み合わせMaxRevenue(const vector<int>& price, int n) {
        vector<int> dp(n + 1, 0);
        vector<int> parent(n + 1, 0);

        for (int j = 1; j <= n; ++j) {
            int maxVal = INT_MIN;
            for (int i = 1; i <= j; ++i) {
                if (price[i - 1] + dp[j - i] > maxVal) {
                    maxVal = price[i - 1] + dp[j - i];
                    parent[j] = i;
                }
            }
            dp[j] = maxVal;
        }

        cout << "Max Revenue: " << dp[n] << endl;
        cout << "Optimal Piece Lengths: ";
        int temp = n;
        while (temp > 0) {
            cout << parent[temp] << " ";
            temp -= parent[temp];
        }
        cout << endl;

        return dp[n];
    }
};

int main() {
    vector<int> price = {1, 5, 8, 9, 10, 17, 17, 20};
    int n = 8;

    RodCutting::get組み合わせMaxRevenue(price, n);
    return 0;
}
```

### Java
```java
import java.util.Arrays;

public class RodCutting {

    public static int maxRevenue(int[] price, int n) {
        int[] dp = new int[n + 1];
        int[] parent = new int[n + 1];

        dp[0] = 0;

        for (int j = 1; j <= n; j++) {
            int maxVal = Integer.MIN_VALUE;
            for (int i = 1; i <= j; i++) {
                if (price[i - 1] + dp[j - i] > maxVal) {
                    maxVal = price[i - 1] + dp[j - i];
                    parent[j] = i;
                }
            }
            dp[j] = maxVal;
        }

        System.out.println("Max Revenue: " + dp[n]);
        System.out.print("Optimal Piece Lengths: ");
        int temp = n;
        while (temp > 0) {
            System.out.print(parent[temp] + " ");
            temp -= parent[temp];
        }
        System.out.println();

        return dp[n];
    }

    public static void main(String[] args) {
        int[] price = {1, 5, 8, 9, 10, 17, 17, 20};
        int n = 8;

        maxRevenue(price, n);
    }
}
```

### Python
```python
def rod_cutting(price: list[int], n: int) -> int:
    """Computes maximum revenue for cutting a rod of length n."""
    dp = [0] * (n + 1)
    parent = [0] * (n + 1)

    for j in range(1, n + 1):
        max_val = -1
        for i in range(1, j + 1):
            if price[i - 1] + dp[j - i] > max_val:
                max_val = price[i - 1] + dp[j - i]
                parent[j] = i
        dp[j] = max_val

    print(f"Max Revenue: {dp[n]}")
    
    # Reconstruct optimal cuts
    cuts = []
    temp = n
    while temp > 0:
        cuts.append(parent[temp])
        temp -= parent[temp]
        
    print(f"Optimal Piece Lengths: {cuts}")
    return dp[n]

if __name__ == "__main__":
    price = [1, 5, 8, 9, 10, 17, 17, 20]
    n = 8

    rod_cutting(price, n)
```

### JavaScript
```javascript
/**
 * Rod Cutting DP Algorithm with Piece Reconstruction
 * @param {number[]} price 
 * @param {number} n 
 * @returns {number}
 */
function rodCutting(price, n) {
    const dp = new Array(n + 1).fill(0);
    const parent = new Array(n + 1).fill(0);

    for (let j = 1; j <= n; j++) {
        let maxVal = -1;
        for (let i = 1; i <= j; i++) {
            if (price[i - 1] + dp[j - i] > maxVal) {
                maxVal = price[i - 1] + dp[j - i];
                parent[j] = i;
            }
        }
        dp[j] = maxVal;
    }

    console.log(`Max Revenue: ${dp[n]}`);

    const cuts = [];
    let temp = n;
    while (temp > 0) {
        cuts.push(parent[temp]);
        temp -= parent[temp];
    }
    console.log(`Optimal Piece Lengths: ${cuts.join(", ")}`);

    return dp[n];
}

// Execution and testing
const price = [1, 5, 8, 9, 10, 17, 17, 20];
const n = 8;

rodCutting(price, n);
```

---

## 10. Code Explanation

1. **State Definition (`dp[j]`):** Stores the maximum revenue attainable for a rod of length $j$.
2. **Outer Loop (`for j from 1 to n`):** Iteratively solves subproblem lengths from $1$ up to total length $n$.
3. **Inner Loop (`for i from 1 to j`):** Considers taking a first piece of length $i$ (costing `price[i-1]`) and combining it with the optimal revenue of the remaining length `dp[j - i]`.
4. **Parent Array Tracking:** `parent[j] = i` records the optimal length $i$ chosen for subproblem $j$, enabling full solution reconstruction.

---

## 11. Interactive Demo

An interactive Rod Cutting Visualizer provides:
- **Interactive Rod Length Canvas:** A visual horizontal bar representing a rod of length $n$.
- **Price Matrix Editor:** Editable inputs for price table values $price[1 \dots n]$.
- **Cut Lines Generator:** Automatically overlays dotted red cut lines on the visual rod bar to show optimal cut positions.
- **DP Grid Stepper:** Step-by-step matrix filling view highlighting `dp[j - i]` lookups.

---

## 12. Dry Run

### Sample Input:
- Rod Length: $n = 4$
- Prices: $price = [1, 5, 8, 9]$ (representing lengths 1, 2, 3, 4)

### Execution Trace:

| $j$ (Sub-length) | $i=1$ (`price[0]+dp[j-1]`) | $i=2$ (`price[1]+dp[j-2]`) | $i=3$ (`price[2]+dp[j-3]`) | $i=4$ (`price[3]+dp[j-4]`) | `dp[j]` Max | `parent[j]` |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **0** | - | - | - | - | **0** | - |
| **1** | $1 + 0 = 1$ | - | - | - | **1** | 1 |
| **2** | $1 + 1 = 2$ | $5 + 0 = 5$ | - | - | **5** | 2 |
| **3** | $1 + 5 = 6$ | $5 + 1 = 6$ | $8 + 0 = 8$ | - | **8** | 3 |
| **4** | $1 + 8 = 9$ | $5 + 5 = 10$ | $8 + 1 = 9$ | $9 + 0 = 9$ | **10** | 2 |

### Reconstruction:
- `temp = 4` $\rightarrow$ `parent[4] = 2`. Piece length = **2**. `temp` becomes $4 - 2 = 2$.
- `temp = 2` $\rightarrow$ `parent[2] = 2`. Piece length = **2**. `temp` becomes $2 - 2 = 0$.
- **Result:** Max Revenue = **10** (achieved by cutting into two pieces of length **2**).

---

## 13. Time & Space Complexity Analysis

| Metric | Complexity | Explanation |
|:---|:---:|:---|
| **Naive Recursion** | $O(2^n)$ | Explores $2^{n-1}$ cut combinations |
| **Top-Down DP** | $O(n^2)$ | Solves $n$ subproblems, each taking $O(n)$ loop steps |
| **Bottom-Up DP** | **$O(n^2)$** | Nested loops: $1 + 2 + \dots + n = \frac{n(n+1)}{2}$ steps |
| **Space Complexity** | **$O(n)$** | 1D DP table + parent tracking array of size $n+1$ |

---

## 14. Advantages

- **Optimal Global Revenue:** Guaranteed to find maximum profit across all $2^{n-1}$ cut choices.
- **Linear Auxiliary Memory:** Requires only $O(n)$ space for DP lookup table.
- **Easy Piece Reconstruction:** Reconstructs optimal piece lengths efficiently via a parent array.

---

## 15. Disadvantages

- **Quadratic Time Bounds:** $O(n^2)$ execution time can become slow for large $n$ ($n > 10^5$).
- **No Wastage Penalty by Default:** Standard model assumes cuts have zero width/cost and zero material waste.

---

## 16. Variations & Advanced Optimizations

1. **Rod Cutting with Fixed Cut Cost $c$:**
   Each physical cut incurs a cost $c$. Recurrence adjusts to:
   $$dp[j] = \max \Big( price[j-1], \; \max_{1 \le i < j}(price[i-1] + dp[j-i] - c) \Big)$$
2. **Unbounded Knapsack Form:**
   If only a subset $k$ of specific piece lengths is allowed, inner loop checks only those $k$ lengths, reducing time complexity to $O(n \cdot k)$.

---

## 17. Common Mistakes & Pitfalls

- **0-Based Array Off-By-One:** Accessing `price[i]` instead of `price[i - 1]` when length is $i$.
- **Forgetting `dp[0] = 0` Base Case:** Uninitialized base cases cause garbage output.
- **Assuming Uncut Rod is Always Best:** Failing to check combinations of smaller pieces.

---

## 18. Interview Questions

1. **How does Rod Cutting relate to Unbounded Knapsack?**
   *Answer:* Rod length $n$ is knapsack capacity $W$, piece lengths $1 \dots n$ are item weights, and prices are item values with unlimited piece availability.

2. **What is the time complexity of the naive recursive Rod Cutting solution?**
   *Answer:* $O(2^n)$, because there are $n-1$ cut positions, each with 2 choices (cut or no cut).

3. **How do you modify Rod Cutting if each cut costs a fixed fee $c$?**
   *Answer:* Subtract cost $c$ whenever a rod of length $j$ is split into $i$ and $j-i$ (for $i < j$).

4. **Why is the space complexity $O(n)$ instead of $O(n^2)$?**
   *Answer:* Because $dp[j]$ depends only on previously computed 1D values $dp[j-i]$, eliminating the need for a 2D matrix.

5. **How does Rod Cutting differ from Matrix Chain Multiplication?**
   *Answer:* Rod Cutting is a 1D DP problem ($O(n^2)$ time), whereas Matrix Chain Multiplication is a 2D interval DP problem ($O(n^3)$ time).

6. **What does `parent[j]` store in the reconstruction algorithm?**
   *Answer:* The optimal length $i$ of the first cut chosen for a rod of length $j$.

7. **Can Rod Cutting be solved in $O(n \log n)$ time?**
   *Answer:* No, for arbitrary price tables, general Rod Cutting requires $O(n^2)$ time.

8. **What happens if prices are strictly proportional to length ($price[i] = i \cdot k$)?**
   *Answer:* Any combination of cuts yields the exact same total price $n \cdot k$.

9. **If $price[i]$ grows exponentially with length, what cut choice will DP pick?**
   *Answer:* DP will choose to leave the rod completely uncut (single piece of length $n$).

10. **What is the base case of Rod Cutting?**
    *Answer:* `dp[0] = 0` (a rod of length 0 yields 0 revenue).

---

## 19. Practice Problems

### Easy
1. **GeeksforGeeks:** [Rod Cutting](https://practice.geeksforgeeks.org/) - Standard Rod Cutting DP formulation.
2. **LeetCode 279:** [Perfect Squares](https://leetcode.com/problems/perfect-squares/) - Unbounded DP similar to rod cutting with square lengths.

### Medium
3. **LeetCode 343:** [Integer Break](https://leetcode.com/problems/integer-break/) - Maximizing product of integer cuts (multiplicative rod cutting).
4. **LeetCode 1547:** [Minimum Cost to Cut a Stick](https://leetcode.com/problems/minimum-cost-to-cut-a-stick/) - Interval DP variant with fixed cut points.

### Hard
5. **LeetCode 312:** [Burst Balloons](https://leetcode.com/problems/burst-balloons/) - Advanced interval DP related to rod splitting choices.

---

## 20. Related Algorithms

- **Unbounded Knapsack:** General DP framework for infinite item selection.
- **Coin Change:** Minimizing/counting items for target sum.
- **Integer Break:** Multiplicative optimization over integer splits.
- **Cutting Stock Problem:** Industrial linear programming generalization.

---

## 21. Summary

The Rod Cutting Problem demonstrates classic 1D Dynamic Programming. By decomposing a rod of length $n$ into a first cut of length $i$ and an optimal remaining sub-rod $n-i$, DP optimizes the time complexity from exponential $O(2^n)$ down to **$O(n^2)$ time** and **$O(n)$ space**.

---

## 22. Quiz

**Question 1:** How many total ways are there to cut a rod of length $n$?
- A) $n!$
- B) $n^2$
- C) $2^{n-1}$
- D) $2^n$
- **Correct Answer:** C
- **Explanation:** There are $n-1$ potential cut points, each having 2 choices (cut or don't cut), yielding $2^{n-1}$ ways.

**Question 2:** What is the time complexity of Bottom-Up Rod Cutting DP?
- A) $O(n)$
- B) $O(n \log n)$
- C) $O(n^2)$
- D) $O(2^n)$
- **Correct Answer:** C
- **Explanation:** Nested loops execute $1 + 2 + \dots + n = \frac{n(n+1)}{2}$ steps $\rightarrow O(n^2)$.

**Question 3:** What is the space complexity of Rod Cutting DP?
- A) $O(1)$
- B) $O(n)$
- C) $O(n^2)$
- D) $O(2^n)$
- **Correct Answer:** B
- **Explanation:** Requires a 1D DP table of size $n+1$.

**Question 4:** Which algorithmic family does Rod Cutting directly belong to?
- A) 0/1 Knapsack
- B) Unbounded Knapsack
- C) Fractional Knapsack
- D) Topological Sort
- **Correct Answer:** B
- **Explanation:** Pieces of length $i$ can be reused multiple times without restriction.

**Question 5:** What is the recurrence formula for Rod Cutting?
- A) $dp[j] = \max(dp[j-1], price[j])$
- B) $dp[j] = \max_{1 \le i \le j}(price[i-1] + dp[j-i])$
- C) $dp[j] = dp[j-1] + dp[j-2]$
- D) $dp[j] = \min(dp[j], price[j])$
- **Correct Answer:** B
- **Explanation:** Evaluates taking a first cut of length $i$ and adding optimal revenue of remaining length $j-i$.

**Question 6:** What does `dp[0] = 0` represent?
- A) Rod length 0 cannot be sold
- B) Rod length 0 yields 0 revenue
- C) Error condition
- D) Rod must be cut 0 times
- **Correct Answer:** B
- **Explanation:** Base case: A rod of length 0 generates 0 value.

**Question 7:** How do you track the exact piece lengths used in optimal solution?
- A) Use a 2D matrix
- B) Maintain a `parent[j]` array storing the optimal cut length $i$ chosen for length $j$
- C) Run a second recursive pass
- D) Sort the price array
- **Correct Answer:** B
- **Explanation:** `parent[j]` records the optimal piece length, allowing simple trace-back.

**Question 8:** In LeetCode 343 (Integer Break), how does the state transition change compared to standard Rod Cutting?
- A) Uses addition instead of multiplication
- B) Uses multiplication ($\max(i \cdot (j-i), i \cdot dp[j-i])$) instead of addition
- C) Uses division
- D) Uses XOR
- **Correct Answer:** B
- **Explanation:** Integer Break maximizes the product of integer parts rather than sum of prices.

**Question 9:** What happens if $price[i]$ decreases as length $i$ increases?
- A) Code crashes
- B) DP picks many 1-unit length pieces
- C) DP picks uncut rod
- D) DP returns negative revenue
- **Correct Answer:** B
- **Explanation:** Smaller pieces have higher relative value, so DP cuts the rod into small 1-unit pieces.

**Question 10:** What is the primary difference between Rod Cutting and 0/1 Knapsack?
- A) Rod Cutting uses 2D arrays, 0/1 uses 1D
- B) Rod Cutting allows multiple pieces of the same length (unbounded), 0/1 allows max 1 choice per item
- C) Rod Cutting is NP-Hard, 0/1 is in P
- D) Rod Cutting only works on even numbers
- **Correct Answer:** B
- **Explanation:** Rod Cutting has unbounded availability per piece length, whereas 0/1 Knapsack allows at most one copy per item.
