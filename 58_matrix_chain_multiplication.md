# Matrix Chain Multiplication (Dynamic Programming)

## 1. Introduction

**Matrix Chain Multiplication (MCM)** is a classic optimization problem solved using **Interval Dynamic Programming**. Given a sequence (chain) of $n$ matrices $A_1, A_2, \dots, A_n$, where matrix $A_i$ has dimensions $p[i-1] \times p[i]$, the goal is to find the optimal parenthesization (order of multiplication) that minimizes the total number of scalar multiplications required to compute the product $A_1 \cdot A_2 \cdots A_n$.

Matrix multiplication is **associative**, meaning $(A \cdot B) \cdot C = A \cdot (B \cdot C)$. However, the order in which matrices are multiplied dramatically impacts the number of scalar multiplication operations performed. 

The number of distinct parenthesizations of a chain of $n$ matrices is given by the **Catalan Number**:
$$P(n) = \frac{1}{n} \binom{2n-2}{n-1} = \Omega\left(\frac{4^n}{n^{3/2}}\right)$$

Evaluating all parenthesizations using naive recursion takes exponential time $O(4^n / n^{3/2})$. Dynamic Programming solves MCM in **$O(n^3)$ time** and **$O(n^2)$ space** by breaking the problem into subsegment/interval subproblems.

---

## 2. Why Use This Algorithm?

### Dramatic Impact of Parenthesization:
Consider three matrices $A_1 (10 \times 100)$, $A_2 (100 \times 5)$, and $A_3 (5 \times 50)$:
- **Option 1: $(A_1 \cdot A_2) \cdot A_3$**
  - $A_1 \cdot A_2$ takes $10 \times 100 \times 5 = 5,000$ scalar multiplications (resulting in a $10 \times 5$ matrix).
  - $(A_1 A_2) \cdot A_3$ takes $10 \times 5 \times 50 = 2,500$ scalar multiplications.
  - **Total Operations:** $5,000 + 2,500 = \mathbf{7,500}$.

- **Option 2: $A_1 \cdot (A_2 \cdot A_3)$**
  - $A_2 \cdot A_3$ takes $100 \times 5 \times 50 = 25,000$ scalar multiplications (resulting in a $100 \times 50$ matrix).
  - $A_1 \cdot (A_2 A_3)$ takes $10 \times 100 \times 50 = 50,000$ scalar multiplications.
  - **Total Operations:** $25,000 + 50,000 = \mathbf{75,000}$.

Option 1 is **10 times faster** than Option 2! For chains of 10 or more matrices in machine learning pipelines, optimal parenthesization can speed up computation by orders of magnitude.

**Benefits:**
- **Guaranteed Minimum Computation:** Finds the exact mathematical global minimum scalar operations.
- **Polynomial Bound:** Reduces complexity from exponential $O(4^n / n^{3/2})$ down to $O(n^3)$.
- **Exact Expression Reconstruction:** Generates the exact parenthesized string (e.g. `((A1 (A2 A3)) A4)`).

---

## 3. Real-World Applications

- **Deep Learning & Tensor Networks:** Optimizing tensor contraction orders in PyTorch, TensorFlow, and JAX for multi-layer neural network execution.
- **3D Computer Graphics Pipelines:** Parenthesizing long chains of transformation matrices (scaling, rotation, translation, projection matrices) before applying them to millions of vertices.
- **Database Query Optimization:** Ordering multi-table Join operations in SQL query engines to minimize intermediate tuple generation.
- **Quantum Circuit Simulation:** Contraction of tensor networks representing quantum circuits in quantum computing simulators.

---

## 4. Prerequisites

Before studying Matrix Chain Multiplication, you should be comfortable with:
- **Matrix Multiplication Basics:** If $A$ is $p \times q$ and $B$ is $q \times r$, multiplying $A \cdot B$ yields a $p \times r$ matrix requiring $p \cdot q \cdot r$ scalar multiplications.
- **2D Dynamic Programming:** Grid manipulation and state lookup tables.
- **Interval/Subsegment DP:** Iterating over subsegment lengths $L = 2 \dots n$.

---

## 5. Visualization

### Splitting Chain $A_i \dots A_j$ at Split Point $k$ ($i \le k < j$)

```
                  Chain A_i ... A_j (Subproblem m[i][j])
                               /     \
                              /       \
                     Subchain A_i..A_k   Subchain A_{k+1}..A_j
                          (m[i][k])          (m[k+1][j])

Cost = m[i][k] + m[k+1][j] + (p[i-1] * p[k] * p[j])
```

### Mermaid Flowchart: Interval DP State Loops

```mermaid
graph TD
    Start([Start: Dimensions array p of length n+1]) --> InitDP[Initialize m[n+1][n+1] = 0, s[n+1][n+1] = 0]
    InitDP --> OuterLoop[Loop L from 2 to n - Chain Length]
    OuterLoop --> InnerLoop[Loop i from 1 to n - L + 1 - Start Index]
    InnerLoop --> CalcJ[Set j = i + L - 1 - End Index]
    CalcJ --> SetInf["Set m[i][j] = INFINITY"]
    SetInf --> SplitLoop[Loop k from i to j - 1 - Split Point]
    SplitLoop --> ComputeCost["cost = m[i][k] + m[k+1][j] + p[i-1]*p[k]*p[j]"]
    ComputeCost --> CheckMin{Is cost < m[i][j]?}
    CheckMin -- Yes --> UpdateMin["m[i][j] = cost, s[i][j] = k"]
    CheckMin -- No --> NextK{k < j - 1?}
    UpdateMin --> NextK
    NextK -- Yes --> SplitLoop
    NextK -- No --> NextI{i <= n - L + 1?}
    NextI -- Yes --> InnerLoop
    NextI -- No --> NextL{L <= n?}
    NextL -- Yes --> OuterLoop
    NextL -- No --> ReturnResult[Return m[1][n] and print parenthesization via s matrix]
    ReturnResult --> End([End])
```

---

## 6. How It Works

Let $m[i][j]$ represent the minimum number of scalar multiplications needed to compute the matrix product $A_i A_{i+1} \dots A_j$ (where $1 \le i \le j \le n$).

### Base Cases:
- $m[i][i] = 0$ for all $1 \le i \le n$ (a single matrix requires 0 multiplications).

### State Transition (Recurrence Relation):
For a chain $A_i \dots A_j$, we consider making a split at position $k$ where $i \le k < j$:
- Left subchain product $A_i \dots A_k$ has dimensions $p[i-1] \times p[k]$, cost $m[i][k]$.
- Right subchain product $A_{k+1} \dots A_j$ has dimensions $p[k] \times p[j]$, cost $m[k+1][j]$.
- Multiplying the resulting two matrices takes $p[i-1] \cdot p[k] \cdot p[j]$ scalar multiplications.

Therefore:
$$m[i][j] = \min_{i \le k < j} \left( m[i][k] + m[k+1][j] + p[i-1] \cdot p[k] \cdot p[j] \right)$$

We also maintain a split table $s[i][j] = k$ recording the optimal split point $k$ that achieved the minimum cost for $m[i][j]$.

---

## 7. Step-by-Step Algorithm

1. Input: Dimensions array $p[]$ of size $n+1$ (where $n$ is the number of matrices).
2. Create 2D DP array $m[n+1][n+1]$ initialized to 0.
3. Create 2D split array $s[n+1][n+1]$ initialized to 0.
4. Outer Loop $L$ from $2$ to $n$ (chain length):
   - Loop $i$ from $1$ to $n - L + 1$ (start index):
     - Set $j = i + L - 1$ (end index).
     - Set $m[i][j] = \infty$.
     - Loop $k$ from $i$ to $j - 1$ (split point):
       - $q = m[i][k] + m[k+1][j] + p[i-1] \cdot p[k] \cdot p[j]$.
       - If $q < m[i][j]$:
         - Update $m[i][j] = q$.
         - Update $s[i][j] = k$.
5. Return $m[1][n]$ as the minimum scalar multiplication cost.
6. Reconstruct parenthesization by recursively calling `printParenthesis(s, i, j)`.

---

## 8. Pseudocode

```text
function matrixChainOrder(p, n):
    create 2D array m[n + 1][n + 1] initialized to 0
    create 2D array s[n + 1][n + 1] initialized to 0

    for L from 2 to n:
        for i from 1 to n - L + 1:
            j = i + L - 1
            m[i][j] = INFINITY
            for k from i to j - 1:
                q = m[i][k] + m[k + 1][j] + p[i - 1] * p[k] * p[j]
                if q < m[i][j]:
                    m[i][j] = q
                    s[i][j] = k

    return m, s

function printOptimalParenthesis(s, i, j):
    if i == j:
        print "A" + i
    else:
        print "("
        printOptimalParenthesis(s, i, s[i][j])
        printOptimalParenthesis(s, s[i][j] + 1, j)
        print ")"
```

---

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>
#include <limits.h>

void printParenthesis(int** s, int i, int j) {
    if (i == j) {
        printf("A%d", i);
    } else {
        printf("(");
        printParenthesis(s, i, s[i][j]);
        printParenthesis(s, s[i][j] + 1, j);
        printf(")");
    }
}

int matrixChainOrder(int p[], int n) {
    int** m = (int**)malloc((n + 1) * sizeof(int*));
    int** s = (int**)malloc((n + 1) * sizeof(int*));
    for (int i = 0; i <= n; i++) {
        m[i] = (int*)calloc(n + 1, sizeof(int));
        s[i] = (int*)calloc(n + 1, sizeof(int));
    }

    for (int L = 2; L <= n; L++) {
        for (int i = 1; i <= n - L + 1; i++) {
            int j = i + L - 1;
            m[i][j] = INT_MAX;
            for (int k = i; k <= j - 1; k++) {
                int q = m[i][k] + m[k + 1][j] + p[i - 1] * p[k] * p[j];
                if (q < m[i][j]) {
                    m[i][j] = q;
                    s[i][j] = k;
                }
            }
        }
    }

    int minCost = m[1][n];
    printf("Minimum Scalar Multiplications: %d\n", minCost);
    printf("Optimal Parenthesization: ");
    printParenthesis(s, 1, n);
    printf("\n");

    for (int i = 0; i <= n; i++) {
        free(m[i]);
        free(s[i]);
    }
    free(m);
    free(s);

    return minCost;
}

int main() {
    // 4 matrices: A1 (10x30), A2 (30x5), A3 (5x60), A4 (60x8)
    int p[] = {10, 30, 5, 60, 8};
    int n = 4;

    matrixChainOrder(p, n);
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
#include <climits>

using namespace std;

class MatrixChainMultiplication {
public:
    static void printParenthesis(const vector<vector<int>>& s, int i, int j) {
        if (i == j) {
            cout << "A" << i;
        } else {
            cout << "(";
            printParenthesis(s, i, s[i][j]);
            printParenthesis(s, s[i][j] + 1, j);
            cout << ")";
        }
    }

    static int solve(const vector<int>& p) {
        int n = p.size() - 1;
        vector<vector<int>> m(n + 1, vector<int>(n + 1, 0));
        vector<vector<int>> s(n + 1, vector<int>(n + 1, 0));

        for (int L = 2; L <= n; ++L) {
            for (int i = 1; i <= n - L + 1; ++i) {
                int j = i + L - 1;
                m[i][j] = INT_MAX;
                for (int k = i; k <= j - 1; ++k) {
                    int q = m[i][k] + m[k + 1][j] + p[i - 1] * p[k] * p[j];
                    if (q < m[i][j]) {
                        m[i][j] = q;
                        s[i][j] = k;
                    }
                }
            }
        }

        cout << "Minimum Scalar Multiplications: " << m[1][n] << endl;
        cout << "Optimal Parenthesization: ";
        printParenthesis(s, 1, n);
        cout << endl;

        return m[1][n];
    }
};

int main() {
    vector<int> p = {10, 30, 5, 60, 8};
    MatrixChainMultiplication::solve(p);
    return 0;
}
```

### Java
```java
import java.util.Arrays;

public class MatrixChainMultiplication {

    private static void printParenthesis(int[][] s, int i, int j) {
        if (i == j) {
            System.out.print("A" + i);
        } else {
            System.out.print("(");
            printParenthesis(s, i, s[i][j]);
            printParenthesis(s, s[i][j] + 1, j);
            System.out.print(")");
        }
    }

    public static int solve(int[] p) {
        int n = p.length - 1;
        int[][] m = new int[n + 1][n + 1];
        int[][] s = new int[n + 1][n + 1];

        for (int L = 2; L <= n; L++) {
            for (int i = 1; i <= n - L + 1; i++) {
                int j = i + L - 1;
                m[i][j] = Integer.MAX_VALUE;
                for (int k = i; k <= j - 1; k++) {
                    int q = m[i][k] + m[k + 1][j] + p[i - 1] * p[k] * p[j];
                    if (q < m[i][j]) {
                        m[i][j] = q;
                        s[i][j] = k;
                    }
                }
            }
        }

        System.out.println("Minimum Scalar Multiplications: " + m[1][n]);
        System.out.print("Optimal Parenthesization: ");
        printParenthesis(s, 1, n);
        System.out.println();

        return m[1][n];
    }

    public static void main(String[] args) {
        int[] p = {10, 30, 5, 60, 8};
        solve(p);
    }
}
```

### Python
```python
import sys

def print_parenthesis(s: list[list[int]], i: int, j: int) -> str:
    """Recursively reconstructs the parenthesized matrix string."""
    if i == j:
        return f"A{i}"
    else:
        left = print_parenthesis(s, i, s[i][j])
        right = print_parenthesis(s, s[i][j] + 1, j)
        return f"({left}{right})"

def matrix_chain_order(p: list[int]) -> tuple[int, str]:
    """Computes minimum multiplications and optimal parenthesization."""
    n = len(p) - 1
    m = [[0] * (n + 1) for _ in range(n + 1)]
    s = [[0] * (n + 1) for _ in range(n + 1)]

    for L in range(2, n + 1):  # L is chain length
        for i in range(1, n - L + 2):
            j = i + L - 1
            m[i][j] = sys.maxsize
            for k in range(i, j):
                q = m[i][k] + m[k + 1][j] + p[i - 1] * p[k] * p[j]
                if q < m[i][j]:
                    m[i][j] = q
                    s[i][j] = k

    expression = print_parenthesis(s, 1, n)
    print(f"Minimum Scalar Multiplications: {m[1][n]}")
    print(f"Optimal Parenthesization: {expression}")
    return m[1][n], expression

if __name__ == "__main__":
    p = [10, 30, 5, 60, 8]
    matrix_chain_order(p)
```

### JavaScript
```javascript
/**
 * Matrix Chain Multiplication DP with Parenthesization Reconstruction
 * @param {number[]} p Dimensions array
 */
function matrixChainOrder(p) {
    const n = p.length - 1;
    const m = Array.from({ length: n + 1 }, () => new Array(n + 1).fill(0));
    const s = Array.from({ length: n + 1 }, () => new Array(n + 1).fill(0));

    function printParenthesis(i, j) {
        if (i === j) {
            return `A${i}`;
        } else {
            const left = printParenthesis(i, s[i][j]);
            const right = printParenthesis(s[i][j] + 1, j);
            return `(${left}${right})`;
        }
    }

    for (let L = 2; L <= n; L++) {
        for (let i = 1; i <= n - L + 1; i++) {
            const j = i + L - 1;
            m[i][j] = Infinity;
            for (let k = i; k <= j - 1; k++) {
                const q = m[i][k] + m[k + 1][j] + p[i - 1] * p[k] * p[j];
                if (q < m[i][j]) {
                    m[i][j] = q;
                    s[i][j] = k;
                }
            }
        }
    }

    const expression = printParenthesis(1, n);
    console.log(`Minimum Scalar Multiplications: ${m[1][n]}`);
    console.log(`Optimal Parenthesization: ${expression}`);

    return m[1][n];
}

// Execution and testing
const p = [10, 30, 5, 60, 8];
matrixChainOrder(p);
```

---

## 10. Code Explanation

1. **Outer Loop `L` (Chain Length):** Iterates over subsegment lengths from $L = 2$ up to $n$. Subproblems must be solved in order of increasing length so that smaller subsegment values $m[i][k]$ and $m[k+1][j]$ are already computed when solving length $L$.
2. **Inner Loop `i` (Start Index):** Determines the starting matrix index of the subchain.
3. **End Index `j = i + L - 1`:** Calculates the end matrix index.
4. **Split Point Loop `k` ($i \le k < j$):** Tests every possible splitting position $k$ between $i$ and $j-1$.
5. **Cost Formula `p[i-1] * p[k] * p[j]`:** Represents the cost of multiplying the resulting $(p[i-1] \times p[k])$ matrix by the $(p[k] \times p[j])$ matrix.

---

## 11. Interactive Demo

An interactive MCM Visualizer includes:
- **Matrix Dimension Builder:** Controls to add matrices and adjust dimensions dynamically.
- **Parenthesization Syntax Tree:** Real-time tree view displaying the hierarchical parenthesization brackets.
- **DP Grid Stepper:** Interactive upper-triangular matrix view highlighting subsegment calculations step-by-step.

---

## 12. Dry Run

### Sample Input:
- Matrix Dimensions: $p = [10, 20, 30, 40, 30]$ ($n = 4$ matrices)
  - $A_1: 10 \times 20$
  - $A_2: 20 \times 30$
  - $A_3: 30 \times 40$
  - $A_4: 40 \times 30$

### Execution Steps by Chain Length $L$:

#### Length $L = 2$ (Chains of 2 matrices):
- $m[1][2] = p[0] \cdot p[1] \cdot p[2] = 10 \times 20 \times 30 = \mathbf{6,000}, \; s[1][2] = 1$
- $m[2][3] = p[1] \cdot p[2] \cdot p[3] = 20 \times 30 \times 40 = \mathbf{24,000}, \; s[2][3] = 2$
- $m[3][4] = p[2] \cdot p[3] \cdot p[4] = 30 \times 40 \times 30 = \mathbf{36,000}, \; s[3][4] = 3$

#### Length $L = 3$ (Chains of 3 matrices):
- $m[1][3]$:
  - $k=1: m[1][1] + m[2][3] + p[0]p[1]p[3] = 0 + 24,000 + (10 \times 20 \times 40) = 32,000$
  - $k=2: m[1][2] + m[3][3] + p[0]p[2]p[3] = 6,000 + 0 + (10 \times 30 \times 40) = \mathbf{18,000}$
  - **Min:** $m[1][3] = 18,000, \; s[1][3] = 2$

- $m[2][4]$:
  - $k=2: m[2][2] + m[3][4] + p[1]p[2]p[4] = 0 + 36,000 + (20 \times 30 \times 30) = 54,000$
  - $k=3: m[2][3] + m[4][4] + p[1]p[3]p[4] = 24,000 + 0 + (20 \times 40 \times 30) = \mathbf{48,000}$
  - **Min:** $m[2][4] = 48,000, \; s[2][4] = 3$

#### Length $L = 4$ (Full Chain $A_1 \dots A_4$):
- $m[1][4]$:
  - $k=1: m[1][1] + m[2][4] + p[0]p[1]p[4] = 0 + 48,000 + (10 \times 20 \times 30) = 54,000$
  - $k=2: m[1][2] + m[3][4] + p[0]p[2]p[4] = 6,000 + 36,000 + (10 \times 30 \times 30) = \mathbf{51,000}$
  - $k=3: m[1][3] + m[4][4] + p[0]p[3]p[4] = 18,000 + 0 + (10 \times 40 \times 30) = 30,000 + 18,000 = 30,000 \dots$ wait, $18,000 + 12,000 = 30,000$.
  - **Min Cost:** **30,000** ($k=3$), Parenthesization: `((A1 (A2 A3)) A4)`

---

## 13. Time & Space Complexity Analysis

| Stage | Complexity | Explanation |
|:---|:---:|:---|
| **Naive Recursion** | $O(4^n / n^{3/2})$ | Explores all Catalan number combinations |
| **DP Time Complexity** | **$O(n^3)$** | 3 nested loops: $L$ ($1 \dots n$), $i$ ($1 \dots n$), $k$ ($i \dots j$) |
| **DP Space Complexity** | **$O(n^2)$** | 2D matrix $m[n+1][n+1]$ and $s[n+1][n+1]$ |

---

## 14. Advantages

- **Guaranteed Optimal Parenthesization:** Finds the absolute minimum scalar operations required.
- **Huge Computational Savings:** Can speed up heavy matrix pipeline execution by $10\times$ to $100\times$.
- **Exact Tree Reconstruction:** Provides the explicit bracketed expression for evaluation engines.

---

## 15. Disadvantages

- **Cubic Time Complexity:** $O(n^3)$ execution time becomes noticeable for $n > 500$ matrices.
- **Requires Known Dimensions:** Matrix dimensions must be fixed before execution starts.

---

## 16. Variations & Advanced Optimizations

1. **Knuth-Yao Optimization:** If cost functions satisfy the quadrangle inequality, the split search bound can be restricted to $s[i][j-1] \le s[i][j] \le s[i+1][j]$, reducing time complexity to **$O(n^2)$**.
2. **Hu & Shing Algorithm:** Converts Matrix Chain Multiplication into a polygon triangulation problem, solving it in **$O(n \log n)$ time**.

---

## 17. Common Mistakes & Pitfalls

- **Incorrect Loop Order:** Iterating $i$ before interval length $L$. Subproblems of smaller lengths MUST be computed first.
- **Index Out-of-Bounds on $p$ array:** Accessing $p[i]$ instead of $p[i-1]$ for matrix $A_i$ rows.
- **Integer Overflow:** $p[i-1] \cdot p[k] \cdot p[j]$ can overflow standard 32-bit integers for large dimensions. Use 64-bit integers (`long long` / `long`).

---

## 18. Interview Questions

1. **Why does Matrix Chain Multiplication require interval loop order ($L=2 \dots n$) instead of standard row-by-row iteration?**
   *Answer:* Computing $m[i][j]$ requires subproblems $m[i][k]$ and $m[k+1][j]$, which have shorter chain lengths. Iterating by chain length $L$ guarantees smaller subsegments are solved first.

2. **What mathematical sequence counts the number of parenthesizations for $n$ matrices?**
   *Answer:* The **Catalan Numbers**, $P(n) = \frac{1}{n} \binom{2n-2}{n-1}$.

3. **Does Matrix Chain Multiplication actually multiply the matrices?**
   *Answer:* No. It determines the optimal order (parenthesization) of multiplication to minimize scalar operations before actual multiplication occurs.

4. **What are the dimensions of matrix $A_i$ given dimension array $p$?**
   *Answer:* Matrix $A_i$ has dimensions $p[i-1] \times p[i]$.

5. **How does MCM relate to Polygon Triangulation?**
   *Answer:* Finding an optimal matrix parenthesization is isomorphic to finding a minimum-weight triangulation of an $(n+1)$-sided convex polygon.

6. **What is the space complexity of MCM?**
   *Answer:* $O(n^2)$ space for storing the $m$ cost table and $s$ split table.

7. **How does Knuth-Yao optimization improve MCM?**
   *Answer:* By narrowing the split search range using $s[i][j-1] \le s[i][j] \le s[i+1][j]$, reducing time complexity from $O(n^3)$ to $O(n^2)$.

8. **What is Hu & Shing's algorithm time complexity for MCM?**
   *Answer:* $O(n \log n)$ time.

9. **Why is the base case $m[i][i] = 0$?**
   *Answer:* A single matrix $A_i$ requires zero multiplications to be evaluated.

10. **If all matrices are square $N \times N$, does parenthesization order matter?**
    *Answer:* For $n$ square matrices of identical size $N \times N$, every parenthesization yields the exact same total cost $(n-1)N^3$.

---

## 19. Practice Problems

### Easy
1. **GeeksforGeeks:** [Matrix Chain Multiplication](https://practice.geeksforgeeks.org/) - Standard MCM implementation.

### Medium
2. **LeetCode 312:** [Burst Balloons](https://leetcode.com/problems/burst-balloons/) - Interval DP isomorphic to MCM.
3. **LeetCode 1000:** [Minimum Cost to Merge Stones](https://leetcode.com/problems/minimum-cost-to-merge-stones/) - Multi-element interval contraction DP.

### Hard
4. **LeetCode 1547:** [Minimum Cost to Cut a Stick](https://leetcode.com/problems/minimum-cost-to-cut-a-stick/) - Interval DP cut optimization.

---

## 20. Related Algorithms

- **Optimal Binary Search Tree (OBST):** Interval DP for search cost minimization.
- **Polygon Triangulation:** Minimum-weight geometric decomposition.
- **Burst Balloons DP:** Reverse interval contraction DP.

---

## 21. Summary

Matrix Chain Multiplication demonstrates **Interval Dynamic Programming**. By defining state $m[i][j]$ over subsegment lengths $L = 2 \dots n$ and recording optimal split points $k$, MCM reduces an exponential $O(4^n / n^{3/2})$ parenthesization problem down to **$O(n^3)$ time** and **$O(n^2)$ space**.

---

## 22. Quiz

**Question 1:** What is the main objective of Matrix Chain Multiplication?
- A) Multiply matrices in $O(1)$ time
- B) Find the parenthesization order that minimizes total scalar multiplications
- C) Invert a chain of matrices
- D) Compute eigenvalues of a matrix chain
- **Correct Answer:** B
- **Explanation:** MCM finds the optimal bracket ordering to minimize scalar multiplication operations.

**Question 2:** What mathematical series describes the number of ways to parenthesize $n$ matrices?
- A) Fibonacci numbers
- B) Catalan numbers
- C) Prime numbers
- D) Pascal triangle numbers
- **Correct Answer:** B
- **Explanation:** The number of parenthesizations is given by the $(n-1)$-th Catalan number.

**Question 3:** What is the time complexity of standard MCM DP?
- A) $O(n)$
- B) $O(n \log n)$
- C) $O(n^2)$
- D) $O(n^3)$
- **Correct Answer:** D
- **Explanation:** Standard MCM uses 3 nested loops (length $L$, start $i$, split $k$), resulting in $O(n^3)$ time.

**Question 4:** What is the space complexity of MCM DP?
- A) $O(1)$
- B) $O(n)$
- C) $O(n^2)$
- D) $O(n^3)$
- **Correct Answer:** C
- **Explanation:** Stores 2D cost table $m$ and split table $s$ of size $(n+1) \times (n+1)$.

**Question 5:** Given $A_1 (10 \times 30)$ and $A_2 (30 \times 5)$, how many scalar multiplications are needed to compute $A_1 \cdot A_2$?
- A) 150
- B) 300
- C) 1,500
- D) 15,000
- **Correct Answer:** C
- **Explanation:** Cost = $10 \times 30 \times 5 = 1,500$.

**Question 6:** What loop order MUST be used in MCM DP?
- A) Outer loop over start index $i$
- B) Outer loop over chain length $L$ from $2$ to $n$
- C) Outer loop over split point $k$
- D) Any loop order works
- **Correct Answer:** B
- **Explanation:** Subproblems must be solved in increasing order of chain length $L$ so smaller subsegment values are ready when needed.

**Question 7:** What is the base case for single matrix subchains $m[i][i]$?
- A) 0
- B) 1
- C) Infinity
- D) $p[i]$
- **Correct Answer:** A
- **Explanation:** A single matrix requires 0 multiplications.

**Question 8:** Which advanced optimization reduces MCM time complexity to $O(n \log n)$?
- A) Strassen Multiplication
- B) Hu & Shing Algorithm
- C) Fast Fourier Transform
- D) Binary Search Tree
- **Correct Answer:** B
- **Explanation:** Hu & Shing maps MCM to polygon triangulation, achieving $O(n \log n)$ time.

**Question 9:** What does `s[i][j]` store in the DP implementation?
- A) Maximum value
- B) Optimal split index $k$ that minimizes cost for chain $A_i \dots A_j$
- C) Matrix dimension
- D) Number of rows
- **Correct Answer:** B
- **Explanation:** `s[i][j]` stores the optimal split point $k$ used to reconstruct parenthesization.

**Question 10:** If all matrices in the chain are identical square matrices $N \times N$, does parenthesization change the total multiplication cost?
- A) Yes, parenthesization makes it 10x faster
- B) No, all parenthesizations yield the exact same cost $(n-1)N^3$
- C) Yes, it causes integer overflow
- D) It depends on matrix rank
- **Correct Answer:** B
- **Explanation:** For identical square matrices $N \times N$, every matrix product step takes $N^3$ scalar multiplications, so all parenthesizations cost $(n-1)N^3$.
