# Radix Sort

## 1. Introduction

Radix Sort is a non-comparison-based integer sorting algorithm that processes numbers digit by digit, starting from the least significant digit (LSD) or the most significant digit (MSD). Instead of directly comparing two keys against each other, Radix Sort groups keys into buckets based on individual digits sharing the same position and value. 

Imagine sorting a stack of 3-digit numbers (like 432, 124, 751). You first group and sort them by their ones digit, then re-group and sort that intermediate list by their tens digit, and finally sort by their hundreds digit. After processing the highest digit, the list is completely sorted.

It was created to overcome the theoretical $O(n \log n)$ lower bound of comparison-based sorting algorithms by leveraging key structure (positional notation) to achieve linear time complexity under appropriate conditions.

You should use Radix Sort when sorting large collections of integers or fixed-length strings where the number of digits/characters ($d$) is relatively small compared to the count of elements ($n$).

## 2. Why Use This Algorithm?

Radix Sort is a specialized linear-time algorithm with unique strengths:

**Benefits:**
- **Linear-time performance:** $O(d \cdot (n + k))$ where $d$ is the number of digits and $k$ is the radix/base.
- **Stable sorting:** Preserves the relative order of duplicate keys (when implemented with a stable counting sort sub-routine).
- **Fast for bounded key lengths:** Outperforms comparison sorts like Quick Sort when keys have small positional lengths.

**Performance:**
- **Time Complexity:** $O(d \cdot (n + k))$ in all cases (Best, Average, Worst).
- **Space Complexity:** $O(n + k)$ auxiliary space required by the underlying Counting Sort engine.

**When it is better than other algorithms:**
Radix Sort beats Quick Sort and Merge Sort when sorting large numbers of integers (e.g., 32-bit or 64-bit integers) or fixed-length strings (such as IP addresses, telephone numbers, or ZIP codes).

## 3. Real-World Applications

- **Sorting Large Arrays of Integers:** Used in graphics, parallel computing (CUDA/GPU sorting), and big data frameworks.
- **Suffix Arrays & String Processing:** Used as a building block for fast suffix array construction algorithms.
- **Sorting Fixed-Length Keys:** Telephone directory sorting, social security numbers, and MAC/IP address routing tables.
- **Card Sorting Machines:** Historically used in mechanical punched-card sorting machines (Tabulating Technologies / IBM).

## 4. Prerequisites

Before studying Radix Sort, you should be familiar with:
- Position notation and digit extraction (`(num / 10^i) % 10`).
- [Counting Sort](./18_Counting_sort.md) (used as the stable counting pass for each digit position).
- Algorithm stability concepts.

## 5. Visualization

Given array: `[170, 45, 75, 90, 802, 24, 2, 66]`

1. **Sort by Ones digit (10^0):**
   - 17**0**, 09**0** -> bucket 0
   - 80**2**, 00**2** -> bucket 2
   - 02**4** -> bucket 4
   - 04**5**, 07**5** -> bucket 5
   - 06**6** -> bucket 6
   - Result: `[170, 90, 802, 2, 24, 45, 75, 66]`

2. **Sort by Tens digit (10^1):**
   - 8**0**2, 0**0**2 -> bucket 0
   - 0**2**4 -> bucket 2
   - 0**4**5 -> bucket 4
   - 0**6**6 -> bucket 6
   - 1**7**0, 0**7**5 -> bucket 7
   - 0**9**0 -> bucket 9
   - Result: `[802, 2, 24, 45, 66, 170, 75, 90]`

3. **Sort by Hundreds digit (10^2):**
   - **0**02, **0**24, **0**45, **0**66, **0**75, **0**90 -> bucket 0
   - **1**70 -> bucket 1
   - **8**02 -> bucket 8
   - Final Result: `[2, 24, 45, 66, 75, 90, 170, 802]`

## 6. How It Works

1. Find the maximum element in the array to determine the maximum number of digits ($d$).
2. Loop through each digit position `exp` (1, 10, 100, ...), where `exp` represents $10^i$.
3. For each digit position, run a stable **Counting Sort** subroutine to sort elements based on `(array[i] / exp) % 10`.
4. Overwrite the array with the result of the pass and advance to the next digit position until all digits are processed.

## 7. Step-by-Step Algorithm

1. Find `max_val = max(arr)`.
2. Initialize `exp = 1`.
3. While `max_val / exp > 0`:
   a. Call `counting_sort_by_digit(arr, exp)`.
   b. Multiply `exp` by 10 (`exp *= 10`).
4. Array is now sorted.

## 8. Pseudocode

```text
function radixSort(arr):
    max_val = getMax(arr)
    exp = 1
    
    while max_val / exp > 0:
        countingSortByDigit(arr, exp)
        exp = exp * 10

function countingSortByDigit(arr, exp):
    n = arr.length
    output = new Array(n)
    count = new Array(10) filled with 0
    
    for i = 0 to n - 1:
        digit = (arr[i] / exp) mod 10
        count[digit]++
        
    for i = 1 to 9:
        count[i] = count[i] + count[i - 1]
        
    for i = n - 1 down to 0:
        digit = (arr[i] / exp) mod 10
        output[count[digit] - 1] = arr[i]
        count[digit]--
        
    for i = 0 to n - 1:
        arr[i] = output[i]
```

## 9. Code Implementations

### Python
```python
def counting_sort_by_digit(arr: list[int], exp: int) -> None:
    n = len(arr)
    output = [0] * n
    count = [0] * 10

    for i in range(n):
        index = (arr[i] // exp) % 10
        count[index] += 1

    for i in range(1, 10):
        count[i] += count[i - 1]

    for i in range(n - 1, -1, -1):
        index = (arr[i] // exp) % 10
        output[count[index] - 1] = arr[i]
        count[index] -= 1

    for i in range(n):
        arr[i] = output[i]


def radix_sort(arr: list[int]) -> list[int]:
    if not arr:
        return arr
    max_val = max(arr)
    exp = 1
    while max_val // exp > 0:
        counting_sort_by_digit(arr, exp)
        exp *= 10
    return arr
```

### C++
```cpp
#include <vector>
#include <algorithm>

void countingSortByDigit(std::vector<int>& arr, int exp) {
    int n = arr.size();
    std::vector<int> output(n);
    int count[10] = {0};

    for (int i = 0; i < n; i++) {
        int index = (arr[i] / exp) % 10;
        count[index]++;
    }

    for (int i = 1; i < 10; i++) {
        count[i] += count[i - 1];
    }

    for (int i = n - 1; i >= 0; i--) {
        int index = (arr[i] / exp) % 10;
        output[count[index] - 1] = arr[i];
        count[index]--;
    }

    for (int i = 0; i < n; i++) {
        arr[i] = output[i];
    }
}

void radixSort(std::vector<int>& arr) {
    if (arr.empty()) return;
    int max_val = *std::max_element(arr.begin(), arr.end());
    for (int exp = 1; max_val / exp > 0; exp *= 10) {
        countingSortByDigit(arr, exp);
    }
}
```

### Java
```java
import java.util.Arrays;

public class RadixSort {
    private static void countingSortByDigit(int[] arr, int exp) {
        int n = arr.length;
        int[] output = new int[n];
        int[] count = new int[10];

        for (int i = 0; i < n; i++) {
            int index = (arr[i] / exp) % 10;
            count[index]++;
        }

        for (int i = 1; i < 10; i++) {
            count[i] += count[i - 1];
        }

        for (int i = n - 1; i >= 0; i--) {
            int index = (arr[i] / exp) % 10;
            output[count[index] - 1] = arr[i];
            count[index]--;
        }

        System.arraycopy(output, 0, arr, 0, n);
    }

    public static void radixSort(int[] arr) {
        if (arr.length == 0) return;
        int maxVal = Arrays.stream(arr).max().getAsInt();
        for (int exp = 1; maxVal / exp > 0; exp *= 10) {
            countingSortByDigit(arr, exp);
        }
    }
}
```

### JavaScript
```javascript
function countingSortByDigit(arr, exp) {
    const n = arr.length;
    const output = new Array(n).fill(0);
    const count = new Array(10).fill(0);

    for (let i = 0; i < n; i++) {
        const index = Math.floor(arr[i] / exp) % 10;
        count[index]++;
    }

    for (let i = 1; i < 10; i++) {
        count[i] += count[i - 1];
    }

    for (let i = n - 1; i >= 0; i--) {
        const index = Math.floor(arr[i] / exp) % 10;
        output[count[index] - 1] = arr[i];
        count[index]--;
    }

    for (let i = 0; i < n; i++) {
        arr[i] = output[i];
    }
}

function radixSort(arr) {
    if (arr.length === 0) return arr;
    const maxVal = Math.max(...arr);
    for (let exp = 1; Math.floor(maxVal / exp) > 0; exp *= 10) {
        countingSortByDigit(arr, exp);
    }
    return arr;
}
```

## 10. Complexity Analysis

- **Time Complexity:**
  - **Best Case:** $O(d \cdot (n + k))$
  - **Average Case:** $O(d \cdot (n + k))$
  - **Worst Case:** $O(d \cdot (n + k))$
  Where $d$ is the number of digits in the maximum number, $n$ is the number of elements, and $k$ is the base/radix (10 for decimal).

- **Space Complexity:**
  - $O(n + k)$ auxiliary space for the counting array and output buffer during each pass.

## 11. Edge Cases & Potential Hazards

- **Negative Numbers:** Standard Radix Sort assumes positive numbers. To handle negative numbers, separate them or offset values so all elements are non-negative.
- **Large Digit Length ($d$):** If $d$ is very large (e.g. $d \approx n$), Radix Sort time degrades to $O(n^2)$, making Quick Sort or Merge Sort better.
- **Floating Point Numbers:** Requires converting floats into order-preserving integer bitwise representations before sorting.

## 12. Comparison with Other Algorithms

| Feature | Radix Sort | Counting Sort | Quick Sort | Merge Sort |
|---|---|---|---|---|
| **Time Complexity** | $O(d(n+k))$ | $O(n+k)$ | $O(n \log n)$ | $O(n \log n)$ |
| **Space Complexity** | $O(n+k)$ | $O(n+k)$ | $O(\log n)$ | $O(n)$ |
| **Comparison-Based** | No | No | Yes | Yes |
| **Stable** | Yes | Yes | No | Yes |

## 13. Summary & Quick Reference

- **Core Idea:** Sort digit by digit from LSD to MSD using stable Counting Sort.
- **Best Used For:** Large arrays of integers or fixed-length keys with small digit counts.
- **Stability:** Must use a stable subroutine (like Counting Sort) to remain stable overall.
