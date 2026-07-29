# Ford-Fulkerson Algorithm for Maximum Flow

## 1. Introduction
The Ford-Fulkerson algorithm is a prominent approach in graph theory used to compute the maximum possible flow in a flow network. It was published in 1956 by L. R. Ford Jr. and D. R. Fulkerson. The core idea is simple: as long as there is a path from the source (start node) to the sink (end node) with available capacity on all edges in the path, we send flow along that path.

## 2. Why Use This Algorithm?
Network flow problems are pervasive. When you need to move items (data, water, traffic, goods) through a network of pipes/roads/links with specific capacity constraints, you want to know the maximum throughput from the starting point to the destination. Ford-Fulkerson provides a systematic way to find this upper limit by continually finding augmenting paths and adjusting the network's residual capacities.

## 3. Real-World Applications
*   **Water Distribution:** Determining the maximum amount of water that can flow through a city's pipeline network from a reservoir to neighborhoods.
*   **Traffic Control:** Finding the maximum number of vehicles that can travel from point A to point B given road capacities.
*   **Data Routing:** Calculating the maximum data bandwidth between servers in a computer network or the Internet.
*   **Bipartite Matching:** Assigning applicants to jobs where each applicant can do certain jobs, aiming to maximize the number of jobs filled.
*   **Airline Scheduling:** Optimizing flight crews and planes across different routes.

## 4. Prerequisites
Before diving into Ford-Fulkerson, you should be familiar with:
*   **Graph Theory Basics:** Vertices, edges, directed graphs.
*   **Flow Networks:** Source (s), sink (t), and capacity of an edge `C(u, v)`.
*   **Residual Graph:** A network showing remaining capacities.
*   **Graph Traversal:** Breadth-First Search (BFS) or Depth-First Search (DFS) for finding paths. (When BFS is used, the algorithm is specifically called the **Edmonds-Karp algorithm**).

## 5. Visualization (ASCII diagram & trace)
Let's consider a simple network with source `0` and sink `3`.

```
      (10)
   0 -------> 1
   |          |
(10)|          |(4)
   v          v
   2 -------> 3
      (10)
```
*   `0 -> 1`: capacity 10
*   `0 -> 2`: capacity 10
*   `1 -> 3`: capacity 4
*   `2 -> 3`: capacity 10

**Trace:**
1.  Find path: `0 -> 1 -> 3`. Bottleneck is 4 (from `1->3`). Add 4 to max flow.
    *   Update residual capacities.
2.  Find path: `0 -> 2 -> 3`. Bottleneck is 10. Add 10 to max flow.
    *   Update residual capacities.
3.  No more paths from 0 to 3 with capacity > 0.
4.  Total Max Flow = 14.

## 6. How It Works
The algorithm maintains a "residual graph" that keeps track of how much more flow can be sent along each edge. 
1.  Start with initial flow as 0.
2.  While there is an augmenting path from the source to the sink in the residual graph:
    *   Find the bottleneck capacity (minimum capacity) along the path.
    *   Add this bottleneck capacity to the total flow.
    *   Update the residual graph: subtract the bottleneck from the forward edges and add the bottleneck to the reverse edges. (Adding to reverse edges allows the algorithm to "undo" bad flow decisions later).
3.  Return the total flow.

## 7. Step-by-Step Algorithm
1.  Initialize max_flow to 0.
2.  Create a residual graph identical to the original graph's capacities.
3.  Loop: Find a simple path from `source` to `sink` in the residual graph using DFS or BFS where all edges on the path have residual capacity > 0.
4.  If no such path exists, terminate the loop.
5.  If a path exists, find the minimum capacity edge along this path. Let this be `path_flow`.
6.  Add `path_flow` to `max_flow`.
7.  For every edge `(u, v)` in the path:
    *   Decrease residual capacity of `(u, v)` by `path_flow`.
    *   Increase residual capacity of reverse edge `(v, u)` by `path_flow`.
8.  Go back to step 3.

## 8. Pseudocode
```text
function FordFulkerson(Graph G, Node S, Node T):
    max_flow = 0
    residual_graph = G.copy()

    while exists path P from S to T in residual_graph with capacity > 0:
        path_flow = minimum capacity of an edge in P
        max_flow = max_flow + path_flow
        
        for each edge (u, v) in P:
            residual_graph[u][v] -= path_flow
            residual_graph[v][u] += path_flow
            
    return max_flow
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdbool.h>
#include <string.h>

#define V 6

bool bfs(int rGraph[V][V], int s, int t, int parent[]) {
    bool visited[V];
    memset(visited, 0, sizeof(visited));
    int queue[V];
    int front = 0, rear = 0;

    queue[rear++] = s;
    visited[s] = true;
    parent[s] = -1;

    while (front < rear) {
        int u = queue[front++];
        for (int v = 0; v < V; v++) {
            if (visited[v] == false && rGraph[u][v] > 0) {
                if (v == t) {
                    parent[v] = u;
                    return true;
                }
                queue[rear++] = v;
                parent[v] = u;
                visited[v] = true;
            }
        }
    }
    return false;
}

int fordFulkerson(int graph[V][V], int s, int t) {
    int u, v;
    int rGraph[V][V];
    for (u = 0; u < V; u++)
        for (v = 0; v < V; v++)
             rGraph[u][v] = graph[u][v];

    int parent[V];
    int max_flow = 0;

    while (bfs(rGraph, s, t, parent)) {
        int path_flow = 1e9;
        for (v = t; v != s; v = parent[v]) {
            u = parent[v];
            if (rGraph[u][v] < path_flow)
                path_flow = rGraph[u][v];
        }

        for (v = t; v != s; v = parent[v]) {
            u = parent[v];
            rGraph[u][v] -= path_flow;
            rGraph[v][u] += path_flow;
        }
        max_flow += path_flow;
    }
    return max_flow;
}

int main() {
    int graph[V][V] = { 
        {0, 16, 13, 0, 0, 0},
        {0, 0, 10, 12, 0, 0},
        {0, 4, 0, 0, 14, 0},
        {0, 0, 9, 0, 0, 20},
        {0, 0, 0, 7, 0, 4},
        {0, 0, 0, 0, 0, 0}
    };
    printf("The maximum possible flow is %d\n", fordFulkerson(graph, 0, 5));
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <limits.h>
#include <queue>
#include <string.h>

using namespace std;
#define V 6

bool bfs(int rGraph[V][V], int s, int t, int parent[]) {
    bool visited[V];
    memset(visited, 0, sizeof(visited));
    queue<int> q;
    q.push(s);
    visited[s] = true;
    parent[s] = -1;

    while (!q.empty()) {
        int u = q.front();
        q.pop();

        for (int v = 0; v < V; v++) {
            if (visited[v] == false && rGraph[u][v] > 0) {
                if (v == t) {
                    parent[v] = u;
                    return true;
                }
                q.push(v);
                parent[v] = u;
                visited[v] = true;
            }
        }
    }
    return false;
}

int fordFulkerson(int graph[V][V], int s, int t) {
    int u, v;
    int rGraph[V][V];
    for (u = 0; u < V; u++)
        for (v = 0; v < V; v++)
            rGraph[u][v] = graph[u][v];

    int parent[V];
    int max_flow = 0;

    while (bfs(rGraph, s, t, parent)) {
        int path_flow = INT_MAX;
        for (v = t; v != s; v = parent[v]) {
            u = parent[v];
            path_flow = min(path_flow, rGraph[u][v]);
        }

        for (v = t; v != s; v = parent[v]) {
            u = parent[v];
            rGraph[u][v] -= path_flow;
            rGraph[v][u] += path_flow;
        }
        max_flow += path_flow;
    }
    return max_flow;
}

int main() {
    int graph[V][V] = { 
        {0, 16, 13, 0, 0, 0},
        {0, 0, 10, 12, 0, 0},
        {0, 4, 0, 0, 14, 0},
        {0, 0, 9, 0, 0, 20},
        {0, 0, 0, 7, 0, 4},
        {0, 0, 0, 0, 0, 0}
    };
    cout << "The maximum possible flow is " << fordFulkerson(graph, 0, 5) << endl;
    return 0;
}
```

### Java
```java
import java.util.*;

class MaxFlow {
    static final int V = 6;

    boolean bfs(int rGraph[][], int s, int t, int parent[]) {
        boolean visited[] = new boolean[V];
        for (int i = 0; i < V; ++i)
            visited[i] = false;

        LinkedList<Integer> queue = new LinkedList<Integer>();
        queue.add(s);
        visited[s] = true;
        parent[s] = -1;

        while (queue.size() != 0) {
            int u = queue.poll();
            for (int v = 0; v < V; v++) {
                if (visited[v] == false && rGraph[u][v] > 0) {
                    if (v == t) {
                        parent[v] = u;
                        return true;
                    }
                    queue.add(v);
                    parent[v] = u;
                    visited[v] = true;
                }
            }
        }
        return false;
    }

    int fordFulkerson(int graph[][], int s, int t) {
        int u, v;
        int rGraph[][] = new int[V][V];
        for (u = 0; u < V; u++)
            for (v = 0; v < V; v++)
                rGraph[u][v] = graph[u][v];

        int parent[] = new int[V];
        int max_flow = 0;

        while (bfs(rGraph, s, t, parent)) {
            int path_flow = Integer.MAX_VALUE;
            for (v = t; v != s; v = parent[v]) {
                u = parent[v];
                path_flow = Math.min(path_flow, rGraph[u][v]);
            }

            for (v = t; v != s; v = parent[v]) {
                u = parent[v];
                rGraph[u][v] -= path_flow;
                rGraph[v][u] += path_flow;
            }
            max_flow += path_flow;
        }
        return max_flow;
    }

    public static void main(String args[]) throws Exception {
        int graph[][] = new int[][] {
            { 0, 16, 13, 0, 0, 0 }, 
            { 0, 0, 10, 12, 0, 0 },
            { 0, 4, 0, 0, 14, 0 }, 
            { 0, 0, 9, 0, 0, 20 },
            { 0, 0, 0, 7, 0, 4 }, 
            { 0, 0, 0, 0, 0, 0 } 
        };
        MaxFlow m = new MaxFlow();
        System.out.println("The maximum possible flow is " + m.fordFulkerson(graph, 0, 5));
    }
}
```

### Python
```python
class Graph:
    def __init__(self, graph):
        self.graph = graph
        self.ROW = len(graph)

    def bfs(self, s, t, parent):
        visited = [False] * self.ROW
        queue = []
        queue.append(s)
        visited[s] = True
        
        while queue:
            u = queue.pop(0)
            for ind, val in enumerate(self.graph[u]):
                if visited[ind] == False and val > 0:
                    queue.append(ind)
                    visited[ind] = True
                    parent[ind] = u
                    if ind == t:
                        return True
        return False
             
    def ford_fulkerson(self, source, sink):
        parent = [-1] * self.ROW
        max_flow = 0
        
        while self.bfs(source, sink, parent):
            path_flow = float("Inf")
            s = sink
            while s != source:
                path_flow = min(path_flow, self.graph[parent[s]][s])
                s = parent[s]
                
            max_flow += path_flow
            
            v = sink
            while v != source:
                u = parent[v]
                self.graph[u][v] -= path_flow
                self.graph[v][u] += path_flow
                v = parent[v]
                
        return max_flow

if __name__ == "__main__":
    graph = [
        [0, 16, 13, 0, 0, 0],
        [0, 0, 10, 12, 0, 0],
        [0, 4, 0, 0, 14, 0],
        [0, 0, 9, 0, 0, 20],
        [0, 0, 0, 7, 0, 4],
        [0, 0, 0, 0, 0, 0]
    ]
    g = Graph(graph)
    print("The maximum possible flow is %d" % g.ford_fulkerson(0, 5))
```

### JavaScript
```javascript
function bfs(rGraph, s, t, parent, V) {
    let visited = new Array(V).fill(false);
    let queue = [];
    queue.push(s);
    visited[s] = true;
    parent[s] = -1;

    while (queue.length !== 0) {
        let u = queue.shift();
        for (let v = 0; v < V; v++) {
            if (visited[v] === false && rGraph[u][v] > 0) {
                if (v === t) {
                    parent[v] = u;
                    return true;
                }
                queue.push(v);
                parent[v] = u;
                visited[v] = true;
            }
        }
    }
    return false;
}

function fordFulkerson(graph, s, t, V) {
    let rGraph = new Array(V);
    for (let u = 0; u < V; u++) {
        rGraph[u] = new Array(V);
        for (let v = 0; v < V; v++) {
            rGraph[u][v] = graph[u][v];
        }
    }

    let parent = new Array(V);
    let max_flow = 0;

    while (bfs(rGraph, s, t, parent, V)) {
        let path_flow = Number.MAX_VALUE;
        for (let v = t; v !== s; v = parent[v]) {
            let u = parent[v];
            path_flow = Math.min(path_flow, rGraph[u][v]);
        }

        for (let v = t; v !== s; v = parent[v]) {
            let u = parent[v];
            rGraph[u][v] -= path_flow;
            rGraph[v][u] += path_flow;
        }
        max_flow += path_flow;
    }
    return max_flow;
}

// Driver Code
function main() {
    let graph = [
        [0, 16, 13, 0, 0, 0],
        [0, 0, 10, 12, 0, 0],
        [0, 4, 0, 0, 14, 0],
        [0, 0, 9, 0, 0, 20],
        [0, 0, 0, 7, 0, 4],
        [0, 0, 0, 0, 0, 0]
    ];
    let V = 6;
    console.log("The maximum possible flow is " + fordFulkerson(graph, 0, 5, V));
}

main();
```

## 10. Code Explanation
*   **Adjacency Matrix (`graph`)**: The network is represented as a 2D array where `graph[i][j]` is the capacity from node `i` to node `j`.
*   **Residual Graph (`rGraph`)**: It begins as a copy of the original graph. As flow is pushed, capacities decrease along the path and increase in the reverse direction.
*   **Path Finding (`bfs`)**: A Breadth-First Search is used to find any augmenting path from source `s` to sink `t`. It builds a `parent` array to reconstruct the path backwards from sink to source.
*   **Bottleneck (`path_flow`)**: We trace back from `t` to `s` using `parent` to find the minimum capacity among the edges in the found path.
*   **Residual Update**: For every edge `(u, v)` in the path, `rGraph[u][v]` is reduced by `path_flow`, and `rGraph[v][u]` is increased by `path_flow`.
*   **Termination**: The loop terminates when `bfs` can no longer reach `t` from `s`. 

## 11. Interactive Demo
To build an interactive UI demo of this algorithm, use the following specifications:
*   **Graph Editor:** An HTML5 Canvas or SVG based drag-and-drop editor to create nodes and directed edges with capacities.
*   **Algorithm Controls:** Play, Pause, Step Forward, and Step Backward buttons.
*   **Visual States:**
    *   Highlight augmenting paths in a distinct color (e.g., green).
    *   Show current flow vs. capacity on edges as `flow / capacity` (e.g., `5 / 10`).
    *   Animate the "bottle-neck" calculation visually by tracing the path and highlighting the minimum value.
*   **Residual Graph View:** A toggle to view the internal residual graph state showing reverse edges.

## 12. Dry Run
Let's do a dry run on a 4-node graph:
Edges: 0->1(10), 0->2(5), 1->2(15), 1->3(5), 2->3(10). Source 0, Sink 3.

| Iteration | Path Found | Bottleneck Capacity (path_flow) | Cumulative Max Flow | Residual Updates (decreased/increased) |
|---|---|---|---|---|
| 1 | 0 -> 1 -> 3 | min(10, 5) = 5 | 0 + 5 = 5 | 0->1 (-5), 1->0 (+5); 1->3 (-5), 3->1 (+5) |
| 2 | 0 -> 2 -> 3 | min(5, 10) = 5 | 5 + 5 = 10 | 0->2 (-5), 2->0 (+5); 2->3 (-5), 3->2 (+5) |
| 3 | 0 -> 1 -> 2 -> 3 | min(5, 15, 5) = 5 | 10 + 5 = 15 | 0->1 (-5), 1->2 (-5), 2->3 (-5)... reverse edges +5 |
| 4 | None | N/A | 15 | Loop terminates |

## 13. Time & Space Complexity

| Complexity | Ford-Fulkerson (DFS) | Edmonds-Karp (BFS) | Explanation |
| :--- | :--- | :--- | :--- |
| **Time Complexity** | $O(E \times \text{MaxFlow})$ | $O(V \times E^2)$ | In FF with DFS, max flow increases by at least 1 each iteration. Path finding takes $O(E)$. EK uses BFS, finding shortest paths, bounding augmenting paths to $O(V \times E)$. |
| **Space Complexity** | $O(V^2)$ or $O(V+E)$ | $O(V^2)$ or $O(V+E)$ | Space is used for the residual graph representation and the `parent` array/recursion stack. Adjacency matrix takes $O(V^2)$, adjacency list takes $O(V+E)$. |

## 14. Advantages
*   Conceptually simple and easy to understand.
*   Guarantees finding the maximum flow for graphs with integer capacities.
*   Forms the foundation for more advanced network flow algorithms.
*   Provides both the maximum flow and the min-cut of the network (Max-Flow Min-Cut Theorem).

## 15. Disadvantages
*   If capacities are irrational numbers, the algorithm might not terminate and may not even converge to the correct max flow.
*   Time complexity is dependent on the maximum flow value (if using DFS). A large max flow with a bad choice of paths can lead to a very slow execution time (e.g., bouncing flow back and forth across a bottleneck edge).
*   Residual graphs can be memory-intensive if represented inefficiently.

## 16. Applications
*   **Bipartite Matching:** Max bipartite matching can be solved by adding a supersource and supersink and setting all edge capacities to 1.
*   **Circulation with Demands:** Ensuring a supply chain can meet all point demands given link constraints.
*   **Image Segmentation:** Dividing an image into foreground and background in computer vision uses max-flow min-cut.
*   **Project Selection:** Choosing a set of projects that maximize profit subject to prerequisites.

## 17. Common Mistakes
*   **Forgetting to add reverse edges:** Adding flow to the reverse edge in the residual graph is crucial because it allows the algorithm to "push back" flow if a previously chosen path is suboptimal.
*   **Assuming it's always polynomial time:** Standard Ford-Fulkerson (DFS) is pseudo-polynomial. Only the Edmonds-Karp implementation (BFS) is polynomial $O(V \cdot E^2)$.
*   **Incorrect initialization of the residual graph:** It must copy the original capacities exactly at the start.

## 18. Interview Questions
1.  **What is the Max-Flow Min-Cut Theorem?** 
    *   It states that the maximum flow in a network is exactly equal to the capacity of the minimum cut separating the source and the sink.
2.  **Why do we need reverse edges in the residual graph?**
    *   To allow the algorithm to "undo" suboptimal flow assignments made in earlier steps.
3.  **What is the difference between Ford-Fulkerson and Edmonds-Karp?**
    *   Edmonds-Karp is an implementation of Ford-Fulkerson that uses BFS to find augmenting paths, ensuring polynomial time complexity.
4.  **Can Ford-Fulkerson handle graphs with cycles?**
    *   Yes, it inherently handles cycles correctly due to the residual graph mechanism.
5.  **What happens if capacities are floating-point numbers?**
    *   The algorithm may not terminate and can converge to an incorrect value if capacities are irrational.
6.  **How do you find the edges that form the minimum cut after running the algorithm?**
    *   Run BFS/DFS from the source in the final residual graph. The min-cut edges are those from a visited node to an unvisited node in the original graph.
7.  **How can we use Max Flow to solve maximum bipartite matching?**
    *   Connect a super-source to all left nodes with capacity 1, connect all right nodes to a super-sink with capacity 1, and set all original edges to capacity 1.
8.  **Does multiple sources and sinks change the algorithm?**
    *   You can reduce it to single source/sink by adding a super-source connecting to all sources and a super-sink connected from all sinks with infinite capacity.
9.  **What is a blocking flow?**
    *   A flow where every path from source to sink has at least one saturated edge (used in Dinic's algorithm).
10. **Is Ford-Fulkerson a Greedy Algorithm?**
    *   No, it's a dynamic algorithm that uses a form of backtracking (via reverse residual edges) to correct greedy mistakes.

## 19. Practice Problems
*   **Easy:**
    1.  Implement standard Ford-Fulkerson with an adjacency matrix.
    2.  Find max flow in a tree structure.
    3.  Check if a given flow network is a valid flow.
    4.  Convert a graph with multiple sources into a single source graph.
*   **Medium:**
    5.  Implement Edmonds-Karp algorithm.
    6.  Maximum Bipartite Matching using Max Flow.
    7.  Find the minimum cut of a given flow network.
    8.  Determine if there is a path with capacity > $K$.
*   **Hard:**
    9.  Implement Dinic's Algorithm for faster max flow.
    10. Project Selection Problem (Max-weight closure problem).
    11. Implement Push-Relabel algorithm.

## 20. Related Algorithms
*   **[Edmonds-Karp Algorithm](https://en.wikipedia.org/wiki/Edmonds%E2%80%93Karp_algorithm):** An implementation of Ford-Fulkerson using BFS.
*   **[Dinic's Algorithm](https://en.wikipedia.org/wiki/Dinic%27s_algorithm):** Uses level graphs and blocking flows for $O(V^2E)$ time.
*   **[Push-Relabel Algorithm](https://en.wikipedia.org/wiki/Push%E2%80%93relabel_maximum_flow_algorithm):** Localized flow pushing, $O(V^3)$ or $O(V^2 \sqrt{E})$.
*   **[Karger's Algorithm](https://en.wikipedia.org/wiki/Karger%27s_algorithm):** Randomized algorithm for finding a global minimum cut.

## 21. Summary
The Ford-Fulkerson algorithm is the foundational method for solving network flow problems. By repeatedly finding paths with available capacity and pushing flow through them while maintaining a residual graph to allow for "undoing" flow, it accurately identifies the maximum flow. While the basic DFS version can be slow depending on capacities, the BFS version (Edmonds-Karp) ensures a strong polynomial time bound.

## 22. Quiz
**Question 1:** What graph traversal algorithm does Edmonds-Karp use to find augmenting paths?
A) DFS
B) BFS
C) Dijkstra's
D) A* Search
**Correct Answer:** B
**Explanation:** Edmonds-Karp specifically uses Breadth-First Search (BFS) to find the shortest augmenting path (in terms of number of edges), which guarantees polynomial time complexity.

**Question 2:** What is the primary purpose of the residual graph in Ford-Fulkerson?
A) To speed up pathfinding.
B) To allow the algorithm to decrease flow on certain edges, effectively undoing bad decisions.
C) To compress the graph representation.
D) To avoid cycles.
**Correct Answer:** B
**Explanation:** By adding capacity to reverse edges when flow is pushed forward, the residual graph allows subsequent iterations to push flow back, canceling out suboptimal paths.

**Question 3:** What is the worst-case time complexity of the standard Ford-Fulkerson algorithm (using DFS)?
A) $O(V \cdot E^2)$
B) $O(V \cdot E)$
C) $O(E \cdot \text{max\_flow})$
D) $O(V^3)$
**Correct Answer:** C
**Explanation:** In the worst case, the algorithm might only add 1 unit of flow per iteration. Finding a path takes $O(E)$, so the total time is proportional to the maximum flow value.

**Question 4:** What theorem states that the maximum flow is equal to the capacity of the minimum cut?
A) Min-Max Theorem
B) Max-Flow Min-Cut Theorem
C) Euler's Theorem
D) Hall's Marriage Theorem
**Correct Answer:** B
**Explanation:** The Max-Flow Min-Cut Theorem is a fundamental theorem in network flow theory proving this exact relationship.

**Question 5:** Which condition can cause the standard Ford-Fulkerson algorithm to fail to terminate?
A) Graphs with negative edge weights
B) Graphs with cycles
C) Edge capacities that are irrational numbers
D) Graphs with multiple sources and sinks
**Correct Answer:** C
**Explanation:** If capacities are irrational, the algorithm might take infinitely small steps and fail to terminate or converge to the correct max flow.

**Question 6:** How do you handle multiple sources and multiple sinks in a flow network?
A) Run the algorithm separately for each pair.
B) Add a super-source connected to all sources and a super-sink connected from all sinks.
C) It is impossible to solve with Ford-Fulkerson.
D) Average the capacities of the sources.
**Correct Answer:** B
**Explanation:** Adding a super-source with infinite capacity edges to all original sources, and similar for a super-sink, reduces the problem to a standard single-source single-sink network.

**Question 7:** If an augmenting path is found with edges having capacities 10, 5, and 8, what is the path flow?
A) 10
B) 8
C) 5
D) 23
**Correct Answer:** C
**Explanation:** The path flow is determined by the bottleneck edge, which is the edge with the minimum capacity along the path (5 in this case).

**Question 8:** When does the Ford-Fulkerson algorithm terminate?
A) When all edges are saturated.
B) When there are no more nodes to visit.
C) When no augmenting path can be found in the residual graph from source to sink.
D) After $V$ iterations.
**Correct Answer:** C
**Explanation:** The algorithm continues as long as there is a valid path with capacity > 0 from source to sink in the residual graph. Once no such path exists, it terminates.

**Question 9:** In max bipartite matching using network flow, what are the edge capacities set to?
A) Infinity
B) The number of nodes
C) 0
D) 1
**Correct Answer:** D
**Explanation:** Setting capacities to 1 ensures that each node from the left set is matched to at most one node from the right set.

**Question 10:** Which of the following is NOT a direct application of the Max Flow algorithm?
A) Airline Crew Scheduling
B) Finding the shortest path between two nodes
C) Water distribution network optimization
D) Maximum Bipartite Matching
**Correct Answer:** B
**Explanation:** Finding the shortest path is solved using algorithms like Dijkstra's or Bellman-Ford, not max flow algorithms, which are concerned with capacity and throughput rather than distance.
