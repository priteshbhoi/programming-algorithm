# Ternary Search

## 1. Introduction

Ternary Search is a divide-and-conquer searching algorithm that splits the array into three parts instead of two. It finds two middle points and compares the target with the elements at those points to decide which of the three segments to search next. While it makes more comparisons per step than Binary Search, it is particularly useful for finding the maximum or minimum of a unimodal function, not just for searching sorted arrays.

It was created to solve optimization problems where you need to find the peak or valley of a function that first increases and then decreases (or vice versa). By dividing into three parts, it can more precisely narrow in on the extremum.

You should use Ternary Search when searching for an extremum (maximum or minimum) in a unimodal function, or when you want to explore a search technique that divides the space into three parts.

## 2. Why Use This Algorithm?

Ternary Search offers a different perspective on divide-and-conquer. While Binary Search is optimal for finding an exact value in a sorted array, Ternary Search excels at finding peaks and valleys.

**Benefits:**
- Excellent for finding extrema in unimodal functions
- Reduces the search space to one-third in each iteration for extremum finding
- Conceptually simple extension of Binary Search
- Works on both discrete arrays and continuous functions

**Performance:**
For finding an exact value in a sorted array, Ternary Search has O(log₃ n) time complexity, which is technically O(log n) but with a larger constant factor than Binary Search. For finding extrema, it also achieves O(log n) convergence.

**When it is better than other algorithms:**
Ternary Search is superior to Binary Search when you need to find the maximum or minimum of a unimodal function, because Binary Search cannot directly handle the "peak" scenario where the ordering changes direction.

## 3. Real-World Applications

- **Finding the maximum profit point:** In economics, finding the optimal price that maximizes revenue where demand decreases as price increases.
- **Optimizing resource allocation:** Finding the optimal number of workers or machines that maximizes output before diminishing returns set in.
- **Peak detection in signal processing:** Finding the highest amplitude in a signal that rises and then falls.
- **Numerical optimization:** Finding roots or extrema of mathematical functions in calculus.
- **Game theory:** Finding optimal strategies in continuous strategy spaces.

## 4. Prerequisites

Before learning Ternary Search, you should know:
- Binary Search thoroughly
- What a unimodal function is (increases to a peak then decreases, or vice versa)
- Arrays and indexing
- Basic calculus concepts (helpful but not mandatory for discrete versions)
- Comparison operators and conditional logic

## 5. Visualization

Imagine a mountain range with a single peak. You stand at two points that divide the range into three equal sections. If the left point is higher than the right point, you know the peak must be in the left two-thirds. If the right point is higher, the peak is in the right two-thirds. If they are equal, the peak is in the middle third. You then repeat this process on the smaller section, getting closer to the summit with each step.

## 6. How It Works

For finding an extremum in a unimodal function, Ternary Search calculates two points: `mid1` at one-third of the range and `mid2` at two-thirds. It compares the function values at these two points. If `f(mid1)` < `f(mid2)`, the maximum cannot be in the left third, so the left boundary moves to `mid1`. If `f(mid1)` > `f(mid2)`, the maximum cannot be in the right third, so the right boundary moves to `mid2`. If they are equal, both outer thirds are discarded. This process repeats until the range is sufficiently small.

For searching a sorted array, the logic is similar but compares against the target value at both midpoints.

## 7. Step-by-Step Algorithm

For finding a maximum in a unimodal function:

1. Set `left` to the start and `right` to the end of the range.
2. While `right - left` is greater than a small epsilon (or 1 for discrete):
   1. Calculate `mid1` as `left + (right - left) / 3`.
   2. Calculate `mid2` as `right - (right - left) / 3`.
   3. If `f(mid1)` < `f(mid2)`, set `left` to `mid1`.
   4. If `f(mid1)` > `f(mid2)`, set `right` to `mid2`.
   5. If equal, set `left` to `mid1` and `right` to `mid2`.
3. Return `left` (or `right`) as the approximate position of the maximum.

## 8. Pseudocode

```
function ternarySearch(left, right, function f):
    while right - left > epsilon:
        mid1 = left + (right - left) / 3
        mid2 = right - (right - left) / 3
        if f(mid1) < f(mid2):
            left = mid1
        else if f(mid1) > f(mid2):
            right = mid2
        else:
            left = mid1
            right = mid2
    return left
```

## 9. Code Examples

### C
```c
#include <stdio.h>

// Example unimodal function: parabola with maximum at x=5
double f(double x) {
    return -(x - 5) * (x - 5) + 25;
}

double ternarySearch(double left, double right, double epsilon) {
    while (right - left > epsilon) {
        double mid1 = left + (right - left) / 3.0;
        double mid2 = right - (right - left) / 3.0;
        if (f(mid1) < f(mid2))
            left = mid1;
        else if (f(mid1) > f(mid2))
            right = mid2;
        else {
            left = mid1;
            right = mid2;
        }
    }
    return left;
}

int main() {
    double left = 0.0, right = 10.0, epsilon = 0.001;
    double result = ternarySearch(left, right, epsilon);
    printf("Maximum is near x = %.3f, f(x) = %.3f\n", result, f(result));
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <cmath>
using namespace std;

double f(double x) {
    return -(x - 5) * (x - 5) + 25;
}

double ternarySearch(double left, double right, double epsilon) {
    while (right - left > epsilon) {
        double mid1 = left + (right - left) / 3.0;
        double mid2 = right - (right - left) / 3.0;
        if (f(mid1) < f(mid2))
            left = mid1;
        else if (f(mid1) > f(mid2))
            right = mid2;
        else {
            left = mid1;
            right = mid2;
        }
    }
    return left;
}

int main() {
    double left = 0.0, right = 10.0, epsilon = 0.001;
    double result = ternarySearch(left, right, epsilon);
    cout << "Maximum is near x = " << result << ", f(x) = " << f(result) << endl;
    return 0;
}
```

### Java
```java
public class TernarySearch {
    static double f(double x) {
        return -(x - 5) * (x - 5) + 25;
    }

    static double ternarySearch(double left, double right, double epsilon) {
        while (right - left > epsilon) {
            double mid1 = left + (right - left) / 3.0;
            double mid2 = right - (right - left) / 3.0;
            if (f(mid1) < f(mid2))
                left = mid1;
            else if (f(mid1) > f(mid2))
                right = mid2;
            else {
                left = mid1;
                right = mid2;
            }
        }
        return left;
    }

    public static void main(String[] args) {
        double left = 0.0, right = 10.0, epsilon = 0.001;
        double result = ternarySearch(left, right, epsilon);
        System.out.printf("Maximum is near x = %.3f, f(x) = %.3f%n", result, f(result));
    }
}
```

### Python
```python
def f(x):
    return -(x - 5) ** 2 + 25

def ternary_search(left, right, epsilon):
    while right - left > epsilon:
        mid1 = left + (right - left) / 3.0
        mid2 = right - (right - left) / 3.0
        if f(mid1) < f(mid2):
            left = mid1
        elif f(mid1) > f(mid2):
            right = mid2
        else:
            left = mid1
            right = mid2
    return left

left, right, epsilon = 0.0, 10.0, 0.001
result = ternary_search(left, right, epsilon)
print(f"Maximum is near x = {result:.3f}, f(x) = {f(result):.3f}")
```

### JavaScript
```javascript
function f(x) {
    return -(x - 5) ** 2 + 25;
}

function ternarySearch(left, right, epsilon) {
    while (right - left > epsilon) {
        const mid1 = left + (right - left) / 3.0;
        const mid2 = right - (right - left) / 3.0;
        if (f(mid1) < f(mid2)) {
            left = mid1;
        } else if (f(mid1) > f(mid2)) {
            right = mid2;
        } else {
            left = mid1;
            right = mid2;
        }
    }
    return left;
}

const left = 0.0, right = 10.0, epsilon = 0.001;
const result = ternarySearch(left, right, epsilon);
console.log(`Maximum is near x = ${result.toFixed(3)}, f(x) = ${f(result).toFixed(3)}`);
```

## 10. Code Explanation

The algorithm maintains a range `[left, right]` that is guaranteed to contain the maximum. In each iteration, it evaluates the function at two points that trisect the interval. The comparison of `f(mid1)` and `f(mid2)` tells us which third cannot contain the maximum. If `mid1` is lower, the maximum must be to the right of `mid1`, so we discard everything left of `mid1`. If `mid1` is higher, the maximum must be to the left of `mid2`, so we discard everything right of `mid2`. The epsilon determines when the interval is small enough to stop.

## 11. Interactive Demo

The demo displays a curve representing a unimodal function. Two vertical markers labeled "mid1" and "mid2" appear at the one-third and two-thirds positions. The user can click "Next Step" to watch the algorithm compare the heights at these two points. The portion of the curve that cannot contain the maximum fades out. The surviving range is highlighted, and new mid1 and mid2 markers appear within it. A status panel shows the current range, the function values at the midpoints, and the number of iterations. The user can adjust epsilon with a slider to see how precision affects the number of steps.

## 12. Dry Run

**Sample Function:** `f(x) = -(x - 5)² + 25` on range `[0, 10]`
Target: Find maximum, epsilon = `0.5`

| Step | Left | Right | Mid1 | Mid2 | f(Mid1) | f(Mid2) | Comparison | Action |
|------|------|-------|------|------|---------|---------|------------|--------|
| 1 | 0.0 | 10.0 | 3.33 | 6.67 | 22.22 | 22.22 | Equal | left=3.33, right=6.67 |
| 2 | 3.33 | 6.67 | 4.44 | 5.56 | 24.69 | 24.69 | Equal | left=4.44, right=5.56 |
| 3 | 4.44 | 5.56 | 4.81 | 5.19 | 24.96 | 24.96 | Equal | left=4.81, right=5.19 |

**Final Output:** Maximum near `x = 4.81` (approaching the true peak at x = 5)

## 13. Time & Space Complexity

| Case | Time Complexity | Reason |
|------|-----------------|--------|
| Best Case | O(1) | If the function is flat, mid1 equals mid2 immediately |
| Average Case | O(log n) | The range shrinks by one-third each iteration |
| Worst Case | O(log n) | The range converges to epsilon over logarithmic steps |
| Space Complexity | O(1) | Only left, right, mid1, and mid2 are stored |

## 14. Advantages

- **Optimal for unimodal functions:** Specifically designed to find peaks and valleys efficiently.
- **Works on continuous functions:** Unlike array-based searches, it can optimize real-valued functions.
- **Simple extension of Binary Search:** Easy to understand if you already know Binary Search.
- **Guaranteed convergence:** The range always shrinks, ensuring the extremum is found.
- **No derivatives needed:** Unlike Newton's method, it does not require calculating derivatives.

## 15. Disadvantages

- **More comparisons per step than Binary Search:** Two function evaluations per iteration instead of one.
- **Requires unimodality:** If the function has multiple peaks, Ternary Search may converge to a local extremum or fail.
- **Not optimal for exact value search:** Binary Search is faster for finding a specific value in a sorted array.
- **Epsilon dependency:** The precision depends on choosing an appropriate epsilon; too small wastes time, too large reduces accuracy.

## 16. Applications

- Finding the optimal price point for maximum revenue
- Determining the best resource allocation before diminishing returns
- Signal processing peak detection
- Numerical optimization in scientific computing
- Game theory strategy optimization
- Machine learning hyperparameter tuning on unimodal loss curves

## 17. Common Mistakes

- **Applying to non-unimodal functions:** Ternary Search assumes a single peak; multiple peaks will cause incorrect results.
- **Wrong epsilon choice:** Setting epsilon too large gives imprecise results; too small causes unnecessary iterations.
- **Integer division errors:** When working with discrete arrays, ensure mid1 and mid2 are calculated with proper integer arithmetic.
- **Confusing maximum and minimum:** The comparison operators must be reversed if searching for a minimum instead of a maximum.

## 18. Interview Questions

1. What is the main difference between Binary Search and Ternary Search?
2. What type of function is required for Ternary Search to work correctly?
3. What is the time complexity of Ternary Search?
4. Why does Ternary Search make two comparisons per iteration?
5. Can Ternary Search be used to find a specific value in a sorted array? Is it efficient?
6. How would you modify Ternary Search to find a minimum instead of a maximum?
7. What happens if you apply Ternary Search to a function with multiple peaks?
8. Compare Ternary Search with gradient descent for finding function extrema.
9. How do you choose an appropriate epsilon value?
10. Can Ternary Search be generalized to divide the range into k parts instead of 3?

## 19. Practice Problems

**Easy:**
1. Implement Ternary Search to find the maximum of a unimodal function.
2. Modify the code to find the minimum of a unimodal function.
3. Apply Ternary Search to find the peak element in a mountain array.
4. Trace through Ternary Search manually for a given function and range.

**Medium:**
5. Implement Ternary Search on a discrete array to find a target value.
6. Use Ternary Search to find the maximum area of a rectangle inscribed in a parabola.
7. Apply Ternary Search to optimize the throwing angle for maximum projectile distance.
8. Implement a recursive version of Ternary Search.

**Hard:**
9. Create a k-ary search generalization where the range is divided into k parts.
10. Combine Ternary Search with gradient information to accelerate convergence.
11. Apply Ternary Search to a 2D unimodal function (finding a peak in a 2D grid).

## 20. Related Algorithms

- Binary Search (divides into two parts)
- Golden Section Search (uses the golden ratio instead of thirds)
- Gradient Descent (uses derivatives for optimization)
- Newton's Method (uses second derivatives for faster convergence)
- Hill Climbing (greedy approach to finding extrema)

## 21. Summary

Ternary Search is a powerful divide-and-conquer algorithm that divides the search space into three parts. While it is not as efficient as Binary Search for finding exact values in sorted arrays, it excels at finding extrema in unimodal functions. Its ability to work on both discrete arrays and continuous functions makes it a versatile tool in optimization problems. Remember to verify that your function is truly unimodal before applying Ternary Search, and choose your epsilon carefully to balance precision with performance.

## 22. Quiz

**Question 1:** How many parts does Ternary Search divide the range into?
- A) 2
- B) 3
- C) 4
- D) n
- **Correct Answer:** B
- **Explanation:** The name "ternary" refers to the three-way division of the search space.

**Question 2:** What type of function is required for Ternary Search?
- A) Linear
- B) Unimodal
- C) Exponential
- D) Periodic
- **Correct Answer:** B
- **Explanation:** The algorithm assumes the function has a single peak or valley.

**Question 3:** What is the time complexity of Ternary Search?
- A) O(n)
- B) O(log n)
- C) O(sqrt(n))
- D) O(1)
- **Correct Answer:** B
- **Explanation:** The range shrinks by a constant fraction each iteration, giving logarithmic time.

**Question 4:** How many function evaluations are made per iteration?
- A) 1
- B) 2
- C) 3
- D) 4
- **Correct Answer:** B
- **Explanation:** It evaluates the function at mid1 and mid2 in each step.

**Question 5:** Ternary Search is best suited for:
- A) Searching unsorted arrays
- B) Finding extrema in unimodal functions
- C) Sorting data
- D) Hash table lookups
- **Correct Answer:** B
- **Explanation:** Its primary strength is finding maximum or minimum points.

**Question 6:** What happens if f(mid1) < f(mid2)?
- A) The right portion is discarded
- B) The left portion is discarded
- C) The middle portion is discarded
- D) The search terminates
- **Correct Answer:** B
- **Explanation:** If mid1 is lower, the maximum cannot be in the left third.

**Question 7:** What is the space complexity?
- A) O(log n)
- B) O(1)
- C) O(n)
- D) O(3^n)
- **Correct Answer:** B
- **Explanation:** Only a constant number of boundary variables are maintained.

**Question 8:** What determines when the algorithm stops?
- A) Finding the exact maximum
- B) The range becoming smaller than epsilon
- C) A fixed number of iterations
- D) Reaching the array boundary
- **Correct Answer:** B
- **Explanation:** For continuous functions, epsilon defines the acceptable precision.

**Question 9:** Why is Ternary Search less efficient than Binary Search for exact value lookup?
- A) It uses more memory
- B) It makes more comparisons per step
- C) It only works on integers
- D) It requires sorting
- **Correct Answer:** B
- **Explanation:** Two comparisons per iteration give it a higher constant factor.

**Question 10:** Which of these is a valid application of Ternary Search?
- A) Finding a word in a dictionary
- B) Finding the optimal price for maximum profit
- C) Sorting an array of numbers
- D) Building a hash table
- **Correct Answer:** B
- **Explanation:** Profit as a function of price is often unimodal, making Ternary Search ideal.
