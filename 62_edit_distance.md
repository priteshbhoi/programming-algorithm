# Edit Distance (Levenshtein Distance - Dynamic Programming)

## 1. Introduction

The **Edit Distance** (commonly known as **Levenshtein Distance**) problem is a fundamental string similarity algorithm introduced by Vladimir Levenshtein in 1965. Given two strings $X$ of length $m$ and $Y$ of length $n$, the goal is to find the **minimum number of single-character operations** required to transform string $X$ into string $Y$.

The three allowed single-character edit operations are:
1. **Insert** a character into string $X$.
2. **Delete** a character from string $X$.
3. **Replace (Substitute)** a character in string $X$ with a character from $Y$.

For example, to transform $X = \text{"horse"}$ into $Y = \text{"ros"}$:
1. `horse` $\rightarrow$ `rorse` (Replace 'h' with 'r')
2. `rorse` $\rightarrow$ `rose` (Delete 'r')
3. `rose` $\rightarrow$ `ros` (Delete 'e')
Total minimum edit operations = **3**.

A naive recursive search explores 3 choices at each character position, taking exponential time $O(3^{\max(m, n)})$. Dynamic Programming (specifically the **Wagner-Fischer Algorithm**) solves Edit Distance in **$O(m \cdot n)$ time** and **$O(m \cdot n)$ space** (optimizable to $O(\min(m, n))$ space).

---

## 2. Why Use This Algorithm?

### Measuring String Distance vs. Exact Matching:
Strings in real-world data often contain typos, misspellings, transcription noise, or genetic mutations. Simple equality checks ($X == Y$) fail completely when a single character differs. Edit Distance provides a quantitative **metric of similarity** between any two strings.

**Benefits:**
- **Guaranteed Minimal Edit Operations:** Finds the absolute smallest number of edits required.
- **Robust Typo Tolerance:** Drives auto-correct and fuzzy search engines across modern software.
- **Flexible Cost Customization:** Supports weighted edit operations (e.g. key distance on a QWERTY keyboard).
- **Polynomial Bound:** Computes similarity predictably in $O(m \cdot n)$ time.

---

## 3. Real-World Applications

- **Spell Checkers & Auto-Correct:** Suggesting nearest dictionary words for misspelled user queries in search engines and word processors.
- **Fuzzy Database Search:** Querying databases (Elasticsearch, Solr) for names or addresses that tolerate minor typographical errors.
- **Computational Biology & Bioinformatics:** Measuring genetic distance between DNA/RNA strands by counting insertion, deletion, and mutation gaps.
- **Speech Recognition (Word Error Rate - WER):** Evaluating speech-to-text model accuracy by comparing predicted transcriptions against ground truth.
- **Optical Character Recognition (OCR):** Correcting misread characters in scanned PDF documents.

---

## 4. Prerequisites

Before studying Edit Distance, you should be comfortable with:
- **Basic String Indexing:** Accessing characters $X[i-1]$ and $Y[j-1]$.
- **2D Dynamic Programming Grids:** Grid state updates and neighbor lookups.
- **Understanding 3-Way Choice Decisions:** Minimum of Insert, Delete, and Replace options.

---

## 5. Visualization

### Edit Operations Grid Transitions

```
               dp[i-1][j-1] (Replace)    dp[i-1][j] (Delete)
                          \                   |
                           \                  |
                            \-----------------> dp[i][j]
                                              /
                                             /
                                 dp[i][j-1] (Insert)
```

### 2D DP Matrix ($dp[i][j]$) for $X = \text{"horse"}$ and $Y = \text{"ros"}$

```
       Ø   r   o   s
   Ø   0   1   2   3
   h   1   1   2   3
   o   2   2   1   2
   r   3   2   2   2
   s   4   3   3   2
   e   5   4   4   3  <-- Min Edit Distance = 3
```

### Mermaid Flowchart: Edit Distance State Choice Decision

```mermaid
graph TD
    Start([Start: String X of length m, String Y of length n]) --> InitDP[Initialize dp matrix of size m+1 x n+1]
    InitDP --> BaseInit["Set base cases: dp[i][0] = i, dp[0][j] = j"]
    BaseInit --> OuterLoop[Loop i from 1 to m]
    OuterLoop --> InnerLoop[Loop j from 1 to n]
    InnerLoop --> CharCheck{"Is X[i-1] == Y[j-1]?"}
    CharCheck -- Yes --> NoOp["dp[i][j] = dp[i-1][j-1] (No Edit Cost)"]
    CharCheck -- No --> MinChoice["dp[i][j] = 1 + min( dp[i][j-1] Insert, dp[i-1][j] Delete, dp[i-1][j-1] Replace )"]
    NoOp --> NextJ{j < n?}
    MinChoice --> NextJ
    NextJ -- Yes --> InnerLoop
    NextJ -- No --> NextI{i < m?}
    NextI -- Yes --> OuterLoop
    NextI -- No --> ReturnResult[Return dp[m][n] and reconstruct operations]
    ReturnResult --> End([End])
```

---

## 6. How It Works

Let $dp[i][j]$ represent the minimum edit distance between prefix $X[0 \dots i-1]$ and prefix $Y[0 \dots j-1]$.

### Base Cases:
- $dp[i][0] = i$ for all $0 \le i \le m$ (Converting $X[0 \dots i-1]$ to an empty string requires $i$ **deletions**).
- $dp[0][j] = j$ for all $0 \le j \le n$ (Converting an empty string to $Y[0 \dots j-1]$ requires $j$ **insertions**).

### State Transition Formula:
For $1 \le i \le m$ and $1 \le j \le n$:

1. **If Characters Match ($X[i-1] == Y[j-1]$):**
   No edit operation is required! The cost equals the edit distance of the preceding prefixes:
   $$dp[i][j] = dp[i-1][j-1]$$

2. **If Characters Mismatch ($X[i-1] \neq Y[j-1]$):**
   We take the minimum cost among the 3 allowed operations plus 1:
   $$dp[i][j] = 1 + \min \begin{cases}
   dp[i][j-1] & \text{(Insert character } Y[j-1] \text{)} \\
   dp[i-1][j] & \text{(Delete character } X[i-1] \text{)} \\
   dp[i-1][j-1] & \text{(Replace } X[i-1] \text{ with } Y[j-1] \text{)}
   \end{cases}$$

---

## 7. Step-by-Step Algorithm

### 1. Computing Min Edit Distance:
1. Create a 2D array `dp` of size $(m+1) \times (n+1)$.
2. Initialize base cases:
   - For $i$ from $0$ to $m$: `dp[i][0] = i`
   - For $j$ from $0$ to $n$: `dp[0][j] = j`
3. Outer loop `i` from $1$ to $m$:
   - Inner loop `j` from $1$ to $n$:
     - If $X[i-1] == Y[j-1]$:
       - `dp[i][j] = dp[i-1][j-1]`
     - Else:
       - `dp[i][j] = 1 + min(dp[i][j-1], dp[i-1][j], dp[i-1][j-1])`
4. Return `dp[m][n]`.

### 2. Reconstructing Edit Operations:
1. Start at $i = m, j = n$.
2. While $i > 0$ or $j > 0$:
   - If $i > 0$ and $j > 0$ and $X[i-1] == Y[j-1]$:
     - Move to $i-1, j-1$ (No operation).
   - Else if $j > 0$ and $dp[i][j] == 1 + dp[i][j-1]$:
     - Record: `"Insert " + Y[j-1]`, move to $i, j-1$.
   - Else if $i > 0$ and $dp[i][j] == 1 + dp[i-1][j]$:
     - Record: `"Delete " + X[i-1]`, move to $i-1, j$.
   - Else:
     - Record: `"Replace " + X[i-1] + " with " + Y[j-1]`, move to $i-1, j-1$.

---

## 8. Pseudocode

```text
function minDistance(X, Y):
    m = length(X)
    n = length(Y)
    create 2D array dp[m + 1][n + 1]

    for i from 0 to m:
        dp[i][0] = i
    for j from 0 to n:
        dp[0][j] = j

    for i from 1 to m:
        for j from 1 to n:
            if X[i - 1] == Y[j - 1]:
                dp[i][j] = dp[i - 1][j - 1]
            else:
                dp[i][j] = 1 + min(dp[i][j - 1],    // Insert
                                   dp[i - 1][j],    // Delete
                                   dp[i - 1][j - 1] // Replace
                                  )

    return dp[m][n]
```

---

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int min3(int a, int b, int c) {
    int min = a;
    if (b < min) min = b;
    if (c < min) min = c;
    return min;
}

int minDistance(char* X, char* Y) {
    int m = strlen(X);
    int n = strlen(Y);

    int** dp = (int**)malloc((m + 1) * sizeof(int*));
    for (int i = 0; i <= m; i++) {
        dp[i] = (int*)malloc((n + 1) * sizeof(int));
    }

    for (int i = 0; i <= m; i++) dp[i][0] = i;
    for (int j = 0; j <= n; j++) dp[0][j] = j;

    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (X[i - 1] == Y[j - 1]) {
                dp[i][j] = dp[i - 1][j - 1];
            } else {
                dp[i][j] = 1 + min3(dp[i][j - 1],    // Insert
                                    dp[i - 1][j],    // Delete
                                    dp[i - 1][j - 1] // Replace
                                   );
            }
        }
    }

    int dist = dp[m][n];

    // Reconstruct operations
    printf("Min Edit Distance: %d\n", dist);
    printf("Edit Operations:\n");

    int i = m, j = n;
    while (i > 0 || j > 0) {
        if (i > 0 && j > 0 && X[i - 1] == Y[j - 1]) {
            i--; j--;
        } else if (j > 0 && dp[i][j] == 1 + dp[i][j - 1]) {
            printf(" - Insert '%c'\n", Y[j - 1]);
            j--;
        } else if (i > 0 && dp[i][j] == 1 + dp[i - 1][j]) {
            printf(" - Delete '%c'\n", X[i - 1]);
            i--;
        } else if (i > 0 && j > 0 && dp[i][j] == 1 + dp[i - 1][j - 1]) {
            printf(" - Replace '%c' with '%c'\n", X[i - 1], Y[j - 1]);
            i--; j--;
        }
    }

    for (int k = 0; k <= m; k++) free(dp[k]);
    free(dp);
    return dist;
}

int main() {
    char X[] = "horse";
    char Y[] = "ros";

    minDistance(X, Y);
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

using namespace std;

class EditDistance {
public:
    static int minDistance(const string& X, const string& Y) {
        int m = X.length();
        int n = Y.length();

        vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));

        for (int i = 0; i <= m; ++i) dp[i][0] = i;
        for (int j = 0; j <= n; ++j) dp[0][j] = j;

        for (int i = 1; i <= m; ++i) {
            for (int j = 1; j <= n; ++j) {
                if (X[i - 1] == Y[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else {
                    dp[i][j] = 1 + min({dp[i][j - 1],    // Insert
                                        dp[i - 1][j],    // Delete
                                        dp[i - 1][j - 1] // Replace
                                       });
                }
            }
        }

        cout << "Min Edit Distance: " << dp[m][n] << endl;
        return dp[m][n];
    }
};

int main() {
    string X = "horse";
    string Y = "ros";

    EditDistance::minDistance(X, Y);
    return 0;
}
```

### Java
```java
import java.util.Arrays;

public class EditDistance {

    public static int minDistance(String X, String Y) {
        int m = X.length();
        int n = Y.length();

        int[][] dp = new int[m + 1][n + 1];

        for (int i = 0; i <= m; i++) dp[i][0] = i;
        for (int j = 0; j <= n; j++) dp[0][j] = j;

        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (X.charAt(i - 1) == Y.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else {
                    dp[i][j] = 1 + Math.min(dp[i][j - 1],     // Insert
                                   Math.min(dp[i - 1][j],     // Delete
                                            dp[i - 1][j - 1]) // Replace
                                  );
                }
            }
        }

        System.out.println("Min Edit Distance: " + dp[m][n]);
        return dp[m][n];
    }

    public static void main(String[] args) {
        String X = "horse";
        String Y = "ros";

        minDistance(X, Y);
    }
}
```

### Python
```python
def min_distance(X: str, Y: str) -> int:
    """Computes minimum Levenshtein Edit Distance."""
    m, n = len(X), len(Y)
    dp = [[0] * (n + 1) for _ in range(m + 1)]

    for i in range(m + 1):
        dp[i][0] = i
    for j in range(n + 1):
        dp[0][j] = j

    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if X[i - 1] == Y[j - 1]:
                dp[i][j] = dp[i - 1][j - 1]
            else:
                dp[i][j] = 1 + min(
                    dp[i][j - 1],    # Insert
                    dp[i - 1][j],    # Delete
                    dp[i - 1][j - 1] # Replace
                )

    print(f"Min Edit Distance: {dp[m][n]}")
    return dp[m][n]

if __name__ == "__main__":
    X = "horse"
    Y = "ros"

    min_distance(X, Y)
```

### JavaScript
```javascript
/**
 * Edit Distance (Levenshtein Distance) DP Implementation
 * @param {string} X 
 * @param {string} Y 
 * @returns {number}
 */
function minDistance(X, Y) {
    const m = X.length;
    const n = Y.length;

    const dp = Array.from({ length: m + 1 }, () => new Array(n + 1).fill(0));

    for (let i = 0; i <= m; i++) dp[i][0] = i;
    for (let j = 0; j <= n; j++) dp[0][j] = j;

    for (let i = 1; i <= m; i++) {
        for (let j = 1; j <= n; j++) {
            if (X[i - 1] === Y[j - 1]) {
                dp[i][j] = dp[i - 1][j - 1];
            } else {
                dp[i][j] = 1 + Math.min(
                    dp[i][j - 1],    // Insert
                    dp[i - 1][j],    // Delete
                    dp[i - 1][j - 1] // Replace
                );
            }
        }
    }

    console.log(`Min Edit Distance: ${dp[m][n]}`);
    return dp[m][n];
}

// Execution and testing
const X = "horse";
const Y = "ros";

minDistance(X, Y);
```

---

## 10. Code Explanation

1. **Base Case Initialization:** `dp[i][0] = i` (deleting $i$ characters to reach empty string) and `dp[0][j] = j` (inserting $j$ characters from empty string).
2. **Match Case (`X[i-1] == Y[j-1]`):** If characters match, no operation cost is added; $dp[i][j]$ inherits the diagonal value `dp[i-1][j-1]`.
3. **Mismatch Case (`X[i-1] != Y[j-1]`):** Evaluates 3 possible edits:
   - `dp[i][j-1]` $\rightarrow$ **Insert:** Inserts $Y[j-1]$ into $X$.
   - `dp[i-1][j]` $\rightarrow$ **Delete:** Deletes $X[i-1]$ from $X$.
   - `dp[i-1][j-1]` $\rightarrow$ **Replace:** Replaces $X[i-1]$ with $Y[j-1]$.
4. **Result:** $dp[m][n]$ holds the minimum operations to convert full string $X$ into string $Y$.

---

## 11. Interactive Demo

An interactive Edit Distance Simulator includes:
- **Two Text Editors:** Input source string $X$ and target string $Y$.
- **Operation Step Animator:** Visual text transforming step-by-step with color-coded insertion (green), deletion (red), and substitution (blue) badges.
- **DP Grid Heatmap:** Displays values in real time with direction arrows showing the optimal path from $(0,0)$ to $(m,n)$.

---

## 12. Dry Run

### Sample Input:
- $X = \text{"intention"}$ ($m = 9$)
- $Y = \text{"execution"}$ ($n = 9$)

### Execution Key Steps Matrix Trace:

| $X \backslash Y$ | Ø | e | x | e | c | u | t | i | o | n |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **Ø** | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 |
| **i** | 1 | 1 | 2 | 3 | 4 | 5 | 6 | 6 | 7 | 8 |
| **n** | 2 | 2 | 2 | 3 | 4 | 5 | 6 | 7 | 7 | 7 |
| **t** | 3 | 3 | 3 | 3 | 4 | 5 | 5 | 6 | 7 | 8 |
| **e** | 4 | 3 | 4 | 3 | 4 | 5 | 6 | 6 | 7 | 8 |
| **n** | 5 | 4 | 4 | 4 | 4 | 5 | 6 | 7 | 7 | 7 |
| **t** | 6 | 5 | 5 | 5 | 5 | 5 | 5 | 6 | 7 | 8 |
| **i** | 7 | 6 | 6 | 6 | 6 | 6 | 6 | 5 | 6 | 7 |
| **o** | 8 | 7 | 7 | 7 | 7 | 7 | 7 | 6 | 5 | 6 |
| **n** | 9 | 8 | 8 | 8 | 8 | 8 | 8 | 7 | 6 | **5** |

**Final Result:** $dp[9][9] =$ **5** edit operations:
1. `intention` $\rightarrow$ `inention` (Delete 't')
2. `inention` $\rightarrow$ `enention` (Replace 'i' with 'e')
3. `enention` $\rightarrow$ `exention` (Replace 'n' with 'x')
4. `exention` $\rightarrow$ `execution` (Replace 'n' with 'c')
5. `execution` $\rightarrow$ `execution` (Insert 'u' before 't')

---

## 13. Time & Space Complexity Analysis

| Approach | Time Complexity | Space Complexity | Notes |
|:---|:---:|:---:|:---|
| **Naive Recursion** | $O(3^{\max(m, n)})$ | $O(\max(m, n))$ | Explores 3 branches at each position |
| **Standard 2D DP** | **$O(m \cdot n)$** | **$O(m \cdot n)$** | Basic 2D grid DP |
| **1D Space-Optimized DP** | **$O(m \cdot n)$** | **$O(\min(m, n))$** | Solves using single 1D array + diagonal temp |
| **Ukkonen's Cutoff DP** | **$O(k \cdot \min(m, n))$** | $O(k)$ | Optimized for threshold distance $\le k$ |

---

## 14. Advantages

- **Guarantees Minimal Edits:** Finds exact minimum operations across all edit sequences.
- **Handles All 3 Basic Edits:** Seamlessly integrates Insertions, Deletions, and Substitutions.
- **Space-Optimized Execution:** Can run in $O(\min(m, n))$ auxiliary space.

---

## 15. Disadvantages

- **Quadratic Complexity:** $O(m \cdot n)$ time can be slow for massive strings (e.g. genome sequences of $10^6$ characters).
- **Does Not Support Transposition natively:** Swapping two adjacent characters (e.g. `teh` $\to$ `the`) counts as 2 operations (Replace + Replace) rather than 1 transposition.

---

## 16. Variations & Advanced Optimizations

1. **Damerau-Levenshtein Distance:** Adds a 4th operation: **Transposition** of two adjacent characters ($X[i-1]X[i-2] \leftrightarrow Y[j-2]Y[j-1]$).
2. **Weighted Edit Distance:** Custom operation costs (e.g. Keyboard distance cost: replacing 'a' with 's' costs less than replacing 'a' with 'p').
3. **Ukkonen's Algorithm:** Evaluates only a diagonal band of width $2k+1$ around the main matrix diagonal when checking if edit distance is $\le k$, reducing time to $O(k \cdot \min(m, n))$.

---

## 17. Common Mistakes & Pitfalls

- **Uninitialized Base Cases:** Forgetting `dp[i][0] = i` or `dp[0][j] = j`, leading to wrong costs.
- **Confusing Matrix Directions:** Swapping Insert ($dp[i][j-1]$) and Delete ($dp[i-1][j]$) coordinates during reconstruction.
- **Using 32-bit Integer Overflow:** In weighted variants with large cost values.

---

## 18. Interview Questions

1. **What are the 3 operations allowed in standard Levenshtein Edit Distance?**
   *Answer:* Insertion, Deletion, and Substitution (Replacement).

2. **What is the state transition when characters match ($X[i-1] == Y[j-1]$)?**
   *Answer:* $dp[i][j] = dp[i-1][j-1]$ (0 additional cost).

3. **How do you optimize Edit Distance space to $O(\min(m, n))$?**
   *Answer:* Use two DP rows (or a single 1D array with a temporary variable to hold the top-left diagonal value).

4. **What is the difference between Levenshtein Distance and Damerau-Levenshtein Distance?**
   *Answer:* Damerau-Levenshtein adds a 4th operation allowing transposition of two adjacent characters.

5. **How does Ukkonen's Algorithm optimize Edit Distance for fuzzy search?**
   *Answer:* If we only care whether distance is $\le k$, Ukkonen's evaluates a diagonal band of width $2k+1$, reducing time complexity to $O(k \cdot \min(m, n))$.

6. **What is the Edit Distance between string $S$ and an empty string ""?**
   *Answer:* Length of $S$ (requires $|S|$ deletion operations).

7. **How does Edit Distance relate to Longest Common Subsequence (LCS)?**
   *Answer:* If only Insert and Delete operations are allowed (Substitution cost = $\infty$), $\text{EditDistance}(X, Y) = m + n - 2 \cdot \text{LCS}(X, Y)$.

8. **What is Hamming Distance, and how does it differ from Edit Distance?**
   *Answer:* Hamming Distance only permits Substitutions and requires both strings to have equal length. Edit Distance allows Insertions and Deletions on arbitrary lengths.

9. **What are the base case values for $dp[0][j]$?**
   *Answer:* $dp[0][j] = j$.

10. **How can custom keyboard layouts be factored into Edit Distance?**
    *Answer:* Use a weighted replacement cost matrix based on physical key distance (e.g. QWERTY key distances).

---

## 19. Practice Problems

### Easy
1. **LeetCode 72:** [Edit Distance](https://leetcode.com/problems/edit-distance/) - Standard Levenshtein Distance implementation.
2. **LeetCode 161:** [One Edit Distance](https://leetcode.com/problems/one-edit-distance/) - Check if strings are exactly 1 edit apart.

### Medium
3. **LeetCode 583:** [Delete Operation for Two Strings](https://leetcode.com/problems/delete-operation-for-two-strings/) - Edit distance with Insert/Delete operations only.
4. **LeetCode 712:** [Minimum ASCII Delete Sum for Two Strings](https://leetcode.com/problems/minimum-ascii-delete-sum-for-two-strings/) - Weighted deletion variant.

### Hard
5. **LeetCode 44:** [Wildcard Matching](https://leetcode.com/problems/wildcard-matching/) - Pattern matching DP closely related to edit distance grids.

---

## 20. Related Algorithms

- **Longest Common Subsequence (LCS):** Base model for insert/delete edit operations.
- **Damerau-Levenshtein Distance:** Adds adjacent transposition edits.
- **Hamming Distance:** Substitution-only edit distance for equal length strings.
- **Wagner-Fischer Algorithm:** The standard matrix DP implementation for Edit Distance.

---

## 21. Summary

The Edit Distance algorithm is the benchmark metric for string similarity. By evaluating 3-way choices (Insert, Delete, Replace) with recurrence $1 + \min(dp[i][j-1], dp[i-1][j], dp[i-1][j-1])$, DP solves string transformation in **$O(m \cdot n)$ time** and **$O(\min(m, n))$ space**.

---

## 22. Quiz

**Question 1:** What are the three basic operations allowed in Levenshtein Distance?
- A) Insert, Delete, Transpose
- B) Insert, Delete, Replace (Substitute)
- C) Append, Prepend, Shift
- D) Rotate, Mirror, Split
- **Correct Answer:** B
- **Explanation:** Standard Levenshtein Distance permits Insertion, Deletion, and Substitution.

**Question 2:** What is the state transition when characters match ($X[i-1] == Y[j-1]$)?
- A) $dp[i][j] = 1 + dp[i-1][j-1]$
- B) $dp[i][j] = dp[i-1][j-1]$
- C) $dp[i][j] = 0$
- D) $dp[i][j] = \min(dp[i-1][j], dp[i][j-1])$
- **Correct Answer:** B
- **Explanation:** Matching characters incur 0 edit cost, inheriting the previous diagonal value.

**Question 3:** What matrix cell in 2D DP corresponds to the INSERTION operation?
- A) Top cell $dp[i-1][j]$
- B) Left cell $dp[i][j-1]$
- C) Top-Left diagonal cell $dp[i-1][j-1]$
- D) Bottom cell $dp[i+1][j]$
- **Correct Answer:** B
- **Explanation:** $dp[i][j-1]$ represents inserting character $Y[j-1]$.

**Question 4:** What is the time complexity of standard 2D DP Edit Distance for strings of length $m$ and $n$?
- A) $O(m + n)$
- B) $O(m \log n)$
- C) $O(m \cdot n)$
- D) $O(3^{\max(m, n)})$
- **Correct Answer:** C
- **Explanation:** Fills a matrix of size $(m+1) \times (n+1)$, taking $O(m \cdot n)$ time.

**Question 5:** What is the Edit Distance between "cat" and "cut"?
- A) 0
- B) 1
- C) 2
- D) 3
- **Correct Answer:** B
- **Explanation:** Requires 1 substitution (replace 'a' with 'u').

**Question 6:** What is the base case value for $dp[i][0]$?
- A) 0
- B) $i$
- C) $-1$
- D) Infinity
- **Correct Answer:** B
- **Explanation:** Converting a string of length $i$ to an empty string requires $i$ deletions.

**Question 7:** How does Damerau-Levenshtein Distance extend standard Levenshtein Distance?
- A) Allows case-insensitive matching
- B) Adds a 4th operation: Transposition of two adjacent characters
- C) Reduces time to $O(1)$
- D) Removes deletion operations
- **Correct Answer:** B
- **Explanation:** Damerau-Levenshtein allows adjacent character swaps as a single edit step.

**Question 8:** If only Insert and Delete operations are allowed (Replace disabled), how is Edit Distance calculated from LCS?
- A) $m + n - 2 \cdot \text{LCS}$
- B) $m + n + \text{LCS}$
- C) $m \cdot n - \text{LCS}$
- D) $\text{LCS} / 2$
- **Correct Answer:** A
- **Explanation:** Deleting non-LCS characters from $X$ and inserting missing characters from $Y$ takes $(m - \text{LCS}) + (n - \text{LCS}) = m + n - 2 \cdot \text{LCS}$ steps.

**Question 9:** What is the space complexity of 1D space-optimized Edit Distance?
- A) $O(1)$
- B) $O(\min(m, n))$
- C) $O(m \cdot n)$
- D) $O(m^2)$
- **Correct Answer:** B
- **Explanation:** Storing 2 rows of size $\min(m, n) + 1$ reduces space to $O(\min(m, n))$.

**Question 10:** What algorithm optimizes Edit Distance to $O(k \cdot \min(m, n))$ for threshold limit $k$?
- A) Dijkstra's Algorithm
- B) Ukkonen's Cutoff Algorithm
- C) KMP Algorithm
- D) Floyd-Warshall Algorithm
- **Correct Answer:** B
- **Explanation:** Ukkonen's algorithm evaluates only a diagonal band of width $2k+1$, reducing time to $O(k \cdot \min(m, n))$.
