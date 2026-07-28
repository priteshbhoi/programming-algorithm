# Quick Sort

## 1. Introduction

Quick Sort is one of the most widely used sorting algorithms in practice. It works by selecting a "pivot" element from the array and partitioning the other elements into two groups: those less than the pivot and those greater than the pivot. The pivot is then in its final sorted position. This process is repeated recursively for each subgroup until the entire array is sorted. Imagine organizing a bookshelf by picking one book, placing all thinner books to its left and all thicker books to its right, then repeating the process for each side.

It was invented by Tony Hoare in 1959 and has become the default sorting algorithm in most standard libraries due to its excellent average-case performance and low memory overhead.

You should use Quick Sort when you need fast in-memory sorting, when average-case performance matters more than worst-case guarantees, and when memory is limited.

## 2. Why Use This Algorithm?

Quick Sort dominates in practice because of its cache efficiency and low overhead.

**Benefits:**
- Excellent average-case performance: O(n log n)
- In-place sorting with O(log n) stack space
- Cache-friendly due to sequential memory access patterns
- Simple to implement and widely understood
- Tail-call optimization possible

**Performance:**
On average, Quick Sort is 2-3 times faster than Merge Sort for in-memory arrays due to better cache locality and fewer memory allocations. Its in-place nature makes it ideal for systems with limited RAM.

**When it is better than other algorithms:**
Quick Sort beats Merge Sort for in-memory sorting when stability is not required. It beats Heap Sort in practice due to better constant factors. It is the algorithm of choice for general-purpose sorting in C (qsort), C++ (std::sort), and Java (for primitives).

## 3. Real-World Applications

- **C standard library qsort():** The classic C sorting function uses Quick Sort.
- **C++ std::sort():** Uses Introsort (Quick Sort + Heap Sort hybrid).
- **Java Arrays.sort() for primitives:** Dual-Pivot Quick Sort.
- **Database query optimization:** Sorting intermediate results during query execution.
- **File system indexing:** Sorting directory entries and file metadata.
- **Competitive programming:** Default choice for fast in-memory sorting.
- **3D rendering:** Sorting polygons by depth (Z-sorting) in graphics pipelines.

## 4. Prerequisites

Before learning Quick Sort, you should know:
- Recursion and the call stack
- Arrays and indexing
- The partition operation
- Pivot selection strategies
- Understanding of time complexity and probabilistic analysis
- Tail recursion optimization (helpful but not mandatory)

## 5. Visualization

Picture a row of blocks of varying heights. You pick one block as the pivot and hold it up. All shorter blocks are pushed to the left side, and all taller blocks are pushed to the right side. The pivot block is then placed in the gap between them — its final sorted position. Now you have two smaller groups: the short blocks on the left and the tall blocks on the right. You repeat the process on each group independently, picking new pivots and partitioning, until every group has only one block.

## 6. How It Works

Quick Sort follows three steps:
1. **Choose a pivot:** Select an element from the array to act as the partition point.
2. **Partition:** Rearrange the array so all elements less than the pivot come before it, and all elements greater come after it.
3. **Recursively sort:** Apply Quick Sort to the subarrays on either side of the pivot.

The magic is in the partition step. A good partition places the pivot in its final position and creates two smaller sorting problems. The base case is a subarray with 0 or 1 elements, which is already sorted.

## 7. Step-by-Step Algorithm

1. If the subarray has 0 or 1 elements, it is already sorted. Return.
2. Choose a pivot element from the subarray.
3. Partition the subarray around the pivot:
   1. Initialize a pointer at the start of the subarray.
   2. Scan through the subarray.
   3. For each element smaller than the pivot, swap it with the element at the pointer and advance the pointer.
   4. After scanning, swap the pivot into its correct position at the pointer.
4. Recursively sort the left subarray (elements before the pivot).
5. Recursively sort the right subarray (elements after the pivot).

## 8. Pseudocode

```
function quickSort(array, low, high):
    if low < high:
        pivotIndex = partition(array, low, high)
        quickSort(array, low, pivotIndex - 1)
        quickSort(array, pivotIndex + 1, high)

function partition(array, low, high):
    pivot = array[high]
    i = low - 1
    for j from low to high - 1:
        if array[j] <= pivot:
            i = i + 1
            swap array[i] and array[j]
    swap array[i + 1] and array[high]
    return i + 1
```

## 9. Code Examples

### C
```c
#include <stdio.h>

void swap(int* a, int* b) {
    int temp = *a;
    *a = *b;
    *b = temp;
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

void quickSort(int arr[], int low, int high) {
    if (low < high) {
        int pi = partition(arr, low, high);
        quickSort(arr, low, pi - 1);
        quickSort(arr, pi + 1, high);
    }
}

int main() {
    int arr[] = {10, 7, 8, 9, 1, 5};
    int n = sizeof(arr) / sizeof(arr[0]);
    quickSort(arr, 0, n - 1);
    printf("Sorted array: ");
    for (int i = 0; i < n; i++) printf("%d ", arr[i]);
    printf("\n");
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
using namespace std;

int partition(vector<int>& arr, int low, int high) {
    int pivot = arr[high];
    int i = low - 1;
    for (int j = low; j < high; j++) {
        if (arr[j] <= pivot) {
            i++;
            swap(arr[i], arr[j]);
        }
    }
    swap(arr[i + 1], arr[high]);
    return i + 1;
}

void quickSort(vector<int>& arr, int low, int high) {
    if (low < high) {
        int pi = partition(arr, low, high);
        quickSort(arr, low, pi - 1);
        quickSort(arr, pi + 1, high);
    }
}

int main() {
    vector<int> arr = {10, 7, 8, 9, 1, 5};
    quickSort(arr, 0, arr.size() - 1);
    cout << "Sorted array: ";
    for (int x : arr) cout << x << " ";
    cout << endl;
    return 0;
}
```

### Java
```java
public class QuickSort {
    static int partition(int[] arr, int low, int high) {
        int pivot = arr[high];
        int i = low - 1;
        for (int j = low; j < high; j++) {
            if (arr[j] <= pivot) {
                i++;
                int temp = arr[i];
                arr[i] = arr[j];
                arr[j] = temp;
            }
        }
        int temp = arr[i + 1];
        arr[i + 1] = arr[high];
        arr[high] = temp;
        return i + 1;
    }

    static void quickSort(int[] arr, int low, int high) {
        if (low < high) {
            int pi = partition(arr, low, high);
            quickSort(arr, low, pi - 1);
            quickSort(arr, pi + 1, high);
        }
    }

    public static void main(String[] args) {
        int[] arr = {10, 7, 8, 9, 1, 5};
        quickSort(arr, 0, arr.length - 1);
        System.out.print("Sorted array: ");
        for (int x : arr) System.out.print(x + " ");
        System.out.println();
    }
}
```

### Python
```python
def partition(arr, low, high):
    pivot = arr[high]
    i = low - 1
    for j in range(low, high):
        if arr[j] <= pivot:
            i += 1
            arr[i], arr[j] = arr[j], arr[i]
    arr[i + 1], arr[high] = arr[high], arr[i + 1]
    return i + 1

def quick_sort(arr, low, high):
    if low < high:
        pi = partition(arr, low, high)
        quick_sort(arr, low, pi - 1)
        quick_sort(arr, pi + 1, high)

arr = [10, 7, 8, 9, 1, 5]
quick_sort(arr, 0, len(arr) - 1)
print("Sorted array:", arr)
```

### JavaScript
```javascript
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

function quickSort(arr, low, high) {
    if (low < high) {
        const pi = partition(arr, low, high);
        quickSort(arr, low, pi - 1);
        quickSort(arr, pi + 1, high);
    }
}

const arr = [10, 7, 8, 9, 1, 5];
quickSort(arr, 0, arr.length - 1);
console.log("Sorted array:", arr.join(" "));
```

## 10. Code Explanation

The `partition` function uses the Lomuto scheme (pivot at the end). Pointer `i` marks the boundary between elements smaller than the pivot and those larger. Pointer `j` scans the array. Whenever `arr[j]` is less than or equal to the pivot, `i` is incremented and a swap places the small element on the left side. After the scan, the pivot is swapped into position `i+1`, which is its final sorted position. The `quickSort` function then recursively sorts the left and right partitions. The beauty is that after partitioning, the pivot never moves again.

## 11. Interactive Demo

The demo shows a row of bars. The user clicks "Sort." The last bar is highlighted as the pivot (e.g., red). The algorithm scans left to right: bars smaller than the pivot are swapped to the left side (turning green), while larger ones stay on the right (gray). When the scan completes, the pivot slides into its final position (turns blue). The array visually splits into left and right groups with gaps. The process repeats recursively on each group. A recursion tree on the side shows the current subarray being processed. The user can step through or watch the full animation.

## 12. Dry Run

**Sample Input:**
Array: `[10, 7, 8, 9, 1, 5]`

| Call | low | high | pivot | Partition Result | Array After Partition |
|------|-----|------|-------|------------------|----------------------|
| quickSort | 0 | 5 | 5 | pivot index = 2 | `[1, 5, 8, 9, 10, 7]` |
| quickSort | 0 | 1 | 5 | pivot index = 1 | `[1, 5, ...]` (left sorted) |
| quickSort | 3 | 5 | 7 | pivot index = 5 | `[..., 7, 8, 9, 10]` |
| quickSort | 3 | 4 | 10 | pivot index = 5 | `[..., 7, 8, 9, 10]` |
| quickSort | 3 | 3 | - | base case | - |
| quickSort | 5 | 5 | - | base case | - |

**Final Output:** `[1, 5, 7, 8, 9, 10]`

## 13. Time & Space Complexity

| Case | Time Complexity | Reason |
|------|-----------------|--------|
| Best Case | O(n log n) | Balanced partitions, each level processes n elements |
| Average Case | O(n log n) | Random pivots produce reasonably balanced partitions on average |
| Worst Case | O(n²) | Already sorted or reverse-sorted array with last-element pivot |
| Space Complexity | O(log n) | Recursion stack depth in average case |

## 14. Advantages

- **Fast in practice:** Cache-friendly and low overhead make it 2-3x faster than Merge Sort.
- **In-place:** Sorts without allocating auxiliary arrays.
- **Simple to implement:** The core algorithm fits in a few lines.
- **Tail-call optimizable:** Can be optimized to sort the smaller partition first.
- **Good locality of reference:** Sequential scanning during partitioning is cache-friendly.

## 15. Disadvantages

- **Worst-case O(n²):** Poor pivot choices can degrade performance catastrophically.
- **Not stable:** The partition process changes the relative order of equal elements.
- **Recursive stack risk:** Deep recursion on bad inputs can cause stack overflow.
- **Sensitive to pivot choice:** Performance heavily depends on pivot selection strategy.

## 16. Applications

- General-purpose in-memory sorting (C qsort, C++ std::sort for primitives)
- Competitive programming due to its speed and simplicity
- Sorting large datasets where average-case performance matters
- Database internal sorting operations
- Graphics rendering pipelines (Z-sorting polygons)
- As a component in hybrid algorithms like Introsort

## 17. Common Mistakes

- **Always picking the last element as pivot:** This causes O(n²) on sorted input. Use random or median-of-three instead.
- **Off-by-one in partition:** Ensure `i` starts at `low - 1` and the final swap uses `i + 1`.
- **Not handling duplicates efficiently:** Many equal elements can cause unbalanced partitions.
- **Stack overflow:** On large or pathological inputs, the recursion depth can exceed stack limits.
- **Confusing it with stable sort:** Do not use Quick Sort when stability is required.

## 18. Interview Questions

1. What is the average-case and worst-case time complexity of Quick Sort?
2. Why is Quick Sort faster than Merge Sort in practice for in-memory arrays?
3. How can you avoid the O(n²) worst case in Quick Sort?
4. Is Quick Sort stable? Why or why not?
5. What is the space complexity of Quick Sort?
6. Explain the Lomuto partition scheme vs. the Hoare partition scheme.
7. How does random pivot selection improve Quick Sort?
8. What is Introsort, and how does it relate to Quick Sort?
9. Can Quick Sort be implemented iteratively?
10. How would you modify Quick Sort to find the k-th smallest element?

## 19. Practice Problems

**Easy:**
1. Implement Quick Sort using the Lomuto partition scheme.
2. Implement Quick Sort using the Hoare partition scheme.
3. Sort an array in descending order using Quick Sort.
4. Trace through Quick Sort manually for a small array.

**Medium:**
5. Implement Quick Sort with random pivot selection.
6. Implement Quick Sort with median-of-three pivot selection.
7. Use Quick Sort to find the k-th smallest element (QuickSelect).
8. Sort an array with many duplicate elements efficiently (3-way Quick Sort).

**Hard:**
9. Implement an iterative version of Quick Sort using an explicit stack.
10. Implement 3-way Quick Sort (Dutch National Flag partitioning).
11. Design a parallel Quick Sort algorithm for multi-core processors.

## 20. Related Algorithms

- Merge Sort (stable, guaranteed O(n log n), but needs extra space)
- Heap Sort (guaranteed O(n log n) in-place, but slower in practice)
- Introsort (hybrid of Quick Sort, Heap Sort, and Insertion Sort)
- Tim Sort (stable hybrid used in Python/Java)
- QuickSelect (finding k-th smallest element using partition)

## 21. Summary

Quick Sort is the workhorse of in-memory sorting. By partitioning around a pivot and recursively sorting subarrays, it achieves excellent average-case performance with minimal memory overhead. Its speed comes from cache-friendly sequential access and in-place operation. The main risk is the O(n²) worst case, which can be mitigated through random or median-of-three pivot selection. While not stable, its practical performance makes it the default choice for sorting primitives in virtually every programming language. Master Quick Sort, and you master the most important sorting algorithm in computer science.

## 22. Quiz

**Question 1:** What is the average-case time complexity of Quick Sort?
- A) O(n)
- B) O(n log n)
- C) O(n²)
- D) O(log n)
- **Correct Answer:** B
- **Explanation:** On average, balanced partitions produce a recursion tree of height log n with n work per level.

**Question 2:** What is the worst-case time complexity?
- A) O(n)
- B) O(n log n)
- C) O(n²)
- D) O(2^n)
- **Correct Answer:** C
- **Explanation:** With poor pivot choices (e.g., always the last element on sorted data), partitions are highly unbalanced.

**Question 3:** Is Quick Sort stable?
- A) Yes
- B) No
- C) Only for integers
- D) Only with random pivots
- **Correct Answer:** B
- **Explanation:** The partition process swaps non-adjacent elements, which can change the relative order of equal elements.

**Question 4:** What is the space complexity in the average case?
- A) O(1)
- B) O(log n)
- C) O(n)
- D) O(n²)
- **Correct Answer:** B
- **Explanation:** The recursion stack depth is proportional to the height of the recursion tree, which is log n on average.

**Question 5:** What happens if the pivot is always the last element and the array is already sorted?
- A) It runs in O(n log n)
- B) It runs in O(n²)
- C) It throws an error
- D) It becomes stable
- **Correct Answer:** B
- **Explanation:** Each partition creates one empty subarray and one subarray of size n-1, leading to quadratic time.

**Question 6:** Which pivot strategy helps avoid worst-case behavior?
- A) Always first element
- B) Random element
- C) Always last element
- D) Always middle element
- **Correct Answer:** B
- **Explanation:** Random pivot selection makes worst-case inputs statistically improbable.

**Question 7:** Quick Sort is an example of:
- A) Greedy algorithm
- B) Divide and Conquer
- C) Dynamic Programming
- D) Backtracking
- **Correct Answer:** B
- **Explanation:** It divides the array, sorts subarrays recursively, and combines by virtue of the partition.

**Question 8:** The partition step places the pivot:
- A) At the beginning
- B) At the end
- C) In its final sorted position
- D) In the middle
- **Correct Answer:** C
- **Explanation:** After partitioning, all smaller elements are to the left and all larger to the right, so the pivot is correctly placed.

**Question 9:** Which algorithm is used in C++ std::sort?
- A) Pure Quick Sort
- B) Introsort (Quick Sort + Heap Sort + Insertion Sort)
- C) Merge Sort
- D) Heap Sort
- **Correct Answer:** B
- **Explanation:** Introsort starts with Quick Sort but switches to Heap Sort if recursion depth exceeds a threshold.

**Question 10:** What is a key advantage of Quick Sort over Merge Sort?
- A) It is stable
- B) It sorts in-place with less memory
- C) It has guaranteed O(n log n) worst case
- D) It works better on linked lists
- **Correct Answer:** B
- **Explanation:** Quick Sort requires only O(log n) stack space, while Merge Sort needs O(n) auxiliary memory.
