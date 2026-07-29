# Radix Sort

## 1. Introduction

Radix Sort is a non-comparison-based sorting algorithm that sorts elements digit by digit, starting from the least significant digit (LSD) to the most significant digit (MSD), or vice versa. Instead of directly comparing elements against one another, Radix Sort groups elements into buckets based on their individual digits at the current position.

Imagine sorting a stack of 3-digit index cards (e.g., 432, 124, 751). You first group and order them according to their ones digit, reassemble the stack while preserving stability, then repeat the process for the tens digit, and finally for the hundreds digit. After processing the most significant digit, the entire stack is perfectly sorted.

It was created to bypass the theoretical $\mathcal{O}(n \log n)$ lower bound of comparison-based sorting algorithms by exploiting positional numerical notation to achieve linear time complexity when digit count is limited.

You should use Radix Sort when sorting large collections of non-negative integers or fixed-length strings (such as IP addresses or phone numbers) where the maximum number of digits ($d$) is small relative to the number of elements ($n$).

## 2. Why Use This Algorithm?

Radix Sort is a specialized linear-time sorting algorithm with unique structural advantages:

**Benefits:**
- **Linear Time Complexity:** Runs in $\mathcal{O}(d \cdot (n + k))$ time, where $d$ is the maximum number of digits and $k$ is the radix base (typically 10).
- **Stable Sorting:** Preserves the relative order of duplicate keys when backed by a stable counting subroutine.
- **Fast for Fixed-Length Keys:** Beats comparison-based sorting algorithms like Quick Sort when key length is bounded.
- **No Direct Comparisons:** Avoids costly element-to-element comparison operators.

**Performance:**
- **Best Case:** $\mathcal{O}(d \cdot (n + k))$
- **Average Case:** $\mathcal{O}(d \cdot (n + k))$
- **Worst Case:** $\mathcal{O}(d \cdot (n + k))$
- **Space Complexity:** $\mathcal{O}(n + k)$ auxiliary space for the output buffer and count array.

**When it is better than other algorithms:**
Radix Sort outperforms Quick Sort and Merge Sort when sorting huge arrays of integers (e.g., 32-bit integers or fixed-length string identifiers) where $d \ll n$.

## 3. Real-World Applications

- **GPU and Parallel Sorting:** Frequently used in CUDA and OpenCL implementations for sorting primitive integer keys rapidly.
- **Suffix Array Construction:** Acts as a foundational building block for constructing suffix arrays in string processing.
- **Network Routing:** Sorting fixed-length IP addresses, MAC addresses, and packet headers.
- **Historical Tabulation:** Powering legacy mechanical punched-card sorting machines (IBM card sorters).
- **Database Keys:** Fast key sorting when dealing with fixed-size numeric primary keys.

## 4. Prerequisites

Before learning Radix Sort, you should understand:
- Basic positional notation and digit extraction using integer division and modulo (`(num / exp) % 10`).
- The mechanics of [Counting Sort](./18_Counting_sort.md), which serves as the inner stable sorting subroutine.
- Concept of sorting algorithm stability.
- Big-O notation and asymptotic analysis.

## 5. Visualization

Given Array: `[170, 45, 75, 90, 802, 24, 2, 66]`

1. **Pass 1: Ones Digit ($10^0 = 1$)**
   - Bucket 0: `170`, `090`
   - Bucket 2: `802`, `002`
   - Bucket 4: `024`
   - Bucket 5: `045`, `075`
   - Bucket 6: `066`
   - **Reassembled Array:** `[170, 90, 802, 2, 24, 45, 75, 66]`

2. **Pass 2: Tens Digit ($10^1 = 10$)**
   - Bucket 0: `802`, `002`
   - Bucket 2: `024`
   - Bucket 4: `045`
   - Bucket 6: `066`
   - Bucket 7: `170`, `075`
   - Bucket 9: `090`
   - **Reassembled Array:** `[802, 2, 24, 45, 66, 170, 75, 90]`

3. **Pass 3: Hundreds Digit ($10^2 = 100$)**
   - Bucket 0: `002`, `024`, `045`, `066`, `075`, `090`
   - Bucket 1: `170`
   - Bucket 8: `802`
   - **Final Sorted Array:** `[2, 24, 45, 66, 75, 90, 170, 802]`

## 6. How It Works

Radix Sort processes digits position by position:
1. Identify the maximum element to determine how many digit positions ($d$) need to be processed.
2. Initialize an exponent factor `exp = 1` (representing $10^0$).
3. Run a modified, stable Counting Sort on the array using `(arr[i] / exp) % 10` as the sorting key.
4. Multiply `exp` by 10 to move to the next digit position (tens, hundreds, etc.).
5. Repeat until all digit positions up to the maximum element have been processed.

## 7. Step-by-Step Algorithm

1. Find the maximum element in `arr` to determine `max_val`.
2. Set `exp = 1`.
3. While `max_val / exp > 0`:
   1. Create an `output` array of size $n$ and a `count` array of size 10 initialized to zeros.
   2. Count the occurrences of each digit at current `exp`: `count[(arr[i] / exp) % 10]++`.
   3. Update `count` array to hold prefix sums so each index indicates final positional placement.
   4. Iterate from `n - 1` down to `0` to build the `output` array stably.
   5. Copy `output` back into `arr`.
   6. Multiply `exp` by 10.
4. Return the sorted array.

## 8. Pseudocode

```text
function radixSort(arr):
    if length(arr) <= 1:
        return arr
    
    max_val = max(arr)
    exp = 1
    
    while max_val / exp > 0:
        countingSortByDigit(arr, exp)
        exp = exp * 10
        
    return arr

function countingSortByDigit(arr, exp):
    n = length(arr)
    output = array of size n
    count = array of size 10 initialized to 0
    
    for i = 0 to n - 1:
        digit = (arr[i] / exp) mod 10
        count[digit] = count[digit] + 1
        
    for i = 1 to 9:
        count[i] = count[i] + count[i - 1]
        
    for i = n - 1 down to 0:
        digit = (arr[i] / exp) mod 10
        output[count[digit] - 1] = arr[i]
        count[digit] = count[digit] - 1
        
    for i = 0 to n - 1:
        arr[i] = output[i]
```

## 9. Code Examples

### C
```c
#include <stdio.h>

int getMax(int arr[], int n) {
    int max = arr[0];
    for (int i = 1; i < n; i++) {
        if (arr[i] > max)
            max = arr[i];
    }
    return max;
}

void countingSortByDigit(int arr[], int n, int exp) {
    int output[n];
    int count[10] = {0};

    for (int i = 0; i < n; i++)
        count[(arr[i] / exp) % 10]++;

    for (int i = 1; i < 10; i++)
        count[i] += count[i - 1];

    for (int i = n - 1; i >= 0; i--) {
        int digit = (arr[i] / exp) % 10;
        output[count[digit] - 1] = arr[i];
        count[digit]--;
    }

    for (int i = 0; i < n; i++)
        arr[i] = output[i];
}

void radixSort(int arr[], int n) {
    if (n <= 1) return;
    int max_val = getMax(arr, n);
    for (int exp = 1; max_val / exp > 0; exp *= 10)
        countingSortByDigit(arr, n, exp);
}

int main() {
    int arr[] = {170, 45, 75, 90, 802, 24, 2, 66};
    int n = sizeof(arr) / sizeof(arr[0]);
    radixSort(arr, n);
    for (int i = 0; i < n; i++)
        printf("%d ", arr[i]);
    printf("\n");
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

void countingSortByDigit(std::vector<int>& arr, int exp) {
    int n = arr.size();
    std::vector<int> output(n);
    int count[10] = {0};

    for (int i = 0; i < n; i++) {
        int digit = (arr[i] / exp) % 10;
        count[digit]++;
    }

    for (int i = 1; i < 10; i++) {
        count[i] += count[i - 1];
    }

    for (int i = n - 1; i >= 0; i--) {
        int digit = (arr[i] / exp) % 10;
        output[count[digit] - 1] = arr[i];
        count[digit]--;
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

int main() {
    std::vector<int> arr = {170, 45, 75, 90, 802, 24, 2, 66};
    radixSort(arr);
    for (int val : arr) {
        std::cout << val << " ";
    }
    std::cout << "\n";
    return 0;
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
            int digit = (arr[i] / exp) % 10;
            count[digit]++;
        }

        for (int i = 1; i < 10; i++) {
            count[i] += count[i - 1];
        }

        for (int i = n - 1; i >= 0; i--) {
            int digit = (arr[i] / exp) % 10;
            output[count[digit] - 1] = arr[i];
            count[digit]--;
        }

        System.arraycopy(output, 0, arr, 0, n);
    }

    public static void radixSort(int[] arr) {
        if (arr.length <= 1) return;
        int maxVal = Arrays.stream(arr).max().getAsInt();
        for (int exp = 1; maxVal / exp > 0; exp *= 10) {
            countingSortByDigit(arr, exp);
        }
    }

    public static void main(String[] args) {
        int[] arr = {170, 45, 75, 90, 802, 24, 2, 66};
        radixSort(arr);
        System.out.println(Arrays.toString(arr));
    }
}
```

### Python
```python
def counting_sort_by_digit(arr: list[int], exp: int) -> None:
    n = len(arr)
    output = [0] * n
    count = [0] * 10

    for i in range(n):
        digit = (arr[i] // exp) % 10
        count[digit] += 1

    for i in range(1, 10):
        count[i] += count[i - 1]

    for i in range(n - 1, -1, -1):
        digit = (arr[i] // exp) % 10
        output[count[digit] - 1] = arr[i]
        count[digit] -= 1

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

if __name__ == "__main__":
    data = [170, 45, 75, 90, 802, 24, 2, 66]
    radix_sort(data)
    print(data)
```

### JavaScript
```javascript
function countingSortByDigit(arr, exp) {
    const n = arr.length;
    const output = new Array(n).fill(0);
    const count = new Array(10).fill(0);

    for (let i = 0; i < n; i++) {
        const digit = Math.floor(arr[i] / exp) % 10;
        count[digit]++;
    }

    for (let i = 1; i < 10; i++) {
        count[i] += count[i - 1];
    }

    for (let i = n - 1; i >= 0; i--) {
        const digit = Math.floor(arr[i] / exp) % 10;
        output[count[digit] - 1] = arr[i];
        count[digit]--;
    }

    for (let i = 0; i < n; i++) {
        arr[i] = output[i];
    }
}

function radixSort(arr) {
    if (arr.length <= 1) return arr;
    const maxVal = Math.max(...arr);
    for (let exp = 1; Math.floor(maxVal / exp) > 0; exp *= 10) {
        countingSortByDigit(arr, exp);
    }
    return arr;
}

const arr = [170, 45, 75, 90, 802, 24, 2, 66];
radixSort(arr);
console.log(arr);
```

## 10. Code Explanation

The algorithm works by using a nested execution structure. The outer loop calculates the maximum value to know the total number of digit passes needed. In each pass, `exp` increases by a factor of 10 (`1, 10, 100, ...`). The helper `countingSortByDigit` extracts the digit at the `exp` position using `(element / exp) % 10`. It then constructs a frequency map, calculates prefix sums for exact positioning, and populates an auxiliary output array backward from right to left to guarantee stability. Finally, it copies the intermediate sorted results back into the primary array.

## 11. Interactive Demo

The demo displays 10 vertical bucket bins labeled 0 through 9. Below the bins, an array of multi-digit numbers is rendered as cards. 

When "Start Sort" is clicked:
1. The active digit position (Ones, Tens, Hundreds) is highlighted on each card.
2. Cards animate sequentially from the input row into their corresponding bucket bin according to their highlighted digit.
3. Once all cards enter bins, they slide out from bucket 0 to bucket 9 back into the main array bar.
4. The active digit indicator shifts one column to the left, and the process repeats until all digits are sorted, culminating in a celebration glow.

## 12. Dry Run

**Sample Input:** `[170, 45, 75, 90]`
Max value: `170` ($d = 3$ passes: `exp = 1, 10, 100`)

| Pass (`exp`) | Element Processed | Extracted Digit | Count Array (`0-9`) | Reassembled Output Array |
| :--- | :--- | :--- | :--- | :--- |
| `exp = 1` | `170, 45, 75, 90` | `0, 5, 5, 0` | `[2,0,0,0,0,2,0,0,0,0]` | `[170, 90, 45, 75]` |
| `exp = 10` | `170, 90, 45, 75` | `7, 9, 4, 7` | `[0,0,0,0,1,0,0,2,0,1]` | `[45, 170, 75, 90]` |
| `exp = 100`| `45, 170, 75, 90` | `0, 1, 0, 0` | `[3,1,0,0,0,0,0,0,0,0]` | `[45, 75, 90, 170]` |

**Final Sorted Output:** `[45, 75, 90, 170]`

## 13. Time & Space Complexity

| Case | Time Complexity | Reason |
| :--- | :--- | :--- |
| **Best Case** | $\mathcal{O}(d \cdot (n + k))$ | All passes complete $d$ digit iterations |
| **Average Case** | $\mathcal{O}(d \cdot (n + k))$ | Digits are distributed uniformly across bases |
| **Worst Case** | $\mathcal{O}(d \cdot (n + k))$ | Always performs $d$ full digit passes |
| **Space Complexity** | $\mathcal{O}(n + k)$ | Auxiliary memory needed for `output` and `count` arrays |

Where $d$ is number of digits in max key, $n$ is array length, and $k$ is the radix base (10).

## 14. Advantages

- **Linear Speed for Small $d$:** Outperforms $\mathcal{O}(n \log n)$ algorithms when $d \ll n$.
- **Stable Sorting:** Maintains initial relative order of equal key values.
- **Non-Comparison Based:** Eliminates overhead of custom comparator function calls.
- **Highly Parallelizable:** Ideal for high-throughput GPU sorting algorithms.

## 15. Disadvantages

- **Poor Performance for Large Keys:** If keys have many digits ($d \approx n$), complexity degrades to $\mathcal{O}(n^2)$.
- **Extra Memory Required:** Needs $\mathcal{O}(n + k)$ auxiliary space, unlike in-place sorts like Quick Sort.
- **Limited Key Types:** Requires keys that can be partitioned into discrete digits or characters (integers, strings, fixed-length bytes).
- **High Constant Factor:** For small $n$, overhead of repeated counting sort passes makes it slower than Quick Sort.

## 16. Applications

- GPU-accelerated sorting algorithms in computer graphics.
- Sorting IP and MAC addresses in telecommunications.
- Suffix array construction for genomic sequence alignment.
- Fixed-length key sorting in embedded database engines.

## 17. Common Mistakes

- **Using Unstable Subroutine:** Replacing Counting Sort with an unstable sort breaks the entire algorithm.
- **Ignoring Negative Numbers:** Standard implementation fails on negative integers without offset transformation.
- **Incorrect Exponent Termination:** Using `<` instead of `<=` or floating point division truncation bugs.
- **Assuming $\mathcal{O}(n)$ Always:** Forgetting that $d$ grows logarithmically with maximum key size ($d = \log_k(\text{max\_val})$).

## 18. Interview Questions

1. How does Radix Sort bypass the $\mathcal{O}(n \log n)$ lower bound of comparison-based sorting algorithms?
2. Why must the digit sorting subroutine be stable in Radix Sort?
3. How can you modify Radix Sort to handle negative integers?
4. Compare Radix Sort and Quick Sort for sorting 1,000,000 32-bit integers.
5. What is the difference between LSD (Least Significant Digit) and MSD (Most Significant Digit) Radix Sort?
6. What happens to Radix Sort time complexity if key lengths equal array length ($d = n$)?
7. How do you choose an optimal radix base $k$ for performance tuning?
8. Can Radix Sort be implemented in-place without auxiliary arrays?
9. Explain how Radix Sort can sort floating-point numbers.
10. Write an MSD Radix Sort function for string sorting.

## 19. Practice Problems

**Easy:**
1. Implement LSD Radix Sort for a list of non-negative 3-digit integers.
2. Modify Radix Sort to sort numbers in descending order.
3. Adapt Radix Sort to sort an array of lower-case 4-letter strings.

**Medium:**
4. Implement Radix Sort capable of handling both positive and negative integers.
5. Implement Radix Sort using base 256 (byte-by-byte sorting) for 32-bit integers.
6. Count total digit pass iterations performed for a given dataset.

**Hard:**
7. Implement an MSD (Most Significant Digit) recursive Radix Sort for strings of variable length.
8. Sort floating-point numbers in $\mathcal{O}(n)$ time using bit conversion and Radix Sort.

## 20. Related Algorithms

- [Counting Sort](./18_Counting_sort.md) (The stable inner subroutine for Radix Sort)
- [Bucket Sort](./20_bucket_sort.md) (Distributes elements into buckets based on ranges)
- [Quick Sort](./15_quick_sort.md) (Comparison-based divide-and-conquer alternative)

## 21. Summary

Radix Sort is a non-comparison linear sorting algorithm that processes elements digit by digit using a stable subroutine like Counting Sort. By processing from LSD to MSD (or MSD to LSD), it achieves $\mathcal{O}(d \cdot (n + k))$ time complexity. It excels when sorting large quantities of fixed-length keys like integers, IP addresses, or strings, providing an efficient alternative to traditional comparison-based sorting algorithms.

## 22. Quiz

**Question 1:** What is the worst-case time complexity of Radix Sort?
- A) $\mathcal{O}(n^2)$
- B) $\mathcal{O}(n \log n)$
- C) $\mathcal{O}(d \cdot (n + k))$
- D) $\mathcal{O}(1)$
- **Correct Answer:** C
- **Explanation:** Radix Sort executes $d$ passes, each taking $\mathcal{O}(n + k)$ time.

**Question 2:** Why must the subroutine used in Radix Sort be stable?
- A) To prevent integer overflow
- B) To preserve ordering established by previously processed lower-significant digits
- C) To reduce space complexity to $\mathcal{O}(1)$
- D) To allow sorting negative numbers
- **Correct Answer:** B
- **Explanation:** Stability guarantees that sorting by a higher digit does not destroy the ordering established by lower digits.

**Question 3:** What does $d$ represent in Radix Sort complexity $\mathcal{O}(d \cdot (n + k))$?
- A) Array length
- B) Number of buckets
- C) Maximum number of digits/positions in elements
- D) Depth of recursion
- **Correct Answer:** C
- **Explanation:** $d$ represents key length or digit count of the largest number.

**Question 4:** What is the space complexity of standard LSD Radix Sort?
- A) $\mathcal{O}(1)$
- B) $\mathcal{O}(\log n)$
- C) $\mathcal{O}(n + k)$
- D) $\mathcal{O}(n^2)$
- **Correct Answer:** C
- **Explanation:** Memory is needed for the intermediate output array ($\mathcal{O}(n)$) and count buckets ($\mathcal{O}(k)$).

**Question 5:** Which subroutine is most commonly used inside LSD Radix Sort?
- A) Quick Sort
- B) Counting Sort
- C) Heap Sort
- D) Binary Search
- **Correct Answer:** B
- **Explanation:** Counting Sort is stable, non-comparison based, and runs in linear time per digit pass.

**Question 6:** What direction does LSD Radix Sort process digits?
- A) Most Significant Digit to Least Significant Digit
- B) Least Significant Digit to Most Significant Digit
- C) Random digit order
- D) Middle digit outward
- **Correct Answer:** B
- **Explanation:** LSD stands for Least Significant Digit, processing right to left.

**Question 7:** If $d \approx n$, what is the effective time complexity of Radix Sort?
- A) $\mathcal{O}(n)$
- B) $\mathcal{O}(n \log n)$
- C) $\mathcal{O}(n^2)$
- D) $\mathcal{O}(2^n)$
- **Correct Answer:** C
- **Explanation:** Substituting $d = n$ into $\mathcal{O}(d \cdot n)$ yields $\mathcal{O}(n^2)$.

**Question 8:** Can standard Radix Sort directly sort floating point numbers?
- A) Yes, without any modification
- B) No, unless bit representations or offsets are mapped first
- C) Only positive floats under 1.0
- D) Only integer floats
- **Correct Answer:** B
- **Explanation:** Floats require bitwise mapping to preserve sign and exponent ordering before radix digit extraction.

**Question 9:** Is standard iterative LSD Radix Sort an in-place algorithm?
- A) Yes
- B) No
- C) Only for arrays under 10 elements
- D) Only in C++
- **Correct Answer:** B
- **Explanation:** It requires auxiliary arrays for bucket counts and temporary output storage.

**Question 10:** Which key type is Radix Sort LEAST suited for?
- A) 32-bit integers
- B) Fixed-length string codes
- C) Arbitrary variable-length data with huge key ranges
- D) IP addresses
- **Correct Answer:** C
- **Explanation:** Huge key length ranges ($d$) cause performance degradation.
