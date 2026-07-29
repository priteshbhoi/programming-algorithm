# Longest Common Subsequence (Dynamic Programming)

## 1. Introduction

The **Longest Common Subsequence (LCS)** problem is a foundational problem in computer science and string processing. Given two sequences (typically strings) $X$ of length $m$ and $Y$ of length $n$, the goal is to find the length of the longest subsequence that is present in both $X$ and $Y$.

A **subsequence** is a sequence derived from another sequence by deleting zero or more elements without changing the order of the remaining elements. Unlike a **substring**, elements in a subsequence do **not** need to be contiguous in the original string.

For example, for strings $X = \text{"AGGTAB"}$ and $Y = \text{"GXTXAYB"}$, the longest common subsequence is $\text{"GTAB"}$, which has a length of **4**.

A naive recursive approach evaluates all $2^m$ subsequences of $X$ against $Y$, leading to exponential time complexity $O(2^m)$. Dynamic Programming solves LCS in **$O(m \cdot n)$ time** and **$O(m \cdot n)$ space** (which can be optimized to $O(\min(m, n))$ space).

---

## 2. Why Use This Algorithm?

### Subsequence vs. Substring Difference:
- **String:** `"ABCDE"`
- **Substrings (Must be contiguous):** `"ABC"`, `"BCD"`, `"CDE"`.
- **Subsequences (Order preserved, non-contiguous allowed):** `"ACE"`, `"BD"`, `"ABE"`.

Comparing sequences sequentially is essential when inputs may contain noise, insertions, or deletions between matching characters.

**Benefits:**
- **Guaranteed Optimal Alignment:** Identifies exact maximum matching subsequence elements.
- **Foundation for Text Comparison:** Drives core file difference utilities (`git diff`, `diff`, `patch`).
- **Essential for Computational Biology:** Used to measure similarity between DNA/RNA sequences.
- **Polynomial Bounds:** Operates in predictable $O(m \cdot n)$ time.

---

## 3. Real-World Applications

- **Version Control Systems (`git diff` & `patch`):** Calculating line-by-line file differences to display additions, deletions, and modifications between code commits.
- **Bioinformatics & Genomics:** Aligning DNA, RNA, and protein sequences (e.g. Needleman-Wunsch and Smith-Waterman algorithms) to identify evolutionary relationships.
- **Plagiarism Detection Engines:** Comparing student code or essays against existing document databases to compute text similarity percentages.
- **Data Deduplication & Record Linkage:** Matching customer names or address records across disparate databases despite minor typographical discrepancies.
- **Speech Recognition:** Aligning spoken acoustic frames with text transcriptions.

---

## 4. Prerequisites

Before learning the Longest Common Subsequence algorithm, you should be familiar with:
- **Basic String Manipulation:** Indexing, character comparison, and string building.
- **2D Dynamic Programming Grids:** Matrix state representations and neighbor lookups.
- **Difference between Subsequence and Substring.**

---

## 5. Visualization

### 2D DP Matrix ($dp[i][j]$) for $X = \text{"AGGTAB"}$ and $Y = \text{"GXTXAYB"}$

```
       Ø   G   X   T   X   A   Y   B
   Ø   0   0   0   0   0   0   0   0
   A   0   0   0   0   0   1   1   1
   G   0   1   1   1   1   1   1   1
   G   0   1   1   1   1   1   1   1
   T   0   1   1   2   2   2   2   2
   A   0   1   1   2   2   3   3   3
   B   0   1   1   2   2   3   3   4

Length of LCS = 4  ("GTAB")
```

### Mermaid Flowchart: LCS State Decision Logic

```mermaid
graph TD
    Start([Start: String X of length m, String Y of length n]) --> InitDP[Initialize dp matrix of size m+1 x n+1 with 0]
    InitDP --> OuterLoop[Loop i from 1 to m]
    OuterLoop --> InnerLoop[Loop j from 1 to n]
    InnerLoop --> CharCheck{"Is X[i-1] == Y[j-1]?"}
    CharCheck -- Yes --> MatchChoice["dp[i][j] = 1 + dp[i-1][j-1]"]
    CharCheck -- No --> MismatchChoice["dp[i][j] = max(dp[i-1][j], dp[i][j-1])"]
    MatchChoice --> NextJ{j < n?}
    MismatchChoice --> NextJ
    NextJ -- Yes --> InnerLoop
    NextJ -- No --> NextI{i < m?}
    NextI -- Yes --> OuterLoop
    NextI -- No --> ReturnResult[Return dp[m][n] and trace back string]
    ReturnResult --> End([End])
```

---

## 6. How It Works

Let $dp[i][j]$ represent the length of the longest common subsequence of prefixes $X[0 \dots i-1]$ and $Y[0 \dots j-1]$.

### Base Cases:
- $dp[0][j] = 0$ for all $0 \le j \le n$ (empty prefix of $X$).
- $dp[i][0] = 0$ for all $0 \le i \le m$ (empty prefix of $Y$).

### State Transition Formula:
For $1 \le i \le m$ and $1 \le j \le n$:

1. **Character Match ($X[i-1] == Y[j-1]$):**
   The matching character extends the LCS of shorter prefixes $X[0 \dots i-2]$ and $Y[0 \dots j-2]$ by $1$:
   $$dp[i][j] = 1 + dp[i-1][j-1]$$

2. **Character Mismatch ($X[i-1] \neq Y[j-1]$):**
   The current characters cannot both be part of the same LCS end. We take the maximum of excluding character $X[i-1]$ or excluding character $Y[j-1]$:
   $$dp[i][j] = \max(dp[i-1][j], \, dp[i][j-1])$$

---

## 7. Step-by-Step Algorithm

### 1. Finding LCS Length:
1. Create a 2D array `dp` of size $(m+1) \times (n+1)$, initialized to $0$.
2. Outer loop `i` from $1$ to $m$:
   - Inner loop `j` from $1$ to $n$:
     - If $X[i-1] == Y[j-1]$:
       - `dp[i][j] = 1 + dp[i-1][j-1]`
     - Else:
       - `dp[i][j] = max(dp[i-1][j], dp[i][j-1])`
3. Return `dp[m][n]`.

### 2. Reconstructing LCS String:
1. Start at $i = m, j = n$.
2. While $i > 0$ and $j > 0$:
   - If $X[i-1] == Y[j-1]$, append $X[i-1]$ to result, and move diagonally up-left ($i = i-1, j = j-1$).
   - Else if $dp[i-1][j] > dp[i][j-1]$, move up ($i = i-1$).
   - Else, move left ($j = j-1$).
3. Reverse the resulting string to get the final LCS string.

---

## 8. Pseudocode

```text
function LCS(X, Y, m, n):
    create 2D array dp[m + 1][n + 1] initialized to 0

    for i from 1 to m:
        for j from 1 to n:
            if X[i - 1] == Y[j - 1]:
                dp[i][j] = 1 + dp[i - 1][j - 1]
            else:
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])

    lcsLength = dp[m][n]
    
    // String Reconstruction
    lcsString = ""
    i = m
    j = n
    while i > 0 and j > 0:
        if X[i - 1] == Y[j - 1]:
            lcsString.append(X[i - 1])
            i = i - 1
            j = j - 1
        else if dp[i - 1][j] > dp[i][j - 1]:
            i = i - 1
        else:
            j = j - 1

    reverse(lcsString)
    return lcsLength, lcsString
```

---

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int max(int a, int b) {
    return (a > b) ? a : b;
}

void lcs(char* X, char* Y) {
    int m = strlen(X);
    int n = strlen(Y);

    int** dp = (int**)malloc((m + 1) * sizeof(int*));
    for (int i = 0; i <= m; i++) {
        dp[i] = (int*)calloc(n + 1, sizeof(int));
    }

    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (X[i - 1] == Y[j - 1]) {
                dp[i][j] = 1 + dp[i - 1][j - 1];
            } else {
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
    }

    int length = dp[m][n];
    printf("LCS Length: %d\n", length);

    // Reconstruction
    char* lcs_str = (char*)malloc((length + 1) * sizeof(char));
    lcs_str[length] = '\0';

    int i = m, j = n, index = length - 1;
    while (i > 0 && j > 0) {
        if (X[i - 1] == Y[j - 1]) {
            lcs_str[index--] = X[i - 1];
            i--;
            j--;
        } else if (dp[i - 1][j] > dp[i][j - 1]) {
            i--;
        } else {
            j--;
        }
    }

    printf("LCS String: %s\n", lcs_str);

    for (int k = 0; k <= m; k++) free(dp[k]);
    free(dp);
    free(lcs_str);
}

int main() {
    char X[] = "AGGTAB";
    char Y[] = "GXTXAYB";

    lcs(X, Y);
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

class LongestCommonSubsequence {
public:
    static void solve(const string& X, const string& Y) {
        int m = X.length();
        int n = Y.length();

        vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));

        for (int i = 1; i <= m; ++i) {
            for (int j = 1; j <= n; ++j) {
                if (X[i - 1] == Y[j - 1]) {
                    dp[i][j] = 1 + dp[i - 1][j - 1];
                } else {
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }

        cout << "LCS Length: " << dp[m][n] << endl;

        // String Reconstruction
        string lcsStr = "";
        int i = m, j = n;
        while (i > 0 && j > 0) {
            if (X[i - 1] == Y[j - 1]) {
                lcsStr.push_back(X[i - 1]);
                i--;
                j--;
            } else if (dp[i - 1][j] > dp[i][j - 1]) {
                i--;
            } else {
                j--;
            }
        }

        reverse(lcsStr.begin(), lcsStr.end());
        cout << "LCS String: " << lcsStr << endl;
    }
};

int main() {
    string X = "AGGTAB";
    string Y = "GXTXAYB";

    LongestCommonSubsequence::solve(X, Y);
    return 0;
}
```

### Java
```java
import java.util.Arrays;

public class LongestCommonSubsequence {

    public static void solve(String X, String Y) {
        int m = X.length();
        int n = Y.length();

        int[][] dp = new int[m + 1][n + 1];

        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (X.charAt(i - 1) == Y.charAt(j - 1)) {
                    dp[i][j] = 1 + dp[i - 1][j - 1];
                } else {
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }

        System.out.println("LCS Length: " + dp[m][n]);

        // String Reconstruction
        StringBuilder sb = new StringBuilder();
        int i = m, j = n;
        while (i > 0 && j > 0) {
            if (X.charAt(i - 1) == Y.charAt(j - 1)) {
                sb.append(X.charAt(i - 1));
                i--;
                j--;
            } else if (dp[i - 1][j] > dp[i][j - 1]) {
                i--;
            } else {
                j--;
            }
        }

        String lcsStr = sb.reverse().toString();
        System.out.println("LCS String: " + lcsStr);
    }

    public static void main(String[] args) {
        String X = "AGGTAB";
        String Y = "GXTXAYB";

        solve(X, Y);
    }
}
```

### Python
```python
def lcs(X: str, Y: str) -> tuple[int, str]:
    """Computes LCS length and reconstructs the LCS string."""
    m, n = len(X), len(Y)
    dp = [[0] * (n + 1) for _ in range(m + 1)]

    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if X[i - 1] == Y[j - 1]:
                dp[i][j] = 1 + dp[i - 1][j - 1]
            else:
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])

    lcs_length = dp[m][n]

    # Reconstruct LCS string
    lcs_chars = []
    i, j = m, n
    while i > 0 and j > 0:
        if X[i - 1] == Y[j - 1]:
            lcs_chars.append(X[i - 1])
            i -= 1
            j -= 1
        elif dp[i - 1][j] > dp[i][j - 1]:
            i -= 1
        else:
            j -= 1

    lcs_str = "".join(reversed(lcs_chars))
    print(f"LCS Length: {lcs_length}")
    print(f"LCS String: {lcs_str}")
    return lcs_length, lcs_str

if __name__ == "__main__":
    X = "AGGTAB"
    Y = "GXTXAYB"

    lcs(X, Y)
```

### JavaScript
```javascript
/**
 * Longest Common Subsequence DP Implementation
 * @param {string} X 
 * @param {string} Y 
 */
function lcs(X, Y) {
    const m = X.length;
    const n = Y.length;

    const dp = Array.from({ length: m + 1 }, () => new Array(n + 1).fill(0));

    for (let i = 1; i <= m; i++) {
        for (let j = 1; j <= n; j++) {
            if (X[i - 1] === Y[j - 1]) {
                dp[i][j] = 1 + dp[i - 1][j - 1];
            } else {
                dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
    }

    const length = dp[m][n];
    console.log(`LCS Length: ${length}`);

    // Reconstruct string
    const chars = [];
    let i = m, j = n;
    while (i > 0 && j > 0) {
        if (X[i - 1] === Y[j - 1]) {
            chars.push(X[i - 1]);
            i--;
            j--;
        } else if (dp[i - 1][j] > dp[i][j - 1]) {
            i--;
        } else {
            j--;
        }
    }

    const lcsStr = chars.reverse().join("");
    console.log(`LCS String: ${lcsStr}`);

    return length;
}

// Execution and testing
const X = "AGGTAB";
const Y = "GXTXAYB";

lcs(X, Y);
```

---

## 10. Code Explanation

1. **2D DP Grid Allocation (`dp[m+1][n+1]`):** `dp[i][j]` holds the LCS length between prefix $X[0 \dots i-1]$ and $Y[0 \dots j-1]$.
2. **Base Case Row & Column ($i=0$ or $j=0$):** Initialized to 0 because an empty string shares no subsequence with any string.
3. **Character Match Logic (`X[i-1] == Y[j-1]`):** Increments the diagonal top-left neighbor value `dp[i-1][j-1]` by 1.
4. **Character Mismatch Logic (`X[i-1] != Y[j-1]`):** Takes the maximum of top neighbor `dp[i-1][j]` and left neighbor `dp[i][j-1]`.
5. **Backtracking String Retrieval:** Moving backwards from `dp[m][n]`, diagonal moves indicate matching characters that belong to the LCS string.

---

## 11. Interactive Demo

An interactive LCS simulator includes:
- **Two Input Text Fields:** Type custom strings $X$ and $Y$.
- **Heatmap DP Matrix:** Visual grid where matching character cells light up green and numbers increment dynamically.
- **Backtracking Animation:** Highlights the path taken from $dp[m][n]$ back to $dp[0][0]$, building the matched LCS string character-by-character.

---

## 12. Dry Run

### Sample Input:
- $X = \text{"ABCD"}$ ($m = 4$)
- $Y = \text{"ACBD"}$ ($n = 4$)

### Step-by-Step 2D Matrix Fill:

| $i \backslash j$ | Ø | A (1) | C (2) | B (3) | D (4) |
|:---:|:---:|:---:|:---:|:---:|:---:|
| **Ø** | 0 | 0 | 0 | 0 | 0 |
| **A (1)** | 0 | **1** (Match) | 1 | 1 | 1 |
| **B (2)** | 0 | 1 | 1 | **2** (Match) | 2 |
| **C (3)** | 0 | 1 | **2** (Match) | 2 | 2 |
| **D (4)** | 0 | 1 | 2 | 2 | **3** (Match) |

### Backtracking Step:
- From `dp[4][4]` (Match 'D'): Pick 'D', move to `dp[3][3]`.
- From `dp[3][3]` (Mismatch): `dp[3][2]` (2) == `dp[2][3]` (2). Move left to `dp[3][2]`.
- From `dp[3][2]` (Match 'C'): Pick 'C', move to `dp[2][1]`.
- From `dp[2][1]` (Mismatch): `dp[1][1]` (1) > `dp[2][0]` (0). Move up to `dp[1][1]`.
- From `dp[1][1]` (Match 'A'): Pick 'A', move to `dp[0][0]`.

**Final Output:** Length = **3**, Reconstructed LCS = **"ACD"** (or **"ABD"**).

---

## 13. Time & Space Complexity Analysis

| Metric | Standard 2D DP | Space-Optimized 2-Row DP | Hirschberg's Algorithm |
|:---|:---:|:---:|:---:|
| **Time Complexity** | **$O(m \cdot n)$** | **$O(m \cdot n)$** | **$O(m \cdot n)$** |
| **Space Complexity** | $O(m \cdot n)$ | **$O(\min(m, n))$** | **$O(\min(m, n))$** |
| **String Reconstruction** | Supported | Length only | **Full string supported** |

> **Hirschberg's Algorithm:** Combines Divide & Conquer with 2-row DP to compute both the LCS length AND reconstruct the full string in $O(m \cdot n)$ time and $O(\min(m, n))$ space.

---

## 14. Advantages

- **Guaranteed Optimal Alignment:** Finds exact maximum matching sequence.
- **Foundation for Diff Tools:** Essential algorithm for source control version tracking.
- **Space-Optimized Variations:** Can run in linear $O(\min(m, n))$ space.

---

## 15. Disadvantages

- **Quadratic Time:** $O(m \cdot n)$ time can be slow for massive DNA sequences ($m, n > 10^5$) without heuristic filtering.
- **Full Matrix Memory:** Storing 2D grid for long strings takes significant memory ($10^4 \times 10^4 = 10^8$ cells) if Hirschberg's algorithm is not used.

---

## 16. Variations & Advanced Optimizations

1. **Space Optimization (2-Row DP):**
   Maintain only `prev` and `curr` rows of size $n+1$, reducing space to $O(n)$.
2. **Hunt-Szymanski Algorithm:**
   Optimizes LCS for sparse matching character pairs (common in `diff` for text files), reducing time to $O((r + n) \log n)$ where $r$ is total matching character pairs.
3. **Longest Common Substring:**
   Requires characters to be contiguous. Transition changes to resetting $dp[i][j] = 0$ on mismatch.

---

## 17. Common Mistakes & Pitfalls

- **Confusing Subsequence with Substring:** Resetting state to 0 on mismatch (that computes Substring length, not Subsequence).
- **Index Alignment Errors:** Mixing 0-based string characters $X[i-1]$ with 1-based DP table indices $dp[i][j]$.
- **Incorrect Backtracking Direction:** Moving in the wrong direction during string reconstruction when top and left neighbors are equal.

---

## 18. Interview Questions

1. **What is the difference between a Subsequence and a Substring?**
   *Answer:* A substring must consist of contiguous characters, whereas a subsequence does not require contiguous characters (only order must be preserved).

2. **What is the time and space complexity of standard LCS DP?**
   *Answer:* $O(m \cdot n)$ time and $O(m \cdot n)$ space.

3. **How can space complexity be reduced to $O(\min(m, n))$ if only the length is required?**
   *Answer:* By maintaining only two active DP rows (current and previous) of size $\min(m, n)$.

4. **How does `git diff` utilize LCS?**
   *Answer:* Lines of text are treated as items in a sequence. `git diff` computes the LCS of lines to identify unchanged blocks, rendering added/deleted lines around them.

5. **How do you modify LCS to find the Shortest Common Supersequence (SCS)?**
   *Answer:* $\text{Length of SCS} = m + n - \text{Length of LCS}(X, Y)$.

6. **What is Hirschberg's Algorithm?**
   *Answer:* An algorithm combining Divide & Conquer with DP that computes the LCS string in $O(m \cdot n)$ time and $O(\min(m, n))$ space.

7. **How is Longest Palindromic Subsequence (LPS) solved using LCS?**
   *Answer:* $\text{LPS}(S) = \text{LCS}(S, \text{Reverse}(S))$.

8. **What is the recurrence relation when $X[i-1] == Y[j-1]$?**
   *Answer:* $dp[i][j] = 1 + dp[i-1][j-1]$.

9. **Can LCS have multiple valid answer strings of the same length?**
   *Answer:* Yes. For example, $X = \text{"AB"}$ and $Y = \text{"BA"}$ yields two valid LCS strings: `"A"` and `"B"`, both of length 1.

10. **What is the base case of LCS?**
    *Answer:* $dp[0][j] = 0$ and $dp[i][0] = 0$ (matching with an empty string yields length 0).

---

## 19. Practice Problems

### Easy
1. **LeetCode 1143:** [Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence/) - Standard LCS implementation.
2. **LeetCode 392:** [Is Subsequence](https://leetcode.com/problems/is-subsequence/) - Two-pointer subsequence check.

### Medium
3. **LeetCode 516:** [Longest Palindromic Subsequence](https://leetcode.com/problems/longest-palindromic-subsequence/) - LCS of string and its reverse.
4. **LeetCode 1092:** [Shortest Common Supersequence](https://leetcode.com/problems/shortest-common-supersequence/) - Reconstructing supersequence from LCS.
5. **LeetCode 712:** [Minimum ASCII Delete Sum for Two Strings](https://leetcode.com/problems/minimum-ascii-delete-sum-for-two-strings/) - Weighted character deletion variant.

### Hard
6. **LeetCode 1092 / GeeksforGeeks:** [Printing Shortest Common Supersequence](https://practice.geeksforgeeks.org/) - Reconstructing complete supersequence string.

---

## 20. Related Algorithms

- **Longest Common Substring:** Requires contiguous character matching.
- **Edit Distance (Levenshtein Distance):** Computes minimum insertions, deletions, and substitutions to transform $X \to Y$.
- **Shortest Common Supersequence (SCS):** Smallest string containing both $X$ and $Y$ as subsequences.
- **Needleman-Wunsch Algorithm:** Global sequence alignment in bioinformatics.

---

## 21. Summary

The Longest Common Subsequence Problem is a foundational 2D Dynamic Programming algorithm. By comparing prefixes of strings $X$ and $Y$ and applying state transitions $1 + dp[i-1][j-1]$ (on match) or $\max(dp[i-1][j], dp[i][j-1])$ (on mismatch), LCS solves sequence alignment in **$O(m \cdot n)$ time**.

---

## 22. Quiz

**Question 1:** What is the difference between a Subsequence and a Substring?
- A) Substrings do not preserve character order
- B) Subsequence characters must be contiguous; Substring characters do not
- C) Substring characters must be contiguous; Subsequence characters do not
- D) They are identical terms
- **Correct Answer:** C
- **Explanation:** Substrings are contiguous blocks; subsequences maintain relative order but allow gaps.

**Question 2:** What is the state transition when $X[i-1] == Y[j-1]$?
- A) $dp[i][j] = dp[i-1][j] + 1$
- B) $dp[i][j] = 1 + dp[i-1][j-1]$
- C) $dp[i][j] = \max(dp[i-1][j], dp[i][j-1])$
- D) $dp[i][j] = 0$
- **Correct Answer:** B
- **Explanation:** Matching characters extend the LCS of shorter prefixes by 1.

**Question 3:** What is the time complexity of standard LCS DP for strings of length $m$ and $n$?
- A) $O(m + n)$
- B) $O(m \log n)$
- C) $O(m \cdot n)$
- D) $O(2^m)$
- **Correct Answer:** C
- **Explanation:** Fills a 2D matrix of size $(m+1) \times (n+1)$, taking $O(m \cdot n)$ time.

**Question 4:** How do version control systems like `git diff` utilize LCS?
- A) To compress binary files
- B) To compute line-by-line common subsequences to display added and deleted code lines
- C) To sort commit messages
- D) To encrypt repository keys
- **Correct Answer:** B
- **Explanation:** `git diff` uses LCS on lines of code to identify modified, added, and removed lines.

**Question 5:** What is the relationship between Shortest Common Supersequence (SCS) length and LCS length?
- A) $\text{SCS} = m \times n - \text{LCS}$
- B) $\text{SCS} = m + n - \text{LCS}$
- C) $\text{SCS} = \text{LCS} / 2$
- D) $\text{SCS} = m + n + \text{LCS}$
- **Correct Answer:** B
- **Explanation:** The shortest supersequence contains all characters of $X$ and $Y$, avoiding duplicating the shared LCS characters.

**Question 6:** How can Longest Palindromic Subsequence (LPS) of string $S$ be solved using LCS?
- A) $\text{LCS}(S, S)$
- B) $\text{LCS}(S, \text{Reverse}(S))$
- C) $\text{LCS}(S, \text{"ABC"})$
- D) Cannot be solved with LCS
- **Correct Answer:** B
- **Explanation:** The longest common subsequence between $S$ and its reversed string equals the longest palindromic subsequence.

**Question 7:** What space complexity can be achieved if only the LCS length is required?
- A) $O(1)$
- B) $O(\min(m, n))$
- C) $O(m^2)$
- D) $O(2^n)$
- **Correct Answer:** B
- **Explanation:** Maintaining only 2 rows of size $\min(m, n)$ reduces space complexity to $O(\min(m, n))$.

**Question 8:** What algorithm reconstructs the full LCS string in $O(m \cdot n)$ time and $O(\min(m, n))$ space?
- A) Dijkstra's Algorithm
- B) Hirschberg's Algorithm
- C) KMP Algorithm
- D) Floyd-Warshall Algorithm
- **Correct Answer:** B
- **Explanation:** Hirschberg's Algorithm uses Divide & Conquer with 2-row DP to achieve linear space string reconstruction.

**Question 9:** What is the LCS of $X = \text{"ABC"}$ and $Y = \text{"DEF"}$?
- A) 1
- B) 3
- C) 0 (Empty string)
- D) -1
- **Correct Answer:** C
- **Explanation:** Strings share no common characters, so LCS length is 0.

**Question 10:** What is the base case value for $dp[0][j]$ and $dp[i][0]$?
- A) 0
- B) 1
- C) Infinity
- D) -1
- **Correct Answer:** A
- **Explanation:** An empty string shares 0 common subsequence characters with any string.
