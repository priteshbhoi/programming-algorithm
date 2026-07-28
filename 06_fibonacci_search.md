# Fibonacci Search

## 1. Introduction

Fibonacci Search is a comparison-based searching algorithm that uses Fibonacci numbers to divide the array into unequal parts. Instead of splitting the array exactly in half like Binary Search, it splits it into two parts whose sizes are consecutive Fibonacci numbers. This approach minimizes the number of comparisons on average and works particularly well when the cost of comparison is significantly higher than the cost of access.

It was created as an alternative to Binary Search that avoids the division operation needed to calculate the midpoint. In systems where division is expensive, using addition and subtraction with Fibonacci numbers can be more efficient. It also has a unique property of examining elements closer to the beginning first, which can be advantageous when the target is likely near the front.

You should use Fibonacci Search on sorted arrays when comparison operations are costly, or when you want an algorithm that naturally favors the left side of the array.

## 2. Why Use This Algorithm?

Fibonacci Search offers a clever way to avoid division while still achieving logarithmic performance. It relies on the golden ratio properties of Fibonacci numbers to create a search pattern.

**Benefits:**
- No division required, only addition and subtraction
- Examines elements toward the left side first
- Logarithmic time complexity
- Works well when comparison is expensive
- Elegant mathematical foundation

**Performance:**
The time complexity is O(log n), similar to Binary Search. The number of comparisons is often slightly better than Binary Search in the average case because the unequal split can eliminate more elements with fewer checks.

**When it is better than other algorithms:**
Fibonacci Search shines when division operations are slow (such as on certain embedded processors) or when the target is statistically more likely to appear in the left portion of the array.

## 3. Real-World Applications

- **Embedded systems with slow division:** Microcontrollers where integer division takes many clock cycles.
- **Searching in comparison-heavy data:** Comparing large objects or strings where each comparison is expensive.
- **Magnetic tape storage:** Sequential access media where moving backward is costly; Fibonacci Search prefers forward checks.
- **Historical computing systems:** Early computers where division was a major bottleneck.
- **Teaching algorithm design:** Excellent for demonstrating how number sequences can guide search strategies.

## 4. Prerequisites

Before learning Fibonacci Search, you should know:
- Binary Search thoroughly
- What Fibonacci numbers are and how to generate them
- Arrays and indexing
- Basic loop and conditional logic
- Why sorted data is necessary for comparison-based searches

## 5. Visualization

Imagine a sorted row of blocks. Instead of placing a marker exactly in the middle, you use Fibonacci numbers to decide where to look. If the array has a Fibonacci number of elements, you divide it so the larger portion is on the left. You check the element that separates these portions. If the target is smaller, you focus on the larger left portion. If larger, you focus on the smaller right portion. You then find the next Fibonacci number down and repeat. The search pattern creates a spiral-like narrowing that follows the golden ratio.

## 6. How It Works

Fibonacci Search generates Fibonacci numbers until it finds the smallest Fibonacci number greater than or equal to the array size. It then uses the property that any Fibonacci number is the sum of the two preceding ones to divide the array. The algorithm maintains three Fibonacci numbers: `fibM` (the current), `fibM1` (the previous), and `fibM2` (the one before that). The index to check is calculated as `offset + fibM2`. Based on the comparison, the offset moves and the Fibonacci numbers shift down the sequence.

## 7. Step-by-Step Algorithm

1. Find the smallest Fibonacci number `fibM` that is greater than or equal to the array length.
2. Initialize `fibM1` as the Fibonacci number before `fibM`, and `fibM2` as the one before `fibM1`.
3. Initialize `offset` to -1.
4. While `fibM` > 1:
   1. Calculate `i` as the minimum of `offset + fibM2` and `n - 1`.
   2. If `array[i]` < target, move to the right half: set `fibM = fibM1`, `fibM1 = fibM2`, `fibM2 = fibM - fibM1`, and `offset = i`.
   3. If `array[i]` > target, move to the left half: set `fibM = fibM2`, `fibM1 = fibM1 - fibM2`, `fibM2 = fibM - fibM1`.
   4. If `array[i]` == target, return `i`.
5. If `fibM1` equals 1 and `array[offset + 1]` equals target, return `offset + 1`.
6. Return -1 (not found).

## 8. Pseudocode

```
function fibonacciSearch(array, target):
    fibM2 = 0
    fibM1 = 1
    fibM = fibM2 + fibM1
    while fibM < length(array):
        fibM2 = fibM1
        fibM1 = fibM
        fibM = fibM2 + fibM1
    offset = -1
    while fibM > 1:
        i = minimum(offset + fibM2, length(array) - 1)
        if array[i] < target:
            fibM = fibM1
            fibM1 = fibM2
            fibM2 = fibM - fibM1
            offset = i
        else if array[i] > target:
            fibM = fibM2
            fibM1 = fibM1 - fibM2
            fibM2 = fibM - fibM1
        else:
            return i
    if fibM1 == 1 and array[offset + 1] == target:
        return offset + 1
    return -1
```

## 9. Code Examples

### C
```c
#include <stdio.h>

int min(int a, int b) {
    return (a < b) ? a : b;
}

int fibonacciSearch(int arr[], int n, int target) {
    int fibM2 = 0;
    int fibM1 = 1;
    int fibM = fibM2 + fibM1;
    while (fibM < n) {
        fibM2 = fibM1;
        fibM1 = fibM;
        fibM = fibM2 + fibM1;
    }
    int offset = -1;
    while (fibM > 1) {
        int i = min(offset + fibM2, n - 1);
        if (arr[i] < target) {
            fibM = fibM1;
            fibM1 = fibM2;
            fibM2 = fibM - fibM1;
            offset = i;
        } else if (arr[i] > target) {
            fibM = fibM2;
            fibM1 = fibM1 - fibM2;
            fibM2 = fibM - fibM1;
        } else {
            return i;
        }
    }
    if (fibM1 == 1 && offset + 1 < n && arr[offset + 1] == target)
        return offset + 1;
    return -1;
}

int main() {
    int arr[] = {10, 22, 35, 40, 45, 50, 80, 82, 85, 90, 100};
    int n = sizeof(arr) / sizeof(arr[0]);
    int target = 85;
    int result = fibonacciSearch(arr, n, target);
    if (result != -1)
        printf("Element found at index %d\n", result);
    else
        printf("Element not found\n");
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int fibonacciSearch(const vector<int>& arr, int target) {
    int n = arr.size();
    int fibM2 = 0;
    int fibM1 = 1;
    int fibM = fibM2 + fibM1;
    while (fibM < n) {
        fibM2 = fibM1;
        fibM1 = fibM;
        fibM = fibM2 + fibM1;
    }
    int offset = -1;
    while (fibM > 1) {
        int i = min(offset + fibM2, n - 1);
        if (arr[i] < target) {
            fibM = fibM1;
            fibM1 = fibM2;
            fibM2 = fibM - fibM1;
            offset = i;
        } else if (arr[i] > target) {
            fibM = fibM2;
            fibM1 = fibM1 - fibM2;
            fibM2 = fibM - fibM1;
        } else {
            return i;
        }
    }
    if (fibM1 == 1 && offset + 1 < n && arr[offset + 1] == target)
        return offset + 1;
    return -1;
}

int main() {
    vector<int> arr = {10, 22, 35, 40, 45, 50, 80, 82, 85, 90, 100};
    int target = 85;
    int result = fibonacciSearch(arr, target);
    if (result != -1)
        cout << "Element found at index " << result << endl;
    else
        cout << "Element not found" << endl;
    return 0;
}
```

### Java
```java
public class FibonacciSearch {
    public static int fibonacciSearch(int[] arr, int target) {
        int n = arr.length;
        int fibM2 = 0;
        int fibM1 = 1;
        int fibM = fibM2 + fibM1;
        while (fibM < n) {
            fibM2 = fibM1;
            fibM1 = fibM;
            fibM = fibM2 + fibM1;
        }
        int offset = -1;
        while (fibM > 1) {
            int i = Math.min(offset + fibM2, n - 1);
            if (arr[i] < target) {
                fibM = fibM1;
                fibM1 = fibM2;
                fibM2 = fibM - fibM1;
                offset = i;
            } else if (arr[i] > target) {
                fibM = fibM2;
                fibM1 = fibM1 - fibM2;
                fibM2 = fibM - fibM1;
            } else {
                return i;
            }
        }
        if (fibM1 == 1 && offset + 1 < n && arr[offset + 1] == target)
            return offset + 1;
        return -1;
    }

    public static void main(String[] args) {
        int[] arr = {10, 22, 35, 40, 45, 50, 80, 82, 85, 90, 100};
        int target = 85;
        int result = fibonacciSearch(arr, target);
        if (result != -1)
            System.out.println("Element found at index " + result);
        else
            System.out.println("Element not found");
    }
}
```

### Python
```python
def fibonacci_search(arr, target):
    n = len(arr)
    fibM2 = 0
    fibM1 = 1
    fibM = fibM2 + fibM1
    while fibM < n:
        fibM2 = fibM1
        fibM1 = fibM
        fibM = fibM2 + fibM1
    offset = -1
    while fibM > 1:
        i = min(offset + fibM2, n - 1)
        if arr[i] < target:
            fibM = fibM1
            fibM1 = fibM2
            fibM2 = fibM - fibM1
            offset = i
        elif arr[i] > target:
            fibM = fibM2
            fibM1 = fibM1 - fibM2
            fibM2 = fibM - fibM1
        else:
            return i
    if fibM1 == 1 and offset + 1 < n and arr[offset + 1] == target:
        return offset + 1
    return -1

arr = [10, 22, 35, 40, 45, 50, 80, 82, 85, 90, 100]
target = 85
result = fibonacci_search(arr, target)
if result != -1:
    print(f"Element found at index {result}")
else:
    print("Element not found")
```

### JavaScript
```javascript
function fibonacciSearch(arr, target) {
    const n = arr.length;
    let fibM2 = 0;
    let fibM1 = 1;
    let fibM = fibM2 + fibM1;
    while (fibM < n) {
        fibM2 = fibM1;
        fibM1 = fibM;
        fibM = fibM2 + fibM1;
    }
    let offset = -1;
    while (fibM > 1) {
        const i = Math.min(offset + fibM2, n - 1);
        if (arr[i] < target) {
            fibM = fibM1;
            fibM1 = fibM2;
            fibM2 = fibM - fibM1;
            offset = i;
        } else if (arr[i] > target) {
            fibM = fibM2;
            fibM1 = fibM1 - fibM2;
            fibM2 = fibM - fibM1;
        } else {
            return i;
        }
    }
    if (fibM1 === 1 && offset + 1 < n && arr[offset + 1] === target)
        return offset + 1;
    return -1;
}

const arr = [10, 22, 35, 40, 45, 50, 80, 82, 85, 90, 100];
const target = 85;
const result = fibonacciSearch(arr, target);
if (result !== -1) {
    console.log(`Element found at index ${result}`);
} else {
    console.log("Element not found");
}
```

## 10. Code Explanation

The algorithm begins by generating Fibonacci numbers until it exceeds the array size. These three consecutive Fibonacci numbers (`fibM`, `fibM1`, `fibM2`) define the split points. The `offset` variable tracks where the remaining search space begins. The index to check is always `offset + fibM2`, which corresponds to approximately the golden ratio split of the remaining range. When the target is larger, the offset moves forward and the Fibonacci sequence shifts down. When smaller, the range shrinks from the right. The final check handles the edge case where only one element remains.

## 11. Interactive Demo

The demo displays a sorted row of blocks. A panel shows the current Fibonacci triplet (`fibM`, `fibM1`, `fibM2`). A "Probe" highlights the block at `offset + fibM2`. If the target is larger, the blocks before the probe (including it) slide away and the offset updates. The Fibonacci numbers cascade down. If smaller, the right portion shrinks and the Fibonacci numbers recalculate. A golden ratio spiral overlay visually connects the split sizes. The status panel tracks the offset, the probe index, and the remaining range size.

## 12. Dry Run

**Sample Input:**
Array: `[10, 22, 35, 40, 45, 50, 80, 82, 85, 90, 100]`
Target: `85`

| Step | fibM | fibM1 | fibM2 | Offset | i | arr[i] | Comparison | Action |
|------|------|-------|-------|--------|---|--------|------------|--------|
| 1 | 13 | 8 | 5 | -1 | 4 | 45 | 45 < 85 | Move right, offset=4 |
| 2 | 8 | 5 | 3 | 4 | 7 | 82 | 82 < 85 | Move right, offset=7 |
| 3 | 5 | 3 | 2 | 7 | 9 | 90 | 90 > 85 | Move left |
| 4 | 3 | 2 | 1 | 7 | 8 | 85 | 85 == 85 | Return 8 |

**Final Output:** Index `8`

## 13. Time & Space Complexity

| Case | Time Complexity | Reason |
|------|-----------------|--------|
| Best Case | O(1) | The first probe lands exactly on the target |
| Average Case | O(log n) | Fibonacci numbers grow exponentially, so the number of steps is logarithmic |
| Worst Case | O(log n) | The target is at the end or not present |
| Space Complexity | O(1) | Only three Fibonacci numbers and an offset are stored |

## 14. Advantages

- **No division required:** Uses only addition and subtraction, which is faster on some hardware.
- **Favors the left side:** The unequal split checks the left portion first, which is beneficial if targets are statistically near the front.
- **Logarithmic performance:** Matches Binary Search's time complexity.
- **Mathematically elegant:** Demonstrates a beautiful application of the Fibonacci sequence.
- **Good for comparison-heavy data:** Slightly fewer comparisons on average than Binary Search.

## 15. Disadvantages

- **More complex to implement:** Tracking three Fibonacci numbers and an offset is harder than Binary Search's two pointers.
- **Requires sorted data:** Like all comparison-based array searches.
- **Not cache-optimal:** The unequal split can lead to less predictable memory access patterns.
- **Slightly more overhead:** The initial Fibonacci generation loop adds a small constant cost.

## 16. Applications

- Searching in embedded systems where division is expensive
- Looking up values in comparison-heavy sorted datasets
- Educational demonstrations of the Fibonacci sequence in algorithms
- Searching in systems where the target distribution skews toward lower values
- Historical computing environments where hardware division was slow

## 17. Common Mistakes

- **Incorrect Fibonacci initialization:** Starting with wrong seed values (0 and 1) is crucial; starting with 1 and 1 changes the sequence.
- **Forgetting the offset update:** When moving right, the offset must be set to the current index.
- **Missing the final single-element check:** The last `if (fibM1 == 1)` check catches the case where one element remains after the main loop.
- **Bounds errors in `min()`:** Always use `min(offset + fibM2, n - 1)` to prevent reading past the array end.

## 18. Interview Questions

1. What is the key difference between Binary Search and Fibonacci Search?
2. Why does Fibonacci Search avoid division operations?
3. What is the time complexity of Fibonacci Search?
4. How are the three Fibonacci numbers updated when the target is greater than the current element?
5. How are they updated when the target is smaller?
6. Why is Fibonacci Search said to favor the left side of the array?
7. Can Fibonacci Search be used on a linked list? Explain.
8. What is the purpose of the `offset` variable?
9. How would you modify Fibonacci Search to find the first occurrence of a duplicate?
10. Compare the number of comparisons between Fibonacci Search and Binary Search for a uniformly distributed target.

## 19. Practice Problems

**Easy:**
1. Implement Fibonacci Search on a sorted array of integers.
2. Trace through Fibonacci Search manually for a small array and target.
3. Modify Fibonacci Search to return the insertion point if the target is not found.
4. Implement Fibonacci Search on a sorted array of strings.

**Medium:**
5. Find the first occurrence of a duplicate target using Fibonacci Search.
6. Compare the actual number of comparisons between Binary Search and Fibonacci Search on the same dataset.
7. Implement Fibonacci Search where the Fibonacci numbers are precomputed and passed as parameters.
8. Search for a target in a rotated sorted array using Fibonacci Search.

**Hard:**
9. Optimize Fibonacci Search for a specific hardware platform where addition is much faster than division.
10. Create a generalized version using any sequence that follows a recurrence relation.
11. Apply Fibonacci Search principles to a 2D sorted matrix.

## 20. Related Algorithms

- Binary Search (divides equally)
- Jump Search (fixed-size skipping)
- Exponential Search (power-of-2 doubling)
- Interpolation Search (value-based probing)
- Golden Section Search (uses the golden ratio for optimization)

## 21. Summary

Fibonacci Search is a sophisticated alternative to Binary Search that uses Fibonacci numbers to create unequal splits. By avoiding division and favoring the left side of the array, it offers practical advantages on certain hardware and data distributions. Its O(log n) time complexity and O(1) space usage make it efficient, while its mathematical elegance makes it a favorite in algorithm courses. Mastering Fibonacci Search deepens your understanding of how number theory can guide search strategies.

## 22. Quiz

**Question 1:** What sequence of numbers does Fibonacci Search use?
- A) Prime numbers
- B) Fibonacci numbers
- C) Powers of 2
- D) Factorials
- **Correct Answer:** B
- **Explanation:** The algorithm is named after and fundamentally relies on the Fibonacci sequence.

**Question 2:** What is the time complexity of Fibonacci Search?
- A) O(n)
- B) O(log n)
- C) O(sqrt(n))
- D) O(1)
- **Correct Answer:** B
- **Explanation:** Like Binary Search, it eliminates a significant portion of the search space each iteration.

**Question 3:** Which arithmetic operation does Fibonacci Search avoid?
- A) Addition
- B) Subtraction
- C) Division
- D) Multiplication
- **Correct Answer:** C
- **Explanation:** It uses addition and subtraction of Fibonacci numbers instead of division to find split points.

**Question 4:** What is the space complexity?
- A) O(log n)
- B) O(1)
- C) O(n)
- D) O(Fibonacci(n))
- **Correct Answer:** B
- **Explanation:** Only a constant number of variables (three Fibonacci numbers and an offset) are used.

**Question 5:** Fibonacci Search typically examines which side first?
- A) Right side
- B) Left side
- C) Middle
- D) Random side
- **Correct Answer:** B
- **Explanation:** The unequal split created by Fibonacci numbers makes the left portion larger, so it is checked first.

**Question 6:** What happens if `arr[i] < target` during the search?
- A) The right half is discarded
- B) The offset moves to i and Fibonacci numbers shift down
- C) The array is sorted again
- D) The search terminates
- **Correct Answer:** B
- **Explanation:** The offset updates to the current position, and the Fibonacci triplet shifts to the next lower set.

**Question 7:** Why is the `min()` function used when calculating index i?
- A) To find the smaller value
- B) To prevent index out of bounds
- C) To speed up the search
- D) To compare with the target
- **Correct Answer:** B
- **Explanation:** It ensures we do not access indices beyond the array length.

**Question 8:** Which property of Fibonacci numbers makes this search possible?
- A) They are all even
- B) Each number is the sum of the two preceding ones
- C) They are prime
- D) They decrease over time
- **Correct Answer:** B
- **Explanation:** This recurrence relation allows the algorithm to shrink the search space by subtracting the appropriate Fibonacci number.

**Question 9:** In what type of system is Fibonacci Search particularly advantageous?
- A) Systems with fast division
- B) Embedded systems with slow division
- C) Systems with infinite memory
- D) Unsorted data systems
- **Correct Answer:** B
- **Explanation:** Avoiding division is beneficial when division operations are computationally expensive.

**Question 10:** What is the final check after the main loop for?
- A) To sort the array
- B) To check the last remaining element
- C) To reset the offset
- D) To generate more Fibonacci numbers
- **Correct Answer:** B
- **Explanation:** When fibM1 equals 1, there may be one last element at offset + 1 that needs to be checked.
