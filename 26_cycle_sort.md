# Cycle Sort

## 1. Introduction

Cycle Sort is an in-place, non-stable comparison sorting algorithm invented by W. D. Frazer and A. C. McKellar in 1970. It is unique because it minimizes the total number of memory writes required to sort an array. Every element is written to its final destination at most once (or twice if an element is already in place).

Imagine a group of people sitting in numbered chairs, but everyone is sitting in the wrong chair. Instead of shuffling people around randomly, you pick up person #1, count how many people in the room should sit before them, and put person #1 directly into their correct target chair. The person who was sitting in that chair is now displaced, so you repeat the process with them until the cycle closes back to person #1's original seat. That is Cycle Sort.

It was created to optimize memory writes in situations where writing to memory is extremely expensive or wears out physical storage media (such as EEPROM or Flash memory).

You should use Cycle Sort when writing to memory is significantly more expensive than reading from memory.

## 2. Why Use This Algorithm?

Cycle Sort guarantees the theoretical minimum number of memory writes to sort an array.

**Benefits:**
- **Theoretical Minimum Memory Writes:** Performs at most $n - 1$ memory writes in the worst case (optimal for EEPROM/Flash memory).
- **In-Place Execution:** Requires $\mathcal{O}(1)$ auxiliary memory space.
- **Optimal Write Efficiency:** Never writes an element unless it is moving it to its final sorted position.

**Performance:**
- **Best Case:** $\mathcal{O}(n^2)$ (or $\mathcal{O}(n^2)$ comparisons)
- **Average Case:** $\mathcal{O}(n^2)$
- **Worst Case:** $\mathcal{O}(n^2)$
- **Space Complexity:** $\mathcal{O}(1)$ auxiliary memory space.

**When it is better than other algorithms:**
Cycle Sort dominates when write operations have a high cost (e.g., solid-state flash memory write cycle limits or expensive hardware memory transactions).

## 3. Real-World Applications

- **Flash Memory & EEPROM:** Systems where write operations cause physical hardware degradation over time.
- **NVRAM Systems:** Embedded microcontrollers with high-cost persistent write cycles.
- **Theoretical Minimum Write Benchmarks:** Measuring minimum memory modification bounds.

## 4. Prerequisites

Before studying Cycle Sort, you should understand:
- In-place array rotation and permutation cycles.
- Linear counting to determine the rank of an element.
- Handling duplicate element placements.

## 5. Visualization

Given Array: `[4, 3, 2, 1]`

1. **Cycle 1 (Start at index 0, item `4`):**
   - Count elements smaller than `4` -> 3 elements (`3, 2, 1`).
   - `4` belongs at index `3`.
   - Swap `4` with item currently at index `3` (`1`). Array becomes `[4, 3, 2, 4]` (temp holds `1`).
   - Now place `1`: Count smaller elements -> 0 elements. Belongs at index 0. Put `1` at index 0. `[1, 3, 2, 4]`. Cycle 1 closed!

2. **Cycle 2 (Start at index 1, item `3`):**
   - Count smaller elements to the right -> 1 element (`2`). Belongs at index 2.
   - Swap `3` into index 2. Array becomes `[1, 2, 3, 4]`. Cycle 2 closed!

3. Final Sorted Array: `[1, 2, 3, 4]`.

## 6. How It Works

1. Iterate `cycleStart` from `0` to `n - 2`.
2. Set `item = arr[cycleStart]`.
3. Count how many elements after `cycleStart` are smaller than `item`. Let this count be `pos`.
4. If `pos == cycleStart`, the item is already in place; continue to next `cycleStart`.
5. Skip any duplicate elements (`while (item == arr[pos]) pos++`).
6. Swap `item` with `arr[pos]` (this puts `item` into its correct final position).
7. While the current cycle has not returned to `cycleStart`:
   - Repeat the process for the displaced `item`: count smaller elements, skip duplicates, and write `item` to its final `pos`.

## 7. Step-by-Step Algorithm

1. Loop `cycle_start` from `0` to `n - 2`:
   1. `item = arr[cycle_start]`.
   2. Compute `pos = cycle_start + count(arr[i] < item for i > cycle_start)`.
   3. If `pos == cycle_start`: continue.
   4. Skip duplicates: `while item == arr[pos]: pos++`.
   5. Swap `item` and `arr[pos]`.
   6. While `pos != cycle_start`:
      - Compute `pos = cycle_start + count(arr[i] < item for i > cycle_start)`.
      - Skip duplicates: `while item == arr[pos]: pos++`.
      - Swap `item` and `arr[pos]`.
2. Array is sorted.

## 8. Pseudocode

```text
function cycleSort(arr):
    n = length(arr)

    for cycleStart = 0 to n - 2:
        item = arr[cycleStart]
        pos = cycleStart

        for i = cycleStart + 1 to n - 1:
            if arr[i] < item:
                pos = pos + 1

        if pos == cycleStart:
            continue

        while item == arr[pos]:
            pos = pos + 1

        swap(item, arr[pos])

        while pos != cycleStart:
            pos = cycleStart
            for i = cycleStart + 1 to n - 1:
                if arr[i] < item:
                    pos = pos + 1

            while item == arr[pos]:
                pos = pos + 1

            swap(item, arr[pos])
```

## 9. Code Examples

### C
```c
#include <stdio.h>

void cycleSort(int arr[], int n) {
    int writes = 0;

    for (int cycleStart = 0; cycleStart <= n - 2; cycleStart++) {
        int item = arr[cycleStart];
        int pos = cycleStart;

        for (int i = cycleStart + 1; i < n; i++) {
            if (arr[i] < item) pos++;
        }

        if (pos == cycleStart) continue;

        while (item == arr[pos]) pos++;

        int temp = arr[pos];
        arr[pos] = item;
        item = temp;
        writes++;

        while (pos != cycleStart) {
            pos = cycleStart;
            for (int i = cycleStart + 1; i < n; i++) {
                if (arr[i] < item) pos++;
            }

            while (item == arr[pos]) pos++;

            temp = arr[pos];
            arr[pos] = item;
            item = temp;
            writes++;
        }
    }
}

int main() {
    int arr[] = {10, 5, 2, 3, 7, 6, 4, 1};
    int n = sizeof(arr) / sizeof(arr[0]);
    cycleSort(arr, n);
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

void cycleSort(std::vector<int>& arr) {
    int n = arr.size();

    for (int cycleStart = 0; cycleStart <= n - 2; cycleStart++) {
        int item = arr[cycleStart];
        int pos = cycleStart;

        for (int i = cycleStart + 1; i < n; i++) {
            if (arr[i] < item) pos++;
        }

        if (pos == cycleStart) continue;

        while (item == arr[pos]) pos++;

        std::swap(item, arr[pos]);

        while (pos != cycleStart) {
            pos = cycleStart;
            for (int i = cycleStart + 1; i < n; i++) {
                if (arr[i] < item) pos++;
            }

            while (item == arr[pos]) pos++;

            std::swap(item, arr[pos]);
        }
    }
}

int main() {
    std::vector<int> arr = {10, 5, 2, 3, 7, 6, 4, 1};
    cycleSort(arr);
    for (int x : arr) std::cout << x << " ";
    std::cout << "\n";
    return 0;
}
```

### Java
```java
import java.util.Arrays;

public class CycleSort {
    public static void cycleSort(int[] arr) {
        int n = arr.length;

        for (int cycleStart = 0; cycleStart <= n - 2; cycleStart++) {
            int item = arr[cycleStart];
            int pos = cycleStart;

            for (int i = cycleStart + 1; i < n; i++) {
                if (arr[i] < item) pos++;
            }

            if (pos == cycleStart) continue;

            while (item == arr[pos]) pos++;

            int temp = arr[pos];
            arr[pos] = item;
            item = temp;

            while (pos != cycleStart) {
                pos = cycleStart;
                for (int i = cycleStart + 1; i < n; i++) {
                    if (arr[i] < item) pos++;
                }

                while (item == arr[pos]) pos++;

                temp = arr[pos];
                arr[pos] = item;
                item = temp;
            }
        }
    }

    public static void main(String[] args) {
        int[] arr = {10, 5, 2, 3, 7, 6, 4, 1};
        cycleSort(arr);
        System.out.println(Arrays.toString(arr));
    }
}
```

### Python
```python
def cycle_sort(arr: list[int]) -> list[int]:
    n = len(arr)

    for cycle_start in range(0, n - 1):
        item = arr[cycle_start]
        pos = cycle_start

        for i in range(cycle_start + 1, n):
            if arr[i] < item:
                pos += 1

        if pos == cycle_start:
            continue

        while item == arr[pos]:
            pos += 1

        arr[pos], item = item, arr[pos]

        while pos != cycle_start:
            pos = cycle_start
            for i in range(cycle_start + 1, n):
                if arr[i] < item:
                    pos += 1

            while item == arr[pos]:
                pos += 1

            arr[pos], item = item, arr[pos]

    return arr

if __name__ == "__main__":
    data = [10, 5, 2, 3, 7, 6, 4, 1]
    cycle_sort(data)
    print(data)
```

### JavaScript
```javascript
function cycleSort(arr) {
    const n = arr.length;

    for (let cycleStart = 0; cycleStart <= n - 2; cycleStart++) {
        let item = arr[cycleStart];
        let pos = cycleStart;

        for (let i = cycleStart + 1; i < n; i++) {
            if (arr[i] < item) pos++;
        }

        if (pos === cycleStart) continue;

        while (item === arr[pos]) pos++;

        [arr[pos], item] = [item, arr[pos]];

        while (pos !== cycleStart) {
            pos = cycleStart;
            for (let i = cycleStart + 1; i < n; i++) {
                if (arr[i] < item) pos++;
            }

            while (item === arr[pos]) pos++;

            [arr[pos], item] = [item, arr[pos]];
        }
    }
    return arr;
}

const data = [10, 5, 2, 3, 7, 6, 4, 1];
cycleSort(data);
console.log(data);
```

## 10. Code Explanation

Cycle Sort works by decomposing the array permutation into disjoint cycles. For each index `cycleStart`, the algorithm counts how many items in the array are strictly smaller than `arr[cycleStart]`. That count gives the exact final index `pos` where `arr[cycleStart]` belongs. The algorithm places `arr[cycleStart]` directly into `arr[pos]` and picks up the displaced value. It repeats this cycle until the displaced value returns to `cycleStart`, completing the cycle write with minimum operations.

## 11. Interactive Demo

The demo features a Write Counter metric prominently displayed in glowing neon green alongside a Comparison Counter.

- As elements move, a line traces the permutation cycle visually connecting displaced positions.
- When an element is placed at its target index, a "Write +1" badge flashes and locks the slot.

## 12. Dry Run

**Input:** `[3, 2, 1]` ($n = 3$)

| `cycleStart` | Item Picked | Smaller Count (`pos`) | Write Action | Array State |
| :--- | :--- | :--- | :--- | :--- |
| `0` | `3` | 2 (`pos = 2`) | Place `3` at index 2 (displace `1`) | `[3, 2, 3]` (`item` = 1) |
| `0` (cont.) | `1` | 0 (`pos = 0`) | Place `1` at index 0 | `[1, 2, 3]` (Cycle 1 done) |
| `1` | `2` | 1 (`pos = 1`) | `pos == cycleStart` | `[1, 2, 3]` (Done) |

Total Writes: **2** (Theoretical minimum for 3-element reverse array).

## 13. Time & Space Complexity

| Case | Time Complexity | Reason |
| :--- | :--- | :--- |
| **Best Case** | $\mathcal{O}(n^2)$ | Requires counting smaller items for all elements |
| **Average Case** | $\mathcal{O}(n^2)$ | Quadratic comparison scans |
| **Worst Case** | $\mathcal{O}(n^2)$ | Quadratic comparison scans |
| **Space Complexity** | $\mathcal{O}(1)$ | In-place execution |

## 14. Advantages

- **Optimal Memory Writes:** Guarantees at most $n - 1$ memory writes in the worst case.
- **In-Place:** Requires $\mathcal{O}(1)$ extra memory.
- **Ideal for Flash Storage:** Extends hardware lifespan on write-sensitive storage hardware.

## 15. Disadvantages

- **Quadratic Time Complexity:** $\mathcal{O}(n^2)$ time complexity makes it slow for large datasets.
- **Unstable:** Does not preserve initial relative order of equal elements.

## 16. Applications

- Writing to EEPROM, Flash memory, and solid-state storage.
- Systems where memory write cycles wear out physical hardware.

## 17. Common Mistakes

- **Failing to Skip Duplicate Elements:** Causes infinite loop when identical elements exist (`while (item == arr[pos]) pos++`).

## 18. Interview Questions

1. What is the main theoretical advantage of Cycle Sort over all other sorting algorithms?
2. What is the maximum number of memory writes performed by Cycle Sort on an array of size $n$?
3. Is Cycle Sort stable? Why?

## 19. Practice Problems

**Easy:**
1. Implement Cycle Sort for an integer array.
2. Count the exact number of memory writes performed for an input array.

**Medium:**
3. Handle duplicate values correctly in Cycle Sort.

## 20. Related Algorithms

- [Selection Sort](./12_selection_sort.md) (Performs $\mathcal{O}(n)$ swaps but $\mathcal{O}(n^2)$ writes during scans)
- [In-Place Array Rotation](file:///D:/Pritesh/Learning%20Materials/Algorithm/README.md) (Permutation cycle techniques)

## 21. Summary

Cycle Sort is an in-place non-stable sorting algorithm that minimizes memory write operations. By calculating the exact final position of each element through linear rank counts and placing elements via permutation cycles, it guarantees at most $n - 1$ memory writes, making it ideal for write-sensitive hardware like Flash memory.

## 22. Quiz

**Question 1:** What is the primary advantage of Cycle Sort?
- A) Fastest comparison speed
- B) Minimum number of memory writes ($\le n - 1$)
- C) Best cache locality
- D) Linear time complexity
- **Correct Answer:** B
- **Explanation:** Cycle Sort writes elements to memory at most once per cycle, achieving theoretical minimum memory writes.

**Question 2:** What is the maximum number of memory writes performed by Cycle Sort on an array of length $n$?
- A) $n^2$
- B) $n \log n$
- C) $n - 1$
- D) $2^n$
- **Correct Answer:** C
- **Explanation:** In the worst case, every element is part of a single cycle, taking $n - 1$ write swaps.

**Question 3:** What type of hardware is Cycle Sort specifically suited for?
- A) High-speed RAM
- B) GPU VRAM
- C) EEPROM and Flash memory (where write cycles cause physical wear)
- D) Cloud servers
- **Correct Answer:** C
- **Explanation:** Minimizing memory writes extends the lifespan of write-sensitive Flash/EEPROM storage.

**Question 4:** What is the time complexity of Cycle Sort in all cases?
- A) $\mathcal{O}(n \log n)$
- B) $\mathcal{O}(n)$
- C) $\mathcal{O}(n^2)$
- D) $\mathcal{O}(1)$
- **Correct Answer:** C
- **Explanation:** Counting smaller elements requires scanning the remaining array for each cycle, resulting in $\mathcal{O}(n^2)$ time.

**Question 5:** Is Cycle Sort a stable sorting algorithm?
- A) Yes
- B) No
- **Correct Answer:** B
- **Explanation:** Cycle placement swaps elements across long distances, breaking stability.

**Question 6:** What is the space complexity of Cycle Sort?
- A) $\mathcal{O}(n)$
- B) $\mathcal{O}(1)$
- C) $\mathcal{O}(\log n)$
- D) $\mathcal{O}(n^2)$
- **Correct Answer:** B
- **Explanation:** It operates in-place using only primitive scalar variables.

**Question 7:** How does Cycle Sort determine the correct position `pos` for an element?
- A) By binary search
- B) By counting how many elements in the array are smaller than the item
- C) By hashing
- D) By random guess
- **Correct Answer:** B
- **Explanation:** The number of elements smaller than `item` equals its exact 0-indexed position in a sorted array.

**Question 8:** How does Cycle Sort handle duplicate elements?
- A) It crashes
- B) It skips duplicate indices by incrementing `pos` until a unique slot is found
- C) It converts duplicates to zero
- D) It ignores them
- **Correct Answer:** B
- **Explanation:** `while (item == arr[pos]) pos++` pushes identical items into adjacent open slots.

**Question 9:** Who invented Cycle Sort and in what year?
- A) W. D. Frazer and A. C. McKellar in 1970
- B) Tony Hoare in 1960
- C) Tim Peters in 2002
- D) John von Neumann in 1945
- **Correct Answer:** A
- **Explanation:** W. D. Frazer and A. C. McKellar published Cycle Sort in 1970.

**Question 10:** What happens if an element is already in its correct sorted position?
- A) It is written to memory twice
- B) `pos == cycleStart`, so 0 memory writes are performed for that element
- C) The loop breaks
- D) Memory is freed
- **Correct Answer:** B
- **Explanation:** If `pos == cycleStart`, the item is skipped without performing any memory write.
