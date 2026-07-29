# Hungarian Algorithm (Kuhn-Munkres)

## 1. Introduction
The Hungarian matching algorithm, also known as the Kuhn-Munkres algorithm, is a combinatorial optimization algorithm that solves the assignment problem in polynomial time and which anticipated later primal-dual methods. It was developed and published in 1955 by Harold Kuhn, who gave the name "Hungarian method" because the algorithm was largely based on the earlier works of two Hungarian mathematicians: Dénes Kőnig and Jenő Egerváry. James Munkres reviewed the algorithm in 1957 and observed that it is (strongly) polynomial. The algorithm finds a maximum or minimum weight perfect matching in a bipartite graph.

## 2. Why Use This Algorithm?
The assignment problem can be formulated as finding the optimal pairing of `N` workers to `N` tasks such that the total cost is minimized. A brute-force approach, evaluating every possible permutation, takes `O(N!)` time, which is computationally infeasible even for small `N` (e.g., `N=20`). The Hungarian Algorithm is a mathematically sound, primal-dual method that guarantees an optimal solution in `O(N^3)` time. 

## 3. Real-World Applications
*   **Job Scheduling**: Assigning workers to specific tasks or shifts to minimize total cost or time.
*   **Transportation & Logistics**: Assigning delivery trucks to delivery routes or drivers to vehicles.
*   **Computer Vision**: Object tracking across multiple frames (assigning detected objects in frame `t` to objects in frame `t+1`).
*   **Ride-Sharing**: Assigning passengers to nearby drivers to minimize the total wait time.
*   **Network Routing**: Assigning available bandwidth or servers to incoming requests optimally.

## 4. Prerequisites
To fully understand the Hungarian Algorithm, you should be familiar with:
*   **Bipartite Graphs**: A graph whose vertices can be divided into two disjoint sets such that every edge connects a vertex in one set to a vertex in the other.
*   **Matching**: A set of edges without common vertices.
*   **Perfect Matching**: A matching that covers every vertex of the graph.
*   **Matrices**: The problem is typically represented as an `N x N` cost matrix where `cost[i][j]` is the cost of assigning worker `i` to task `j`.

## 5. Visualization (ASCII diagram & trace)
```text
Cost Matrix:
      Task1 Task2 Task3
Work1   2     5     8
Work2   9     2     7
Work3   4     1     3

Step 1: Row Reduction (Subtract min from each row)
[ 0,  3,  6 ]
[ 7,  0,  5 ]
[ 3,  0,  2 ]

Step 2: Column Reduction (Subtract min from each column)
[ 0,  3,  4 ]
[ 7,  0,  3 ]
[ 3,  0,  0 ]

Step 3: Cover all 0s with min lines
Lines at Row 1, Row 2, Row 3. Total lines = 3 = N.
Optimal assignment reached.

Assign: Work1->Task1 (cost 2), Work2->Task2 (cost 2), Work3->Task3 (cost 3).
Total Cost = 2 + 2 + 3 = 7.
```

## 6. How It Works
The algorithm transforms the original cost matrix into a new matrix with equivalent optimal assignments but easier to solve (by introducing zeros).
1.  **Subtract the minimum:** Perform row and column reductions to create zeros in the matrix.
2.  **Cover zeros:** Find the minimum number of horizontal and vertical lines required to cover all zeros.
3.  **Check for optimality:** If the number of lines equals `N` (number of rows/columns), an optimal assignment is possible using the zeros.
4.  **Adjust matrix (if needed):** If the number of lines is less than `N`, find the smallest uncovered value, subtract it from all uncovered values, and add it to all values covered by two lines. Repeat step 2.

## 7. Step-by-Step Algorithm
1.  **Row Reduction**: For each row, find the minimum element and subtract it from all elements in that row.
2.  **Column Reduction**: For each column, find the minimum element and subtract it from all elements in that column.
3.  **Covering Zeros**: Draw the minimum number of horizontal and vertical lines to cover all zeros in the matrix. Let this number of lines be `L`.
4.  **Optimality Check**: 
    *   If `L == N`, an optimal assignment exists. Proceed to step 6.
    *   If `L < N`, proceed to step 5.
5.  **Matrix Adjustment**: Find the smallest value `m` that is not covered by any line. Subtract `m` from all uncovered elements, and add `m` to all elements covered by both a horizontal and vertical line (intersections). Leave elements covered by one line unchanged. Return to Step 3.
6.  **Assignment**: Select a set of `N` zeros such that no two zeros share the same row or column. The positions of these zeros represent the optimal assignments.

## 8. Pseudocode
```text
function Hungarian(CostMatrix):
    N = size(CostMatrix)
    
    // Step 1: Row Reduction
    for i from 0 to N-1:
        min_val = min(CostMatrix[i])
        for j from 0 to N-1:
            CostMatrix[i][j] -= min_val
            
    // Step 2: Column Reduction
    for j from 0 to N-1:
        min_val = min of column j
        for i from 0 to N-1:
            CostMatrix[i][j] -= min_val
            
    loop:
        // Step 3: Line Covering
        lines = cover_all_zeros_with_min_lines(CostMatrix)
        
        if lines == N:
            return extract_optimal_assignment(CostMatrix)
            
        // Step 5: Adjustment
        min_uncovered = find_min_uncovered(CostMatrix)
        for i from 0 to N-1:
            for j from 0 to N-1:
                if is_uncovered(i, j):
                    CostMatrix[i][j] -= min_uncovered
                if is_intersection(i, j):
                    CostMatrix[i][j] += min_uncovered
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdbool.h>

#define N 3
#define INF 1000000

int hungarian(int cost[N][N]) {
    int u[N+1], v[N+1], p[N+1], way[N+1];
    for (int i = 0; i <= N; i++) { u[i] = v[i] = p[i] = way[i] = 0; }
    
    for (int i = 1; i <= N; i++) {
        p[0] = i;
        int j0 = 0;
        int minv[N+1];
        for (int k = 0; k <= N; k++) minv[k] = INF;
        bool used[N+1] = {false};
        do {
            used[j0] = true;
            int i0 = p[j0], delta = INF, j1;
            for (int j = 1; j <= N; ++j) {
                if (!used[j]) {
                    int cur = cost[i0-1][j-1] - u[i0] - v[j];
                    if (cur < minv[j]) {
                        minv[j] = cur;  way[j] = j0;
                    }
                    if (minv[j] < delta) {
                        delta = minv[j];  j1 = j;
                    }
                }
            }
            for (int j = 0; j <= N; ++j) {
                if (used[j]) {
                    u[p[j]] += delta;  v[j] -= delta;
                } else {
                    minv[j] -= delta;
                }
            }
            j0 = j1;
        } while (p[j0] != 0);
        do {
            int j1 = way[j0];
            p[j0] = p[j1];
            j0 = j1;
        } while (j0);
    }
    return -v[0];
}

int main() {
    int cost[N][N] = {{2, 5, 8}, {9, 2, 7}, {4, 1, 3}};
    printf("Minimum cost: %d\n", hungarian(cost));
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>

using namespace std;
const int INF = 1e9;

int hungarian(const vector<vector<int>>& cost) {
    int n = cost.size();
    vector<int> u(n + 1), v(n + 1), p(n + 1), way(n + 1);
    
    for (int i = 1; i <= n; ++i) {
        p[0] = i;
        int j0 = 0;
        vector<int> minv(n + 1, INF);
        vector<bool> used(n + 1, false);
        do {
            used[j0] = true;
            int i0 = p[j0], delta = INF, j1;
            for (int j = 1; j <= n; ++j) {
                if (!used[j]) {
                    int cur = cost[i0 - 1][j - 1] - u[i0] - v[j];
                    if (cur < minv[j]) {
                        minv[j] = cur;  way[j] = j0;
                    }
                    if (minv[j] < delta) {
                        delta = minv[j];  j1 = j;
                    }
                }
            }
            for (int j = 0; j <= n; ++j) {
                if (used[j]) {
                    u[p[j]] += delta;  v[j] -= delta;
                } else {
                    minv[j] -= delta;
                }
            }
            j0 = j1;
        } while (p[j0] != 0);
        do {
            int j1 = way[j0];
            p[j0] = p[j1];
            j0 = j1;
        } while (j0);
    }
    return -v[0];
}

int main() {
    vector<vector<int>> cost = {{2, 5, 8}, {9, 2, 7}, {4, 1, 3}};
    cout << "Minimum cost: " << hungarian(cost) << endl;
    return 0;
}
```

### Java
```java
import java.util.Arrays;

public class Hungarian {
    static final int INF = 1000000;

    public static int hungarianAlgo(int[][] cost) {
        int n = cost.length;
        int[] u = new int[n + 1];
        int[] v = new int[n + 1];
        int[] p = new int[n + 1];
        int[] way = new int[n + 1];

        for (int i = 1; i <= n; i++) {
            p[0] = i;
            int j0 = 0;
            int[] minv = new int[n + 1];
            Arrays.fill(minv, INF);
            boolean[] used = new boolean[n + 1];
            
            do {
                used[j0] = true;
                int i0 = p[j0], delta = INF, j1 = 0;
                for (int j = 1; j <= n; j++) {
                    if (!used[j]) {
                        int cur = cost[i0 - 1][j - 1] - u[i0] - v[j];
                        if (cur < minv[j]) {
                            minv[j] = cur;
                            way[j] = j0;
                        }
                        if (minv[j] < delta) {
                            delta = minv[j];
                            j1 = j;
                        }
                    }
                }
                for (int j = 0; j <= n; j++) {
                    if (used[j]) {
                        u[p[j]] += delta;
                        v[j] -= delta;
                    } else {
                        minv[j] -= delta;
                    }
                }
                j0 = j1;
            } while (p[j0] != 0);
            
            do {
                int j1 = way[j0];
                p[j0] = p[j1];
                j0 = j1;
            } while (j0 != 0);
        }
        return -v[0];
    }

    public static void main(String[] args) {
        int[][] cost = {{2, 5, 8}, {9, 2, 7}, {4, 1, 3}};
        System.out.println("Minimum cost: " + hungarianAlgo(cost));
    }
}
```

### Python
```python
def hungarian(cost_matrix):
    n = len(cost_matrix)
    u = [0] * (n + 1)
    v = [0] * (n + 1)
    p = [0] * (n + 1)
    way = [0] * (n + 1)
    
    INF = 10**9
    for i in range(1, n + 1):
        p[0] = i
        j0 = 0
        minv = [INF] * (n + 1)
        used = [False] * (n + 1)
        
        while True:
            used[j0] = True
            i0 = p[j0]
            delta = INF
            j1 = 0
            
            for j in range(1, n + 1):
                if not used[j]:
                    cur = cost_matrix[i0 - 1][j - 1] - u[i0] - v[j]
                    if cur < minv[j]:
                        minv[j] = cur
                        way[j] = j0
                    if minv[j] < delta:
                        delta = minv[j]
                        j1 = j
                        
            for j in range(n + 1):
                if used[j]:
                    u[p[j]] += delta
                    v[j] -= delta
                else:
                    minv[j] -= delta
            
            j0 = j1
            if p[j0] == 0:
                break
                
        while j0 != 0:
            j1 = way[j0]
            p[j0] = p[j1]
            j0 = j1
            
    return -v[0]

if __name__ == "__main__":
    cost = [[2, 5, 8], [9, 2, 7], [4, 1, 3]]
    print("Minimum cost:", hungarian(cost))
```

### JavaScript
```javascript
function hungarian(costMatrix) {
    const n = costMatrix.length;
    const u = new Array(n + 1).fill(0);
    const v = new Array(n + 1).fill(0);
    const p = new Array(n + 1).fill(0);
    const way = new Array(n + 1).fill(0);
    const INF = 1e9;

    for (let i = 1; i <= n; i++) {
        p[0] = i;
        let j0 = 0;
        const minv = new Array(n + 1).fill(INF);
        const used = new Array(n + 1).fill(false);
        
        while (true) {
            used[j0] = true;
            let i0 = p[j0];
            let delta = INF;
            let j1 = 0;
            
            for (let j = 1; j <= n; j++) {
                if (!used[j]) {
                    const cur = costMatrix[i0 - 1][j - 1] - u[i0] - v[j];
                    if (cur < minv[j]) {
                        minv[j] = cur;
                        way[j] = j0;
                    }
                    if (minv[j] < delta) {
                        delta = minv[j];
                        j1 = j;
                    }
                }
            }
            
            for (let j = 0; j <= n; j++) {
                if (used[j]) {
                    u[p[j]] += delta;
                    v[j] -= delta;
                } else {
                    minv[j] -= delta;
                }
            }
            j0 = j1;
            if (p[j0] === 0) break;
        }
        
        while (j0 !== 0) {
            let j1 = way[j0];
            p[j0] = p[j1];
            j0 = j1;
        }
    }
    return -v[0];
}

const cost = [[2, 5, 8], [9, 2, 7], [4, 1, 3]];
console.log("Minimum cost:", hungarian(cost));
```

## 10. Code Explanation
The implementations above utilize an $O(N^3)$ approach using dual variables (potentials) `u` and `v`. 
- **`u[i]` and `v[j]`**: These represent the potential variables for the rows and columns, used to maintain non-negativity of reduced costs (`cost[i][j] - u[i] - v[j] >= 0`).
- **`p[j]`**: Keeps track of the matching. `p[j]` represents the row matched to column `j`. `p[0]` is a dummy node to track the starting node of an augmenting path.
- **`way[j]`**: Helps to reconstruct the augmenting path when an unmatched column is found.
- **Outer Loop (`i` from 1 to N)**: Iterates over each row to find a match. For every row, we try to augment the matching by finding an alternating path.
- **Inner Do-While Loop**: Expands the set of reachable nodes in the bipartite graph, updating potentials (`delta`) and finding the shortest path to an unmatched column. 
- **Reconstruction Loop**: Updates the matching `p` backwards using the `way` array, flipping edges along the augmenting path.

## 11. Interactive Demo
### Specification for an Interactive UI Demo
1. **Input Grid**: An `NxN` dynamic grid where the user can input integers representing the cost matrix. `N` is controllable via a slider (from 2 to 10).
2. **Step Forward/Backward Controls**: Buttons to trace the algorithm visually step-by-step.
3. **Visual Highlights**: 
   - Row and Column reductions animate by subtracting numbers (color-coded red).
   - "Lines" drawn across rows/cols to cover zeros appear as animated bars crossing the grid.
   - Highlighting minimum uncovered numbers.
4. **Final Matching Visualization**: A bipartite graph (Workers on left, Tasks on right) animating the final chosen edges.
5. **State Panel**: Displays current step (e.g., "Step 3: Covering Zeros... Only 2 lines needed, but N=3. Adjustment required.").

## 12. Dry Run
Let's consider a 3x3 cost matrix:
```
  2   5   8
  9   2   7
  4   1   3
```

| Step | Action | Resulting Matrix | Note |
|---|---|---|---|
| 1 | Original Matrix | `[2, 5, 8], [9, 2, 7], [4, 1, 3]` | Starting state |
| 2 | Row Reduction | `[0, 3, 6], [7, 0, 5], [3, 0, 2]` | Subtract min of row 1 (2), row 2 (2), row 3 (1). |
| 3 | Column Reduction | `[0, 3, 4], [7, 0, 3], [3, 0, 0]` | Subtract min of col 3 (2). Cols 1, 2 have 0s. |
| 4 | Cover Zeros | Lines at Row 1, Row 2, Row 3 | We need 3 lines to cover all zeros: (0,0), (1,1), (2,1), (2,2). |
| 5 | Optimality | `Lines (3) == N (3)` | Optimal matching found. |
| 6 | Match Selection | `(0,0), (1,1), (2,2)` | Select `(0,0)` [cost 2], `(1,1)` [cost 2], `(2,2)` [cost 3] = Total: 7. |

## 13. Time & Space Complexity

| Aspect | Complexity | Explanation |
|---|---|---|
| Time | O(N^3) | Finding augmenting paths takes `O(N^2)` and we do this for `N` rows. Modifying potentials and min arrays takes `O(N)` per path extension step. Total `O(N^3)`. |
| Space | O(N^2) | Space required to store the `N x N` cost matrix. The auxiliary arrays `u`, `v`, `p`, `way`, `minv`, `used` take `O(N)` space. |

*(Note: Original implementation without optimization and potential adjustments can be O(N^4), but modern competitive programming implementations like the one provided use O(N^3) with Fibonacci heaps or simple arrays).*

## 14. Advantages
- **Optimal Guarantee**: Always finds the absolute minimum (or maximum) cost assignment.
- **Polynomial Time**: Solves in `O(N^3)` whereas brute force takes `O(N!)`.
- **Adaptable**: Can handle unbalanced assignments (M workers, N tasks) by padding the matrix with dummy rows/cols of zeros.
- **Max-Weight variant**: Easily adaptable to maximize profit by subtracting all elements from the max element in the matrix.

## 15. Disadvantages
- **Complexity implementation**: Non-trivial to implement from scratch compared to simple greedy heuristics.
- **Memory/Space bound**: Dense matrix representation means `O(N^2)` memory. Large sparse problems might be better solved with network flow algorithms (Min-Cost Max-Flow).
- **Not suited for dynamic graphs**: If one cost changes slightly, the algorithm typically must be re-run from scratch (though incremental Hungarian exists).

## 16. Applications
1.  **Transport Optimization**: Dispatching taxis to riders.
2.  **Resource Allocation**: Allocating computing resources (VMs) to server racks.
3.  **Target Tracking**: Assigning radar plots to known trajectories in aviation.
4.  **Bipartite Graph Matching**: Finding maximum matchings in dense bipartite graphs.
5.  **Personnel Scheduling**: Matching employees to shifts based on preference costs.

## 17. Common Mistakes
1.  **Forgetting to convert Maximization to Minimization**: Hungarian algorithm solves minimization. To maximize, subtract all elements from the maximum element.
2.  **Unbalanced matrices**: If `N != M`, you must pad the matrix with 0s to make it square before running the algorithm.
3.  **Drawing more lines than necessary**: Finding the minimum number of lines to cover zeros is equivalent to finding the maximum bipartite matching, which can be tricky to do manually.
4.  **Incorrect matrix adjustments**: Forgetting to add the min uncovered value to the intersections of two lines in step 5.

## 18. Interview Questions
1.  *What is the time complexity of the Hungarian Algorithm, and how does it compare to Brute Force?*
2.  *How do you handle an assignment problem where the number of tasks is greater than the number of workers?*
3.  *How can the Hungarian Algorithm be modified to solve a maximization problem?*
4.  *Explain the concept of "row reduction" and "column reduction" in this algorithm.*
5.  *What happens if you use a greedy approach for the assignment problem instead of the Hungarian algorithm? Give a counter-example.*
6.  *Can the Hungarian algorithm handle negative costs? If so, how?*
7.  *Explain how drawing lines to cover zeros relates to Kőnig's Theorem.*
8.  *What is the difference between Min-Cost Max-Flow and the Hungarian algorithm for assignments?*
9.  *Walk me through adjusting the matrix when `L < N` lines are drawn.*
10. *Why is the dummy column/row initialization used in the $O(N^3)$ array implementation?*

## 19. Practice Problems
*   **Easy 1-4**:
    1.  Implement a basic 3x3 matrix row and column reduction.
    2.  Pad an unbalanced 3x4 cost matrix properly.
    3.  Find the minimum cost in a 4x4 matrix using manual line covering.
    4.  Convert a 3x3 maximization problem to minimization.
*   **Medium 5-8**:
    5.  Implement the full algorithm in Python.
    6.  Solve the assignment problem for negative edge weights.
    7.  Implement the greedy matching algorithm and compare its cost to the Hungarian's result on a 5x5 random matrix.
    8.  Use the algorithm to solve a basic 2D object tracking frame matching problem.
*   **Hard 9-11**:
    9.  Implement the $O(N^3)$ efficient version using dual variables.
    10. Solve an assignment problem where some assignments are strictly forbidden (cost = Infinity).
    11. Formulate and solve the assignment problem using the Min-Cost Max-Flow algorithm and compare execution times.

## 20. Related Algorithms
*   **[Min-Cost Max-Flow](https://en.wikipedia.org/wiki/Minimum-cost_flow_problem)**: Solves the assignment problem more generally, suited for sparse graphs.
*   **[Hopcroft-Karp Algorithm](https://en.wikipedia.org/wiki/Hopcroft%E2%80%93Karp_algorithm)**: Finds maximum cardinality matching in unweighted bipartite graphs in $O(E \sqrt{V})$.
*   **[Edmonds' Blossom Algorithm](https://en.wikipedia.org/wiki/Blossom_algorithm)**: Finds maximum matching in general (non-bipartite) graphs.
*   **[Gale-Shapley Algorithm](https://en.wikipedia.org/wiki/Gale%E2%80%93Shapley_algorithm)**: Solves the stable marriage problem (preferences rather than costs).

## 21. Summary
The Hungarian algorithm is a foundational combinatorial optimization algorithm that elegantly solves the assignment problem (minimum weight perfect bipartite matching) in polynomial time $O(N^3)$. By maintaining dual potential variables, modifying them to expose zeros, and searching for augmenting paths, the algorithm avoids the computational impossibility of `O(N!)` brute force and guarantees an optimal solution.

## 22. Quiz
**Question 1: What is the optimal time complexity of the Hungarian Algorithm?**
A) O(N^2)
B) O(N^3)
C) O(N^4)
D) O(N!)
**Correct Answer: B**
*Explanation*: The most efficient implementations run in O(N^3) time by using augmenting paths and dual variables.

**Question 2: What must be done if the cost matrix is not square (e.g., 3 workers, 4 tasks)?**
A) The algorithm cannot be used.
B) Delete the largest task.
C) Pad the matrix with a dummy row of zeros.
D) Pad the matrix with a dummy row of infinities.
**Correct Answer: C**
*Explanation*: Padding with zeros creates a balanced square matrix without affecting the optimal assignment of the real workers.

**Question 3: How is a maximization problem handled in the Hungarian algorithm?**
A) It handles it natively.
B) By negating all elements.
C) By taking the reciprocal of all elements.
D) By subtracting all elements from the maximum element in the matrix.
**Correct Answer: D**
*Explanation*: This converts the values into relative "losses", turning it into a minimization problem which the algorithm can solve. (Negating also works if you handle negative values appropriately).

**Question 4: In the step where lines cover zeros, what does it mean if the minimum number of lines is less than N?**
A) An optimal assignment is found.
B) The problem has no solution.
C) We must adjust the matrix by adding/subtracting the minimum uncovered value.
D) We made a mistake in subtraction.
**Correct Answer: C**
*Explanation*: If lines < N, a perfect matching hasn't been found. We adjust the matrix to create new zeros.

**Question 5: Which theorem is closely related to the line-drawing step?**
A) Kőnig's theorem
B) Hall's Marriage theorem
C) Max-Flow Min-Cut theorem
D) Euler's theorem
**Correct Answer: A**
*Explanation*: Kőnig's theorem states that in bipartite graphs, the size of the maximum matching equals the size of the minimum vertex cover.

**Question 6: What happens to elements covered by the intersection of two lines during the matrix adjustment step?**
A) The minimum uncovered value is subtracted from them.
B) They remain unchanged.
C) The minimum uncovered value is added to them.
D) They are set to zero.
**Correct Answer: C**
*Explanation*: To maintain matrix validity after subtracting from uncovered elements, we add the value back to intersections.

**Question 7: What does the Hungarian Algorithm fundamentally compute?**
A) Minimum Spanning Tree
B) Shortest Path
C) Minimum Weight Perfect Bipartite Matching
D) Stable Marriage
**Correct Answer: C**
*Explanation*: It is explicitly designed for finding optimal assignments in bipartite graphs with edge weights.

**Question 8: Why is brute force not used for the assignment problem?**
A) It gives incorrect answers.
B) It takes O(N!) time, which is too slow.
C) It cannot handle non-square matrices.
D) It takes O(N^3) time, same as Hungarian.
**Correct Answer: B**
*Explanation*: Checking every permutation requires N! operations, scaling terribly for even small inputs.

**Question 9: Which of the following is NOT an application of the Hungarian algorithm?**
A) Assigning delivery routes.
B) Scheduling jobs to machines.
C) Finding the shortest path in a road network.
D) Multi-object tracking in computer vision.
**Correct Answer: C**
*Explanation*: Shortest path is solved by Dijkstra's or Bellman-Ford, not Hungarian.

**Question 10: During matrix adjustment, if we subtract the minimum uncovered value, what is guaranteed to happen?**
A) We immediately finish the algorithm.
B) At least one new zero will appear.
C) All previous zeros are preserved.
D) The matrix becomes square.
**Correct Answer: B**
*Explanation*: The minimum uncovered value itself becomes 0 after subtraction, creating at least one new zero to explore.
