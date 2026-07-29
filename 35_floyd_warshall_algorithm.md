# Floyd-Warshall Algorithm

## 1. Introduction

The Floyd-Warshall Algorithm is an all-pairs shortest path dynamic programming algorithm published independently by Bernard Roy (1959), Robert Floyd (1962), and Stephen Warshall (1962). It calculates the shortest paths between **every pair of vertices** in a weighted directed or undirected graph in a single execution matrix computation.

Imagine a global airline flight matrix containing distances between every major city pair in the world. Floyd-Warshall systematically tests whether introducing a stopover city $k$ between departure city $i$ and destination city $j$ shortens the total flight distance ($dist[i][j] > dist[i][k] + dist[k][j]$). It tests every possible intermediate city $k \in \{0 \dots V-1\}$.

It was created to solve the All-Pairs Shortest Path (APSP) problem efficiently using simple 2D dynamic programming matrices.

You should use Floyd-Warshall when you need to compute shortest paths between all pairs of nodes in small-to-medium dense graphs ($V \le 500$) or detect negative cycles anywhere in the graph.

## 2. Why Use This Algorithm?

Floyd-Warshall calculates all-pairs shortest distances in clean $\mathcal{O}(V^3)$ time using a triple-nested loop.

**Benefits:**
- **All-Pairs Shortest Path:** Computes shortest paths between every pair of nodes $(i, j)$ simultaneously.
- **Handles Negative Edge Weights:** Works with negative edges (as long as no negative weight cycles exist).
- **Global Negative Cycle Detection:** Detects negative cycles anywhere in the graph if any diagonal element $dist[i][i] < 0$.
- **Extremely Simple Implementation:** Consists of just 3 nested `for` loops.
- **Transitive Closure Computing:** Can compute reachability matrices using bitwise operations (Warshall's Algorithm).

**Performance:**
- **Time Complexity:** $\mathcal{O}(V^3)$ (Best, Average, and Worst case).
- **Space Complexity:** $\mathcal{O}(V^2)$ auxiliary matrix space.

**When it is better than running Dijkstra $V$ times:**
Floyd-Warshall is simpler to code and has lower constant factors for small dense graphs ($V \le 500$).

## 3. Real-World Applications

- **All-Pairs Distance Matrices:** Computing distance matrices for Travelling Salesperson Problem (TSP) or clustering algorithms.
- **Transitive Closure:** Determining reachability between all pairs of states in finite state machines.
- **Network Routing Protocols:** Pre-computing complete distance lookup tables in traffic routing.
- **Graph Diameter Calculation:** Finding the maximum shortest distance between any pair of vertices in a network.

## 4. Prerequisites

Before learning Floyd-Warshall, you should understand:
- 2D Array / Matrix representations of graphs.
- Dynamic Programming state transitions ($dist[i][j] = \min(dist[i][j], dist[i][k] + dist[k][j])$).
- Concept of Intermediate Vertices in paths.

## 5. Visualization

```text
Graph:
    (3)
 0 ────> 1
 │       │ (1)
(8)      v
 └────> 2

Initial Matrix dist[][]:
     0    1    2
0 [  0,   3,   8 ]
1 [ INF,  0,   1 ]
2 [ INF, INF,  0 ]

Intermediate Vertex k = 1:
Can we shorten path from 0 to 2 via 1?
dist[0][2] = 8
dist[0][1] + dist[1][2] = 3 + 1 = 4
Since 4 < 8 -> Update dist[0][2] = 4!

Final Matrix dist[][]:
     0    1    2
0 [  0,   3,   4 ]
1 [ INF,  0,   1 ]
2 [ INF, INF,  0 ]
```

## 6. How It Works

1. Initialize a $V \times V$ matrix `dist` where `dist[i][j]` is the weight of edge $(i, j)$, `0` if $i = j$, and $\infty$ if no direct edge exists.
2. **Dynamic Programming Loop:**
   - Loop `k` from `0` to `V - 1` (intermediate vertex candidate):
     - Loop `i` from `0` to `V - 1` (source vertex):
       - Loop `j` from `0` to `V - 1` (destination vertex):
         - If `dist[i][k] != ∞` and `dist[k][j] != ∞`:
           - `dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])`.
3. **Negative Cycle Check:**
   - If any `dist[i][i] < 0`, a negative weight cycle exists in the graph.
4. Return `dist` matrix.

## 7. Step-by-Step Algorithm

1. Create $V \times V$ matrix `dist`.
2. Populate `dist[i][j]`:
   - `dist[i][j] = 0` if `i == j`.
   - `dist[i][j] = weight` if edge $(i, j)$ exists.
   - `dist[i][j] = ∞` otherwise.
3. For `k = 0` to `V - 1`:
   - For `i = 0` to `V - 1`:
     - For `j = 0` to `V - 1`:
       - If `dist[i][k] + dist[k][j] < dist[i][j]`:
         - `dist[i][j] = dist[i][k] + dist[k][j]`.
4. Return `dist`.

## 8. Pseudocode

```text
function FloydWarshall(graph):
    V = graph.V
    dist = V x V matrix initialized to infinity

    for i = 0 to V - 1:
        dist[i][i] = 0
        for each (v, weight) in graph.adj[i]:
            dist[i][v] = weight

    for k = 0 to V - 1:
        for i = 0 to V - 1:
            for j = 0 to V - 1:
                if dist[i][k] != infinity and dist[k][j] != infinity:
                    if dist[i][k] + dist[k][j] < dist[i][j]:
                        dist[i][j] = dist[i][k] + dist[k][j]

    for i = 0 to V - 1:
        if dist[i][i] < 0:
            print("Negative weight cycle detected!")
            return null

    return dist
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdbool.h>

#define V 4
#define INF 99999

void floydWarshall(int graph[V][V]) {
    int dist[V][V];

    for (int i = 0; i < V; i++) {
        for (int j = 0; j < V; j++) {
            dist[i][j] = graph[i][j];
        }
    }

    for (int k = 0; k < V; k++) {
        for (int i = 0; i < V; i++) {
            for (int j = 0; j < V; j++) {
                if (dist[i][k] != INF && dist[k][j] != INF && dist[i][k] + dist[k][j] < dist[i][j]) {
                    dist[i][j] = dist[i][k] + dist[k][j];
                }
            }
        }
    }

    for (int i = 0; i < V; i++) {
        if (dist[i][i] < 0) {
            printf("Negative weight cycle detected!\n");
            return;
        }
    }

    printf("Shortest distances between every pair of vertices:\n");
    for (int i = 0; i < V; i++) {
        for (int j = 0; j < V; j++) {
            if (dist[i][j] == INF) printf("%7s", "INF");
            else printf("%7d", dist[i][j]);
        }
        printf("\n");
    }
}

int main() {
    int graph[V][V] = {
        {0,   3,   INF, 5},
        {2,   0,   INF, 4},
        {INF, 1,   0,   INF},
        {INF, INF, 2,   0}
    };

    floydWarshall(graph);
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>

const int INF = 1e9;

void floydWarshall(std::vector<std::vector<int>>& dist, int V) {
    for (int k = 0; k < V; k++) {
        for (int i = 0; i < V; i++) {
            for (int j = 0; j < V; j++) {
                if (dist[i][k] != INF && dist[k][j] != INF) {
                    if (dist[i][k] + dist[k][j] < dist[i][j]) {
                        dist[i][j] = dist[i][k] + dist[k][j];
                    }
                }
            }
        }
    }

    for (int i = 0; i < V; i++) {
        if (dist[i][i] < 0) {
            std::cout << "Negative weight cycle detected!\n";
            return;
        }
    }

    std::cout << "Shortest distances matrix:\n";
    for (int i = 0; i < V; i++) {
        for (int j = 0; j < V; j++) {
            if (dist[i][j] == INF) std::cout << "INF\t";
            else std::cout << dist[i][j] << "\t";
        }
        std::cout << "\n";
    }
}

int main() {
    int V = 4;
    std::vector<std::vector<int>> dist = {
        {0,   3,   INF, 5},
        {2,   0,   INF, 4},
        {INF, 1,   0,   INF},
        {INF, INF, 2,   0}
    };

    floydWarshall(dist, V);
    return 0;
}
```

### Java
```java
import java.util.Arrays;

public class FloydWarshallGraph {
    private static final int INF = 100000000;

    public static void floydWarshall(int[][] dist, int V) {
        for (int k = 0; k < V; k++) {
            for (int i = 0; i < V; i++) {
                for (int j = 0; j < V; j++) {
                    if (dist[i][k] != INF && dist[k][j] != INF) {
                        if (dist[i][k] + dist[k][j] < dist[i][j]) {
                            dist[i][j] = dist[i][k] + dist[k][j];
                        }
                    }
                }
            }
        }

        for (int i = 0; i < V; i++) {
            if (dist[i][i] < 0) {
                System.out.println("Negative weight cycle detected!");
                return;
            }
        }

        System.out.println("Shortest distances matrix:");
        for (int i = 0; i < V; i++) {
            for (int j = 0; j < V; j++) {
                if (dist[i][j] == INF) System.out.print("INF\t");
                else System.out.print(dist[i][j] + "\t");
            }
            System.out.println();
        }
    }

    public static void main(String[] args) {
        int V = 4;
        int[][] dist = {
            {0,   3,   INF, 5},
            {2,   0,   INF, 4},
            {INF, 1,   0,   INF},
            {INF, INF, 2,   0}
        };

        floydWarshall(dist, V);
    }
}
```

### Python
```python
INF = float('inf')

def floyd_warshall(dist: list[list[float]], V: int) -> list[list[float]] | None:
    for k in range(V):
        for i in range(V):
            for j in range(V):
                if dist[i][k] != INF and dist[k][j] != INF:
                    if dist[i][k] + dist[k][j] < dist[i][j]:
                        dist[i][j] = dist[i][k] + dist[k][j]

    for i in range(V):
        if dist[i][i] < 0:
            print("Negative weight cycle detected!")
            return None

    return dist

if __name__ == "__main__":
    V = 4
    dist = [
        [0, 3, INF, 5],
        [2, 0, INF, 4],
        [INF, 1, 0, INF],
        [INF, INF, 2, 0]
    ]

    result = floyd_warshall(dist, V)
    if result:
        for row in result:
            print([x if x != INF else "INF" for x in row])
```

### JavaScript
```javascript
const INF = Infinity;

function floydWarshall(dist, V) {
    for (let k = 0; k < V; k++) {
        for (let i = 0; i < V; i++) {
            for (let j = 0; j < V; j++) {
                if (dist[i][k] !== INF && dist[k][j] !== INF) {
                    if (dist[i][k] + dist[k][j] < dist[i][j]) {
                        dist[i][j] = dist[i][k] + dist[k][j];
                    }
                }
            }
        }
    }

    for (let i = 0; i < V; i++) {
        if (dist[i][i] < 0) {
            console.log("Negative weight cycle detected!");
            return null;
        }
    }

    return dist;
}

const V = 4;
const dist = [
    [0, 3, INF, 5],
    [2, 0, INF, 4],
    [INF, 1, 0, INF],
    [INF, INF, 2, 0]
];

console.log(floydWarshall(dist, V));
```

## 10. Code Explanation

Floyd-Warshall uses a 3D Dynamic Programming state compressed into a 2D matrix `dist[i][j]`. The outer loop variable `k` acts as the set of allowable intermediate vertices $\{0, 1, \dots, k\}$. At step $k$, `dist[i][j]` represents the shortest path from $i$ to $j$ using only vertices in $\{0 \dots k\}$ as intermediate nodes. The recurrence relation `dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])` tests if routing through vertex $k$ produces a shorter path. Crucially, the `k` loop **must** be the outermost loop for dynamic programming state dependencies to hold.

## 11. Interactive Demo

An interactive 2D Distance Matrix Grid allows users to edit edge weights in real-time.

- A step button moves the intermediate node indicator `k` from 0 to $V-1$.
- Highlighted cells show `dist[i][k]` and `dist[k][j]` being added and compared against `dist[i][j]`.
- Updated cells flash Cyan when a shorter intermediate route is discovered.

## 12. Dry Run

**Matrix ($V = 3$):**  
$0 \to 1 (3)$, $1 \to 2 (1)$, $0 \to 2 (8)$

| Outer `k` | Pair `(i, j)` | Compare `dist[i][j]` vs `dist[i][k] + dist[k][j]` | Matrix Action |
| :--- | :--- | :--- | :--- |
| `k = 0` | All pairs | No intermediate paths through 0 found | Unchanged |
| `k = 1` | `(0, 2)` | `dist[0][2]` (8) vs `dist[0][1] + dist[1][2]` ($3 + 1 = 4$) | `dist[0][2]` updated to 4! |
| `k = 2` | All pairs | No shorter paths found | Finalized! |

Resulting shortest path $0 \to 2$ is **4**.

## 13. Time & Space Complexity

| Metric | Complexity | Reason |
| :--- | :--- | :--- |
| **Time Complexity** | $\mathcal{O}(V^3)$ | Triple nested loop running $V \times V \times V$ iterations |
| **Space Complexity** | $\mathcal{O}(V^2)$ | Requires $V \times V$ distance matrix storage |

## 14. Advantages

- **All-Pairs Shortest Paths:** Calculates distances between every pair of nodes in a single algorithm.
- **Handles Negative Edge Weights:** Functions properly with negative edges.
- **Ultra-Simple Implementation:** Simple 3-nested loop structure without complex data structures.

## 15. Disadvantages

- **High $\mathcal{O}(V^3)$ Time:** Impractical for graphs with $V > 500$ vertices.
- **Quadratic $\mathcal{O}(V^2)$ Memory:** Requires storing a full $V \times V$ matrix.

## 16. Applications

- Computing distance matrices for Travelling Salesperson Problem (TSP).
- Network graph diameter and central node computation.
- Transitive closure of directed graphs (Warshall's algorithm).
- Finding shortest paths in dense traffic networks.

## 17. Common Mistakes

- **Incorrect Loop Order:** Putting `k` as the inner loop instead of the outermost loop (completely breaks dynamic programming correctness!).
- **Infinity Overflow:** Adding weights to `INF` without checking `dist[i][k] != INF` causing overflow into negative numbers.

## 18. Interview Questions

1. Why MUST the intermediate node loop `k` be the outermost loop in Floyd-Warshall?
2. How does Floyd-Warshall detect a negative weight cycle anywhere in the graph?
3. How can Floyd-Warshall be adapted to compute the Transitive Closure of a graph?
4. Compare Floyd-Warshall ($\mathcal{O}(V^3)$) against running Johnson's Algorithm ($\mathcal{O}(V^2 \log V + V E)$).

## 19. Practice Problems

**Easy:**
1. Implement Floyd-Warshall for a $4 \times 4$ distance matrix.
2. Find the graph diameter (maximum of all shortest path distances).

**Medium:**
3. Adapt Floyd-Warshall to reconstruct the full path sequence for any pair $(i, j)$ using a `parent[i][j]` matrix.
4. Solve Warshall's Transitive Closure reachability matrix using bitwise OR/AND logic.

**Hard:**
5. Find the minimum cycle mean in a directed weighted graph using Floyd-Warshall.

## 20. Related Algorithms

- [Dijkstra's Algorithm](./33_dijkstras_algorithm.md) (Single-source shortest path for non-negative weights)
- [Bellman-Ford Algorithm](./34_bellman_ford_algorithm.md) (Single-source shortest path with negative weights)
- [Johnson's Algorithm](./43_johnsons_algorithm.md) (All-pairs shortest path for sparse graphs)

## 21. Summary

The Floyd-Warshall Algorithm computes all-pairs shortest paths on weighted graphs in $\mathcal{O}(V^3)$ time and $\mathcal{O}(V^2)$ space. Using a 3-nested dynamic programming loop where intermediate vertex `k` is the outer loop, it handles negative edge weights and detects negative weight cycles cleanly.

## 22. Quiz

**Question 1:** What is the loop order for Floyd-Warshall algorithm?
- A) `i` outer, `j` middle, `k` inner
- B) `k` outer, `i` middle, `j` inner
- C) `j` outer, `k` middle, `i` inner
- D) Any order is fine
- **Correct Answer:** B
- **Explanation:** Intermediate vertex `k` MUST be the outermost loop for dynamic programming state dependencies to hold.

**Question 2:** What is the time complexity of Floyd-Warshall?
- A) $\mathcal{O}(V + E)$
- B) $\mathcal{O}(V \cdot E)$
- C) $\mathcal{O}(V^3)$
- D) $\mathcal{O}(V^2 \log V)$
- **Correct Answer:** C
- **Explanation:** The 3 nested loops each execute $V$ times, taking $V \times V \times V = V^3$ operations.

**Question 3:** What is the space complexity of Floyd-Warshall?
- A) $\mathcal{O}(V)$
- B) $\mathcal{O}(V^2)$
- C) $\mathcal{O}(V^3)$
- D) $\mathcal{O}(E)$
- **Correct Answer:** B
- **Explanation:** Requires a $V \times V$ distance matrix.

**Question 4:** How does Floyd-Warshall detect a negative weight cycle?
- A) If `dist[i][j] == INF`
- B) If `dist[i][i] < 0` for any vertex `i`
- C) If `k` reaches $V$
- D) If total sum is negative
- **Correct Answer:** B
- **Explanation:** A negative diagonal entry `dist[i][i] < 0` indicates a path from node $i$ back to itself with negative cost.

**Question 5:** What is Warshall's Algorithm used for?
- A) Minimum Spanning Tree
- B) Transitive Closure (Boolean reachability matrix between all pairs)
- C) Sorting
- D) Max Flow
- **Correct Answer:** B
- **Explanation:** Warshall's variant uses boolean OR/AND operations to compute reachability.

**Question 6:** For what size graph is Floyd-Warshall practically recommended?
- A) $V \le 500$
- B) $V \ge 1,000,000$
- C) Only $V = 2$
- D) Infinite graphs
- **Correct Answer:** A
- **Explanation:** $\mathcal{O}(V^3)$ scales well up to a few hundred vertices ($500^3 = 1.25 \times 10^8$ operations).

**Question 7:** Can Floyd-Warshall handle graphs with negative edge weights?
- A) No, never
- B) Yes, as long as there are no negative weight cycles
- C) Only if graph is undirected
- D) Only for integer weights
- **Correct Answer:** B
- **Explanation:** It handles negative edge weights correctly unless a negative cycle exists.

**Question 8:** What does `dist[i][k] + dist[k][j] < dist[i][j]` test?
- A) If vertex $k$ is closer to $i$ than $j$
- B) If routing from $i$ to $j$ through intermediate stopover $k$ is shorter than the direct route from $i$ to $j$
- C) If graph is bipartite
- D) If $k$ is a leaf node
- **Correct Answer:** B
- **Explanation:** This is the core triangle inequality edge relaxation step.

**Question 9:** Who published the algorithm in 1962?
- A) Robert Floyd and Stephen Warshall
- B) Edsger Dijkstra
- C) Richard Bellman
- D) Lester Ford
- **Correct Answer:** A
- **Explanation:** Robert Floyd and Stephen Warshall published it independently in 1962.

**Question 10:** What algorithm outperforms Floyd-Warshall on sparse all-pairs shortest path queries?
- A) Johnson's Algorithm ($\mathcal{O}(V^2 \log V + V E)$)
- B) Bubble Sort
- C) BFS
- D) Depth-First Search
- **Correct Answer:** A
- **Explanation:** Johnson's algorithm uses Dijkstra's with reweighting, outperforming Floyd-Warshall on sparse graphs.
