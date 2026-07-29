# Bucket Sort

## 1. Introduction

Bucket Sort (also known as Bin Sort) is a distribution-based sorting algorithm that works by partitioning an array into a number of small groups called "buckets". Each bucket is then sorted individually, either using a different sorting algorithm (typically Insertion Sort) or by recursively applying the Bucket Sort algorithm. Finally, the sorted buckets are concatenated in order to produce the final sorted array.

Imagine sorting a large stack of exam answer sheets scored from 0.00 to 1.00. Instead of comparing each paper against all others, you place sheets with scores in `[0.0, 0.1)` into Bin 0, scores in `[0.1, 0.2)` into Bin 1, and so on up to Bin 9. You then sort each small bin independently and stack Bin 0 through Bin 9 together. That is the essence of Bucket Sort.

It was developed to take advantage of uniformly distributed input data, offering average-case linear time complexity $\mathcal{O}(n + k)$.

You should use Bucket Sort when the input is uniformly distributed over a known range (such as floating-point numbers in `[0, 1)` or uniformly distributed real numbers).

## 2. Why Use This Algorithm?

Bucket Sort offers near-linear efficiency when data distribution properties are known beforehand.

**Benefits:**
- **Linear-Time Average Case:** Runs in $\mathcal{O}(n + k)$ when input elements are uniformly distributed across buckets.
- **Cache & Parallel Friendly:** Buckets can be processed completely independently in parallel threads.
- **Versatile:** Can use any suitable sorting algorithm for individual buckets.
- **Preserves Subroutine Stability:** If individual bucket sorting subroutines are stable, Bucket Sort remains stable.

**Performance:**
- **Best Case:** $\mathcal{O}(n + k)$
- **Average Case:** $\mathcal{O}(n + k)$
- **Worst Case:** $\mathcal{O}(n^2)$ (when all elements fall into a single bucket and insertion sort is used)
- **Space Complexity:** $\mathcal{O}(n + k)$ auxiliary space for buckets.

**When it is better than other algorithms:**
Bucket Sort outperforms Quick Sort and Merge Sort when sorting uniformly distributed floating-point values or numbers bounded in a known range.

## 3. Real-World Applications

- **Sorting Floating-Point Numbers:** Standard choice for sorting uniform decimal data in `[0, 1)`.
- **Histogram & Density Calculations:** Used in statistics and data analysis for binning datasets.
- **External Sorting:** Distributed map-reduce frameworks use bucket partitioning to distribute work across clusters.
- **Graphics & Spatial Indexing:** Partitioning 2D/3D points into spatial grid buckets for collision detection.

## 4. Prerequisites

Before learning Bucket Sort, you should know:
- Dynamic list arrays or vectors in your target language.
- How to map float ranges to integer bucket indices (`floor(n * val)`).
- [Insertion Sort](./13_insertion_sort.md) (used to sort individual buckets).
- Basic understanding of statistical uniform distribution.

## 5. Visualization

Given Array of Floats: `[0.78, 0.17, 0.39, 0.26, 0.72, 0.94, 0.21, 0.12]`

1. **Scatter (Partition into 10 Buckets `[0..9]`):**
   - Bucket 1: `0.17`, `0.12`
   - Bucket 2: `0.26`, `0.21`
   - Bucket 3: `0.39`
   - Bucket 7: `0.78`, `0.72`
   - Bucket 9: `0.94`

2. **Sort Individual Buckets:**
   - Bucket 1: `[0.12, 0.17]`
   - Bucket 2: `[0.21, 0.26]`
   - Bucket 3: `[0.39]`
   - Bucket 7: `[0.72, 0.78]`
   - Bucket 9: `[0.94]`

3. **Gather (Concatenate Buckets):**
   - Result: `[0.12, 0.17, 0.21, 0.26, 0.39, 0.72, 0.78, 0.94]`

## 6. How It Works

1. Create $k$ empty buckets.
2. Iterate through the input array and place each element `arr[i]` into bucket index `floor(k * arr[i])`.
3. Sort each non-empty bucket individually using Insertion Sort.
4. Concatenate all sorted buckets sequentially back into the main array.

## 7. Step-by-Step Algorithm

1. Initialize $n$ empty buckets (where $n$ is array length).
2. For each element `x` in input array:
   - Compute `index = floor(n * x)`.
   - Insert `x` into `buckets[index]`.
3. For each bucket `b` in `buckets`:
   - Sort `b` using Insertion Sort.
4. Concatenate elements from all buckets in order into `arr`.
5. Return `arr`.

## 8. Pseudocode

```text
function bucketSort(arr):
    n = length(arr)
    if n <= 1:
        return arr

    buckets = array of n empty lists

    for i = 0 to n - 1:
        bucketIndex = floor(n * arr[i])
        append arr[i] to buckets[bucketIndex]

    for i = 0 to n - 1:
        insertionSort(buckets[i])

    index = 0
    for i = 0 to n - 1:
        for item in buckets[i]:
            arr[index] = item
            index = index + 1

    return arr
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>

struct Node {
    float data;
    struct Node* next;
};

void insertionSortList(struct Node** list) {
    if (*list == NULL || (*list)->next == NULL) return;
    struct Node* sorted = NULL;
    struct Node* current = *list;

    while (current != NULL) {
        struct Node* next = current->next;
        if (sorted == NULL || sorted->data >= current->data) {
            current->next = sorted;
            sorted = current;
        } else {
            struct Node* temp = sorted;
            while (temp->next != NULL && temp->next->data < current->data) {
                temp = temp->next;
            }
            current->next = temp->next;
            temp->next = current;
        }
        current = next;
    }
    *list = sorted;
}

void bucketSort(float arr[], int n) {
    struct Node** buckets = (struct Node**)calloc(n, sizeof(struct Node*));

    for (int i = 0; i < n; i++) {
        int idx = (int)(n * arr[i]);
        struct Node* newNode = (struct Node*)malloc(sizeof(struct Node));
        newNode->data = arr[i];
        newNode->next = buckets[idx];
        buckets[idx] = newNode;
    }

    for (int i = 0; i < n; i++) {
        insertionSortList(&buckets[i]);
    }

    int index = 0;
    for (int i = 0; i < n; i++) {
        struct Node* curr = buckets[i];
        while (curr != NULL) {
            arr[index++] = curr->data;
            struct Node* temp = curr;
            curr = curr->next;
            free(temp);
        }
    }
    free(buckets);
}

int main() {
    float arr[] = {0.78f, 0.17f, 0.39f, 0.26f, 0.72f, 0.94f, 0.21f, 0.12f};
    int n = sizeof(arr) / sizeof(arr[0]);
    bucketSort(arr, n);
    for (int i = 0; i < n; i++) printf("%.2f ", arr[i]);
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

void bucketSort(std::vector<float>& arr) {
    int n = arr.size();
    if (n <= 1) return;

    std::vector<std::vector<float>> buckets(n);

    for (int i = 0; i < n; i++) {
        int bucketIndex = static_cast<int>(n * arr[i]);
        if (bucketIndex >= n) bucketIndex = n - 1;
        buckets[bucketIndex].push_back(arr[i]);
    }

    for (int i = 0; i < n; i++) {
        std::sort(buckets[i].begin(), buckets[i].end());
    }

    int index = 0;
    for (int i = 0; i < n; i++) {
        for (float val : buckets[i]) {
            arr[index++] = val;
        }
    }
}

int main() {
    std::vector<float> arr = {0.78f, 0.17f, 0.39f, 0.26f, 0.72f, 0.94f, 0.21f, 0.12f};
    bucketSort(arr);
    for (float v : arr) std::cout << v << " ";
    std::cout << "\n";
    return 0;
}
```

### Java
```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.Arrays;

public class BucketSort {
    public static void bucketSort(float[] arr) {
        int n = arr.length;
        if (n <= 1) return;

        List<Float>[] buckets = new ArrayList[n];
        for (int i = 0; i < n; i++) {
            buckets[i] = new ArrayList<>();
        }

        for (int i = 0; i < n; i++) {
            int bucketIdx = (int) (n * arr[i]);
            if (bucketIdx >= n) bucketIdx = n - 1;
            buckets[bucketIdx].add(arr[i]);
        }

        for (int i = 0; i < n; i++) {
            Collections.sort(buckets[i]);
        }

        int index = 0;
        for (int i = 0; i < n; i++) {
            for (float val : buckets[i]) {
                arr[index++] = val;
            }
        }
    }

    public static void main(String[] args) {
        float[] arr = {0.78f, 0.17f, 0.39f, 0.26f, 0.72f, 0.94f, 0.21f, 0.12f};
        bucketSort(arr);
        System.out.println(Arrays.toString(arr));
    }
}
```

### Python
```python
def bucket_sort(arr: list[float]) -> list[float]:
    n = len(arr)
    if n <= 1:
        return arr

    buckets: list[list[float]] = [[] for _ in range(n)]

    for val in arr:
        idx = int(n * val)
        if idx >= n:
            idx = n - 1
        buckets[idx].append(val)

    for b in buckets:
        b.sort()

    index = 0
    for b in buckets:
        for val in b:
            arr[index] = val
            index += 1

    return arr

if __name__ == "__main__":
    data = [0.78, 0.17, 0.39, 0.26, 0.72, 0.94, 0.21, 0.12]
    bucket_sort(data)
    print(data)
```

### JavaScript
```javascript
function bucketSort(arr) {
    const n = arr.length;
    if (n <= 1) return arr;

    const buckets = Array.from({ length: n }, () => []);

    for (let i = 0; i < n; i++) {
        let idx = Math.floor(n * arr[i]);
        if (idx >= n) idx = n - 1;
        buckets[idx].push(arr[i]);
    }

    for (let i = 0; i < n; i++) {
        buckets[i].sort((a, b) => a - b);
    }

    let index = 0;
    for (let i = 0; i < n; i++) {
        for (let j = 0; j < buckets[i].length; j++) {
            arr[index++] = buckets[i][j];
        }
    }

    return arr;
}

const data = [0.78, 0.17, 0.39, 0.26, 0.72, 0.94, 0.21, 0.12];
bucketSort(data);
console.log(data);
```

## 10. Code Explanation

The implementation allocates $n$ bucket containers. For every value `v` in the input, `floor(n * v)` converts the floating-point range into an integer index mapping into the corresponding bucket. Next, standard sorting (`sort` or Insertion Sort) processes each individual bucket list. Because each bucket contains only a few elements under uniform distribution, sorting individual buckets runs in $\mathcal{O}(1)$ average time per bucket. Finally, the sorted buckets are flattened sequentially into the original array.

## 11. Interactive Demo

The demo displays $n$ vertical clear glass jars labeled $0$ through $n-1$. Floating colored spheres with float values enter from the top.

When "Run Bucket Sort" is pressed:
1. Each sphere drops into its computed bucket jar.
2. The spheres inside each jar physically rearrange themselves from smallest value at the bottom to largest at the top.
3. The jars open from left to right, pouring their ordered contents into a single linear conveyer belt.

## 12. Dry Run

**Input:** `[0.42, 0.32, 0.33, 0.52]` ($n = 4$)

| Value | Bucket Calculation (`floor(4 * val)`) | Assigned Bucket Index | State of Buckets |
| :--- | :--- | :--- | :--- |
| `0.42` | `floor(1.68) = 1` | `1` | `B0:[], B1:[0.42], B2:[], B3:[]` |
| `0.32` | `floor(1.28) = 1` | `1` | `B0:[], B1:[0.42, 0.32], B2:[], B3:[]` |
| `0.33` | `floor(1.32) = 1` | `1` | `B0:[], B1:[0.42, 0.32, 0.33], B2:[], B3:[]` |
| `0.52` | `floor(2.08) = 2` | `2` | `B0:[], B1:[0.42, 0.32, 0.33], B2:[0.52], B3:[]` |

**Sort Buckets:** `B1` becomes `[0.32, 0.33, 0.42]`, `B2` stays `[0.52]`.  
**Flatten Output:** `[0.32, 0.33, 0.42, 0.52]`.

## 13. Time & Space Complexity

| Case | Time Complexity | Reason |
| :--- | :--- | :--- |
| **Best Case** | $\mathcal{O}(n + k)$ | Uniform distribution, 1 element per bucket |
| **Average Case** | $\mathcal{O}(n + k)$ | Elements evenly distributed across buckets |
| **Worst Case** | $\mathcal{O}(n^2)$ | All elements land in 1 bucket (Insertion Sort worst case) |
| **Space Complexity** | $\mathcal{O}(n + k)$ | Auxiliary space for $k$ buckets and $n$ elements |

## 14. Advantages

- **Linear Average Time:** Achieves $\mathcal{O}(n)$ time for uniform floating-point data.
- **Parallel processing:** Buckets can be processed concurrently on multiple threads.
- **Flexible Inner Subroutine:** Can pick any sorting algorithm for individual buckets.

## 15. Disadvantages

- **Sensitive to Distribution:** Non-uniform clustering causes performance to collapse to $\mathcal{O}(n^2)$.
- **Memory Overhead:** Requires extra space for $k$ bucket structures.
- **Requires Range Mapping:** Must know min/max range boundaries to compute bucket indices.

## 16. Applications

- Sorting normalized floating-point numbers in graphics.
- MapReduce distributed partitioning phase.
- External sorting of large disk files.

## 17. Common Mistakes

- **Index Out of Bounds:** Forgetting that `arr[i] = 1.0` results in `bucketIndex = n`, which requires clamping to `n - 1`.
- **Assuming Non-Uniform Efficiency:** Applying Bucket Sort to highly clustered data without dynamic normalization.
- **Memory Leaks in C:** Forgetting to free linked list bucket nodes.

## 18. Interview Questions

1. What condition is required for Bucket Sort to run in $\mathcal{O}(n)$ average time?
2. How does Bucket Sort handle numbers outside the `[0, 1)` interval?
3. What is the worst-case time complexity of Bucket Sort and when does it occur?
4. Compare Bucket Sort and Radix Sort.
5. How can you optimize Bucket Sort if data is skewed?

## 19. Practice Problems

**Easy:**
1. Implement Bucket Sort for floating-point values in `[0, 1)`.
2. Sort an array of integers bounded between 0 and 100 using Bucket Sort.

**Medium:**
3. Adapt Bucket Sort to handle arbitrary min and max ranges `[min, max]`.
4. Implement multi-threaded Bucket Sort in C++ or Java.

**Hard:**
5. Implement dynamic bucket sizing based on variance analysis of the input data.

## 20. Related Algorithms

- [Radix Sort](./19_radix_sort.md) (Digit-based distribution sort)
- [Counting Sort](./18_counting_sort.md) (Value frequency counting sort)
- [Insertion Sort](./13_insertion_sort.md) (Common inner subroutine for Bucket Sort)

## 21. Summary

Bucket Sort partitions data into multiple ordered bins based on numerical range, sorts each bin independently, and concatenates the results. When data is uniformly distributed, it achieves linear time complexity $\mathcal{O}(n + k)$, making it extremely efficient for floating-point and range-bounded data.

## 22. Quiz

**Question 1:** What is the average time complexity of Bucket Sort for uniform input?
- A) $\mathcal{O}(n \log n)$
- B) $\mathcal{O}(n + k)$
- C) $\mathcal{O}(n^2)$
- D) $\mathcal{O}(\log n)$
- **Correct Answer:** B
- **Explanation:** Uniform distribution places $\mathcal{O}(1)$ elements in each bucket, giving linear $\mathcal{O}(n + k)$ average time.

**Question 2:** What is the worst-case time complexity of Bucket Sort using Insertion Sort for buckets?
- A) $\mathcal{O}(n)$
- B) $\mathcal{O}(n \log n)$
- C) $\mathcal{O}(n^2)$
- D) $\mathcal{O}(n^3)$
- **Correct Answer:** C
- **Explanation:** If all $n$ elements land in a single bucket, Insertion Sort takes $\mathcal{O}(n^2)$ time.

**Question 3:** Which data type is Bucket Sort most naturally suited for?
- A) Large unsorted strings of random lengths
- B) Uniformly distributed floating-point numbers in `[0, 1)`
- C) Linked list nodes
- D) Binary trees
- **Correct Answer:** B
- **Explanation:** Float range mapping `floor(n * val)` naturally fits `[0, 1)` ranges.

**Question 4:** Is standard Bucket Sort an in-place algorithm?
- A) Yes
- B) No
- **Correct Answer:** B
- **Explanation:** It requires $\mathcal{O}(n + k)$ extra space for bucket storage.

**Question 5:** What happens if `val = 1.0` in standard bucket index formula `floor(n * val)`?
- A) Index is `-1`
- B) Index equals `n`, which causes an out-of-bounds error if unclamped
- C) Index equals `0`
- D) Memory allocation failure
- **Correct Answer:** B
- **Explanation:** `floor(n * 1.0) = n`, which exceeds valid index range `0..n-1` unless clamped.

**Question 6:** How can bucket processing be accelerated on modern multi-core CPUs?
- A) By sorting buckets sequentially
- B) By running bucket sorts concurrently on separate threads
- C) By converting floats to integers
- D) By disabling garbage collection
- **Correct Answer:** B
- **Explanation:** Buckets are completely independent, enabling clean multi-threaded execution.

**Question 7:** Which algorithm is commonly used to sort individual buckets in Bucket Sort?
- A) Quick Sort
- B) Insertion Sort
- C) Heap Sort
- D) Bubble Sort
- **Correct Answer:** B
- **Explanation:** Insertion sort has low overhead and runs fast on tiny, nearly-sorted bucket lists.

**Question 8:** What is the space complexity of Bucket Sort with $n$ elements and $k$ buckets?
- A) $\mathcal{O}(1)$
- B) $\mathcal{O}(n + k)$
- C) $\mathcal{O}(n \cdot k)$
- D) $\mathcal{O}(\log n)$
- **Correct Answer:** B
- **Explanation:** Memory is allocated for $k$ bucket headers and $n$ total elements.

**Question 9:** Can Bucket Sort be made stable?
- A) No, never
- B) Yes, if the bucket insertion and inner bucket sorting subroutine are stable
- C) Only for integer inputs
- D) Only when $k = 1$
- **Correct Answer:** B
- **Explanation:** Preserving element arrival order during insertion and using a stable inner sort maintains stability.

**Question 10:** What is the main drawback of Bucket Sort?
- A) Cannot sort numbers
- B) Performance degrades significantly under skewed or non-uniform data distributions
- C) Cannot be implemented in Python
- D) Requires $O(n^2)$ space
- **Correct Answer:** B
- **Explanation:** Skewed data causes element clustering in few buckets, degrading performance toward $\mathcal{O}(n^2)$.
