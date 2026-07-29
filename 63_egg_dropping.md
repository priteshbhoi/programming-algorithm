# Egg Dropping Problem (Super Egg Drop - Dynamic Programming)

## 1. Introduction

The **Egg Dropping Problem** (also known as **Super Egg Drop**) is a famous mathematical puzzle and classic computer science interview problem. Given $k$ identical eggs and a building with $n$ floors (numbered $1$ to $n$), there exists a critical floor $f$ ($0 \le f \le n$) such that any egg dropped from a floor higher than $f$ will break, while an egg dropped from floor $f$ or below will survive intact.

The rules of the drop test are:
- An egg that survives a drop can be picked up and reused for subsequent tests.
- A broken egg cannot be used again.
- All eggs are identical in physical strength.
- If an egg breaks from a certain floor, it will break from all higher floors.
- If an egg survives a drop from a certain floor, it will survive drops from all lower floors.

The goal is to find the **minimum number of drop attempts required in the worst case** to determine the critical floor $f$ with certainty.

A naive recursive approach takes exponential time. Standard Dynamic Programming takes $O(k \cdot n^2)$ time. Using **Binary Search on DP transitions** optimizes time to **$O(k \cdot n \log n)$**. By reformulating the problem into **Inverse DP** (finding maximum floors testable with $m$ moves and $k$ eggs), we achieve optimal **$O(k \log n)$ time** and **$O(k)$ space**.

---

## 2. Why Use This Algorithm?

### Minimax Optimization Principle:
This is a **Minimax Problem**:
- **Mini:** We want to **minimize** the total number of drops.
- **Max:** We must prepare for the **worst-case scenario** (nature making the egg break or survive in the worst possible way).

### Special Cases:
1. **1 Egg ($k = 1$):** You must test floors sequentially from floor 1 up to floor $n$ (Linear Search) to prevent destroying your only egg prematurely. Worst case = **$n$ drops**.
2. **Infinite Eggs ($k \ge \log_2(n+1)$):** You can use standard Binary Search by dropping from the middle floor repeatedly. Worst case = **$\lceil \log_2(n+1) \rceil$ drops**.
3. **Limited Eggs ($1 < k < \log_2(n+1)$):** Binary search fails because breaking an egg cuts down your remaining eggs. We must balance binary and linear search using Dynamic Programming.

---

## 3. Real-World Applications

- **Destructive Hardware Stress Testing:** Finding the maximum drop height or impact force a smartphone casing can sustain before cracking, using a limited count of prototype test devices.
- **Semiconductor Breakdown Voltage Testing:** Determining the exact maximum voltage threshold a microchip can withstand before experiencing permanent gate oxide failure.
- **High-Pressure Pipeline Burst Tests:** Testing maximum internal pressure tolerances for oil/gas pipelines with limited destructive test samples.
- **API Rate Limiting & Network Probing:** Determining the exact network throughput ceiling before packet drops occur using limited test probe packets.
- **Clinical Trial Maximum Tolerated Dose (MTD):** Finding the highest safe drug dosage in oncology trials without causing toxic side effects in patients.

---

## 4. Prerequisites

Before studying the Egg Dropping Problem, you should be familiar with:
- **Minimax Strategy:** Minimizing maximum worst-case outcomes.
- **2D Dynamic Programming Grids.**
- **Binary Search Optimization on DP State Transitions.**

---

## 5. Visualization

### Dropping an Egg from Floor $x$ out of $n$ Floors with $k$ Eggs

```
                      Drop Egg from Floor x
                          /          \
                         /            \
              Egg Breaks                Egg Survives
             /                            \
Search lower x-1 floors               Search upper n-x floors
with k-1 eggs remaining               with k eggs remaining
Cost: 1 + dp[k-1][x-1]                 Cost: 1 + dp[k][n-x]
                         \            /
                          \          /
                    Worst Case: MAX(Breaks, Survives)
```

### 2 Eggs 100 Floors Classic Puzzle Solution:
For 2 eggs and 100 floors, the optimal step interval decreases by 1 each time ($x + (x-1) + (x-2) + \dots + 1 \ge 100$):
$$\frac{x(x+1)}{2} \ge 100 \implies x = 14 \text{ drops}$$
First drop at floor 14, then 27, 39, 50, 60, 70, 79, 87, 94, 99, 100.

### Mermaid Flowchart: Minimax State Decision

```mermaid
graph TD
    Start([Start: k eggs, n floors]) --> BaseCheck{Is k == 1 OR n <= 1?}
    BaseCheck -- Yes --> ReturnBase[Return n]
    BaseCheck -- No --> StateCheck{"Is dp[k][n] computed?"}
    StateCheck -- Yes --> ReturnDP[Return dp[k][n]]
    StateCheck -- No --> BinarySearch[Binary Search for optimal split floor x in 1..n]
    BinarySearch --> EvaluateSplit["Compute cost = 1 + max( dp[k-1][x-1], dp[k][n-x] )"]
    EvaluateSplit --> StoreMin["dp[k][n] = min( cost over all x )"]
    StoreMin --> ReturnResult[Return dp[k][n]]
    ReturnResult --> End([End])
    ReturnBase --> End
    ReturnDP --> End
```

---

## 6. How It Works

### Approach 1: Standard Dynamic Programming ($O(k \cdot n^2)$ Time)
Let $dp[k][n]$ represent the minimum number of attempts needed in the worst case with $k$ eggs and $n$ floors.

If we drop an egg from floor $x$ ($1 \le x \le n$):
1. **Egg Breaks:** We now have $k-1$ eggs and must search the lower $x-1$ floors. Cost = $dp[k-1][x-1]$.
2. **Egg Survives:** We still have $k$ eggs and must search the upper $n-x$ floors. Cost = $dp[k][n-x]$.

Since nature dictates the worst case, we take $\max(dp[k-1][x-1], dp[k][n-x])$. Since we choose floor $x$ to minimize worst-case attempts, the recurrence relation is:
$$dp[k][n] = 1 + \min_{1 \le x \le n} \left( \max(dp[k-1][x-1], \, dp[k][n-x]) \right)$$

### Base Cases:
- $dp[k][0] = 0$ (0 floors require 0 drops).
- $dp[k][1] = 1$ (1 floor requires 1 drop).
- $dp[1][n] = n$ (1 egg requires $n$ sequential drops).

---

### Approach 2: DP + Binary Search ($O(k \cdot n \log n)$ Time)
Observe that as floor $x$ increases:
- $T_1(x) = dp[k-1][x-1]$ is strictly **increasing**.
- $T_2(x) = dp[k][n-x]$ is strictly **decreasing**.

The maximum of an increasing function $T_1$ and decreasing function $T_2$ is minimized at their **intersection point**. We can use **Binary Search** over $x \in [1, n]$ to find the optimal split floor in $O(\log n)$ time, reducing total DP runtime to $O(k \cdot n \log n)$.

---

### Approach 3: Inverse DP ($O(k \log n)$ Time & $O(k)$ Space) — Optimal!
Instead of asking *"How many drops for $k$ eggs and $n$ floors?"*, we reverse the question:
**"What is the maximum number of floors we can test with $m$ moves and $k$ eggs?"**

Let $dp[m][k]$ be the maximum floors testable with $m$ moves and $k$ eggs.

If we drop an egg with $m$ moves and $k$ eggs:
- If it breaks, we can test $dp[m-1][k-1]$ floors below.
- If it survives, we can test $dp[m-1][k]$ floors above.
- Including the current test floor itself ($1$), the total testable floors is:
$$dp[m][k] = 1 + dp[m-1][k-1] + dp[m-1][k]$$

We increment $m$ step-by-step until $dp[m][k] \ge n$. The value of $m$ is our answer!
This requires only **$O(k \log n)$ time** and **$O(k)$ space**.

---

## 7. Step-by-Step Algorithm

### Inverse DP Algorithm ($O(k \log n)$ Time, $O(k)$ Space):
1. Input: Number of eggs $k$, number of floors $n$.
2. Create a 1D array `dp` of size $k + 1$, initialized to $0$.
3. Initialize `moves = 0`.
4. While `dp[k] < n`:
   a. `moves = moves + 1`
   b. Loop `j` backward from $k$ down to $1$:
      - `dp[j] = 1 + dp[j - 1] + dp[j]`
5. Return `moves`.

---

## 8. Pseudocode

### Approach 1: $O(k \cdot n \log n)$ DP + Binary Search
```text
function superEggDropBS(k, n):
    create 2D array dp[k + 1][n + 1] initialized to 0

    for j from 1 to n:
        dp[1][j] = j

    for i from 2 to k:
        for j from 1 to n:
            low = 1
            high = j
            minAttempts = j

            while low <= high:
                mid = low + (high - low) / 2
                broken = dp[i - 1][mid - 1]
                survived = dp[i][j - mid]

                if broken > survived:
                    minAttempts = min(minAttempts, 1 + broken)
                    high = mid - 1
                else:
                    minAttempts = min(minAttempts, 1 + survived)
                    low = mid + 1

            dp[i][j] = minAttempts

    return dp[k][n]
```

### Approach 2: $O(k \log n)$ Inverse DP (Optimal)
```text
function superEggDropOptimal(k, n):
    create 1D array dp of size (k + 1) filled with 0
    moves = 0

    while dp[k] < n:
        moves = moves + 1
        for j from k down to 1:
            dp[j] = 1 + dp[j - 1] + dp[j]

    return moves
```

---

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>

// Optimal Inverse DP: O(k log n) Time, O(k) Space
int superEggDropOptimal(int k, int n) {
    int* dp = (int*)calloc(k + 1, sizeof(int));
    int moves = 0;

    while (dp[k] < n) {
        moves++;
        for (int j = k; j >= 1; j--) {
            dp[j] = 1 + dp[j - 1] + dp[j];
        }
    }

    free(dp);
    return moves;
}

// Standard DP + Binary Search: O(k * n log n) Time
int superEggDropBS(int k, int n) {
    int** dp = (int**)malloc((k + 1) * sizeof(int*));
    for (int i = 0; i <= k; i++) {
        dp[i] = (int*)calloc(n + 1, sizeof(int));
    }

    for (int j = 1; j <= n; j++) dp[1][j] = j;

    for (int i = 2; i <= k; i++) {
        for (int j = 1; j <= n; j++) {
            int low = 1, high = j, minAttempts = j;
            while (low <= high) {
                int mid = low + (high - low) / 2;
                int broken = dp[i - 1][mid - 1];
                int survived = dp[i][j - mid];

                if (broken > survived) {
                    if (1 + broken < minAttempts) minAttempts = 1 + broken;
                    high = mid - 1;
                } else {
                    if (1 + survived < minAttempts) minAttempts = 1 + survived;
                    low = mid + 1;
                }
            }
            dp[i][j] = minAttempts;
        }
    }

    int result = dp[k][n];
    for (int i = 0; i <= k; i++) free(dp[i]);
    free(dp);
    return result;
}

int main() {
    int k = 2, n = 100;
    printf("Min Attempts for %d eggs, %d floors (Optimal): %d\n", k, n, superEggDropOptimal(k, n));
    printf("Min Attempts for %d eggs, %d floors (BS DP): %d\n", k, n, superEggDropBS(k, n));
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

class SuperEggDrop {
public:
    // Optimal Inverse DP: O(k log n) Time, O(k) Space
    static int superEggDropOptimal(int k, int n) {
        vector<int> dp(k + 1, 0);
        int moves = 0;

        while (dp[k] < n) {
            moves++;
            for (int j = k; j >= 1; --j) {
                dp[j] = 1 + dp[j - 1] + dp[j];
            }
        }
        return moves;
    }

    // DP + Binary Search: O(k * n log n) Time
    static int superEggDropBS(int k, int n) {
        vector<vector<int>> dp(k + 1, vector<int>(n + 1, 0));

        for (int j = 1; j <= n; ++j) dp[1][j] = j;

        for (int i = 2; i <= k; ++i) {
            for (int j = 1; j <= n; ++j) {
                int low = 1, high = j, minAttempts = j;
                while (low <= high) {
                    int mid = low + (high - low) / 2;
                    int broken = dp[i - 1][mid - 1];
                    int survived = dp[i][j - mid];

                    if (broken > survived) {
                        minAttempts = min(minAttempts, 1 + broken);
                        high = mid - 1;
                    } else {
                        minAttempts = min(minAttempts, 1 + survived);
                        low = mid + 1;
                    }
                }
                dp[i][j] = minAttempts;
            }
        }
        return dp[k][n];
    }
};

int main() {
    int k = 2, n = 100;
    cout << "Min Attempts for " << k << " eggs, " << n << " floors (Optimal): " 
         << SuperEggDrop::superEggDropOptimal(k, n) << endl;
    cout << "Min Attempts for " << k << " eggs, " << n << " floors (BS DP): " 
         << SuperEggDrop::superEggDropBS(k, n) << endl;
    return 0;
}
```

### Java
```java
public class SuperEggDrop {

    // Optimal Inverse DP: O(k log n) Time, O(k) Space
    public static int superEggDropOptimal(int k, int n) {
        int[] dp = new int[k + 1];
        int moves = 0;

        while (dp[k] < n) {
            moves++;
            for (int j = k; j >= 1; j--) {
                dp[j] = 1 + dp[j - 1] + dp[j];
            }
        }
        return moves;
    }

    public static void main(String[] args) {
        int k = 2, n = 100;
        System.out.println("Min Attempts for " + k + " eggs, " + n + " floors: " 
                           + superEggDropOptimal(k, n));
    }
}
```

### Python
```python
def super_egg_drop_optimal(k: int, n: int) -> int:
    """Optimal Inverse DP: O(k log n) time, O(k) space."""
    dp = [0] * (k + 1)
    moves = 0

    while dp[k] < n:
        moves += 1
        for j in range(k, 0, -1):
            dp[j] = 1 + dp[j - 1] + dp[j]

    return moves

def super_egg_drop_bs(k: int, n: int) -> int:
    """DP + Binary Search: O(k * n log n) time."""
    dp = [[0] * (n + 1) for _ in range(k + 1)]

    for j in range(1, n + 1):
        dp[1][j] = j

    for i in range(2, k + 1):
        for j in range(1, n + 1):
            low, high, min_attempts = 1, j, j
            while low <= high:
                mid = (low + high) // 2
                broken = dp[i - 1][mid - 1]
                survived = dp[i][j - mid]

                if broken > survived:
                    min_attempts = min(min_attempts, 1 + broken)
                    high = mid - 1
                else:
                    min_attempts = min(min_attempts, 1 + survived)
                    low = mid + 1

            dp[i][j] = min_attempts

    return dp[k][n]

if __name__ == "__main__":
    k, n = 2, 100
    print(f"Min Attempts for {k} eggs, {n} floors (Optimal): {super_egg_drop_optimal(k, n)}")
    print(f"Min Attempts for {k} eggs, {n} floors (BS DP): {super_egg_drop_bs(k, n)}")
```

### JavaScript
```javascript
/**
 * Optimal Inverse DP Super Egg Drop
 * @param {number} k Number of eggs
 * @param {number} n Number of floors
 * @returns {number} Minimum moves
 */
function superEggDropOptimal(k, n) {
    const dp = new Array(k + 1).fill(0);
    let moves = 0;

    while (dp[k] < n) {
        moves++;
        for (let j = k; j >= 1; j--) {
            dp[j] = 1 + dp[j - 1] + dp[j];
        }
    }
    return moves;
}

// Execution and testing
const k = 2, n = 100;
console.log(`Min Attempts for ${k} eggs, ${n} floors:`, superEggDropOptimal(k, n));
```

---

## 10. Code Explanation

1. **Inverse DP State Definition (`dp[j]`):** In the optimal $O(k \log n)$ implementation, `dp[j]` stores the maximum number of floors that can be checked using current `moves` and $j$ eggs.
2. **State Formula (`1 + dp[j-1] + dp[j]`):** 
   - `1`: The floor we drop the egg from.
   - `dp[j-1]`: Max floors we can check below if the egg breaks (with $j-1$ eggs remaining).
   - `dp[j]`: Max floors we can check above if the egg survives (with $j$ eggs remaining).
3. **Backward Loop (`for j from k down to 1`):** Backward iteration ensures `dp[j-1]` retains the value from the previous move step (`moves - 1`), avoiding overwriting active values.
4. **Termination Condition (`dp[k] >= n`):** The loop halts as soon as the total testable floors reach or exceed $n$.

---

## 11. Interactive Demo

An interactive Super Egg Drop Simulator includes:
- **Building Height & Egg Count Sliders:** Adjust $n$ floors and $k$ eggs dynamically.
- **Interactive Floor Dropper:** Click a floor to drop an egg; visual indicators show egg shattering (red) or landing safely (green).
- **Minimax Decision Tree Visualizer:** Displays the optimal floor split paths and remaining egg inventory.

---

## 12. Dry Run

### Sample Input: $k = 2$ eggs, $n = 6$ floors

### Step-by-Step Inverse DP Trace:

| Move ($m$) | `dp[0]` | `dp[1]` ($m$) | `dp[2]` ($1 + dp[0] + dp[1]$) | Max Floors Covered (`dp[2]`) | Target $n=6$ Reached? |
|:---:|:---:|:---:|:---:|:---:|:---:|
| **Init** | 0 | 0 | 0 | 0 | No |
| **Move 1** | 0 | 1 | $1 + 0 + 0 = \mathbf{1}$ | 1 | No |
| **Move 2** | 0 | 2 | $1 + 1 + 1 = \mathbf{3}$ | 3 | No |
| **Move 3** | 0 | 3 | $1 + 2 + 3 = \mathbf{7}$ | **7** | **Yes ($7 \ge 6$)** |

**Final Result:** Total Moves = **3** (For $k=2$ eggs and $n=6$ floors, minimum worst-case drops = 3).

---

## 13. Time & Space Complexity Analysis

| Algorithm | Time Complexity | Space Complexity | Notes |
|:---|:---:|:---:|:---|
| **Naive DP** | $O(k \cdot n^2)$ | $O(k \cdot n)$ | Checks all $x \in [1, n]$ floors linearly |
| **DP + Binary Search** | $O(k \cdot n \log n)$ | $O(k \cdot n)$ | Uses binary search for optimal split floor |
| **Inverse DP (Optimal)** | **$O(k \log n)$** | **$O(k)$** | **Evaluates moves until $n$ floors are covered** |

---

## 14. Advantages

- **Ultra-Fast $O(k \log n)$ Runtime:** Easily evaluates $n = 10^9$ floors in under a microsecond.
- **Minimal Space Requirement:** Requires only a 1D array of size $k+1$ ($O(k)$ space).
- **Guaranteed Worst-Case Optimal:** Mathematically minimizes worst-case testing costs.

---

## 15. Disadvantages

- **Non-Intuitive Dual State Formulation:** Inverse DP ($m$ moves vs $k$ eggs) requires re-framing the problem away from standard building floor recursion.
- **Assumes Uniform Drop Cost:** Does not account for variable floor travel time or physical movement costs.

---

## 16. Variations & Advanced Optimizations

1. **2 Eggs 100 Floors Closed-Form Formula:**
   For $k=2$ eggs, solving $\frac{x(x+1)}{2} \ge n$ directly gives $x = \lceil \frac{-1 + \sqrt{1 + 8n}}{2} \rceil$. For $n=100$, $x = 14$.
2. **Infinite Eggs Case ($k \ge \log_2(n+1)$):**
   If eggs exceed $\log_2(n+1)$, standard Binary Search requires exactly $\lceil \log_2(n+1) \rceil$ drops.

---

## 17. Common Mistakes & Pitfalls

- **Confusing Average Case with Worst Case:** Minimizing average attempts instead of taking $\max(\text{breaks}, \text{survives})$.
- **Using Linear Search Over Floors in $O(k \cdot n^2)$ DP:** Failing to apply Binary Search or Inverse DP, causing execution timeout on LeetCode 887.
- **Forgetting `1 +` for Current Drop:** Omitting the $+1$ attempt cost in state transitions.

---

## 18. Interview Questions

1. **How many drops are needed for 2 eggs and 100 floors in the worst case?**
   *Answer:* **14 drops**. Using decreasing floor intervals ($14, 27, 39, 50, 60, 70, 79, 87, 94, 99, 100$).

2. **Why does Binary Search fail when you only have 2 eggs for 100 floors?**
   *Answer:* If you drop the first egg from floor 50 and it breaks, you only have 1 egg left and must test floors 1 to 49 sequentially, taking $1 + 49 = 50$ drops in the worst case.

3. **What is the time complexity of the optimal Inverse DP algorithm for Super Egg Drop?**
   *Answer:* $O(k \log n)$ time.

4. **What does $dp[m][k]$ represent in the Inverse DP formulation?**
   *Answer:* The maximum number of floors that can be tested using $m$ moves and $k$ eggs.

5. **What is the recurrence relation of Inverse DP?**
   *Answer:* $dp[m][k] = 1 + dp[m-1][k-1] + dp[m-1][k]$.

6. **What is the answer if $k = 1$ egg for $n$ floors?**
   *Answer:* $n$ drops (Linear search from bottom to top).

7. **What is the answer if $k \ge \log_2(n+1)$ eggs?**
   *Answer:* $\lceil \log_2(n+1) \rceil$ drops (Standard Binary Search).

8. **Why is the Egg Dropping Problem classified as a Minimax problem?**
   *Answer:* Because we choose floor $x$ to **minimize** the maximum attempts across worst-case outcomes (**max** of breaks vs survives).

9. **What is the space complexity of optimal Super Egg Drop?**
   *Answer:* $O(k)$ space.

10. **How many floors can be tested with 3 moves and 2 eggs?**
    *Answer:* 7 floors. ($dp[3][2] = 1 + dp[2][1] + dp[2][2] = 1 + 2 + 4 = 7$).

---

## 19. Practice Problems

### Medium
1. **GeeksforGeeks:** [Egg Dropping Puzzle](https://practice.geeksforgeeks.org/) - Standard 2-egg or $k$-egg DP puzzle.

### Hard
2. **LeetCode 887:** [Super Egg Drop](https://leetcode.com/problems/super-egg-drop/) - Requires $O(k \log n)$ Inverse DP or DP + Binary Search to avoid TLE.
3. **LeetCode 1884:** [Egg Drop With 2 Eggs and N Floors](https://leetcode.com/problems/egg-drop-with-2-eggs-and-n-floors/) - 2-egg special case with closed-form solution.

---

## 20. Related Algorithms

- **Binary Search:** Optimal search when eggs are unlimited.
- **Minimax Algorithm:** Game theory strategy for worst-case optimization.
- **Triangular Numbers:** Mathematical series used for 2-egg closed-form solution.

---

## 21. Summary

The Egg Dropping Problem is a masterclass in Minimax optimization. By reformulating the building floor search into **Inverse DP** ($dp[m][k] = 1 + dp[m-1][k-1] + dp[m-1][k]$), it solves the worst-case drop test in **$O(k \log n)$ time** and **$O(k)$ space**.

---

## 22. Quiz

**Question 1:** What is the minimum worst-case number of drops for 2 eggs and 100 floors?
- A) 10
- B) 14
- C) 50
- D) 100
- **Correct Answer:** B
- **Explanation:** Solving $\frac{x(x+1)}{2} \ge 100$ gives $x = 14$.

**Question 2:** If you have only 1 egg ($k=1$) for $n$ floors, what is the required drop strategy?
- A) Binary search from floor $n/2$
- B) Drop from top floor $n$ first
- C) Sequential linear search from floor 1 to $n$ ($n$ drops)
- D) Drop from random floors
- **Correct Answer:** C
- **Explanation:** With 1 egg, breaking it destroys all testing capability, forcing linear search from bottom to top.

**Question 3:** What is the time complexity of the optimal Inverse DP algorithm for Super Egg Drop?
- A) $O(n^2)$
- B) $O(k \cdot n)$
- C) $O(k \log n)$
- D) $O(2^k)$
- **Correct Answer:** C
- **Explanation:** Inverse DP computes testable floors in $O(k \log n)$ time.

**Question 4:** What does $dp[m][k]$ represent in Inverse DP?
- A) The minimum drops for $m$ floors and $k$ eggs
- B) The maximum number of floors that can be tested using $m$ moves and $k$ eggs
- C) The number of broken eggs
- D) The cost of dropping an egg from floor $m$
- **Correct Answer:** B
- **Explanation:** Inverse DP tracks total testable floors for a given move count $m$ and egg count $k$.

**Question 5:** What is the state transition formula for Inverse DP?
- A) $dp[m][k] = dp[m-1][k] + 1$
- B) $dp[m][k] = 1 + dp[m-1][k-1] + dp[m-1][k]$
- C) $dp[m][k] = dp[m-1][k-1] \times 2$
- D) $dp[m][k] = \max(dp[m-1][k-1], dp[m-1][k])$
- **Correct Answer:** B
- **Explanation:** Includes current test floor (1) + floors testable if egg breaks ($dp[m-1][k-1]$) + floors testable if egg survives ($dp[m-1][k]$).

**Question 6:** How many drops are required for $k \ge \log_2(n+1)$ unlimited eggs?
- A) $n$
- B) $\lceil \log_2(n+1) \rceil$
- C) $\sqrt{n}$
- D) $n / 2$
- **Correct Answer:** B
- **Explanation:** Unlimited eggs allow standard binary search in $\lceil \log_2(n+1) \rceil$ steps.

**Question 7:** Why does standard $O(k \cdot n^2)$ DP cause Time Limit Exceeded (TLE) on LeetCode 887?
- A) Because array indexing is slow
- B) Because $n$ can be up to $10^4$, making $n^2 = 10^8$ operations per egg
- C) Because LeetCode disables DP
- D) Because $k$ is always 1
- **Correct Answer:** B
- **Explanation:** For $n = 10^4$, $O(k \cdot n^2)$ performs $10^8$ operations per egg, exceeding time limits.

**Question 8:** What is the space complexity of optimal Super Egg Drop?
- A) $O(k \cdot n)$
- B) $O(n)$
- C) $O(k)$
- D) $O(1)$
- **Correct Answer:** C
- **Explanation:** Requires a 1D array of size $k+1$.

**Question 9:** How many floors can be tested with 3 moves and 2 eggs?
- A) 3
- B) 5
- C) 7
- D) 10
- **Correct Answer:** C
- **Explanation:** $dp[3][2] = 1 + dp[2][1] + dp[2][2] = 1 + 2 + 4 = 7$ floors.

**Question 10:** Why is taking $\max(\text{breaks}, \text{survives})$ required in standard Egg Drop DP?
- A) To compute the average number of drops
- B) To prepare for the worst-case outcome determined by nature
- C) To minimize memory usage
- D) To sort the floor array
- **Correct Answer:** B
- **Explanation:** Minimax optimization requires taking the maximum of possible outcomes to guarantee worst-case correctness.
