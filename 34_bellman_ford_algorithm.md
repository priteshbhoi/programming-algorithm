# Bellman-Ford Algorithm

## 1. Introduction

The Bellman-Ford Algorithm is a single-source shortest path algorithm designed by Alfonso Shimbel (1955), Richard Bellman (1958), and Lester Ford Jr. (1956). Unlike Dijkstra's Algorithm, Bellman-Ford is capable of handling graphs with **negative edge weights** and can detect the presence of **negative weight cycles** (cycles whose total edge weight sum is less than zero).

Imagine a financial currency arbitrage network where trading between currencies incurs exchange rates or fees represented as edge weights. If a sequence of trades yields a net profit, it manifests as a negative cycle where looping infinitely generates endless value. Bellman-Ford detects such impossible infinite loops and alerts the system.

It was created to solve shortest path problems on general weighted graphs where negative costs or rewards may exist.

You should use Bellman-Ford when a weighted graph may contain negative edge weights, or when you explicitly need to detect negative weight cycles in a network.

## 2. Why Use This Algorithm?

Bellman-Ford handles negative weights and identifies destructive infinite negative cycles.

**Benefits:**
- **Handles Negative Edge Weights:** Correctly computes shortest paths when some edge weights are negative.
- **Negative Cycle Detection:** Identifies if a negative weight cycle exists and reports an error.
- **Simple Dynamic Programming Design:** Relies on straightforward edge relaxation iterations.
- **Distributed Computing Friendly:** Serves as the mathematical foundation for Distance Vector routing protocols (e.g., RIP).

**Performance:**
- **Time Complexity:** $\mathcal{O}(V \cdot E)$ where $V$ is vertices and $E$ is edges.
- **Space Complexity:** $\mathcal{O}(V)$ for storing distances and parent arrays.

**When it is better than Dijkstra's algorithm:**
Dijkstra's fails or loops indefinitely on graphs with negative edge weights. Bellman-Ford guarantees correct shortest path distances or reports the existence of a negative cycle.

## 3. Real-World Applications

- **Financial Arbitrage Detection:** Detecting profitable currency exchange loops in foreign exchange markets.
- **Distance Vector Routing (RIP):** Routing Information Protocol in computer networking.
- **Electronic Design Automation:** Timing constraint verification in digital VLSI circuits.
- **Game Economics:** Calculating trade routes with discounts or cash-back rewards (negative costs).

## 4. Prerequisites

Before studying Bellman-Ford, you should understand:
- Edge Relaxation (`dist[v] = min(dist[v], dist[u] + weight)`).
- Concept of Negative Weight Cycles.
- Basic Dynamic Programming principles.

## 5. Visualization

```text
Graph with Negative Edge:
    (4)         (-5)
 0 ────> 1 ──────────> 2
 │                     ^
 └─────────────────────┘
           (2)

Source = 0
Init: dist = [0, ∞, ∞]

Pass 1 (Relax all edges):
- Edge (0->1, 4): dist[1] = min(∞, 0+4) = 4
- Edge (1->2, -5): dist[2] = min(∞, 4-5) = -1
- Edge (0->2, 2): dist[2] = min(-1, 0+2) = -1 (unchanged)
Result Pass 1: dist = [0, 4, -1]

Pass 2 (Relax all edges): No changes. Array converged early!

Final Distances: dist = [0, 4, -1]
Shortest path to Node 2 is 0 -> 1 -> 2 (Cost: 4 + (-5) = -1).
```

## 6. How It Works

1. Set `dist[src] = 0` and `dist[v] = ∞` for all other vertices.
2. **Relaxation Phase:** Repeat $V - 1$ times:
   - For every edge $(u, v)$ with weight $w$ in the graph:
     - If `dist[u] != ∞` and `dist[u] + w < dist[v]`:
       - `dist[v] = dist[u] + w`.
       - `parent[v] = u`.
3. **Negative Cycle Check Phase:** Iterate through all edges $(u, v, w)$ one final time:
   - If `dist[u] != ∞` and `dist[u] + w < dist[v]`:
     - A **Negative Weight Cycle** exists! Report error or return `false`.
4. If no negative cycle is found, return `dist`.

## 7. Step-by-Step Algorithm

1. `dist = array of size V filled with ∞`, `dist[src] = 0`.
2. For `i = 1` to `V - 1`:
   - `updated = false`
   - For each edge `(u, v, w)` in `edges`:
     - If `dist[u] != ∞` and `dist[u] + w < dist[v]`:
       - `dist[v] = dist[u] + w`.
       - `updated = true`.
   - If `updated == false`: Break early (optimization).
3. For each edge `(u, v, w)` in `edges`:
   - If `dist[u] != ∞` and `dist[u] + w < dist[v]`:
     - Return "Negative Weight Cycle Detected".
4. Return `dist`.

## 8. Pseudocode

```text
function BellmanFord(graph, source):
    dist = array of size graph.V filled with infinity
    parent = array of size graph.V filled with null
    dist[source] = 0

    // Relax all edges V - 1 times
    for i = 1 to graph.V - 1:
        for each edge (u, v, w) in graph.edges:
            if dist[u] != infinity and dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
                parent[v] = u

    // Check for negative weight cycles
    for each edge (u, v, w) in graph.edges:
        if dist[u] != infinity and dist[u] + w < dist[v]:
            print("Graph contains a negative weight cycle")
            return null, null

    return dist, parent
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>
#include <limits.h>

#define INF INT_MAX

struct Edge {
    int u, v, weight;
};

struct Graph {
    int V, E;
    struct Edge* edges;
};

struct Graph* createGraph(int V, int E) {
    struct Graph* g = (struct Graph*)malloc(sizeof(struct Graph));
    g->V = V; g->E = E;
    g->edges = (struct Edge*)malloc(E * sizeof(struct Edge));
    return g;
}

bool bellmanFord(struct Graph* g, int src) {
    int V = g->V;
    int E = g->E;
    int dist[V];

    for (int i = 0; i < V; i++) dist[i] = INF;
    dist[src] = 0;

    for (int i = 1; i <= V - 1; i++) {
        bool updated = false;
        for (int j = 0; j < E; j++) {
            int u = g->edges[j].u;
            int v = g->edges[j].v;
            int w = g->edges[j].weight;
            if (dist[u] != INF && dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                updated = true;
            }
        }
        if (!updated) break;
    }

    for (int j = 0; j < E; j++) {
        int u = g->edges[j].u;
        int v = g->edges[j].v;
        int w = g->edges[j].weight;
        if (dist[u] != INF && dist[u] + w < dist[v]) {
            printf("Graph contains a negative-weight cycle!\n");
            return false;
        }
    }

    printf("Distances from source %d:\n", src);
    for (int i = 0; i < V; i++) {
        printf("Node %d: %d\n", i, dist[i]);
    }
    return true;
}

int main() {
    int V = 4, E = 5;
    struct Graph* g = createGraph(V, E);

    g->edges[0] = (struct Edge){0, 1, 4};
    g->edges[1] = (struct Edge){0, 2, 2};
    g->edges[2] = (struct Edge){1, 2, -5};
    g->edges[3] = (struct Edge){2, 3, 2};
    g->edges[4] = (struct Edge){1, 3, 1};

    bellmanFord(g, 0);
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
#include <limits>

const int INF = std::numeric_limits<int>::max();

struct Edge {
    int u, v, weight;
};

bool bellmanFord(int src, const std::vector<Edge>& edges, int V) {
    std::vector<int> dist(V, INF);
    dist[src] = 0;

    for (int i = 1; i <= V - 1; ++i) {
        bool updated = false;
        for (const auto& edge : edges) {
            if (dist[edge.u] != INF && dist[edge.u] + edge.weight < dist[edge.v]) {
                dist[edge.v] = dist[edge.u] + edge.weight;
                updated = true;
            }
        }
        if (!updated) break;
    }

    for (const auto& edge : edges) {
        if (dist[edge.u] != INF && dist[edge.u] + edge.weight < dist[edge.v]) {
            std::cout << "Graph contains a negative-weight cycle!\n";
            return false;
        }
    }

    std::cout << "Shortest distances from source " << src << ":\n";
    for (int i = 0; i < V; ++i) {
        std::cout << "Node " << i << ": " << dist[i] << "\n";
    }
    return true;
}

int main() {
    int V = 4;
    std::vector<Edge> edges = {
        {0, 1, 4},
        {0, 2, 2},
        {1, 2, -5},
        {2, 3, 2},
        {1, 3, 1}
    };

    bellmanFord(0, edges, V);
    return 0;
}
```

### Java
```java
import java.util.*;

public class BellmanFordGraph {
    static class Edge {
        int u, v, weight;
        Edge(int u, int v, int weight) {
            this.u = u; this.v = v; this.weight = weight;
        }
    }

    public static boolean bellmanFord(int src, List<Edge> edges, int V) {
        int[] dist = new int[V];
        Arrays.fill(dist, Integer.MAX_VALUE);
        dist[src] = 0;

        for (int i = 1; i <= V - 1; i++) {
            boolean updated = false;
            for (Edge edge : edges) {
                if (dist[edge.u] != Integer.MAX_VALUE && dist[edge.u] + edge.weight < dist[edge.v]) {
                    dist[edge.v] = dist[edge.u] + edge.weight;
                    updated = true;
                }
            }
            if (!updated) break;
        }

        for (Edge edge : edges) {
            if (dist[edge.u] != Integer.MAX_VALUE && dist[edge.u] + edge.weight < dist[edge.v]) {
                System.out.println("Graph contains a negative-weight cycle!");
                return false;
            }
        }

        System.out.println("Shortest distances from source " + src + ":");
        for (int i = 0; i < V; i++) {
            System.out.println("Node " + i + ": " + dist[i]);
        }
        return true;
    }

    public static void main(String[] args) {
        int V = 4;
        List<Edge> edges = new ArrayList<>();
        edges.add(new Edge(0, 1, 4));
        edges.add(new Edge(0, 2, 2));
        edges.add(new Edge(1, 2, -5));
        edges.add(new Edge(2, 3, 2));
        edges.add(new Edge(1, 3, 1));

        bellmanFord(0, edges, V);
    }
}
```

### Python
```python
def bellman_ford(src: int, edges: list[tuple[int, int, int]], V: int) -> list[int] | None:
    dist = [float('inf')] * V
    dist[src] = 0

    for _ in range(V - 1):
        updated = False
        for u, v, w in edges:
            if dist[u] != float('inf') and dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
                updated = True
        if not updated:
            break

    for u, v, w in edges:
        if dist[u] != float('inf') and dist[u] + w < dist[v]:
            print("Graph contains a negative-weight cycle!")
            return None

    return dist

if __name__ == "__main__":
    V = 4
    edges = [
        (0, 1, 4),
        (0, 2, 2),
        (1, 2, -5),
        (2, 3, 2),
        (1, 3, 1)
    ]

    distances = bellman_ford(0, edges, V)
    if distances is not None:
        print("Distances from node 0:", distances)
```

### JavaScript
```javascript
function bellmanFord(src, edges, V) {
    const dist = new Array(V).fill(Infinity);
    dist[src] = 0;

    for (let i = 1; i <= V - 1; i++) {
        let updated = false;
        for (const { u, v, weight } of edges) {
            if (dist[u] !== Infinity && dist[u] + weight < dist[v]) {
                dist[v] = dist[u] + weight;
                updated = true;
            }
        }
        if (!updated) break;
    }

    for (const { u, v, weight } of edges) {
        if (dist[u] !== Infinity && dist[u] + weight < dist[v]) {
            console.log("Graph contains a negative-weight cycle!");
            return null;
        }
    }

    return dist;
}

const V = 4;
const edges = [
    { u: 0, v: 1, weight: 4 },
    { u: 0, v: 2, weight: 2 },
    { u: 1, v: 2, weight: -5 },
    { u: 2, v: 3, weight: 2 },
    { u: 1, v: 3, weight: 1 }
];

console.log("Distances from node 0:", bellmanFord(0, edges, V));
```

## 10. Code Explanation

Bellman-Ford relies on the structural property that any simple path without cycles in a graph of $V$ vertices contains at most $V - 1$ edges. Therefore, relaxing every edge in the graph $V - 1$ times guarantees that the shortest distance to all reachable vertices is calculated. If a distance can still be relaxed on the $V$-th pass, it proves that an infinite negative-weight cycle exists that decreases path cost indefinitely.

## 11. Interactive Demo

An interactive graph sandbox allows users to draw directed graphs with positive and negative edge weights.

- An "Execution Step" counter tracks relaxation passes $1 \dots V-1$.
- Negative edges are highlighted in Red.
- If a user creates a negative cycle (e.g., $A \to B (2) \to C (-5) \to A (1)$), the negative cycle flashes red and triggers a "Negative Cycle Alert".

## 12. Dry Run

**Graph:** $0 \xrightarrow{4} 1$, $1 \xrightarrow{-5} 2$, $0 \xrightarrow{2} 2$ ($V = 3$, $E = 3$)

| Pass | Edge Examined | Condition (`dist[u] + w < dist[v]`) | Updated `dist` Array |
| :--- | :--- | :--- | :--- |
| **Init** | - | - | `[0, ∞, ∞]` |
| 1 | $(0, 1, 4)$ | $0 + 4 < \infty \implies$ Update `dist[1] = 4` | `[0, 4, ∞]` |
| 1 | $(1, 2, -5)$ | $4 - 5 < \infty \implies$ Update `dist[2] = -1` | `[0, 4, -1]` |
| 1 | $(0, 2, 2)$ | $0 + 2 < -1$ (False) | `[0, 4, -1]` |
| 2 | All edges | No updates (`updated == false`) | `[0, 4, -1]` (Early exit!) |
| 3 (Check) | All edges | No edge can be relaxed further | Valid! Return `[0, 4, -1]` |

## 13. Time & Space Complexity

| Metric | Complexity | Reason |
| :--- | :--- | :--- |
| **Time Complexity** | $\mathcal{O}(V \cdot E)$ | $V - 1$ passes over all $E$ edges |
| **Space Complexity** | $\mathcal{O}(V)$ | Requires storing `dist` array of size $V$ |

## 14. Advantages

- **Handles Negative Edges:** Accurately computes shortest paths with negative weights.
- **Detects Negative Cycles:** Identifies infinite negative loops cleanly.
- **Simple Edge Representation:** Operates directly on a simple list of edges.

## 15. Disadvantages

- **Slower Than Dijkstra:** $\mathcal{O}(V \cdot E)$ time is significantly slower than Dijkstra's $\mathcal{O}((V + E) \log V)$.
- **Dense Graph Performance:** On dense graphs where $E = \mathcal{O}(V^2)$, runtime degrades to $\mathcal{O}(V^3)$.

## 16. Applications

- Foreign exchange (FX) currency arbitrage detection.
- Distance Vector Routing Protocol (RIP).
- VLSI design clock skew timing analysis.

## 17. Common Mistakes

- **Forgetting Infinity Check:** Writing `dist[u] + w < dist[v]` without checking `dist[u] != INF` (causes integer underflow in languages with fixed integer limits).
- **Stopping at $V-1$ Passes without Cycle Check:** Omitting the $V$-th check pass, missing negative weight cycles.

## 18. Interview Questions

1. Why does relaxing edges $V - 1$ times guarantee finding shortest simple paths?
2. How does Bellman-Ford detect a negative weight cycle?
3. What is the optimized version of Bellman-Ford called? (Answer: Shortest Path Faster Algorithm / SPFA).
4. Can Bellman-Ford handle negative edges on undirected graphs? (Answer: No, an undirected edge with negative weight is automatically a negative cycle).

## 19. Practice Problems

**Easy:**
1. Implement Bellman-Ford algorithm for a list of weighted edges.
2. Optimize Bellman-Ford to stop early if a pass performs 0 updates.

**Medium:**
3. Detect currency arbitrage opportunity given an exchange matrix using log-transformed weights.

**Hard:**
4. Implement SPFA (Shortest Path Faster Algorithm) using a Queue to optimize Bellman-Ford average time to $\mathcal{O}(E)$.

## 20. Related Algorithms

- [Dijkstra's Algorithm](./33_dijkstras_algorithm.md) (Faster algorithm for non-negative weights)
- [Floyd-Warshall Algorithm](./35_floyd_warshall_algorithm.md) (All-pairs shortest paths)
- [SPFA Algorithm](./52_spfa_algorithm.md) (Queue-based Bellman-Ford optimization)

## 21. Summary

The Bellman-Ford Algorithm computes single-source shortest paths on weighted graphs in $\mathcal{O}(V \cdot E)$ time. By relaxing all $E$ edges $V - 1$ times, it handles negative edge weights and detects negative weight cycles, serving as a core tool for financial arbitrage detection and Distance Vector routing.

## 22. Quiz

**Question 1:** Why is Bellman-Ford executed for $V - 1$ relaxation passes?
- A) Because a simple path in a graph of $V$ vertices contains at most $V - 1$ edges
- B) Because $V - 1$ is a prime number
- C) To save memory
- D) Because edges are sorted
- **Correct Answer:** A
- **Explanation:** The maximum number of edges in any simple shortest path is $V - 1$.

**Question 2:** How does Bellman-Ford detect a negative weight cycle?
- A) If the queue overflows
- B) If an edge can still be relaxed on the $V$-th pass
- C) If all distances are negative
- D) By counting vertices
- **Correct Answer:** B
- **Explanation:** If a path length decreases after $V-1$ passes, an infinite negative cycle exists.

**Question 3:** What is the time complexity of Bellman-Ford algorithm?
- A) $\mathcal{O}(V + E)$
- B) $\mathcal{O}((V + E) \log V)$
- C) $\mathcal{O}(V \cdot E)$
- D) $\mathcal{O}(V^3)$
- **Correct Answer:** C
- **Explanation:** $V-1$ iterations over $E$ edges takes $\mathcal{O}(V \cdot E)$ time.

**Question 4:** What happens if an undirected graph contains an edge with negative weight in Bellman-Ford?
- A) It is processed normally
- B) An undirected negative edge acts as a two-node negative cycle ($u \to v \to u$), triggering negative cycle detection
- C) The weight becomes positive
- D) The algorithm runs in $\mathcal{O}(1)$
- **Correct Answer:** B
- **Explanation:** Traversing back and forth across a negative undirected edge continually reduces cost to $-\infty$.

**Question 5:** Which networking protocol is built on the Bellman-Ford algorithm?
- A) OSPF
- B) RIP (Routing Information Protocol)
- C) BGP
- D) HTTP
- **Correct Answer:** B
- **Explanation:** RIP uses Distance Vector routing derived from Bellman-Ford.

**Question 6:** What is the space complexity of Bellman-Ford?
- A) $\mathcal{O}(1)$
- B) $\mathcal{O}(V)$
- C) $\mathcal{O}(V \cdot E)$
- D) $\mathcal{O}(V^2)$
- **Correct Answer:** B
- **Explanation:** Stores a `dist` array of size $V$.

**Question 7:** How can Bellman-Ford be optimized for early termination?
- A) By sorting edges
- B) By stopping if an entire pass completes without updating any distance
- C) By using a binary search tree
- D) By ignoring negative edges
- **Correct Answer:** B
- **Explanation:** If no distance changes during a pass, distances have converged and the loop can break early.

**Question 8:** What mathematical problem can be modeled as negative cycle detection using Bellman-Ford?
- A) Foreign currency arbitrage
- B) Travelling Salesman Problem
- C) Binary Search
- D) Convex Hull
- **Correct Answer:** A
- **Explanation:** Converting exchange rates to negative logs ($-\log(\text{rate})$) maps profitable trade loops to negative cycles.

**Question 9:** Who are the co-creators of the Bellman-Ford algorithm?
- A) Alfonso Shimbel, Richard Bellman, and Lester Ford Jr.
- B) Edsger Dijkstra and Robert Tarjan
- C) John von Neumann and Alan Turing
- D) Ken Batcher and Tim Peters
- **Correct Answer:** A
- **Explanation:** Shimbel (1955), Bellman (1958), and Ford (1956) developed the algorithm.

**Question 10:** What does `dist[u] != INF` check prevent in C/C++ implementations?
- A) Stack overflow
- B) Integer underflow when adding negative weights to `INF` (`INT_MAX`)
- C) Infinite loops
- D) Null pointer exceptions
- **Correct Answer:** B
- **Explanation:** Adding a negative number to `INT_MAX` causes integer overflow/underflow wrapping into negative values.
