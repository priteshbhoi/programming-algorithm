# Boyer-Moore Algorithm (Bad Character Heuristic)

## 1. Introduction

The **Boyer-Moore Algorithm** is widely considered the gold standard for general-purpose string searching.

It operates by matching pattern characters **from right to left** (tail to head) while shifting the pattern window **from left to right** across the text.

This right-to-left scan strategy enables massive character jumps—achieving **sublinear best-case time complexity $O(n / m)$**!

---

## 2. Why Use This Algorithm?

### Bad Character Heuristic Advantage:
- **Naïve Matching ($O(n \cdot m)$):** Always shifts pattern by 1 position.
- **Boyer-Moore ($O(n / m)$ best case):** When a mismatch occurs at text character $T[i]$, Boyer-Moore aligns $T[i]$ with its last occurrence in the pattern. If $T[i]$ does not exist in the pattern at all, the entire pattern shifts past $T[i]$ (shift of $m$ positions)!

---

## 3. Real-World Applications

- **GNU Grep Utility:** `grep` uses Boyer-Moore variants for lightning-fast command-line text searches.
- **Database Query Matching:** String `LIKE '%pattern%'` optimization.

---

## 4. Prerequisites

- Right-to-left character scanning.
- Precomputed Bad Character table mapping ASCII characters to last occurrence indices.

---

## 5. Visualization

```
Text:     A B A A A B C D A B C
Pattern:  A B C
          ^   |  (Scan right-to-left: 'C' != 'A', 'A' is at pattern index 0)
Shift:    Align 'A' in pattern with 'A' in text
```

---

## 6. How It Works

1. Build `badChar` table: Stores last occurrence index of each character in pattern $P$. Unseen characters are set to `-1`.
2. Align pattern with start of text $s = 0$.
3. Scan pattern right-to-left $j = m - 1$ down to 0:
   - If `pattern[j] == text[s + j]`, decrement $j$.
   - If mismatch at $j$: shift $s += \max(1, j - badChar[text[s + j]])$.
   - If $j < 0$ (match): record match $s$, shift $s += (s + m < n) ? m - badChar[text[s + m]] : 1$.

---

## 7. Step-by-Step Algorithm

1. Initialize `badChar` array of size 256 with `-1`.
2. For $i = 0$ to $m - 1$: set `badChar[(unsigned char)pattern[i]] = i`.
3. Set shift `s = 0`.
4. While `s <= n - m`:
   - Set `j = m - 1`.
   - While `j >= 0` and `pattern[j] == text[s + j]`: `j--`.
   - If `j < 0`: record match `s`, update `s`.
   - Else: `s += max(1, j - badChar[text[s + j]])`.

---

## 8. Pseudocode

```text
function boyerMooreSearch(text, pattern):
    n = length(text)
    m = length(pattern)
    badChar = array of size 256 initialized to -1
    
    for i from 0 to m - 1:
        badChar[pattern[i]] = i
        
    s = 0 // shift
    matches = []
    
    while s <= n - m:
        j = m - 1
        
        while j >= 0 and pattern[j] == text[s + j]:
            j = j - 1
            
        if j < 0:
            matches.append(s)
            s = s + (s + m < n ? m - badChar[text[s + m]] : 1)
        else:
            s = s + max(1, j - badChar[text[s + j]])
            
    return matches
```

---

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

#define NO_OF_CHARS 256

int max(int a, int b) { return (a > b) ? a : b; }

void badCharHeuristic(char* str, int size, int badchar[NO_OF_CHARS]) {
    for (int i = 0; i < NO_OF_CHARS; i++) badchar[i] = -1;
    for (int i = 0; i < size; i++) badchar[(unsigned char)str[i]] = i;
}

void search(char* txt, char* pat) {
    int m = strlen(pat);
    int n = strlen(txt);
    int badchar[NO_OF_CHARS];

    badCharHeuristic(pat, m, badchar);

    int s = 0;
    while (s <= (n - m)) {
        int j = m - 1;

        while (j >= 0 && pat[j] == txt[s + j])
            j--;

        if (j < 0) {
            printf("Pattern occurs at shift = %d\n", s);
            s += (s + m < n) ? m - badchar[(unsigned char)txt[s + m]] : 1;
        } else {
            s += max(1, j - badchar[(unsigned char)txt[s + j]]);
        }
    }
}

int main() {
    char txt[] = "ABAAABCDABC";
    char pat[] = "ABC";
    search(txt, pat);
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

#define NO_OF_CHARS 256

void badCharHeuristic(const string& pattern, vector<int>& badChar) {
    int m = pattern.length();
    fill(badChar.begin(), badChar.end(), -1);

    for (int i = 0; i < m; i++) {
        badChar[(unsigned char)pattern[i]] = i;
    }
}

vector<int> boyerMooreSearch(const string& text, const string& pattern) {
    vector<int> matches;
    int n = text.length();
    int m = pattern.length();
    if (m == 0 || n < m) return matches;

    vector<int> badChar(NO_OF_CHARS, -1);
    badCharHeuristic(pattern, badChar);

    int s = 0;

    while (s <= (n - m)) {
        int j = m - 1;

        while (j >= 0 && pattern[j] == text[s + j]) {
            j--;
        }

        if (j < 0) {
            matches.push_back(s);
            s += (s + m < n) ? m - badChar[(unsigned char)text[s + m]] : 1;
        } else {
            s += max(1, j - badChar[(unsigned char)text[s + j]]);
        }
    }

    return matches;
}

int main() {
    string text = "ABAAABCDABC";
    string pattern = "ABC";

    vector<int> matches = boyerMooreSearch(text, pattern);
    cout << "Pattern found at index: ";
    for (int idx : matches) cout << idx << " ";
    cout << "\n";
    return 0;
}
```

### Java
```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class BoyerMoore {

    private static final int NO_OF_CHARS = 256;

    private static void badCharHeuristic(String pattern, int[] badChar) {
        Arrays.fill(badChar, -1);
        for (int i = 0; i < pattern.length(); i++) {
            badChar[pattern.charAt(i)] = i;
        }
    }

    public static List<Integer> search(String text, String pattern) {
        List<Integer> matches = new ArrayList<>();
        int n = text.length();
        int m = pattern.length();
        if (m == 0 || n < m) return matches;

        int[] badChar = new int[NO_OF_CHARS];
        badCharHeuristic(pattern, badChar);

        int s = 0;
        while (s <= (n - m)) {
            int j = m - 1;

            while (j >= 0 && pattern.charAt(j) == text.charAt(s + j)) {
                j--;
            }

            if (j < 0) {
                matches.add(s);
                s += (s + m < n) ? m - badChar[text.charAt(s + m)] : 1;
            } else {
                s += Math.max(1, j - badChar[text.charAt(s + j)]);
            }
        }

        return matches;
    }

    public static void main(String[] args) {
        String text = "ABAAABCDABC";
        String pattern = "ABC";
        System.out.println("Pattern found at indices: " + search(text, pattern));
    }
}
```

### Python
```python
def bad_character_heuristic(pattern):
    bad_char = {}
    for i, char in enumerate(pattern):
        bad_char[char] = i
    return bad_char


def boyer_moore_search(text, pattern):
    n = len(text)
    m = len(pattern)
    if m == 0 or n < m:
        return []

    bad_char = bad_character_heuristic(pattern)
    matches = []
    s = 0

    while s <= (n - m):
        j = m - 1

        while j >= 0 and pattern[j] == text[s + j]:
            j -= 1

        if j < 0:
            matches.append(s)
            s += (m - bad_char.get(text[s + m], -1)) if (s + m < n) else 1
        else:
            s += max(1, j - bad_char.get(text[s + j], -1))

    return matches


if __name__ == "__main__":
    text = "ABAAABCDABC"
    pattern = "ABC"
    print(f"Pattern found at indices: {boyer_moore_search(text, pattern)}")
```

### JavaScript
```javascript
function boyerMooreSearch(text, pattern) {
    const n = text.length;
    const m = pattern.length;
    if (m === 0 || n < m) return [];

    const badChar = {};
    for (let i = 0; i < m; i++) {
        badChar[pattern[i]] = i;
    }

    const matches = [];
    let s = 0;

    while (s <= (n - m)) {
        let j = m - 1;

        while (j >= 0 && pattern[j] === text[s + j]) {
            j--;
        }

        if (j < 0) {
            matches.push(s);
            const lastOcc = badChar[text[s + m]] !== undefined ? badChar[text[s + m]] : -1;
            s += (s + m < n) ? m - lastOcc : 1;
        } else {
            const lastOcc = badChar[text[s + j]] !== undefined ? badChar[text[s + j]] : -1;
            s += Math.max(1, j - lastOcc);
        }
    }

    return matches;
}

const text = "ABAAABCDABC";
const pattern = "ABC";
console.log(`Pattern found at indices: ${boyerMooreSearch(text, pattern)}`);
```

---

## 10. Code Explanation

- **Right-to-Left Scan ($j = m - 1 \dots 0$):** Scans tail character first. If tail doesn't match and doesn't exist in pattern, we shift by full $m$ length.

---

## 11. Interactive Demo

Visual setup for Boyer-Moore:
1. **Bad Character Table Display:** Shows ASCII last occurrence array.
2. **Shift Animator:** Visualizes sublinear jumps across text string.

---

## 12. Dry Run

Tracing `pattern = "ABC"` on `text = "ABAAABCDABC"`:

| Shift `s` | Window Text | Pattern | Match Right-to-Left | Bad Char | Shift Calculation | Next `s` |
|-----------|-------------|---------|----------------------|----------|-------------------|----------|
| 0 | `ABA` | `ABC` | 'A' != 'C' | 'A' (index 0 in pat) | $\max(1, 2 - 0) = 2$ | 2 |
| 2 | `AAA` | `ABC` | 'A' != 'C' | 'A' (index 0 in pat) | $\max(1, 2 - 0) = 2$ | 4 |
| 4 | `ABC` | `ABC` | Full Match! | - | Match at index 4 | 8 |

---

## 13. Time & Space Complexity

| Metric | Complexity | Explanation |
|--------|------------|-------------|
| Best Case Time | $O(n / m)$ | Occurs when text contains characters not present in pattern. |
| Average Time | $O(n)$ | Sublinear / linear performance. |
| Space Complexity | $O(|\Sigma|)$ | Alphabet size array space. |

---

## 14. Advantages

- **Fastest in Practice:** Sublinear speed $O(n / m)$ on long natural language texts.

---

## 15. Disadvantages

- Preprocessing complexity for full Good-Suffix rule (though Bad-Character alone works great for large alphabet texts).

---

## 16. Applications

- Text search tools (`grep`).

---

## 17. Common Mistakes

- **Scan Direction Error:** Scanning left-to-right instead of right-to-left.

---

## 18. Interview Questions

1. Why does Boyer-Moore scan pattern characters from right to left?
2. What is the best-case time complexity of Boyer-Moore?

---

## 19. Practice Problems

1. **LeetCode 28:** Find the Index of the First Occurrence in a String

---

## 20. Related Algorithms

- **KMP Algorithm:** Scans left to right using LPS prefix function.

---

## 21. Summary

Boyer-Moore uses right-to-left character scanning and bad-character shifts to achieve sublinear $O(n / m)$ search speed.

---

## 22. Quiz

**Question 1:** What is the best-case time complexity of Boyer-Moore string search?
- A) $O(n \cdot m)$
- B) $O(n / m)$
- C) $O(n^2)$
- D) $O(\log n)$
- **Correct Answer:** B
- **Explanation:** When text characters do not exist in pattern, each shift moves by $m$ positions, taking $n/m$ checks.
