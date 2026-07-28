# Heap Sort

## 1. Introduction

Heap Sort is a comparison-based sorting algorithm that uses a binary heap data structure. It works by first building a max heap from the input array, then repeatedly extracting the maximum element from the heap and placing it at the end of the array. Imagine a tournament where players are arranged in a bracket. The winner (maximum) bubbles to the top. You remove the winner, promote the next best, and repeat until everyone is ranked. That is Heap Sort in essence.

It was invented by J. W. J. Williams in 1964 as a way to sort data using the heap structure, which was itself invented for this purpose. Heap Sort combines the advantages of Merge Sort (guaranteed O(n log n)) with the space efficiency of Quick Sort (in-place sorting).

You should use Heap Sort when you need guaranteed O(n log n) performance with O(1) extra space, or when sorting data in memory-constrained environments where recursion stack space is limited.

## 2. Why Use This Algorithm?

Heap Sort offers the best of both worlds: guaranteed logarithmic performance without extra memory.

**Benefits:**
- Guaranteed O(n log n) time in all cases — best, average, and worst
- In-place sorting with O(1) auxiliary space
- No recursion required (can be implemented iteratively)
- Not sensitive to input distribution — no worst-case pathological inputs
- Excellent for memory-constrained embedded systems

**Performance:**
Heap Sort consistently performs at O(n log n) regardless of whether the input is sorted, reverse-sorted, or random. This predictability makes it ideal for real-time systems where worst-case guarantees matter.

**When it is better than other algorithms:**
Heap Sort beats Quick Sort when worst-case guarantees are required and recursion depth must be bounded. It beats Merge Sort when memory is scarce because it does not need auxiliary arrays. It is the algorithm of choice for in-place sorting with guaranteed performance.

## 3. Real-World Applications

- **Embedded systems with limited RAM:** Heap Sort sorts without allocating extra memory.
- **Real-time systems:** Guaranteed O(n log n) prevents timing violations.
- **Priority queue implementations:** The heap structure is fundamental to priority queues used in task schedulers.
- **Finding the k largest elements:** A min-heap of size k can find top-k elements in O(n log k).
- **External sorting:** Used in merge phases where memory is constrained.
- **Operating system process scheduling:** Priority schedulers often use heap-based structures.
- **Dijkstra's and Prim's algorithms:** Both rely on priority queues backed by heaps.

## 4. Prerequisites

Before learning Heap Sort, you should know:
- Binary trees and complete binary trees
- Array representation of binary heaps (parent at i, children at 2i+1 and 2i+2)
- The heap property (max heap: parent >= children)
- The heapify operation (sifting down)
- Basic loop constructs and array indexing

## 5. Visualization

Imagine a complete binary tree drawn vertically, where each parent node is larger than its children (max heap). The largest element sits at the very top (root). You pluck the root and swap it with the last leaf at the bottom right. Now the tree is slightly broken — the new root might be smaller than its children. You "sift" it down by repeatedly swapping it with its larger child until the heap property is restored. The plucked element is now in its final position at the end of the array. The tree shrinks by one, and you repeat. Slowly, the sorted portion grows at the end of the array like a tail while the heap shrinks at the front.

## 6. How It Works

Heap Sort has two phases:
1. **Build Max Heap:** Rearrange the array so it satisfies the max heap property (every parent is greater than or equal to its children). This is done by calling heapify on all non-leaf nodes from the bottom up.
2. **Extract and Sort:** Repeatedly swap the root (maximum element) with the last element of the heap, reduce the heap size by one, and heapify the root to restore the heap property. The extracted element is now in its final sorted position.

## 7. Step-by-Step Algorithm

1. Build a max heap from the input array:
   1. Start from the last non-leaf node (index n/2 - 1) down to the root.
   2. For each node, call heapify to ensure it and its descendants satisfy the heap property.
2. Extract elements one by one:
   1. Swap the root (index 0) with the last element of the current heap.
   2. Reduce the heap size by 1.
   3. Call heapify on the root to restore the heap property.
   4. The swapped element is now in its final sorted position.
3. Repeat step 2 until the heap size is 1.

**Heapify procedure:**
1. Compare the node with its left and right children.
2. If either child is larger, swap the node with the largest child.
3. Recursively heapify the affected subtree.

## 8. Pseudocode

```
function heapSort(array):
    n = length(array)
    // Build max heap
    for i from n/2 - 1 down to 0:
        heapify(array, n, i)
    // Extract elements
    for i from n - 1 down to 1:
        swap array[0] and array[i]
        heapify(array, i, 0)

function heapify(array, heapSize, rootIndex):
    largest = rootIndex
    left = 2 * rootIndex + 1
    right = 2 * rootIndex + 2
    if left < heapSize and array[left] > array[largest]:
        largest = left
    if right < heapSize and array[right] > array[largest]:
        largest = right
    if largest != rootIndex:
        swap array[rootIndex] and array[largest]
        heapify(array, heapSize, largest)
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

void heapify(int arr[], int heapSize, int rootIndex) {
    int largest = rootIndex;
    int left = 2 * rootIndex + 1;
    int right = 2 * rootIndex + 2;
    if (left < heapSize && arr[left] > arr[largest])
        largest = left;
    if (right < heapSize && arr[right] > arr[largest])
        largest = right;
    if (largest != rootIndex) {
        swap(&arr[rootIndex], &arr[largest]);
        heapify(arr, heapSize, largest);
    }
}

void heapSort(int arr[], int n) {
    for (int i = n / 2 - 1; i >= 0; i--)
        heapify(arr, n, i);
    for (int i = n - 1; i > 0; i--) {
        swap(&arr[0], &arr[i]);
        heapify(arr, i, 0);
    }
}

int main() {
    int arr[] = {12, 11, 13, 5, 6, 7};
    int n = sizeof(arr) / sizeof(arr[0]);
    heapSort(arr, n);
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

void heapify(vector<int>& arr, int heapSize, int rootIndex) {
    int largest = rootIndex;
    int left = 2 * rootIndex + 1;
    int right = 2 * rootIndex + 2;
    if (left < heapSize && arr[left] > arr[largest])
        largest = left;
    if (right < heapSize && arr[right] > arr[largest])
        largest = right;
    if (largest != rootIndex) {
        swap(arr[rootIndex], arr[largest]);
        heapify(arr, heapSize, largest);
    }
}

void heapSort(vector<int>& arr) {
    int n = arr.size();
    for (int i = n / 2 - 1; i >= 0; i--)
        heapify(arr, n, i);
    for (int i = n - 1; i > 0; i--) {
        swap(arr[0], arr[i]);
        heapify(arr, i, 0);
    }
}

int main() {
    vector<int> arr = {12, 11, 13, 5, 6, 7};
    heapSort(arr);
    cout << "Sorted array: ";
    for (int x : arr) cout << x << " ";
    cout << endl;
    return 0;
}
```

### Java
```java
public class HeapSort {
    static void heapify(int[] arr, int heapSize, int rootIndex) {
        int largest = rootIndex;
        int left = 2 * rootIndex + 1;
        int right = 2 * rootIndex + 2;
        if (left < heapSize && arr[left] > arr[largest])
            largest = left;
        if (right < heapSize && arr[right] > arr[largest])
            largest = right;
        if (largest != rootIndex) {
            int temp = arr[rootIndex];
            arr[rootIndex] = arr[largest];
            arr[largest] = temp;
            heapify(arr, heapSize, largest);
        }
    }

    static void heapSort(int[] arr) {
        int n = arr.length;
        for (int i = n / 2 - 1; i >= 0; i--)
            heapify(arr, n, i);
        for (int i = n - 1; i > 0; i--) {
            int temp = arr[0];
            arr[0] = arr[i];
            arr[i] = temp;
            heapify(arr, i, 0);
        }
    }

    public static void main(String[] args) {
        int[] arr = {12, 11, 13, 5, 6, 7};
        heapSort(arr);
        System.out.print("Sorted array: ");
        for (int x : arr) System.out.print(x + " ");
        System.out.println();
    }
}
```

### Python
```python
def heapify(arr, heap_size, root_index):
    largest = root_index
    left = 2 * root_index + 1
    right = 2 * root_index + 2
    if left < heap_size and arr[left] > arr[largest]:
        largest = left
    if right < heap_size and arr[right] > arr[largest]:
        largest = right
    if largest != root_index:
        arr[root_index], arr[largest] = arr[largest], arr[root_index]
        heapify(arr, heap_size, largest)

def heap_sort(arr):
    n = len(arr)
    for i in range(n // 2 - 1, -1, -1):
        heapify(arr, n, i)
    for i in range(n - 1, 0, -1):
        arr[0], arr[i] = arr[i], arr[0]
        heapify(arr, i, 0)

arr = [12, 11, 13, 5, 6, 7]
heap_sort(arr)
print("Sorted array:", arr)
```

### JavaScript
```javascript
function heapify(arr, heapSize, rootIndex) {
    let largest = rootIndex;
    const left = 2 * rootIndex + 1;
    const right = 2 * rootIndex + 2;
    if (left < heapSize && arr[left] > arr[largest])
        largest = left;
    if (right < heapSize && arr[right] > arr[largest])
        largest = right;
    if (largest !== rootIndex) {
        [arr[rootIndex], arr[largest]] = [arr[largest], arr[rootIndex]];
        heapify(arr, heapSize, largest);
    }
}

function heapSort(arr) {
    const n = arr.length;
    for (let i = Math.floor(n / 2) - 1; i >= 0; i--)
        heapify(arr, n, i);
    for (let i = n - 1; i > 0; i--) {
        [arr[0], arr[i]] = [arr[i], arr[0]];
        heapify(arr, i, 0);
    }
}

const arr = [12, 11, 13, 5, 6, 7];
heapSort(arr);
console.log("Sorted array:", arr.join(" "));
```

## 10. Code Explanation

The `heapify` function is the core operation. It assumes the subtrees of the given root are already valid heaps, and it fixes the heap property at the root by sifting the root down to its correct position. The `heapSort` function first builds the max heap by calling `heapify` on all non-leaf nodes from bottom to top. This takes O(n) time (not O(n log n) as one might expect). Then it enters the extraction phase: swap root with last element, reduce heap size, and heapify the root. Each extraction takes O(log n), and there are n extractions, giving O(n log n) total. The sorted portion grows from the end of the array backward.

## 11. Interactive Demo

The demo shows the array as both a horizontal bar chart and a binary tree visualization. In phase 1 (Build Heap), non-leaf nodes are highlighted in yellow as heapify is called on them. Nodes that need swapping flash red and swap positions with an animated transition. The tree gradually transforms into a max heap, with the root becoming the largest element (highlighted in green). In phase 2 (Extract), the root swaps with the last element (animation shows the root sliding to the end). The extracted element turns blue and is locked in place. The new root is heapified down, with comparisons and swaps animated. A status panel shows the current heap size and which element was just placed.

## 12. Dry Run

**Sample Input:**
Array: `[12, 11, 13, 5, 6, 7]`

**Build Heap Phase:**

| i | Node | Action | Array After |
|---|------|--------|-------------|
| 2 | 13 | Already heap | `[12, 11, 13, 5, 6, 7]` |
| 1 | 11 | 11 > 5, 11 > 6. No swap | `[12, 11, 13, 5, 6, 7]` |
| 0 | 12 | 12 < 13 (right child). Swap 12 and 13. Then heapify index 2: 12 has no children. | `[13, 11, 12, 5, 6, 7]` |

**Extract Phase:**

| Step | Swap | Heapify Root | Array State |
|------|------|--------------|-------------|
| 1 | 13 <-> 7 | 7 sifted down: 7<11, 7<12. Swap with 12. | `[12, 11, 7, 5, 6, 13]` |
| 2 | 12 <-> 6 | 6 sifted down: 6<11, 6<7. Swap with 11. | `[11, 6, 7, 5, 12, 13]` |
| 3 | 11 <-> 5 | 5 sifted down: 5<6, 5<7. Swap with 7. | `[7, 6, 5, 11, 12, 13]` |
| 4 | 7 <-> 5 | 5 sifted down: 5<6. Swap with 6. | `[6, 5, 7, 11, 12, 13]` |
| 5 | 6 <-> 5 | Heap size = 1. Done. | `[5, 6, 7, 11, 12, 13]` |

**Final Output:** `[5, 6, 7, 11, 12, 13]`

## 13. Time & Space Complexity

| Case | Time Complexity | Reason |
|------|-----------------|--------|
| Best Case | O(n log n) | Build heap is O(n), n extractions at O(log n) each |
| Average Case | O(n log n) | Same structure regardless of input order |
| Worst Case | O(n log n) | Guaranteed by the heap structure; no pathological inputs |
| Space Complexity | O(1) | Sorts in-place; only recursion stack for heapify (can be made iterative) |

## 14. Advantages

- **Guaranteed O(n log n):** No worst-case degradation like Quick Sort.
- **In-place:** Uses only O(1) extra memory.
- **No recursion required:** Can be implemented entirely with loops.
- **Not sensitive to input:** Performance is identical for sorted, reverse-sorted, and random data.
- **Useful heap structure:** The heap itself is valuable for priority queues and selection algorithms.

## 15. Disadvantages

- **Not stable:** The swap-and-sift process changes the relative order of equal elements.
- **Poor cache performance:** Jumping around the tree (parent to child indices) causes cache misses.
- **Slower than Quick Sort in practice:** Cache inefficiency makes it 2-3x slower than Quick Sort for in-memory arrays.
- **Not adaptive:** Does not take advantage of nearly sorted input.

## 16. Applications

- Embedded systems with severe memory constraints
- Real-time systems requiring guaranteed performance bounds
- Priority queue implementations (task scheduling, event simulation)
- Finding k largest/smallest elements efficiently
- External sorting where auxiliary memory is limited
- Operating system process schedulers
- Graph algorithms (Dijkstra, Prim) that use priority queues

## 17. Common Mistakes

- **Forgetting to reduce heap size during extraction:** The heapify call must use the current heap size, not the original array size.
- **Wrong child index formulas:** Left child is `2*i + 1`, right child is `2*i + 2` (0-indexed arrays).
- **Building heap from top to bottom:** Heapify must be called from the last non-leaf node upward.
- **Confusing max heap and min heap:** For ascending sort, use a max heap. For descending sort, use a min heap.
- **Off-by-one in heap size:** When swapping root with last element, heapify must not include the already-sorted tail.

## 18. Interview Questions

1. What is the time complexity of Heap Sort in all cases?
2. Why does Heap Sort use O(1) extra space while Merge Sort uses O(n)?
3. Is Heap Sort stable? Why or why not?
4. What is the heap property for a max heap?
5. How do you find the parent, left child, and right child of a node at index i?
6. What is the difference between Build Heap (O(n)) and n individual insertions (O(n log n))?
7. Can Heap Sort be implemented without recursion?
8. How would you use a heap to find the k-th largest element?
9. Compare Heap Sort and Quick Sort for in-memory sorting.
10. What is a priority queue, and how is it related to heaps?

## 19. Practice Problems

**Easy:**
1. Implement Heap Sort on an array of integers.
2. Implement the heapify function for a max heap.
3. Sort an array in descending order using Heap Sort.
4. Build a max heap from an arbitrary array.

**Medium:**
5. Implement Heap Sort iteratively without recursion in heapify.
6. Find the k-th smallest element using a max heap.
7. Sort a nearly sorted array (each element is at most k away from its position) using a heap.
8. Implement a min heap and use it for Heap Sort in descending order.

**Hard:**
9. Implement an in-place heap sort that is stable (advanced and inefficient, but educational).
10. Design a parallel Heap Sort algorithm.
11. Implement a d-ary heap sort where each node has d children instead of 2.

## 20. Related Algorithms

- Quick Sort (faster in practice but O(n²) worst case)
- Merge Sort (stable and guaranteed O(n log n) but needs O(n) space)
- Binary Heap (the data structure underlying Heap Sort)
- Priority Queue (abstract data type implemented with heaps)
- Tree Sort (uses BST instead of heap)

## 21. Summary

Heap Sort is the algorithm of choice when you need guaranteed O(n log n) performance with minimal memory usage. By leveraging the binary heap data structure, it sorts in-place without recursion and without pathological worst cases. While its cache performance is inferior to Quick Sort, its predictability and space efficiency make it invaluable for embedded systems, real-time applications, and as the foundation for priority queues. Master Heap Sort, and you master both a sorting algorithm and one of the most important data structures in computer science.

## 22. Quiz

**Question 1:** What is the time complexity of Heap Sort in all cases?
- A) O(n²)
- B) O(n log n)
- C) O(n)
- D) O(log n)
- **Correct Answer:** B
- **Explanation:** Building the heap is O(n), and n extractions each take O(log n), giving O(n log n) total.

**Question 2:** What is the space complexity of Heap Sort?
- A) O(n)
- B) O(log n)
- C) O(1)
- D) O(n²)
- **Correct Answer:** C
- **Explanation:** It sorts entirely in-place with only a few variables for indexing and swapping.

**Question 3:** Is Heap Sort stable?
- A) Yes
- B) No
- C) Only for integers
- D) Only for small arrays
- **Correct Answer:** B
- **Explanation:** The distant swapping of elements during heapify can change the relative order of equal elements.

**Question 4:** In a max heap, what is the relationship between a parent and its children?
- A) Parent <= children
- B) Parent >= children
- C) Parent = children
- D) No relationship
- **Correct Answer:** B
- **Explanation:** The max heap property requires every parent node to be greater than or equal to its children.

**Question 5:** What is the index of the left child of a node at index i (0-indexed)?
- A) 2*i
- B) 2*i + 1
- C) 2*i + 2
- D) i/2
- **Correct Answer:** B
- **Explanation:** For 0-indexed arrays, left child = 2*i + 1 and right child = 2*i + 2.

**Question 6:** Why is Heap Sort slower than Quick Sort in practice?
- A) It has worse time complexity
- B) Poor cache locality due to jumping between parent and child indices
- C) It uses more memory
- D) It is recursive
- **Correct Answer:** B
- **Explanation:** The tree-like access pattern causes cache misses compared to Quick Sort's sequential partitioning.

**Question 7:** What is the first phase of Heap Sort?
- A) Extract elements
- B) Build max heap
- C) Quick partition
- D) Binary search
- **Correct Answer:** B
- **Explanation:** The array must first be rearranged to satisfy the max heap property.

**Question 8:** What happens during the extraction phase?
- A) The smallest element is removed
- B) The root is swapped with the last element and heap size is reduced
- C) The array is split in half
- D) A new heap is built
- **Correct Answer:** B
- **Explanation:** The maximum (root) is placed at the end, and the heap is restored on the remaining elements.

**Question 9:** Heap Sort is particularly useful when:
- A) Stability is required
- B) Memory is limited and guaranteed performance is needed
- C) Data is nearly sorted
- D) Only integers need sorting
- **Correct Answer:** B
- **Explanation:** Its O(1) space and guaranteed O(n log n) make it ideal for memory-constrained systems.

**Question 10:** Which data structure is fundamental to Heap Sort?
- A) Binary Search Tree
- B) Binary Heap
- C) Linked List
- D) Hash Table
- **Correct Answer:** B
- **Explanation:** Heap Sort is named after and entirely dependent on the binary heap data structure.
