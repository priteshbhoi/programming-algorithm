# Counting Sort

## 1. Introduction

Counting Sort is a highly efficient, non-comparison-based sorting algorithm. Imagine you are a teacher tallying the grades of 30 students, and the grades only range from 1 to 10. Instead of comparing every student's grade to every other student's grade, you simply create 10 buckets (one for each possible grade) and count how many students got each grade. Then, you reconstruct the sorted list by reading the counts from bucket 1 to bucket 10. This is the core idea behind Counting Sort: counting the frequency of each distinct element and using arithmetic to determine their positions in the final sorted array.

## 2. Why Use This Algorithm?

Counting Sort bridges the gap between theoretical linear-time sorting and practical implementation for specific data types.

**Benefits:**
- **Linear time complexity:** $O(n + k)$ when the range of input values ($k$) is not significantly greater than the number of elements ($n$).
- **Stable sorting algorithm:** Preserves the relative order of equal elements.
- **Simple logic:** No complex recursive calls or partitioning.

**Performance:**
- **Time Complexity:** $O(n + k)$, where $n$ is the number of elements and $k$ is the range of input ($	ext{max} - 	ext{min} + 1$).
- **Space Complexity:** $O(n + k)$ for the count and output arrays.

**When it is better than other algorithms:**
Counting Sort is vastly superior to comparison-based sorts (like Quick Sort or Merge Sort) when sorting integers within a small, known range (e.g., ages of people, test scores from 0–100, or days of the month).

## 3. Real-World Applications

- **Sorting bounded integers:** Ages of a population, test scores, or shoe sizes.
- **Subroutine for Radix Sort:** Counting Sort is the stable sorting engine used in each digit pass of Radix Sort.
- **Histogram generation:** Counting frequencies of categorical or discrete numerical data.
- **Database indexing:** Sorting records by a small integer key (like a status code or priority level).

## 4. Prerequisites

Before learning Counting Sort, you should know:
- Basic array manipulation and indexing.
- The concept of algorithm stability.
- The difference between comparison-based and non-comparison-based sorting.
- Basic understanding of Big-O notation.

## 5. Visualization

Imagine a row of colored balls, each with a number from 1 to 5 written on it. Above the row, you have 5 empty jars labeled 1 through 5. You pick up each ball, look at its number, and drop a pebble into the corresponding jar. Once all balls are processed, the jars contain the exact count of each number. You then read the jars from 1 to 5. If jar 2 has 3 pebbles, you place three "2" balls into a new, sorted row. The original order of balls with the same number is preserved if you fill the new row from right to left.

## 6. How It Works

Counting Sort works in four main phases:
1. **Find the maximum value:** Traverse the input array to determine the range $k$.
2. **Count frequencies:** Create a count array of size $k+1$ and tally the occurrences of each element.
3. **Compute prefix sums:** Modify the count array so that each element at index $i$ stores the sum of counts up to $i$. This tells us the actual position of each element in the sorted output.
4. **Build the output array:** Iterate through the original array backwards (to maintain stability), use the count array to find the correct position for each element, place it in the output array, and decrement the count.

## 7. Step-by-Step Algorithm

1. Find the maximum element `max_val` in the input array.
2. Initialize a count array of size `max_val + 1` with all zeros.
3. Traverse the input array and increment the count of each element: `count[array[i]]++`.
4. Modify the count array to store cumulative sums: `count[i] = count[i] + count[i-1]`.
5. Initialize an output array of the same size as the input array.
6. Traverse the input array backwards (from `n - 1` down to `0`):
   1. Find the correct position: `pos = count[array[i]] - 1`.
   2. Place the element: `output[pos] = array[i]`.
   3. Decrement the count: `count[array[i]]--`.
7. Copy the output array back to the original array.

## 8. Pseudocode

```text
function countingSort(array):
    n = length(array)
    max_val = max(array)
    
    // Step 1: Initialize count array with zeros
    count = array of size (max_val + 1) filled with 0
    
    // Step 2: Store the count of each element
    for i from 0 to n - 1:
        count[array[i]] = count[array[i]] + 1
        
    // Step 3: Store cumulative count
    for i from 1 to max_val:
        count[i] = count[i] + count[i - 1]
        
    // Step 4: Build output array (iterate backwards to preserve stability)
    output = array of size n
    for i from n - 1 down to 0:
        pos = count[array[i]] - 1
        output[pos] = array[i]
        count[array[i]] = count[array[i]] - 1
        
    // Step 5: Copy output back to original array
    for i from 0 to n - 1:
        array[i] = output[i]
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>

void countingSort(int arr[], int n) {
    int max = arr[0];
    for (int i = 1; i < n; i++) {
        if (arr[i] > max)
            max = arr[i];
    }

    int* count = (int*)calloc(max + 1, sizeof(int));
    int* output = (int*)malloc(n * sizeof(int));

    for (int i = 0; i < n; i++) {
        count[arr[i]]++;
    }

    for (int i = 1; i <= max; i++) {
        count[i] += count[i - 1];
    }

    for (int i = n - 1; i >= 0; i--) {
        output[count[arr[i]] - 1] = arr[i];
        count[arr[i]]--;
    }

    for (int i = 0; i < n; i++) {
        arr[i] = output[i];
    }

    free(count);
    free(output);
}

void printArray(int arr[], int n) {
    for (int i = 0; i < n; i++)
        printf("%d ", arr[i]);
    printf("\n");
}

int main() {
    int arr[] = {4, 2, 2, 8, 3, 3, 1};
    int n = sizeof(arr) / sizeof(arr[0]);
    countingSort(arr, n);
    printf("Sorted array: ");
    printArray(arr, n);
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

void countingSort(vector<int>& arr) {
    if (arr.empty()) return;
    
    int max_val = *max_element(arr.begin(), arr.end());
    int n = arr.size();

    vector<int> count(max_val + 1, 0);
    vector<int> output(n);

    for (int x : arr) {
        count[x]++;
    }

    for (int i = 1; i <= max_val; i++) {
        count[i] += count[i - 1];
    }

    for (int i = n - 1; i >= 0; i--) {
        output[count[arr[i]] - 1] = arr[i];
        count[arr[i]]--;
    }

    arr = output;
}

int main() {
    vector<int> arr = {4, 2, 2, 8, 3, 3, 1};
    countingSort(arr);
    cout << "Sorted array: ";
    for (int x : arr) cout << x << " ";
    cout << endl;
    return 0;
}
```

### Java
```java
import java.util.Arrays;

public class CountingSort {
    public static void countingSort(int[] arr) {
        if (arr.length == 0) return;

        int max = arr[0];
        for (int val : arr) {
            if (val > max) max = val;
        }

        int[] count = new int[max + 1];
        int[] output = new int[arr.length];

        for (int val : arr) {
            count[val]++;
        }

        for (int i = 1; i <= max; i++) {
            count[i] += count[i - 1];
        }

        for (int i = arr.length - 1; i >= 0; i--) {
            output[count[arr[i]] - 1] = arr[i];
            count[arr[i]]--;
        }

        System.arraycopy(output, 0, arr, 0, arr.length);
    }

    public static void main(String[] args) {
        int[] arr = {4, 2, 2, 8, 3, 3, 1};
        countingSort(arr);
        System.out.println("Sorted array: " + Arrays.toString(arr));
    }
}
```

### Python
```python
def counting_sort(arr):
    if not arr:
        return arr

    max_val = max(arr)
    count = [0] * (max_val + 1)
    output = [0] * len(arr)

    for num in arr:
        count[num] += 1

    for i in range(1, len(count)):
        count[i] += count[i - 1]

    for i in range(len(arr) - 1, -1, -1):
        output[count[arr[i]] - 1] = arr[i]
        count[arr[i]] -= 1

    for i in range(len(arr)):
        arr[i] = output[i]

arr = [4, 2, 2, 8, 3, 3, 1]
counting_sort(arr)
print("Sorted array:", arr)
```

### JavaScript
```javascript
function countingSort(arr) {
    if (arr.length === 0) return arr;

    const maxVal = Math.max(...arr);
    const count = new Array(maxVal + 1).fill(0);
    const output = new Array(arr.length).fill(0);

    for (let i = 0; i < arr.length; i++) {
        count[arr[i]]++;
    }

    for (let i = 1; i <= maxVal; i++) {
        count[i] += count[i - 1];
    }

    for (let i = arr.length - 1; i >= 0; i--) {
        output[count[arr[i]] - 1] = arr[i];
        count[arr[i]]--;
    }

    for (let i = 0; i < arr.length; i++) {
        arr[i] = output[i];
    }
}

const arr = [4, 2, 2, 8, 3, 3, 1];
countingSort(arr);
console.log("Sorted array:", arr.join(" "));
```

## 10. Code Explanation

The algorithm begins by identifying the maximum value to size the count array correctly. The first loop tallies how many times each number appears. The second loop transforms these tallies into cumulative sums, which represent the last valid index (plus one) for each number in the sorted array. The third loop iterates backwards through the original array. This backward iteration is crucial: it ensures that if two elements have the same value, the one that appeared later in the original array is placed later in the output array, preserving stability.

## 11. Interactive Demo

The demo displays the original array as a row of numbered blocks. Above them, a "Count Array" visually fills up with tally marks as each block is processed. Next, the Count Array transforms into a "Cumulative Count Array" with animated addition. Finally, blocks fly from the original array to the "Output Array" one by one, starting from the rightmost block. A highlight shows the cumulative count value being used to calculate the exact target index, and the count value visibly decrements after placement.

## 12. Dry Run

**Sample Input:** Array: `[4, 2, 2, 8, 3, 3, 1]` ($n = 7, 	ext{max} = 8$)

- **Phase 1: Frequency Count:** `[0, 1, 2, 2, 1, 0, 0, 0, 1]` (indices 0 to 8)
- **Phase 2: Cumulative Sum:** `[0, 1, 3, 5, 6, 6, 6, 6, 7]`

**Phase 3: Backward Placement into Output Array:**

| Step | Element Processed | Target Position (`pos = count[val] - 1`) | Output Array State | Updated Count Array |
| :--- | :--- | :--- | :--- | :--- |
| **Initial** | — | — | `[0, 0, 0, 0, 0, 0, 0]` | `[0, 1, 3, 5, 6, 6, 6, 6, 7]` |
| **1** | `arr[6] = 1` | `count[1] - 1 = 1 - 1 = 0` | `[1, 0, 0, 0, 0, 0, 0]` | `[0, 0, 3, 5, 6, 6, 6, 6, 7]` |
| **2** | `arr[5] = 3` | `count[3] - 1 = 5 - 1 = 4` | `[1, 0, 0, 0, 3, 0, 0]` | `[0, 0, 3, 4, 6, 6, 6, 6, 7]` |
| **3** | `arr[4] = 3` | `count[3] - 1 = 4 - 1 = 3` | `[1, 0, 0, 3, 3, 0, 0]` | `[0, 0, 3, 3, 6, 6, 6, 6, 7]` |
| **4** | `arr[3] = 8` | `count[8] - 1 = 7 - 1 = 6` | `[1, 0, 0, 3, 3, 0, 8]` | `[0, 0, 3, 3, 6, 6, 6, 6, 6]` |
| **5** | `arr[2] = 2` | `count[2] - 1 = 3 - 1 = 2` | `[1, 0, 2, 3, 3, 0, 8]` | `[0, 0, 2, 3, 6, 6, 6, 6, 6]` |
| **6** | `arr[1] = 2` | `count[2] - 1 = 2 - 1 = 1` | `[1, 2, 2, 3, 3, 0, 8]` | `[0, 0, 1, 3, 6, 6, 6, 6, 6]` |
| **7** | `arr[0] = 4` | `count[4] - 1 = 6 - 1 = 5` | `[1, 2, 2, 3, 3, 4, 8]` | `[0, 0, 1, 3, 5, 6, 6, 6, 6]` |

**Final Output:** `[1, 2, 2, 3, 3, 4, 8]`

## 13. Time & Space Complexity

| Case | Time Complexity | Reason |
| :--- | :--- | :--- |
| **Best Case** | $O(n + k)$ | Must always count elements and compute cumulative sums. |
| **Average Case** | $O(n + k)$ | Linear passes through array and count array. |
| **Worst Case** | $O(n + k)$ | Same as average; no input arrangement changes the number of operations. |
| **Space Complexity** | $O(n + k)$ | Requires auxiliary count array of size $k+1$ and output array of size $n$. |

## 14. Advantages

- **Linear Time:** Achieves $O(n)$ time when $k$ is proportional to $n$, breaking the $O(n \log n)$ comparison sort barrier.
- **Stable:** Preserves the original order of equal elements, making it ideal as a subroutine for Radix Sort.
- **Simple Logic:** Easy to implement with basic loops and array indexing.
- **Predictable:** Performance does not degrade based on input order (already sorted, reverse sorted, or random).

## 15. Disadvantages

- **Range Dependency:** Highly inefficient if $k$ (the range of values) is much larger than $n$ (e.g., sorting `[1, 1000000]` requires a massive count array).
- **Not In-Place:** Requires $O(n + k)$ extra space, which can be prohibitive for large datasets.
- **Data Type Restricted:** Only works directly with integers or data that can be mapped to small integers (not floating-point numbers or strings without modification).
- **Negative Numbers:** Requires an offset adjustment to handle negative values, adding slight complexity.

## 16. Applications

- Sorting student grades, ages, or other bounded demographic data.
- The core sorting mechanism inside Radix Sort.
- Suffix array construction algorithms.
- Fast frequency analysis and histogram generation in data processing pipelines.

## 17. Common Mistakes

- **Forgetting backward iteration:** Iterating forwards when building the output array destroys the algorithm's stability.
- **Off-by-one errors in cumulative sum:** Forgetting that the cumulative count represents 1-based positioning, requiring a `-1` adjustment when placing into a 0-indexed array.
- **Ignoring negative numbers:** Failing to shift the array values by the minimum value when the input contains negative integers.
- **Using it for large ranges:** Applying Counting Sort to data with a massive range (e.g., 32-bit integers) leads to Out-Of-Memory errors.

## 18. Interview Questions

1. Why is Counting Sort not considered a comparison-based sorting algorithm?
2. What is the time complexity of Counting Sort, and what do $n$ and $k$ represent?
3. Why do we iterate backwards through the original array when building the output array?
4. How would you modify Counting Sort to handle an array containing negative numbers?
5. Is Counting Sort an in-place sorting algorithm? Explain.
6. When would you choose Counting Sort over Quick Sort?
7. How is Counting Sort used as a subroutine in Radix Sort?
8. What happens to the space complexity if the maximum value in the array is $10^9$ but $n = 100$?
9. Can Counting Sort be used to sort floating-point numbers directly? Why or why not?
10. Prove that Counting Sort is a stable sorting algorithm.

## 19. Practice Problems

**Easy:**
1. Implement basic Counting Sort for an array of positive integers.
2. Modify your Counting Sort to handle arrays containing negative numbers.
3. Write a function that uses Counting Sort to find the mode (most frequent element) of an array.

**Medium:**
4. Implement Counting Sort to sort an array of characters (e.g., `'a'` to `'z'`).
5. Given an array of objects with an integer age property, sort the array by age using Counting Sort while preserving the original order of people with the same age.
6. Optimize the space complexity of Counting Sort by finding both the minimum and maximum values to reduce the size of the count array.

**Hard:**
7. Implement Radix Sort using your Counting Sort function as the stable digit-sorting subroutine.
8. Design a variant of Counting Sort that sorts an array in-place (Hint: this is very difficult and requires swapping, potentially losing stability).
9. Given a massive stream of integers within a small range, design a system to maintain a sorted view of the data using Counting Sort principles.

## 20. Related Algorithms

- **Radix Sort:** Uses Counting Sort as a stable subroutine to sort digit by digit.
- **Bucket Sort:** Similar concept, but distributes elements into ranges (buckets) rather than exact counts.
- **Pigeonhole Sort:** A simpler variant of Counting Sort that moves items directly rather than using cumulative counts.
- **Hash Map / Frequency Array:** The counting phase is identical to building a frequency hash map.

## 21. Summary

Counting Sort is a powerful, linear-time, non-comparison sorting algorithm that excels when the range of input values is small relative to the number of elements. By tallying frequencies and using cumulative sums, it determines the exact position of each element in $O(n + k)$ time. While its space requirements and restriction to discrete, bounded data prevent it from being a general-purpose sort, it remains an indispensable tool for specific use cases and serves as the critical engine behind Radix Sort.

## 22. Quiz

**Question 1:** What is the primary requirement for Counting Sort to be efficient?
- A) The array must be nearly sorted.
- B) The range of input values ($k$) should not be significantly larger than $n$.
- C) The array must contain only positive numbers.
- D) The array size must be a power of 2.
- **Correct Answer:** B
- **Explanation:** If $k$ is much larger than $n$, the space and time required to initialize and traverse the count array become highly inefficient.

**Question 2:** What is the time complexity of Counting Sort?
- A) $O(n \log n)$
- B) $O(n^2)$
- C) $O(n + k)$
- D) $O(k \log k)$
- **Correct Answer:** C
- **Explanation:** It takes $O(n)$ to count elements, $O(k)$ to compute cumulative sums, and $O(n)$ to build the output array, totaling $O(n + k)$.

**Question 3:** Why do we iterate backwards through the input array when building the output array?
- A) To save memory.
- B) To make the algorithm faster.
- C) To maintain the stability of the sort.
- D) To handle negative numbers.
- **Correct Answer:** C
- **Explanation:** Iterating backwards ensures that elements with the same value appear in the output array in the same relative order as they did in the input array.

**Question 4:** Is Counting Sort an in-place sorting algorithm?
- A) Yes, always.
- B) No, it requires $O(n + k)$ extra space.
- C) Yes, if $k$ is small.
- D) Only in Python.
- **Correct Answer:** B
- **Explanation:** It requires auxiliary arrays (count and output) whose sizes depend on $n$ and $k$, so it is not in-place.

**Question 5:** How do you adapt Counting Sort to handle negative numbers?
- A) It cannot handle negative numbers.
- B) Multiply all numbers by -1.
- C) Find the minimum value and subtract it from all elements to shift the range to start at 0.
- D) Use a separate count array for negative numbers.
- **Correct Answer:** C
- **Explanation:** Shifting all values by subtracting the minimum value maps the smallest number to 0, allowing standard count array indexing.

**Question 6:** What does the cumulative sum in the count array represent?
- A) The total number of elements.
- B) The exact index (1-based) where the next occurrence of that element should be placed.
- C) The frequency of the element.
- D) The sum of all array elements.
- **Correct Answer:** B
- **Explanation:** The cumulative sum at index $i$ tells us the position (1-based) of the last occurrence of value $i$ in the sorted output.

**Question 7:** Which of the following data types is Counting Sort LEAST suitable for?
- A) Ages of people (0–120)
- B) Test scores (0–100)
- C) 32-bit random integers
- D) Days of the month (1–31)
- **Correct Answer:** C
- **Explanation:** 32-bit integers have a range of over 4 billion, making the count array impossibly large and inefficient for typical $n$.

**Question 8:** What is the space complexity of Counting Sort?
- A) $O(1)$
- B) $O(\log n)$
- C) $O(n)$
- D) $O(n + k)$
- **Correct Answer:** D
- **Explanation:** It requires an output array of size $n$ and a count array of size $k + 1$.

**Question 9:** Counting Sort is often used as a subroutine in which other algorithm?
- A) Quick Sort
- B) Merge Sort
- C) Radix Sort
- D) Heap Sort
- **Correct Answer:** C
- **Explanation:** Radix Sort relies on a stable sorting algorithm to sort digit by digit, and Counting Sort is the standard choice for this.

**Question 10:** If the input array is already sorted, what is the time complexity of Counting Sort?
- A) $O(n)$
- B) $O(n \log n)$
- C) $O(n + k)$
- D) $O(1)$
- **Correct Answer:** C
- **Explanation:** Counting Sort is not adaptive; it always performs the same number of operations regardless of the initial order of the input.