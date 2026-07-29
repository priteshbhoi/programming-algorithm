# Bitonic Sort

## 1. Introduction

Bitonic Sort is a parallel comparison-based sorting algorithm invented by Ken Batcher in 1968. It is designed around the concept of a **bitonic sequence**—a sequence of numbers that first increases monotonically and then decreases monotonically, or can be circularly shifted to form such a sequence (e.g., `[1, 3, 5, 7, 6, 4, 2]` or `[4, 6, 7, 5, 3, 2, 1]`).

Imagine two mountain hikers walking up a peak from opposite sides and meeting at the top. If you convert their altitudes into a list, you get a bitonic sequence. Bitonic Sort systematically converts an arbitrary unsorted array into bitonic sequences, and then splits and merges those sequences into a fully sorted array.

It was created specifically to serve as a hardware-friendly **sorting network** algorithm, where comparisons are performed in fixed, pre-determined parallel stages independent of data values.

You should study Bitonic Sort to understand parallel sorting networks, GPU parallel sorting (such as CUDA Bitonic Sort), and data-independent algorithm execution.

## 2. Why Use This Algorithm?

Bitonic Sort is a flagship algorithm for parallel hardware architectures because its sequence of comparisons is completely independent of the data being sorted.

**Benefits:**
- **Data-Independent Comparisons:** The sequence of element comparisons never changes based on input values, preventing branch misprediction.
- **Parallel Complexity $\mathcal{O}(\log^2 n)$:** Runs in $\mathcal{O}(\log^2 n)$ parallel time stages using $n$ parallel processors.
- **Hardware-Friendly:** Ideal for fixed hardware comparator networks (FPGAs, GPUs, ASICs).
- **In-Place:** Can be executed completely in-place.

**Performance:**
- **Sequential Time Complexity:** $\mathcal{O}(n \log^2 n)$ (all cases)
- **Parallel Time Complexity:** $\mathcal{O}(\log^2 n)$ stages (with $n$ parallel processing units)
- **Space Complexity:** $\mathcal{O}(\log^2 n)$ recursive stack space or $\mathcal{O}(1)$ iterative space.

**When it is better than other algorithms:**
Bitonic Sort outperforms Quick Sort and Merge Sort on massively parallel hardware (like GPUs with thousands of threads) where branch divergence in conditional code carries a severe penalty.

## 3. Real-World Applications

- **GPU Acceleration (CUDA / OpenCL):** High-throughput sorting on graphics hardware.
- **Hardware Circuitry (FPGAs / ASICs):** Designing fixed physical comparator networks for signal processing.
- **Supercomputing & Parallel Networks:** Hypercube parallel architectures.

## 4. Prerequisites

Before learning Bitonic Sort, you should understand:
- Bitonic sequence definition.
- Power-of-two array constraints ($n = 2^k$).
- Divide-and-conquer recursion and bitwise operations (`XOR`).

## 5. Visualization

```text
[Input: 3, 7, 4, 8, 6, 2, 1, 5] (n = 8 = 2^3)

Stage 1: Build 2-element bitonic pairs (Ascending & Descending)
   [3, 7] (Asc) | [8, 4] (Desc) | [2, 6] (Asc) | [5, 1] (Desc)
   Result: [3, 7, 8, 4, 2, 6, 5, 1]

Stage 2: Build 4-element bitonic sequences
   Combine [3, 7, 8, 4] (Ascending sequence) and [2, 6, 5, 1] (Descending sequence)
   Result: [3, 4, 7, 8, 6, 5, 2, 1]  <-- 8-element Bitonic Sequence!

Stage 3: Bitonic Merge (Ascending)
   Split and compare pairs at stride 4, then stride 2, then stride 1
   Final Result: [1, 2, 3, 4, 5, 6, 7, 8]
```

## 6. How It Works

1. Bitonic Sort requires the input array length $n$ to be a power of 2 ($n = 2^k$).
2. **Bitonic Compare & Swap:** A helper `bitonicCompare(i, j, dir)` compares `arr[i]` and `arr[j]`. If `dir == 1` (Ascending) and `arr[i] > arr[j]`, swap them. If `dir == 0` (Descending) and `arr[i] < arr[j]`, swap them.
3. **Bitonic Merge:** Given a bitonic sequence of length $k$, compare elements at distance $k/2$. Recursively merge the two halves in the same direction `dir`.
4. **Bitonic Sort Recursive Structure:** Recursively sort the left half in Ascending direction (`dir = 1`) and the right half in Descending direction (`dir = 0`). This produces a single combined bitonic sequence of size $2k$. Call `bitonicMerge` to flatten it into the target direction.

## 7. Step-by-Step Algorithm

1. Function `bitonicSort(arr, low, count, dir)`:
   - If `count > 1`:
     - `k = count / 2`.
     - Recurse `bitonicSort(arr, low, k, 1)` (sort left in ascending order).
     - Recurse `bitonicSort(arr, low + k, k, 0)` (sort right in descending order).
     - Call `bitonicMerge(arr, low, count, dir)` (merge result in target direction).
2. Function `bitonicMerge(arr, low, count, dir)`:
   - If `count > 1`:
     - `k = count / 2`.
     - For `i = low` to `low + k - 1`: `bitonicCompare(arr, i, i + k, dir)`.
     - Recurse `bitonicMerge(arr, low, k, dir)`.
     - Recurse `bitonicMerge(arr, low + k, k, dir)`.

## 8. Pseudocode

```text
function bitonicSort(arr, low, count, dir):
    if count > 1:
        k = count / 2
        
        // Sort first half in ascending order
        bitonicSort(arr, low, k, 1)
        
        // Sort second half in descending order
        bitonicSort(arr, low + k, k, 0)
        
        // Merge entire sequence in direction 'dir'
        bitonicMerge(arr, low, count, dir)

function bitonicMerge(arr, low, count, dir):
    if count > 1:
        k = count / 2
        for i = low to low + k - 1:
            bitonicCompare(arr, i, i + k, dir)
            
        bitonicMerge(arr, low, k, dir)
        bitonicMerge(arr, low + k, k, dir)

function bitonicCompare(arr, i, j, dir):
    if (dir == 1 and arr[i] > arr[j]) or (dir == 0 and arr[i] < arr[j]):
        swap(arr[i], arr[j])
```

## 9. Code Examples

### C
```c
#include <stdio.h>

void swap(int* a, int* b) {
    int t = *a; *a = *b; *b = t;
}

void bitonicCompare(int arr[], int i, int j, int dir) {
    if ((dir == 1 && arr[i] > arr[j]) || (dir == 0 && arr[i] < arr[j])) {
        swap(&arr[i], &arr[j]);
    }
}

void bitonicMerge(int arr[], int low, int count, int dir) {
    if (count > 1) {
        int k = count / 2;
        for (int i = low; i < low + k; i++) {
            bitonicCompare(arr, i, i + k, dir);
        }
        bitonicMerge(arr, low, k, dir);
        bitonicMerge(arr, low + k, k, dir);
    }
}

void bitonicSortUtil(int arr[], int low, int count, int dir) {
    if (count > 1) {
        int k = count / 2;
        bitonicSortUtil(arr, low, k, 1);
        bitonicSortUtil(arr, low + k, k, 0);
        bitonicMerge(arr, low, count, dir);
    }
}

void bitonicSort(int arr[], int n) {
    bitonicSortUtil(arr, 0, n, 1);
}

int main() {
    int arr[] = {3, 7, 4, 8, 6, 2, 1, 5};
    int n = sizeof(arr) / sizeof(arr[0]);
    bitonicSort(arr, n);
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

void bitonicCompare(std::vector<int>& arr, int i, int j, int dir) {
    if ((dir == 1 && arr[i] > arr[j]) || (dir == 0 && arr[i] < arr[j])) {
        std::swap(arr[i], arr[j]);
    }
}

void bitonicMerge(std::vector<int>& arr, int low, int count, int dir) {
    if (count > 1) {
        int k = count / 2;
        for (int i = low; i < low + k; i++) {
            bitonicCompare(arr, i, i + k, dir);
        }
        bitonicMerge(arr, low, k, dir);
        bitonicMerge(arr, low + k, k, dir);
    }
}

void bitonicSortUtil(std::vector<int>& arr, int low, int count, int dir) {
    if (count > 1) {
        int k = count / 2;
        bitonicSortUtil(arr, low, k, 1);
        bitonicSortUtil(arr, low + k, k, 0);
        bitonicMerge(arr, low, count, dir);
    }
}

void bitonicSort(std::vector<int>& arr) {
    bitonicSortUtil(arr, 0, arr.size(), 1);
}

int main() {
    std::vector<int> arr = {3, 7, 4, 8, 6, 2, 1, 5};
    bitonicSort(arr);
    for (int x : arr) std::cout << x << " ";
    std::cout << "\n";
    return 0;
}
```

### Java
```java
import java.util.Arrays;

public class BitonicSort {
    private static void bitonicCompare(int[] arr, int i, int j, int dir) {
        if ((dir == 1 && arr[i] > arr[j]) || (dir == 0 && arr[i] < arr[j])) {
            int temp = arr[i];
            arr[i] = arr[j];
            arr[j] = temp;
        }
    }

    private static void bitonicMerge(int[] arr, int low, int count, int dir) {
        if (count > 1) {
            int k = count / 2;
            for (int i = low; i < low + k; i++) {
                bitonicCompare(arr, i, i + k, dir);
            }
            bitonicMerge(arr, low, k, dir);
            bitonicMerge(arr, low + k, k, dir);
        }
    }

    private static void bitonicSortUtil(int[] arr, int low, int count, int dir) {
        if (count > 1) {
            int k = count / 2;
            bitonicSortUtil(arr, low, k, 1);
            bitonicSortUtil(arr, low + k, k, 0);
            bitonicMerge(arr, low, count, dir);
        }
    }

    public static void bitonicSort(int[] arr) {
        bitonicSortUtil(arr, 0, arr.length, 1);
    }

    public static void main(String[] args) {
        int[] arr = {3, 7, 4, 8, 6, 2, 1, 5};
        bitonicSort(arr);
        System.out.println(Arrays.toString(arr));
    }
}
```

### Python
```python
def bitonic_compare(arr: list[int], i: int, j: int, dir: int) -> None:
    if (dir == 1 and arr[i] > arr[j]) or (dir == 0 and arr[i] < arr[j]):
        arr[i], arr[j] = arr[j], arr[i]

def bitonic_merge(arr: list[int], low: int, count: int, dir: int) -> None:
    if count > 1:
        k = count // 2
        for i in range(low, low + k):
            bitonic_compare(arr, i, i + k, dir)
        bitonic_merge(arr, low, k, dir)
        bitonic_merge(arr, low + k, k, dir)

def bitonic_sort_util(arr: list[int], low: int, count: int, dir: int) -> None:
    if count > 1:
        k = count // 2
        bitonic_sort_util(arr, low, k, 1)
        bitonic_sort_util(arr, low + k, k, 0)
        bitonic_merge(arr, low, count, dir)

def bitonic_sort(arr: list[int]) -> list[int]:
    bitonic_sort_util(arr, 0, len(arr), 1)
    return arr

if __name__ == "__main__":
    data = [3, 7, 4, 8, 6, 2, 1, 5]
    bitonic_sort(data)
    print(data)
```

### JavaScript
```javascript
function bitonicCompare(arr, i, j, dir) {
    if ((dir === 1 && arr[i] > arr[j]) || (dir === 0 && arr[i] < arr[j])) {
        [arr[i], arr[j]] = [arr[j], arr[i]];
    }
}

function bitonicMerge(arr, low, count, dir) {
    if (count > 1) {
        const k = Math.floor(count / 2);
        for (let i = low; i < low + k; i++) {
            bitonicCompare(arr, i, i + k, dir);
        }
        bitonicMerge(arr, low, k, dir);
        bitonicMerge(arr, low + k, k, dir);
    }
}

function bitonicSortUtil(arr, low, count, dir) {
    if (count > 1) {
        const k = Math.floor(count / 2);
        bitonicSortUtil(arr, low, k, 1);
        bitonicSortUtil(arr, low + k, k, 0);
        bitonicMerge(arr, low, count, dir);
    }
}

function bitonicSort(arr) {
    bitonicSortUtil(arr, 0, arr.length, 1);
    return arr;
}

const data = [3, 7, 4, 8, 6, 2, 1, 5];
bitonicSort(data);
console.log(data);
```

## 10. Code Explanation

Bitonic Sort exploits the mathematical properties of bitonic sequences. It recursively splits the array into two halves, sorting the left half in Ascending order (`dir = 1`) and the right half in Descending order (`dir = 0`). Concatenating an ascending sequence and a descending sequence forms a larger bitonic sequence. The `bitonicMerge` routine then performs element comparisons across stride distance $k = \text{count} / 2$, which splits the bitonic sequence into two smaller bitonic sequences where every element in the first half is $\le$ every element in the second half. Recursively merging down to length 1 yields a fully sorted array.

## 11. Interactive Demo

The UI displays a Bitonic Wire Diagram (sorting network circuit layout).

- Horizontal wires represent array elements.
- Vertical connector lines represent comparison gates.
- Glowing signals move from left to right through the network stages, illustrating how comparisons execute concurrently in parallel columns regardless of input values.

## 12. Dry Run

**Input:** `[3, 7, 4, 8]` ($n = 4$)

| Stage | Action | Result |
| :--- | :--- | :--- |
| **Sort Pair 1 (Asc)** | Compare (3,7) -> No swap | `[3, 7]` |
| **Sort Pair 2 (Desc)** | Compare (4,8) -> Swap (4,8) | `[8, 4]` |
| **Bitonic Sequence** | Combine `[3, 7]` and `[8, 4]` | `[3, 7, 8, 4]` (Bitonic!) |
| **Merge Stride 2** | Compare (3,8) -> Swap, Compare (7,4) -> Swap | `[3, 4, 8, 7]` |
| **Merge Stride 1** | Compare (3,4) -> OK, Compare (8,7) -> Swap | `[3, 4, 7, 8]` (Sorted!) |

## 13. Time & Space Complexity

| Case | Sequential Time Complexity | Parallel Time Complexity ($n$ threads) |
| :--- | :--- | :--- |
| **All Cases** | $\mathcal{O}(n \log^2 n)$ | $\mathcal{O}(\log^2 n)$ parallel stages |
| **Space Complexity** | $\mathcal{O}(\log^2 n)$ recursive stack | $\mathcal{O}(1)$ iterative network |

## 14. Advantages

- **Data-Independent Comparisons:** Fixed, deterministic comparison network structure perfect for hardware implementation.
- **Extremely Fast on GPUs:** High thread utilization without branch divergence.
- **In-Place:** Does not require extra array allocations.

## 15. Disadvantages

- **Power-of-Two Constraint:** Array size $n$ must be a power of 2 (or padded with dummy infinity values).
- **Sequential Overhead:** $\mathcal{O}(n \log^2 n)$ sequential complexity is slower than Quick Sort ($\mathcal{O}(n \log n)$) on single-threaded CPUs.
- **Unstable:** Long-distance comparisons destroy stability.

## 16. Applications

- GPU parallel sorting algorithms (NVIDIA CUDA SDK Bitonic Sort).
- FPGA sorting network circuits.
- Optical and quantum comparator networks.

## 17. Common Mistakes

- **Passing Non-Power-of-Two Length:** Causes infinite recursion or out-of-bounds array access.
- **Direction Inversion Bugs:** Mismatched `dir` parameters during left/right recursive passes.

## 18. Interview Questions

1. What is a "bitonic sequence"?
2. What is the main advantage of Bitonic Sort over Quick Sort on GPUs?
3. What is the parallel time complexity of Bitonic Sort with $n$ processors?
4. How do you handle input arrays whose size is not a power of 2?

## 19. Practice Problems

**Easy:**
1. Implement Bitonic Sort for an array of 8 integers.
2. Check if a given array is a bitonic sequence.

**Medium:**
3. Implement an iterative (non-recursive) Bitonic Sort using bitwise XOR operations.
4. Adapt Bitonic Sort for arrays whose size is not a power of 2 using padding.

**Hard:**
5. Write a CUDA kernel implementing Bitonic Sort on 1024 GPU threads.

## 20. Related Algorithms

- [Merge Sort](./14_merge_sort.md) (Divide-and-conquer parent algorithm)
- [Odd-Even Sort](./28_odd_even_sort.md) (Parallel sorting network alternative)

## 21. Summary

Bitonic Sort is a parallel sorting network algorithm created by Ken Batcher. By forming bitonic sequences and recursively executing data-independent comparison passes, it achieves $\mathcal{O}(\log^2 n)$ parallel execution steps on $n$ processors, making it the algorithm of choice for hardware circuits and GPU computing.

## 22. Quiz

**Question 1:** Who invented Bitonic Sort in 1968?
- A) Ken Batcher
- B) Tim Peters
- C) Tony Hoare
- D) David Musser
- **Correct Answer:** A
- **Explanation:** Ken Batcher invented Bitonic Sort and Odd-Even Mergesort in 1968.

**Question 2:** What is a bitonic sequence?
- A) A sequence of even numbers
- B) A sequence that increases then decreases (or a circular shift thereof)
- C) A sequence containing only 0s and 1s
- D) A sequence of prime numbers
- **Correct Answer:** B
- **Explanation:** A bitonic sequence monotonically increases up to a peak and then monotonically decreases.

**Question 3:** What constraint does standard Bitonic Sort impose on array length $n$?
- A) $n$ must be an odd number
- B) $n$ must be a prime number
- C) $n$ must be a power of 2 ($n = 2^k$)
- D) $n \le 100$
- **Correct Answer:** C
- **Explanation:** Bitonic splitting requires repeated division into equal binary pairs.

**Question 4:** What is the parallel time complexity of Bitonic Sort on $n$ processors?
- A) $\mathcal{O}(n^2)$
- B) $\mathcal{O}(n \log n)$
- C) $\mathcal{O}(\log^2 n)$
- D) $\mathcal{O}(1)$
- **Correct Answer:** C
- **Explanation:** Bitonic Sort runs in $\mathcal{O}(\log^2 n)$ parallel time stages.

**Question 5:** What is the sequential time complexity of Bitonic Sort on a single CPU thread?
- A) $\mathcal{O}(n \log^2 n)$
- B) $\mathcal{O}(n \log n)$
- C) $\mathcal{O}(n^2)$
- D) $\mathcal{O}(n)$
- **Correct Answer:** A
- **Explanation:** Sequential execution performs $\mathcal{O}(n \log^2 n)$ total comparison steps.

**Question 6:** Why is Bitonic Sort ideal for GPUs and FPGAs?
- A) It uses less memory than any algorithm
- B) Its comparison pattern is completely data-independent (no branch divergence)
- C) It is a stable sort
- D) It sorts strings faster
- **Correct Answer:** B
- **Explanation:** Data-independent execution eliminates branch mispredictions on parallel hardware.

**Question 7:** Is Bitonic Sort a stable sorting algorithm?
- A) Yes
- B) No
- **Correct Answer:** B
- **Explanation:** Distance comparison swaps change the relative order of duplicate items.

**Question 8:** How can non-power-of-two arrays be processed with Bitonic Sort?
- A) By throwing an exception
- B) By padding the array with dummy infinity values up to the next power of 2
- C) By truncating the array
- D) By running Bubble Sort instead
- **Correct Answer:** B
- **Explanation:** Padding with $+\infty$ extends length to $2^k$, which can be trimmed after sorting.

**Question 9:** What does `bitonicMerge` do to a bitonic sequence of length $k$?
- A) Reverses the sequence
- B) Splits it into two bitonic sequences where all items in the first half are $\le$ items in the second half
- C) Deletes duplicate elements
- D) Sums all values
- **Correct Answer:** B
- **Explanation:** Comparing items at distance $k/2$ separates values into two smaller ordered bitonic halves.

**Question 10:** What is the space complexity of iterative Bitonic Sort?
- A) $\mathcal{O}(n)$
- B) $\mathcal{O}(1)$
- C) $\mathcal{O}(n^2)$
- D) $\mathcal{O}(\log n)$
- **Correct Answer:** B
- **Explanation:** Iterative hardware or loop implementations run completely in-place with $\mathcal{O}(1)$ space.
