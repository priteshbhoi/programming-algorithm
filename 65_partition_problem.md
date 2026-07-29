# Partition Problem (Equal Subset Sum Partition - Dynamic Programming)

## 1. Introduction

The **Partition Problem** (also known as **Equal Subset Sum Partition**) is a classic decision problem in computer science and combinatorial optimization. Given a set/array of positive integers `arr[]` of size $n$, the objective is to determine whether the array can be partitioned into two disjoint subsets, $S_1$ and $S_2$, such that the sum of elements in $S_1$ equals the sum of elements in $S_2$.

Mathematically:
$$S_1 \cup S_2 = \text{arr}, \quad S_1 \cap S_2 = \emptyset, \quad \sum_{x \in S_1} x = \sum_{y \in S_2} y = \frac{\text{TotalSum}}{2}$$

For example, for `arr = [1, 5, 11, 5]`:
- Total sum = $1 + 5 + 11 + 5 = 22$ (Even).
- Target subset sum = $22 / 2 = 11$.
- Valid partition: $S_1 = [1, 5, 5]$ (Sum = 11) and $S_2 = [11]$ (Sum = 11).
- Output: **True**.

A critical prerequisite is that the **total sum of all elements must be even**. If the total sum is odd, an equal integer partition is mathematically impossible.

The Partition Problem is NP-complete, but Dynamic Programming solves it in **pseudo-polynomial time $O(n \cdot \text{target})$** and **$O(\text{target})$ space** by reducing it directly to the **Subset Sum Problem** with $\text{target} = \text{TotalSum} / 2$.

---

## 2. Why Use This Algorithm?

### Reduction to Subset Sum:
Instead of testing all $2^n$ possible partition splits, we observe that if a subset $S_1$ sums to $\text{TotalSum} / 2$, the remaining elements $S_2$ automatically sum to $\text{TotalSum} / 2$.

**Benefits:**
- **$O(1)$ Early Exit:** Instantly returns `false` if `TotalSum % 2 != 0`.
- **Exact Balance Guarantee:** Ensures perfect numerical equality between two split sets.
- **Low Memory Overhead:** Requires only a 1D boolean array of size $\text{target} + 1$ ($O(\text{target})$ space).
- **Bitwise Parallel Acceleration:** Can be accelerated using C++ `std::bitset` by $64\times$.

---

## 3. Real-World Applications

- **Dual-Processor / Dual-Core Load Balancing:** Dividing a batch of non-preemptive computational tasks between two identical CPU cores to equalize execution time.
- **Estate & Asset Partitioning:** Dividing a collection of discrete physical assets or real estate properties of known monetary value equally between two heirs.
- **Dual-Warehouse Inventory Allocation:** Splitting stock shipments evenly between two distribution centers to balance storage strain.
- **Fair Team Assignment in Sports:** Grouping players with assigned numerical skill ratings into two teams with identical total skill sums.

---

## 4. Prerequisites

Before studying the Partition Problem, you should be familiar with:
- **Parity Checking:** Odd vs. Even numbers (`totalSum % 2 == 0`).
- **Subset Sum DP Algorithm.**
- **1D Array Tabulation with Reverse Iteration.**

---

## 5. Visualization

### Equal Partition Target Reduction

```
Total Array Sum = 22

               [1, 5, 11, 5]   (Total Sum = 22)
                     |
         Is Total Sum Even? (22 % 2 == 0) -> YES!
                     |
         Target Subset Sum = 22 / 2 = 11
                     |
         Find Subset Summing to 11
        /                         \
    Subset S1 = [1, 5, 5]     Subset S2 = [11]
    (Sum = 11)                (Sum = 11)
```

### Mermaid Flowchart: Equal Subset Sum Partition Pipeline

```mermaid
graph TD
    Start([Start: Input array arr of size n]) --> CalcSum[Compute totalSum = sum of all elements]
    CalcSum --> ParityCheck{"Is totalSum % 2 != 0?"}
    ParityCheck -- Yes --> ReturnFalse[Return False - Cannot partition odd sum]
    ParityCheck -- No --> SetTarget["target = totalSum / 2"]
    SetTarget --> InitDP[Initialize boolean dp array of size target + 1 with false]
    InitDP --> BaseSet[dp[0] = true]
    BaseSet --> OuterLoop[Loop num in arr]
    OuterLoop --> InnerLoop[Loop j from target down to num]
    InnerLoop --> StateUpdate["dp[j] = dp[j] OR dp[j - num]"]
    StateUpdate --> NextJ{j >= num?}
    NextJ -- Yes --> InnerLoop
    NextJ -- No --> NextNum{More nums?}
    NextNum -- Yes --> OuterLoop
    NextNum -- No --> ReturnResult[Return dp[target]]
    ReturnResult --> End([End])
    ReturnFalse --> End
```

---

## 6. How It Works

1. **Calculate Total Sum:** Compute $\text{totalSum} = \sum_{i=0}^{n-1} \text{arr}[i]$.
2. **Parity Check:** If $\text{totalSum}$ is odd ($\text{totalSum} \pmod 2 \neq 0$), return `false`.
3. **Set Target:** Calculate $\text{target} = \text{totalSum} / 2$.
4. **Run 1D Subset Sum DP:**
   - Create boolean array `dp` of size $\text{target} + 1$, with `dp[0] = true`.
   - For each element `num` in `arr`:
     - Loop $j$ from $\text{target}$ down to `num`:
       $$dp[j] = dp[j] \lor dp[j - \text{num}]$$
5. **Return Result:** `dp[target]`.

---

## 7. Step-by-Step Algorithm

### Partition Problem Algorithm:
1. Input: Array `arr[]` of size $n$.
2. Compute `totalSum` of all elements in `arr`.
3. If `totalSum % 2 != 0`, return `false`.
4. Set `target = totalSum / 2`.
5. Create boolean array `dp` of size `target + 1` filled with `false`.
6. Set `dp[0] = true`.
7. For each `num` in `arr`:
   - For `j` from `target` down to `num`:
     - `dp[j] = dp[j] || dp[j - num]`
8. Return `dp[target]`.

### Partition Reconstruction (Reconstructing $S_1$ and $S_2$):
1. Build 2D DP matrix `dp[n+1][target+1]`.
2. Start at $i = n, j = \text{target}$.
3. While $i > 0$:
   - If `dp[i-1][j]` is true, element $\text{arr}[i-1]$ belongs to **$S_2$**.
   - Else, element $\text{arr}[i-1]$ belongs to **$S_1$**; update $j = j - \text{arr}[i-1]$.
   - Move $i = i - 1$.

---

## 8. Pseudocode

```text
function canPartition(arr, n):
    totalSum = 0
    for i from 0 to n - 1:
        totalSum = totalSum + arr[i]

    if totalSum % 2 != 0:
        return false

    target = totalSum / 2
    create boolean array dp of size (target + 1) filled with false
    dp[0] = true

    for num in arr:
        for j from target down to num:
            if dp[j - num] == true:
                dp[j] = true

    return dp[target]
```

---

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>

bool canPartition(int arr[], int n) {
    int totalSum = 0;
    for (int i = 0; i < n; i++) {
        totalSum += arr[i];
    }

    // Odd sum cannot be partitioned equally
    if (totalSum % 2 != 0) return false;

    int target = totalSum / 2;
    bool* dp = (bool*)calloc(target + 1, sizeof(bool));
    dp[0] = true;

    for (int i = 0; i < n; i++) {
        for (int j = target; j >= arr[i]; j--) {
            if (dp[j - arr[i]]) {
                dp[j] = true;
            }
        }
    }

    bool result = dp[target];
    free(dp);
    return result;
}

// Full Partition Reconstruction
void printPartition(int arr[], int n) {
    int totalSum = 0;
    for (int i = 0; i < n; i++) totalSum += arr[i];

    if (totalSum % 2 != 0) {
        printf("Cannot partition: Total sum (%d) is odd.\n", totalSum);
        return;
    }

    int target = totalSum / 2;
    bool** dp = (bool**)malloc((n + 1) * sizeof(bool*));
    for (int i = 0; i <= n; i++) {
        dp[i] = (bool*)calloc(target + 1, sizeof(bool));
        dp[i][0] = true;
    }

    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= target; j++) {
            if (arr[i - 1] <= j) {
                dp[i][j] = dp[i - 1][j] || dp[i - 1][j - arr[i - 1]];
            } else {
                dp[i][j] = dp[i - 1][j];
            }
        }
    }

    if (!dp[n][target]) {
        printf("Cannot partition into equal sum subsets.\n");
    } else {
        printf("Equal Partition Found (Target Sum = %d):\n", target);
        printf("Subset 1: [ ");
        int j = target;
        for (int i = n; i > 0; i--) {
            if (!dp[i - 1][j]) {
                printf("%d ", arr[i - 1]);
                j -= arr[i - 1];
            }
        }
        printf("]\n");
    }

    for (int i = 0; i <= n; i++) free(dp[i]);
    free(dp);
}

int main() {
    int arr[] = {1, 5, 11, 5};
    int n = sizeof(arr) / sizeof(arr[0]);

    printf("Can Partition: %s\n", canPartition(arr, n) ? "Yes" : "No");
    printPartition(arr, n);
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
#include <numeric>
#include <bitset>

using namespace std;

class PartitionProblem {
public:
    // 1D Space-Optimized O(n * target) Time, O(target) Space
    static bool canPartition(const vector<int>& arr) {
        int totalSum = accumulate(arr.begin(), arr.end(), 0);
        if (totalSum % 2 != 0) return false;

        int target = totalSum / 2;
        vector<bool> dp(target + 1, false);
        dp[0] = true;

        for (int num : arr) {
            for (int j = target; j >= num; --j) {
                if (dp[j - num]) {
                    dp[j] = true;
                }
            }
        }
        return dp[target];
    }

    // std::bitset Accelerated Partition (64x faster)
    static bool canPartitionBitset(const vector<int>& arr) {
        int totalSum = accumulate(arr.begin(), arr.end(), 0);
        if (totalSum % 2 != 0) return false;

        int target = totalSum / 2;
        bitset<100001> dp;
        dp[0] = 1;

        for (int num : arr) {
            dp |= (dp << num);
        }
        return dp[target];
    }
};

int main() {
    vector<int> arr = {1, 5, 11, 5};

    cout << "Can Partition (1D DP): " << (PartitionProblem::canPartition(arr) ? "Yes" : "No") << endl;
    cout << "Can Partition (Bitset): " << (PartitionProblem::canPartitionBitset(arr) ? "Yes" : "No") << endl;

    return 0;
}
```

### Java
```java
import java.util.Arrays;

public class PartitionProblem {

    public static boolean canPartition(int[] arr) {
        int totalSum = 0;
        for (int num : arr) totalSum += num;

        if (totalSum % 2 != 0) return false;

        int target = totalSum / 2;
        boolean[] dp = new boolean[target + 1];
        dp[0] = true;

        for (int num : arr) {
            for (int j = target; j >= num; j--) {
                if (dp[j - num]) {
                    dp[j] = true;
                }
            }
        }
        return dp[target];
    }

    public static void main(String[] args) {
        int[] arr = {1, 5, 11, 5};
        System.out.println("Can Partition: " + (canPartition(arr) ? "Yes" : "No"));
    }
}
```

### Python
```python
def can_partition(arr: list[int]) -> bool:
    """Determines if arr can be partitioned into two equal sum subsets."""
    total_sum = sum(arr)
    if total_sum % 2 != 0:
        return False

    target = total_sum // 2
    dp = [False] * (target + 1)
    dp[0] = True

    for num in arr:
        for j in range(target, num - 1, -1):
            if dp[j - num]:
                dp[j] = True

    return dp[target]

def get_partition_subsets(arr: list[int]) -> tuple[bool, list[int], list[int]]:
    """Reconstructs both subsets S1 and S2."""
    total_sum = sum(arr)
    if total_sum % 2 != 0:
        return False, [], []

    target = total_sum // 2
    n = len(arr)
    dp = [[False] * (target + 1) for _ in range(n + 1)]
    for i in range(n + 1):
        dp[i][0] = True

    for i in range(1, n + 1):
        for j in range(1, target + 1):
            if arr[i - 1] <= j:
                dp[i][j] = dp[i - 1][j] or dp[i - 1][j - arr[i - 1]]
            else:
                dp[i][j] = dp[i - 1][j]

    if not dp[n][target]:
        return False, [], []

    s1, s2 = [], []
    j = target
    for i in range(n, 0, -1):
        if not dp[i - 1][j]:
            s1.append(arr[i - 1])
            j -= arr[i - 1]
        else:
            s2.append(arr[i - 1])

    print(f"Subset 1: {s1} (Sum={sum(s1)})")
    print(f"Subset 2: {s2} (Sum={sum(s2)})")
    return True, s1, s2

if __name__ == "__main__":
    arr = [1, 5, 11, 5]
    print(f"Can Partition: {can_partition(arr)}")
    get_partition_subsets(arr)
```

### JavaScript
```javascript
/**
 * Partition Problem DP Implementation
 * @param {number[]} arr 
 * @returns {boolean}
 */
function canPartition(arr) {
    const totalSum = arr.reduce((acc, val) => acc + val, 0);
    if (totalSum % 2 !== 0) return false;

    const target = Math.floor(totalSum / 2);
    const dp = new Array(target + 1).fill(false);
    dp[0] = true;

    for (const num of arr) {
        for (let j = target; j >= num; j--) {
            if (dp[j - num]) {
                dp[j] = true;
            }
        }
    }

    return dp[target];
}

// Execution and testing
const arr = [1, 5, 11, 5];
console.log("Can Partition:", canPartition(arr) ? "Yes" : "No");
```

---

## 10. Code Explanation

1. **Sum & Parity Check (`totalSum % 2 != 0`):** Fast $O(1)$ initial check. If the total array sum is odd, equal partition into two integer subsets is impossible.
2. **Target Reduction (`target = totalSum / 2`):** Reduces the problem to checking if a single subset can reach sum `target`.
3. **1D DP Boolean Array (`dp[target + 1]`):** `dp[j]` tracks whether a subset sum $j$ is formable.
4. **Reverse Capacity Loop (`for j from target down to num`):** Ensures elements are used at most once (0/1 constraint).
5. **Dual Subset Reconstruction:** By tracing backward through 2D DP matrix, elements that contributed to reaching `target` form $S_1$, while remaining elements form $S_2$.

---

## 11. Interactive Demo

An interactive Partition Visualizer includes:
- **Weight Scale Pan View:** Displays two balance pans (Pan A vs Pan B).
- **Number Card Drag-and-Drop:** Allows users to manually move number blocks between scale pans while showing live total sum gauges.
- **DP Solver Trigger:** Animates the DP solver placing blocks into scale pans to achieve exact equilibrium.

---

## 12. Dry Run

### Sample Input: `arr = [1, 5, 11, 5]`
- Total Sum = $1 + 5 + 11 + 5 = 22$ (Even $\rightarrow$ Valid)
- Target = $22 / 2 = 11$

### Step-by-Step 1D DP Evolution:

| Step / Item | Num | $j=0$ | $j=1$ | $j=5$ | $j=6$ | $j=10$ | $j=11$ | Action / Reached Target Sums |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---|
| **Init** | - | T | F | F | F | F | F | `dp[0] = T` |
| **Item 0** | 1 | T | **T** | F | F | F | F | Reached $j=1$ |
| **Item 1** | 5 | T | T | **T** | **T** | F | F | Reached $j=5, 6 (1+5)$ |
| **Item 2** | 11 | T | T | T | T | F | **T** | Reached $j=11$ |
| **Item 3** | 5 | T | T | T | T | **T** | **T** | Reached $j=10 (5+5), 11 (5+6)$ |

**Final Result:** `dp[11]` = **True** ($S_1 = [1, 5, 5]$, $S_2 = [11]$).

---

## 13. Time & Space Complexity Analysis

| Approach | Time Complexity | Space Complexity | Notes |
|:---|:---:|:---:|:---|
| **Naive Recursion** | $O(2^n)$ | $O(n)$ | Explores all $2^n$ subset combinations |
| **2D Dynamic Programming** | $O(n \cdot \text{target})$ | $O(n \cdot \text{target})$ | Basic matrix approach |
| **1D Space-Optimized DP** | **$O(n \cdot \text{target})$** | **$O(\text{target})$** | Single boolean array |
| **C++ Bitset Acceleration** | **$O(n \cdot \text{target} / 64)$** | **$O(\text{target} / 64)$** | **64x bitwise hardware speedup** |

---

## 14. Advantages

- **Instant Odd-Sum Rejection:** $O(1)$ parity check rejects impossible inputs immediately.
- **Exact Subset Reduction:** Simplifies partition into standard 1D Subset Sum DP.
- **Linear Space Usage:** Requires only $O(\text{target})$ auxiliary space.

---

## 15. Disadvantages

- **NP-Complete in General:** If total sum is extremely large (e.g. $10^{14}$), pseudo-polynomial DP becomes intractable.
- **Requires Integer Elements:** Relies on discrete integer targets (requires scaling for floating-point values).

---

## 16. Variations & Advanced Optimizations

1. **Partition to K Equal Sum Subsets (LeetCode 698):**
   Generalization to $K$ equal sum subsets using Bitmask DP in $O(K \cdot 2^n)$ time.
2. **Minimum Subset Sum Difference (LeetCode 2035):**
   If equal partition is impossible, find partition $S_1, S_2$ that **minimizes** $|\sum S_1 - \sum S_2|$.
3. **Equal Size & Equal Sum Partition:**
   Enforcing $|S_1| = |S_2| = n/2$ in addition to equal sums (solved using 2D state $dp[j][count]$).

---

## 17. Common Mistakes & Pitfalls

- **Forgetting the Odd Parity Check:** Running DP when `totalSum` is odd, wasting CPU cycles before returning false.
- **Confusing Equal Subset Size with Equal Subset Sum:** Assuming $S_1$ and $S_2$ must have the same number of elements (e.g. $S_1 = [1, 5, 5]$ has 3 elements, $S_2 = [11]$ has 1 element, but their sums are equal).
- **Forward Loop Error:** Iterating target loop forward, allowing multiple picks of the same element.

---

## 18. Interview Questions

1. **What is the first check you should execute in Partition Problem?**
   *Answer:* Check if total sum of elements is odd (`totalSum % 2 != 0`). If odd, return `false` immediately.

2. **How does Partition Problem reduce to Subset Sum?**
   *Answer:* Partitioning an array into two equal sum subsets is equivalent to finding a single subset that sums to $\text{target} = \text{TotalSum} / 2$.

3. **Does Partition Problem require both subsets to have the same number of elements?**
   *Answer:* No. The subsets must have equal **sums**, not necessarily equal element counts.

4. **What is the time complexity of Partition Problem DP?**
   *Answer:* $O(n \cdot \text{target})$ where $\text{target} = \text{TotalSum} / 2$.

5. **How can C++ `std::bitset` optimize the Partition Problem?**
   *Answer:* By computing bitwise left shifts `dp |= (dp << num)` across 64 bits simultaneously, speeding up execution by $64\times$.

6. **What is the space complexity of 1D Partition DP?**
   *Answer:* $O(\text{target})$ space.

7. **How do you solve Partition to K Equal Sum Subsets?**
   *Answer:* Check `totalSum % K == 0`, set $\text{target} = \text{totalSum} / K$, and use Bitmask DP with backtracking.

8. **What is the result if the array has only 1 element?**
   *Answer:* `false` (cannot partition 1 element into 2 non-empty subsets).

9. **How would you solve the problem if elements contain floating-point numbers?**
   *Answer:* Multiply all numbers by a common factor to convert them to integers, or use a Greedy/Branch-and-Bound approximation.

10. **Is Partition Problem NP-Complete?**
    *Answer:* Yes, it is one of Karp's 21 NP-complete problems.

---

## 19. Practice Problems

### Easy
1. **LeetCode 416:** [Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/) - Direct implementation.

### Medium
2. **GeeksforGeeks:** [Partition Equal Subset Sum](https://practice.geeksforgeeks.org/) - Standard GFG partition challenge.
3. **LeetCode 698:** [Partition to K Equal Sum Subsets](https://leetcode.com/problems/partition-to-k-equal-sum-subsets/) - Extension to $K$ subsets.

### Hard
4. **LeetCode 2035:** [Partition Array Into Two Arrays to Minimize Sum Difference](https://leetcode.com/problems/partition-array-into-two-arrays-to-minimize-sum-difference/) - Equal size partition with Meet-in-the-Middle.

---

## 20. Related Algorithms

- **Subset Sum Problem:** Direct parent decision problem.
- **0/1 Knapsack Problem:** Core optimization framework.
- **Partition to K Subsets:** Multi-subset bitmask generalization.
- **Meet-in-the-Middle:** Exponent-halving technique for large sum bounds.

---

## 21. Summary

The Partition Problem determines if an array can be split into two equal-sum halves. By verifying even parity and applying 1D Subset Sum DP with target $\text{totalSum} / 2$, DP solves partition verification in **$O(n \cdot \text{target})$ time** and **$O(\text{target})$ space**.

---

## 22. Quiz

**Question 1:** What is the first condition for an array to be partitionable into two equal sum subsets?
- A) Array must be sorted
- B) Total sum of all elements must be even
- C) Array length must be even
- D) All elements must be prime
- **Correct Answer:** B
- **Explanation:** If the total sum is odd, it cannot be divided into two equal integer subset sums.

**Question 2:** What is the target sum for the reduced Subset Sum problem in Partition Problem?
- A) `TotalSum`
- B) `TotalSum / 2`
- C) `TotalSum * 2`
- D) `MaxElement`
- **Correct Answer:** B
- **Explanation:** Finding a subset that sums to `TotalSum / 2` guarantees the remaining elements also sum to `TotalSum / 2`.

**Question 3:** What is the time complexity of Partition Problem DP?
- A) $O(n \log n)$
- B) $O(n \cdot \text{target})$
- C) $O(2^n)$
- D) $O(n^3)$
- **Correct Answer:** B
- **Explanation:** Solved via Subset Sum DP in $O(n \cdot \text{target})$ time where $\text{target} = \text{TotalSum} / 2$.

**Question 4:** Does equal subset sum partition require both subsets to have the same number of elements?
- A) Yes, always
- B) No, only the SUM of elements in both subsets must be equal
- C) Yes, if $n$ is even
- D) Only for prime numbers
- **Correct Answer:** B
- **Explanation:** Subsets must have equal total sums, not equal element counts.

**Question 5:** What is the space complexity of 1D Partition DP?
- A) $O(1)$
- B) $O(\text{target})$
- C) $O(n \cdot \text{target})$
- D) $O(2^n)$
- **Correct Answer:** B
- **Explanation:** Requires a single boolean DP array of size $\text{target} + 1$.

**Question 6:** What is the output for `arr = [1, 2, 5]`?
- A) True
- B) False (Total sum = 8, target = 4, but no subset sums to 4)
- C) True (Subsets [1, 2] and [5])
- D) Error
- **Correct Answer:** B
- **Explanation:** Total sum = 8, target = 4. Subsets can sum to 1, 2, 3, 5, 6, 7, 8, but NOT 4. Output is False.

**Question 7:** What is the output for `arr = [1, 5, 11, 5]`?
- A) True ($S_1 = [1, 5, 5]$ and $S_2 = [11]$)
- B) False
- C) Error
- D) Infinite loop
- **Correct Answer:** A
- **Explanation:** Total sum = 22, target = 11. $1+5+5 = 11$ and $11 = 11$. Output is True.

**Question 8:** Which bitwise operation in C++ accelerates Partition DP by 64x?
- A) `dp ^= (dp >> num)`
- B) `dp |= (dp << num)`
- C) `dp &= (dp << num)`
- D) `dp = ~dp`
- **Correct Answer:** B
- **Explanation:** Bitwise left shift and OR processes 64 boolean states simultaneously per CPU cycle.

**Question 9:** Why must the target sum loop iterate backward in 1D DP?
- A) To sort the output
- B) To ensure each element is used at most once (0/1 constraint)
- C) To reverse the array
- D) To print elements in order
- **Correct Answer:** B
- **Explanation:** Backward iteration prevents updated values from the current item step from being reused.

**Question 10:** What complexity class does the Partition Problem belong to?
- A) P
- B) NP-Complete
- C) EXPTIME
- D) O(1)
- **Correct Answer:** B
- **Explanation:** The Partition Problem is one of Karp's 21 NP-complete problems.
