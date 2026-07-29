# IntroSort

## 1. Introduction

IntroSort (short for Introspective Sort) is a fast, hybrid comparison-based sorting algorithm designed by David Musser in 1997. It starts with Quick Sort, monitors recursion depth, and switches to Heap Sort if the recursion depth exceeds a predefined threshold (typically $2 \lfloor \log_2 n \rfloor$). For small partitioning ranges (usually length $< 16$), it switches to Insertion Sort.

Imagine a high-speed racing car (Quick Sort). On standard roads, it zooms ahead of everyone else. However, if it runs into a muddy patch where it risks spinning out into a worst-case ditch ($\mathcal{O}(n^2)$ complexity), an automated safety system switches drive mode to a sturdy all-terrain tractor (Heap Sort) to guarantee progress in $\mathcal{O}(n \log n)$ time. Once near the finish line, it uses a simple, low-overhead cart (Insertion Sort) to park. That is IntroSort.

It was designed to solve the fatal flaw of Quick Sort: its $\mathcal{O}(n^2)$ worst-case time complexity.

Today, IntroSort is the standard sorting engine behind `std::sort` in the C++ Standard Template Library (STL) and Microsoft .NET `Array.Sort()`.

## 2. Why Use This Algorithm?

IntroSort combines the average-case speed and cache efficiency of Quick Sort with the strict $\mathcal{O}(n \log n)$ worst-case bound of Heap Sort.

**Benefits:**
- **Fastest Average Speed:** Retains Quick Sort's exceptional cache locality and low constant factors.
- **Worst-Case Guarantee:** Prevents $\mathcal{O}(n^2)$ catastrophic degradation by falling back to Heap Sort when partitioning becomes unbalanced.
- **In-Place Execution:** Requires only $\mathcal{O}(\log n)$ stack space.
- **Low Overhead on Small Subarrays:** Switches to Insertion Sort for tiny ranges ($n < 16$).

**Performance:**
- **Best Case:** $\mathcal{O}(n \log n)$
- **Average Case:** $\mathcal{O}(n \log n)$
- **Worst Case:** $\mathcal{O}(n \log n)$
- **Space Complexity:** $\mathcal{O}(\log n)$ auxiliary stack space.

**When it is better than other algorithms:**
IntroSort is ideal for fast, general-purpose in-place sorting where memory is constrained and stability is not required.

## 3. Real-World Applications

- **C++ Standard Template Library (STL):** Powers `std::sort` in GCC (libstdc++) and LLVM (libc++).
- **Microsoft .NET Framework:** Engine for `Array.Sort()` and `List<T>.Sort()`.
- **Game Engine Development:** Preferred in C++ game engines where non-allocating, maximum-speed in-place sorting is required.

## 4. Prerequisites

Before studying IntroSort, you should understand:
- [Quick Sort](./15_quick_sort.md) and median-of-three partitioning.
- [Heap Sort](./16_heap_sort.md) and max-heap array manipulation.
- [Insertion Sort](./13_insertion_sort.md) for small array bounds.
- Binary logarithm depth limits ($2 \lfloor \log_2 n \rfloor$).

## 5. Visualization

```text
[Input Array] ──(Size > 16)──> [Quick Sort Partitioning]
                                     │
                                Depth Check: depth_limit == 0?
                                ├── YES ──> [Heap Sort Sub-range] (Guarantees O(n log n))
                                └── NO  ──> [Recurse Quick Sort]
                                
Sub-range Size < 16 ─────────> [Insertion Sort Final Pass] (Low Overhead)
```

## 6. How It Works

1. Compute initial max depth limit: `depthLimit = 2 * floor(log2(n))`.
2. Begin Quick Sort partitioning using Median-of-Three pivot selection.
3. If subarray length is smaller than threshold (e.g., 16), stop partitioning.
4. If recursion depth reaches 0 (meaning Quick Sort partitioning is unbalanced), switch the current range to **Heap Sort**.
5. Once Quick Sort/Heap Sort partitioning completes, execute a single **Insertion Sort** pass over the entire array to finalize order.

## 7. Step-by-Step Algorithm

1. `max_depth = 2 * floor(log2(n))`.
2. Call `introSortUtil(arr, 0, n - 1, max_depth)`.
3. In `introSortUtil(arr, low, high, depth_limit)`:
   - If `high - low < 16`: Return (let final Insertion Sort handle small ranges).
   - If `depth_limit == 0`: Call `heapSort(arr, low, high)` and return.
   - Else:
     - Partition array using median-of-three pivot to find `pivot_index`.
     - Recurse `introSortUtil(arr, low, pivot_index - 1, depth_limit - 1)`.
     - Recurse `introSortUtil(arr, pivot_index + 1, high, depth_limit - 1)`.
4. Call `insertionSort(arr, 0, n - 1)` on the whole array.

## 8. Pseudocode

```text
function introSort(arr):
    n = length(arr)
    depthLimit = 2 * floor(log2(n))
    introsortUtil(arr, 0, n - 1, depthLimit)
    insertionSort(arr, 0, n - 1)

function introsortUtil(arr, low, high, depthLimit):
    size = high - low + 1
    if size < 16:
        return
    if depthLimit == 0:
        heapSort(arr, low, high)
        return

    pivot = medianOfThree(arr, low, low + size / 2, high)
    pIndex = partition(arr, low, high, pivot)

    introsortUtil(arr, low, pIndex - 1, depthLimit - 1)
    introsortUtil(arr, pIndex + 1, high, depthLimit - 1)
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <math.h>

void swap(int* a, int* b) { int t = *a; *a = *b; *b = t; }

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

void heapify(int arr[], int n, int i, int base) {
    int largest = i;
    int left = 2 * i + 1;
    int right = 2 * i + 2;

    if (left < n && arr[base + left] > arr[base + largest]) largest = left;
    if (right < n && arr[base + right] > arr[base + largest]) largest = right;

    if (largest != i) {
        swap(&arr[base + i], &arr[base + largest]);
        heapify(arr, n, largest, base);
    }
}

void heapSort(int arr[], int low, int high) {
    int n = high - low + 1;
    for (int i = n / 2 - 1; i >= 0; i--) heapify(arr, n, i, low);
    for (int i = n - 1; i > 0; i--) {
        swap(&arr[low], &arr[low + i]);
        heapify(arr, i, 0, low);
    }
}

int partition(int arr[], int low, int high) {
    int pivot = arr[high];
    int i = low - 1;
    for (int j = low; j < high; j++) {
        if (arr[j] <= pivot) {
            i++;
            swap(&arr[i], &arr[j]);
        }
    }
    swap(&arr[i + 1], &arr[high]);
    return i + 1;
}

void introsortUtil(int arr[], int low, int high, int depthLimit) {
    int size = high - low + 1;
    if (size < 16) return;
    if (depthLimit == 0) {
        heapSort(arr, low, high);
        return;
    }
    int p = partition(arr, low, high);
    introsortUtil(arr, low, p - 1, depthLimit - 1);
    introsortUtil(arr, p + 1, high, depthLimit - 1);
}

void introSort(int arr[], int n) {
    int depthLimit = 2 * (int)log2(n);
    introsortUtil(arr, 0, n - 1, depthLimit);
    insertionSort(arr, 0, n - 1);
}

int main() {
    int arr[] = {3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5, 8, 9, 7, 9, 3, 2, 3, 8, 4, 6};
    int n = sizeof(arr) / sizeof(arr[0]);
    introSort(arr, n);
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
#include <cmath>

void heapSortRange(std::vector<int>& arr, int low, int high) {
    std::make_heap(arr.begin() + low, arr.begin() + high + 1);
    std::sort_heap(arr.begin() + low, arr.begin() + high + 1);
}

int partition(std::vector<int>& arr, int low, int high) {
    int pivot = arr[high];
    int i = low - 1;
    for (int j = low; j < high; j++) {
        if (arr[j] <= pivot) {
            i++;
            std::swap(arr[i], arr[j]);
        }
    }
    std::swap(arr[i + 1], arr[high]);
    return i + 1;
}

void introsortUtil(std::vector<int>& arr, int low, int high, int depthLimit) {
    int size = high - low + 1;
    if (size < 16) return;
    if (depthLimit == 0) {
        heapSortRange(arr, low, high);
        return;
    }
    int p = partition(arr, low, high);
    introsortUtil(arr, low, p - 1, depthLimit - 1);
    introsortUtil(arr, p + 1, high, depthLimit - 1);
}

void insertionSort(std::vector<int>& arr, int n) {
    for (int i = 1; i < n; i++) {
        int key = arr[i];
        int j = i - 1;
        while (j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j];
            j--;
        }
        arr[j + 1] = key;
    }
}

void introSort(std::vector<int>& arr) {
    int n = arr.size();
    if (n <= 1) return;
    int depthLimit = 2 * std::floor(std::log2(n));
    introsortUtil(arr, 0, n - 1, depthLimit);
    insertionSort(arr, n);
}

int main() {
    std::vector<int> arr = {3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5, 8, 9, 7, 9, 3, 2, 3, 8, 4, 6};
    introSort(arr);
    for (int x : arr) std::cout << x << " ";
    std::cout << "\n";
    return 0;
}
```

### Java
```java
import java.util.Arrays;

public class IntroSort {
    private static void swap(int[] arr, int i, int j) {
        int temp = arr[i]; arr[i] = arr[j]; arr[j] = temp;
    }

    private static void insertionSort(int[] arr, int n) {
        for (int i = 1; i < n; i++) {
            int key = arr[i];
            int j = i - 1;
            while (j >= 0 && arr[j] > key) {
                arr[j + 1] = arr[j];
                j--;
            }
            arr[j + 1] = key;
        }
    }

    private static int partition(int[] arr, int low, int high) {
        int pivot = arr[high];
        int i = low - 1;
        for (int j = low; j < high; j++) {
            if (arr[j] <= pivot) {
                i++;
                swap(arr, i, j);
            }
        }
        swap(arr, i + 1, high);
        return i + 1;
    }

    private static void heapify(int[] arr, int n, int i, int base) {
        int largest = i;
        int left = 2 * i + 1, right = 2 * i + 2;
        if (left < n && arr[base + left] > arr[base + largest]) largest = left;
        if (right < n && arr[base + right] > arr[base + largest]) largest = right;
        if (largest != i) {
            swap(arr, base + i, base + largest);
            heapify(arr, n, largest, base);
        }
    }

    private static void heapSort(int[] arr, int low, int high) {
        int n = high - low + 1;
        for (int i = n / 2 - 1; i >= 0; i--) heapify(arr, n, i, low);
        for (int i = n - 1; i > 0; i--) {
            swap(arr, low, low + i);
            heapify(arr, i, 0, low);
        }
    }

    private static void introsortUtil(int[] arr, int low, int high, int depthLimit) {
        int size = high - low + 1;
        if (size < 16) return;
        if (depthLimit == 0) {
            heapSort(arr, low, high);
            return;
        }
        int p = partition(arr, low, high);
        introsortUtil(arr, low, p - 1, depthLimit - 1);
        introsortUtil(arr, p + 1, high, depthLimit - 1);
    }

    public static void introSort(int[] arr) {
        int n = arr.length;
        if (n <= 1) return;
        int depthLimit = (int) (2 * Math.floor(Math.log(n) / Math.log(2)));
        introsortUtil(arr, 0, n - 1, depthLimit);
        insertionSort(arr, n);
    }

    public static void main(String[] args) {
        int[] arr = {3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5, 8, 9, 7, 9, 3, 2, 3, 8, 4, 6};
        introSort(arr);
        System.out.println(Arrays.toString(arr));
    }
}
```

### Python
```python
import math

def insertion_sort(arr: list[int]) -> None:
    for i in range(1, len(arr)):
        key = arr[i]
        j = i - 1
        while j >= 0 and arr[j] > key:
            arr[j + 1] = arr[j]
            j -= 1
        arr[j + 1] = key

def heapify(arr: list[int], n: int, i: int, base: int) -> None:
    largest = i
    left = 2 * i + 1
    right = 2 * i + 2
    if left < n and arr[base + left] > arr[base + largest]:
        largest = left
    if right < n and arr[base + right] > arr[base + largest]:
        largest = right
    if largest != i:
        arr[base + i], arr[base + largest] = arr[base + largest], arr[base + i]
        heapify(arr, n, largest, base)

def heap_sort(arr: list[int], low: int, high: int) -> None:
    n = high - low + 1
    for i in range(n // 2 - 1, -1, -1):
        heapify(arr, n, i, low)
    for i in range(n - 1, 0, -1):
        arr[low], arr[low + i] = arr[low + i], arr[low]
        heapify(arr, i, 0, low)

def partition(arr: list[int], low: int, high: int) -> int:
    pivot = arr[high]
    i = low - 1
    for j in range(low, high):
        if arr[j] <= pivot:
            i += 1
            arr[i], arr[j] = arr[j], arr[i]
    arr[i + 1], arr[high] = arr[high], arr[i + 1]
    return i + 1

def introsort_util(arr: list[int], low: int, high: int, depth_limit: int) -> None:
    size = high - low + 1
    if size < 16:
        return
    if depth_limit == 0:
        heap_sort(arr, low, high)
        return
    p = partition(arr, low, high)
    introsort_util(arr, low, p - 1, depth_limit - 1)
    introsort_util(arr, p + 1, high, depth_limit - 1)

def intro_sort(arr: list[int]) -> list[int]:
    n = len(arr)
    if n <= 1:
        return arr
    depth_limit = 2 * math.floor(math.log2(n))
    introsort_util(arr, 0, n - 1, depth_limit)
    insertion_sort(arr)
    return arr

if __name__ == "__main__":
    data = [3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5, 8, 9, 7, 9, 3, 2, 3, 8, 4, 6]
    intro_sort(data)
    print(data)
```

### JavaScript
```javascript
function insertionSort(arr) {
    for (let i = 1; i < arr.length; i++) {
        const key = arr[i];
        let j = i - 1;
        while (j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j];
            j--;
        }
        arr[j + 1] = key;
    }
}

function heapify(arr, n, i, base) {
    let largest = i;
    const left = 2 * i + 1, right = 2 * i + 2;
    if (left < n && arr[base + left] > arr[base + largest]) largest = left;
    if (right < n && arr[base + right] > arr[base + largest]) largest = right;
    if (largest !== i) {
        [arr[base + i], arr[base + largest]] = [arr[base + largest], arr[base + i]];
        heapify(arr, n, largest, base);
    }
}

function heapSort(arr, low, high) {
    const n = high - low + 1;
    for (let i = Math.floor(n / 2) - 1; i >= 0; i--) heapify(arr, n, i, low);
    for (let i = n - 1; i > 0; i--) {
        [arr[low], arr[low + i]] = [arr[low + i], arr[low]];
        heapify(arr, i, 0, low);
    }
}

function partition(arr, low, high) {
    const pivot = arr[high];
    let i = low - 1;
    for (let j = low; j < high; j++) {
        if (arr[j] <= pivot) {
            i++;
            [arr[i], arr[j]] = [arr[j], arr[i]];
        }
    }
    [arr[i + 1], arr[high]] = [arr[high], arr[i + 1]];
    return i + 1;
}

function introsortUtil(arr, low, high, depthLimit) {
    const size = high - low + 1;
    if (size < 16) return;
    if (depthLimit === 0) {
        heapSort(arr, low, high);
        return;
    }
    const p = partition(arr, low, high);
    introsortUtil(arr, low, p - 1, depthLimit - 1);
    introsortUtil(arr, p + 1, high, depthLimit - 1);
}

function introSort(arr) {
    const n = arr.length;
    if (n <= 1) return arr;
    const depthLimit = 2 * Math.floor(Math.log2(n));
    introsortUtil(arr, 0, n - 1, depthLimit);
    insertionSort(arr);
    return arr;
}

const data = [3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5, 8, 9, 7, 9, 3, 2, 3, 8, 4, 6];
introSort(data);
console.log(data);
```

## 10. Code Explanation

IntroSort uses three subroutines in tandem. It computes `depthLimit = 2 * floor(log2(n))`. Quick Sort performs partitions while decrementing `depthLimit` at each recursion step. If `depthLimit` drops to 0, it means Quick Sort partitioning is splitting unbalanced sub-ranges (which would risk $\mathcal{O}(n^2)$ time); IntroSort immediately aborts Quick Sort recursion on that slice and sorts it with `heapSort` ($\mathcal{O}(n \log n)$ guarantee). Small slices ($size < 16$) are skipped during partitioning and cleaned up efficiently in a single final `insertionSort` pass.

## 11. Interactive Demo

An animated UI shows an array bar chart. A recursion depth counter displays `Current Depth / Max Depth Limit`.

- Normal Quick Sort splits are drawn with Blue partitions.
- If recursion exceeds `depthLimit`, the target segment turns Amber/Orange and a Heap Tree visualizer pops up to sort the segment in place.
- Finally, a green scanner moves left to right performing the quick Insertion Sort cleanup pass.

## 12. Dry Run

**Array:** 20 elements ($n = 20$), `depthLimit = 2 * floor(log2(20)) = 8`.

1. **Quick Sort Phase:** Partition array recursively. Depth decreases ($8 \to 7 \to 6 \dots$).
2. **Depth Limit Check:** If depth hits 0 on an anti-median partition, invoke `heapSort` on that sub-range.
3. **Small Slices ($<16$):** Sub-ranges under 16 items are bypassed during Quick Sort.
4. **Insertion Sort Pass:** Single final pass over the whole array finishes sorting nearly-ordered array.

## 13. Time & Space Complexity

| Case | Time Complexity | Reason |
| :--- | :--- | :--- |
| **Best Case** | $\mathcal{O}(n \log n)$ | Standard balanced Quick Sort behavior |
| **Average Case** | $\mathcal{O}(n \log n)$ | Quick Sort partition efficiency |
| **Worst Case** | $\mathcal{O}(n \log n)$ | Heap Sort fallback guarantees $O(n \log n)$ bound |
| **Space Complexity** | $\mathcal{O}(\log n)$ | Maximum recursion stack depth bound |

## 14. Advantages

- **Guaranteed $\mathcal{O}(n \log n)$ Worst Case:** Never degrades to $\mathcal{O}(n^2)$ like plain Quick Sort.
- **In-Place:** Requires no extra array allocations ($\mathcal{O}(\log n)$ stack space).
- **Fastest General Sort:** Combines Quick Sort's speed with Heap Sort's safety and Insertion Sort's low overhead.

## 15. Disadvantages

- **Unstable:** Does not preserve initial relative order of duplicate elements.
- **Non-Adaptive:** Does not exploit pre-existing order as efficiently as Tim Sort.

## 16. Applications

- C++ Standard Template Library (`std::sort`).
- Microsoft .NET `Array.Sort()`.

## 17. Common Mistakes

- **Incorrect Depth Threshold:** Setting depth limit too low causes premature Heap Sort switching (slowing performance).
- **Unbounded Recursion:** Forgetting to decrement `depthLimit` during recursive calls.

## 18. Interview Questions

1. Why does C++ `std::sort` use IntroSort instead of standard Quick Sort?
2. What is the formula for the maximum recursion depth before switching to Heap Sort?
3. Is IntroSort stable or unstable?
4. Why does IntroSort switch to Insertion Sort for small subarrays ($n < 16$)?

## 19. Practice Problems

**Easy:**
1. Implement IntroSort for an array of integers with depth threshold $= 2 \lfloor \log_2 n \rfloor$.

**Medium:**
2. Modify IntroSort to record how many times Heap Sort fallback was triggered.

**Hard:**
3. Implement a custom C++ STL container comparator compatible IntroSort template function.

## 20. Related Algorithms

- [Quick Sort](./15_quick_sort.md) (Primary component)
- [Heap Sort](./16_heap_sort.md) (Worst-case fallback guard)
- [Tim Sort](./21_tim_sort.md) (Stable hybrid alternative)

## 21. Summary

IntroSort is a hybrid sorting algorithm that begins with Quick Sort, monitors recursion depth, switches to Heap Sort if partitioning becomes unbalanced to guarantee $\mathcal{O}(n \log n)$ worst-case time, and finishes small subarrays with Insertion Sort. It powers C++ `std::sort`.

## 22. Quiz

**Question 1:** Which algorithm does IntroSort start with?
- A) Heap Sort
- B) Merge Sort
- C) Quick Sort
- D) Bubble Sort
- **Correct Answer:** C
- **Explanation:** IntroSort starts with Quick Sort because of its superior average-case cache locality and speed.

**Question 2:** Why does IntroSort switch to Heap Sort?
- A) To increase memory usage
- B) To avoid Quick Sort's $\mathcal{O}(n^2)$ worst-case time complexity
- C) To make the algorithm stable
- D) To sort strings faster
- **Correct Answer:** B
- **Explanation:** Heap Sort guarantees $\mathcal{O}(n \log n)$ performance when Quick Sort partitioning becomes unbalanced.

**Question 3:** What is the standard formula for max recursion depth in IntroSort?
- A) $n / 2$
- B) $2 \lfloor \log_2 n \rfloor$
- C) $n^2$
- D) $\sqrt{n}$
- **Correct Answer:** B
- **Explanation:** $2 \lfloor \log_2 n \rfloor$ allows sufficient Quick Sort recursion before intervening.

**Question 4:** Which standard library implementation relies on IntroSort?
- A) Python `list.sort()`
- B) C++ `std::sort`
- C) Java `Collections.sort()`
- D) PostgreSQL `ORDER BY`
- **Correct Answer:** B
- **Explanation:** C++ STL `std::sort` uses IntroSort.

**Question 5:** Is IntroSort a stable sorting algorithm?
- A) Yes
- B) No
- **Correct Answer:** B
- **Explanation:** Quick Sort and Heap Sort components are both unstable.

**Question 6:** What is the worst-case time complexity of IntroSort?
- A) $\mathcal{O}(n^2)$
- B) $\mathcal{O}(n \log n)$
- C) $\mathcal{O}(n)$
- D) $\mathcal{O}(n!)$
- **Correct Answer:** B
- **Explanation:** Heap Sort fallback guarantees $\mathcal{O}(n \log n)$ worst-case time bound.

**Question 7:** What is the space complexity of IntroSort?
- A) $\mathcal{O}(n)$
- B) $\mathcal{O}(\log n)$
- C) $\mathcal{O}(n^2)$
- D) $\mathcal{O}(1)$
- **Correct Answer:** B
- **Explanation:** Requires $\mathcal{O}(\log n)$ stack memory space for recursion depth.

**Question 8:** Why does IntroSort use Insertion Sort for small subarrays?
- A) Insertion Sort is $\mathcal{O}(1)$ space
- B) Insertion Sort has lower constant factors and overhead for small $n$ ($< 16$)
- C) Quick Sort cannot handle small arrays
- D) To achieve stability
- **Correct Answer:** B
- **Explanation:** For small array bounds, Insertion Sort is faster than recursive function calls.

**Question 9:** What pivot selection technique is typically used in IntroSort's Quick Sort phase?
- A) First element
- B) Last element
- C) Median-of-Three
- D) Random selection
- **Correct Answer:** C
- **Explanation:** Median-of-three chooses the median of first, middle, and last elements to avoid bad splits.

**Question 10:** Who invented IntroSort and in what year?
- A) Tim Peters in 2002
- B) David Musser in 1997
- C) Tony Hoare in 1960
- D) John von Neumann in 1945
- **Correct Answer:** B
- **Explanation:** David Musser introduced IntroSort in 1997.
