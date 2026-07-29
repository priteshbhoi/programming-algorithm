# Subset Sum Problem (Dynamic Programming)

## 1. Introduction

The **Subset Sum Problem** is a foundational decision problem in computer science, complexity theory, and cryptography. Given a set/array of non-negative integers `arr[]` of size $n$ and a target integer `sum`, the goal is to determine whether there exists a non-empty subset of `arr` whose elements add up to exactly `sum`.

Mathematically:
$$\text{Find } x_i \in \{0, 1\} \quad \text{such that} \quad \sum_{i=1}^{n} \text{arr}[i-1] \cdot x_i = \text{sum}$$

For example, for `arr = [3, 34, 4, 12, 5, 2]` and `sum = 9`:
- The subset `[4, 5]` sums to $4 + 5 = 9$.
- The subset `[3, 4, 2]` sums to $3 + 4 + 2 = 9$.
- Output: **True** (Subset exists).

Brute-force subset evaluation tests all $2^n$ subsets in exponential time $O(2^n)$. Dynamic Programming solves the Subset Sum Problem in **pseudo-polynomial time $O(n \cdot \text{sum})$** and **$O(\text{sum})$ space**. Modern C++ implementations can further accelerate this using `std::bitset` by a factor of $64\times$.

The Subset Sum Problem is a special decision case of the **0/1 Knapsack Problem** where each item's weight equals its value ($w_i = v_i = \text{arr}[i]$) and capacity $W = \text{sum}$.

---

## 2. Why Use This Algorithm?

### Pseudo-Polynomial vs Exponential Complexity:
Searching through $2^n$ subsets for $n = 40$ requires $2^{40} \approx 1.1 \times 10^{12}$ calculations. Dynamic Programming reduces this to $O(n \cdot \text{sum})$. For $n = 40$ and $\text{sum} = 1000$, DP executes in only $40,000$ operations—taking under a millisecond.

**Benefits:**
- **Guaranteed Exact Result:** Proves definitively whether a valid subset exists.
- **Ultra-Low Memory Footprint:** Requires only a single boolean array of size $\text{sum} + 1$ ($O(\text{sum})$ space).
- **Bitwise Parallelism:** Can be optimized using bitset shifts (`dp |= (dp << arr[i])`) to process 64 states per CPU instruction.
- **Foundation for Financial Auditing:** Used extensively in invoice matching and ledger reconciliation.

---

## 3. Real-World Applications

- **Financial Ledger & Invoice Reconciliation:** Matching an unaccounted bank deposit/discrepancy against combinations of outstanding client invoices.
- **Merkle-Hellman Knapsack Cryptosystem:** Public-key cryptosystem based on the hardness of superincreasing subset sum problems.
- **Resource & Server Load Balancing:** Partitioning server tasks into two balanced computing clusters with equal workload sums.
- **Retail Gift Card & Coupon Combination:** Checking if a combination of store coupons or gift card balances covers an exact purchase total.
- **Cargo Container Weight Optimization:** Verifying if a set of shipping pallets can exactly fill a transport vehicle's payload limit.

---

## 4. Prerequisites

Before studying the Subset Sum Problem, you should be comfortable with:
- **Boolean Logic & Bitwise Operations.**
- **0/1 Knapsack DP Principles:** Include vs. Exclude decision choices.
- **1D Array Tabulation with Reverse Looping.**

---

## 5. Visualization

### State Transition Choice for Element `x` at Target `j`

```
                          SubsetSum(i, j)
                          /            \
                         /              \
            Exclude arr[i-1]           Include arr[i-1] (if arr[i-1] <= j)
                 /                                \
                /                                  \
        SubsetSum(i-1, j)                SubsetSum(i-1, j - arr[i-1])

dp[i][j] = dp[i-1][j] OR dp[i-1][j - arr[i-1]]
```

### Mermaid Flowchart: 1D Space-Optimized Subset Sum

```mermaid
graph TD
    Start([Start: arr[], target sum]) --> InitDP[Initialize boolean dp array of size sum + 1 with false]
    InitDP --> SetBase[dp[0] = true]
    SetBase --> OuterLoop[Loop num in arr]
    OuterLoop --> InnerLoop[Loop j from sum down to num]
    InnerLoop --> StateUpdate["dp[j] = dp[j] OR dp[j - num]"]
    StateUpdate --> NextJ{j >= num?}
    NextJ -- Yes --> InnerLoop
    NextJ -- No --> NextNum{More nums?}
    NextNum -- Yes --> OuterLoop
    NextNum -- No --> ReturnResult[Return dp[sum]]
    ReturnResult --> End([End])
```

---

## 6. How It Works

Let $dp[j]$ be a boolean value indicating whether a subset with sum $j$ can be formed using the elements processed so far.

### Base Case:
- $dp[0] = \text{true}$ (Sum 0 can always be formed using the empty subset $\emptyset$).
- $dp[j] = \text{false}$ for all $j > 0$.

### State Transition:
For each element $x$ in `arr`:
Iterate target sum $j$ **backward** from $\text{sum}$ down to $x$:
$$dp[j] = dp[j] \;\lor\; dp[j - x]$$

> **Why Backward Iteration?**
> Iterating $j$ in reverse ensures that $dp[j - x]$ represents the boolean state from the **previous item step**, preventing the current element $x$ from being used multiple times (which would solve Unbounded Subset Sum instead of 0/1 Subset Sum).

---

## 7. Step-by-Step Algorithm

### 1D Space-Optimized Algorithm:
1. Input: Array `arr[]` of size $n$, target `sum`.
2. Create a boolean array `dp` of size `sum + 1`, initialized to `false`.
3. Set base case: `dp[0] = true`.
4. Outer loop: For each `num` in `arr`:
   - Inner loop: For `j` from `sum` down to `num`:
     - `dp[j] = dp[j] || dp[j - num]`
5. Return `dp[sum]`.

### Subset Reconstruction (Extracting actual numbers):
1. Use a 2D boolean array `dp[n+1][sum+1]`.
2. After filling the matrix, if `dp[n][sum]` is true:
   - Start at $i = n, j = \text{sum}$.
   - While $i > 0$ and $j > 0$:
     - If `dp[i-1][j]` is true, element $arr[i-1]$ was **not** included. Move $i = i - 1$.
     - Else, element $arr[i-1]$ **was** included. Add $arr[i-1]$ to result subset, update $j = j - arr[i-1]$, and move $i = i - 1$.

---

## 8. Pseudocode

### 1D Space-Optimized Pseudocode
```text
function isSubsetSum(arr, n, sum):
    create boolean array dp of size (sum + 1) filled with false
    dp[0] = true

    for i from 0 to n - 1:
        for j from sum down to arr[i]:
            if dp[j - arr[i]] == true:
                dp[j] = true

    return dp[sum]
```

### C++ Bitset Optimized Pseudocode ($64\times$ Speedup)
```text
function isSubsetSumBitset(arr, n, sum):
    create bitset dp of size (sum + 1)
    dp[0] = 1 // bit 0 set to 1

    for i from 0 to n - 1:
        dp = dp | (dp << arr[i])

    return dp[sum]
```

---

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>

// 1D Space-Optimized Subset Sum O(n * sum) Time, O(sum) Space
bool isSubsetSum(int arr[], int n, int sum) {
    bool* dp = (bool*)calloc(sum + 1, sizeof(bool));
    dp[0] = true;

    for (int i = 0; i < n; i++) {
        for (int j = sum; j >= arr[i]; j--) {
            if (dp[j - arr[i]]) {
                dp[j] = true;
            }
        }
    }

    bool result = dp[sum];
    free(dp);
    return result;
}

// 2D DP with Subset Reconstruction
void printSubsetSum(int arr[], int n, int sum) {
    bool** dp = (bool**)malloc((n + 1) * sizeof(bool*));
    for (int i = 0; i <= n; i++) {
        dp[i] = (bool*)calloc(sum + 1, sizeof(bool));
    }

    for (int i = 0; i <= n; i++) dp[i][0] = true;

    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= sum; j++) {
            if (arr[i - 1] <= j) {
                dp[i][j] = dp[i - 1][j] || dp[i - 1][j - arr[i - 1]];
            } else {
                dp[i][j] = dp[i - 1][j];
            }
        }
    }

    if (!dp[n][sum]) {
        printf("No subset sums to %d\n", sum);
    } else {
        printf("Subset summing to %d found: [ ", sum);
        int j = sum;
        for (int i = n; i > 0 && j > 0; i--) {
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
    int arr[] = {3, 34, 4, 12, 5, 2};
    int n = sizeof(arr) / sizeof(arr[0]);
    int sum = 9;

    printf("Is Subset Sum Possible: %s\n", isSubsetSum(arr, n, sum) ? "Yes" : "No");
    printSubsetSum(arr, n, sum);
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
#include <bitset>

using namespace std;

class SubsetSum {
public:
    // 1D Space-Optimized O(n * sum) Time, O(sum) Space
    static bool isSubsetSum(const vector<int>& arr, int sum) {
        vector<bool> dp(sum + 1, false);
        dp[0] = true;

        for (int num : arr) {
            for (int j = sum; j >= num; --j) {
                if (dp[j - num]) {
                    dp[j] = true;
                }
            }
        }
        return dp[sum];
    }

    // Ultra-Fast std::bitset Acceleration (64x speedup)
    static bool isSubsetSumBitset(const vector<int>& arr, int sum) {
        bitset<100001> dp;
        dp[0] = 1;

        for (int num : arr) {
            dp |= (dp << num);
        }
        return dp[sum];
    }
};

int main() {
    vector<int> arr = {3, 34, 4, 12, 5, 2};
    int sum = 9;

    cout << "Is Subset Sum Possible (1D DP): " << (SubsetSum::isSubsetSum(arr, sum) ? "Yes" : "No") << endl;
    cout << "Is Subset Sum Possible (Bitset): " << (SubsetSum::isSubsetSumBitset(arr, sum) ? "Yes" : "No") << endl;

    return 0;
}
```

### Java
```java
public class SubsetSum {

    // 1D Space-Optimized DP
    public static boolean isSubsetSum(int[] arr, int sum) {
        boolean[] dp = new boolean[sum + 1];
        dp[0] = true;

        for (int num : arr) {
            for (int j = sum; j >= num; j--) {
                if (dp[j - num]) {
                    dp[j] = true;
                }
            }
        }
        return dp[sum];
    }

    public static void main(String[] args) {
        int[] arr = {3, 34, 4, 12, 5, 2};
        int sum = 9;

        System.out.println("Is Subset Sum Possible: " + (isSubsetSum(arr, sum) ? "Yes" : "No"));
    }
}
```

### Python
```python
def is_subset_sum(arr: list[int], target_sum: int) -> bool:
    """1D Space-Optimized Subset Sum DP."""
    dp = [False] * (target_sum + 1)
    dp[0] = True

    for num in arr:
        for j in range(target_sum, num - 1, -1):
            if dp[j - num]:
                dp[j] = True

    return dp[target_sum]

def get_subset_sum_elements(arr: list[int], target_sum: int) -> tuple[bool, list[int]]:
    """2D DP approach with exact subset element extraction."""
    n = len(arr)
    dp = [[False] * (target_sum + 1) for _ in range(n + 1)]

    for i in range(n + 1):
        dp[i][0] = True

    for i in range(1, n + 1):
        for j in range(1, target_sum + 1):
            if arr[i - 1] <= j:
                dp[i][j] = dp[i - 1][j] or dp[i - 1][j - arr[i - 1]]
            else:
                dp[i][j] = dp[i - 1][j]

    if not dp[n][target_sum]:
        return False, []

    subset = []
    j = target_sum
    for i in range(n, 0, -1):
        if not dp[i - 1][j]:
            subset.append(arr[i - 1])
            j -= arr[i - 1]

    print(f"Subset summing to {target_sum}: {subset}")
    return True, subset

if __name__ == "__main__":
    arr = [3, 34, 4, 12, 5, 2]
    target_sum = 9

    print(f"Is Subset Sum Possible: {is_subset_sum(arr, target_sum)}")
    get_subset_sum_elements(arr, target_sum)
```

### JavaScript
```javascript
/**
 * 1D Space-Optimized Subset Sum DP
 * @param {number[]} arr 
 * @param {number} sum 
 * @returns {boolean}
 */
function isSubsetSum(arr, sum) {
    const dp = new Array(sum + 1).fill(false);
    dp[0] = true;

    for (const num of arr) {
        for (let j = sum; j >= num; j--) {
            if (dp[j - num]) {
                dp[j] = true;
            }
        }
    }

    return dp[sum];
}

// Execution and testing
const arr = [3, 34, 4, 12, 5, 2];
const sum = 9;

console.log("Is Subset Sum Possible:", isSubsetSum(arr, sum) ? "Yes" : "No");
```

---

## 10. Code Explanation

1. **Base Case `dp[0] = true`:** Sum 0 is always formable using an empty subset.
2. **Reverse Capacity Loop (`for j from sum down to num`):** Guarantees that each array element `num` is used at most once in forming target sum $j$.
3. **State Transition `dp[j] = dp[j] || dp[j - num]`:** Sum $j$ becomes reachable if it was already reachable OR if sum $j - \text{num}$ was previously reachable.
4. **C++ `std::bitset` Speedup:** Performs 64 state bitwise OR shifts simultaneously in a single CPU clock cycle (`dp |= (dp << num)`).

---

## 11. Interactive Demo

An interactive Subset Sum visualizer features:
- **Number Cards Configurator:** Add custom numbers to the input array.
- **Target Sum Dial:** Adjust target sum `S`.
- **Boolean Grid Matrix Animator:** Displays DP table cells turning green as sums become reachable.
- **Bitset Shift Animation:** Demonstrates how bitwise left-shifts (`dp << num`) compute all reachable sums in parallel.

---

## 12. Dry Run

### Sample Input:
- `arr = [3, 4, 5]`
- Target `sum = 9`

### Step-by-Step 1D DP Array Trace:

| Step / Item | Element | $j=0$ | $j=1$ | $j=2$ | $j=3$ | $j=4$ | $j=5$ | $j=6$ | $j=7$ | $j=8$ | $j=9$ | Action / Newly Reached Sums |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---|
| **Init** | - | T | F | F | F | F | F | F | F | F | F | `dp[0] = T` |
| **Item 0** | 3 | T | F | F | **T** | F | F | F | F | F | F | Reached $j=3$ |
| **Item 1** | 4 | T | F | F | T | **T** | F | F | **T** | F | F | Reached $j=4, 7$ ($3+4$) |
| **Item 2** | 5 | T | F | F | T | T | **T** | F | T | **T** | **T** | Reached $j=5, 8 (3+5), 9 (4+5)$ |

**Final Result:** `dp[9]` = **True** (formed by subset `[4, 5]`).

---

## 13. Time & Space Complexity Analysis

| Approach | Time Complexity | Space Complexity | Notes |
|:---|:---:|:---:|:---|
| **Naive Recursion** | $O(2^n)$ | $O(n)$ | Explores all $2^n$ subsets |
| **2D Dynamic Programming** | $O(n \cdot \text{sum})$ | $O(n \cdot \text{sum})$ | Basic DP table approach |
| **1D Space-Optimized DP** | **$O(n \cdot \text{sum})$** | **$O(\text{sum})$** | Single boolean array |
| **C++ Bitset Optimization** | **$O(n \cdot \text{sum} / 64)$** | **$O(\text{sum} / 64)$** | **64x hardware bitwise speedup** |
| **Meet-in-the-Middle** | $O(2^{n/2} \cdot n)$ | $O(2^{n/2})$ | Optimal for large $\text{sum}$, small $n \le 40$ |

---

## 14. Advantages

- **Exact Verification:** Guarantees proving whether a valid subset sum exists.
- **Linear Auxiliary Memory:** Solvable in $O(\text{sum})$ space.
- **Hardware-Accelerated Bitset:** Bitset bit-shift optimization executes $64\times$ faster than standard loops.

---

## 15. Disadvantages

- **NP-Complete in General:** If target `sum` is massive (e.g. $\text{sum} = 2^{64}$), pseudo-polynomial DP fails.
- **Requires Non-Negative Integers:** Standard array indexing requires non-negative integers (requires offset transformation for negative numbers).

---

## 16. Variations & Advanced Optimizations

1. **Partition Equal Subset Sum (LeetCode 416):**
   Check if total array sum is even; then solve Subset Sum with target $\text{sum} = \text{TotalSum} / 2$.
2. **Target Sum (LeetCode 494):**
   Assigning `+` or `-` to array elements converts to subset sum with target $P = (\text{TotalSum} + \text{Target}) / 2$.
3. **Meet-in-the-Middle (for large $\text{sum}$, $n \le 40$):**
   Splits array into two halves of size $n/2$, computes all subset sums for both, sorts one, and binary searches in $O(2^{n/2} \cdot n)$ time.

---

## 17. Common Mistakes & Pitfalls

- **Iterating Target Loop Forward ($0 \to \text{sum}$):** Forward iteration allows the same element to be added multiple times, converting 0/1 Subset Sum into Unbounded Subset Sum.
- **Odd Total Sum in Partition Equal Subset Sum:** Failing to check if total array sum is odd before attempting division by 2.
- **Negative Numbers in Array:** Passing negative numbers directly into non-offset DP array indices, causing out-of-bounds crash.

---

## 18. Interview Questions

1. **How is the Subset Sum Problem related to the 0/1 Knapsack Problem?**
   *Answer:* Subset Sum is a special case of 0/1 Knapsack where each element's weight equals its value ($w_i = v_i = \text{arr}[i]$) and target sum equals knapsack capacity $W$.

2. **Why must the target loop run backward in 1D space-optimized Subset Sum DP?**
   *Answer:* Running backward ensures $dp[j - \text{num}]$ reads boolean state from the previous item iteration, preventing an element from being reused multiple times.

3. **How does C++ `std::bitset` optimize Subset Sum?**
   *Answer:* By representing the DP array as a bitset, `dp |= (dp << num)` shifts and ORs 64 boolean states simultaneously per CPU cycle, speeding up execution by $64\times$.

4. **What approach is optimal if $n = 40$ but target $\text{sum} = 10^{14}$?**
   *Answer:* **Meet-in-the-Middle**. Divide array into two sets of 20, compute subset sums, sort one set, and binary search in $O(2^{n/2} \cdot n)$ time.

5. **How do you reduce Partition Equal Subset Sum to Subset Sum?**
   *Answer:* If total array sum is odd, return `false`. Otherwise, run Subset Sum with target $\text{sum} = \text{TotalSum} / 2$.

6. **What is the space complexity of 1D Subset Sum DP?**
   *Answer:* $O(\text{sum})$ space.

7. **Why is Subset Sum called Pseudo-Polynomial?**
   *Answer:* Because runtime $O(n \cdot \text{sum})$ depends on the numeric magnitude of `sum`, which is exponential relative to the number of bits needed to represent `sum`.

8. **What is the base case of Subset Sum?**
   *Answer:* `dp[0] = true` (Sum 0 can always be formed using the empty subset).

9. **Can Subset Sum handle negative numbers?**
   *Answer:* Standard DP indexing requires non-negative integers. Negative numbers require mapping states via hash maps or offset indexing.

10. **What is the time complexity of naive recursive subset sum search?**
    *Answer:* $O(2^n)$.

---

## 19. Practice Problems

### Easy
1. **GeeksforGeeks:** [Subset Sum Problem](https://practice.geeksforgeeks.org/) - Standard DP formulation.

### Medium
2. **LeetCode 416:** [Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/) - Exact half-sum subset partition.
3. **LeetCode 494:** [Target Sum](https://leetcode.com/problems/target-sum/) - Subset sum variation with sign assignments.
4. **LeetCode 698:** [Partition to K Equal Sum Subsets](https://leetcode.com/problems/partition-to-k-equal-sum-subsets/) - Multi-subset DP with bitmask state.

### Hard
5. **LeetCode 2035:** [Partition Array Into Two Arrays to Minimize Sum Difference](https://leetcode.com/problems/partition-array-into-two-arrays-to-minimize-sum-difference/) - Meet-in-the-Middle algorithm on split arrays.

---

## 20. Related Algorithms

- **0/1 Knapsack Problem:** Parent general optimization problem.
- **Partition Equal Subset Sum:** Exact half-sum division problem.
- **Target Sum:** Sign assignment DP.
- **Meet-in-the-Middle:** Exponent-halving technique for subset sum on small $n$.

---

## 21. Summary

The Subset Sum Problem is a classic decision problem solved using 1D Dynamic Programming. By iterating target sums backward and applying recurrence $dp[j] = dp[j] \lor dp[j - \text{num}]$, DP proves subset existence in **$O(n \cdot \text{sum})$ time** and **$O(\text{sum})$ space**, further accelerated by **$64\times$ using C++ `std::bitset`**.

---

## 22. Quiz

**Question 1:** What does `dp[j]` store in 1D Subset Sum DP?
- A) The minimum count of numbers needed to make sum $j$
- B) A boolean indicating whether a subset exists that sums to $j$
- C) The product of elements summing to $j$
- D) The number of prime factors
- **Correct Answer:** B
- **Explanation:** $dp[j]$ is a boolean flag recording whether sum $j$ is reachable.

**Question 2:** Why must the inner loop iterate backward ($j = \text{sum} \dots \text{num}$)?
- A) To sort elements in descending order
- B) To ensure each element is used at most once per subset (0/1 constraint)
- C) To print numbers in reverse
- D) To speed up memory allocation
- **Correct Answer:** B
- **Explanation:** Backward iteration prevents newly set values in the current step from being reused, enforcing 0/1 selection.

**Question 3:** What is the time complexity of standard 1D DP Subset Sum?
- A) $O(2^n)$
- B) $O(n \log n)$
- C) $O(n \cdot \text{sum})$
- D) $O(n^2)$
- **Correct Answer:** C
- **Explanation:** Nested loops execute $n \times \text{sum}$ iterations $\rightarrow O(n \cdot \text{sum})$.

**Question 4:** What feature allows C++ `std::bitset` to execute Subset Sum $64\times$ faster?
- A) Multi-threading
- B) Bitwise left-shift (`dp |= (dp << num)`) processing 64 bits per instruction
- C) GPU acceleration
- D) Binary search
- **Correct Answer:** B
- **Explanation:** Bitwise OR and shift operators process 64 boolean states simultaneously on 64-bit CPU registers.

**Question 5:** What is the base case initialization for Subset Sum?
- A) `dp[0] = false`
- B) `dp[0] = true`
- C) `dp[sum] = true`
- D) `dp[1] = true`
- **Correct Answer:** B
- **Explanation:** Base case: Target sum 0 can always be formed using the empty subset.

**Question 6:** How do you transform Partition Equal Subset Sum into Subset Sum?
- A) Set target $\text{sum} = \text{TotalSum} / 2$ (if TotalSum is even)
- B) Multiply all elements by 2
- C) Sort array in ascending order
- D) Reverse array
- **Correct Answer:** A
- **Explanation:** Partitioning into two equal sum halves requires finding a subset summing to $\text{TotalSum} / 2$.

**Question 7:** What is the space complexity of 1D Subset Sum DP?
- A) $O(n \cdot \text{sum})$
- B) $O(\text{sum})$
- C) $O(n)$
- D) $O(1)$
- **Correct Answer:** B
- **Explanation:** Requires a single boolean array of size $\text{sum} + 1$.

**Question 8:** What algorithm is optimal for Subset Sum when $n = 40$ and target $\text{sum} = 10^{12}$?
- A) 1D DP Tabulation
- B) Meet-in-the-Middle ($O(2^{n/2} \cdot n)$)
- C) Fractional Greedy Sort
- D) Linear Search
- **Correct Answer:** B
- **Explanation:** Since $\text{sum}$ is massive, DP fails; Meet-in-the-Middle splits $n=40$ into two sets of 20, taking $2^{20} \times 20 \approx 2 \times 10^7$ operations.

**Question 9:** What happens if you iterate the target sum loop FORWARD ($0 \to \text{sum}$)?
- A) Code crashes
- B) Solves Unbounded Subset Sum (allowing multiple uses of the same number)
- C) Returns false for all inputs
- D) Sorts the array
- **Correct Answer:** B
- **Explanation:** Forward iteration uses updated values from the current step, allowing a number to be selected multiple times.

**Question 10:** What complexity class does general Subset Sum belong to?
- A) P
- B) NP-Complete
- C) EXPTIME
- D) O(1)
- **Correct Answer:** B
- **Explanation:** Subset Sum is one of Karp's 21 NP-complete problems.
