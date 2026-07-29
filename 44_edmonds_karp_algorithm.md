# Edmonds-Karp Maximum Flow Algorithm

## 1. Introduction
The Edmonds-Karp algorithm is a specific implementation of the Ford-Fulkerson method for computing the maximum flow in a flow network in $O(V E^2)$ time. It was first published by Yefim Dinitz in 1970 and independently published by Jack Edmonds and Richard Karp in 1972. The core idea is identical to Ford-Fulkerson, except that the search order when finding the augmenting path is defined. The path found must be a shortest path that has available capacity, which can be found by a breadth-first search (BFS), letting edges have unit length.

In flow networks, we often need to find the maximum possible amount of a commodity that can be routed from a single source node to a single sink node. The Edmonds-Karp algorithm guarantees termination and provides a strongly polynomial time bound, unlike the generic Ford-Fulkerson algorithm which can perform poorly or even fail to terminate if edge capacities are irrational numbers.

## 2. Why Use This Algorithm?
While the generic Ford-Fulkerson algorithm uses Depth First Search (DFS) to find augmenting paths, this can lead to inefficient path selections, especially in graphs with "bottleneck" edges. This inefficiency can result in $O(E \cdot |f^*|)$ time complexity, where $|f^*|$ is the maximum flow. This is pseudo-polynomial and highly dependent on the maximum capacity.

Edmonds-Karp solves this by using Breadth-First Search (BFS) to find augmenting paths. By always picking the shortest augmenting path (in terms of number of edges), it guarantees that the algorithm will terminate in at most $O(V E)$ augmentations. Since each BFS takes $O(E)$ time, the overall time complexity is $O(V E^2)$. This is strongly polynomial, meaning the running time depends only on the number of vertices and edges, not on the capacities of the edges. Thus, it is the algorithm of choice for many practical network flow problems where capacities can be arbitrarily large.

## 3. Real-World Applications
The Edmonds-Karp algorithm (and max flow algorithms in general) are widely used to solve practical problems:
*   **Routing in Networks:** Determining the maximum amount of data that can be sent through a computer network (like the Internet) given bandwidth limits on connections.
*   **Transportation Logistics:** Maximizing the flow of goods from warehouses (sources) to distribution centers (sinks) over a network of roads or railways with limited transport capacities.
*   **Bipartite Matching:** Finding maximum pairings, such as assigning applicants to jobs they are qualified for, or matching students to schools.
*   **Image Segmentation:** Separating the foreground of an image from the background in computer vision by constructing a flow network of pixels.
*   **Water Supply Networks:** Analyzing municipal water pipes to determine the maximum flow from a reservoir to city neighborhoods.

## 4. Prerequisites
To fully understand the Edmonds-Karp algorithm, you should be familiar with:
*   **Graph Theory:** Understanding of directed graphs, vertices (nodes), edges, and paths.
*   **Flow Networks:** Concepts of source ($s$), sink ($t$), edge capacities ($c$), and flows ($f$).
*   **Breadth-First Search (BFS):** Knowing how BFS traverses a graph level by level and finds the shortest path in unweighted graphs.
*   **Residual Graph:** A network that indicates additional possible flow. If there's an edge $(u, v)$ with capacity $C$ and current flow $F$, the residual capacity is $C - F$. Also, we add a reverse edge $(v, u)$ with residual capacity $F$, which represents the ability to "push back" or cancel existing flow to optimize the overall flow.

## 5. Visualization
Imagine a network of pipes with different diameters. Water flows from a main source to a destination.
1.  **Initial State:** All pipes are empty (flow = 0).
2.  **Iteration 1:** We find the shortest path (fewest pipes) from source to destination. We push as much water as possible through this path until one pipe is completely full (the bottleneck).
3.  **Iteration 2:** We look for another shortest path. The "push back" mechanism (reverse edges in the residual graph) allows us to redirect water that was sent down a suboptimal path.
4.  **Termination:** We repeat this process. The algorithm stops when there is no longer any path from the source to the destination in the residual graph, meaning we have achieved the absolute maximum possible flow.

## 6. How It Works
The Edmonds-Karp algorithm follows these core principles:
1.  **Initialize Flow:** Start with 0 flow on all edges.
2.  **Residual Graph Maintenance:** At every step, maintain a residual graph that represents the remaining capacities of all edges.
3.  **Find Shortest Augmenting Path:** Use BFS to find a path from the source to the sink in the residual graph. BFS ensures we find the path with the minimum number of edges.
4.  **Find Bottleneck:** The maximum flow we can push through this augmenting path is determined by the edge with the smallest residual capacity on this path (the bottleneck).
5.  **Augment Flow:** Increase the flow along the path by the bottleneck value. Simultaneously, decrease the capacity of the forward edges and increase the capacity of the reverse edges in the residual graph by the bottleneck value.
6.  **Repeat:** Go back to step 3. If BFS cannot reach the sink, it means no more augmenting paths exist, and the current flow is the maximum flow.

## 7. Step-by-Step Algorithm
1.  **Input:** A directed graph $G = (V, E)$, a source vertex $s \in V$, a sink vertex $t \in V$, and a capacity function $c(u, v)$ for each edge $(u, v) \in E$.
2.  **Initialize:** Create a 2D array or adjacency list to represent the residual capacities, initialized to the given capacities $c(u, v)$. Create an array `parent` to keep track of the augmenting path found by BFS. Initialize `max_flow = 0`.
3.  **Loop:** While there exists a path from $s$ to $t$ in the residual graph (found using BFS):
    *   **Find Path capacity (Bottleneck):** Trace back the path from $t$ to $s$ using the `parent` array. Find the minimum residual capacity among all edges in this path. Let this be `path_flow`.
    *   **Update Residual Capacities:** Trace back the path again from $t$ to $s$. For each edge $(u, v)$ in the path:
        *   Subtract `path_flow` from the residual capacity of $(u, v)$.
        *   Add `path_flow` to the residual capacity of the reverse edge $(v, u)$.
    *   **Add to Max Flow:** `max_flow = max_flow + path_flow`.
4.  **Output:** Return `max_flow`.

## 8. Pseudocode
```text
function EdmondsKarp(graph, source, sink):
    max_flow = 0
    residual_graph = copy of graph capacities
    
    while BFS(residual_graph, source, sink, parent_array):
        // Find bottleneck capacity along the path
        path_flow = INFINITY
        curr = sink
        while curr != source:
            prev = parent_array[curr]
            path_flow = min(path_flow, residual_graph[prev][curr])
            curr = prev
            
        // Update residual capacities and reverse edges
        curr = sink
        while curr != source:
            prev = parent_array[curr]
            residual_graph[prev][curr] -= path_flow
            residual_graph[curr][prev] += path_flow
            curr = prev
            
        max_flow += path_flow
        
    return max_flow

function BFS(residual_graph, source, sink, parent_array):
    visited = array of booleans initialized to false
    queue = empty Queue
    
    queue.enqueue(source)
    visited[source] = true
    parent_array[source] = -1
    
    while queue is not empty:
        u = queue.dequeue()
        
        for each vertex v adjacent to u:
            if not visited[v] and residual_graph[u][v] > 0:
                queue.enqueue(v)
                parent_array[v] = u
                visited[v] = true
                
                if v == sink:
                    return true
                    
    return false
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <limits.h>
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

int edmondsKarp(int graph[V][V], int s, int t) {
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
    
    printf("The maximum possible flow is %d\n", edmondsKarp(graph, 0, 5));
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <climits>

using namespace std;

#define V 6

bool bfs(vector<vector<int>>& rGraph, int s, int t, vector<int>& parent) {
    vector<bool> visited(V, false);
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

int edmondsKarp(vector<vector<int>>& graph, int s, int t) {
    int u, v;
    vector<vector<int>> rGraph = graph;
    vector<int> parent(V);
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
    vector<vector<int>> graph = {
        {0, 16, 13, 0, 0, 0},
        {0, 0, 10, 12, 0, 0},
        {0, 4, 0, 0, 14, 0},
        {0, 0, 9, 0, 0, 20},
        {0, 0, 0, 7, 0, 4},
        {0, 0, 0, 0, 0, 0}
    };
    
    cout << "The maximum possible flow is " << edmondsKarp(graph, 0, 5) << endl;
    return 0;
}
```

### Java
```java
import java.util.*;

class EdmondsKarp {
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

    int edmondsKarp(int graph[][], int s, int t) {
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

    public static void main(String java[]) throws java.lang.Exception {
        int graph[][] = new int[][] {
            { 0, 16, 13, 0, 0, 0 },
            { 0, 0, 10, 12, 0, 0 },
            { 0, 4, 0, 0, 14, 0 },
            { 0, 0, 9, 0, 0, 20 },
            { 0, 0, 0, 7, 0, 4 },
            { 0, 0, 0, 0, 0, 0 }
        };
        EdmondsKarp m = new EdmondsKarp();
        System.out.println("The maximum possible flow is " + m.edmondsKarp(graph, 0, 5));
    }
}
```

### Python
```python
import collections

class Graph:
    def __init__(self, graph):
        self.graph = graph
        self.ROW = len(graph)

    def BFS(self, s, t, parent):
        visited = [False] * (self.ROW)
        queue = collections.deque([s])
        visited[s] = True
        
        while queue:
            u = queue.popleft()
            
            for ind, val in enumerate(self.graph[u]):
                if visited[ind] == False and val > 0:
                    queue.append(ind)
                    visited[ind] = True
                    parent[ind] = u
                    if ind == t:
                        return True
        return False

    def EdmondsKarp(self, source, sink):
        parent = [-1] * (self.ROW)
        max_flow = 0
        
        while self.BFS(source, sink, parent):
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
    print("The maximum possible flow is %d " % g.EdmondsKarp(0, 5))
```

### JavaScript
```javascript
class Graph {
    constructor(graph) {
        this.graph = graph;
        this.V = graph.length;
    }

    bfs(s, t, parent) {
        let visited = new Array(this.V).fill(false);
        let queue = [];
        
        queue.push(s);
        visited[s] = true;
        parent[s] = -1;

        while (queue.length !== 0) {
            let u = queue.shift();

            for (let v = 0; v < this.V; v++) {
                if (visited[v] === false && this.graph[u][v] > 0) {
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

    edmondsKarp(s, t) {
        let u, v;
        // In JavaScript we can modify the graph directly or make a deep copy. 
        // Modifying directly for brevity.
        let parent = new Array(this.V);
        let max_flow = 0;

        while (this.bfs(s, t, parent)) {
            let path_flow = Number.MAX_VALUE;
            
            for (v = t; v != s; v = parent[v]) {
                u = parent[v];
                path_flow = Math.min(path_flow, this.graph[u][v]);
            }

            for (v = t; v != s; v = parent[v]) {
                u = parent[v];
                this.graph[u][v] -= path_flow;
                this.graph[v][u] += path_flow;
            }

            max_flow += path_flow;
        }
        return max_flow;
    }
}

const graphMatrix = [
    [0, 16, 13, 0, 0, 0],
    [0, 0, 10, 12, 0, 0],
    [0, 4, 0, 0, 14, 0],
    [0, 0, 9, 0, 0, 20],
    [0, 0, 0, 7, 0, 4],
    [0, 0, 0, 0, 0, 0]
];

const g = new Graph(graphMatrix);
console.log("The maximum possible flow is " + g.edmondsKarp(0, 5));
```

## 10. Code Explanation
The implementations across all languages share the same core logic:
1.  **Graph Representation:** We use a 2D adjacency matrix `graph[V][V]` to represent the capacity of each edge. `graph[u][v]` is the capacity from vertex `u` to vertex `v`.
2.  **`bfs` function:** Performs a standard Breadth-First Search. It uses a queue to visit nodes level by level. It only traverses edges where the residual capacity (`rGraph[u][v] > 0`) is strictly positive. It populates a `parent` array to allow us to reconstruct the shortest augmenting path later. It returns `true` if the sink `t` is reached, `false` otherwise.
3.  **`edmondsKarp` function:**
    *   Creates a `rGraph` (residual graph) initialized with the original capacities. (Note: in Python and JS examples above, we modify the original matrix directly for brevity, but a true implementation often uses a deep copy to preserve original capacities).
    *   Initializes `max_flow = 0`.
    *   Enters a `while` loop that continues as long as `bfs` returns `true`.
    *   **Inside the loop:**
        *   Traces back the path from sink to source using the `parent` array to find the minimum capacity edge along that path (`path_flow`).
        *   Traces back the path again to update the capacities. It subtracts `path_flow` from forward edges (`rGraph[u][v] -= path_flow`) and adds it to reverse edges (`rGraph[v][u] += path_flow`). This critical step implements the "push back" ability.
        *   Adds `path_flow` to the total `max_flow`.
4.  **Final Output:** Once no more paths can be found, the loop terminates and returns the accumulated `max_flow`.

## 11. Interactive Demo
*(Imagine an interactive web component here)*
Users can define a graph by adding nodes (circles) and directed edges (arrows). They assign numerical capacities to each edge. By clicking "Step Forward", the UI highlights the BFS search level by level. Once an augmenting path is found, it is highlighted in bold. The bottleneck capacity is identified, and an animation shows the flow "filling up" the pipes. The residual edges (reverse arrows) dynamically appear or update their values. Users can see how poor initial paths are corrected via the reverse edges.

## 12. Dry Run
Let's use a small graph: 4 nodes `S` (0), `A` (1), `B` (2), `T` (3).
Edges and Capacities: `(S, A)=10, (S, B)=5, (A, B)=15, (A, T)=5, (B, T)=10`.

**Initialization:**
*   Max Flow = 0.
*   Residual Graph = Initial capacities.

**Iteration 1:**
1.  **BFS Search:** Starts at `S`. Sees `A` (10) and `B` (5). Let's say it finds path `S -> A -> T`.
2.  **Bottleneck:** Path is `S(10)->A(5)->T`. Minimum capacity is 5.
3.  **Update Residual Graph:**
    *   `S->A`: $10 - 5 = 5$
    *   `A->S`: $0 + 5 = 5$
    *   `A->T`: $5 - 5 = 0$
    *   `T->A`: $0 + 5 = 5$
4.  **Max Flow:** $0 + 5 = 5$.

**Iteration 2:**
1.  **BFS Search:** Starts at `S`. Can go to `A` (5) or `B` (5). `A->T` is 0. So it finds `S -> B -> T`.
2.  **Bottleneck:** Path is `S(5)->B(10)->T`. Minimum capacity is 5.
3.  **Update Residual Graph:**
    *   `S->B`: $5 - 5 = 0$
    *   `B->S`: $0 + 5 = 5$
    *   `B->T`: $10 - 5 = 5$
    *   `T->B`: $0 + 5 = 5$
4.  **Max Flow:** $5 + 5 = 10$.

**Iteration 3:**
1.  **BFS Search:** Starts at `S`. Only edge from S with capacity > 0 is `S->A` (capacity 5). From `A`, can go to `B` (capacity 15) or `S` (capacity 5). Go to `B`. From `B`, can go to `T` (capacity 5). Path found: `S -> A -> B -> T`.
2.  **Bottleneck:** Path is `S(5)->A(15)->B(5)->T`. Minimum capacity is 5.
3.  **Update Residual Graph:**
    *   `S->A`: $5 - 5 = 0$
    *   `A->S`: $5 + 5 = 10$
    *   `A->B`: $15 - 5 = 10$
    *   `B->A`: $0 + 5 = 5$
    *   `B->T`: $5 - 5 = 0$
    *   `T->B`: $5 + 5 = 10$
4.  **Max Flow:** $10 + 5 = 15$.

**Iteration 4:**
1.  **BFS Search:** Starts at `S`. `S->A` is 0, `S->B` is 0. Queue is empty. BFS returns false.
**Final Max Flow = 15.**

## 13. Time & Space Complexity
*   **Time Complexity:** $O(V E^2)$
    *   In the worst case, the number of augmenting paths found by BFS is at most $O(V E)$.
    *   Finding a single augmenting path using BFS takes $O(E)$ time.
    *   Therefore, the total time complexity is $O(V \cdot E \cdot E) = O(V E^2)$. This makes it a strongly polynomial time algorithm, completely independent of the maximum flow value or the edge capacities.
*   **Space Complexity:** $O(V^2)$ or $O(V + E)$
    *   Using an adjacency matrix representation (as in the code examples), the space complexity is $O(V^2)$.
    *   If implemented with an adjacency list, the space complexity would be $O(V + E)$, which is much more efficient for sparse graphs. The `parent` array and the BFS `queue` take $O(V)$ auxiliary space.

## 14. Advantages
*   **Guaranteed Termination:** Unlike the generic Ford-Fulkerson (which can loop infinitely on graphs with irrational capacities), Edmonds-Karp is guaranteed to terminate.
*   **Strongly Polynomial:** The runtime bounds rely purely on graph topology ($V$ and $E$), not the size of the numerical capacities.
*   **Easy Implementation:** Built on top of standard BFS, which is a very well-understood and easy-to-implement graph traversal algorithm.

## 15. Disadvantages
*   **Slower than advanced algorithms:** For very large or dense networks, an $O(V E^2)$ complexity might be too slow. Algorithms like Dinic's Algorithm ($O(V^2 E)$) or Push-Relabel algorithms offer better theoretical and practical bounds for dense graphs.
*   **Space intensive (Matrix):** Simple implementations using an adjacency matrix take $O(V^2)$ space, which is prohibitive for networks with millions of nodes but very few edges.

## 16. Applications
*   **Max Flow / Min Cut Theorem:** Edmonds-Karp is the standard way to prove and calculate the minimum cut of a graph (the smallest total capacity of edges that, if removed, would disconnect the source from the sink).
*   **Maximum Bipartite Matching:** Finding the maximum number of matches between two distinct sets (e.g., job applicants and companies). We create a supersource connecting to all applicants with capacity 1, and all companies connect to a supersink with capacity 1.
*   **Circulation with Demands:** Determining if a flow network can satisfy specific supply/demand requirements at various nodes.
*   **Image Processing:** Used in graph-cut optimization algorithms for tasks like foreground extraction, where pixels are nodes and edge capacities represent pixel similarities.

## 17. Common Mistakes
*   **Forgetting Reverse Edges:** The most common mistake in implementing any Ford-Fulkerson method is forgetting to add the flow to the reverse edge in the residual graph. This prevents the algorithm from "undoing" bad flow assignments.
*   **Using DFS instead of BFS:** If you use DFS, you are implementing generic Ford-Fulkerson, not Edmonds-Karp. You lose the strongly polynomial time guarantee and risk extreme inefficiency or non-termination.
*   **Incorrect Initialization:** Failing to initialize the residual graph correctly or not resetting the `parent` and `visited` arrays correctly before every BFS call.
*   **Infinite Loops:** If reverse edges are not properly maintained, the algorithm might repeatedly push flow back and forth in a loop.

## 18. Interview Questions
1.  **What is the core difference between Ford-Fulkerson and Edmonds-Karp?**
    *   Ford-Fulkerson is a method that uses any path-finding algorithm (often DFS). Edmonds-Karp specifically mandates using BFS to find the shortest augmenting path (in terms of number of edges).
2.  **Why does Edmonds-Karp guarantee termination while Ford-Fulkerson doesn't?**
    *   Ford-Fulkerson can get stuck on irrational capacities. Edmonds-Karp's reliance on the shortest path strictly bounds the number of augmentations to $O(V E)$, forcing termination.
3.  **Explain the significance of the residual graph.**
    *   It tracks remaining capacity on forward edges and allowable 'push-back' flow on reverse edges, enabling the algorithm to correct early, sub-optimal path choices.
4.  **What is the time complexity and how is it derived?**
    *   $O(V E^2)$. $O(V E)$ max augmentations $\times$ $O(E)$ time for BFS.
5.  **How would you find the Minimum Cut using this algorithm?**
    *   Run Edmonds-Karp to get max flow. Then perform a BFS/DFS from the source on the final residual graph (only using edges with capacity > 0). The reachable vertices form one set, unreachable form the other. The min-cut edges are those originating in the reachable set and terminating in the unreachable set in the *original* graph.
6.  **Can Edmonds-Karp handle multiple sources and sinks?**
    *   Yes, by creating a "supersource" connected to all actual sources with infinite capacity, and a "supersink" connected from all actual sinks with infinite capacity.
7.  **What happens if all edge capacities are multiplied by a constant $k$?**
    *   The maximum flow is also multiplied by $k$. The algorithm will find the exact same augmenting paths in the exact same order.
8.  **Is it better to use an Adjacency Matrix or Adjacency List for Edmonds-Karp?**
    *   Adjacency List is generally better, especially for sparse graphs, reducing space to $O(V+E)$. Matrix is $O(V^2)$ and iterating through it during BFS takes $O(V)$ per node, strictly enforcing $O(V^2)$ for the BFS phase.
9.  **Describe a scenario where Edmonds-Karp is too slow.**
    *   On a highly dense graph with large number of vertices, $O(V E^2)$ becomes roughly $O(V^5)$. Dinic's algorithm is preferred here.
10. **Explain how to model Bipartite Matching as a Max Flow problem.**
    *   Create a directed graph. Connect a supersource to all nodes in set U (capacity 1). Connect all valid edges from U to set V (capacity 1). Connect all nodes in V to a supersink (capacity 1). Max flow = Max matching.

## 19. Practice Problems
*   **Easy:**
    1.  Implement Edmonds-Karp on a small predefined matrix.
    2.  Given a max flow, calculate the bottleneck edge.
    3.  Count the number of augmenting paths the algorithm takes on a specific graph.
    4.  Modify the algorithm to print all augmenting paths taken.
*   **Medium:**
    5.  Solve the "Maximum Bipartite Matching" problem on LeetCode using Edmonds-Karp.
    6.  Find the Minimum Cut edges for a given flow network.
    7.  Model a network with multiple sources and sinks and solve it.
    8.  Optimize the space complexity by switching from Adjacency Matrix to Adjacency List.
*   **Hard:**
    9.  Solve a Circulation with Demands problem (nodes have explicit supply/demand values).
    10. Implement Image Segmentation (Foreground/Background separation) on a small grid of pixels.
    11. Compare execution times of Edmonds-Karp and Dinic's Algorithm on randomly generated large sparse and dense graphs.

## 20. Related Algorithms
*   **Ford-Fulkerson Method:** The foundational method. Uses DFS instead of BFS. Pseudo-polynomial time $O(E \cdot |f^*|)$.
*   **Dinic's Algorithm:** Uses level graphs and blocking flows to achieve a faster $O(V^2 E)$ time complexity. Often the practical choice for competitive programming.
*   **Push-Relabel Algorithm:** A fundamentally different approach that maintains a "preflow" and pushes excess flow locally, operating in $O(V^3)$ or $O(V^2 \sqrt{E})$ time.
*   **Karger's Algorithm:** A randomized algorithm for finding the global minimum cut in an undirected graph, related but distinct from the $s-t$ min cut found by max flow.

## 21. Summary
The Edmonds-Karp algorithm provides a robust, strongly polynomial solution to the maximum flow problem by instantiating the Ford-Fulkerson method with a Breadth-First Search. It guarantees that the shortest augmenting path is always chosen, capping the number of required iterations and ensuring termination. While its $O(V E^2)$ time complexity makes it slower than some advanced alternatives for dense graphs, its simplicity and reliability make it a cornerstone algorithm in graph theory, network routing, and combinatorial optimization.

## 22. Quiz
**Question 1:** What search strategy does Edmonds-Karp use to find augmenting paths?
*   A) Depth-First Search (DFS)
*   B) Breadth-First Search (BFS)
*   C) Dijkstra's Algorithm
*   D) A* Search
**Correct Answer:** B
**Explanation:** Edmonds-Karp specifically uses BFS to ensure the shortest augmenting path (fewest edges) is found, which guarantees the strongly polynomial time complexity.

**Question 2:** What is the time complexity of the Edmonds-Karp algorithm?
*   A) $O(V E)$
*   B) $O(E \cdot \text{max\_flow})$
*   C) $O(V E^2)$
*   D) $O(V^2 E)$
**Correct Answer:** C
**Explanation:** Finding a path takes $O(E)$ via BFS, and it is proven that at most $O(V E)$ augmenting paths will be found, resulting in $O(V E^2)$.

**Question 3:** What problem does the reverse edge in the residual graph solve?
*   A) It increases the total capacity of the network.
*   B) It allows the algorithm to "push back" or undo suboptimal flow choices.
*   C) It prevents the algorithm from entering an infinite loop.
*   D) It speeds up the BFS traversal.
**Correct Answer:** B
**Explanation:** The residual reverse edge represents the capacity to cancel out flow that was previously sent down that path, allowing the algorithm to dynamically correct mistakes and find the true global maximum.

**Question 4:** If a generic Ford-Fulkerson implementation uses DFS, what is its worst-case time complexity?
*   A) $O(V E^2)$
*   B) $O(E \cdot |f^*|)$ where $f^*$ is the max flow
*   C) $O(V^3)$
*   D) $O(V+E)$
**Correct Answer:** B
**Explanation:** DFS might pick a path that increases the flow by only 1 unit per iteration. In the worst case, it will take $|f^*|$ iterations, taking $O(E)$ time each.

**Question 5:** Which of the following is NOT a direct application of Maximum Flow algorithms?
*   A) Bipartite Matching
*   B) Shortest Path routing in a GPS
*   C) Finding the Minimum Cut of a network
*   D) Circulation with Demands
**Correct Answer:** B
**Explanation:** Shortest path routing is handled by algorithms like Dijkstra or Bellman-Ford. Max flow algorithms handle capacity and volume, not distance.

**Question 6:** How do you handle multiple sources and multiple sinks in Edmonds-Karp?
*   A) Run the algorithm for every pair of source and sink and sum the results.
*   B) It is impossible; Edmonds-Karp only works for single source/sink.
*   C) Create a supersource connected to all sources and a supersink connected to all sinks with infinite capacity.
*   D) Add the capacities of all sources together.
**Correct Answer:** C
**Explanation:** A standard reduction technique involves routing all flow from a single imaginary supersource into the actual sources, and collecting all flow from actual sinks into an imaginary supersink.

**Question 7:** What defines the bottleneck of an augmenting path?
*   A) The edge with the maximum capacity.
*   B) The edge with the minimum residual capacity on that path.
*   C) The sum of all capacities on the path.
*   D) The first edge on the path.
**Correct Answer:** B
**Explanation:** The maximum amount of flow you can push through a specific path is constrained by the narrowest "pipe", which is the edge with the lowest residual capacity.

**Question 8:** In a residual graph, if edge (U, V) has flow F and capacity C, what is the capacity of the reverse edge (V, U)?
*   A) C
*   B) C - F
*   C) F
*   D) 0
**Correct Answer:** C
**Explanation:** The reverse edge (V, U) in the residual graph has a capacity equal to the current flow F on (U, V), representing the ability to push back up to F units of flow.

**Question 9:** Why might Dinic's Algorithm be preferred over Edmonds-Karp?
*   A) It uses less memory.
*   B) It is easier to implement.
*   C) It has a better time complexity of $O(V^2 E)$, which is faster for dense graphs.
*   D) It doesn't require a residual graph.
**Correct Answer:** C
**Explanation:** For dense graphs where E is close to $V^2$, Edmonds-Karp is $O(V^5)$ while Dinic is $O(V^4)$. Dinic uses level graphs to find blocking flows more efficiently.

**Question 10:** According to the Max-Flow Min-Cut theorem, what is the relationship between the maximum flow and the minimum cut of a network?
*   A) Maximum Flow = Minimum Cut + 1
*   B) Maximum Flow is strictly less than Minimum Cut
*   C) Maximum Flow is equal to the capacity of the Minimum Cut
*   D) There is no guaranteed relationship
**Correct Answer:** C
**Explanation:** A fundamental theorem of network flows states that the maximum amount of flow passing from the source to the sink is exactly equal to the total capacity of the edges in a minimum cut.
