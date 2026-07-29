# Coin Change Problem (Dynamic Programming)

## 1. Introduction

The **Coin Change Problem** is a classic optimization problem in computer science and dynamic programming. Given an array of coin denominations `coins[]` and a total target amount `amount`, the problem typically presents itself in two primary variants:

1. **Coin Change I (Min Coins):** Find the **minimum number of coins** required to make up the target `amount`. If the amount cannot be formed by any combination of coins, return `-1`.
2. **Coin Change II (Total Ways):** Find the **total number of distinct combinations** to make up the target `amount`.

We assume an infinite supply of each coin denomination, making this a variation of the **Unbounded Knapsack Problem**.

While standard currency systems (like USD or EUR) are designed such that a **Greedy Algorithm** works (always picking the largest coin possible), arbitrary coin systems (such as `coins = [1, 3, 4]` for `amount = 6`) break greedy logic. Dynamic Programming guarantees finding the globally optimal solution in **$O(n \cdot \text{amount})$** pseudo-polynomial time.

---

## 2. Why Use This Algorithm?

### Why Greedy Fails:
Consider `coins = [1, 3, 4]` and target `amount = 6`:
- **Greedy Strategy:** Picks the largest coin `4`, leaving remaining amount `2`. Then picks `1` twice ($4 + 1 + 1 = 6$). Total coins = **3**.
- **Dynamic Programming (Optimal):** Picks `3` twice ($3 + 3 = 6$). Total coins = **2**.

Thus, the greedy approach fails to provide the optimal solution for non-canonical coin systems.

**Benefits of DP:**
- **Guaranteed Global Optimum:** Evaluates all valid sub-amount combinations efficiently without redundant computations.
- **Pseudo-Polynomial Performance:** Operates in $O(n \cdot \text{amount})$ time and $O(\text{amount})$ space using 1D tabulation.
- **Versatility:** Easily adapts to finding minimum coins, total combinations, or exact coin permutation counts.

---

## 3. Real-World Applications

- **Automated Teller Machines (ATMs):** Determining optimal bill dispensing breakdowns to minimize paper note output or balance drawer counts.
- **Vending Machines & POS Systems:** Calculating change to return to customers using minimal coin counts.
- **Cryptocurrency & Micro-Transactions:** Determining minimal gas token splits or transaction fee outputs across liquidity pools.
- **Resource Allocation in Parallel Systems:** Packaging compute tasks of fixed sizes into server execution blocks.
- **Data Packet Decomposition:** Fragmenting network payloads into fixed MTU frame sizes to minimize header overhead.

---

## 4. Prerequisites

Before studying the Coin Change algorithm, you should be familiar with:
- **1D Array Tabulation:** Storing dynamic programming state lookups in arrays.
- **Unbounded Knapsack Concept:** Allowing unlimited reuse of items/coins.
- **Basic Recursion & Memoization:** Understanding state trees and subproblem overlapping.

---

## 5. Visualization

### Min Coins DP State Array ($dp[i]$ for `coins = [1, 2, 5]`, `amount = 11`)

```
Amount (i):   [0]  [1]  [2]  [3]  [4]  [5]  [6]  [7]  [8]  [9]  [10] [11]
dp[i] value:   0    1    1    2    2    1    2    2    3    3    2    3

Transitions for Amount 11:
  - From dp[10] (using coin 1): 1 + dp[10] = 1 + 2 = 3
  - From dp[9]  (using coin 2): 1 + dp[9]  = 1 + 3 = 4
  - From dp[6]  (using coin 5): 1 + dp[6]  = 1 + 2 = 3
Minimum = 3 coins (5 + 5 + 1 = 11)
```

### Mermaid Flowchart: Min Coins DP Execution

```mermaid
graph TD
    Start([Start: coins[], amount]) --> InitDP[Initialize dp array of size amount + 1 with INF]
    InitDP --> SetBase[dp[0] = 0]
    SetBase --> LoopAmount[Loop i from 1 to amount]
    LoopAmount --> LoopCoins[Loop each coin in coins]
    LoopCoins --> CheckValid{Is coin <= i?}
    CheckValid -- Yes --> UpdateDP["dp[i] = min(dp[i], 1 + dp[i - coin])"]
    CheckValid -- No --> NextCoin[Skip coin]
    UpdateDP --> NextCoin
    NextCoin --> MoreCoins{More coins?}
    MoreCoins -- Yes --> LoopCoins
    MoreCoins -- No --> MoreAmounts{i < amount?}
    MoreAmounts -- Yes --> LoopAmount
    MoreAmounts -- No --> CheckResult{Is dp[amount] == INF?}
    CheckResult -- Yes --> ReturnNeg[Return -1]
    CheckResult -- No --> ReturnVal[Return dp[amount]]
    ReturnNeg --> End([End])
    ReturnVal --> End
```

---

## 6. How It Works

### Variant 1: Minimum Coins (Coin Change I)
Let $dp[i]$ represent the minimum number of coins required to make amount $i$.

- **Base Case:** $dp[0] = 0$ (0 coins needed for amount 0). All other cells initialized to infinity ($\infty$ or `amount + 1`).
- **State Transition:** For each amount $i$ from $1$ to $\text{amount}$, and for each coin $c \in \text{coins}$:
  $$\text{If } c \le i: \quad dp[i] = \min(dp[i], \, 1 + dp[i - c])$$
- **Final Result:** If $dp[\text{amount}] > \text{amount}$, return $-1$; otherwise, return $dp[\text{amount}]$.

### Variant 2: Total Ways / Combinations (Coin Change II)
Let $dp[i]$ represent the number of distinct combinations to form amount $i$.

- **Base Case:** $dp[0] = 1$ (1 way to make amount 0: pick no coins).
- **Loop Order (Crucial for Combinations):**
  - Outer loop: `for coin in coins`
  - Inner loop: `for i from coin to amount`
- **State Transition:**
  $$dp[i] = dp[i] + dp[i - c]$$

---

## 7. Step-by-Step Algorithm

### Algorithm for Minimum Coins:
1. Create a 1D array `dp` of size `amount + 1`.
2. Fill `dp` with default value `amount + 1` (a sentinel value representing infinity).
3. Set base case: `dp[0] = 0`.
4. Outer loop `i` from $1$ to `amount`:
   - Inner loop `c` through all elements in `coins`:
     - If $c \le i$, update `dp[i] = min(dp[i], 1 + dp[i - c])`.
5. Return `dp[amount] > amount ? -1 : dp[amount]`.

---

## 8. Pseudocode

### Min Coins Pseudocode
```text
function coinChangeMin(coins, amount):
    create array dp of size (amount + 1) filled with (amount + 1)
    dp[0] = 0

    for i from 1 to amount:
        for coin in coins:
            if coin <= i:
                dp[i] = min(dp[i], 1 + dp[i - coin])

    if dp[amount] > amount:
        return -1
    else:
        return dp[amount]
```

### Total Ways Pseudocode
```text
function coinChangeWays(coins, amount):
    create array dp of size (amount + 1) filled with 0
    dp[0] = 1

    for coin in coins:
        for i from coin to amount:
            dp[i] = dp[i] + dp[i - coin]

    return dp[amount]
```

---

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>
#include <limits.h>

int min(int a, int b) {
    return (a < b) ? a : b;
}

// Variant 1: Minimum Coins
int coinChangeMin(int* coins, int coinsSize, int amount) {
    int max_val = amount + 1;
    int* dp = (int*)malloc((amount + 1) * sizeof(int));
    
    for (int i = 0; i <= amount; i++) {
        dp[i] = max_val;
    }
    dp[0] = 0;

    for (int i = 1; i <= amount; i++) {
        for (int j = 0; j < coinsSize; j++) {
            if (coins[j] <= i) {
                dp[i] = min(dp[i], 1 + dp[i - coins[j]]);
            }
        }
    }

    int result = (dp[amount] > amount) ? -1 : dp[amount];
    free(dp);
    return result;
}

// Variant 2: Total Ways / Combinations
long long coinChangeWays(int* coins, int coinsSize, int amount) {
    long long* dp = (long long*)calloc(amount + 1, sizeof(long long));
    dp[0] = 1;

    for (int j = 0; j < coinsSize; j++) {
        for (int i = coins[j]; i <= amount; i++) {
            dp[i] += dp[i - coins[j]];
        }
    }

    long long result = dp[amount];
    free(dp);
    return result;
}

int main() {
    int coins[] = {1, 2, 5};
    int coinsSize = 3;
    int amount = 11;

    printf("Min Coins for Amount %d: %d\n", amount, coinChangeMin(coins, coinsSize, amount));
    printf("Total Ways for Amount %d: %lld\n", amount, coinChangeWays(coins, coinsSize, amount));

    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

class CoinChange {
public:
    // Variant 1: Min Coins O(n * amount) Time, O(amount) Space
    static int minCoins(const vector<int>& coins, int amount) {
        int maxVal = amount + 1;
        vector<int> dp(amount + 1, maxVal);
        dp[0] = 0;

        for (int i = 1; i <= amount; ++i) {
            for (int coin : coins) {
                if (coin <= i) {
                    dp[i] = min(dp[i], 1 + dp[i - coin]);
                }
            }
        }
        return dp[amount] > amount ? -1 : dp[amount];
    }

    // Variant 2: Total Combinations O(n * amount) Time, O(amount) Space
    static int changeWays(const vector<int>& coins, int amount) {
        vector<unsigned int> dp(amount + 1, 0);
        dp[0] = 1;

        for (int coin : coins) {
            for (int i = coin; i <= amount; ++i) {
                dp[i] += dp[i - coin];
            }
        }
        return dp[amount];
    }
};

int main() {
    vector<int> coins = {1, 2, 5};
    int amount = 11;

    cout << "Min Coins for Amount " << amount << ": " << CoinChange::minCoins(coins, amount) << endl;
    cout << "Total Ways for Amount " << amount << ": " << CoinChange::changeWays(coins, amount) << endl;

    return 0;
}
```

### Java
```java
import java.util.Arrays;

public class CoinChange {

    // Variant 1: Minimum Coins
    public static int minCoins(int[] coins, int amount) {
        int maxVal = amount + 1;
        int[] dp = new int[amount + 1];
        Arrays.fill(dp, maxVal);
        dp[0] = 0;

        for (int i = 1; i <= amount; i++) {
            for (int coin : coins) {
                if (coin <= i) {
                    dp[i] = Math.min(dp[i], 1 + dp[i - coin]);
                }
            }
        }
        return dp[amount] > amount ? -1 : dp[amount];
    }

    // Variant 2: Total Ways (Combinations)
    public static int changeWays(int[] coins, int amount) {
        int[] dp = new int[amount + 1];
        dp[0] = 1;

        for (int coin : coins) {
            for (int i = coin; i <= amount; i++) {
                dp[i] += dp[i - coin];
            }
        }
        return dp[amount];
    }

    public static void main(String[] args) {
        int[] coins = {1, 2, 5};
        int amount = 11;

        System.out.println("Min Coins for Amount " + amount + ": " + minCoins(coins, amount));
        System.out.println("Total Ways for Amount " + amount + ": " + changeWays(coins, amount));
    }
}
```

### Python
```python
def min_coins(coins: list[int], amount: int) -> int:
    """Computes minimum coins needed to make amount."""
    max_val = amount + 1
    dp = [max_val] * (amount + 1)
    dp[0] = 0

    for i in range(1, amount + 1):
        for coin in coins:
            if coin <= i:
                dp[i] = min(dp[i], 1 + dp[i - coin])

    return dp[amount] if dp[amount] <= amount else -1

def change_ways(coins: list[int], amount: int) -> int:
    """Computes total distinct combinations to make amount."""
    dp = [0] * (amount + 1)
    dp[0] = 1

    for coin in coins:
        for i in range(coin, amount + 1):
            dp[i] += dp[i - coin]

    return dp[amount]

if __name__ == "__main__":
    coins = [1, 2, 5]
    amount = 11

    print(f"Min Coins for Amount {amount}: {min_coins(coins, amount)}")
    print(f"Total Ways for Amount {amount}: {change_ways(coins, amount)}")
```

### JavaScript
```javascript
/**
 * Minimum Coins DP
 * @param {number[]} coins 
 * @param {number} amount 
 * @returns {number}
 */
function minCoins(coins, amount) {
    const maxVal = amount + 1;
    const dp = new Array(amount + 1).fill(maxVal);
    dp[0] = 0;

    for (let i = 1; i <= amount; i++) {
        for (const coin of coins) {
            if (coin <= i) {
                dp[i] = Math.min(dp[i], 1 + dp[i - coin]);
            }
        }
    }
    return dp[amount] > amount ? -1 : dp[amount];
}

/**
 * Total Combinations DP
 * @param {number[]} coins 
 * @param {number} amount 
 * @returns {number}
 */
function changeWays(coins, amount) {
    const dp = new Array(amount + 1).fill(0);
    dp[0] = 1;

    for (const coin of coins) {
        for (let i = coin; i <= amount; i++) {
            dp[i] += dp[i - coin];
        }
    }
    return dp[amount];
}

// Execution and testing
const coins = [1, 2, 5];
const amount = 11;

console.log(`Min Coins for Amount ${amount}:`, minCoins(coins, amount));
console.log(`Total Ways for Amount ${amount}:`, changeWays(coins, amount));
```

---

## 10. Code Explanation

1. **Sentinel Initialization (`amount + 1`):** In Min Coins, initializing array elements with `amount + 1` serves as infinity ($\infty$) without risk of integer overflow when calculating `1 + dp[i - coin]` (which occurs if `INT_MAX` is used).
2. **Base Case `dp[0] = 0` vs `dp[0] = 1`:**
   - **Min Coins:** $dp[0] = 0$ (0 coins needed to form amount 0).
   - **Total Ways:** $dp[0] = 1$ (1 valid combination to form amount 0: select no coins).
3. **Loop Ordering Distinction (Combinations vs Permutations):**
   - **Outer loop `coins`, inner loop `amount`:** Computes **Combinations** (un-ordered sets like $\{1, 2\}$ and $\{2, 1\}$ count as 1 way).
   - **Outer loop `amount`, inner loop `coins`:** Computes **Permutations** (ordered sequences like $\{1, 2\}$ and $\{2, 1\}$ count as 2 distinct ways).

---

## 11. Interactive Demo

An interactive Coin Change visualizer features:
- **Custom Denomination Selector:** Chips to add/remove coin denominations (e.g. $[1, 3, 4]$ vs $[1, 2, 5]$).
- **Greedy vs DP Race Simulator:** Compares Greedy output vs DP output side-by-side to visually highlight cases where Greedy produces sub-optimal results.
- **DP Grid Stepper:** Step-by-step animation highlighting `dp[i - coin]` array lookups and updates.

---

## 12. Dry Run

### Sample Input:
- Coins: $[1, 2, 5]$
- Target Amount: $11$

### Step-by-Step Array Trace (`Min Coins`):

| Amount $i$ | `coin = 1` ($1 + dp[i-1]$) | `coin = 2` ($1 + dp[i-2]$) | `coin = 5` ($1 + dp[i-5]$) | `dp[i]` Final Value | Optimal Coin Choices |
|:---:|:---:|:---:|:---:|:---:|:---|
| **0** | - | - | - | **0** | Base Case |
| **1** | $1+0=1$ | - | - | **1** | $[1]$ |
| **2** | $1+1=2$ | $1+0=1$ | - | **1** | $[2]$ |
| **3** | $1+1=2$ | $1+1=2$ | - | **2** | $[1, 2]$ |
| **4** | $1+2=3$ | $1+1=2$ | - | **2** | $[2, 2]$ |
| **5** | $1+2=3$ | $1+2=3$ | $1+0=1$ | **1** | $[5]$ |
| **6** | $1+1=2$ | $1+2=3$ | $1+1=2$ | **2** | $[5, 1]$ |
| **7** | $1+2=3$ | $1+2=3$ | $1+1=2$ | **2** | $[5, 2]$ |
| **8** | $1+2=3$ | $1+2=3$ | $1+2=3$ | **3** | $[5, 2, 1]$ |
| **9** | $1+3=4$ | $1+2=3$ | $1+2=3$ | **3** | $[5, 2, 2]$ |
| **10** | $1+3=4$ | $1+3=4$ | $1+1=2$ | **2** | $[5, 5]$ |
| **11** | $1+2=3$ | $1+3=4$ | $1+2=3$ | **3** | $[5, 5, 1]$ |

**Final Result:** `dp[11]` = **3**

---

## 13. Time & Space Complexity Analysis

| Metric | Complexity | Explanation |
|:---|:---:|:---|
| **Time Complexity** | **$O(n \cdot \text{amount})$** | Outer loop runs `amount` times, inner loop checks $n$ coin denominations |
| **Space Complexity** | **$O(\text{amount})$** | 1D DP array of size `amount + 1` |
| **BFS Graph Variant** | $O(n \cdot \text{amount})$ | Treats amounts as nodes and coin deductions as directed edges |

> **Pseudo-Polynomial Complexity:** Time complexity is proportional to numeric input value `amount`. For massive amounts (e.g. `amount = 10^15`), DP becomes intractable.

---

## 14. Advantages

- **Guaranteed Optimal Result:** Works accurately on arbitrary coin systems where Greedy fails.
- **Linear Space Usage:** Requires only a single 1D array of size `amount + 1`.
- **Flexible Extensions:** Readily solves minimum coins, total combinations, and specific coin combination reconstruction.

---

## 15. Disadvantages

- **Pseudo-Polynomial Time:** Impractical if `amount` is extremely large.
- **Memory Bound for Huge Amounts:** Allocating array of size $10^9$ causes out-of-memory crash.

---

## 16. Variations & Advanced Optimizations

1. **BFS Shortest Path Approach (for Min Coins):**
   Treat amounts as graph nodes ($0 \dots \text{amount}$) and coin transitions as directed edges of weight 1. BFS finds the shortest path to target in $O(n \cdot \text{amount})$ time with early termination upon reaching `amount`.
2. **Combinations vs Permutations:**
   - Outer loop `coins`, inner loop `amount` $\rightarrow$ **Combinations** (LeetCode 518).
   - Outer loop `amount`, inner loop `coins` $\rightarrow$ **Permutations** (LeetCode 377 - Combination Sum IV).

---

## 17. Common Mistakes & Pitfalls

- **Using `INT_MAX` directly in C/C++:** Executing `1 + INT_MAX` leads to integer overflow wrap-around to negative numbers. Use `amount + 1` as sentinel.
- **Confusing Combinations and Permutations:** Swapping inner and outer loops in Coin Change II leads to overcounting permutations instead of unique combinations.
- **Forgetting to Return `-1` for Unformable Amounts:** Returning default sentinel value `amount + 1` or `0` instead of `-1` when no combination forms the target amount.

---

## 18. Interview Questions

1. **When does the Greedy approach work for the Coin Change problem?**
   *Answer:* Greedy works when the coin system is **canonical** (e.g. US currency $1, 5, 10, 25$), where every denomination is a multiple of or optimally compatible with smaller denominations.

2. **How do you distinguish whether a Coin Change DP problem counts combinations vs permutations?**
   *Answer:* If outer loop iterates over `coins` and inner loop over `amount`, it counts **combinations**. If outer loop iterates over `amount` and inner loop over `coins`, it counts **permutations**.

3. **Why do we initialize `dp` array elements to `amount + 1` instead of `INT_MAX`?**
   *Answer:* To avoid integer overflow when evaluating `1 + dp[i - coin]`. Since the max coins needed cannot exceed `amount` (using 1-unit coins), `amount + 1` is a safe infinity sentinel.

4. **How would you reconstruct the actual coins used in Min Coins?**
   *Answer:* Maintain a parent tracking array `parent[i]` storing the coin used to reach state $i$. Trace backwards from `amount` subtracting `parent[curr]` until reaching 0.

5. **What is the time complexity if all coins have denomination 1?**
   *Answer:* $O(\text{amount})$.

6. **Can Coin Change be solved using Breadth-First Search (BFS)?**
   *Answer:* Yes. Since each coin step has an unweighted cost of 1, BFS finds the shortest level path from 0 to `amount`.

7. **What does `dp[0] = 1` signify in Coin Change II?**
   *Answer:* There is exactly 1 way to make amount 0—by choosing no coins at all.

8. **What is the difference between 0/1 Knapsack and Coin Change?**
   *Answer:* 0/1 Knapsack allows each item to be used at most once (bounded). Coin Change allows unlimited uses of each coin (unbounded).

9. **What happens if `amount = 0`?**
   *Answer:* Returns `0` for Min Coins and `1` for Change Ways.

10. **Is Coin Change NP-Hard?**
    *Answer:* The general decision version of Coin Change for arbitrary coin systems is NP-complete.

---

## 19. Practice Problems

### Easy
1. **LeetCode 322:** [Coin Change](https://leetcode.com/problems/coin-change/) - Min coins implementation.
2. **GeeksforGeeks:** [Number of Coins](https://practice.geeksforgeeks.org/) - Standard min coin count.

### Medium
3. **LeetCode 518:** [Coin Change II](https://leetcode.com/problems/coin-change-ii/) - Total combination ways implementation.
4. **LeetCode 377:** [Combination Sum IV](https://leetcode.com/problems/combination-sum-iv/) - Permutation counts variation.

### Hard
5. **LeetCode 983:** [Minimum Cost For Tickets](https://leetcode.com/problems/minimum-cost-for-tickets/) - Time-interval variant of coin change state transitions.

---

## 20. Related Algorithms

- **Unbounded Knapsack:** Broad family of DP problems allowing infinite item reuse.
- **Combination Sum:** Backtracking/DP generation of sum subsets.
- **Breadth-First Search (BFS):** Unweighted shortest path calculation on state graphs.
- **Rod Cutting Problem:** Unbounded DP maximizing value for length divisions.

---

## 21. Summary

The Coin Change Problem showcases the power of 1D Dynamic Programming. By recognizing the **Unbounded Knapsack** structure, it finds minimum coin counts and total combination ways in **$O(n \cdot \text{amount})$ time** and **$O(\text{amount})$ space**, overcoming the failures of greedy heuristics on non-canonical currency systems.

---

## 22. Quiz

**Question 1:** Why does the Greedy strategy fail for `coins = [1, 3, 4]` and `amount = 6`?
- A) Greedy produces 4 coins, optimal is 5
- B) Greedy picks $4 + 1 + 1$ (3 coins), but optimal is $3 + 3$ (2 coins)
- C) Greedy enters an infinite loop
- D) Greedy cannot handle even amounts
- **Correct Answer:** B
- **Explanation:** Greedy selects the largest coin 4 first, missing the optimal combination of two 3s.

**Question 2:** What is the space complexity of 1D Coin Change DP?
- A) $O(n \cdot \text{amount})$
- B) $O(n)$
- C) $O(\text{amount})$
- D) $O(1)$
- **Correct Answer:** C
- **Explanation:** Uses a single array of size `amount + 1`.

**Question 3:** What does `dp[0] = 0` represent in Min Coins?
- A) Amount 0 requires infinity coins
- B) Amount 0 requires 0 coins
- C) Amount 0 cannot be formed
- D) Coin 0 is invalid
- **Correct Answer:** B
- **Explanation:** Base case: 0 coins are needed to make an amount of 0.

**Question 4:** How do you compute COMBINATIONS instead of PERMUTATIONS in Coin Change II?
- A) Put `coins` loop outside and `amount` loop inside
- B) Put `amount` loop outside and `coins` loop inside
- C) Sort coins in ascending order
- D) Use a 2D array
- **Correct Answer:** A
- **Explanation:** Processing each coin one by one in the outer loop prevents counting different orderings of the same coins.

**Question 5:** What should the Min Coins function return if an amount cannot be formed?
- A) 0
- B) `INT_MAX`
- C) -1
- D) `amount`
- **Correct Answer:** C
- **Explanation:** By standard convention (e.g. LeetCode 322), unformable amounts return -1.

**Question 6:** What is the time complexity of Coin Change DP?
- A) $O(n \log n)$
- B) $O(2^n)$
- C) $O(n \cdot \text{amount})$
- D) $O(n^2)$
- **Correct Answer:** C
- **Explanation:** Outer loop runs `amount` times, inner loop checks $n$ coins.

**Question 7:** Why is `amount + 1` used as a sentinel value instead of `INT_MAX` in C/C++?
- A) To save memory
- B) To prevent integer overflow when adding 1 (`1 + INT_MAX`)
- C) Because `amount + 1` is faster to calculate
- D) To allow negative coin values
- **Correct Answer:** B
- **Explanation:** Adding 1 to `INT_MAX` causes signed integer overflow wrap-around to negative values.

**Question 8:** Which alternative algorithm can find Min Coins in unweighted graph terms?
- A) Depth-First Search (DFS)
- B) Breadth-First Search (BFS)
- C) Kruskal's Algorithm
- D) Binary Search
- **Correct Answer:** B
- **Explanation:** BFS finds the shortest path level in an unweighted graph where each coin deduction is a step of weight 1.

**Question 9:** What is the base case `dp[0]` for Coin Change II (Total Ways)?
- A) 0
- B) 1
- C) -1
- D) Infinity
- **Correct Answer:** B
- **Explanation:** There is 1 valid way to make amount 0 (choosing no coins).

**Question 10:** What family of Dynamic Programming problems does Coin Change belong to?
- A) 0/1 Knapsack
- B) Unbounded Knapsack
- C) Fractional Knapsack
- D) Matrix Chain Multiplication
- **Correct Answer:** B
- **Explanation:** Coins can be reused unlimited times, matching the Unbounded Knapsack definition.
