# Selection Sort

## 1. Introduction

Selection Sort is one of the most intuitive sorting algorithms. It works by dividing the array into a sorted portion and an unsorted portion. In each iteration, it finds the smallest element in the unsorted portion and swaps it into its correct position at the end of the sorted portion. Imagine you have a pile of mixed-up numbered cards. You repeatedly scan the entire pile, pull out the smallest card, and place it face-up in a new stack. That is Selection Sort in action.

It was created as a straightforward sorting method that minimizes the number of swaps. While it is simple to understand, it is not efficient for large datasets. However, its predictable behavior and minimal write operations make it useful in specific scenarios.

You should use Selection Sort when memory writes are expensive, when you need a simple algorithm for small datasets, or when you want to minimize the number of swaps at the cost of more comparisons.

## 2. Why Use This Algorithm?

Selection Sort shines in its simplicity and its minimal swap count.

**Benefits:**
- Very easy to understand and implement
- Performs at most n-1 swaps, regardless of input
- In-place sorting with O(1) extra space
- Predictable performance — always makes the same number of comparisons

**Performance:**
Selection Sort makes roughly n²/2 comparisons but only n swaps. This makes it preferable to Bubble Sort in environments where writing to memory is much more expensive than reading.

**When it is better than other algorithms:**
Selection Sort is better than Bubble Sort when swap operations are costly (e.g., flash memory, network writes). It is also simpler than Insertion Sort for beginners to grasp conceptually.

## 3. Real-World Applications

- **Sorting with expensive writes:** Systems where writing data costs significantly more than reading, such as flash memory or EEPROM.
- **Small embedded systems:** Microcontrollers where code simplicity and minimal RAM usage are priorities.
- **Teaching sorting fundamentals:** Its clear separation of sorted and unsorted regions makes it easy to visualize.
- **Sorting linked lists:** Selection Sort can be adapted for linked lists with minimal pointer manipulation.
- **One-off scripts:** When you need to sort a tiny array and do not want to import a library.

## 4. Prerequisites

Before learning Selection Sort, you should know:
- Arrays and indexing
- Loop constructs (nested loops)
- Conditional statements (if/else)
- Variable swapping
- Basic understanding of time complexity

## 5. Visualization

Picture a row of colored blocks of different heights. A glowing boundary separates a sorted section on the left from an unsorted section on the right. A scanner sweeps through the unsorted section looking for the shortest block. When found, that block is lifted and swapped with the first block in the unsorted section. The boundary moves one step to the right. This process repeats until the boundary reaches the end and every block is in the sorted section.

## 6. How It Works

Selection Sort maintains two subarrays: the sorted subarray at the beginning and the unsorted subarray at the end. Initially, the sorted subarray is empty and the unsorted subarray contains all elements. The algorithm repeatedly finds the minimum element from the unsorted subarray and moves it to the end of the sorted subarray by swapping. After n-1 iterations, the entire array is sorted.

## 7. Step-by-Step Algorithm

1. Set the current position to the first index (0).
2. Scan the unsorted portion from the current position to the end to find the minimum element.
3. Swap the minimum element with the element at the current position.
4. Move the current position one step to the right.
5. Repeat steps 2-4 until the current position reaches the second-to-last element.
6. The array is now sorted.

## 8. Pseudocode

```
function selectionSort(array):
    n = length(array)
    for i from 0 to n - 2:
        minIndex = i
        for j from i + 1 to n - 1:
            if array[j] < array[minIndex]:
                minIndex = j
        if minIndex != i:
            swap array[i] and array[minIndex]
```

## 9. Code Examples

### C
```c
#include <stdio.h>

void selectionSort(int arr[], int n) {
    for (int i = 0; i < n - 1; i++) {
        int minIndex = i;
        for (int j = i + 1; j < n; j++) {
            if (arr[j] < arr[minIndex]) {
                minIndex = j;
            }
        }
        if (minIndex != i) {
            int temp = arr[i];
            arr[i] = arr[minIndex];
            arr[minIndex] = temp;
        }
    }
}

void printArray(int arr[], int n) {
    for (int i = 0; i < n; i++)
        printf("%d ", arr[i]);
    printf("\n");
}

int main() {
    int arr[] = {64, 25, 12, 22, 11};
    int n = sizeof(arr) / sizeof(arr[0]);
    selectionSort(arr, n);
    printf("Sorted array: ");
    printArray(arr, n);
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
using namespace std;

void selectionSort(vector<int>& arr) {
    int n = arr.size();
    for (int i = 0; i < n - 1; i++) {
        int minIndex = i;
        for (int j = i + 1; j < n; j++) {
            if (arr[j] < arr[minIndex]) {
                minIndex = j;
            }
        }
        if (minIndex != i) {
            swap(arr[i], arr[minIndex]);
        }
    }
}

int main() {
    vector<int> arr = {64, 25, 12, 22, 11};
    selectionSort(arr);
    cout << "Sorted array: ";
    for (int x : arr)
        cout << x << " ";
    cout << endl;
    return 0;
}
```

### Java
```java
public class SelectionSort {
    public static void selectionSort(int[] arr) {
        int n = arr.length;
        for (int i = 0; i < n - 1; i++) {
            int minIndex = i;
            for (int j = i + 1; j < n; j++) {
                if (arr[j] < arr[minIndex]) {
                    minIndex = j;
                }
            }
            if (minIndex != i) {
                int temp = arr[i];
                arr[i] = arr[minIndex];
                arr[minIndex] = temp;
            }
        }
    }

    public static void main(String[] args) {
        int[] arr = {64, 25, 12, 22, 11};
        selectionSort(arr);
        System.out.print("Sorted array: ");
        for (int x : arr)
            System.out.print(x + " ");
        System.out.println();
    }
}
```

### Python
```python
def selection_sort(arr):
    n = len(arr)
    for i in range(n - 1):
        min_index = i
        for j in range(i + 1, n):
            if arr[j] < arr[min_index]:
                min_index = j
        if min_index != i:
            arr[i], arr[min_index] = arr[min_index], arr[i]

arr = [64, 25, 12, 22, 11]
selection_sort(arr)
print("Sorted array:", arr)
```

### JavaScript
```javascript
function selectionSort(arr) {
    const n = arr.length;
    for (let i = 0; i < n - 1; i++) {
        let minIndex = i;
        for (let j = i + 1; j < n; j++) {
            if (arr[j] < arr[minIndex]) {
                minIndex = j;
            }
        }
        if (minIndex !== i) {
            [arr[i], arr[minIndex]] = [arr[minIndex], arr[i]];
        }
    }
}

const arr = [64, 25, 12, 22, 11];
selectionSort(arr);
console.log("Sorted array:", arr.join(" "));
```

## 10. Code Explanation

The outer loop tracks the boundary between sorted and unsorted regions. The inner loop scans the unsorted portion to find the minimum element's index. Importantly, we only swap once per outer iteration — after the inner loop completes. This minimizes writes. The `if (minIndex != i)` check avoids unnecessary swaps when the minimum is already in place. Unlike Bubble Sort, which bubbles elements to their positions through many swaps, Selection Sort places each element exactly once per iteration.

## 11. Interactive Demo

The demo displays a row of vertical bars. A vertical line separates the sorted (left) and unsorted (right) portions. When "Sort" is clicked, the algorithm highlights the current position in green, then scans the unsorted portion in yellow to find the minimum. The minimum bar flashes red, then swaps with the green bar via animation. The green bar turns blue to indicate it is now in the sorted section. The boundary line moves right. A counter shows the number of comparisons and swaps. After completion, all bars turn blue.

## 12. Dry Run

**Sample Input:**
Array: `[64, 25, 12, 22, 11]`

| Pass | i | Unsorted Scan | minIndex | Swap | Array After |
|------|---|---------------|----------|------|-------------|
| 1 | 0 | [64,25,12,22,11] | 4 (value 11) | 64 <-> 11 | `[11, 25, 12, 22, 64]` |
| 2 | 1 | [25,12,22,64] | 2 (value 12) | 25 <-> 12 | `[11, 12, 25, 22, 64]` |
| 3 | 2 | [25,22,64] | 3 (value 22) | 25 <-> 22 | `[11, 12, 22, 25, 64]` |
| 4 | 3 | [25,64] | 3 (value 25) | No swap | `[11, 12, 22, 25, 64]` |

**Final Output:** `[11, 12, 22, 25, 64]`

## 13. Time & Space Complexity

| Case | Time Complexity | Reason |
|------|-----------------|--------|
| Best Case | O(n²) | Always scans the entire unsorted portion regardless of input |
| Average Case | O(n²) | Makes n(n-1)/2 comparisons on average |
| Worst Case | O(n²) | Same number of comparisons for reverse-sorted data |
| Space Complexity | O(1) | Sorts in-place with only a few variables |

## 14. Advantages

- **Minimal swaps:** At most n-1 swaps, making it ideal for write-expensive environments.
- **Simple logic:** Easy to understand, implement, and debug.
- **In-place:** Uses constant extra memory.
- **Predictable:** Performance does not vary with input order.
- **Fewer cache writes:** Less memory mutation compared to Bubble Sort.

## 15. Disadvantages

- **Always O(n²) comparisons:** Cannot be optimized for nearly sorted data like Bubble Sort or Insertion Sort.
- **Not stable:** Swapping non-adjacent elements can change the relative order of equal elements.
- **Poor cache performance:** The scan for the minimum jumps around memory unpredictably.
- **Slow on large datasets:** Quadratic time makes it impractical for n > 10,000.

## 16. Applications

- Sorting in memory-constrained embedded systems
- Situations where swap operations are significantly more expensive than comparisons
- Educational demonstrations of the selection principle
- Sorting linked lists where pointer swaps are cheaper than data swaps
- One-off scripts with very small input sizes

## 17. Common Mistakes

- **Forgetting to update minIndex inside the inner loop:** The minimum must be tracked throughout the entire unsorted scan.
- **Swapping inside the inner loop:** This defeats the purpose of Selection Sort; swap only once per outer iteration.
- **Assuming it is stable:** Selection Sort is not stable unless carefully implemented with shifts instead of swaps.
- **Using it for large datasets:** Always consider O(n²) implications for production code.

## 18. Interview Questions

1. What is the time complexity of Selection Sort in all cases?
2. How many swaps does Selection Sort perform in the worst case?
3. Is Selection Sort stable? Why or why not?
4. Compare Selection Sort and Bubble Sort in terms of swap operations.
5. When would you prefer Selection Sort over Insertion Sort?
6. Can Selection Sort be implemented recursively?
7. What is the space complexity of Selection Sort?
8. How would you make Selection Sort stable?
9. What is the main advantage of Selection Sort over Bubble Sort?
10. How would you modify Selection Sort to find both the minimum and maximum in each pass?

## 19. Practice Problems

**Easy:**
1. Implement Selection Sort to sort an array in ascending order.
2. Modify Selection Sort to sort in descending order.
3. Count the number of comparisons and swaps performed.
4. Implement Selection Sort on an array of strings.

**Medium:**
5. Implement a stable version of Selection Sort using shifts instead of swaps.
6. Optimize Selection Sort by finding both min and max in each pass (Double Selection Sort).
7. Sort a linked list using Selection Sort.
8. Find the k-th smallest element using a partial Selection Sort.

**Hard:**
9. Implement Selection Sort that minimizes cache misses for large arrays.
10. Create a hybrid algorithm that uses Selection Sort for small subarrays and Merge Sort for large ones.
11. Implement recursive Selection Sort and analyze its stack usage.

## 20. Related Algorithms

- Bubble Sort (more swaps, adaptive)
- Insertion Sort (better for nearly sorted data)
- Heap Sort (similar selection principle but with a heap, O(n log n))
- Cycle Sort (minimizes writes even further)
- Bingo Sort (variant that handles duplicates efficiently)

## 21. Summary

Selection Sort is a straightforward algorithm that repeatedly selects the minimum element from the unsorted portion and places it at the end of the sorted portion. Its main strengths are simplicity and minimal swap operations, making it suitable for write-expensive environments. However, its O(n²) time complexity and lack of adaptivity make it unsuitable for large or nearly sorted datasets. Use it when you need predictable behavior with minimal writes, or when teaching the fundamental concept of selection in sorting.

## 22. Quiz

**Question 1:** What is the time complexity of Selection Sort in all cases?
- A) O(n)
- B) O(n log n)
- C) O(n²)
- D) O(2^n)
- **Correct Answer:** C
- **Explanation:** It always scans the entire unsorted portion, making n(n-1)/2 comparisons regardless of input order.

**Question 2:** How many swaps does Selection Sort perform at most?
- A) n²
- B) n log n
- C) n - 1
- D) 0
- **Correct Answer:** C
- **Explanation:** It performs exactly one swap per outer iteration, for a maximum of n-1 swaps.

**Question 3:** Is Selection Sort stable?
- A) Yes
- B) No
- C) Only for integers
- D) Only for small arrays
- **Correct Answer:** B
- **Explanation:** Swapping non-adjacent elements can change the relative order of equal elements.

**Question 4:** What is the space complexity?
- A) O(n)
- B) O(log n)
- C) O(1)
- D) O(n²)
- **Correct Answer:** C
- **Explanation:** It sorts in-place using only a few index variables.

**Question 5:** In each pass, Selection Sort finds:
- A) The maximum element
- B) The minimum element
- C) The median element
- D) A random element
- **Correct Answer:** B
- **Explanation:** It scans for the smallest element in the unsorted portion.

**Question 6:** When is Selection Sort preferable to Bubble Sort?
- A) When comparisons are expensive
- B) When swaps are expensive
- C) When memory is unlimited
- D) When data is nearly sorted
- **Correct Answer:** B
- **Explanation:** Selection Sort makes far fewer swaps than Bubble Sort.

**Question 7:** What does the outer loop track?
- A) The minimum value found
- B) The boundary between sorted and unsorted regions
- C) The number of swaps
- D) The array size
- **Correct Answer:** B
- **Explanation:** Index i marks where the sorted portion ends and the unsorted portion begins.

**Question 8:** Selection Sort is an example of:
- A) Divide and conquer
- B) Greedy algorithm
- C) Comparison-based sort
- D) Non-comparison sort
- **Correct Answer:** C
- **Explanation:** It sorts by comparing elements to find the minimum.

**Question 9:** What happens if the array is already sorted?
- A) It runs in O(n)
- B) It still makes n²/2 comparisons
- C) It throws an error
- D) It runs in O(log n)
- **Correct Answer:** B
- **Explanation:** Unlike Bubble Sort, it has no early exit and always scans the full unsorted portion.

**Question 10:** Which algorithm uses a similar "selection" principle but achieves O(n log n)?
- A) Bubble Sort
- B) Heap Sort
- C) Insertion Sort
- D) Linear Search
- **Correct Answer:** B
- **Explanation:** Heap Sort repeatedly selects the maximum element using a heap structure for efficient extraction.
