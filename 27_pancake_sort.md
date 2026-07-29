# Pancake Sort

## 1. Introduction

Pancake Sort is a comparison sorting algorithm in which the only allowed operation is reversing the elements of some prefix of the array (called a "flip"). The name comes from the mathematical problem of sorting a stack of pancakes by size using a spatula to flip the top $k$ pancakes in the stack.

Imagine a stack of pancakes of varying diameters. You can insert a spatula anywhere in the stack and flip all the pancakes above the spatula upside down. Your goal is to arrange the stack so that the largest pancake is on the bottom and the smallest pancake is on top.

It was discussed mathematically by Jacob E. Goodman under the pseudonym "Harry Dweighter" in 1975, and later famously analyzed by Bill Gates and Christos Papadimitriou in 1979 (providing a bound of $\frac{5n + 5}{3}$ flips).

You should study Pancake Sort as a classic algorithm problem in computer science that restricts allowed operations to prefix reversals.

## 2. Why Use This Algorithm?

Pancake Sort demonstrates how complex sorting tasks can be solved under strict procedural constraints (only prefix reversals allowed).

**Benefits:**
- **Constrained Operation Mastery:** Uses only a single `flip(arr, k)` helper function.
- **In-Place Execution:** Requires $\mathcal{O}(1)$ auxiliary space.
- **Guaranteed Flip Bounds:** Sorts any array in at most $2n - 3$ flip operations.

**Performance:**
- **Best Case:** $\mathcal{O}(n)$ (when array is already sorted)
- **Average Case:** $\mathcal{O}(n^2)$ time ($\mathcal{O}(n)$ flips)
- **Worst Case:** $\mathcal{O}(n^2)$ time ($2n - 3$ flips)
- **Space Complexity:** $\mathcal{O}(1)$ auxiliary space.

**When it is better than other algorithms:**
Pancake Sort is utilized when memory or mechanical manipulators can only perform prefix reversals (such as robotic arms or optical ribbon routing).

## 3. Real-World Applications

- **Robotics & Spatial Manipulators:** Robotic arms that can flip or reverse contiguous top items in a stack.
- **Parallel Interconnection Networks:** Pancake graphs used in parallel processing network topologies.
- **Gene Reordering & Bioinformatics:** Modeling genome rearrangements via reversal operations.

## 4. Prerequisites

Before learning Pancake Sort, you should understand:
- In-place array prefix reversal (`reverse(arr, 0, k)`).
- Finding the maximum element index in a sub-range `[0...curr_size-1]`.
- Selection sort principles (placing the largest remaining element at the end).

## 5. Visualization

Given Stack: `[3, 2, 4, 1]`

1. **Find max in `[3, 2, 4, 1]`:** Max is `4` at index 2.
2. **Flip 1 (`k = 2`):** Reverse `[0..2]` (`[3, 2, 4]` -> `[4, 2, 3]`). Array: `[4, 2, 3, 1]`. (Max is now at top!).
3. **Flip 2 (`k = 3`):** Reverse `[0..3]` (`[4, 2, 3, 1]` -> `[1, 3, 2, 4]`). (Max `4` is now at bottom!).

4. **Find max in `[1, 3, 2]`:** Max is `3` at index 1.
5. **Flip 3 (`k = 1`):** Reverse `[0..1]` -> `[3, 1, 2, 4]`.
6. **Flip 4 (`k = 2`):** Reverse `[0..2]` -> `[2, 1, 3, 4]`.

7. **Find max in `[2, 1]`:** Max is `2` at index 0 (at top).
8. **Flip 5 (`k = 1`):** Reverse `[0..1]` -> `[1, 2, 3, 4]`. Sorted!

## 6. How It Works

1. Start with `curr_size = n`.
2. Find the index `max_idx` of the maximum element in `arr[0 ... curr_size - 1]`.
3. If `max_idx != curr_size - 1`:
   - If `max_idx != 0`: Flip sub-array `arr[0 ... max_idx]` so the maximum element moves to index `0` (top of stack).
   - Flip sub-array `arr[0 ... curr_size - 1]` so the maximum element moves to index `curr_size - 1` (bottom of current sub-stack).
4. Decrement `curr_size` by 1 and repeat until `curr_size <= 1`.

## 7. Step-by-Step Algorithm

1. Loop `curr_size` from `n` down to `2`:
   1. Find index `mi` of max element in `arr[0...curr_size - 1]`.
   2. If `mi != curr_size - 1`:
      - If `mi != 0`: Call `flip(arr, mi)` (bring max to top).
      - Call `flip(arr, curr_size - 1)` (bring max to bottom of current stack).
2. Array is sorted.

## 8. Pseudocode

```text
function pancakeSort(arr):
    n = length(arr)

    for currSize = n down to 2:
        maxIdx = findMaxIndex(arr, currSize)

        if maxIdx != currSize - 1:
            if maxIdx != 0:
                flip(arr, maxIdx)
            flip(arr, currSize - 1)

function flip(arr, k):
    left = 0
    right = k
    while left < right:
        swap(arr[left], arr[right])
        left = left + 1
        right = right - 1

function findMaxIndex(arr, size):
    maxIdx = 0
    for i = 1 to size - 1:
        if arr[i] > arr[maxIdx]:
            maxIdx = i
    return maxIdx
```

## 9. Code Examples

### C
```c
#include <stdio.h>

void flip(int arr[], int k) {
    int left = 0;
    while (left < k) {
        int temp = arr[left];
        arr[left] = arr[k];
        arr[k] = temp;
        left++;
        k--;
    }
}

int findMaxIndex(int arr[], int n) {
    int mi = 0;
    for (int i = 1; i < n; i++) {
        if (arr[i] > arr[mi]) mi = i;
    }
    return mi;
}

void pancakeSort(int arr[], int n) {
    for (int curr_size = n; curr_size > 1; curr_size--) {
        int mi = findMaxIndex(arr, curr_size);
        if (mi != curr_size - 1) {
            if (mi != 0) flip(arr, mi);
            flip(arr, curr_size - 1);
        }
    }
}

int main() {
    int arr[] = {23, 10, 20, 11, 12, 6, 7};
    int n = sizeof(arr) / sizeof(arr[0]);
    pancakeSort(arr, n);
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

void flip(std::vector<int>& arr, int k) {
    std::reverse(arr.begin(), arr.begin() + k + 1);
}

int findMaxIndex(const std::vector<int>& arr, int n) {
    int mi = 0;
    for (int i = 1; i < n; i++) {
        if (arr[i] > arr[mi]) mi = i;
    }
    return mi;
}

void pancakeSort(std::vector<int>& arr) {
    for (int curr_size = arr.size(); curr_size > 1; curr_size--) {
        int mi = findMaxIndex(arr, curr_size);
        if (mi != curr_size - 1) {
            if (mi != 0) flip(arr, mi);
            flip(arr, curr_size - 1);
        }
    }
}

int main() {
    std::vector<int> arr = {23, 10, 20, 11, 12, 6, 7};
    pancakeSort(arr);
    for (int x : arr) std::cout << x << " ";
    std::cout << "\n";
    return 0;
}
```

### Java
```java
import java.util.Arrays;

public class PancakeSort {
    private static void flip(int[] arr, int k) {
        int left = 0;
        while (left < k) {
            int temp = arr[left];
            arr[left] = arr[k];
            arr[k] = temp;
            left++;
            k--;
        }
    }

    private static int findMaxIndex(int[] arr, int n) {
        int mi = 0;
        for (int i = 1; i < n; i++) {
            if (arr[i] > arr[mi]) mi = i;
        }
        return mi;
    }

    public static void pancakeSort(int[] arr) {
        for (int currSize = arr.length; currSize > 1; currSize--) {
            int mi = findMaxIndex(arr, currSize);
            if (mi != currSize - 1) {
                if (mi != 0) flip(arr, mi);
                flip(arr, currSize - 1);
            }
        }
    }

    public static void main(String[] args) {
        int[] arr = {23, 10, 20, 11, 12, 6, 7};
        pancakeSort(arr);
        System.out.println(Arrays.toString(arr));
    }
}
```

### Python
```python
def flip(arr: list[int], k: int) -> None:
    left = 0
    while left < k:
        arr[left], arr[k] = arr[k], arr[left]
        left += 1
        k -= 1

def find_max_index(arr: list[int], n: int) -> int:
    mi = 0
    for i in range(1, n):
        if arr[i] > arr[mi]:
            mi = i
    return mi

def pancake_sort(arr: list[int]) -> list[int]:
    curr_size = len(arr)
    while curr_size > 1:
        mi = find_max_index(arr, curr_size)
        if mi != curr_size - 1:
            if mi != 0:
                flip(arr, mi)
            flip(arr, curr_size - 1)
        curr_size -= 1
    return arr

if __name__ == "__main__":
    data = [23, 10, 20, 11, 12, 6, 7]
    pancake_sort(data)
    print(data)
```

### JavaScript
```javascript
function flip(arr, k) {
    let left = 0;
    while (left < k) {
        [arr[left], arr[k]] = [arr[k], arr[left]];
        left++;
        k--;
    }
}

function findMaxIndex(arr, n) {
    let mi = 0;
    for (let i = 1; i < n; i++) {
        if (arr[i] > arr[mi]) mi = i;
    }
    return mi;
}

function pancakeSort(arr) {
    for (let currSize = arr.length; currSize > 1; currSize--) {
        const mi = findMaxIndex(arr, currSize);
        if (mi !== currSize - 1) {
            if (mi !== 0) flip(arr, mi);
            flip(arr, currSize - 1);
        }
    }
    return arr;
}

const data = [23, 10, 20, 11, 12, 6, 7];
pancakeSort(data);
console.log(data);
```

## 10. Code Explanation

Pancake Sort operates like a variation of Selection Sort constrained to prefix reversals. In each iteration over `curr_size` from `n` down to 2, it identifies the location `mi` of the maximum element in `arr[0...curr_size - 1]`. If the maximum element is not already at the end of the current sub-stack (`curr_size - 1`), it performs at most two flips:
1. `flip(arr, mi)` moves the maximum element to index `0` (top of stack).
2. `flip(arr, curr_size - 1)` flips the entire active sub-stack, sending the maximum element to index `curr_size - 1` (bottom of current sub-stack).

## 11. Interactive Demo

The UI renders an animated stack of golden pancakes of varying widths.

- A spatula icon slides under the $k$-th pancake.
- On click, the top portion flips $180^\circ$ upside down with a realistic rotation animation.
- A "Flips Performed" counter logs the sequence of $k$-values used during sorting.

## 12. Dry Run

**Input:** `[3, 2, 4, 1]` ($n = 4$)

| Pass (`curr_size`) | Max Index | Flip 1 (Top) | Flip 2 (Bottom) | Stack State |
| :--- | :--- | :--- | :--- | :--- |
| `4` | 2 (`4`) | `flip(2)` -> `[4, 2, 3, 1]` | `flip(3)` -> `[1, 3, 2, 4]` | `[1, 3, 2, 4]` |
| `3` | 1 (`3`) | `flip(1)` -> `[3, 1, 2, 4]` | `flip(2)` -> `[2, 1, 3, 4]` | `[2, 1, 3, 4]` |
| `2` | 0 (`2`) | Skipped (already top) | `flip(1)` -> `[1, 2, 3, 4]` | `[1, 2, 3, 4]` |

Total Flips: **5 flips**.

## 13. Time & Space Complexity

| Case | Time Complexity | Reason |
| :--- | :--- | :--- |
| **Best Case** | $\mathcal{O}(n)$ | Pre-sorted array requires 0 flips |
| **Average Case** | $\mathcal{O}(n^2)$ | Finding max takes $\mathcal{O}(n)$ per iteration |
| **Worst Case** | $\mathcal{O}(n^2)$ | Maximum $2n - 3$ flips, each taking $\mathcal{O}(n)$ time |
| **Space Complexity** | $\mathcal{O}(1)$ | In-place prefix reversals |

## 14. Advantages

- **Minimal Allowed Primitives:** Solves sorting using only prefix reversal flips.
- **In-Place:** Requires $\mathcal{O}(1)$ extra memory space.
- **Bounded Flips:** At most $2n - 3$ flips required for any array.

## 15. Disadvantages

- **$\mathcal{O}(n^2)$ Time:** Slow for large datasets compared to $\mathcal{O}(n \log n)$ algorithms.
- **Unstable:** Prefix reversals alter relative order of duplicate elements.

## 16. Applications

- Robotic manipulators and automated stack reordering.
- Bioinformatics (modeling chromosomal inversion mutations).
- Parallel network topology routing (Pancake graphs).

## 17. Common Mistakes

- **Incorrect Flip Bounds:** Reversing index `0` to `k - 1` instead of `0` to `k`.
- **Flipping when element is already at target:** Performing unnecessary zero-effect flips.

## 18. Interview Questions

1. What famous computer scientist published a paper on Pancake Sorting bounds in 1979?
2. What is the maximum number of flips needed to sort an array of size $n$?
3. How is Pancake Sort related to Selection Sort?

## 19. Practice Problems

**Easy:**
1. Implement Pancake Sort function returning the array of $k$ flip values used.
2. Given array `[3, 2, 4, 1]`, output the minimal sequence of flips.

**Medium:**
3. Implement Burnt Pancake Sort (where pancakes have a burnt side that must end up facing down).

## 20. Related Algorithms

- [Selection Sort](./12_selection_sort.md) (Conceptually places maximum elements at the end)
- [Gnome Sort](./25_gnome_sort.md) (Constrained movement exchange sort)

## 21. Summary

Pancake Sort is an in-place sorting algorithm that arranges elements using only prefix reversals (flips). By repeatedly finding the maximum unsorted element, flipping it to the top, and then flipping it to its correct bottom position, it sorts any array of size $n$ in at most $2n - 3$ flips and $\mathcal{O}(n^2)$ time.

## 22. Quiz

**Question 1:** What is the only allowed modification operation in Pancake Sort?
- A) Element swap
- B) Prefix reversal ("flip")
- C) Bitwise shift
- D) Heap insertion
- **Correct Answer:** B
- **Explanation:** Pancake Sort allows only reversing the order of the first $k$ elements.

**Question 2:** Who co-authored a famous 1979 paper establishing bounds for Pancake Sorting?
- A) Bill Gates and Christos Papadimitriou
- B) Tim Peters and Guido van Rossum
- C) Donald Knuth and Alan Turing
- D) Dennis Ritchie and Ken Thompson
- **Correct Answer:** A
- **Explanation:** Bill Gates and Christos Papadimitriou published bounds on Pancake Sorting in 1979.

**Question 3:** What is the maximum number of flips required to sort an array of size $n$ using standard Pancake Sort?
- A) $n^2$
- B) $2n - 3$
- C) $n \log n$
- D) $2^n$
- **Correct Answer:** B
- **Explanation:** Each element takes at most 2 flips to reach its position, giving a bound of $2n - 3$.

**Question 4:** What is the worst-case time complexity of Pancake Sort?
- A) $\mathcal{O}(n \log n)$
- B) $\mathcal{O}(n^2)$
- C) $\mathcal{O}(n)$
- D) $\mathcal{O}(1)$
- **Correct Answer:** B
- **Explanation:** Finding the max and reversing prefixes for $n$ elements takes $\mathcal{O}(n^2)$ time.

**Question 5:** Is Pancake Sort an in-place algorithm?
- A) Yes
- B) No
- **Correct Answer:** A
- **Explanation:** It reverses sub-arrays in-place using $\mathcal{O}(1)$ auxiliary memory.

**Question 6:** What basic sorting strategy does Pancake Sort emulate?
- A) Merge Sort
- B) Selection Sort
- C) Radix Sort
- D) Binary Search
- **Correct Answer:** B
- **Explanation:** It selects the maximum remaining element and places it at the end of the unsorted range.

**Question 7:** How many flips are needed to move an element from index `i` to index `curr_size - 1` if it is not at top or bottom?
- A) 1 flip
- B) 2 flips (one to top, one to bottom)
- C) 3 flips
- D) 0 flips
- **Correct Answer:** B
- **Explanation:** Flip 1 moves it from index `i` to top (index 0); Flip 2 moves it from top to index `curr_size - 1`.

**Question 8:** What is "Burnt Pancake Sort"?
- A) Pancake sort on floating point numbers
- B) A variant where each pancake has a burnt side that must face down in the final stack
- C) Pancake sort using 2 spatulas
- D) Pancake sort on multi-threaded CPUs
- **Correct Answer:** B
- **Explanation:** Burnt Pancake Sort adds orientation constraints (burnt side down) to every pancake.

**Question 9:** Is Pancake Sort stable?
- A) Yes
- B) No
- **Correct Answer:** B
- **Explanation:** Reversing array prefixes reverses relative order of equal elements.

**Question 10:** What is the space complexity of Pancake Sort?
- A) $\mathcal{O}(n)$
- B) $\mathcal{O}(1)$
- C) $\mathcal{O}(\log n)$
- D) $\mathcal{O}(n^2)$
- **Correct Answer:** B
- **Explanation:** Uses constant auxiliary space for pointers during prefix reversal.
