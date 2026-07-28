# Interpolation Search

## 1. Introduction

Interpolation Search is an improved variant of Binary Search for sorted arrays where the values are uniformly distributed. Instead of always checking the middle element, it estimates the target's position based on the values at the ends of the search range. If you were looking for a name in a phone book, you would not open to the middle if you were searching for "Anderson"; you would open near the beginning. That intuition is exactly what Interpolation Search does mathematically.

It was created to take advantage of data that is not just sorted but also evenly spread out. When values are uniformly distributed, this algorithm can achieve near-constant time performance.

You should use Interpolation Search when your sorted data is approximately uniformly distributed and you need faster average-case performance than Binary Search.

## 2. Why Use This Algorithm?

Interpolation Search can be dramatically faster than Binary Search when the data distribution is favorable.

**Benefits:**
- O(log log n) average-case time complexity for uniformly distributed data
- Still O(log n) in many practical scenarios
- Intuitive position estimation reduces unnecessary checks
- Adapts to the data distribution rather than blindly halving

**Performance:**
For uniformly distributed data, the number of steps grows incredibly slowly. Even for massive datasets, the average case is often just a handful of comparisons.

**When it is better than other algorithms:**
It outperforms Binary Search on large, uniformly distributed sorted arrays. It is particularly strong when the target is near the edges of the range.

## 3. Real-World Applications

- **IP address lookup tables:** IP ranges are often allocated in blocks that create uniform numeric distributions.
- **Searching in numerical scientific datasets:** Sensor readings taken at regular intervals create uniform value distributions.
- **Dictionary and phone book applications:** Words and names sorted alphabetically with relatively even distributions.
- **Database indexes on auto-incrementing keys:** IDs that increase linearly over time.
- **Finding timestamps in evenly sampled time-series data:** Financial tick data or IoT metrics collected at fixed intervals.

## 4. Prerequisites

Before learning Interpolation Search, you should know:
- Binary Search thoroughly
- Basic algebra (linear interpolation formula)
- What uniform distribution means
- Integer arithmetic and potential division-by-zero risks

## 5. Visualization

Imagine a sorted bar chart where the height of each bar corresponds to its value. If the values increase evenly, the tops of the bars form an almost straight diagonal line. Instead of jumping to the physical middle, Interpolation Search looks at the target value, sees where it would fit on that diagonal line, and jumps directly to that estimated position. If the estimate is slightly off, it narrows the range and estimates again.

## 6. How It Works

Interpolation Search calculates a probe position using the formula:

```
position = low + ((target - arr[low]) * (high - low)) / (arr[high] - arr[low])
```

This formula assumes the values between `arr[low]` and `arr[high]` are spread evenly. The algorithm compares the target with the element at the calculated position. If they match, it returns the index. If the target is larger, it searches the right portion. If smaller, it searches the left portion. The process repeats until the target is found or the range becomes invalid.

## 7. Step-by-Step Algorithm

1. Set `low` to 0 and `high` to the last index.
2. While `low` <= `high` and target is between `arr[low]` and `arr[high]`:
   1. If `low` equals `high`, check that single element and return if matched.
   2. Calculate `pos` using the interpolation formula.
   3. If `arr[pos]` equals target, return `pos`.
   4. If `arr[pos]` is less than target, set `low` to `pos + 1`.
   5. If `arr[pos]` is greater than target, set `high` to `pos - 1`.
3. Return -1 if the loop exits without finding the target.

## 8. Pseudocode

```
function interpolationSearch(array, target):
    low = 0
    high = length(array) - 1
    while low <= high and target >= array[low] and target <= array[high]:
        if low == high:
            if array[low] == target:
                return low
            return -1
        pos = low + ((target - array[low]) * (high - low)) / (array[high] - array[low])
        if array[pos] == target:
            return pos
        if array[pos] < target:
            low = pos + 1
        else:
            high = pos - 1
    return -1
```

## 9. Code Examples

### C
```c
#include <stdio.h>

int interpolationSearch(int arr[], int n, int target) {
    int low = 0;
    int high = n - 1;
    while (low <= high && target >= arr[low] && target <= arr[high]) {
        if (low == high) {
            if (arr[low] == target) return low;
            return -1;
        }
        int pos = low + ((target - arr[low]) * (high - low)) / (arr[high] - arr[low]);
        if (arr[pos] == target)
            return pos;
        if (arr[pos] < target)
            low = pos + 1;
        else
            high = pos - 1;
    }
    return -1;
}

int main() {
    int arr[] = {10, 20, 30, 40, 50, 60, 70, 80, 90, 100};
    int n = sizeof(arr) / sizeof(arr[0]);
    int target = 70;
    int result = interpolationSearch(arr, n, target);
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

int interpolationSearch(const vector<int>& arr, int target) {
    int low = 0;
    int high = arr.size() - 1;
    while (low <= high && target >= arr[low] && target <= arr[high]) {
        if (low == high) {
            if (arr[low] == target) return low;
            return -1;
        }
        int pos = low + ((target - arr[low]) * (high - low)) / (arr[high] - arr[low]);
        if (arr[pos] == target)
            return pos;
        if (arr[pos] < target)
            low = pos + 1;
        else
            high = pos - 1;
    }
    return -1;
}

int main() {
    vector<int> arr = {10, 20, 30, 40, 50, 60, 70, 80, 90, 100};
    int target = 70;
    int result = interpolationSearch(arr, target);
    if (result != -1)
        cout << "Element found at index " << result << endl;
    else
        cout << "Element not found" << endl;
    return 0;
}
```

### Java
```java
public class InterpolationSearch {
    public static int interpolationSearch(int[] arr, int target) {
        int low = 0;
        int high = arr.length - 1;
        while (low <= high && target >= arr[low] && target <= arr[high]) {
            if (low == high) {
                if (arr[low] == target) return low;
                return -1;
            }
            int pos = low + ((target - arr[low]) * (high - low)) / (arr[high] - arr[low]);
            if (arr[pos] == target)
                return pos;
            if (arr[pos] < target)
                low = pos + 1;
            else
                high = pos - 1;
        }
        return -1;
    }

    public static void main(String[] args) {
        int[] arr = {10, 20, 30, 40, 50, 60, 70, 80, 90, 100};
        int target = 70;
        int result = interpolationSearch(arr, target);
        if (result != -1)
            System.out.println("Element found at index " + result);
        else
            System.out.println("Element not found");
    }
}
```

### Python
```python
def interpolation_search(arr, target):
    low, high = 0, len(arr) - 1
    while low <= high and target >= arr[low] and target <= arr[high]:
        if low == high:
            if arr[low] == target:
                return low
            return -1
        pos = low + ((target - arr[low]) * (high - low)) // (arr[high] - arr[low])
        if arr[pos] == target:
            return pos
        elif arr[pos] < target:
            low = pos + 1
        else:
            high = pos - 1
    return -1

arr = [10, 20, 30, 40, 50, 60, 70, 80, 90, 100]
target = 70
result = interpolation_search(arr, target)
if result != -1:
    print(f"Element found at index {result}")
else:
    print("Element not found")
```

### JavaScript
```javascript
function interpolationSearch(arr, target) {
    let low = 0;
    let high = arr.length - 1;
    while (low <= high && target >= arr[low] && target <= arr[high]) {
        if (low === high) {
            if (arr[low] === target) return low;
            return -1;
        }
        const pos = low + Math.floor(((target - arr[low]) * (high - low)) / (arr[high] - arr[low]));
        if (arr[pos] === target) return pos;
        if (arr[pos] < target) {
            low = pos + 1;
        } else {
            high = pos - 1;
        }
    }
    return -1;
}

const arr = [10, 20, 30, 40, 50, 60, 70, 80, 90, 100];
const target = 70;
const result = interpolationSearch(arr, target);
if (result !== -1) {
    console.log(`Element found at index ${result}`);
} else {
    console.log("Element not found");
}
```

## 10. Code Explanation

The probe position formula is the heart of the algorithm. It scales the target's relative position within the value range to the index range. The `while` condition includes bounds checking (`target >= arr[low]` and `target <= arr[high]`) to handle cases where the target is outside the current range, preventing invalid probe calculations. The `low == high` guard avoids division by zero when all remaining elements have the same value. Once `pos` is calculated, the logic mirrors Binary Search: compare, narrow, repeat.

## 11. Interactive Demo

The demo shows a sorted bar chart where bar heights correspond to values. The user enters a target. When searching, a "Probe" marker does not jump to the middle but instead moves to a position proportional to the target's value relative to the range endpoints. The bar at the probe position flashes. If wrong, the bars outside the new range dim, and a new probe is calculated within the surviving range. A graph line connects `arr[low]` and `arr[high]` to visualize the interpolation assumption. The status panel shows the calculated probe formula result at each step.

## 12. Dry Run

**Sample Input:**
Array: `[10, 20, 30, 40, 50, 60, 70, 80, 90, 100]`
Target: `70`

| Step | Low | High | Pos Calculation | Pos | arr[Pos] | Comparison | Action |
|------|-----|------|-----------------|-----|----------|------------|--------|
| 1 | 0 | 9 | 0 + ((70-10)*(9-0))/(100-10) = 6 | 6 | 70 | 70 == 70 | Return 6 |

**Final Output:** Index `6`

## 13. Time & Space Complexity

| Case | Time Complexity | Reason |
|------|-----------------|--------|
| Best Case | O(1) | The first probe lands exactly on the target |
| Average Case | O(log log n) | For uniformly distributed data, the range collapses doubly exponentially |
| Worst Case | O(n) | If data is exponentially distributed, probes barely shrink the range |
| Space Complexity | O(1) | Only low, high, and pos variables are used |

## 14. Advantages

- **Extremely fast on uniform data:** log log n is practically constant for real-world datasets.
- **Adapts to data shape:** Unlike Binary Search, it uses value information to guide the probe.
- **Better edge performance:** Targets near the extremes are found quickly.
- **Simple to implement:** The structure is nearly identical to Binary Search.

## 15. Disadvantages

- **Poor on non-uniform data:** If values cluster together, performance degrades toward O(n).
- **Requires sorted data:** Like all comparison-based searches on arrays.
- **Division overhead:** The probe calculation involves division, which is slower than addition.
- **Risk of division by zero:** Special handling is needed when all elements in the range are equal.

## 16. Applications

- Searching numerical indexes with auto-incrementing keys
- Looking up values in time-series databases with regular intervals
- Finding entries in uniformly partitioned hash tables
- Searching sorted geographic coordinate datasets
- Dictionary lookups where word frequencies are relatively uniform

## 17. Common Mistakes

- **Ignoring the uniform distribution assumption:** Using Interpolation Search on heavily clustered data yields poor results.
- **Division by zero:** Forgetting to handle cases where `arr[high] == arr[low]`.
- **Integer overflow in the numerator:** `(target - arr[low]) * (high - low)` can overflow; use long integers or careful ordering.
- **Skipping the range validation:** The `target >= arr[low]` check prevents nonsensical probe positions.

## 18. Interview Questions

1. What is the key difference between Binary Search and Interpolation Search?
2. What type of data distribution gives Interpolation Search its best performance?
3. What is the worst-case time complexity and when does it occur?
4. How does the probe position formula work intuitively?
5. Why is there a risk of division by zero in Interpolation Search?
6. Can Interpolation Search be applied to strings? What challenges arise?
7. How would you modify the algorithm if the data is mostly uniform but has some clusters?
8. Compare the number of comparisons between Binary Search and Interpolation Search for 1 million uniformly distributed integers.
9. What happens if all elements in the array are identical and the target matches?
10. Is Interpolation Search stable? Can it be adapted to find boundary conditions like lower_bound?

## 19. Practice Problems

**Easy:**
1. Implement Interpolation Search on a uniformly distributed sorted array.
2. Search for the first element in a uniformly distributed array.
3. Search for the last element in a uniformly distributed array.
4. Count the number of probe calculations made during a search.

**Medium:**
5. Implement Interpolation Search that finds the first occurrence of a duplicate target.
6. Adapt Interpolation Search to work on floating-point arrays.
7. Compare the actual number of steps between Binary Search and Interpolation Search on the same dataset.
8. Handle the division-by-zero case gracefully when all range values are equal.

**Hard:**
9. Implement a hybrid search that uses Interpolation Search initially and falls back to Binary Search if probes become ineffective.
10. Apply Interpolation Search to a 2D matrix where rows and columns are sorted and uniformly distributed.
11. Create an Interpolation Search variant for non-uniform data using estimated density functions.

## 20. Related Algorithms

- Binary Search (the foundation)
- Jump Search (another sorted array search)
- Exponential Search (for unbounded ranges)
- Hash Table Lookup (for exact matches regardless of order)
- Ternary Search (for finding extrema)

## 21. Summary

Interpolation Search upgrades Binary Search by estimating where the target likely sits based on the value range. For uniformly distributed sorted data, it achieves O(log log n) average time, making it one of the fastest comparison-based searches available. However, its performance degrades on clustered or skewed data. Always consider your data's distribution before choosing Interpolation Search over Binary Search.

## 22. Quiz

**Question 1:** What distribution makes Interpolation Search fastest?
- A) Random
- B) Uniform
- C) Exponential
- D) Normal
- **Correct Answer:** B
- **Explanation:** Uniform distribution allows the linear interpolation formula to accurately predict the target's position.

**Question 2:** What is the average-case time complexity?
- A) O(log n)
- B) O(log log n)
- C) O(sqrt(n))
- D) O(n)
- **Correct Answer:** B
- **Explanation:** For uniform data, the search space shrinks doubly exponentially.

**Question 3:** What is the worst-case time complexity?
- A) O(log n)
- B) O(n)
- C) O(n log n)
- D) O(1)
- **Correct Answer:** B
- **Explanation:** With badly distributed data, each probe barely narrows the range.

**Question 4:** Which operation is central to calculating the probe position?
- A) Bit shift
- B) Division
- C) Modulo
- D) Exponentiation
- **Correct Answer:** B
- **Explanation:** The formula divides by the value range to map to the index range.

**Question 5:** Interpolation Search requires the data to be:
- A) Unsorted
- B) Sorted
- C) A linked list
- D) A tree
- **Correct Answer:** B
- **Explanation:** Value-based position estimation only works if values are in order.

**Question 6:** What must be guarded against when `arr[low] == arr[high]`?
- A) Infinite loop
- B) Division by zero
- C) Stack overflow
- D) Memory leak
- **Correct Answer:** B
- **Explanation:** The denominator in the probe formula becomes zero.

**Question 7:** For which target location does Interpolation Search typically outperform Binary Search most?
- A) Exact middle
- B) Near the edges
- C) Not present
- D) Random location
- **Correct Answer:** B
- **Explanation:** Binary Search always starts in the middle; Interpolation Search jumps closer to edge values.

**Question 8:** What is the space complexity?
- A) O(log n)
- B) O(1)
- C) O(n)
- D) O(log log n)
- **Correct Answer:** B
- **Explanation:** Only a constant number of index variables are maintained.

**Question 9:** The probe formula assumes values are spread:
- A) Randomly
- B) Linearly
- C) Logarithmically
- D) Exponentially
- **Correct Answer:** B
- **Explanation:** It assumes a linear relationship between index and value.

**Question 10:** If data is heavily clustered, which algorithm is safer?
- A) Interpolation Search
- B) Binary Search
- C) Linear Search
- D) Jump Search
- **Correct Answer:** B
- **Explanation:** Binary Search guarantees O(log n) regardless of value distribution.
