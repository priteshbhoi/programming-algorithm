# Binary Search on Answer

## 1. Introduction

Binary Search on Answer is not a search algorithm in the traditional sense. Instead of looking for a specific element in an array, it uses the principles of Binary Search to find the optimal answer to a problem within a range of possible values. Imagine you are trying to find the minimum speed a train needs to arrive on time. You do not have a sorted list of "correct speeds." Instead, you can test any speed and know whether it is fast enough or too slow. Binary Search on Answer lets you efficiently narrow down to the exact minimum speed by repeatedly testing the middle of your current range.

It was created to solve optimization problems where the answer space is monotonic. That means if a certain value is valid, all larger (or smaller) values are also valid. This monotonicity property allows us to apply the same halving strategy that makes Binary Search so powerful.

You should use Binary Search on Answer whenever you face a problem where you need to minimize or maximize some value, and you can write a function that checks whether a given candidate value is feasible.

## 2. Why Use This Algorithm?

Binary Search on Answer transforms seemingly difficult optimization problems into efficient O(log(range)) solutions.

**Benefits:**
- Solves optimization problems that resist greedy or dynamic programming approaches
- Extremely efficient when the answer space is large
- Simple to implement once the feasibility check is written
- Works on integers, floating-point numbers, and even abstract ranges

**Performance:**
If the answer lies in a range from 1 to 10^9, you can find it in about 30 iterations. Each iteration requires evaluating a feasibility function, which is typically O(n) or better. This gives an overall complexity of O(n log(range)), which is often dramatically faster than brute force.

**When it is better than other algorithms:**
It is better than brute force when the answer space is large. It is better than pure mathematical formulas when the feasibility check involves complex constraints that are hard to solve algebraically.

## 3. Real-World Applications

- **Finding the minimum maximum distance between aggressive cows:** A classic competitive programming problem where you need to place cows in stalls to maximize the minimum distance.
- **Determining the minimum time to complete a task with parallel workers:** Finding the minimum time needed when multiple machines work at different speeds.
- **Finding the square root of a number:** One of the simplest applications, where you binary search the value space.
- **Capacity planning:** Finding the minimum server capacity needed to handle a given load within response time constraints.
- **Resource allocation:** Finding the minimum budget that satisfies all project requirements.
- **Painters partition problem:** Finding the minimum time to paint all boards when painters cannot paint consecutive boards.

## 4. Prerequisites

Before learning Binary Search on Answer, you should know:
- Standard Binary Search thoroughly
- How to identify monotonic predicates
- Basic optimization problem solving
- How to write a feasibility or validation function
- Understanding of integer overflow and precision issues

## 5. Visualization

Imagine a number line representing all possible answers. The left end is "definitely too small" and the right end is "definitely big enough." You place a marker in the middle and test it. If the middle value works, you know the true answer is at or below that point, so you move the right boundary left. If it does not work, the answer must be higher, so you move the left boundary right. The "works" region slowly converges until the boundaries touch, revealing the exact threshold where "too small" becomes "just right."

## 6. How It Works

Binary Search on Answer requires two things: a search range `[low, high]` and a boolean function `isFeasible(value)` that returns true if the given value is a valid (or sufficient) answer. The key insight is that if `isFeasible(x)` is true, then all values greater than x are also true (for minimization problems), or all values less than x are also true (for maximization problems). This monotonicity lets us discard half the range each iteration.

## 7. Step-by-Step Algorithm

1. Determine the minimum possible answer (`low`) and maximum possible answer (`high`).
2. While `low` < `high`:
   1. Calculate `mid` as `low + (high - low) / 2`.
   2. Call `isFeasible(mid)`.
   3. If feasible and searching for minimum: set `high` = `mid`.
   4. If not feasible and searching for minimum: set `low` = `mid + 1`.
   5. If feasible and searching for maximum: set `low` = `mid`.
   6. If not feasible and searching for maximum: set `high` = `mid - 1`.
3. Return `low` (or `high`, they will be equal).

## 8. Pseudocode

```
function binarySearchOnAnswer(low, high, isFeasible):
    while low < high:
        mid = low + (high - low) / 2
        if isFeasible(mid):
            high = mid       // for minimization
        else:
            low = mid + 1    // for minimization
    return low
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdbool.h>

// Example: Find minimum capacity to ship packages within D days
int weights[] = {1, 2, 3, 4, 5};
int n = 5;
int D = 3;

bool isFeasible(int capacity) {
    int days = 1, current = 0;
    for (int i = 0; i < n; i++) {
        if (current + weights[i] <= capacity) {
            current += weights[i];
        } else {
            days++;
            current = weights[i];
            if (days > D) return false;
        }
    }
    return true;
}

int binarySearchOnAnswer() {
    int low = 5;  // max weight
    int high = 15; // sum of all weights
    while (low < high) {
        int mid = low + (high - low) / 2;
        if (isFeasible(mid))
            high = mid;
        else
            low = mid + 1;
    }
    return low;
}

int main() {
    printf("Minimum capacity: %d\n", binarySearchOnAnswer());
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
using namespace std;

vector<int> weights = {1, 2, 3, 4, 5};
int D = 3;

bool isFeasible(int capacity) {
    int days = 1, current = 0;
    for (int w : weights) {
        if (current + w <= capacity) {
            current += w;
        } else {
            days++;
            current = w;
            if (days > D) return false;
        }
    }
    return true;
}

int binarySearchOnAnswer() {
    int low = 5;
    int high = 15;
    while (low < high) {
        int mid = low + (high - low) / 2;
        if (isFeasible(mid))
            high = mid;
        else
            low = mid + 1;
    }
    return low;
}

int main() {
    cout << "Minimum capacity: " << binarySearchOnAnswer() << endl;
    return 0;
}
```

### Java
```java
public class BinarySearchOnAnswer {
    static int[] weights = {1, 2, 3, 4, 5};
    static int D = 3;

    static boolean isFeasible(int capacity) {
        int days = 1, current = 0;
        for (int w : weights) {
            if (current + w <= capacity) {
                current += w;
            } else {
                days++;
                current = w;
                if (days > D) return false;
            }
        }
        return true;
    }

    static int binarySearchOnAnswer() {
        int low = 5;
        int high = 15;
        while (low < high) {
            int mid = low + (high - low) / 2;
            if (isFeasible(mid))
                high = mid;
            else
                low = mid + 1;
        }
        return low;
    }

    public static void main(String[] args) {
        System.out.println("Minimum capacity: " + binarySearchOnAnswer());
    }
}
```

### Python
```python
weights = [1, 2, 3, 4, 5]
D = 3

def is_feasible(capacity):
    days = 1
    current = 0
    for w in weights:
        if current + w <= capacity:
            current += w
        else:
            days += 1
            current = w
            if days > D:
                return False
    return True

def binary_search_on_answer():
    low = 5
    high = 15
    while low < high:
        mid = low + (high - low) // 2
        if is_feasible(mid):
            high = mid
        else:
            low = mid + 1
    return low

print(f"Minimum capacity: {binary_search_on_answer()}")
```

### JavaScript
```javascript
const weights = [1, 2, 3, 4, 5];
const D = 3;

function isFeasible(capacity) {
    let days = 1, current = 0;
    for (const w of weights) {
        if (current + w <= capacity) {
            current += w;
        } else {
            days++;
            current = w;
            if (days > D) return false;
        }
    }
    return true;
}

function binarySearchOnAnswer() {
    let low = 5;
    let high = 15;
    while (low < high) {
        const mid = low + Math.floor((high - low) / 2);
        if (isFeasible(mid))
            high = mid;
        else
            low = mid + 1;
    }
    return low;
}

console.log(`Minimum capacity: ${binarySearchOnAnswer()}`);
```

## 10. Code Explanation

The `isFeasible` function is the heart of the algorithm. It simulates the problem constraints for a given candidate answer. In the shipping example, it tries to pack weights into days without exceeding the capacity. If it succeeds within the allowed days, the capacity is feasible. The Binary Search loop then narrows the range. Notice the loop condition is `low < high` rather than `low <= high`. This is because we are searching for a boundary value, not an index. When `low == high`, we have found the exact threshold.

## 11. Interactive Demo

The demo shows a horizontal slider representing the answer range. The user can see a visualization of the feasibility check. For the shipping example, blocks representing packages are shown, and the demo attempts to pack them into days using the current capacity. Green days indicate success; red indicates failure. The user clicks "Next Step" to watch the binary search narrow the range. A status panel shows the current low, high, mid, and whether mid was feasible. The final result is highlighted when low equals high.

## 12. Dry Run

**Sample Problem:** Minimum capacity to ship `[1, 2, 3, 4, 5]` in 3 days.
Range: `[5, 15]`

| Step | Low | High | Mid | isFeasible(Mid) | Action |
|------|-----|------|-----|-----------------|--------|
| 1 | 5 | 15 | 10 | Yes (1+2+3=6, 4, 5) | high = 10 |
| 2 | 5 | 10 | 7 | No (1+2=3, 3+4=7>7) | low = 8 |
| 3 | 8 | 10 | 9 | Yes (1+2+3=6, 4, 5) | high = 9 |
| 4 | 8 | 9 | 8 | No (1+2+3=6, 4, 5... wait, 1+2+3=6, 4, 5 fits in 3 days) | high = 8 |
| 5 | 8 | 8 | - | - | Return 8 |

**Final Output:** Minimum capacity = `8`

## 13. Time & Space Complexity

| Case | Time Complexity | Reason |
|------|-----------------|--------|
| Best Case | O(log(range)) | The feasibility function is cheap and the answer is found quickly |
| Average Case | O(f(n) * log(range)) | f(n) is the cost of the feasibility check |
| Worst Case | O(f(n) * log(range)) | Full binary search over the entire range |
| Space Complexity | O(1) | Only low, high, and mid are stored (excluding feasibility function space) |

## 14. Advantages

- **Solves hard optimization problems:** Many problems that seem to require complex DP or greedy solutions become simple with this approach.
- **Logarithmic search over answer space:** Even if the answer can be as large as 10^18, it finds it in about 60 iterations.
- **Flexible:** Works on any problem with a monotonic feasibility predicate.
- **Easy to implement:** Once you can write the feasibility check, the binary search part is standard.

## 15. Disadvantages

- **Requires monotonicity:** If the feasibility function is not monotonic, Binary Search on Answer will give wrong results.
- **Depends on feasibility function efficiency:** If the check is O(n^2) and you call it 60 times, the total cost adds up.
- **Range must be bounded:** You need to know reasonable low and high bounds.
- **Precision issues with floats:** For floating-point answers, you need an epsilon and may need many iterations for high precision.

## 16. Applications

- Minimum maximum partition sums (painters partition, book allocation)
- Aggressive cows and similar placement problems
- Finding square roots and other mathematical approximations
- Minimum time to complete tasks with parallel processing
- Capacity planning and resource allocation
- Minimum speed to arrive on time

## 17. Common Mistakes

- **Incorrect monotonicity assumption:** Always verify that if x works, then x+1 also works (or vice versa).
- **Wrong boundary updates:** For minimization, `high = mid` and `low = mid + 1`. For maximization, `low = mid` and `high = mid - 1`. Mixing these up causes infinite loops or wrong answers.
- **Integer overflow in mid:** Use `low + (high - low) / 2`.
- **Infinite loops with `low < high`:** Ensure the updates actually shrink the range.
- **Poor feasibility function:** An inefficient check dominates the runtime.

## 18. Interview Questions

1. What is the key property that must hold for Binary Search on Answer to work?
2. How does Binary Search on Answer differ from standard Binary Search?
3. Write a feasibility function for the "aggressive cows" problem.
4. What is the time complexity if the feasibility check is O(n) and the range is 1 to 10^9?
5. How would you modify the algorithm to find a maximum instead of a minimum?
6. Can Binary Search on Answer work with floating-point ranges?
7. What happens if the feasibility predicate is not monotonic?
8. How do you choose the initial low and high bounds?
9. Implement Binary Search on Answer to find the integer square root of a number.
10. Compare Binary Search on Answer with dynamic programming for optimization problems.

## 19. Practice Problems

**Easy:**
1. Find the integer square root of a number using Binary Search on Answer.
2. Find the minimum capacity to ship packages within D days.
3. Find the minimum eating speed to finish all bananas within H hours.
4. Find the smallest divisor given a threshold.

**Medium:**
5. Allocate minimum number of pages to students (painters partition).
6. Place aggressive cows in stalls to maximize minimum distance.
7. Find the minimum time to complete all tasks with K workers.
8. Find the minimum maximum distance between gas stations.

**Hard:**
9. Minimize the maximum difference between heights of towers.
10. Find the minimum possible largest sum among m subarrays.
11. Optimize the minimum maximum latency in a network routing problem.

## 20. Related Algorithms

- Binary Search (the foundation)
- Greedy Algorithms (often used inside feasibility checks)
- Dynamic Programming (alternative for some optimization problems)
- Parametric Search (generalization of Binary Search on Answer)
- Newton's Method (for continuous optimization)

## 21. Summary

Binary Search on Answer is a powerful technique that applies the halving strategy of Binary Search to optimization problems. Instead of searching for an element in an array, you search for the optimal value in a range of possible answers. The key requirement is a monotonic feasibility function that can test any candidate value. With this approach, problems that seem to require complex algorithms can often be solved in O(log(range)) iterations. Mastering this technique will significantly expand the types of problems you can solve efficiently.

## 22. Quiz

**Question 1:** What is the main requirement for Binary Search on Answer to work?
- A) The array must be sorted
- B) The feasibility predicate must be monotonic
- C) The answer must be an integer
- D) The data must be in a linked list
- **Correct Answer:** B
- **Explanation:** Monotonicity ensures that if a value is feasible, all values on one side are also feasible.

**Question 2:** What does the algorithm search over?
- A) Array indices
- B) A range of possible answer values
- C) Linked list nodes
- D) Hash table keys
- **Correct Answer:** B
- **Explanation:** It searches the space of potential answers, not the input data itself.

**Question 3:** What is the typical time complexity?
- A) O(n)
- B) O(f(n) * log(range))
- C) O(log n)
- D) O(n^2)
- **Correct Answer:** B
- **Explanation:** Each of the log(range) iterations calls the feasibility function, which costs f(n).

**Question 4:** For a minimization problem, what happens if mid is feasible?
- A) low = mid + 1
- B) high = mid
- C) low = mid
- D) high = mid - 1
- **Correct Answer:** B
- **Explanation:** If mid works, the optimal answer is at or below mid, so we shrink the upper bound.

**Question 5:** Can Binary Search on Answer find floating-point answers?
- A) No, only integers
- B) Yes, with an epsilon threshold
- C) Only with special hardware
- D) Only for square roots
- **Correct Answer:** B
- **Explanation:** You can search a continuous range by stopping when the range is smaller than epsilon.

**Question 6:** What is the space complexity?
- A) O(n)
- B) O(1)
- C) O(log(range))
- D) O(range)
- **Correct Answer:** B
- **Explanation:** Only a few variables track the search boundaries.

**Question 7:** Which of these is a classic application?
- A) Sorting an array
- B) Finding minimum capacity to ship packages
- C) Reversing a string
- D) Building a heap
- **Correct Answer:** B
- **Explanation:** The shipping capacity problem is a textbook Binary Search on Answer problem.

**Question 8:** What happens if the feasibility check is O(n^2) and the range is 10^9?
- A) The algorithm is instant
- B) It becomes too slow for large n
- C) It uses more memory
- D) It becomes O(1)
- **Correct Answer:** B
- **Explanation:** 60 iterations times O(n^2) can be prohibitive for large n.

**Question 9:** Why is `low + (high - low) / 2` preferred over `(low + high) / 2`?
- A) It is faster
- B) It prevents integer overflow
- C) It is more accurate
- D) It works with negative numbers
- **Correct Answer:** B
- **Explanation:** Adding two large integers can overflow; the subtraction form is safer.

**Question 10:** Binary Search on Answer is best described as:
- A) A sorting algorithm
- B) An optimization technique using binary search principles
- C) A hashing technique
- D) A graph traversal
- **Correct Answer:** B
- **Explanation:** It applies binary search to find optimal values in monotonic answer spaces.
