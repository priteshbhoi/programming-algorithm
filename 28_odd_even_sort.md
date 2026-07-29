# Odd-Even Sort

## 1. Introduction

Odd-Even Sort (also known as Brick Sort or Odd-Even Transposition Sort) is a parallel comparison sorting algorithm developed by N. Habermann in 1972 for execution on parallel processors with local interconnections. It is a variation of Bubble Sort that operates in two alternating phases: the **Odd Phase** and the **Even Phase**.

Imagine a line of soldiers standing side by side. On the commander's first whistle ("Odd Phase"), every soldier at an odd position compares height with the soldier immediately to their right and swaps if out of order. On the second whistle ("Even Phase"), every soldier at an even position compares height with the soldier to their right and swaps. The commander alternates whistles until the entire line is perfectly ordered.

It was created to enable concurrent parallel comparisons across multiple processor cores without data access collisions.

You should study Odd-Even Sort to understand parallel sorting models, array transposition networks, and concurrent comparison patterns.

## 2. Why Use This Algorithm?

Odd-Even Sort allows multiple pairs of adjacent elements to be compared and swapped simultaneously in parallel.

**Benefits:**
- **Parallel Computing Suitable:** In a parallel computing model with $n$ processors, it sorts in $\mathcal{O}(n)$ time and $\mathcal{O}(n)$ parallel steps.
- **In-Place & Stable:** Requires $\mathcal{O}(1)$ space and preserves relative order of equal elements.
- **Simple Alternating Pattern:** Clean separation between odd-indexed and even-indexed comparison pairs.

**Performance:**
- **Best Case:** $\mathcal{O}(n)$ (when array is already sorted)
- **Average Case:** $\mathcal{O}(n^2)$ (sequential) / $\mathcal{O}(n)$ (parallel with $n$ processors)
- **Worst Case:** $\mathcal{O}(n^2)$ (sequential) / $\mathcal{O}(n)$ (parallel with $n$ processors)
- **Space Complexity:** $\mathcal{O}(1)$ auxiliary space.

**When it is better than standard Bubble Sort:**
Odd-Even Sort is vastly superior on multi-processor or GPU hardware arrays because all comparisons within an odd or even pass can execute concurrently in parallel threads.

## 3. Real-World Applications

- **Parallel Computing & SIMD Hardware:** Multi-core processors, GPUs, and FPGA sorting networks.
- **Interconnection Networks:** Mesh-connected processor arrays where adjacent nodes exchange data.
- **Hardware Circuit Design:** Designing physical hardware sorting networks with fixed comparator units.

## 4. Prerequisites

Before studying Odd-Even Sort, you should understand:
- Standard [Bubble Sort](./11_bubble_sort.md).
- Parity of array indices (odd vs even).
- Concepts of parallel computing and concurrent execution.

## 5. Visualization

Given Array: `[3, 2, 38, 5, 47, 15, 36, 26]`

1. **Odd Phase (Indices `1, 3, 5` compared with `2, 4, 6`):**
   - Compare `arr[1]` (2) & `arr[2]` (38) -> No swap
   - Compare `arr[3]` (5) & `arr[4]` (47) -> No swap
   - Compare `arr[5]` (15) & `arr[6]` (36) -> No swap

2. **Even Phase (Indices `0, 2, 4, 6` compared with `1, 3, 5, 7`):**
   - Compare `arr[0]` (3) & `arr[1]` (2) -> Swap `[2, 3, 38, 5, 47, 15, 36, 26]`
   - Compare `arr[2]` (38) & `arr[3]` (5) -> Swap `[2, 3, 5, 38, 47, 15, 36, 26]`
   - Compare `arr[4]` (47) & `arr[5]` (15) -> Swap `[2, 3, 5, 38, 15, 47, 36, 26]`
   - Compare `arr[6]` (36) & `arr[7]` (26) -> Swap `[2, 3, 5, 38, 15, 47, 26, 36]`

3. Alternate Odd and Even phases until a full cycle occurs with zero swaps.

## 6. How It Works

1. Set `isSorted = false`.
2. While `isSorted` is false:
   - Set `isSorted = true`.
   - **Odd Phase:** Iterate `i` from `1` to `n - 2` with step `2`. Compare `arr[i]` and `arr[i + 1]`. If `arr[i] > arr[i + 1]`, swap them and set `isSorted = false`.
   - **Even Phase:** Iterate `i` from `0` to `n - 2` with step `2`. Compare `arr[i]` and `arr[i + 1]`. If `arr[i] > arr[i + 1]`, swap them and set `isSorted = false`.
3. Repeat until both Odd and Even phases execute without any swaps occurring.

## 7. Step-by-Step Algorithm

1. `isSorted = false`.
2. Loop while `isSorted == false`:
   1. `isSorted = true`.
   2. **Odd Phase:** Loop `i` from `1` to `n - 2` step 2:
      - If `arr[i] > arr[i + 1]`: Swap `arr[i]` and `arr[i + 1]`, `isSorted = false`.
   3. **Even Phase:** Loop `i` from `0` to `n - 2` step 2:
      - If `arr[i] > arr[i + 1]`: Swap `arr[i]` and `arr[i + 1]`, `isSorted = false`.
3. Array is sorted.

## 8. Pseudocode

```text
function oddEvenSort(arr):
    n = length(arr)
    isSorted = false

    while not isSorted:
        isSorted = true

        // Odd Phase
        for i = 1 to n - 2 step 2:
            if arr[i] > arr[i + 1]:
                swap(arr[i], arr[i + 1])
                isSorted = false

        // Even Phase
        for i = 0 to n - 2 step 2:
            if arr[i] > arr[i + 1]:
                swap(arr[i], arr[i + 1])
                isSorted = false
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdbool.h>

void swap(int* a, int* b) {
    int t = *a; *a = *b; *b = t;
}

void oddEvenSort(int arr[], int n) {
    bool isSorted = false;

    while (!isSorted) {
        isSorted = true;

        // Odd Phase
        for (int i = 1; i <= n - 2; i += 2) {
            if (arr[i] > arr[i + 1]) {
                swap(&arr[i], &arr[i + 1]);
                isSorted = false;
            }
        }

        // Even Phase
        for (int i = 0; i <= n - 2; i += 2) {
            if (arr[i] > arr[i + 1]) {
                swap(&arr[i], &arr[i + 1]);
                isSorted = false;
            }
        }
    }
}

int main() {
    int arr[] = {34, 2, 10, -9, 0, 15, 8, 1};
    int n = sizeof(arr) / sizeof(arr[0]);
    oddEvenSort(arr, n);
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

void oddEvenSort(std::vector<int>& arr) {
    int n = arr.size();
    bool isSorted = false;

    while (!isSorted) {
        isSorted = true;

        // Odd Phase
        for (int i = 1; i <= n - 2; i += 2) {
            if (arr[i] > arr[i + 1]) {
                std::swap(arr[i], arr[i + 1]);
                isSorted = false;
            }
        }

        // Even Phase
        for (int i = 0; i <= n - 2; i += 2) {
            if (arr[i] > arr[i + 1]) {
                std::swap(arr[i], arr[i + 1]);
                isSorted = false;
            }
        }
    }
}

int main() {
    std::vector<int> arr = {34, 2, 10, -9, 0, 15, 8, 1};
    oddEvenSort(arr);
    for (int x : arr) std::cout << x << " ";
    std::cout << "\n";
    return 0;
}
```

### Java
```java
import java.util.Arrays;

public class OddEvenSort {
    public static void oddEvenSort(int[] arr) {
        int n = arr.length;
        boolean isSorted = false;

        while (!isSorted) {
            isSorted = false;
            isSorted = true;

            // Odd Phase
            for (int i = 1; i <= n - 2; i += 2) {
                if (arr[i] > arr[i + 1]) {
                    int temp = arr[i];
                    arr[i] = arr[i + 1];
                    arr[i + 1] = temp;
                    isSorted = false;
                }
            }

            // Even Phase
            for (int i = 0; i <= n - 2; i += 2) {
                if (arr[i] > arr[i + 1]) {
                    int temp = arr[i];
                    arr[i] = arr[i + 1];
                    arr[i + 1] = temp;
                    isSorted = false;
                }
            }
        }
    }

    public static void main(String[] args) {
        int[] arr = {34, 2, 10, -9, 0, 15, 8, 1};
        oddEvenSort(arr);
        System.out.println(Arrays.toString(arr));
    }
}
```

### Python
```python
def odd_even_sort(arr: list[int]) -> list[int]:
    n = len(arr)
    is_sorted = False

    while not is_sorted:
        is_sorted = True

        # Odd Phase
        for i in range(1, n - 1, 2):
            if arr[i] > arr[i + 1]:
                arr[i], arr[i + 1] = arr[i + 1], arr[i]
                is_sorted = False

        # Even Phase
        for i in range(0, n - 1, 2):
            if arr[i] > arr[i + 1]:
                arr[i], arr[i + 1] = arr[i + 1], arr[i]
                is_sorted = False

    return arr

if __name__ == "__main__":
    data = [34, 2, 10, -9, 0, 15, 8, 1]
    odd_even_sort(data)
    print(data)
```

### JavaScript
```javascript
function oddEvenSort(arr) {
    const n = arr.length;
    let isSorted = false;

    while (!isSorted) {
        isSorted = true;

        // Odd Phase
        for (let i = 1; i <= n - 2; i += 2) {
            if (arr[i] > arr[i + 1]) {
                [arr[i], arr[i + 1]] = [arr[i + 1], arr[i]];
                isSorted = false;
            }
        }

        // Even Phase
        for (let i = 0; i <= n - 2; i += 2) {
            if (arr[i] > arr[i + 1]) {
                [arr[i], arr[i + 1]] = [arr[i + 1], arr[i]];
                isSorted = false;
            }
        }
    }

    return arr;
}

const data = [34, 2, 10, -9, 0, 15, 8, 1];
oddEvenSort(data);
console.log(data);
```

## 10. Code Explanation

Odd-Even Sort splits Bubble Sort comparison steps into two independent batches. In the Odd Phase, pairs at indices `(1, 2), (3, 4), (5, 6)...` are compared and swapped. In the Even Phase, pairs at indices `(0, 1), (2, 3), (4, 5)...` are compared and swapped. Because no pair within a phase shares an array index, all comparisons in a single phase can be executed concurrently in parallel threads or SIMD vector hardware without race conditions.

## 11. Interactive Demo

The UI renders two parallel execution lane tracks labeled "Odd Phase Comparator Lane" and "Even Phase Comparator Lane".

- During Odd Phase, odd comparator gates light up simultaneously across all odd pairs.
- During Even Phase, even comparator gates light up simultaneously across all even pairs.
- A thread counter shows simulated parallel speedup vs sequential execution.

## 12. Dry Run

**Input:** `[3, 2, 1, 4]`

| Step | Phase | Comparisons (`arr[i]`, `arr[i+1]`) | Swaps | Array State |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Odd | `(2, 1)` at `i = 1` | Swap (2,1) | `[3, 1, 2, 4]` |
| 2 | Even | `(3, 1)` at `i = 0`, `(2, 4)` at `i = 2` | Swap (3,1) | `[1, 3, 2, 4]` |
| 3 | Odd | `(3, 2)` at `i = 1` | Swap (3,2) | `[1, 2, 3, 4]` |
| 4 | Even | `(1, 2)` at `i = 0`, `(3, 4)` at `i = 2` | No Swaps | `[1, 2, 3, 4]` |
| 5 | Odd | `(2, 3)` at `i = 1` | No Swaps | Sorted! |

## 13. Time & Space Complexity

| Case | Time Complexity (Sequential) | Time Complexity (Parallel, $n$ threads) |
| :--- | :--- | :--- |
| **Best Case** | $\mathcal{O}(n)$ | $\mathcal{O}(1)$ |
| **Average Case** | $\mathcal{O}(n^2)$ | $\mathcal{O}(n)$ |
| **Worst Case** | $\mathcal{O}(n^2)$ | $\mathcal{O}(n)$ |
| **Space Complexity** | $\mathcal{O}(1)$ | $\mathcal{O}(1)$ |

## 14. Advantages

- **Parallel Execution:** Enables $n/2$ simultaneous comparisons per phase with zero locking overhead.
- **In-Place & Stable:** Requires $\mathcal{O}(1)$ space and preserves duplicate ordering.
- **Simple Control:** Simple loop structure for parallel FPGA hardware design.

## 15. Disadvantages

- **Quadratic Sequential Speed:** $\mathcal{O}(n^2)$ sequential performance makes it inefficient on single-threaded CPUs.

## 16. Applications

- Parallel sorting on GPU hardware (CUDA / OpenCL).
- FPGA sorting network circuits.
- Distributed mesh processor networks.

## 17. Common Mistakes

- **Incorrect Step Size:** Using `i++` instead of `i += 2` (ruins parallel phase separation).
- **Out of Bounds:** Checking `i + 1` past `n - 1`.

## 18. Interview Questions

1. Why is Odd-Even Sort well-suited for parallel processing?
2. What is the time complexity of Odd-Even Sort when executed on $n$ parallel processors?
3. Is Odd-Even Sort stable?

## 19. Practice Problems

**Easy:**
1. Implement Odd-Even Sort sequentially in C++ or Python.
2. Count the number of phase cycles required to sort an array.

**Medium:**
3. Implement a parallel multi-threaded version of Odd-Even Sort using OpenMP or pthreads.

## 20. Related Algorithms

- [Bubble Sort](./11_bubble_sort.md) (Sequential parent algorithm)
- [Bitonic Sort](./29_bitonic_sort.md) (Parallel divide-and-conquer sorting network)

## 21. Summary

Odd-Even Sort (Brick Sort) is a parallel variant of Bubble Sort that alternates between comparing odd-indexed pairs and even-indexed pairs. Because comparisons within each phase are completely independent, it allows $\mathcal{O}(n)$ parallel execution on $n$ processors while maintaining $\mathcal{O}(1)$ auxiliary space and stability.

## 22. Quiz

**Question 1:** Who invented Odd-Even Sort in 1972?
- A) N. Habermann
- B) Tim Peters
- C) Tony Hoare
- D) Włodzimierz Dobosiewicz
- **Correct Answer:** A
- **Explanation:** N. Habermann designed Odd-Even Transposition Sort in 1972.

**Question 2:** What is another common name for Odd-Even Sort?
- A) Comb Sort
- B) Brick Sort
- C) Pancake Sort
- D) Gnome Sort
- **Correct Answer:** B
- **Explanation:** It is widely known as Brick Sort because of its brick-like staggered comparison pattern.

**Question 3:** Why can comparisons in the Odd Phase be executed simultaneously in parallel?
- A) They use floating point math
- B) No two comparison pairs share the same array index
- C) Array is already sorted
- D) It uses extra memory
- **Correct Answer:** B
- **Explanation:** Disjoint index pairs (`(1,2), (3,4), (5,6)`) can be read and written concurrently without race conditions.

**Question 4:** What is the parallel time complexity of Odd-Even Sort on $n$ processors?
- A) $\mathcal{O}(n^2)$
- B) $\mathcal{O}(n)$
- C) $\mathcal{O}(\log n)$
- D) $\mathcal{O}(1)$
- **Correct Answer:** B
- **Explanation:** With $n$ processors, each phase takes $\mathcal{O}(1)$ time, and at most $n$ phases are needed, yielding $\mathcal{O}(n)$ total time.

**Question 5:** What is the worst-case time complexity of sequential Odd-Even Sort on a single thread?
- A) $\mathcal{O}(n \log n)$
- B) $\mathcal{O}(n^2)$
- C) $\mathcal{O}(n)$
- D) $\mathcal{O}(2^n)$
- **Correct Answer:** B
- **Explanation:** On a single thread, checking pairs sequentially takes $\mathcal{O}(n^2)$ total comparisons.

**Question 6:** Is Odd-Even Sort a stable sorting algorithm?
- A) Yes
- B) No
- **Correct Answer:** A
- **Explanation:** Adjacent equal elements are never swapped, preserving their relative arrival order.

**Question 7:** What is the step size in the loop for each phase of Odd-Even Sort?
- A) `1`
- B) `2`
- C) `1.3`
- D) `n / 2`
- **Correct Answer:** B
- **Explanation:** Loop increments by 2 (`i += 2`) to hop over non-overlapping adjacent pairs.

**Question 8:** What condition terminates the main loop of Odd-Even Sort?
- A) When `gap == 1`
- B) When both Odd Phase and Even Phase complete without a single swap occurring
- C) When `i == n`
- D) When stack overflows
- **Correct Answer:** B
- **Explanation:** The `isSorted` flag remains true only if 0 swaps occurred across both phases.

**Question 9:** What hardware architecture is Odd-Even Sort commonly implemented on?
- A) GPUs and FPGA sorting networks
- B) Single-core microcontrollers
- C) Hard disk drives
- D) Magnetic tapes
- **Correct Answer:** A
- **Explanation:** GPUs and FPGAs exploit SIMD parallel comparison gates naturally.

**Question 10:** What is the space complexity of Odd-Even Sort?
- A) $\mathcal{O}(n)$
- B) $\mathcal{O}(1)$
- C) $\mathcal{O}(\log n)$
- D) $\mathcal{O}(n^2)$
- **Correct Answer:** B
- **Explanation:** Operates entirely in-place requiring constant auxiliary memory.
