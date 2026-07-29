# 108. Binary Indexed Tree (Fenwick Tree)

## 1. Introduction
A Binary Indexed Tree (BIT), also known as a Fenwick Tree, is a data structure that provides efficient methods for calculation and manipulation of prefix sums of a sequence of values. Invented by Peter Fenwick in 1994, it offers an optimal balance between updating elements and querying prefix sums, both taking logarithmic time.

**Real-world Analogy:**
Imagine a bank where you have a ledger of daily deposits over a year. If you want to know the total balance at day 100, you could add up days 1 to 100 (which takes time proportional to 100). If you update day 50's deposit, you just change one entry. 
But what if you need to frequently query the balance on arbitrary days and also update daily deposits? A Fenwick tree is like a hierarchical set of managers where each manager remembers the sum of a specific block of days. When you want the sum up to day 100, you just ask a few managers for their totals. When you update day 50, you only tell the managers who oversee day 50. Both operations are much faster!

## 2. Why Use This Algorithm?
When dealing with arrays where you need to frequently update elements and calculate prefix sums, a simple array approach would have $O(1)$ updates and $O(N)$ prefix sums. A prefix sum array would have $O(N)$ updates and $O(1)$ prefix sums. A Binary Indexed Tree balances this, providing $O(\log N)$ for both updating an element and calculating prefix sums. It is also significantly easier to code and requires less space than a Segment Tree for these specific operations.

## 3. Real-World Applications
* **Cumulative Frequency Tables:** Keeping track of cumulative frequencies in data streams or compression algorithms (like arithmetic coding).
* **Rank Queries:** Finding the rank of elements dynamically in a stream of numbers.
* **Inversion Counting:** Counting the number of inversions in an array efficiently (useful in sorting analysis and recommendation systems).
* **Game Development:** Managing dynamically changing stats or scores over intervals.

## 4. Prerequisites
* **Arrays:** Fundamental understanding of arrays.
* **Bitwise Operations:** Specifically bitwise AND (`&`), two's complement (`-x`), and right shift operators. Understanding how to isolate the lowest set bit (`x & (-x)`).
* **Prefix Sums:** Concept of cumulative sums.

## 5. Visualization
Consider an array `A = [3, 2, -1, 6, 5, 4, -3, 3, 7, 2, 3]` (1-indexed).
A Fenwick tree is represented as an array `BIT`. Each index `i` in the `BIT` stores the sum of a range of elements from `A`. The range ends at `i` and its length is equal to the lowest set bit in the binary representation of `i`.

```text
Index (Binary) -> Range Length -> Stores Sum of A[range]
1 (0001) -> 1 -> A[1]
2 (0010) -> 2 -> A[1..2]
3 (0011) -> 1 -> A[3]
4 (0100) -> 4 -> A[1..4]
5 (0101) -> 1 -> A[5]
6 (0110) -> 2 -> A[5..6]
7 (0111) -> 1 -> A[7]
8 (1000) -> 8 -> A[1..8]
```

## 6. How It Works
The magic of the Fenwick tree relies on the binary representation of indices. 
**Isolating the last set bit:** For any integer `x`, the expression `x & (-x)` isolates the lowest set bit. For example, for 6 (binary `0110`), `-6` is its two's complement (`1010`). `0110 & 1010 = 0010` (which is 2).

**Querying Prefix Sum (sum up to i):**
1. Initialize `sum = 0`.
2. Add `BIT[i]` to `sum`.
3. Strip the lowest set bit from `i` (`i = i - (i & (-i))`).
4. Repeat until `i > 0`.

**Updating (adding val to A[i]):**
1. Add `val` to `BIT[i]`.
2. Add the lowest set bit to `i` (`i = i + (i & (-i))`). This moves up the tree to the next index that encompasses `A[i]`.
3. Repeat until `i` exceeds the size of the BIT.

## 7. Step-by-Step Algorithm
**To construct the BIT from an array `arr` of size `n`:**
1. Create a `BIT` array of size `n + 1`, initialized to 0.
2. For each element `arr[i-1]` (where `i` goes from 1 to `n`), call the `update(i, arr[i-1])` function.

**To `update(index, val)`:**
1. Loop while `index <= n`:
2. `BIT[index] = BIT[index] + val`.
3. `index = index + (index & (-index))`.

**To `query(index)`:**
1. Initialize `sum = 0`.
2. Loop while `index > 0`:
3. `sum = sum + BIT[index]`.
4. `index = index - (index & (-index))`.

## 8. Pseudocode
```text
function update(BIT, n, index, val):
    while index <= n:
        BIT[index] += val
        index += index & (-index)

function query(BIT, index):
    sum = 0
    while index > 0:
        sum += BIT[index]
        index -= index & (-index)
    return sum

function constructBIT(arr, n):
    BIT = array of size n+1 initialized to 0
    for i from 1 to n:
        update(BIT, n, i, arr[i-1])
    return BIT
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

int* constructBIT(int arr[], int n) {
    int *BIT = (int *)calloc(n + 1, sizeof(int));
    for (int i = 0; i < n; i++) {
        update(BIT, n, i + 1, arr[i]);
    }
    return BIT;
}

int main() {
    int arr[] = {3, 2, -1, 6, 5, 4, -3, 3, 7, 2, 3};
    int n = sizeof(arr) / sizeof(arr[0]);
    
    int *BIT = constructBIT(arr, n);
    
    printf("Prefix sum of first 5 elements: %d\n", query(BIT, 5));
    printf("Prefix sum of first 8 elements: %d\n", query(BIT, 8));
    
    // Add 2 to arr[3] (0-indexed, so 4th element)
    update(BIT, n, 4, 2);
    printf("After update, prefix sum of first 5 elements: %d\n", query(BIT, 5));
    
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

    FenwickTree(vector<int>& arr) : FenwickTree(arr.size()) {
        for (int i = 0; i < n; i++) {
            update(i + 1, arr[i]);
        }
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
};

int main() {
    vector<int> arr = {3, 2, -1, 6, 5, 4, -3, 3, 7, 2, 3};
    FenwickTree bit(arr);

    cout << "Prefix sum of first 5 elements: " << bit.query(5) << endl;
    cout << "Prefix sum of first 8 elements: " << bit.query(8) << endl;

    // Add 2 to 4th element (1-based index 4)
    bit.update(4, 2);
    cout << "After update, prefix sum of first 5 elements: " << bit.query(5) << endl;

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

    public FenwickTree(int[] arr) {
        this(arr.length);
        for (int i = 0; i < n; i++) {
            update(i + 1, arr[i]);
        }
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

    public static void main(String[] args) {
        int[] arr = {3, 2, -1, 6, 5, 4, -3, 3, 7, 2, 3};
        FenwickTree bit = new FenwickTree(arr);

        System.out.println("Prefix sum of first 5 elements: " + bit.query(5));
        System.out.println("Prefix sum of first 8 elements: " + bit.query(8));

        // Add 2 to 4th element (1-based index 4)
        bit.update(4, 2);
        System.out.println("After update, prefix sum of first 5 elements: " + bit.query(5));
    }
}
```

### Python
```python
class FenwickTree:
    def __init__(self, size):
        self.n = size
        self.BIT = [0] * (self.n + 1)

    def update(self, index, val):
        while index <= self.n:
            self.BIT[index] += val
            index += index & (-index)

    def query(self, index):
        total_sum = 0
        while index > 0:
            total_sum += self.BIT[index]
            index -= index & (-index)
        return total_sum

    @classmethod
    def from_array(cls, arr):
        tree = cls(len(arr))
        for i, val in enumerate(arr):
            tree.update(i + 1, val)
        return tree

if __name__ == "__main__":
    arr = [3, 2, -1, 6, 5, 4, -3, 3, 7, 2, 3]
    bit = FenwickTree.from_array(arr)

    print(f"Prefix sum of first 5 elements: {bit.query(5)}")
    print(f"Prefix sum of first 8 elements: {bit.query(8)}")

    # Add 2 to 4th element (1-based index 4)
    bit.update(4, 2)
    print(f"After update, prefix sum of first 5 elements: {bit.query(5)}")
```

### JavaScript
```javascript
class FenwickTree {
    constructor(sizeOrArray) {
        if (typeof sizeOrArray === 'number') {
            this.n = sizeOrArray;
            this.BIT = new Array(this.n + 1).fill(0);
        } else {
            this.n = sizeOrArray.length;
            this.BIT = new Array(this.n + 1).fill(0);
            for (let i = 0; i < this.n; i++) {
                this.update(i + 1, sizeOrArray[i]);
            }
        }
    }

    update(index, val) {
        while (index <= this.n) {
            this.BIT[index] += val;
            index += index & (-index);
        }
    }

    query(index) {
        let sum = 0;
        while (index > 0) {
            sum += this.BIT[index];
            index -= index & (-index);
        }
        return sum;
    }
}

// Demo
const arr = [3, 2, -1, 6, 5, 4, -3, 3, 7, 2, 3];
const bit = new FenwickTree(arr);

console.log("Prefix sum of first 5 elements:", bit.query(5));
console.log("Prefix sum of first 8 elements:", bit.query(8));

// Add 2 to 4th element (1-based index 4)
bit.update(4, 2);
console.log("After update, prefix sum of first 5 elements:", bit.query(5));
```

## 10. Code Explanation
1. **Tree Initialization**: The tree uses an array (`BIT`) of size `n + 1` (since it is 1-indexed for ease of bitwise operations). All elements are initially `0`.
2. **`update(index, val)`**: 
   - Modifies `BIT[index]` by adding `val`.
   - To update all other nodes in the BIT that are responsible for this index, we find the next responsible index by adding the lowest set bit: `index += index & (-index)`.
   - This effectively traverses "up" the conceptual tree.
3. **`query(index)`**:
   - Traverses "down" the conceptual tree to sum up ranges.
   - It accumulates `BIT[index]`.
   - Then it moves to the preceding non-overlapping range by subtracting the lowest set bit: `index -= index & (-index)`.
4. **`from_array(arr)`**: A convenience wrapper to build the tree in $O(N \log N)$ time by calling `update` for each element. (Note: An $O(N)$ construction method exists, but $O(N \log N)$ is often sufficient and easier to implement).

## 11. Interactive Demo Description
A great interactive demo for a Binary Indexed Tree would feature an array representation alongside its corresponding tree structure. Users could:
* Input an array to initialize the tree.
* Click on an `update(i, val)` button, showing an animation of the value traversing up the tree layers and modifying specific indices based on the binary arithmetic.
* Click on a `query(i)` button, animating the descent down the tree, accumulating values from specific nodes until reaching 0, clearly displaying the binary bit manipulation step-by-step.

## 12. Dry Run
Let's dry run building a small BIT for `A = [3, 2, -1, 6]` (1-indexed). `n = 4`. `BIT = [0, 0, 0, 0, 0]`.

**i=1 (val=3): `update(1, 3)`**
* `BIT[1] += 3` -> `BIT = [0, 3, 0, 0, 0]`
* `index = 1 + (1 & -1)` = 1 + 1 = 2
* `BIT[2] += 3` -> `BIT = [0, 3, 3, 0, 0]`
* `index = 2 + (2 & -2)` = 2 + 2 = 4
* `BIT[4] += 3` -> `BIT = [0, 3, 3, 0, 3]`
* `index = 4 + (4 & -4)` = 4 + 4 = 8 (Exceeds `n=4`, stop).

**i=2 (val=2): `update(2, 2)`**
* `BIT[2] += 2` -> `BIT = [0, 3, 5, 0, 3]`
* `index = 2 + (2 & -2)` = 4
* `BIT[4] += 2` -> `BIT = [0, 3, 5, 0, 5]`
* (Next index 8 exceeds, stop).

**i=3 (val=-1): `update(3, -1)`**
* `BIT[3] += -1` -> `BIT = [0, 3, 5, -1, 5]`
* `index = 3 + (3 & -3)` = 3 + 1 = 4
* `BIT[4] += -1` -> `BIT = [0, 3, 5, -1, 4]`
* (Next index 8 exceeds, stop).

**i=4 (val=6): `update(4, 6)`**
* `BIT[4] += 6` -> `BIT = [0, 3, 5, -1, 10]`
* `index = 4 + (4 & -4)` = 8 (Exceeds, stop).

**Trace of `query(3)`**:
* `index = 3`, `sum = 0`
* `sum += BIT[3]` -> `sum = 0 + (-1) = -1`
* `index = 3 - (3 & -3)` = 3 - 1 = 2
* `sum += BIT[2]` -> `sum = -1 + 5 = 4`
* `index = 2 - (2 & -2)` = 2 - 2 = 0 (Stop).
Result: 4. (Which is $3 + 2 - 1 = 4$).

## 13. Time & Space Complexity

| Operation | Best Case | Average Case | Worst Case | Space Complexity |
|---|---|---|---|---|
| Query | $O(1)$ | $O(\log N)$ | $O(\log N)$ | $O(N)$ |
| Update | $O(1)$ | $O(\log N)$ | $O(\log N)$ | $O(N)$ |
| Construction | $O(N)$ | $O(N \log N)$ | $O(N \log N)$ | $O(N)$ |

**Reasons:**
* **Query & Update:** The while loop condition processes the bits of the index. In the worst case, it processes every set bit in the binary representation of the index. An index up to $N$ has at most $O(\log N)$ bits.
* **Construction:** $O(N \log N)$ using repeated updates. An $O(N)$ construction is possible by traversing backwards and propagating sums.
* **Space:** Needs an array of size $N+1$, so $O(N)$.

## 14. Advantages
* Very fast updates and prefix sum queries.
* Simpler to implement than a Segment Tree.
* Uses less memory than a Segment Tree (just one array of size $N+1$).
* Highly efficient bitwise operations for navigation.

## 15. Disadvantages
* Limited primarily to invertible operations (like addition/subtraction). It struggles with min/max queries natively compared to Segment Trees.
* Less intuitive to grasp initially due to reliance on binary bit manipulation tricks.
* Adding/removing elements dynamically (changing $N$) is not supported natively.

## 16. Applications
* Competitive programming for range sum queries.
* Compression algorithms (Arithmetic coding).
* Statistical counting where data arrives continuously and rank/percentile checks are frequent.
* Calculating the number of inversions in an array.

## 17. Common Mistakes
* **0-indexing vs 1-indexing:** BIT heavily relies on 1-based indexing to work correctly because `0 & -0` is 0, which would cause an infinite loop in `update`.
* **Not allocating $N+1$ elements:** Forgetting to allocate the extra element for 1-based indexing causes out-of-bounds errors.
* **Confusing `update` parameters:** Passing 0-based indices to update instead of converting to 1-based indices.
* **Applying to Non-Invertible Operations:** Trying to use BIT for Range Minimum Query natively (without complex modifications) usually fails.

## 18. Interview Questions
1. **What is a Binary Indexed Tree?** (A data structure for efficient prefix sums and point updates).
2. **How does `x & (-x)` work?** (It isolates the rightmost set bit in the binary representation of `x`).
3. **Compare BIT with Segment Tree.** (BIT is easier to write, takes less space, but is generally restricted to invertible operations. Segment trees are more versatile).
4. **How would you find a range sum `[L, R]` using BIT?** (`query(R) - query(L - 1)`).
5. **Can BIT handle negative values?** (Yes, because addition and subtraction work perfectly fine with negative numbers).
6. **How do you build a BIT in $O(N)$ time?** (Initialize `BIT` with `arr` values, then iterate `i` from 1 to $N$. Let `p = i + (i & -i)`. If `p <= n`, `BIT[p] += BIT[i]`).
7. **Can a BIT be 2-Dimensional?** (Yes, a 2D BIT can answer 2D range sum queries and 2D point updates in $O(\log^2 N)$).
8. **Why is it called Fenwick Tree?** (Named after its creator, Peter Fenwick).
9. **How would you count inversions in an array using BIT?** (Traverse array backwards, use BIT to count how many smaller elements have already been seen, then add current element to BIT).
10. **Can you update a range and query a point with a BIT?** (Yes, by using a difference array logic where `update(L, v)` and `update(R+1, -v)` is used).

## 19. Practice Problems
* **Easy:** Implement Range Sum Query - Mutable (LeetCode 307).
* **Medium:** Count of Smaller Numbers After Self (LeetCode 315).
* **Hard:** Reverse Pairs (LeetCode 493).

## 20. Related Algorithms
* **Segment Tree:** A more general tree data structure for range queries (min, max, sum) and range updates.
* **Prefix Sum Array:** $O(1)$ query but $O(N)$ update.
* **Sqrt Decomposition:** Groups elements into blocks of size $\sqrt{N}$. $O(\sqrt{N})$ query and update.

## 21. Summary
The Binary Indexed Tree (Fenwick Tree) is a specialized, memory-efficient data structure for computing prefix sums and updating elements in an array, both in $O(\log N)$ time. It leverages elegant bitwise arithmetic to navigate a conceptual tree stored within a flat 1D array. While slightly less versatile than a Segment Tree, its brevity, speed, and low memory footprint make it a favorite for scenarios like frequency counting, cumulative sums, and inversion counting.

## 22. Quiz
**Q1. What is the time complexity of querying a prefix sum in a BIT of size N?**
a) $O(1)$
b) $O(\log N)$
c) $O(N)$
d) $O(N \log N)$
*Correct Answer:* b
*Explanation:* Querying traverses the set bits of the index. An integer $N$ has at most $\log N$ bits, taking $O(\log N)$ time.

**Q2. Which bitwise expression isolates the lowest set bit of an integer `x`?**
a) `x | (x - 1)`
b) `x & (x - 1)`
c) `x & (-x)`
d) `x ^ (x >> 1)`
*Correct Answer:* c
*Explanation:* Due to two's complement representation, `-x` is `~x + 1`. Bitwise ANDing `x` with `-x` isolates the lowest set bit.

**Q3. BIT arrays are typically 0-indexed or 1-indexed?**
a) 0-indexed
b) 1-indexed
*Correct Answer:* b
*Explanation:* They are typically 1-indexed because the bitwise operation `0 & -0` results in 0, which would lead to an infinite loop during updates.

**Q4. To find the sum of elements in the range `[L, R]` using a BIT, you compute:**
a) `query(R) + query(L)`
b) `query(R) - query(L)`
c) `query(R) - query(L - 1)`
d) `query(R) - query(L + 1)`
*Correct Answer:* c
*Explanation:* `query(R)` gives sum up to R. We subtract `query(L - 1)` to remove the sum of elements before index L.

**Q5. Compared to a Segment Tree, a BIT is generally:**
a) More memory-intensive
b) Harder to implement
c) Slower for point updates
d) Less versatile for operations like Range Minimum Query
*Correct Answer:* d
*Explanation:* BITs are primarily designed for invertible operations (like sum). RMQ requires more complex modifications, unlike Segment Trees which handle them naturally.

**Q6. What is the space complexity of a Binary Indexed Tree?**
a) $O(\log N)$
b) $O(N)$
c) $O(N \log N)$
d) $O(2N)$
*Correct Answer:* b
*Explanation:* It requires a single array of size $N+1$, which is $O(N)$ space.

**Q7. During an `update(index, val)` operation, how is the index modified to move up the tree?**
a) `index += index & (-index)`
b) `index -= index & (-index)`
c) `index = index * 2`
d) `index = index / 2`
*Correct Answer:* a
*Explanation:* Adding the lowest set bit moves the index to the parent node in the Fenwick Tree topology.

**Q8. Can a BIT handle range updates and point queries?**
a) Yes, without any modifications.
b) Yes, by maintaining a difference array.
c) No, it's strictly for point updates.
d) No, it requires a Segment Tree.
*Correct Answer:* b
*Explanation:* By treating the input array as a difference array, `update(L, v)` and `update(R+1, -v)` allows for range updates, while a simple `query(i)` gives the point value.

**Q9. Who invented the Binary Indexed Tree?**
a) Edsger Dijkstra
b) Peter Fenwick
c) Donald Knuth
d) Robert Sedgewick
*Correct Answer:* b
*Explanation:* It is named the Fenwick Tree after Peter Fenwick, who introduced it in 1994.

**Q10. In a BIT of size 16, which index is responsible for the sum of the entire array (indices 1 to 16)?**
a) 1
b) 8
c) 15
d) 16
*Correct Answer:* d
*Explanation:* The index 16 (binary `10000`) has its lowest set bit representing a length of 16, meaning it stores the sum of 16 elements ending at index 16.
