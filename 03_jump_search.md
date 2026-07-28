# Jump Search

## 1. Introduction

Jump Search is a searching algorithm designed for sorted arrays that strikes a balance between Linear Search and Binary Search. Instead of checking every element or dividing the array in half, it jumps ahead by fixed steps. Imagine reading a book and looking for a chapter. Rather than turning every single page, you flip forward several pages at a time until you pass the chapter, then you go back and search linearly through those few pages.

It was created to reduce the number of comparisons made by Linear Search while avoiding the complex midpoint calculations and boundary management of Binary Search. It is particularly useful when jumping backward is expensive or when the cost of comparison is high relative to the cost of indexing.

You should use Jump Search on sorted arrays when you want a simpler implementation than Binary Search but better performance than Linear Search, especially if backward jumps are costly.

## 2. Why Use This Algorithm?

Jump Search offers a middle ground. It does not require the careful index arithmetic that Binary Search demands, yet it skips over large chunks of irrelevant data.

**Benefits:**
- Simpler to implement than Binary Search
- Fewer comparisons than Linear Search
- Works well when jumping forward is cheap but backward jumps are expensive
- No risk of integer overflow in midpoint calculations

**Performance:**
The optimal jump size is the square root of n. This gives a time complexity of O(sqrt(n)), which is better than Linear Search but worse than Binary Search.

**When it is better than other algorithms:**
Jump Search can outperform Binary Search in systems where jumping backward is significantly more expensive than jumping forward, such as certain types of storage media or streaming data.

## 3. Real-World Applications

- **Searching in append-only logs:** Systems that write sequentially and rarely read backward can jump forward through log segments.
- **Database block searching:** Some storage engines jump between disk blocks and then scan within a block.
- **Finding timestamps in sorted event streams:** In IoT data pipelines, events arrive sorted by time, and jumping by time intervals is efficient.
- **Searching in read-only memory structures:** ROM-based lookup tables where forward scanning is hardware-optimized.
- **Phone book searching:** Humans naturally jump by sections (A, B, C...) before scanning linearly within a section.

## 4. Prerequisites

Before learning Jump Search, you should understand:
- Arrays and indexing
- Loop constructs
- The square root function and its purpose
- Why sorted data is required for skipping ahead

## 5. Visualization

Picture a sorted staircase of blocks. Instead of climbing one step at a time, you take big leaps of equal size. After each leap, you check the block you landed on. If that block's value is greater than your target, you know the target must be somewhere between your previous position and your current position. You then walk back one step at a time until you find it or confirm it is missing.

## 6. How It Works

Jump Search divides the array into blocks of size m (typically sqrt(n)). It examines the last element of each block. If that element is less than the target, it jumps to the next block. If it is greater than or equal to the target, it performs a Linear Search within that block. The process continues until the target is found or all blocks are exhausted.

## 7. Step-by-Step Algorithm

1. Calculate the jump step size as the square root of the array length.
2. Start at the first element (index 0).
3. Jump forward by the step size and compare the element at that position with the target.
4. If the current element is less than the target, continue jumping forward.
5. If the current element is greater than or equal to the target, the target must lie within the previous block and the current position.
6. Perform a Linear Search backward from the current position to the start of the block.
7. If found, return the index. If the end of the array is reached without finding a block boundary that satisfies step 5, return -1.

## 8. Pseudocode

```
function jumpSearch(array, target):
    n = length(array)
    step = floor(squareRoot(n))
    prev = 0
    while array[min(step, n) - 1] < target:
        prev = step
        step = step + floor(squareRoot(n))
        if prev >= n:
            return -1
    while array[prev] < target:
        prev = prev + 1
        if prev == min(step, n):
            return -1
    if array[prev] == target:
        return prev
    return -1
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <math.h>

int jumpSearch(int arr[], int n, int target) {
    int step = (int)sqrt(n);
    int prev = 0;
    while (arr[(step < n ? step : n) - 1] < target) {
        prev = step;
        step += (int)sqrt(n);
        if (prev >= n)
            return -1;
    }
    while (arr[prev] < target) {
        prev++;
        if (prev == (step < n ? step : n))
            return -1;
    }
    if (arr[prev] == target)
        return prev;
    return -1;
}

int main() {
    int arr[] = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15};
    int n = sizeof(arr) / sizeof(arr[0]);
    int target = 9;
    int result = jumpSearch(arr, n, target);
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
#include <cmath>
using namespace std;

int jumpSearch(const vector<int>& arr, int target) {
    int n = arr.size();
    int step = sqrt(n);
    int prev = 0;
    while (arr[min(step, n) - 1] < target) {
        prev = step;
        step += sqrt(n);
        if (prev >= n)
            return -1;
    }
    while (arr[prev] < target) {
        prev++;
        if (prev == min(step, n))
            return -1;
    }
    if (arr[prev] == target)
        return prev;
    return -1;
}

int main() {
    vector<int> arr = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15};
    int target = 9;
    int result = jumpSearch(arr, target);
    if (result != -1)
        cout << "Element found at index " << result << endl;
    else
        cout << "Element not found" << endl;
    return 0;
}
```

### Java
```java
public class JumpSearch {
    public static int jumpSearch(int[] arr, int target) {
        int n = arr.length;
        int step = (int) Math.sqrt(n);
        int prev = 0;
        while (arr[Math.min(step, n) - 1] < target) {
            prev = step;
            step += (int) Math.sqrt(n);
            if (prev >= n)
                return -1;
        }
        while (arr[prev] < target) {
            prev++;
            if (prev == Math.min(step, n))
                return -1;
        }
        if (arr[prev] == target)
            return prev;
        return -1;
    }

    public static void main(String[] args) {
        int[] arr = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15};
        int target = 9;
        int result = jumpSearch(arr, target);
        if (result != -1)
            System.out.println("Element found at index " + result);
        else
            System.out.println("Element not found");
    }
}
```

### Python
```python
import math

def jump_search(arr, target):
    n = len(arr)
    step = int(math.sqrt(n))
    prev = 0
    while arr[min(step, n) - 1] < target:
        prev = step
        step += int(math.sqrt(n))
        if prev >= n:
            return -1
    while arr[prev] < target:
        prev += 1
        if prev == min(step, n):
            return -1
    if arr[prev] == target:
        return prev
    return -1

arr = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15]
target = 9
result = jump_search(arr, target)
if result != -1:
    print(f"Element found at index {result}")
else:
    print("Element not found")
```

### JavaScript
```javascript
function jumpSearch(arr, target) {
    const n = arr.length;
    let step = Math.floor(Math.sqrt(n));
    let prev = 0;
    while (arr[Math.min(step, n) - 1] < target) {
        prev = step;
        step += Math.floor(Math.sqrt(n));
        if (prev >= n) return -1;
    }
    while (arr[prev] < target) {
        prev++;
        if (prev === Math.min(step, n)) return -1;
    }
    if (arr[prev] === target) return prev;
    return -1;
}

const arr = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15];
const target = 9;
const result = jumpSearch(arr, target);
if (result !== -1) {
    console.log(`Element found at index ${result}`);
} else {
    console.log("Element not found");
}
```

## 10. Code Explanation

The algorithm has two phases. The first phase uses a while loop to leap forward by `sqrt(n)` positions. The condition `arr[min(step, n) - 1] < target` ensures we do not read past the array end. Once we land in a block where the boundary element is greater than or equal to the target, the second phase scans linearly from `prev` to the block boundary. The `min(step, n)` guard is critical for the final block where the step might exceed the array length.

## 11. Interactive Demo

The demo shows a sorted row of blocks grouped visually into segments of size sqrt(n). A "Jumper" icon leaps from the end of one segment to the end of the next. Each landing block highlights in blue. When the jumper lands on a value greater than or equal to the target, the current segment is highlighted in yellow, and a "Scanner" moves linearly through that segment one block at a time. A counter tracks the number of jumps and the number of linear scans. The user can input a target and click "Search" to watch the two-phase process.

## 12. Dry Run

**Sample Input:**
Array: `[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15]`
Target: `9`

| Phase | Step | Prev | Current Index | Value | Action |
|-------|------|------|---------------|-------|--------|
| Jump | 1 | 0 | 3 | 3 | 3 < 9, jump forward |
| Jump | 2 | 4 | 7 | 7 | 7 < 9, jump forward |
| Jump | 3 | 8 | 11 | 11 | 11 >= 9, stop jumping |
| Linear | 4 | 8 | 8 | 8 | 8 < 9, continue |
| Linear | 5 | 9 | 9 | 9 | 9 == 9, found |

**Final Output:** Index `9`

## 13. Time & Space Complexity

| Case | Time Complexity | Reason |
|------|-----------------|--------|
| Best Case | O(1) | Target is the first checked boundary element |
| Average Case | O(sqrt(n)) | sqrt(n) jumps plus sqrt(n) linear scans |
| Worst Case | O(sqrt(n)) | Target is at the end of the last block or absent |
| Space Complexity | O(1) | Only step, prev, and loop counters are stored |

## 14. Advantages

- **Simpler than Binary Search:** No complex midpoint overflow issues or recursive thinking.
- **Forward-only traversal:** Ideal for data structures where backward movement is expensive.
- **Better than Linear Search:** Skips irrelevant data in chunks.
- **Cache-friendly blocks:** The linear scan at the end operates on a small contiguous block.

## 15. Disadvantages

- **Requires sorted data:** Like Binary Search, the array must be ordered.
- **Slower than Binary Search:** O(sqrt(n)) is asymptotically worse than O(log n).
- **Optimal block size depends on n:** The sqrt(n) assumption works well in theory but may need tuning in practice.
- **Not ideal for linked lists:** Finding the k-th node is already O(k), compounding the cost.

## 16. Applications

- Searching sorted append-only logs where forward seeking is fast
- Block-level searching in storage systems
- Index jumping in large sorted CSV files
- Finding entries in sorted read-only lookup tables burned into firmware
- Preliminary filtering in database query planners before finer-grained searches

## 17. Common Mistakes

- **Using a fixed step size regardless of n:** The step size should scale with sqrt(n) for optimal performance.
- **Forgetting the array bounds check:** Always use `min(step, n)` to avoid index out of range errors.
- **Applying to unsorted data:** Jump Search assumes ordering to know when to stop jumping.
- **Incorrect linear scan range:** The linear scan must start from `prev`, not from the beginning of the array.

## 18. Interview Questions

1. What is the optimal jump size in Jump Search and why?
2. Compare the number of comparisons in Jump Search versus Binary Search for large n.
3. Can Jump Search be used on a linked list? Explain.
4. What is the time complexity of Jump Search and how is it derived?
5. How would you modify Jump Search if comparisons are much more expensive than index jumps?
6. In what scenario might Jump Search outperform Binary Search in practice?
7. What happens if the jump size is set to n/2 instead of sqrt(n)?
8. How does Jump Search behave when the target is smaller than the first element?
9. Can you implement Jump Search recursively?
10. How would you adapt Jump Search to find the insertion point for a new element?

## 19. Practice Problems

**Easy:**
1. Implement Jump Search on a sorted array of integers.
2. Modify Jump Search to return the index where the target should be inserted if not found.
3. Implement Jump Search on a sorted array of strings.
4. Count the number of jumps made during a Jump Search.

**Medium:**
5. Find the first occurrence of a duplicate target using Jump Search followed by backward scanning.
6. Adapt Jump Search to work on a rotated sorted array.
7. Implement a variant where the jump size doubles each time (like Exponential Search) and then scan linearly backward.
8. Search for a target in a 2D sorted matrix using Jump Search on rows.

**Hard:**
9. Optimize Jump Search for a cache-conscious environment where block sizes should match cache lines.
10. Combine Jump Search with Interpolation Search to determine dynamic jump sizes based on value distribution.
11. Implement Jump Search on a disk-backed array where each jump corresponds to a disk block read.

## 20. Related Algorithms

- Linear Search (simpler, slower)
- Binary Search (faster, more complex)
- Interpolation Search (better for uniformly distributed data)
- Exponential Search (unbounded search variant)
- Block Search (generalization of Jump Search)

## 21. Summary

Jump Search is a practical compromise between the simplicity of Linear Search and the efficiency of Binary Search. By jumping ahead in blocks of size sqrt(n) and then scanning linearly within the identified block, it achieves O(sqrt(n)) time complexity. It is especially valuable when forward traversal is preferred over backward jumps. Remember to keep your data sorted and your jump size proportional to the square root of your dataset for optimal results.

## 22. Quiz

**Question 1:** What is the optimal jump size for Jump Search?
- A) n/2
- B) sqrt(n)
- C) log n
- D) 1
- **Correct Answer:** B
- **Explanation:** sqrt(n) balances the number of jumps and the length of the final linear scan.

**Question 2:** What is the time complexity of Jump Search?
- A) O(log n)
- B) O(n)
- C) O(sqrt(n))
- D) O(1)
- **Correct Answer:** C
- **Explanation:** It makes sqrt(n) jumps and up to sqrt(n) linear comparisons.

**Question 3:** Does Jump Search require sorted data?
- A) No
- B) Only for strings
- C) Yes
- D) Only for even-length arrays
- **Correct Answer:** C
- **Explanation:** The algorithm relies on ordering to know when to stop jumping forward.

**Question 4:** What is the space complexity?
- A) O(sqrt(n))
- B) O(log n)
- C) O(1)
- D) O(n)
- **Correct Answer:** C
- **Explanation:** Only a few integer variables are maintained.

**Question 5:** Jump Search is best described as a hybrid of which two approaches?
- A) Hashing and Binary Search
- B) Linear Search and Binary Search
- C) Linear Search and block skipping
- D) Sorting and Searching
- **Correct Answer:** C
- **Explanation:** It skips blocks linearly and then searches linearly within a block.

**Question 6:** What happens if the target is greater than all elements?
- A) It returns the last index
- B) It returns -1 after exhausting all blocks
- C) It throws an error
- D) It enters an infinite loop
- **Correct Answer:** B
- **Explanation:** The jump phase eventually exceeds the array bounds, triggering the not-found condition.

**Question 7:** Why might Jump Search be preferred over Binary Search in some hardware contexts?
- A) It uses less CPU
- B) Forward-only traversal matches sequential storage better
- C) It requires no memory
- D) It works on unsorted data
- **Correct Answer:** B
- **Explanation:** Some storage media optimize sequential forward reads.

**Question 8:** In the linear scan phase, where does scanning begin?
- A) Index 0
- B) The start of the current block (prev)
- C) The middle of the array
- D) The end of the array
- **Correct Answer:** B
- **Explanation:** After jumping, we know the target must be between prev and the current step.

**Question 9:** What is a major disadvantage compared to Binary Search?
- A) It uses more memory
- B) It has worse asymptotic time complexity
- C) It cannot handle duplicates
- D) It requires extra data structures
- **Correct Answer:** B
- **Explanation:** O(sqrt(n)) grows faster than O(log n) as n increases.

**Question 10:** Which function is essential for calculating the optimal step size?
- A) Logarithm
- B) Square root
- C) Exponential
- D) Absolute value
- **Correct Answer:** B
- **Explanation:** The square root of n gives the optimal block size.
