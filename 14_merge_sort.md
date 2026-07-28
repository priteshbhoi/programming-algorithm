# Merge Sort

## 1. Introduction

Merge Sort is a classic divide-and-conquer sorting algorithm that breaks down a problem into smaller subproblems, solves each one, and then combines the results. Imagine you have a deck of cards scattered on a table. Instead of trying to sort the entire deck at once, you split it into two piles, sort each pile separately, and then merge the two sorted piles back together by repeatedly picking the smaller card from the top of either pile. That is the essence of Merge Sort.

It was invented by John von Neumann in 1945, making it one of the first algorithms designed for computer implementation. Its predictable performance and stability have kept it relevant for nearly 80 years.

You should use Merge Sort when you need guaranteed O(n log n) performance, when stability matters, or when sorting data that does not fit entirely in memory.

## 2. Why Use This Algorithm?

Merge Sort offers rock-solid performance guarantees that make it indispensable for critical systems.

**Benefits:**
- Guaranteed O(n log n) time in all cases — best, average, and worst
- Stable sort — preserves the relative order of equal elements
- Naturally suited for external sorting (data too large for RAM)
- Excellent parallelization potential
- Predictable performance with no pathological inputs

**Performance:**
Unlike Quick Sort, Merge Sort never degrades to O(n²). Its consistent O(n log n) makes it the algorithm of choice for systems where worst-case performance must be bounded, such as real-time systems or financial transaction processing.

**When it is better than other algorithms:**
Merge Sort beats Quick Sort when stability is required or when worst-case guarantees matter. It is the standard algorithm for sorting linked lists and for external sorting where data resides on disk.

## 3. Real-World Applications

- **Java's Arrays.sort() for objects:** Java uses Tim Sort (a Merge Sort variant) for object arrays.
- **Python's sorted() and list.sort():** Python's default sort is Tim Sort, derived from Merge Sort.
- **External sorting:** Sorting massive datasets that exceed RAM capacity, such as database files or log archives.
- **Linked list sorting:** Merge Sort is the most efficient way to sort linked lists because it does not require random access.
- **Inversion counting:** Merge Sort can be modified to count inversions in an array efficiently.
- **Merge operations in databases:** Combining sorted query results from multiple sources.

## 4. Prerequisites

Before learning Merge Sort, you should know:
- Recursion and the call stack
- Arrays and indexing
- How to merge two sorted arrays
- Basic understanding of time complexity and the Master Theorem
- Memory allocation concepts (for understanding the auxiliary space)

## 5. Visualization

Imagine two sorted stacks of numbered cards on a table. You have an empty tray in front of you. You look at the top card of each stack and place the smaller one into the tray. You repeat this until one stack is empty, then you dump the remaining cards from the other stack into the tray. Now the tray contains a perfectly sorted merged stack. Merge Sort applies this process recursively: it splits a large unsorted stack into single cards (which are trivially sorted), then repeatedly merges pairs of stacks until only one sorted stack remains.

## 6. How It Works

Merge Sort follows three steps recursively:
1. **Divide:** Split the array into two halves.
2. **Conquer:** Recursively sort each half.
3. **Combine:** Merge the two sorted halves into a single sorted array.

The base case occurs when the subarray has zero or one element, which is already sorted. The merge step is where the real work happens: two sorted subarrays are combined by comparing their elements one by one and building the result.

## 7. Step-by-Step Algorithm

1. If the array has 0 or 1 elements, it is already sorted. Return.
2. Find the middle index of the array.
3. Recursively sort the left half (from start to middle).
4. Recursively sort the right half (from middle+1 to end).
5. Merge the two sorted halves:
   1. Create a temporary array to hold the merged result.
   2. Compare the first unmerged element of each half.
   3. Copy the smaller element to the temporary array.
   4. Repeat until one half is exhausted.
   5. Copy any remaining elements from the non-empty half.
   6. Copy the temporary array back to the original array.

## 8. Pseudocode

```
function mergeSort(array, left, right):
    if left < right:
        mid = left + (right - left) / 2
        mergeSort(array, left, mid)
        mergeSort(array, mid + 1, right)
        merge(array, left, mid, right)

function merge(array, left, mid, right):
    n1 = mid - left + 1
    n2 = right - mid
    create temporary arrays L[0..n1-1] and R[0..n2-1]
    copy array[left..mid] into L
    copy array[mid+1..right] into R
    i = 0, j = 0, k = left
    while i < n1 and j < n2:
        if L[i] <= R[j]:
            array[k] = L[i]
            i = i + 1
        else:
            array[k] = R[j]
            j = j + 1
        k = k + 1
    while i < n1:
        array[k] = L[i]
        i = i + 1
        k = k + 1
    while j < n2:
        array[k] = R[j]
        j = j + 1
        k = k + 1
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>

void merge(int arr[], int left, int mid, int right) {
    int n1 = mid - left + 1;
    int n2 = right - mid;
    int *L = (int *)malloc(n1 * sizeof(int));
    int *R = (int *)malloc(n2 * sizeof(int));
    for (int i = 0; i < n1; i++) L[i] = arr[left + i];
    for (int j = 0; j < n2; j++) R[j] = arr[mid + 1 + j];
    int i = 0, j = 0, k = left;
    while (i < n1 && j < n2) {
        if (L[i] <= R[j]) arr[k++] = L[i++];
        else arr[k++] = R[j++];
    }
    while (i < n1) arr[k++] = L[i++];
    while (j < n2) arr[k++] = R[j++];
    free(L); free(R);
}

void mergeSort(int arr[], int left, int right) {
    if (left < right) {
        int mid = left + (right - left) / 2;
        mergeSort(arr, left, mid);
        mergeSort(arr, mid + 1, right);
        merge(arr, left, mid, right);
    }
}

int main() {
    int arr[] = {12, 11, 13, 5, 6, 7};
    int n = sizeof(arr) / sizeof(arr[0]);
    mergeSort(arr, 0, n - 1);
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

void merge(vector<int>& arr, int left, int mid, int right) {
    int n1 = mid - left + 1;
    int n2 = right - mid;
    vector<int> L(n1), R(n2);
    for (int i = 0; i < n1; i++) L[i] = arr[left + i];
    for (int j = 0; j < n2; j++) R[j] = arr[mid + 1 + j];
    int i = 0, j = 0, k = left;
    while (i < n1 && j < n2) {
        if (L[i] <= R[j]) arr[k++] = L[i++];
        else arr[k++] = R[j++];
    }
    while (i < n1) arr[k++] = L[i++];
    while (j < n2) arr[k++] = R[j++];
}

void mergeSort(vector<int>& arr, int left, int right) {
    if (left < right) {
        int mid = left + (right - left) / 2;
        mergeSort(arr, left, mid);
        mergeSort(arr, mid + 1, right);
        merge(arr, left, mid, right);
    }
}

int main() {
    vector<int> arr = {12, 11, 13, 5, 6, 7};
    mergeSort(arr, 0, arr.size() - 1);
    cout << "Sorted array: ";
    for (int x : arr) cout << x << " ";
    cout << endl;
    return 0;
}
```

### Java
```java
public class MergeSort {
    static void merge(int[] arr, int left, int mid, int right) {
        int n1 = mid - left + 1;
        int n2 = right - mid;
        int[] L = new int[n1];
        int[] R = new int[n2];
        for (int i = 0; i < n1; i++) L[i] = arr[left + i];
        for (int j = 0; j < n2; j++) R[j] = arr[mid + 1 + j];
        int i = 0, j = 0, k = left;
        while (i < n1 && j < n2) {
            if (L[i] <= R[j]) arr[k++] = L[i++];
            else arr[k++] = R[j++];
        }
        while (i < n1) arr[k++] = L[i++];
        while (j < n2) arr[k++] = R[j++];
    }

    static void mergeSort(int[] arr, int left, int right) {
        if (left < right) {
            int mid = left + (right - left) / 2;
            mergeSort(arr, left, mid);
            mergeSort(arr, mid + 1, right);
            merge(arr, left, mid, right);
        }
    }

    public static void main(String[] args) {
        int[] arr = {12, 11, 13, 5, 6, 7};
        mergeSort(arr, 0, arr.length - 1);
        System.out.print("Sorted array: ");
        for (int x : arr) System.out.print(x + " ");
        System.out.println();
    }
}
```

### Python
```python
def merge(arr, left, mid, right):
    L = arr[left:mid+1]
    R = arr[mid+1:right+1]
    i = j = 0
    k = left
    while i < len(L) and j < len(R):
        if L[i] <= R[j]:
            arr[k] = L[i]
            i += 1
        else:
            arr[k] = R[j]
            j += 1
        k += 1
    while i < len(L):
        arr[k] = L[i]
        i += 1
        k += 1
    while j < len(R):
        arr[k] = R[j]
        j += 1
        k += 1

def merge_sort(arr, left, right):
    if left < right:
        mid = left + (right - left) // 2
        merge_sort(arr, left, mid)
        merge_sort(arr, mid + 1, right)
        merge(arr, left, mid, right)

arr = [12, 11, 13, 5, 6, 7]
merge_sort(arr, 0, len(arr) - 1)
print("Sorted array:", arr)
```

### JavaScript
```javascript
function merge(arr, left, mid, right) {
    const n1 = mid - left + 1;
    const n2 = right - mid;
    const L = new Array(n1);
    const R = new Array(n2);
    for (let i = 0; i < n1; i++) L[i] = arr[left + i];
    for (let j = 0; j < n2; j++) R[j] = arr[mid + 1 + j];
    let i = 0, j = 0, k = left;
    while (i < n1 && j < n2) {
        if (L[i] <= R[j]) arr[k++] = L[i++];
        else arr[k++] = R[j++];
    }
    while (i < n1) arr[k++] = L[i++];
    while (j < n2) arr[k++] = R[j++];
}

function mergeSort(arr, left, right) {
    if (left < right) {
        const mid = left + Math.floor((right - left) / 2);
        mergeSort(arr, left, mid);
        mergeSort(arr, mid + 1, right);
        merge(arr, left, mid, right);
    }
}

const arr = [12, 11, 13, 5, 6, 7];
mergeSort(arr, 0, arr.length - 1);
console.log("Sorted array:", arr.join(" "));
```

## 10. Code Explanation

The `mergeSort` function recursively splits the array until subarrays of size 1 are reached. The `merge` function does the heavy lifting: it creates two temporary arrays to hold the left and right halves, then walks through both simultaneously, always picking the smaller element. The `<=` comparison (rather than `<`) ensures stability — when elements are equal, the left one is chosen first, preserving original order. After one temporary array is exhausted, the remaining elements from the other are copied over. The temporary arrays are then discarded. This divide-and-conquer approach guarantees that each level of recursion processes all n elements, and there are log n levels.

## 11. Interactive Demo

The demo shows a row of bars. When "Sort" is clicked, the array visually splits into two halves with a gap between them. Each half recursively splits until single bars remain (colored gray). Then pairs begin merging: two bars slide together, compare heights, and reorder into a sorted mini-group (turning blue). These mini-groups merge into larger sorted groups, with bars animating into their correct positions within the merged section. A recursion tree diagram on the side highlights which subarray is currently being processed. The user can step through recursively or watch the full animation.

## 12. Dry Run

**Sample Input:**
Array: `[12, 11, 13, 5, 6, 7]`

| Call | left | right | mid | Action |
|------|------|-------|-----|--------|
| mergeSort | 0 | 5 | 2 | Split into [0..2] and [3..5] |
| mergeSort | 0 | 2 | 1 | Split into [0..1] and [2..2] |
| mergeSort | 0 | 1 | 0 | Split into [0..0] and [1..1] |
| mergeSort | 0 | 0 | - | Base case |
| mergeSort | 1 | 1 | - | Base case |
| merge | 0 | 0 | 1 | Merge [12] and [11] -> [11, 12] |
| mergeSort | 2 | 2 | - | Base case |
| merge | 0 | 1 | 2 | Merge [11, 12] and [13] -> [11, 12, 13] |
| mergeSort | 3 | 5 | 4 | Split into [3..4] and [5..5] |
| mergeSort | 3 | 4 | 3 | Split into [3..3] and [4..4] |
| merge | 3 | 3 | 4 | Merge [5] and [6] -> [5, 6] |
| mergeSort | 5 | 5 | - | Base case |
| merge | 3 | 4 | 5 | Merge [5, 6] and [7] -> [5, 6, 7] |
| merge | 0 | 2 | 5 | Merge [11, 12, 13] and [5, 6, 7] -> [5, 6, 7, 11, 12, 13] |

**Final Output:** `[5, 6, 7, 11, 12, 13]`

## 13. Time & Space Complexity

| Case | Time Complexity | Reason |
|------|-----------------|--------|
| Best Case | O(n log n) | Still divides and merges regardless of input order |
| Average Case | O(n log n) | Consistent divide-and-conquer structure |
| Worst Case | O(n log n) | No pathological input; always splits evenly |
| Space Complexity | O(n) | Temporary arrays of total size n are used during merging |

## 14. Advantages

- **Guaranteed O(n log n):** No worst-case degradation like Quick Sort.
- **Stable:** Equal elements keep their original relative order.
- **Predictable:** Performance does not depend on input distribution.
- **External sorting:** Naturally handles data too large for memory.
- **Parallelizable:** Left and right halves can be sorted independently.
- **Linked list efficient:** Does not require random access; excellent for linked lists.

## 15. Disadvantages

- **Extra memory:** O(n) auxiliary space is required for the merge step.
- **Slower than Quick Sort in practice:** Cache performance and constant factors make Quick Sort faster for in-memory arrays.
- **Not in-place:** The standard implementation requires additional arrays.
- **Recursive overhead:** Deep recursion for large arrays can cause stack issues.

## 16. Applications

- Sorting linked lists (most efficient comparison-based approach)
- External sorting of massive datasets (database files, log processing)
- Inversion count problems (counting how "out of order" an array is)
- Stable sorting requirements in financial and medical systems
- As the foundation for Tim Sort (used in Python and Java)
- Parallel sorting on multi-core processors

## 17. Common Mistakes

- **Off-by-one in merge boundaries:** `mid - left + 1` gives the correct size for the left subarray.
- **Forgetting to copy remaining elements:** After one subarray is exhausted, the other may still have elements.
- **Using `<` instead of `<=` in comparison:** This breaks stability.
- **Stack overflow on huge arrays:** Iterative bottom-up Merge Sort can avoid deep recursion.
- **Memory leaks:** In C/C++, remember to free temporary arrays.

## 18. Interview Questions

1. What is the time complexity of Merge Sort in all cases?
2. Why does Merge Sort require O(n) extra space?
3. Is Merge Sort stable? How is stability maintained during the merge step?
4. Compare Merge Sort and Quick Sort. When would you choose each?
5. How would you sort a linked list using Merge Sort?
6. What is the Master Theorem, and how does it apply to Merge Sort?
7. Can Merge Sort be implemented iteratively? Describe bottom-up Merge Sort.
8. How can Merge Sort be modified to count inversions in an array?
9. Why is Merge Sort preferred over Quick Sort for external sorting?
10. How would you parallelize Merge Sort?

## 19. Practice Problems

**Easy:**
1. Implement recursive Merge Sort on an array of integers.
2. Implement the merge function that combines two sorted arrays.
3. Sort an array of strings using Merge Sort.
4. Trace through Merge Sort manually for a small array.

**Medium:**
5. Implement bottom-up (iterative) Merge Sort without recursion.
6. Count the number of inversions in an array using a modified Merge Sort.
7. Sort a linked list using Merge Sort.
8. Implement a stable Merge Sort that minimizes temporary array allocations.

**Hard:**
9. Implement external Merge Sort that sorts a file larger than available RAM.
10. Design a parallel Merge Sort using multiple threads or processes.
11. Implement an in-place Merge Sort (advanced, O(1) extra space).

## 20. Related Algorithms

- Quick Sort (faster average case but unstable and O(n²) worst case)
- Heap Sort (O(n log n) in-place but unstable)
- Tim Sort (hybrid of Merge Sort and Insertion Sort, used in Python/Java)
- Counting Sort (non-comparison based, O(n+k) for integer ranges)
- Binary Search (uses similar divide-and-conquer philosophy)

## 21. Summary

Merge Sort is the gold standard for stable, predictable sorting. By recursively dividing the array in half and merging the sorted results, it guarantees O(n log n) performance regardless of input order. Its stability and natural suitability for linked lists and external sorting make it indispensable in systems where consistency matters. The trade-off is O(n) extra memory, which is why hybrid algorithms like Tim Sort combine Merge Sort's reliability with Insertion Sort's low overhead for small subarrays. Master Merge Sort, and you master the divide-and-conquer paradigm itself.

## 22. Quiz

**Question 1:** What is the time complexity of Merge Sort in all cases?
- A) O(n²)
- B) O(n log n)
- C) O(n)
- D) O(log n)
- **Correct Answer:** B
- **Explanation:** The array is always divided into halves (log n levels) and each level processes all n elements.

**Question 2:** Is Merge Sort stable?
- A) No
- B) Yes
- C) Only for integers
- D) Only for small arrays
- **Correct Answer:** B
- **Explanation:** Using `<=` in the merge comparison preserves the order of equal elements.

**Question 3:** What is the space complexity of standard Merge Sort?
- A) O(1)
- B) O(log n)
- C) O(n)
- D) O(n²)
- **Correct Answer:** C
- **Explanation:** Temporary arrays totaling n elements are needed during the merge step.

**Question 4:** Merge Sort is based on which paradigm?
- A) Greedy
- B) Dynamic Programming
- C) Divide and Conquer
- D) Backtracking
- **Correct Answer:** C
- **Explanation:** It divides the problem, solves subproblems recursively, and combines results.

**Question 5:** What is the main disadvantage compared to Quick Sort?
- A) It is unstable
- B) It requires O(n) extra space
- C) It has O(n²) worst case
- D) It cannot sort integers
- **Correct Answer:** B
- **Explanation:** Quick Sort sorts in-place with O(log n) stack space, while Merge Sort needs O(n) auxiliary memory.

**Question 6:** In the merge step, what happens when one subarray is exhausted?
- A) The algorithm stops
- B) Remaining elements from the other subarray are copied
- C) An error occurs
- D) The array is reversed
- **Correct Answer:** B
- **Explanation:** The leftover elements are already sorted and can be copied directly.

**Question 7:** Merge Sort is particularly efficient for:
- A) Arrays with many duplicates
- B) Linked lists
- C) Reverse-sorted arrays
- D) Random access databases
- **Correct Answer:** B
- **Explanation:** It does not require random access, making it ideal for sequential-access linked lists.

**Question 8:** What ensures stability during merging?
- A) Using `<` instead of `<=`
- B) Using `<=` instead of `<`
- C) Sorting in descending order
- D) Using a hash table
- **Correct Answer:** B
- **Explanation:** `<=` ensures that when elements are equal, the one from the left subarray (which appeared first) is chosen.

**Question 9:** How many levels of recursion does Merge Sort have for n elements?
- A) n
- B) log n
- C) n/2
- D) 2^n
- **Correct Answer:** B
- **Explanation:** The array is halved at each level until reaching size 1.

**Question 10:** Which sorting algorithm is derived from Merge Sort and used in Python?
- A) Quick Sort
- B) Heap Sort
- C) Tim Sort
- D) Shell Sort
- **Correct Answer:** C
- **Explanation:** Tim Sort is a hybrid stable sorting algorithm derived from Merge Sort and Insertion Sort.
