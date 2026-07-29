# Fenwick Tree (Binary Indexed Tree)

## 1. Introduction
A **Fenwick Tree**, also known as a **Binary Indexed Tree (BIT)**, is a specialized data structure that efficiently updates elements and calculates prefix sums in an array of values. 

**Real-World Analogy:**
Imagine you are managing a large bank with many accounts, and you need to frequently ask "What is the total balance in the first $N$ accounts?" while also constantly receiving updates like "Add $100$ to account $X$." If you keep a simple array, calculating the sum takes $O(N)$ time. If you keep an array of pre-computed prefix sums, updating a single account balance requires updating all subsequent prefix sums, taking $O(N)$ time. A Fenwick Tree acts like a smart middle-manager system where you only need to update a few strategic total-tracking bins (taking $O(\log N)$ time) and can query the total by looking at just a few bins (also $O(\log N)$ time).

## 2. Why Use This Algorithm?
The Fenwick tree solves the dynamic prefix sum problem. When you have an array and need to perform two operations efficiently:
1.  **Point Update**: Add a value to a specific index.
2.  **Range Query**: Calculate the sum of elements from index 1 to $k$ (prefix sum).

Using a standard array, updating takes $O(1)$ and querying takes $O(N)$. Using a prefix sum array, querying takes $O(1)$ but updating takes $O(N)$. A Fenwick Tree elegantly balances this by performing both operations in **$O(\log N)$** time. It requires less memory and is easier to code than a Segment Tree.

## 3. Real-World Applications
- **Financial Systems:** Keeping track of running balances over time where both updates and queries are frequent.
- **Data Compression:** Arithmetic coding, where dynamic cumulative frequency tables are maintained.
- **Inversion Counting:** Finding the number of inversions in an array efficiently (useful in ranking and recommendation systems).
- **Network Traffic Monitoring:** Calculating cumulative data packets sent/received over a dynamic set of IP blocks.

## 4. Prerequisites
To fully understand the Fenwick Tree, you should be familiar with:
- Arrays and basic 1D prefix sums.
- **Bitwise Operations:** Specifically, the bitwise AND (`&`), two's complement (`-x`), and how to extract the lowest set bit (`x & (-x)`).
- Time Complexity (Big O notation).

## 5. Visualization

A Fenwick tree for an array of size 8 can be visualized as an array `BIT[]` where each element stores the sum of a specific range of elements from the original array `A[]`.

```text
Original Array A (1-indexed): [a1, a2, a3, a4, a5, a6, a7, a8]

BIT Array Structure:
Index 1 (0001): stores a1 (length 1)
Index 2 (0010): stores a1 + a2 (length 2)
Index 3 (0011): stores a3 (length 1)
Index 4 (0100): stores a1 + a2 + a3 + a4 (length 4)
Index 5 (0101): stores a5 (length 1)
Index 6 (0110): stores a5 + a6 (length 2)
Index 7 (0111): stores a7 (length 1)
Index 8 (1000): stores a1 + a2 + a3 + a4 + a5 + a6 + a7 + a8 (length 8)
```
Each index $i$ is responsible for the sum of elements from $i - (i \& -i) + 1$ to $i$.

## 6. How It Works
The magic of the Fenwick Tree lies in binary representation. Every integer can be represented as a sum of powers of 2. Similarly, we can calculate a prefix sum by summing up non-overlapping sub-arrays whose lengths are powers of 2.

**Isolating the last set bit (LSB):**
The operation `x & (-x)` gives the lowest set bit of $x$. For example, if $x = 10$ (binary `1010`), `-x` is `...11110110`. `1010 & 0110 = 0010` (which is 2). This means index 10 covers 2 elements (index 9 and 10).

- **Querying (Prefix Sum up to $i$):** Start at index $i$. Add `BIT[i]` to the sum. Then, remove the last set bit of $i$ (`i = i - (i & -i)`) to move to the previous responsible segment. Repeat until $i = 0$.
- **Updating (Adding $val$ at index $i$):** Start at index $i$. Add $val$ to `BIT[i]`. Then, add the last set bit of $i$ (`i = i + (i & -i)`) to find the next segment that includes this element. Repeat until $i$ exceeds the array size.

## 7. Step-by-Step Algorithm
### Construction / Update
1. Initialize an array `BIT` of size $N+1$ with all zeros.
2. For each element in the original array at index $i$ (1-indexed) with value $v$:
   1. Set $idx = i$.
   2. While $idx \le N$:
      1. `BIT[idx] += v`
      2. $idx = idx + (idx\ \&\ -idx)$

### Prefix Sum Query (from 1 to $i$)
1. Initialize $sum = 0$.
2. Set $idx = i$.
3. While $idx > 0$:
   1. $sum += BIT[idx]$
   2. $idx = idx - (idx\ \&\ -idx)$
4. Return $sum$.

## 8. Pseudocode

```text
function update(BIT, N, index, val):
    while index <= N:
        BIT[index] = BIT[index] + val
        index = index + (index & (-index))

function query(BIT, index):
    sum = 0
    while index > 0:
        sum = sum + BIT[index]
        index = index - (index & (-index))
    return sum
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>

void update(int *BIT, int n, int index, int val) {
    while (index <= n) {
        BIT[index] += val;
        index += index & (-index);
    }
}

int query(int *BIT, int index) {
    int sum = 0;
    while (index > 0) {
        sum += BIT[index];
        index -= index & (-index);
    }
    return sum;
}

int main() {
    int arr[] = {3, 2, -1, 6, 5, 4, -3, 3, 7, 2, 3};
    int n = sizeof(arr) / sizeof(arr[0]);
    int *BIT = (int *)calloc(n + 1, sizeof(int));
    
    // Build Fenwick Tree
    for (int i = 0; i < n; i++) {
        update(BIT, n, i + 1, arr[i]);
    }
    
    printf("Prefix sum of first 5 elements: %d\n", query(BIT, 5)); // 3+2-1+6+5 = 15
    
    // Update: add 2 to element at index 3 (0-indexed, so 4th element, originally 6)
    update(BIT, n, 4, 2);
    
    printf("Prefix sum of first 5 elements after update: %d\n", query(BIT, 5)); // 15 + 2 = 17
    
    free(BIT);
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>

using namespace std;

class FenwickTree {
private:
    vector<int> BIT;
    int n;

public:
    FenwickTree(int size) {
        n = size;
        BIT.assign(n + 1, 0);
    }

    void update(int index, int val) {
        while (index <= n) {
            BIT[index] += val;
            index += index & (-index);
        }
    }

    int query(int index) {
        int sum = 0;
        while (index > 0) {
            sum += BIT[index];
            index -= index & (-index);
        }
        return sum;
    }
    
    int queryRange(int left, int right) {
        return query(right) - query(left - 1);
    }
};

int main() {
    vector<int> arr = {3, 2, -1, 6, 5, 4, -3, 3, 7, 2, 3};
    FenwickTree ft(arr.size());
    
    for (int i = 0; i < arr.size(); i++) {
        ft.update(i + 1, arr[i]);
    }
    
    cout << "Prefix sum of first 5 elements: " << ft.query(5) << endl;
    cout << "Sum from index 2 to 6 (1-indexed): " << ft.queryRange(2, 6) << endl;
    
    ft.update(4, 2); // Add 2 to the 4th element
    
    cout << "Sum from index 2 to 6 after update: " << ft.queryRange(2, 6) << endl;
    
    return 0;
}
```

### Java
```java
public class FenwickTree {
    private int[] BIT;
    private int n;

    public FenwickTree(int size) {
        n = size;
        BIT = new int[n + 1];
    }

    public void update(int index, int val) {
        while (index <= n) {
            BIT[index] += val;
            index += index & (-index);
        }
    }

    public int query(int index) {
        int sum = 0;
        while (index > 0) {
            sum += BIT[index];
            index -= index & (-index);
        }
        return sum;
    }

    public int queryRange(int left, int right) {
        return query(right) - query(left - 1);
    }

    public static void main(String[] args) {
        int[] arr = {3, 2, -1, 6, 5, 4, -3, 3, 7, 2, 3};
        FenwickTree ft = new FenwickTree(arr.length);
        
        for (int i = 0; i < arr.length; i++) {
            ft.update(i + 1, arr[i]);
        }
        
        System.out.println("Prefix sum of first 5 elements: " + ft.query(5));
        
        ft.update(4, 2);
        
        System.out.println("Prefix sum of first 5 elements after update: " + ft.query(5));
    }
}
```

### Python
```python
class FenwickTree:
    def __init__(self, size):
        self.n = size
        self.bit = [0] * (self.n + 1)

    def update(self, index, val):
        while index <= self.n:
            self.bit[index] += val
            index += index & (-index)

    def query(self, index):
        total_sum = 0
        while index > 0:
            total_sum += self.bit[index]
            index -= index & (-index)
        return total_sum
        
    def query_range(self, left, right):
        return self.query(right) - self.query(left - 1)

if __name__ == "__main__":
    arr = [3, 2, -1, 6, 5, 4, -3, 3, 7, 2, 3]
    ft = FenwickTree(len(arr))
    
    for i in range(len(arr)):
        ft.update(i + 1, arr[i])
        
    print(f"Prefix sum of first 5 elements: {ft.query(5)}")
    print(f"Range sum (index 2 to 6, 1-based): {ft.query_range(2, 6)}")
    
    ft.update(4, 2)
    print(f"Range sum (index 2 to 6) after update: {ft.query_range(2, 6)}")
```

### JavaScript
```javascript
class FenwickTree {
    constructor(size) {
        this.n = size;
        this.bit = new Array(this.n + 1).fill(0);
    }

    update(index, val) {
        while (index <= this.n) {
            this.bit[index] += val;
            index += index & (-index);
        }
    }

    query(index) {
        let sum = 0;
        while (index > 0) {
            sum += this.bit[index];
            index -= index & (-index);
        }
        return sum;
    }
    
    queryRange(left, right) {
        return this.query(right) - this.query(left - 1);
    }
}

// Demo
const arr = [3, 2, -1, 6, 5, 4, -3, 3, 7, 2, 3];
const ft = new FenwickTree(arr.length);

for (let i = 0; i < arr.length; i++) {
    ft.update(i + 1, arr[i]);
}

console.log(`Prefix sum of first 5 elements: ${ft.query(5)}`);
ft.update(4, 2);
console.log(`Prefix sum of first 5 elements after update: ${ft.query(5)}`);
```

## 10. Code Explanation
1. **1-based Indexing**: The Fenwick tree is designed to work with 1-based indexing. The array `BIT` is initialized to size $N+1$.
2. **`update(index, val)`**: 
   - Starts at `index`. Adds `val` to `BIT[index]`.
   - Modifies `index` by adding its lowest set bit: `index += index & (-index)`. This jumps to the next index in the BIT that also covers this element.
3. **`query(index)`**: 
   - Computes the sum from 1 to `index`.
   - Starts at `index`. Adds `BIT[index]` to the running total.
   - Modifies `index` by subtracting its lowest set bit: `index -= index & (-index)`. This jumps backward to the next non-overlapping segment.
4. **`queryRange(left, right)`**:
   - Uses the prefix sum logic to compute sums within any range: `query(right) - query(left - 1)`.

## 11. Interactive Demo Description
A great interactive demo for the Fenwick Tree would feature:
- A grid representing the original array alongside another grid representing the BIT array.
- When the user clicks an element in the original array to "update" it, highlight the corresponding initial cell in the BIT array.
- An animated arrow or highlight that jumps rightward to show which subsequent cells in the BIT are updated as `index += index & (-index)` is executed.
- A query mode where the user specifies an index $k$, and the demo highlights the non-overlapping ranges (power of 2 sized blocks) in the BIT array that are summed up to give the result.

## 12. Dry Run
Let's build a Fenwick Tree for `arr = [3, 2, -1, 6]`. Size $N = 4$.
`BIT = [0, 0, 0, 0, 0]`

**Update 1: i=1, val=3**
- `idx = 1`. `BIT[1] += 3` -> `BIT = [0, 3, 0, 0, 0]`
- `idx += (1 & -1)` -> `idx = 1 + 1 = 2`
- `idx = 2`. `BIT[2] += 3` -> `BIT = [0, 3, 3, 0, 0]`
- `idx += (2 & -2)` -> `idx = 2 + 2 = 4`
- `idx = 4`. `BIT[4] += 3` -> `BIT = [0, 3, 3, 0, 3]`
- `idx += (4 & -4)` -> `idx = 4 + 4 = 8` (out of bounds)

**Update 2: i=2, val=2**
- `idx = 2`. `BIT[2] += 2` -> `BIT = [0, 3, 5, 0, 3]`
- `idx += (2 & -2)` -> `idx = 2 + 2 = 4`
- `idx = 4`. `BIT[4] += 2` -> `BIT = [0, 3, 5, 0, 5]`

**Update 3: i=3, val=-1**
- `idx = 3`. `BIT[3] += -1` -> `BIT = [0, 3, 5, -1, 5]`
- `idx += (3 & -3)` -> `idx = 3 + 1 = 4`
- `idx = 4`. `BIT[4] += -1` -> `BIT = [0, 3, 5, -1, 4]`

**Update 4: i=4, val=6**
- `idx = 4`. `BIT[4] += 6` -> `BIT = [0, 3, 5, -1, 10]`

Final BIT: `[0, 3, 5, -1, 10]`

**Query prefix sum up to index 3:**
- `idx = 3`, `sum = 0`
- `sum += BIT[3]` -> `sum = -1`
- `idx -= (3 & -3)` -> `idx = 3 - 1 = 2`
- `sum += BIT[2]` -> `sum = -1 + 5 = 4`
- `idx -= (2 & -2)` -> `idx = 2 - 2 = 0` (loop ends)
- Result: 4. (Original array: 3 + 2 - 1 = 4. Correct!)

## 13. Time & Space Complexity

| Operation | Complexity | Reason |
|-----------|------------|--------|
| **Building (Best/Avg/Worst)** | $O(N \log N)$ | Performing $N$ updates, each taking $O(\log N)$ time. (An $O(N)$ build algorithm exists as well) |
| **Point Update** | $O(\log N)$ | The index jumps by its least significant bit. The number of set bits in an index $\le N$ is at most $\log_2(N)$. |
| **Range Query** | $O(\log N)$ | Similar to update, we clear the least significant bit iteratively. At most $\log_2(N)$ operations. |
| **Space Complexity** | $O(N)$ | Requires an additional array `BIT` of size $N+1$. |

## 14. Advantages
- **Fast:** Both point updates and prefix/range queries take logarithmic time.
- **Space Efficient:** Needs exactly $O(N)$ memory, with a small constant factor compared to a Segment Tree (which takes $4N$ memory).
- **Simple to Implement:** The code is incredibly short (just a few lines) and only uses bitwise operations, resulting in very fast execution with a small constant overhead.

## 15. Disadvantages
- **Limited Operations:** Primarily suited for reversible operations like sum, multiplication, or XOR. It cannot natively handle minimum/maximum range queries efficiently.
- **No Native Range Updates:** While simple Fenwick Trees handle point update + range query, handling range update + range query requires two Fenwick Trees and is more complex.
- **Complex to Understand:** The bitwise logic `idx & (-idx)` can be unintuitive and difficult to grasp for beginners.

## 16. Applications
- **Inversion Counting:** Finding how many pairs $(i, j)$ exist such that $i < j$ and $A[i] > A[j]$.
- **Cumulative Frequency Tables:** Finding percentiles or medians in a changing stream of data.
- **Competitive Programming:** Often used as a building block for solving complex array manipulation problems.

## 17. Common Mistakes
- **0-based Indexing:** Attempting to use a Fenwick tree with 0-based index. If `index = 0`, `index + (index & -index)` becomes an infinite loop (`0 + 0 = 0`). You must shift all queries and updates to be 1-based.
- **Wrong Bitwise Operator:** Using `idx & (idx-1)` (which unsets the rightmost set bit) instead of `idx & (-idx)` (which extracts the rightmost set bit).
- **Confusing Update and Query loops:** In update, we *add* the LSB (`idx += ...`). In query, we *subtract* the LSB (`idx -= ...`). Mixing these up results in incorrect sums or infinite loops.

## 18. Interview Questions
1. How does a Fenwick tree compare to a Segment tree?
2. Why does the Fenwick tree use 1-based indexing?
3. How does the bitwise operation `x & (-x)` work?
4. Can a Fenwick tree be used for Range Minimum Query (RMQ)? Why or why not?
5. How would you construct a Fenwick tree in $O(N)$ time instead of $O(N \log N)$?
6. How can you extend a Fenwick tree to support range updates and point queries?
7. How can you extend a Fenwick tree to support range updates and range queries?
8. Explain how to count inversions in an array using a Fenwick Tree.
9. How can a Fenwick tree be used in 2D space?
10. If the array elements can be extremely large but sparse, how do you manage the Fenwick tree space? (Answer: Coordinate Compression or dynamic Fenwick trees using Hash Maps).

## 19. Practice Problems
- **Easy:** Given an array, implement point update and prefix sum query using a Fenwick tree.
- **Medium:** Count Inversions in an array.
- **Hard:** Range Update, Range Query: Given queries to add $V$ to all elements in $[L, R]$, and queries to find the sum in range $[X, Y]$, solve it efficiently.

## 20. Related Algorithms
- **Segment Tree:** A more versatile data structure capable of RMQ, Range sum, and non-reversible operations.
- **Prefix Sum Array:** For static arrays where no updates occur.
- **Square Root Decomposition (Mo's Algorithm):** An alternative technique for handling range queries and point updates in $O(\sqrt{N})$ time.

## 21. Summary
The Fenwick Tree (Binary Indexed Tree) is a brilliant, space-efficient data structure for computing dynamic prefix sums. By leveraging the binary representation of indices and bitwise operations, it achieves $O(\log N)$ time complexity for both point updates and prefix queries, all within a tight memory footprint of $O(N)$. It is a crucial tool for competitive programmers and system designers dealing with dynamic cumulative data.

## 22. Quiz

**Q1. What is the time complexity of querying a prefix sum in a Fenwick Tree?**
A) $O(1)$
B) $O(\log N)$
C) $O(N)$
D) $O(N \log N)$
**Correct Answer: B**
*Explanation: Querying traces back through the set bits of the index, which takes at most $\log_2 N$ steps.*

**Q2. What does the expression `x & (-x)` evaluate to?**
A) The value of $x$ with all bits flipped.
B) The number of set bits in $x$.
C) An integer with only the lowest set bit of $x$ being 1.
D) Zero.
**Correct Answer: C**
*Explanation: Using two's complement, `-x` is `~x + 1`. Bitwise ANDing $x$ and `-x` isolates the least significant bit that is set to 1.*

**Q3. Why does the standard Fenwick Tree use 1-based indexing?**
A) Because arrays in C/C++ are 1-based.
B) Because index 0 would cause an infinite loop during updates since $0\ \&\ -0 = 0$.
C) To save exactly 1 byte of memory.
D) It is only a convention, 0-based works exactly the same.
**Correct Answer: B**
*Explanation: Since adding the lowest set bit of 0 results in 0, the loop `while(index <= n)` would never terminate if starting at 0.*

**Q4. Compared to a Segment Tree, a Fenwick Tree typically:**
A) Uses more memory and is slower.
B) Uses less memory and is easier to code.
C) Can handle a wider variety of operations (like max/min).
D) Has $O(1)$ query time.
**Correct Answer: B**
*Explanation: Fenwick Trees need only $N+1$ space versus $4N$ for Segment Trees, and the code involves just a few short lines.*

**Q5. Can a standard Fenwick Tree easily answer Range Minimum Queries (RMQ)?**
A) Yes, with the same $O(\log N)$ complexity.
B) No, because the minimum operation is not strictly invertible like addition/subtraction.
C) Yes, but it requires $O(N)$ time per query.
D) No, because it can only hold positive numbers.
**Correct Answer: B**
*Explanation: To answer arbitrary range queries in a Fenwick tree using prefix results, the operation must be invertible (e.g., $Sum(L, R) = Sum(R) - Sum(L-1)$). We cannot simply "subtract" a minimum to isolate a range.*

**Q6. During an update operation, how do we modify the index?**
A) `index -= index & (-index)`
B) `index += index & (-index)`
C) `index *= 2`
D) `index = index / 2`
**Correct Answer: B**
*Explanation: Adding the lowest set bit moves the index forward to the next node in the tree that encompasses the current node.*

**Q7. What is the space complexity of a Fenwick tree for an array of $N$ elements?**
A) $O(\log N)$
B) $O(N \log N)$
C) $O(N)$
D) $O(2N)$
**Correct Answer: C**
*Explanation: The Fenwick tree is implemented using a single array of size $N+1$.*

**Q8. If you want to compute the sum of a range from `left` to `right` using a Fenwick tree, what is the formula?**
A) `query(right) + query(left)`
B) `query(right) - query(left)`
C) `query(right) - query(left - 1)`
D) `query(left) - query(right - 1)`
**Correct Answer: C**
*Explanation: The prefix sum up to `right` includes everything. We subtract the prefix sum up to `left - 1` to leave exactly the range `[left, right]`.*

**Q9. Which real-world application is highly suited for a Fenwick tree?**
A) Finding the shortest path in a graph.
B) Sorting an array of strings.
C) Maintaining cumulative frequency counts in arithmetic coding.
D) Storing an associative mapping of keys to values.
**Correct Answer: C**
*Explanation: Arithmetic coding relies heavily on dynamic cumulative frequencies, which is exactly the prefix sum problem a Fenwick Tree solves efficiently.*

**Q10. How can we build a Fenwick tree in $O(N)$ time instead of $O(N \log N)$?**
A) It's impossible; building always takes $O(N \log N)$.
B) By copying the original array and letting each element at $i$ add its value to `i + (i & -i)`.
C) By sorting the array first.
D) By traversing the array backwards.
**Correct Answer: B**
*Explanation: We can initialize `BIT` with original values, then in a single pass from $1$ to $N$, add `BIT[i]` to `BIT[i + (i & -i)]`, achieving linear time construction.*
