# Binary Search

## 1. Introduction

Binary Search is one of the most elegant and efficient algorithms in computer science. Instead of checking every element like Linear Search, it repeatedly divides the search space in half. Think of looking up a word in a dictionary. You do not start at page one; you open somewhere near the middle, decide if your word is before or after that page, and then repeat the process on the remaining half.

It was created to solve the problem of searching large sorted datasets efficiently. When data is sorted and static (or rarely changes), checking elements one by one is wasteful. Binary Search gives you an exponential speedup.

You should use Binary Search whenever your data is sorted and you need to find an element, determine boundaries, or locate insertion points. It is the standard approach for in-memory sorted arrays and is used as a building block in countless advanced algorithms.

## 2. Why Use This Algorithm?

Binary Search transforms an O(n) problem into an O(log n) problem. On a dataset of one billion items, Linear Search might need a billion checks, while Binary Search needs at most thirty.

**Benefits:**
- Dramatically faster than Linear Search on large sorted datasets
- Very simple to implement correctly
- Extremely cache-friendly for arrays
- Forms the basis for many other algorithms

**Performance:**
Each step eliminates half of the remaining elements. This halving behavior means the number of steps equals the number of times you can divide n by 2 before reaching 1, which is log base 2 of n.

**When it is better than other algorithms:**
Binary Search dominates Linear Search when the dataset is sorted and has more than a few dozen elements. It also outperforms Hash Table Lookup when you need to find boundaries or the closest value rather than an exact match.

## 3. Real-World Applications

- **Database indexing:** B-Trees, a generalization of Binary Search, power most database indexes.
- **Git bisect:** Git uses a binary search approach to find the exact commit that introduced a bug.
- **Auto-complete and spell checkers:** Dictionaries of sorted words are searched to find prefixes and suggestions.
- **Java standard library:** `Arrays.binarySearch()` and `Collections.binarySearch()` are built into Java.
- **C++ STL:** `lower_bound` and `upper_bound` use binary search principles.
- **Finding versions in software releases:** When a bug appears between version 1.0 and 2.0, developers binary search through commits.

## 4. Prerequisites

Before learning Binary Search, you should know:
- How arrays work and how indexing functions
- Basic loop and conditional logic
- The concept of sorting and what "sorted order" means
- Integer division and how it affects midpoint calculations
- A basic understanding of logarithms (helpful but not mandatory)

## 5. Visualization

Imagine a sorted row of numbered boxes from smallest to largest. You place a marker in the exact middle. You compare the value inside that middle box to your target. If the target is smaller, you erase everything to the right and focus only on the left half. If the target is larger, you erase everything to the left and focus on the right half. You place a new marker in the middle of the surviving half and repeat. The search space shrinks like a collapsing telescope until the target is found or nothing remains.

## 6. How It Works

Binary Search requires a sorted array. It maintains two boundaries: a left pointer and a right pointer. It calculates the middle index between them. If the middle element equals the target, the search is complete. If the target is smaller, the right boundary moves to just before the middle. If the target is larger, the left boundary moves to just after the middle. This process repeats until the boundaries cross, which means the target is absent.

## 7. Step-by-Step Algorithm

1. Set `left` to 0 and `right` to the last index of the array.
2. While `left` is less than or equal to `right`:
   1. Calculate `mid` as `left + (right - left) / 2`.
   2. If `array[mid]` equals the target, return `mid`.
   3. If `array[mid]` is less than the target, set `left` to `mid + 1`.
   4. If `array[mid]` is greater than the target, set `right` to `mid - 1`.
3. If the loop ends, return -1 (not found).

## 8. Pseudocode

```
function binarySearch(array, target):
    left = 0
    right = length(array) - 1
    while left <= right:
        mid = left + (right - left) / 2
        if array[mid] == target:
            return mid
        else if array[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1
```

## 9. Code Examples

### C
```c
#include <stdio.h>

int binarySearch(int arr[], int n, int target) {
    int left = 0;
    int right = n - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (arr[mid] == target)
            return mid;
        else if (arr[mid] < target)
            left = mid + 1;
        else
            right = mid - 1;
    }
    return -1;
}

int main() {
    int arr[] = {2, 5, 8, 12, 16, 23, 38, 45, 56, 67};
    int n = sizeof(arr) / sizeof(arr[0]);
    int target = 23;
    int result = binarySearch(arr, n, target);
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
using namespace std;

int binarySearch(const vector<int>& arr, int target) {
    int left = 0;
    int right = arr.size() - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (arr[mid] == target)
            return mid;
        else if (arr[mid] < target)
            left = mid + 1;
        else
            right = mid - 1;
    }
    return -1;
}

int main() {
    vector<int> arr = {2, 5, 8, 12, 16, 23, 38, 45, 56, 67};
    int target = 23;
    int result = binarySearch(arr, target);
    if (result != -1)
        cout << "Element found at index " << result << endl;
    else
        cout << "Element not found" << endl;
    return 0;
}
```

### Java
```java
public class BinarySearch {
    public static int binarySearch(int[] arr, int target) {
        int left = 0;
        int right = arr.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (arr[mid] == target)
                return mid;
            else if (arr[mid] < target)
                left = mid + 1;
            else
                right = mid - 1;
        }
        return -1;
    }

    public static void main(String[] args) {
        int[] arr = {2, 5, 8, 12, 16, 23, 38, 45, 56, 67};
        int target = 23;
        int result = binarySearch(arr, target);
        if (result != -1)
            System.out.println("Element found at index " + result);
        else
            System.out.println("Element not found");
    }
}
```

### Python
```python
def binary_search(arr, target):
    left, right = 0, len(arr) - 1
    while left <= right:
        mid = left + (right - left) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1

arr = [2, 5, 8, 12, 16, 23, 38, 45, 56, 67]
target = 23
result = binary_search(arr, target)
if result != -1:
    print(f"Element found at index {result}")
else:
    print("Element not found")
```

### JavaScript
```javascript
function binarySearch(arr, target) {
    let left = 0;
    let right = arr.length - 1;
    while (left <= right) {
        const mid = left + Math.floor((right - left) / 2);
        if (arr[mid] === target) {
            return mid;
        } else if (arr[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    return -1;
}

const arr = [2, 5, 8, 12, 16, 23, 38, 45, 56, 67];
const target = 23;
const result = binarySearch(arr, target);
if (result !== -1) {
    console.log(`Element found at index ${result}`);
} else {
    console.log("Element not found");
}
```

## 10. Code Explanation

The `left` and `right` variables define the current searchable window. The midpoint formula `left + (right - left) / 2` is safer than `(left + right) / 2` because it prevents integer overflow in languages with fixed-size integers. The loop continues while the window is valid (`left <= right`). Each iteration halves the window. When the target is found, the index is returned immediately. If the window collapses, -1 is returned. The algorithm never touches elements outside the current window, making it very predictable.

## 11. Interactive Demo

The demo displays a sorted row of blocks. Two pointers labeled "Left" and "Right" appear at the ends. A "Mid" pointer jumps to the center. The user enters a target value and clicks "Search."

During animation, the Mid block highlights and compares its value to the target. If the target is larger, all blocks left of Mid (including Mid) fade out or gray out, and Left moves to Mid + 1. If smaller, the right side fades and Right moves to Mid - 1. A status panel tracks Left, Mid, Right, and the number of steps taken. When found, the target block pulses green. If not found, a message appears when Left crosses Right.

## 12. Dry Run

**Sample Input:**
Array: `[2, 5, 8, 12, 16, 23, 38, 45, 56, 67]`
Target: `23`

| Step | Left | Right | Mid | arr[Mid] | Comparison | Action |
|------|------|-------|-----|----------|------------|--------|
| 1 | 0 | 9 | 4 | 16 | 16 < 23 | left = 5 |
| 2 | 5 | 9 | 7 | 45 | 45 > 23 | right = 6 |
| 3 | 5 | 6 | 5 | 23 | 23 == 23 | Return 5 |

**Final Output:** Index `5`

## 13. Time & Space Complexity

| Case | Time Complexity | Reason |
|------|-----------------|--------|
| Best Case | O(1) | Target is exactly at the middle index on the first check |
| Average Case | O(log n) | The search space is halved each iteration |
| Worst Case | O(log n) | The target is at the very end or not present |
| Space Complexity | O(1) | Iterative version uses only a few index variables |

## 14. Advantages

- **Extremely fast on large data:** Logarithmic growth means it scales to billions of elements.
- **Predictable performance:** The worst case is only slightly worse than the average case.
- **No extra memory:** The iterative version is completely in-place.
- **Versatile:** Can be adapted to find lower bounds, upper bounds, and closest values.
- **Cache efficient:** Accessing the middle of a contiguous array is hardware-friendly.

## 15. Disadvantages

- **Requires sorted data:** If the array is not sorted, Binary Search will give wrong answers.
- **Not suitable for linked lists:** Finding the middle of a linked list requires a linear scan, defeating the purpose.
- **Maintenance cost:** If the dataset changes frequently, keeping it sorted may be expensive.
- **Slower than hash tables for exact match:** If you only need exact lookups and can afford the memory, hash tables offer O(1) average time.

## 16. Applications

- Finding elements in sorted database indexes
- Implementing efficient `set` and `map` operations in standard libraries
- Determining the square root of a number (Binary Search on Answer)
- Finding the first or last occurrence of a value in a sorted array with duplicates
- Optimizing resource allocation problems where a monotonic predicate exists

## 17. Common Mistakes

- **Integer overflow in midpoint:** Using `(left + right) / 2` can overflow. Always use `left + (right - left) / 2`.
- **Wrong loop condition:** Using `left < right` instead of `left <= right` can cause off-by-one errors when the target is at the last checked position.
- **Forgetting to sort:** Binary Search silently fails on unsorted data. Always verify your input is sorted.
- **Incorrect boundary updates:** Setting `right = mid` or `left = mid` instead of `mid +/- 1` can cause infinite loops.

## 18. Interview Questions

1. What is the prerequisite condition for Binary Search to work correctly?
2. Why is `mid = left + (right - left) / 2` preferred over `(left + right) / 2`?
3. How would you find the first occurrence of a duplicate element using Binary Search?
4. How would you find the last occurrence of a duplicate element using Binary Search?
5. Can Binary Search be implemented recursively? What is the space complexity then?
6. What happens if you run Binary Search on an unsorted array?
7. How can you use Binary Search to find the square root of a number?
8. What is the difference between lower_bound and upper_bound?
9. How many comparisons does Binary Search make in the worst case for n = 1024?
10. Write a Binary Search variant that finds the index of the smallest element greater than the target.

## 19. Practice Problems

**Easy:**
1. Implement iterative Binary Search on a sorted array of integers.
2. Implement recursive Binary Search.
3. Find the index of the first occurrence of a target in a sorted array with duplicates.
4. Find the index of the last occurrence of a target in a sorted array with duplicates.

**Medium:**
5. Count the total number of occurrences of a target in a sorted array using Binary Search.
6. Find the smallest missing element in a sorted array of distinct integers starting from 0.
7. Search in a rotated sorted array (e.g., `[4, 5, 6, 7, 0, 1, 2]`).
8. Find the peak element in a mountain array (strictly increasing then strictly decreasing).

**Hard:**
9. Find the median of two sorted arrays using a Binary Search approach.
10. Search for a target in a sorted matrix where each row and column is sorted.
11. Implement a Binary Search to find the minimum element in a rotated sorted array with duplicates.

## 20. Related Algorithms

- Linear Search (when data is unsorted)
- Jump Search (for sorted arrays with fewer comparisons)
- Interpolation Search (when data is uniformly distributed)
- Exponential Search (for unbounded or infinite arrays)
- Ternary Search (for finding maxima/minima of unimodal functions)

## 21. Summary

Binary Search is the gold standard for searching sorted data. By halving the search space every step, it achieves logarithmic time complexity, making it feasible to search enormous datasets with minimal effort. The key requirements are sorted input and careful index management. Mastering Binary Search opens the door to solving a vast family of optimization and boundary-finding problems in computer science.

## 22. Quiz

**Question 1:** What is the time complexity of Binary Search in the worst case?
- A) O(n)
- B) O(log n)
- C) O(n log n)
- D) O(1)
- **Correct Answer:** B
- **Explanation:** Each iteration halves the search space, resulting in logarithmic time.

**Question 2:** What is the most important prerequisite for Binary Search?
- A) The array must contain unique elements
- B) The array must be sorted
- C) The array must be of even length
- D) The array must contain only positive numbers
- **Correct Answer:** B
- **Explanation:** Binary Search relies on the ordering property to decide which half to discard.

**Question 3:** Which formula safely calculates the middle index?
- A) `(left + right) / 2`
- B) `left + (right - left) / 2`
- C) `(left * right) / 2`
- D) `right - left / 2`
- **Correct Answer:** B
- **Explanation:** This avoids integer overflow that can occur when adding two large indices.

**Question 4:** What is the space complexity of iterative Binary Search?
- A) O(log n)
- B) O(n)
- C) O(1)
- D) O(n log n)
- **Correct Answer:** C
- **Explanation:** Only a constant number of variables are used regardless of input size.

**Question 5:** If Binary Search is run on an unsorted array, what happens?
- A) It throws an error
- B) It may return an incorrect result
- C) It automatically sorts the array first
- D) It runs slower but still correctly
- **Correct Answer:** B
- **Explanation:** Without sorted order, the halving logic is invalid and can miss the target.

**Question 6:** How many elements will be checked in the worst case for n = 128?
- A) 128
- B) 64
- C) 8
- D) 7
- **Correct Answer:** D
- **Explanation:** log2(128) = 7, so at most 7 comparisons are needed.

**Question 7:** Can Binary Search work on a linked list?
- A) Yes, natively
- B) No, because random access is O(n)
- C) Yes, but only on doubly linked lists
- D) Yes, if the list is circular
- **Correct Answer:** B
- **Explanation:** Finding the middle of a linked list requires a linear scan, destroying the O(log n) advantage.

**Question 8:** What does the loop condition `left <= right` ensure?
- A) The array is sorted
- B) The search space is non-empty
- C) The target is found
- D) The array has no duplicates
- **Correct Answer:** B
- **Explanation:** It guarantees there is still a valid range of indices to examine.

**Question 9:** Which problem can be solved by adapting Binary Search?
- A) Sorting an array
- B) Finding square roots
- C) Reversing a string
- D) Merging two arrays
- **Correct Answer:** B
- **Explanation:** Binary Search on Answer can approximate values like square roots by searching a numeric range.

**Question 10:** What is returned when the target is not found?
- A) 0
- B) -1 (or a designated sentinel)
- C) The middle index
- D) The last index checked
- **Correct Answer:** B
- **Explanation:** A negative value conventionally signals that the target does not exist in the array.
