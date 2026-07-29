# Comb Sort

## 1. Introduction

Comb Sort is an improvement over Bubble Sort designed by Włodzimierz Dobosiewicz in 1980 and later rediscovered and popularized by Stephen Lacey and Richard Box in 1991. Standard Bubble Sort always compares adjacent elements (gap size of 1). Comb Sort improves this by comparing elements separated by a larger gap, shrinking the gap by a shrink factor (typically 1.3) on each pass until the gap reaches 1.

Imagine combing tangled hair. You start with a wide-toothed comb to remove large knots spread far apart. Once the main tangles are gone, you switch to a fine-toothed comb (gap = 1) to smooth out tiny kinks. That is how Comb Sort works.

It was created to eliminate "turtles"—small values located near the end of an array that drastically slow down standard Bubble Sort.

You should use or study Comb Sort when you want an easy-to-implement in-place sorting algorithm with $\mathcal{O}(1)$ space complexity that performs significantly faster than Bubble Sort ($\mathcal{O}(n \log n)$ average case).

## 2. Why Use This Algorithm?

Comb Sort turns an $\mathcal{O}(n^2)$ algorithm into a fast $\mathcal{O}(n \log n)$ average-case algorithm by changing just a few lines of code.

**Benefits:**
- **Eliminates Turtles:** Large initial gaps quickly move small elements from the end to the beginning.
- **Average-Case Speed:** Achieves $\mathcal{O}(n \log n)$ average-case performance.
- **In-Place Execution:** Requires $\mathcal{O}(1)$ auxiliary memory space.
- **Extremely Simple Implementation:** Minimal modification to standard Bubble Sort code.

**Performance:**
- **Best Case:** $\mathcal{O}(n \log n)$ (or $\mathcal{O}(n)$ with optimized flag)
- **Average Case:** $\mathcal{O}(n \log n)$
- **Worst Case:** $\mathcal{O}(n^2)$
- **Space Complexity:** $\mathcal{O}(1)$ auxiliary memory.

**When it is better than standard Bubble Sort:**
Comb Sort is dramatically faster than standard Bubble Sort and Selection Sort on random arrays of any size, approaching Shell Sort performance with simpler code.

## 3. Real-World Applications

- **Embedded Systems:** Preferred in constrained microcontrollers where memory is limited ($\mathcal{O}(1)$ space) and simple code footprint is mandatory.
- **Educational Benchmarks:** Demonstrating how changing iteration stride/gap transforms algorithm complexity class.

## 4. Prerequisites

Before studying Comb Sort, you should understand:
- Standard [Bubble Sort](./11_bubble_sort.md).
- Floating-point shrink factors ($1.3$ or $1.301378$).
- Iterative gap reduction logic (`gap = floor(gap / 1.3)`).

## 5. Visualization

Given Array: `[8, 4, 1, 56, 3, -44, 23, -6]` ($n = 8$)

1. **Pass 1 (Gap = `floor(8 / 1.3) = 6`):**
   - Compare `arr[0]` (8) & `arr[6]` (23) -> No swap
   - Compare `arr[1]` (4) & `arr[7]` (-6) -> Swap -> `[8, -6, 1, 56, 3, -44, 23, 4]`

2. **Pass 2 (Gap = `floor(6 / 1.3) = 4`):**
   - Compare elements 4 positions apart -> Swaps move small negative numbers left.

3. **Pass 3 (Gap = `floor(4 / 1.3) = 3`):** Gap shrinks further.
4. **Final Pass (Gap = 1):** Standard Bubble Sort pass on nearly sorted array.

## 6. How It Works

1. Initialize `gap = n` and `swapped = true`.
2. Shrink `gap` using the shrink factor 1.3: `gap = floor(gap / 1.3)`.
3. If `gap < 1`, set `gap = 1`.
4. Iterate through the array from `0` to `n - 1 - gap`. Compare `arr[i]` and `arr[i + gap]`.
5. If `arr[i] > arr[i + gap]`, swap them and set `swapped = true`.
6. Repeat until `gap == 1` and `swapped == false`.

## 7. Step-by-Step Algorithm

1. Set `gap = n`, `shrink = 1.3`, `swapped = true`.
2. Loop while `gap > 1` or `swapped == true`:
   1. `gap = floor(gap / 1.3)`.
   2. If `gap < 1`: `gap = 1`.
   3. `swapped = false`.
   4. Loop `i` from `0` to `n - 1 - gap`:
      - If `arr[i] > arr[i + gap]`:
        - Swap `arr[i]` and `arr[i + gap]`.
        - `swapped = true`.
3. Array is sorted.

## 8. Pseudocode

```text
function combSort(arr):
    n = length(arr)
    gap = n
    shrink = 1.3
    swapped = true

    while gap > 1 or swapped:
        gap = floor(gap / shrink)
        if gap < 1:
            gap = 1

        swapped = false

        for i = 0 to n - 1 - gap:
            if arr[i] > arr[i + gap]:
                swap(arr[i], arr[i + gap])
                swapped = true
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdbool.h>

void swap(int* a, int* b) {
    int t = *a; *a = *b; *b = t;
}

void combSort(int arr[], int n) {
    int gap = n;
    bool swapped = true;

    while (gap > 1 || swapped) {
        gap = (int)(gap / 1.3);
        if (gap < 1) gap = 1;

        swapped = false;

        for (int i = 0; i < n - gap; i++) {
            if (arr[i] > arr[i + gap]) {
                swap(&arr[i], &arr[i + gap]);
                swapped = true;
            }
        }
    }
}

int main() {
    int arr[] = {8, 4, 1, 56, 3, -44, 23, -6};
    int n = sizeof(arr) / sizeof(arr[0]);
    combSort(arr, n);
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

void combSort(std::vector<int>& arr) {
    int n = arr.size();
    int gap = n;
    bool swapped = true;

    while (gap > 1 || swapped) {
        gap = static_cast<int>(gap / 1.3);
        if (gap < 1) gap = 1;

        swapped = false;

        for (int i = 0; i < n - gap; ++i) {
            if (arr[i] > arr[i + gap]) {
                std::swap(arr[i], arr[i + gap]);
                swapped = true;
            }
        }
    }
}

int main() {
    std::vector<int> arr = {8, 4, 1, 56, 3, -44, 23, -6};
    combSort(arr);
    for (int x : arr) std::cout << x << " ";
    std::cout << "\n";
    return 0;
}
```

### Java
```java
import java.util.Arrays;

public class CombSort {
    public static void combSort(int[] arr) {
        int n = arr.length;
        int gap = n;
        boolean swapped = true;

        while (gap > 1 || swapped) {
            gap = (int) (gap / 1.3);
            if (gap < 1) gap = 1;

            swapped = false;

            for (int i = 0; i < n - gap; i++) {
                if (arr[i] > arr[i + gap]) {
                    int temp = arr[i];
                    arr[i] = arr[i + gap];
                    arr[i + gap] = temp;
                    swapped = true;
                }
            }
        }
    }

    public static void main(String[] args) {
        int[] arr = {8, 4, 1, 56, 3, -44, 23, -6};
        combSort(arr);
        System.out.println(Arrays.toString(arr));
    }
}
```

### Python
```python
def comb_sort(arr: list[int]) -> list[int]:
    n = len(arr)
    gap = n
    shrink = 1.3
    swapped = True

    while gap > 1 or swapped:
        gap = int(gap / shrink)
        if gap < 1:
            gap = 1

        swapped = False

        for i in range(0, n - gap):
            if arr[i] > arr[i + gap]:
                arr[i], arr[i + gap] = arr[i + gap], arr[i]
                swapped = True

    return arr

if __name__ == "__main__":
    data = [8, 4, 1, 56, 3, -44, 23, -6]
    comb_sort(data)
    print(data)
```

### JavaScript
```javascript
function combSort(arr) {
    const n = arr.length;
    let gap = n;
    const shrink = 1.3;
    let swapped = true;

    while (gap > 1 || swapped) {
        gap = Math.floor(gap / shrink);
        if (gap < 1) gap = 1;

        swapped = false;

        for (let i = 0; i < n - gap; i++) {
            if (arr[i] > arr[i + gap]) {
                [arr[i], arr[i + gap]] = [arr[i + gap], arr[i]];
                swapped = true;
            }
        }
    }

    return arr;
}

const data = [8, 4, 1, 56, 3, -44, 23, -6];
combSort(data);
console.log(data);
```

## 10. Code Explanation

The key innovation of Comb Sort is the shrinking gap parameter. Starting with `gap = n`, each iteration reduces `gap` by dividing by `1.3`. Comparing elements `gap` indices apart eliminates turtles (small values near the end of the array) early on. When `gap` eventually reduces to 1, the algorithm turns into standard Bubble Sort, but runs on an almost completely ordered array, finishing in logarithmic pass iterations.

## 11. Interactive Demo

An interactive slider lets users adjust the shrink factor (1.1 to 1.5). 

- Connectors draw arched lines between element pairs being compared across the current `gap`.
- As the gap shrinks, the arch width collapses from wide arcs down to adjacent bars.
- A pass counter logs how many swaps occurred at each gap stage.

## 12. Dry Run

**Input:** `[8, 4, 1, 56, 3]` ($n = 5$)

| Pass | Gap | Pairs Compared (`arr[i]`, `arr[i+gap]`) | Swaps | Array State |
| :--- | :--- | :--- | :--- | :--- |
| 1 | `floor(5/1.3)=3` | (8,56), (4,3) | (4,3) swapped | `[8, 3, 1, 56, 4]` |
| 2 | `floor(3/1.3)=2` | (8,1), (3,56), (1,4) | (8,1) swapped | `[1, 3, 8, 56, 4]` |
| 3 | `floor(2/1.3)=1` | (1,3), (3,8), (8,56), (56,4) | (56,4) swapped | `[1, 3, 8, 4, 56]` |
| 4 | 1 | (8,4) | (8,4) swapped | `[1, 3, 4, 8, 56]` |

## 13. Time & Space Complexity

| Case | Time Complexity | Reason |
| :--- | :--- | :--- |
| **Best Case** | $\mathcal{O}(n \log n)$ | Fast pass iterations across decreasing gap sizes |
| **Average Case** | $\mathcal{O}(n \log n)$ | Empirically proven efficiency with shrink factor 1.3 |
| **Worst Case** | $\mathcal{O}(n^2)$ | Pathological gap sequence choices |
| **Space Complexity** | $\mathcal{O}(1)$ | In-place execution |

## 14. Advantages

- **Significantly Faster Than Bubble Sort:** Reduces Bubble Sort $\mathcal{O}(n^2)$ average time to $\mathcal{O}(n \log n)$.
- **In-Place Execution:** Requires $\mathcal{O}(1)$ memory.
- **Easy Implementation:** Requires only replacing `i + 1` with `i + gap` in Bubble Sort.

## 15. Disadvantages

- **Unstable:** Long-distance swaps break the relative order of duplicate items.
- **Worst-Case Risk:** Poor shrink factor selection can degrade to $\mathcal{O}(n^2)$.

## 16. Applications

- Low-memory microcontrollers requiring lightweight code.
- Replacing Bubble Sort in legacy systems for instant performance wins.

## 17. Common Mistakes

- **Incorrect Shrink Factor:** Using integers like `2` instead of floating point `1.3` (causes gaps to shrink too fast, missing swaps).
- **Stopping Early:** Exiting when `gap == 1` before a full zero-swap pass completes.

## 18. Interview Questions

1. What shrink factor value was empirically found to be optimal for Comb Sort?
2. Is Comb Sort stable or unstable? Why?
3. How does Comb Sort differ from Shell Sort?
4. What is the space complexity of Comb Sort?

## 19. Practice Problems

**Easy:**
1. Implement Comb Sort with shrink factor `1.3`.
2. Count total gap reduction steps for array of size $N$.

**Medium:**
3. Implement Comb Sort with the "Rule of 11" optimization (forcing gap values of 9 and 10 to be replaced by 11).

**Hard:**
4. Compare empirical runtimes of Comb Sort vs Shell Sort on random datasets from $10^3$ to $10^6$ elements.

## 20. Related Algorithms

- [Bubble Sort](./11_bubble_sort.md) (Foundation with gap = 1)
- [Shell Sort](./17_shell_sort.md) (Gap-based optimization over Insertion Sort)
- [Cocktail Shaker Sort](./23_cocktail_shaker_sort.md) (Bidirectional Bubble Sort)

## 21. Summary

Comb Sort optimizes standard Bubble Sort by comparing elements across a shrinking gap (starting at array length and dividing by 1.3 each pass). By eliminating small elements at the end ("turtles") early, it dramatically improves average-case time complexity to $\mathcal{O}(n \log n)$ while retaining $\mathcal{O}(1)$ auxiliary space.

## 22. Quiz

**Question 1:** Who popularized Comb Sort in 1991?
- A) Stephen Lacey and Richard Box
- B) Tim Peters
- C) Tony Hoare
- D) Donald Knuth
- **Correct Answer:** A
- **Explanation:** Stephen Lacey and Richard Box published Comb Sort in Byte magazine in 1991.

**Question 2:** What is the ideal empirical shrink factor for Comb Sort?
- A) 2.0
- B) 1.3
- C) 0.5
- D) 10.0
- **Correct Answer:** B
- **Explanation:** Authors proved $1.3$ (specifically $1 / (1 - e^{-\varphi}) \approx 1.2473309$) provides optimal gap reduction.

**Question 3:** What is the average-case time complexity of Comb Sort?
- A) $\mathcal{O}(n^2)$
- B) $\mathcal{O}(n \log n)$
- C) $\mathcal{O}(n)$
- D) $\mathcal{O}(1)$
- **Correct Answer:** B
- **Explanation:** The shrinking gap reduces comparisons to $\mathcal{O}(n \log n)$ on average.

**Question 4:** Is Comb Sort a stable sorting algorithm?
- A) Yes
- B) No
- **Correct Answer:** B
- **Explanation:** Large gap swaps change the relative order of identical elements.

**Question 5:** What is the auxiliary space complexity of Comb Sort?
- A) $\mathcal{O}(n)$
- B) $\mathcal{O}(1)$
- C) $\mathcal{O}(\log n)$
- D) $\mathcal{O}(n^2)$
- **Correct Answer:** B
- **Explanation:** It sorts in-place using only a few primitive integer and float variables.

**Question 6:** What algorithm does Comb Sort become when `gap == 1`?
- A) Quick Sort
- B) Bubble Sort
- C) Merge Sort
- D) Heap Sort
- **Correct Answer:** B
- **Explanation:** When `gap` reaches 1, `arr[i]` is compared to `arr[i + 1]`, which is standard Bubble Sort.

**Question 7:** What problem in Bubble Sort does Comb Sort solve?
- A) Over-recursion
- B) "Turtles" (small elements near the end of the array)
- C) High space complexity
- D) Floating point rounding
- **Correct Answer:** B
- **Explanation:** Large initial gaps quickly shift small values at the end toward the front.

**Question 8:** What is the "Combsort11" optimization rule?
- A) Always use 11 buckets
- B) If the computed gap is 9 or 10, force it to 11
- C) Limit array length to 11
- D) Run 11 passes maximum
- **Correct Answer:** B
- **Explanation:** Forcing gap to 11 when it would be 9 or 10 eliminates specific slow gap sequences.

**Question 9:** How does Comb Sort compare to Shell Sort?
- A) Comb Sort is based on Bubble Sort, while Shell Sort is based on Insertion Sort
- B) Comb Sort requires $\mathcal{O}(n)$ memory
- C) Shell Sort does not use gaps
- D) They are identical algorithms
- **Correct Answer:** A
- **Explanation:** Comb Sort uses gap-swapping on Bubble Sort, while Shell Sort uses gap-insertions on Insertion Sort.

**Question 10:** What happens if the shrink factor is set to 1.0?
- A) The algorithm runs in $\mathcal{O}(n)$ time
- B) The gap stays equal to $n$, causing an infinite loop
- C) It converts to Merge Sort
- D) It sorts instantly
- **Correct Answer:** B
- **Explanation:** Dividing `gap` by 1.0 never reduces the gap size, causing an infinite loop.
