# Z-Algorithm (Z-Array & Linear String Search)

## 1. Introduction

The **Z-Algorithm** constructs an integer array $Z$ for a string $S$ of length $N$ in **linear time $O(N)$**.

For any index $i$, $Z[i]$ is the **length of the longest substring starting from $S[i]$ that matches a prefix of $S$**.

By concatenating pattern $P$ and text $T$ using a special separator character `$` ($S = P + "\$" + T$), the Z-algorithm finds all occurrences of pattern $P$ in text $T$ in **$O(n + m)$ time**.

---

## 2. Why Use This Algorithm?

### Z-Array Advantage:
- Computes prefix matches for all suffix positions in a single $O(N)$ scan.
- Extremely simple and uniform to implement without complicated state machines.

---

## 3. Real-World Applications

- **Pattern Matching:** Finding pattern $P$ in text $T$ by constructing $Z$ array for $P + "\$" + T$.
- **String Periodicity & Compression:** Finding shortest repeating period in strings.
- **Palindromic Prefix Analysis:** Finding longest palindromic prefixes.

---

## 4. Prerequisites

- Prefix and suffix concepts.
- Two-pointer sliding window concept $[L, R]$.

---

## 5. Visualization

### Z-Box Sliding Window $[L, R]$

```
String S:  a a b a a c a a b a a b
Index i:   0 1 2 3 4 5 6 7 8 9 10 11
Z-Array:  [0,1,0,2,1,0,5,1,0,2,1,1]

Z[6] = 5 because substring starting at index 6 "aabaab" shares prefix "aabaa" of length 5 with S.
```

---

## 6. How It Works

1. Maintain window $[L, R]$ representing the interval with maximum $R$ such that $S[L \dots R]$ matches prefix $S[0 \dots R-L]$.
2. For $i = 1 \dots N-1$:
   - If $i > R$: manually compare $S[i \dots]$ with $S[0 \dots]$. Set $Z[i]$ and update $[L, R] = [i, i + Z[i] - 1]$.
   - If $i \le R$: let $k = i - L$.
     - If $Z[k] < R - i + 1$: $Z[i] = Z[k]$.
     - Else: reset $L = i$, extend $R$ manually, set $Z[i] = R - L$, and update $R$.

---

## 7. Step-by-Step Algorithm

1. Initialize $Z$ array of size $N$ with 0. Set $L = 0, R = 0$.
2. For $i = 1$ to $N - 1$:
   - If $i > R$:
     - $L = R = i$.
     - While $R < N$ and $S[R - L] == S[R]$, $R++$.
     - $Z[i] = R - L$, $R--$.
   - Else:
     - $k = i - L$.
     - If $Z[k] < R - i + 1$: $Z[i] = Z[k]$.
     - Else:
       - $L = i$.
       - While $R < N$ and $S[R - L] == S[R]$, $R++$.
       - $Z[i] = R - L$, $R--$.

---

## 8. Pseudocode

```text
function calculateZ(s):
    n = length(s)
    Z = array of size n initialized to 0
    L = 0, R = 0
    
    for i from 1 to n - 1:
        if i > R:
            L = R = i
            while R < n and s[R - L] == s[R]:
                R = R + 1
            Z[i] = R - L
            R = R - 1
        else:
            k = i - L
            if Z[k] < R - i + 1:
                Z[i] = Z[k]
            else:
                L = i
                while R < n and s[R - L] == s[R]:
                    R = R + 1
                Z[i] = R - L
                R = R - 1
    return Z

function zSearch(text, pattern):
    concat = pattern + "$" + text
    Z = calculateZ(concat)
    m = length(pattern)
    matches = []
    
    for i from 0 to length(Z) - 1:
        if Z[i] == m:
            matches.append(i - m - 1)
            
    return matches
```

---

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

void calculateZ(char* s, int n, int Z[]) {
    int L = 0, R = 0;
    for (int i = 1; i < n; i++) {
        if (i > R) {
            L = R = i;
            while (R < n && s[R - L] == s[R]) R++;
            Z[i] = R - L;
            R--;
        } else {
            int k = i - L;
            if (Z[k] < R - i + 1) {
                Z[i] = Z[k];
            } else {
                L = i;
                while (R < n && s[R - L] == s[R]) R++;
                Z[i] = R - L;
                R--;
            }
        }
    }
}

void zSearch(char* text, char* pattern) {
    int m = strlen(pattern);
    int n = strlen(text);
    int totalLen = m + 1 + n;

    char* concat = (char*)malloc(totalLen + 1);
    sprintf(concat, "%s$%s", pattern, text);

    int* Z = (int*)calloc(totalLen, sizeof(int));
    calculateZ(concat, totalLen, Z);

    for (int i = 0; i < totalLen; i++) {
        if (Z[i] == m) {
            printf("Pattern found at index %d\n", i - m - 1);
        }
    }

    free(concat);
    free(Z);
}

int main() {
    char text[] = "BAABAABAA";
    char pattern[] = "AAB";
    zSearch(text, pattern);
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
#include <string>

using namespace std;

vector<int> calculateZ(const string& s) {
    int n = s.length();
    vector<int> Z(n, 0);
    int L = 0, R = 0;

    for (int i = 1; i < n; i++) {
        if (i > R) {
            L = R = i;
            while (R < n && s[R - L] == s[R]) R++;
            Z[i] = R - L;
            R--;
        } else {
            int k = i - L;
            if (Z[k] < R - i + 1) {
                Z[i] = Z[k];
            } else {
                L = i;
                while (R < n && s[R - L] == s[R]) R++;
                Z[i] = R - L;
                R--;
            }
        }
    }
    return Z;
}

vector<int> zSearch(const string& text, const string& pattern) {
    string concat = pattern + "$" + text;
    vector<int> Z = calculateZ(concat);
    vector<int> matches;
    int m = pattern.length();

    for (int i = 0; i < Z.size(); i++) {
        if (Z[i] == m) {
            matches.push_back(i - m - 1);
        }
    }
    return matches;
}

int main() {
    string text = "BAABAABAA";
    string pattern = "AAB";

    vector<int> matches = zSearch(text, pattern);
    cout << "Pattern found at indices: ";
    for (int idx : matches) cout << idx << " ";
    cout << "\n";
    return 0;
}
```

### Java
```java
import java.util.ArrayList;
import java.util.List;

public class ZAlgorithm {

    private static int[] calculateZ(String s) {
        int n = s.length();
        int[] Z = new int[n];
        int L = 0, R = 0;

        for (int i = 1; i < n; i++) {
            if (i > R) {
                L = R = i;
                while (R < n && s.charAt(R - L) == s.charAt(R)) R++;
                Z[i] = R - L;
                R--;
            } else {
                int k = i - L;
                if (Z[k] < R - i + 1) {
                    Z[i] = Z[k];
                } else {
                    L = i;
                    while (R < n && s.charAt(R - L) == s.charAt(R)) R++;
                    Z[i] = R - L;
                    R--;
                }
            }
        }
        return Z;
    }

    public static List<Integer> zSearch(String text, String pattern) {
        String concat = pattern + "$" + text;
        int[] Z = calculateZ(concat);
        List<Integer> matches = new ArrayList<>();
        int m = pattern.length();

        for (int i = 0; i < Z.length; i++) {
            if (Z[i] == m) {
                matches.add(i - m - 1);
            }
        }
        return matches;
    }

    public static void main(String[] args) {
        String text = "BAABAABAA";
        String pattern = "AAB";

        System.out.println("Pattern found at indices: " + zSearch(text, pattern));
    }
}
```

### Python
```python
def calculate_z(s):
    n = len(s)
    z = [0] * n
    l, r = 0, 0

    for i in range(1, n):
        if i > r:
            l = r = i
            while r < n and s[r - l] == s[r]:
                r += 1
            z[i] = r - l
            r -= 1
        else:
            k = i - l
            if z[k] < r - i + 1:
                z[i] = z[k]
            else:
                l = i
                while r < n and s[r - l] == s[r]:
                    r += 1
                z[i] = r - l
                r -= 1
    return z


def z_search(text, pattern):
    concat = pattern + "$" + text
    z = calculate_z(concat)
    m = len(pattern)
    matches = []

    for i in range(len(z)):
        if z[i] == m:
            matches.append(i - m - 1)

    return matches


if __name__ == "__main__":
    text = "BAABAABAA"
    pattern = "AAB"
    print(f"Pattern found at indices: {z_search(text, pattern)}")
```

### JavaScript
```javascript
function calculateZ(s) {
    const n = s.length;
    const Z = Array(n).fill(0);
    let L = 0, R = 0;

    for (let i = 1; i < n; i++) {
        if (i > R) {
            L = R = i;
            while (R < n && s[R - L] === s[R]) R++;
            Z[i] = R - L;
            R--;
        } else {
            const k = i - L;
            if (Z[k] < R - i + 1) {
                Z[i] = Z[k];
            } else {
                L = i;
                while (R < n && s[R - L] === s[R]) R++;
                Z[i] = R - L;
                R--;
            }
        }
    }
    return Z;
}

function zSearch(text, pattern) {
    const concat = pattern + "$" + text;
    const Z = calculateZ(concat);
    const m = pattern.length;
    const matches = [];

    for (let i = 0; i < Z.length; i++) {
        if (Z[i] === m) {
            matches.push(i - m - 1);
        }
    }
    return matches;
}

const text = "BAABAABAA";
const pattern = "AAB";
console.log(`Pattern found at indices: ${zSearch(text, pattern)}`);
```

---

## 10. Code Explanation

- **Z-Box Reuse:** When $i \le R$, if $Z[k] < R - i + 1$, $Z[i]$ is copied in $O(1)$ without character comparisons.
- **Linear Complexity Proof:** Every inner while loop iteration increments $R$. Since $R$ can increase at most $N$ times, total comparisons across the algorithm is strictly bounded by $O(N)$.

---

## 11. Interactive Demo

Visual setup for Z-Algorithm:
1. **Concat String Renderer:** Displays $P + "\$" + T$.
2. **Z-Box Window Highlight:** Highlights current $[L, R]$ sliding bounds in real time.

---

## 12. Dry Run

Tracing string `S = "aab$baabaab"`:

| Index `i` | Char | $[L, R]$ Window | Z-Box Reuse? | Computed $Z[i]$ |
|-----------|------|-----------------|--------------|-----------------|
| 0 | 'a' | $[0, 0]$ | - | 0 |
| 1 | 'a' | $[1, 1]$ | No | 1 |
| 2 | 'b' | $[1, 1]$ | No | 0 |
| 3 | '$' | $[1, 1]$ | No | 0 |
| 4 | 'b' | $[1, 1]$ | No | 0 |
| 5 | 'a' | $[5, 7]$ | No | 3 (`"aab"` matches pattern!) |

---

## 13. Time & Space Complexity

| Metric | Complexity | Explanation |
|--------|------------|-------------|
| Time Complexity | $O(n + m)$ | $R$ pointer moves right at most $n + m$ times. |
| Space Complexity | $O(n + m)$ | Storing concatenated string and Z-array. |

---

## 14. Advantages

- **Simplest Linear Search:** Easier to write than KMP's nested LPS fallback logic.

---

## 15. Disadvantages

- Requires allocating concatenated string space $P + "\$" + T$.

---

## 16. Applications

- String period finding.
- Pattern matching in text editors.

---

## 17. Common Mistakes

- **Forgetting Unique Separator:** Using a separator character `$` that appears in text or pattern.

---

## 18. Interview Questions

1. Why must the separator character `$` be unique in Z-algorithm pattern matching?
2. How can the Z-algorithm be used to find all periodic prefixes of a string?

---

## 19. Practice Problems

1. **LeetCode 3008:** Find Beautiful Indices in the Given Array II (Z-Algo / KMP)

---

## 20. Related Algorithms

- **KMP Algorithm:** Prefix function pattern search.

---

## 21. Summary

The Z-algorithm builds a Z-array in linear $O(N)$ time using $[L, R]$ Z-boxes, providing a simple framework for string matching and prefix analysis.

---

## 22. Quiz

**Question 1:** What does $Z[i]$ store in the Z-algorithm?
- A) The total frequency of character $S[i]$.
- B) The length of the longest substring starting at $S[i]$ that matches a prefix of $S$.
- C) The suffix length of string $S$.
- D) The hash value of $S[0 \dots i]$.
- **Correct Answer:** B
- **Explanation:** By definition, $Z[i]$ is the length of the longest prefix-matching substring starting from $S[i]$.
