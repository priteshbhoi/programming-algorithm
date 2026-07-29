# Cocktail Shaker Sort

## 1. Introduction

Cocktail Shaker Sort (also known as Bidirectional Bubble Sort, Cocktail Sort, Shaker Sort, Ripple Sort, Shuffle Sort, or Happy Hour Sort) is a variation of Bubble Sort. While standard Bubble Sort traverses the list in a single direction (left to right) pushing the largest unsorted element to the end in each pass, Cocktail Shaker Sort traverses the array in both directions alternately: left to right, then right to left.

Imagine a bartender shaking a cocktail shaker back and forth. On the forward shake (left to right), the largest element bubbles up to the top. On the backward shake (right to left), the smallest element drops down to the bottom.

It was created to solve the "turtles" problem in standard Bubble Sort, where small values near the end of an array take a long time to move to the beginning.

You should study Cocktail Shaker Sort to understand how bidirectional traversal optimizes basic comparison-based sorting, though like Bubble Sort, it is primarily used for educational purposes and nearly-sorted datasets.

## 2. Why Use This Algorithm?

Cocktail Shaker Sort offers a noticeable performance boost over standard Bubble Sort on certain types of partially sorted arrays.

**Benefits:**
- **Solves the "Turtles" Problem:** Small values near the end move quickly toward the front during the backward pass.
- **Adaptive:** Detects early completion and exits in $\mathcal{O}(n)$ time if the array becomes sorted mid-way.
- **Stable & In-Place:** Preserves relative order of equal elements and uses $\mathcal{O}(1)$ extra memory.
- **Simple Concept:** Easy to understand extension of Bubble Sort.

**Performance:**
- **Best Case:** $\mathcal{O}(n)$ (when array is already sorted)
- **Average Case:** $\mathcal{O}(n^2)$
- **Worst Case:** $\mathcal{O}(n^2)$
- **Space Complexity:** $\mathcal{O}(1)$ auxiliary memory space.

**When it is better than standard Bubble Sort:**
It outperforms standard Bubble Sort on arrays where small elements are located near the end (e.g., `[2, 3, 4, 5, 1]`). Bubble Sort requires 4 full passes for `1` to move to the front, whereas Cocktail Shaker Sort moves `1` to index 0 on its very first backward pass.

## 3. Real-World Applications

- **Educational Tool:** Teaching bidirectional iteration and comparison-based sorting optimizations.
- **Nearly Sorted Data:** Quick cleanup passes on data arrays with isolated displaced elements at both ends.

## 4. Prerequisites

Before learning Cocktail Shaker Sort, you should understand:
- Standard [Bubble Sort](./11_bubble_sort.md).
- Nested loops and bidirectional index boundaries (`start` and `end` pointers).
- Swapping variables in-place.

## 5. Visualization

Given Array: `[5, 1, 4, 2, 8]`

1. **Forward Pass (Left to Right, `0 -> 3`):**
   - Compare `5` & `1` -> Swap -> `[1, 5, 4, 2, 8]`
   - Compare `5` & `4` -> Swap -> `[1, 4, 5, 2, 8]`
   - Compare `5` & `2` -> Swap -> `[1, 4, 2, 5, 8]`
   - Compare `5` & `8` -> No Swap
   - `8` is fixed at the end. Decrement `end = 3`.

2. **Backward Pass (Right to Left, `3 -> 0`):**
   - Compare `2` & `5` -> No Swap
   - Compare `4` & `2` -> Swap -> `[1, 2, 4, 5, 8]`
   - Compare `1` & `2` -> No Swap
   - `1` is fixed at the start. Increment `start = 1`.

3. **Check:** No swaps occurred in pass 2; Array is sorted `[1, 2, 4, 5, 8]`.

## 6. How It Works

1. Set `start = 0` and `end = n - 1`, and `swapped = true`.
2. While `swapped` is true:
   - Reset `swapped = false`.
   - **Forward Pass:** Loop `i` from `start` to `end - 1`. If `arr[i] > arr[i + 1]`, swap them and set `swapped = true`.
   - If `swapped` is false, break (array is sorted).
   - Decrement `end` by 1 (the largest element is locked at the end).
   - Reset `swapped = false`.
   - **Backward Pass:** Loop `i` from `end - 1` down to `start`. If `arr[i] > arr[i + 1]`, swap them and set `swapped = true`.
   - Increment `start` by 1 (the smallest element is locked at the start).

## 7. Step-by-Step Algorithm

1. Initialize `start = 0`, `end = n - 1`, `swapped = true`.
2. Loop while `swapped` is true:
   1. `swapped = false`.
   2. For `i = start` to `end - 1`:
      - If `arr[i] > arr[i+1]`: Swap `arr[i]` and `arr[i+1]`, `swapped = true`.
   3. If `swapped == false`: Break loop.
   4. `end = end - 1`.
   5. `swapped = false`.
   6. For `i = end - 1` down to `start`:
      - If `arr[i] > arr[i+1]`: Swap `arr[i]` and `arr[i+1]`, `swapped = true`.
   7. `start = start + 1`.
3. Array is sorted.

## 8. Pseudocode

```text
function cocktailShakerSort(arr):
    n = length(arr)
    swapped = true
    start = 0
    end = n - 1

    while swapped:
        swapped = false
        for i = start to end - 1:
            if arr[i] > arr[i + 1]:
                swap(arr[i], arr[i + 1])
                swapped = true

        if not swapped:
            break

        swapped = false
        end = end - 1

        for i = end - 1 down to start:
            if arr[i] > arr[i + 1]:
                swap(arr[i], arr[i + 1])
                swapped = true

        start = start + 1
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdbool.h>

void swap(int* a, int* b) {
    int temp = *a;
    *a = *b;
    *b = temp;
}

void cocktailShakerSort(int arr[], int n) {
    bool swapped = true;
    int start = 0;
    int end = n - 1;

    while (swapped) {
        swapped = false;
        for (int i = start; i < end; i++) {
            if (arr[i] > arr[i + 1]) {
                swap(&arr[i], &arr[i + 1]);
                swapped = true;
            }
        }

        if (!swapped) break;

        swapped = false;
        end--;

        for (int i = end - 1; i >= start; i--) {
            if (arr[i] > arr[i + 1]) {
                swap(&arr[i], &arr[i + 1]);
                swapped = true;
            }
        }
        start++;
    }
}

int main() {
    int arr[] = {5, 1, 4, 2, 8, 9, 0};
    int n = sizeof(arr) / sizeof(arr[0]);
    cocktailShakerSort(arr, n);
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

void cocktailShakerSort(std::vector<int>& arr) {
    bool swapped = true;
    int start = 0;
    int end = arr.size() - 1;

    while (swapped) {
        swapped = false;
        for (int i = start; i < end; ++i) {
            if (arr[i] > arr[i + 1]) {
                std::swap(arr[i], arr[i + 1]);
                swapped = true;
            }
        }

        if (!swapped) break;

        swapped = false;
        --end;

        for (int i = end - 1; i >= start; --i) {
            if (arr[i] > arr[i + 1]) {
                std::swap(arr[i], arr[i + 1]);
                swapped = true;
            }
        }
        ++start;
    }
}

int main() {
    std::vector<int> arr = {5, 1, 4, 2, 8, 9, 0};
    cocktailShakerSort(arr);
    for (int x : arr) std::cout << x << " ";
    std::cout << "\n";
    return 0;
}
```

### Java
```java
import java.util.Arrays;

public class CocktailShakerSort {
    public static void cocktailShakerSort(int[] arr) {
        boolean swapped = true;
        int start = 0;
        int end = arr.length - 1;

        while (swapped) {
            swapped = false;
            for (int i = start; i < end; i++) {
                if (arr[i] > arr[i + 1]) {
                    int temp = arr[i];
                    arr[i] = arr[i + 1];
                    arr[i + 1] = temp;
                    swapped = true;
                }
            }

            if (!swapped) break;

            swapped = false;
            end--;

            for (int i = end - 1; i >= start; i--) {
                if (arr[i] > arr[i + 1]) {
                    int temp = arr[i];
                    arr[i] = arr[i + 1];
                    arr[i + 1] = temp;
                    swapped = true;
                }
            }
            start++;
        }
    }

    public static void main(String[] args) {
        int[] arr = {5, 1, 4, 2, 8, 9, 0};
        cocktailShakerSort(arr);
        System.out.println(Arrays.toString(arr));
    }
}
```

### Python
```python
def cocktail_shaker_sort(arr: list[int]) -> list[int]:
    n = len(arr)
    swapped = True
    start = 0
    end = n - 1

    while swapped:
        swapped = False
        for i in range(start, end):
            if arr[i] > arr[i + 1]:
                arr[i], arr[i + 1] = arr[i + 1], arr[i]
                swapped = True

        if not swapped:
            break

        swapped = False
        end -= 1

        for i in range(end - 1, start - 1, -1):
            if arr[i] > arr[i + 1]:
                arr[i], arr[i + 1] = arr[i + 1], arr[i]
                swapped = True

        start += 1

    return arr

if __name__ == "__main__":
    data = [5, 1, 4, 2, 8, 9, 0]
    cocktail_shaker_sort(data)
    print(data)
```

### JavaScript
```javascript
function cocktailShakerSort(arr) {
    let swapped = true;
    let start = 0;
    let end = arr.length - 1;

    while (swapped) {
        swapped = false;
        for (let i = start; i < end; i++) {
            if (arr[i] > arr[i + 1]) {
                [arr[i], arr[i + 1]] = [arr[i + 1], arr[i]];
                swapped = true;
            }
        }

        if (!swapped) break;

        swapped = false;
        end--;

        for (let i = end - 1; i >= start; i--) {
            if (arr[i] > arr[i + 1]) {
                [arr[i], arr[i + 1]] = [arr[i + 1], arr[i]];
                swapped = true;
            }
        }
        start++;
    }

    return arr;
}

const data = [5, 1, 4, 2, 8, 9, 0];
cocktailShakerSort(data);
console.log(data);
```

## 10. Code Explanation

Cocktail Shaker Sort replaces standard single-direction looping with a double-ended loop. The `start` pointer tracks the left sorted boundary, while `end` tracks the right sorted boundary. In the forward pass, adjacent elements are compared from `start` to `end - 1`, shifting the largest value to `end`. Then `end` decrements. In the backward pass, elements are compared from `end - 1` down to `start`, shifting the smallest value to `start`. Then `start` increments. The `swapped` boolean ensures early termination if a pass completes without swaps.

## 11. Interactive Demo

An animated bar chart shows dual pointers (`Forward Pointer >` and `< Backward Pointer`) moving across the array.

- Blue highlight moves Left to Right, locking the largest bar at the right end with a Green border.
- Red highlight moves Right to Left, locking the smallest bar at the left end with a Green border.
- The active unsorted middle region shrinks from both ends until pointers converge or no swaps occur.

## 12. Dry Run

**Input:** `[3, 4, 1, 2]`

| Pass | Direction | Comparison / Action | Array State |
| :--- | :--- | :--- | :--- |
| **Pass 1** | Forward (`0->2`) | Swap (4,1), Swap (4,2) | `[3, 1, 2, 4]` (`end` = 2) |
| **Pass 1** | Backward (`1->0`) | Swap (3,2), Swap (3,1) | `[1, 2, 3, 4]` (`start` = 1) |
| **Pass 2** | Forward (`1->1`) | No swaps | `[1, 2, 3, 4]` (Terminates) |

## 13. Time & Space Complexity

| Case | Time Complexity | Reason |
| :--- | :--- | :--- |
| **Best Case** | $\mathcal{O}(n)$ | Pre-sorted data detected in 1 pass |
| **Average Case** | $\mathcal{O}(n^2)$ | Quadratic comparison pairs |
| **Worst Case** | $\mathcal{O}(n^2)$ | Reverse-sorted input requires full passes |
| **Space Complexity** | $\mathcal{O}(1)$ | In-place swaps |

## 14. Advantages

- **Fixes Turtle Problem:** Small items near the right end move left quickly.
- **Adaptive:** Terminates in $\mathcal{O}(n)$ on sorted data.
- **Stable & In-Place:** Preserves relative order with $\mathcal{O}(1)$ space.

## 15. Disadvantages

- **Quadratic Worst Case:** Still $\mathcal{O}(n^2)$ on random or reversed data.
- **Slower Than $\mathcal{O}(n \log n)$ Sorts:** Impractical for large datasets.

## 16. Applications

- Educational visualizer for bidirectional algorithms.
- Small arrays with displaced values at both ends.

## 17. Common Mistakes

- **Incorrect Loop Boundaries:** Index out-of-bounds in backward pass (`end - 1` vs `start`).
- **Forgetting to Decrement/Increment Pointers:** Loops endlessly if `start` and `end` boundaries are static.

## 18. Interview Questions

1. How does Cocktail Shaker Sort address the "turtle" problem of Bubble Sort?
2. What is the space complexity of Cocktail Shaker Sort?
3. Is Cocktail Shaker Sort stable? Why?
4. What is the best-case time complexity?

## 19. Practice Problems

**Easy:**
1. Implement Cocktail Shaker Sort for an array of integers.
2. Count total forward vs backward swaps performed.

**Medium:**
3. Optimize Cocktail Shaker Sort by tracking the index of the last swap performed in each direction.

**Hard:**
4. Adapt Cocktail Shaker Sort to work on a doubly-linked list.

## 20. Related Algorithms

- [Bubble Sort](./11_bubble_sort.md) (Standard unidirectional parent algorithm)
- [Comb Sort](./24_comb_sort.md) (Gap-based optimization of Bubble Sort)
- [Gnome Sort](./25_gnome_sort.md) (Another bidirectional exchange sort)

## 21. Summary

Cocktail Shaker Sort is a bidirectional variant of Bubble Sort. By alternating between left-to-right (bubbling largest element up) and right-to-left (dropping smallest element down) passes, it solves the turtle problem where small items at the end take many iterations to reach the front, while keeping $\mathcal{O}(1)$ space and algorithm stability.

## 22. Quiz

**Question 1:** What is another common name for Cocktail Shaker Sort?
- A) Quick Sort
- B) Bidirectional Bubble Sort
- C) Radix Sort
- D) Shell Sort
- **Correct Answer:** B
- **Explanation:** It sorts in both directions like a cocktail shaker moving back and forth.

**Question 2:** What problem in standard Bubble Sort does Cocktail Shaker Sort solve?
- A) Stack overflow
- B) The "turtles" problem (small elements near the end moving slowly)
- C) Integer overflow
- D) Memory allocation
- **Correct Answer:** B
- **Explanation:** Small elements at the right end are pulled left quickly during backward passes.

**Question 3:** What is the best-case time complexity of Cocktail Shaker Sort?
- A) $\mathcal{O}(1)$
- B) $\mathcal{O}(n)$
- C) $\mathcal{O}(n \log n)$
- D) $\mathcal{O}(n^2)$
- **Correct Answer:** B
- **Explanation:** If the array is already sorted, 0 swaps occur in the first pass, exiting in $\mathcal{O}(n)$ time.

**Question 4:** Is Cocktail Shaker Sort a stable sorting algorithm?
- A) Yes
- B) No
- **Correct Answer:** A
- **Explanation:** Swaps only occur when strictly greater than (`>`), preserving relative duplicate order.

**Question 5:** What is the auxiliary space complexity of Cocktail Shaker Sort?
- A) $\mathcal{O}(n)$
- B) $\mathcal{O}(1)$
- C) $\mathcal{O}(\log n)$
- D) $\mathcal{O}(n^2)$
- **Correct Answer:** B
- **Explanation:** It sorts entirely in-place with a constant number of pointer variables.

**Question 6:** What is the worst-case time complexity of Cocktail Shaker Sort?
- A) $\mathcal{O}(n \log n)$
- B) $\mathcal{O}(n^2)$
- C) $\mathcal{O}(n)$
- D) $\mathcal{O}(2^n)$
- **Correct Answer:** B
- **Explanation:** Worst-case reverse-sorted inputs require $\mathcal{O}(n^2)$ total comparison steps.

**Question 7:** What happens to `start` and `end` bounds after each pass?
- A) `start` increases, `end` decreases
- B) Both increase
- C) Both decrease
- D) They stay static
- **Correct Answer:** A
- **Explanation:** Forward pass locks max element (`end--`), backward pass locks min element (`start++`).

**Question 8:** Can Cocktail Shaker Sort be implemented efficiently on a doubly linked list?
- A) No, linked lists cannot be swapped
- B) Yes, because doubly linked lists allow forward and backward pointer traversal
- C) Only if the list length is even
- D) Only on circular lists
- **Correct Answer:** B
- **Explanation:** Doubly linked lists support bidirectional `next` and `prev` node traversal.

**Question 9:** How does Cocktail Shaker Sort detect that the array is already sorted?
- A) By checking if `start == end`
- B) By using a `swapped` flag during a pass
- C) By measuring stack depth
- D) By computing array sum
- **Correct Answer:** B
- **Explanation:** If no swaps occur throughout a full pass, `swapped` remains false and execution stops.

**Question 10:** How does Cocktail Shaker Sort compare to Insertion Sort on average?
- A) Cocktail Shaker Sort is much faster
- B) Both are $\mathcal{O}(n^2)$ on average, but Insertion Sort generally has lower constant factors
- C) Insertion Sort is $\mathcal{O}(n^3)$
- D) They have identical code
- **Correct Answer:** B
- **Explanation:** Both are $\mathcal{O}(n^2)$ average, but Insertion Sort performs fewer comparisons on average.
