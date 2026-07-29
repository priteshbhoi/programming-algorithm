# Tim Sort

## 1. Introduction

Tim Sort is a adaptive, hybrid sorting algorithm derived from Merge Sort and Insertion Sort. Designed by Tim Peters in 2002 for the Python programming language, it is engineered to perform exceptionally well on many kinds of real-world data, which often contain existing ordered subsequences (called "runs").

Imagine sorting a huge pile of papers that are already partially arranged in small sorted stacks. Instead of shuffling everything randomly to start a fresh sort, you identify the natural contiguous sorted sections, extend tiny sections using Insertion Sort to a minimum run size (typically 32 or 64), and then merge these runs systematically using an optimized Merge Sort routine. That is how Tim Sort works.

It was created to deliver an optimal $\mathcal{O}(n \log n)$ worst-case comparison sort while achieving $\mathcal{O}(n)$ linear time on real-world partially sorted arrays.

Today, Tim Sort is the default standard sorting algorithm in Python (`list.sort()`, `sorted()`), Java (`Arrays.sort()` for non-primitives), Android SDK, and Chrome's V8 JavaScript engine (`Array.prototype.sort()`).

## 2. Why Use This Algorithm?

Tim Sort combines the best of both worlds: the low overhead of Insertion Sort on small arrays with the logarithmic scaling of Merge Sort on large arrays.

**Benefits:**
- **Adaptive Linear Time on Ordered Data:** Runs in $\mathcal{O}(n)$ time when input is already sorted or reverse sorted.
- **Strictly Stable:** Never reverses the relative order of duplicate elements.
- **Worst-Case Guarantee:** Guaranteed $\mathcal{O}(n \log n)$ performance even under pathological data distributions.
- **Galloping Mode Optimization:** Skips long contiguous blocks of elements during merging to drastically reduce comparison operations.

**Performance:**
- **Best Case:** $\mathcal{O}(n)$ (when array is already sorted or contains long natural runs)
- **Average Case:** $\mathcal{O}(n \log n)$
- **Worst Case:** $\mathcal{O}(n \log n)$
- **Space Complexity:** $\mathcal{O}(n)$ auxiliary space for run merging buffers.

**When it is better than other algorithms:**
Tim Sort is superior to standard Quick Sort and pure Merge Sort on real-world datasets because real data rarely consists of purely random numbers—it frequently contains pre-sorted segments.

## 3. Real-World Applications

- **Python Standard Library:** Built-in engine for `sort()` and `sorted()`.
- **Java Platform:** Standard sorting implementation for objects (`Arrays.sort(Object[])`, `Collections.sort()`).
- **Android Runtime (ART):** Default sorting algorithm for Java objects across Android systems.
- **V8 JavaScript Engine:** Powers `Array.prototype.sort()` in Google Chrome and Node.js.
- **Rust Standard Library:** Hybrid stable sorting algorithms inspired by Tim Sort principles.

## 4. Prerequisites

Before learning Tim Sort, you should be comfortable with:
- [Insertion Sort](./13_insertion_sort.md) and Binary Insertion Sort.
- [Merge Sort](./14_merge_sort.md) and two-pointer array merging logic.
- Call stack management and memory buffering concepts.

## 5. Visualization

Given Array: `[5, 21, 7, 23, 19, 1, 83, 12]` with `MIN_RUN = 4`

1. **Partition & Extend into Runs:**
   - Scan `[5, 21]`: Increasing run. Extend to length 4 using Insertion Sort -> Run 1: `[5, 7, 21, 23]`
   - Scan `[19, 1, 83, 12]`: Sort block to length 4 using Insertion Sort -> Run 2: `[1, 12, 19, 83]`

2. **Merge Runs:**
   - Merge Run 1 `[5, 7, 21, 23]` and Run 2 `[1, 12, 19, 83]`
   - Final Result: `[1, 5, 7, 12, 19, 21, 23, 83]`

## 6. How It Works

1. **Determine `minrun`:** Calculate a minimum run size $RUN \in [32, 64]$ based on total array length $n$ such that $n / RUN$ is at or slightly below a power of 2.
2. **Identify / Build Runs:**
   - Traverse array to find natural ascending or strictly descending contiguous sub-arrays. (Reverse descending runs in-place to make them ascending).
   - If a run is shorter than `minrun`, extend it to `minrun` elements using Binary Insertion Sort.
3. **Push Runs onto Stack:** Store run metadata `(start_index, run_length)` on a stack.
4. **Merge Runs with Invariants:** Merge adjacent runs on the stack while enforcing balance rules to maintain $\mathcal{O}(n \log n)$ merge hierarchy.

## 7. Step-by-Step Algorithm

1. Calculate `minrun = getMinRun(n)`.
2. Iterate `i` from `0` to `n - 1` with step size equal to run lengths:
   - Sort chunk `arr[i ... min(i + minrun - 1, n - 1)]` using Insertion Sort.
3. Set merge size `size = minrun`.
4. While `size < n`:
   - Merge sub-arrays of size `size` in pairs (left chunk `[left...left+size-1]`, right chunk `[left+size...left+2*size-1]`).
   - Double `size *= 2`.
5. Array is completely sorted.

## 8. Pseudocode

```text
function timSort(arr):
    n = length(arr)
    minRun = calculateMinRun(n)
    
    // Step 1: Sort individual sub-arrays of size minRun using Insertion Sort
    for i = 0 to n - 1 step minRun:
        right = min(i + minRun - 1, n - 1)
        insertionSort(arr, i, right)
        
    // Step 2: Merge sorted runs
    size = minRun
    while size < n:
        for left = 0 to n - 1 step 2 * size:
            mid = min(left + size - 1, n - 1)
            right = min(left + 2 * size - 1, n - 1)
            
            if mid < right:
                merge(arr, left, mid, right)
                
        size = 2 * size

function calculateMinRun(n):
    r = 0
    while n >= 32:
        r = r bitwise-or (n bitwise-and 1)
        n = n >> 1
    return n + r
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>

const int RUN = 32;

int minVal(int a, int b) { return (a < b) ? a : b; }

void insertionSort(int arr[], int left, int right) {
    for (int i = left + 1; i <= right; i++) {
        int temp = arr[i];
        int j = i - 1;
        while (j >= left && arr[j] > temp) {
            arr[j + 1] = arr[j];
            j--;
        }
        arr[j + 1] = temp;
    }
}

void merge(int arr[], int l, int m, int r) {
    int len1 = m - l + 1, len2 = r - m;
    int* left = (int*)malloc(len1 * sizeof(int));
    int* right = (int*)malloc(len2 * sizeof(int));

    for (int i = 0; i < len1; i++) left[i] = arr[l + i];
    for (int i = 0; i < len2; i++) right[i] = arr[m + 1 + i];

    int i = 0, j = 0, k = l;
    while (i < len1 && j < len2) {
        if (left[i] <= right[j]) arr[k++] = left[i++];
        else arr[k++] = right[j++];
    }
    while (i < len1) arr[k++] = left[i++];
    while (j < len2) arr[k++] = right[j++];

    free(left);
    free(right);
}

void timSort(int arr[], int n) {
    for (int i = 0; i < n; i += RUN)
        insertionSort(arr, i, minVal(i + RUN - 1, n - 1));

    for (int size = RUN; size < n; size = 2 * size) {
        for (int left = 0; left < n; left += 2 * size) {
            int mid = left + size - 1;
            int right = minVal(left + 2 * size - 1, n - 1);
            if (mid < right)
                merge(arr, left, mid, right);
        }
    }
}

int main() {
    int arr[] = {5, 21, 7, 23, 19, 1, 83, 12, 44, 3, 9, 15};
    int n = sizeof(arr) / sizeof(arr[0]);
    timSort(arr, n);
    for (int i = 0; i < n; i++) printf("%d ", arr[i]);
    printf("\n");
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

const int RUN = 32;

void insertionSort(std::vector<int>& arr, int left, int right) {
    for (int i = left + 1; i <= right; i++) {
        int temp = arr[i];
        int j = i - 1;
        while (j >= left && arr[j] > temp) {
            arr[j + 1] = arr[j];
            j--;
        }
        arr[j + 1] = temp;
    }
}

void merge(std::vector<int>& arr, int l, int m, int r) {
    int len1 = m - l + 1, len2 = r - m;
    std::vector<int> left(len1), right(len2);

    for (int i = 0; i < len1; i++) left[i] = arr[l + i];
    for (int i = 0; i < len2; i++) right[i] = arr[m + 1 + i];

    int i = 0, j = 0, k = l;
    while (i < len1 && j < len2) {
        if (left[i] <= right[j]) arr[k++] = left[i++];
        else arr[k++] = right[j++];
    }
    while (i < len1) arr[k++] = left[i++];
    while (j < len2) arr[k++] = right[j++];
}

void timSort(std::vector<int>& arr) {
    int n = arr.size();
    for (int i = 0; i < n; i += RUN)
        insertionSort(arr, i, std::min(i + RUN - 1, n - 1));

    for (int size = RUN; size < n; size = 2 * size) {
        for (int left = 0; left < n; left += 2 * size) {
            int mid = left + size - 1;
            int right = std::min(left + 2 * size - 1, n - 1);
            if (mid < right)
                merge(arr, left, mid, right);
        }
    }
}

int main() {
    std::vector<int> arr = {5, 21, 7, 23, 19, 1, 83, 12, 44, 3, 9, 15};
    timSort(arr);
    for (int x : arr) std::cout << x << " ";
    std::cout << "\n";
    return 0;
}
```

### Java
```java
import java.util.Arrays;

public class TimSort {
    private static final int RUN = 32;

    private static void insertionSort(int[] arr, int left, int right) {
        for (int i = left + 1; i <= right; i++) {
            int temp = arr[i];
            int j = i - 1;
            while (j >= left && arr[j] > temp) {
                arr[j + 1] = arr[j];
                j--;
            }
            arr[j + 1] = temp;
        }
    }

    private static void merge(int[] arr, int l, int m, int r) {
        int len1 = m - l + 1, len2 = r - m;
        int[] left = new int[len1];
        int[] right = new int[len2];

        System.arraycopy(arr, l, left, 0, len1);
        System.arraycopy(arr, m + 1, right, 0, len2);

        int i = 0, j = 0, k = l;
        while (i < len1 && j < len2) {
            if (left[i] <= right[j]) arr[k++] = left[i++];
            else arr[k++] = right[j++];
        }
        while (i < len1) arr[k++] = left[i++];
        while (j < len2) arr[k++] = right[j++];
    }

    public static void timSort(int[] arr) {
        int n = arr.length;
        for (int i = 0; i < n; i += RUN) {
            insertionSort(arr, i, Math.min(i + RUN - 1, n - 1));
        }

        for (int size = RUN; size < n; size = 2 * size) {
            for (int left = 0; left < n; left += 2 * size) {
                int mid = left + size - 1;
                int right = Math.min(left + 2 * size - 1, n - 1);
                if (mid < right) {
                    merge(arr, left, mid, right);
                }
            }
        }
    }

    public static void main(String[] args) {
        int[] arr = {5, 21, 7, 23, 19, 1, 83, 12, 44, 3, 9, 15};
        timSort(arr);
        System.out.println(Arrays.toString(arr));
    }
}
```

### Python
```python
RUN = 32

def insertion_sort(arr: list[int], left: int, right: int) -> None:
    for i in range(left + 1, right + 1):
        temp = arr[i]
        j = i - 1
        while j >= left and arr[j] > temp:
            arr[j + 1] = arr[j]
            j -= 1
        arr[j + 1] = temp

def merge(arr: list[int], l: int, m: int, r: int) -> None:
    left = arr[l : m + 1]
    right = arr[m + 1 : r + 1]

    i = j = 0
    k = l

    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            arr[k] = left[i]
            i += 1
        else:
            arr[k] = right[j]
            j += 1
        k += 1

    while i < len(left):
        arr[k] = left[i]
        i += 1
        k += 1

    while j < len(right):
        arr[k] = right[j]
        j += 1
        k += 1

def tim_sort(arr: list[int]) -> list[int]:
    n = len(arr)

    for i in range(0, n, RUN):
        insertion_sort(arr, i, min(i + RUN - 1, n - 1))

    size = RUN
    while size < n:
        for left in range(0, n, 2 * size):
            mid = left + size - 1
            right = min(left + 2 * size - 1, n - 1)
            if mid < right:
                merge(arr, left, mid, right)
        size *= 2

    return arr

if __name__ == "__main__":
    data = [5, 21, 7, 23, 19, 1, 83, 12, 44, 3, 9, 15]
    tim_sort(data)
    print(data)
```

### JavaScript
```javascript
const RUN = 32;

function insertionSort(arr, left, right) {
    for (let i = left + 1; i <= right; i++) {
        const temp = arr[i];
        let j = i - 1;
        while (j >= left && arr[j] > temp) {
            arr[j + 1] = arr[j];
            j--;
        }
        arr[j + 1] = temp;
    }
}

function merge(arr, l, m, r) {
    const left = arr.slice(l, m + 1);
    const right = arr.slice(m + 1, r + 1);

    let i = 0, j = 0, k = l;
    while (i < left.length && j < right.length) {
        if (left[i] <= right[j]) {
            arr[k++] = left[i++];
        } else {
            arr[k++] = right[j++];
        }
    }
    while (i < left.length) arr[k++] = left[i++];
    while (j < right.length) arr[k++] = right[j++];
}

function timSort(arr) {
    const n = arr.length;

    for (let i = 0; i < n; i += RUN) {
        insertionSort(arr, i, Math.min(i + RUN - 1, n - 1));
    }

    for (let size = RUN; size < n; size = 2 * size) {
        for (let left = 0; left < n; left += 2 * size) {
            const mid = left + size - 1;
            const right = Math.min(left + 2 * size - 1, n - 1);
            if (mid < right) {
                merge(arr, left, mid, right);
            }
        }
    }
    return arr;
}

const data = [5, 21, 7, 23, 19, 1, 83, 12, 44, 3, 9, 15];
timSort(data);
console.log(data);
```

## 10. Code Explanation

Tim Sort partitions an un-sorted array into blocks of `RUN` size (typically 32 to 64 elements). For small arrays, Insertion Sort outperforms divide-and-conquer algorithms because of minimal constant factors and fast memory access. Once each individual chunk of size `RUN` is locally sorted via `insertionSort`, a modified, stable bottom-up `merge` pass pairs up contiguous blocks, doubling the merge window (`size = RUN, 2*RUN, 4*RUN, ...`) until the array is globally sorted.

## 11. Interactive Demo

The user sees a long bar chart displaying data values. The control panel includes a "MinRun" slider (8, 16, 32, 64) and speed control.

1. **Phase 1 (Insertion Pass):** Glowing rectangular boxes highlight individual chunks of size `minrun`. Within each highlighted box, bars shift left and right via Insertion Sort.
2. **Phase 2 (Merge Pass):** Adjacent sorted blocks turn matching colors (Blue & Green) and zip together into a larger combined block (Purple).

## 12. Dry Run

**Input:** `[4, 2, 8, 6, 1, 9]` with `RUN = 2`

| Phase | Action | Array State |
| :--- | :--- | :--- |
| **Insertion 1** | Sort chunk `[0..1]` | `[2, 4, 8, 6, 1, 9]` |
| **Insertion 2** | Sort chunk `[2..3]` | `[2, 4, 6, 8, 1, 9]` |
| **Insertion 3** | Sort chunk `[4..5]` | `[2, 4, 6, 8, 1, 9]` |
| **Merge 1** | Merge `[2, 4]` and `[6, 8]` | `[2, 4, 6, 8, 1, 9]` |
| **Merge 2** | Merge `[2, 4, 6, 8]` and `[1, 9]` | `[1, 2, 4, 6, 8, 9]` |

## 13. Time & Space Complexity

| Case | Time Complexity | Reason |
| :--- | :--- | :--- |
| **Best Case** | $\mathcal{O}(n)$ | Pre-sorted data requires 0 insertions & linear run checks |
| **Average Case** | $\mathcal{O}(n \log n)$ | Balanced merges across $\log(n / \text{minrun})$ levels |
| **Worst Case** | $\mathcal{O}(n \log n)$ | Guaranteed by Merge Sort upper bound |
| **Space Complexity** | $\mathcal{O}(n)$ | Temporary merge buffer storage |

## 14. Advantages

- **Adaptive:** Achieves $\mathcal{O}(n)$ performance on partially sorted real-world datasets.
- **Stable:** Guarantees equal elements maintain initial order.
- **Battle-Tested:** Used as the default sorting algorithm across standard systems libraries (Python, Java, Android, V8).

## 15. Disadvantages

- **Auxiliary Space:** Requires $\mathcal{O}(n)$ memory space for merge buffers.
- **Implementation Complexity:** Full production-grade Tim Sort with galloping mode is complex to write from scratch.

## 16. Applications

- Standard library sort in Python (`list.sort()`).
- Object sorting in Java (`Arrays.sort()`).
- Chrome V8 JavaScript Engine (`Array.prototype.sort()`).

## 17. Common Mistakes

- **Choosing Fixed Non-Optimal `MIN_RUN`:** Using arbitrary numbers instead of powers of 2 or calculation logic.
- **Unstable Merge Implementation:** Failing to use `<=` during element comparison in the merge step.

## 18. Interview Questions

1. Why was Tim Sort designed to combine Insertion Sort and Merge Sort?
2. What is "Galloping Mode" in production Tim Sort?
3. What is the time complexity of Tim Sort on an already sorted array?
4. How does Tim Sort decide the value of `minrun`?
5. Why is Tim Sort preferred over Quick Sort for sorting objects in Java?

## 19. Practice Problems

**Easy:**
1. Implement a basic version of Tim Sort with fixed `RUN = 32`.
2. Modify Tim Sort to count total comparisons performed.

**Medium:**
3. Implement `calculateMinRun(n)` function using bitwise shifts.
4. Add binary insertion sort inside Tim Sort run creation.

**Hard:**
5. Implement Galloping Mode in C++ or Java to optimize merging long sorted runs.

## 20. Related Algorithms

- [Merge Sort](./14_merge_sort.md) (Foundation for Tim Sort's merge pass)
- [Insertion Sort](./13_insertion_sort.md) (Foundation for Tim Sort's run creation)
- [IntroSort](./22_introsort.md) (Hybrid sorting alternative based on Quick Sort)

## 21. Summary

Tim Sort is a highly optimized, adaptive hybrid sorting algorithm combining Insertion Sort and Merge Sort. By detecting natural sorted runs and extending small chunks to a minimum run size, it achieves linear time $\mathcal{O}(n)$ on pre-sorted data while maintaining a worst-case guarantee of $\mathcal{O}(n \log n)$ and full algorithm stability.

## 22. Quiz

**Question 1:** Who designed Tim Sort and in what year?
- A) Tony Hoare in 1960
- B) Tim Peters in 2002
- C) Donald Knuth in 1973
- D) Niklaus Wirth in 1985
- **Correct Answer:** B
- **Explanation:** Tim Peters created Tim Sort in 2002 for CPython.

**Question 2:** What is the best-case time complexity of Tim Sort?
- A) $\mathcal{O}(1)$
- B) $\mathcal{O}(n)$
- C) $\mathcal{O}(n \log n)$
- D) $\mathcal{O}(n^2)$
- **Correct Answer:** B
- **Explanation:** On pre-sorted arrays, Tim Sort identifies single runs and finishes in $\mathcal{O}(n)$ time.

**Question 3:** What two algorithms are combined to form Tim Sort?
- A) Quick Sort and Heap Sort
- B) Insertion Sort and Merge Sort
- C) Bubble Sort and Selection Sort
- D) Counting Sort and Radix Sort
- **Correct Answer:** B
- **Explanation:** Tim Sort uses Insertion Sort for small blocks and Merge Sort for joining runs.

**Question 4:** What is the typical range for `minrun` in Tim Sort?
- A) 2 to 8
- B) 32 to 64
- C) 512 to 1024
- D) 1000 to 5000
- **Correct Answer:** B
- **Explanation:** `minrun` is chosen in range `[32, 64]` so array splits cleanly into powers of 2.

**Question 5:** Is Tim Sort a stable sorting algorithm?
- A) Yes
- B) No
- **Correct Answer:** A
- **Explanation:** Both Insertion Sort and Merge Sort subroutines are stable, preserving equal element order.

**Question 6:** Which major programming language uses Tim Sort as its default `list.sort()` algorithm?
- A) C
- B) Python
- C) Go
- D) Assembly
- **Correct Answer:** B
- **Explanation:** Tim Sort was created specifically for Python.

**Question 7:** What is the space complexity of Tim Sort?
- A) $\mathcal{O}(1)$
- B) $\mathcal{O}(\log n)$
- C) $\mathcal{O}(n)$
- D) $\mathcal{O}(n^2)$
- **Correct Answer:** C
- **Explanation:** It requires temporary array buffers to perform stable merging of runs.

**Question 8:** What optimization mechanism does Tim Sort use when one run consistently wins during merging?
- A) Quickselect
- B) Galloping Mode
- C) Binary Search Tree insertion
- D) Heapify
- **Correct Answer:** B
- **Explanation:** Galloping mode uses binary search to jump over large consecutive winning blocks.

**Question 9:** Why does Tim Sort use Insertion Sort for small subarrays instead of recursive Merge Sort?
- A) Insertion Sort uses less code
- B) Insertion Sort has lower constant factor overhead and fast cache performance on small arrays
- C) Insertion Sort handles floating-point numbers better
- D) Merge Sort cannot handle small arrays
- **Correct Answer:** B
- **Explanation:** For $n < 64$, Insertion Sort is faster than recursive function calls.

**Question 10:** What is the worst-case time complexity of Tim Sort?
- A) $\mathcal{O}(n^2)$
- B) $\mathcal{O}(n \log n)$
- C) $\mathcal{O}(n)$
- D) $\mathcal{O}(n!)$
- **Correct Answer:** B
- **Explanation:** Merge Sort structure ensures an upper performance bound of $\mathcal{O}(n \log n)$.
