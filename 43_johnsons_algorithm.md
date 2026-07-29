# Johnson's All-Pairs Shortest Path Algorithm

## 1. Introduction
Johnson's algorithm is a fundamental algorithm in computer science and graph theory used to find the shortest paths between all pairs of vertices in an edge-weighted, directed graph. It is uniquely powerful because it can handle graphs with negative edge weights, provided there are no negative-weight cycles. It was named after Donald B. Johnson, who published the algorithm in 1977. Johnson's algorithm elegantly combines the strengths of two other classic algorithms: the Bellman-Ford algorithm and Dijkstra's algorithm.

## 2. Why Use This Algorithm?
In graph theory, calculating the shortest path between all pairs of nodes is a common requirement. While the Floyd-Warshall algorithm is a popular choice for all-pairs shortest paths, it runs in $O(V^3)$ time, where $V$ is the number of vertices. This makes it inefficient for sparse graphs, where the number of edges $E$ is much less than $V^2$. 

Dijkstra's algorithm is much faster but cannot handle negative edge weights. Johnson's algorithm bridges this gap. By cleverly "reweighting" the edges of the graph to be non-negative, it allows the use of Dijkstra's algorithm. Consequently, Johnson's algorithm achieves a time complexity of $O(V^2 \log V + VE)$, which is significantly faster than Floyd-Warshall for sparse graphs with negative weights.

## 3. Real-World Applications
Johnson's algorithm is widely used in scenarios where the graph is sparse and negative weights exist. Some real-world applications include:
*   **Network Routing:** In communication networks where certain links might have "negative" costs (representing gains, bonuses, or subsidized routes), calculating the most efficient paths between all routers is critical.
*   **Financial Arbitrage:** Currency exchange markets can be modeled as graphs where nodes are currencies and edges are exchange rates. Finding a sequence of trades that yields a profit can sometimes be framed as finding negative cycles or shortest paths, and Johnson's algorithm can be adapted for generalized pathfinding in these systems.
*   **Transportation Logistics:** Optimizing delivery routes across a vast network of cities where some segments might offer credits (e.g., picking up backhaul cargo) leading to effective negative costs.
*   **Circuit Design:** In VLSI design, analyzing timing constraints and finding optimal signal paths through complex, sparse logic circuits.

## 4. Prerequisites
To fully grasp Johnson's algorithm, a solid understanding of the following concepts is required:
*   **Graph Theory Fundamentals:** Vertices, edges, directed vs. undirected graphs, weights.
*   **Bellman-Ford Algorithm:** A single-source shortest path algorithm that can handle negative weights and detect negative weight cycles.
*   **Dijkstra's Algorithm:** A highly efficient single-source shortest path algorithm that requires non-negative edge weights.
*   **Priority Queues / Min-Heaps:** Essential data structures for optimizing Dijkstra's algorithm.
*   **Graph Representations:** Adjacency list and adjacency matrix representations of graphs.

## 5. Visualization
Imagine a map of cities (vertices) connected by roads (edges). Some roads have tolls (positive weights), and some roads offer fuel subsidies (negative weights).
1.  **Add a Source:** We add a temporary "magic city" (vertex $q$) that has zero-cost teleportation roads to all other cities.
2.  **Bellman-Ford:** We calculate the cheapest way to get from the magic city to all other cities. These costs become "heights" for each city.
3.  **Reweighting:** We adjust the toll of every road based on the difference in "heights" of the cities it connects. A road going "downhill" becomes cheaper, and a road going "uphill" becomes more expensive. This magic adjustment ensures all roads now have non-negative tolls!
4.  **Dijkstra:** Now that all tolls are zero or positive, we can safely run standard GPS routing (Dijkstra's algorithm) starting from every single city to find the shortest paths everywhere.
5.  **Reverse Adjustment:** Finally, we adjust the calculated costs back to reality by reversing the height adjustments to get the true minimum costs.

## 6. How It Works
The core brilliance of Johnson's algorithm lies in a technique called **reweighting**. If all edge weights in a graph were non-negative, we could simply run Dijkstra's algorithm from every vertex, taking $O(V \cdot (V \log V + E))$. The problem is negative edges. 

We can't just add a large constant to all edges, as that changes the shortest paths (paths with more edges get penalized more). Instead, Johnson's algorithm uses a vertex potential function $h(v)$.
For every edge $(u, v)$ with weight $w(u, v)$, we create a new weight:
$$w'(u, v) = w(u, v) + h(u) - h(v)$$

If we can find a function $h$ such that $w'(u, v) \ge 0$ for all edges, we can use Dijkstra. The Bellman-Ford algorithm perfectly provides this function! 
By adding a new vertex $q$ with 0-weight edges to all other vertices, the shortest path distance from $q$ to any vertex $v$, calculated by Bellman-Ford, serves as a valid $h(v)$. 
Telescoping property of this reweighting ensures that the shortest path in the reweighted graph is the exact same path as in the original graph.

## 7. Step-by-Step Algorithm
1.  **Create a New Graph $G'$:** Given the original graph $G = (V, E)$, add a new vertex $q$ to $V$.
2.  **Add Edges:** Add directed edges from $q$ to every vertex $v$ in $V$ with a weight of 0: $w(q, v) = 0$.
3.  **Run Bellman-Ford:** Run the Bellman-Ford algorithm on $G'$ starting from the source vertex $q$.
    *   If Bellman-Ford detects a negative-weight cycle, halt the algorithm and report that the graph contains a negative-weight cycle. (Shortest paths are undefined).
    *   Otherwise, let the shortest path distance from $q$ to $v$ be $h(v)$ for all $v \in V$.
4.  **Reweight the Edges:** For every edge $(u, v)$ in the original graph $G$, compute a new non-negative weight:
    $$w'(u, v) = w(u, v) + h(u) - h(v)$$
5.  **Run Dijkstra's Algorithm:** Remove vertex $q$. For every vertex $u \in V$, run Dijkstra's algorithm on the original graph $G$ using the new weights $w'$ to find the shortest path distances $d'(u, v)$ to all other vertices $v$.
6.  **Calculate Final Distances:** For every pair of vertices $(u, v)$, the true shortest path distance in the original graph is:
    $$d(u, v) = d'(u, v) + h(v) - h(u)$$

## 8. Pseudocode
```text
Algorithm Johnson(Graph G)
    Let V be the vertices and E be the edges of G.
    Create a new vertex q.
    Add q to V.
    For each vertex v in V (excluding q):
        Add an edge (q, v) with weight 0 to E.

    If Bellman-Ford(G, q) returns FALSE (negative cycle detected):
        print "The input graph contains a negative-weight cycle"
        return NULL
    Else:
        Let h(v) be the distance from q to v computed by Bellman-Ford.

    For each edge (u, v) in E:
        w'(u, v) = w(u, v) + h(u) - h(v)

    Let D be a |V| x |V| matrix to store shortest path distances.
    For each vertex u in V (excluding q):
        Run Dijkstra(G, u, w') to compute d'(u, v) for all v in V.
        For each vertex v in V (excluding q):
            D[u][v] = d'(u, v) + h(v) - h(u)

    return D
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>
#include <limits.h>
#include <stdbool.h>

#define INF 999999

struct Edge {
    int src, dest, weight;
};

struct Graph {
    int V, E;
    struct Edge* edge;
};

struct Graph* createGraph(int V, int E) {
    struct Graph* graph = (struct Graph*) malloc(sizeof(struct Graph));
    graph->V = V;
    graph->E = E;
    graph->edge = (struct Edge*) malloc(E * sizeof(struct Edge));
    return graph;
}

bool bellmanFord(struct Graph* graph, int src, int* h) {
    int V = graph->V;
    int E = graph->E;
    for (int i = 0; i < V; i++) h[i] = INF;
    h[src] = 0;

    for (int i = 1; i <= V - 1; i++) {
        for (int j = 0; j < E; j++) {
            int u = graph->edge[j].src;
            int v = graph->edge[j].dest;
            int weight = graph->edge[j].weight;
            if (h[u] != INF && h[u] + weight < h[v])
                h[v] = h[u] + weight;
        }
    }

    for (int i = 0; i < E; i++) {
        int u = graph->edge[i].src;
        int v = graph->edge[i].dest;
        int weight = graph->edge[i].weight;
        if (h[u] != INF && h[u] + weight < h[v]) {
            return false;
        }
    }
    return true;
}

int minDistance(int dist[], bool sptSet[], int V) {
    int min = INF, min_index;
    for (int v = 0; v < V; v++)
        if (sptSet[v] == false && dist[v] <= min)
            min = dist[v], min_index = v;
    return min_index;
}

void dijkstra(struct Graph* graph, int src, int* dist, int V_orig) {
    bool sptSet[V_orig];
    for (int i = 0; i < V_orig; i++) {
        dist[i] = INF;
        sptSet[i] = false;
    }
    dist[src] = 0;

    int adj[V_orig][V_orig];
    for (int i = 0; i < V_orig; i++)
        for (int j = 0; j < V_orig; j++)
            adj[i][j] = INF;

    for (int i = 0; i < graph->E; i++) {
        if(graph->edge[i].src < V_orig && graph->edge[i].dest < V_orig)
            adj[graph->edge[i].src][graph->edge[i].dest] = graph->edge[i].weight;
    }

    for (int count = 0; count < V_orig - 1; count++) {
        int u = minDistance(dist, sptSet, V_orig);
        if(u == INF) break;
        sptSet[u] = true;
        for (int v = 0; v < V_orig; v++)
            if (!sptSet[v] && adj[u][v] != INF && dist[u] != INF && dist[u] + adj[u][v] < dist[v])
                dist[v] = dist[u] + adj[u][v];
    }
}

void johnsonsAlgorithm(struct Graph* graph) {
    int V_orig = graph->V;
    int E_orig = graph->E;
    
    struct Graph* modGraph = createGraph(V_orig + 1, E_orig + V_orig);
    for(int i = 0; i < E_orig; i++) {
        modGraph->edge[i] = graph->edge[i];
    }
    for(int i = 0; i < V_orig; i++) {
        modGraph->edge[E_orig + i].src = V_orig;
        modGraph->edge[E_orig + i].dest = i;
        modGraph->edge[E_orig + i].weight = 0;
    }

    int* h = (int*) malloc((V_orig + 1) * sizeof(int));
    if (!bellmanFord(modGraph, V_orig, h)) {
        printf("Graph contains negative weight cycle\n");
        free(h);
        free(modGraph->edge);
        free(modGraph);
        return;
    }

    for (int i = 0; i < modGraph->E; i++) {
        int u = modGraph->edge[i].src;
        int v = modGraph->edge[i].dest;
        if(u < V_orig && v < V_orig) {
             graph->edge[i].weight = graph->edge[i].weight + h[u] - h[v];
        }
    }

    printf("All-Pairs Shortest Path Matrix:\n");
    for (int u = 0; u < V_orig; u++) {
        int dist[V_orig];
        dijkstra(graph, u, dist, V_orig);
        for (int v = 0; v < V_orig; v++) {
            if (dist[v] == INF) {
                printf("INF\t");
            } else {
                printf("%d\t", dist[v] + h[v] - h[u]);
            }
        }
        printf("\n");
    }
    free(h);
    free(modGraph->edge);
    free(modGraph);
}

int main() {
    int V = 4;
    int E = 5;
    struct Graph* graph = createGraph(V, E);

    graph->edge[0] = (struct Edge){0, 1, -5};
    graph->edge[1] = (struct Edge){0, 2, 2};
    graph->edge[2] = (struct Edge){1, 2, 4};
    graph->edge[3] = (struct Edge){2, 3, 1};
    graph->edge[4] = (struct Edge){3, 0, 3};

    johnsonsAlgorithm(graph);
    free(graph->edge);
    free(graph);
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

const int INF = 1e9;

struct Edge {
    int dest, weight;
};

void johnsonsAlgorithm(int V, vector<vector<Edge>>& adj) {
    int new_V = V + 1;
    vector<vector<Edge>> mod_adj = adj;
    mod_adj.push_back(vector<Edge>());
    
    for (int i = 0; i < V; i++) {
        mod_adj[V].push_back({i, 0});
    }

    vector<int> h(new_V, INF);
    h[V] = 0;

    for (int i = 0; i < new_V - 1; i++) {
        for (int u = 0; u < new_V; u++) {
            for (auto& edge : mod_adj[u]) {
                int v = edge.dest;
                int weight = edge.weight;
                if (h[u] != INF && h[u] + weight < h[v]) {
                    h[v] = h[u] + weight;
                }
            }
        }
    }

    for (int u = 0; u < new_V; u++) {
        for (auto& edge : mod_adj[u]) {
            int v = edge.dest;
            int weight = edge.weight;
            if (h[u] != INF && h[u] + weight < h[v]) {
                cout << "Graph contains a negative-weight cycle" << endl;
                return;
            }
        }
    }

    for (int u = 0; u < V; u++) {
        for (auto& edge : adj[u]) {
            edge.weight = edge.weight + h[u] - h[edge.dest];
        }
    }

    cout << "All-Pairs Shortest Path Matrix:\n";
    for (int src = 0; src < V; src++) {
        vector<int> dist(V, INF);
        priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> pq;

        dist[src] = 0;
        pq.push({0, src});

        while (!pq.empty()) {
            int u = pq.top().second;
            int d = pq.top().first;
            pq.pop();

            if (d > dist[u]) continue;

            for (auto& edge : adj[u]) {
                int v = edge.dest;
                int weight = edge.weight;
                if (dist[u] != INF && dist[u] + weight < dist[v]) {
                    dist[v] = dist[u] + weight;
                    pq.push({dist[v], v});
                }
            }
        }

        for (int dest = 0; dest < V; dest++) {
            if (dist[dest] == INF) {
                cout << "INF\t";
            } else {
                cout << dist[dest] + h[dest] - h[src] << "\t";
            }
        }
        cout << endl;
    }
}

int main() {
    int V = 4;
    vector<vector<Edge>> adj(V);
    adj[0].push_back({1, -5});
    adj[0].push_back({2, 2});
    adj[1].push_back({2, 4});
    adj[2].push_back({3, 1});
    adj[3].push_back({0, 3});

    johnsonsAlgorithm(V, adj);

    return 0;
}
```

### Java
```java
import java.util.*;

class Edge {
    int dest, weight;
    Edge(int dest, int weight) {
        this.dest = dest;
        this.weight = weight;
    }
}

public class JohnsonsAlgorithm {
    static final int INF = (int) 1e9;

    public static void johnsonsAlgorithm(int V, List<List<Edge>> adj) {
        int new_V = V + 1;
        List<List<Edge>> mod_adj = new ArrayList<>();
        for (int i = 0; i < new_V; i++) mod_adj.add(new ArrayList<>());

        for (int i = 0; i < V; i++) {
            for (Edge e : adj.get(i)) {
                mod_adj.get(i).add(new Edge(e.dest, e.weight));
            }
        }

        for (int i = 0; i < V; i++) {
            mod_adj.get(V).add(new Edge(i, 0));
        }

        int[] h = new int[new_V];
        Arrays.fill(h, INF);
        h[V] = 0;

        for (int i = 0; i < new_V - 1; i++) {
            for (int u = 0; u < new_V; u++) {
                for (Edge edge : mod_adj.get(u)) {
                    int v = edge.dest;
                    int weight = edge.weight;
                    if (h[u] != INF && h[u] + weight < h[v]) {
                        h[v] = h[u] + weight;
                    }
                }
            }
        }

        for (int u = 0; u < new_V; u++) {
            for (Edge edge : mod_adj.get(u)) {
                int v = edge.dest;
                int weight = edge.weight;
                if (h[u] != INF && h[u] + weight < h[v]) {
                    System.out.println("Graph contains a negative-weight cycle");
                    return;
                }
            }
        }

        for (int u = 0; u < V; u++) {
            for (Edge edge : adj.get(u)) {
                edge.weight = edge.weight + h[u] - h[edge.dest];
            }
        }

        System.out.println("All-Pairs Shortest Path Matrix:");
        for (int src = 0; src < V; src++) {
            int[] dist = new int[V];
            Arrays.fill(dist, INF);
            PriorityQueue<int[]> pq = new PriorityQueue<>(Comparator.comparingInt(a -> a[0]));

            dist[src] = 0;
            pq.add(new int[]{0, src});

            while (!pq.isEmpty()) {
                int[] curr = pq.poll();
                int u = curr[1];
                int d = curr[0];

                if (d > dist[u]) continue;

                for (Edge edge : adj.get(u)) {
                    int v = edge.dest;
                    int weight = edge.weight;
                    if (dist[u] != INF && dist[u] + weight < dist[v]) {
                        dist[v] = dist[u] + weight;
                        pq.add(new int[]{dist[v], v});
                    }
                }
            }

            for (int dest = 0; dest < V; dest++) {
                if (dist[dest] == INF) {
                    System.out.print("INF\t");
                } else {
                    System.out.print((dist[dest] + h[dest] - h[src]) + "\t");
                }
            }
            System.out.println();
        }
    }

    public static void main(String[] args) {
        int V = 4;
        List<List<Edge>> adj = new ArrayList<>();
        for (int i = 0; i < V; i++) adj.add(new ArrayList<>());

        adj.get(0).add(new Edge(1, -5));
        adj.get(0).add(new Edge(2, 2));
        adj.get(1).add(new Edge(2, 4));
        adj.get(2).add(new Edge(3, 1));
        adj.get(3).add(new Edge(0, 3));

        johnsonsAlgorithm(V, adj);
    }
}
```

### Python
```python
import heapq

INF = float('inf')

def johnsons_algorithm(V, edges):
    # edges is a list of tuples: (u, v, weight)
    # Add new vertex V connected to all other vertices with weight 0
    mod_edges = edges.copy()
    for i in range(V):
        mod_edges.append((V, i, 0))

    # Bellman-Ford
    h = [INF] * (V + 1)
    h[V] = 0

    for _ in range(V):
        for u, v, weight in mod_edges:
            if h[u] != INF and h[u] + weight < h[v]:
                h[v] = h[u] + weight

    # Check for negative cycle
    for u, v, weight in mod_edges:
        if h[u] != INF and h[u] + weight < h[v]:
            print("Graph contains a negative-weight cycle")
            return

    # Reweight original edges
    adj = {i: [] for i in range(V)}
    for u, v, weight in edges:
        new_weight = weight + h[u] - h[v]
        adj[u].append((v, new_weight))

    print("All-Pairs Shortest Path Matrix:")
    for src in range(V):
        # Dijkstra
        dist = [INF] * V
        dist[src] = 0
        pq = [(0, src)]

        while pq:
            d, u = heapq.heappop(pq)
            if d > dist[u]:
                continue
            for v, weight in adj[u]:
                if dist[u] + weight < dist[v]:
                    dist[v] = dist[u] + weight
                    heapq.heappush(pq, (dist[v], v))

        # Adjust distances back
        res = []
        for dest in range(V):
            if dist[dest] == INF:
                res.append("INF")
            else:
                res.append(str(dist[dest] + h[dest] - h[src]))
        print("\t".join(res))

if __name__ == "__main__":
    V = 4
    edges = [
        (0, 1, -5),
        (0, 2, 2),
        (1, 2, 4),
        (2, 3, 1),
        (3, 0, 3)
    ]
    johnsons_algorithm(V, edges)
```

### JavaScript
```javascript
const INF = Infinity;

class PriorityQueue {
    constructor() { this.values = []; }
    enqueue(val, priority) {
        this.values.push({val, priority});
        this.sort();
    }
    dequeue() { return this.values.shift(); }
    sort() { this.values.sort((a, b) => a.priority - b.priority); }
    isEmpty() { return this.values.length === 0; }
}

function johnsonsAlgorithm(V, edges) {
    let modEdges = [...edges];
    for(let i = 0; i < V; i++) {
        modEdges.push({u: V, v: i, weight: 0});
    }

    let h = new Array(V + 1).fill(INF);
    h[V] = 0;

    for(let i = 0; i < V; i++) {
        for(let edge of modEdges) {
            if(h[edge.u] !== INF && h[edge.u] + edge.weight < h[edge.v]) {
                h[edge.v] = h[edge.u] + edge.weight;
            }
        }
    }

    for(let edge of modEdges) {
        if(h[edge.u] !== INF && h[edge.u] + edge.weight < h[edge.v]) {
            console.log("Graph contains a negative-weight cycle");
            return;
        }
    }

    let adj = Array.from({length: V}, () => []);
    for(let edge of edges) {
        let newWeight = edge.weight + h[edge.u] - h[edge.v];
        adj[edge.u].push({v: edge.v, weight: newWeight});
    }

    console.log("All-Pairs Shortest Path Matrix:");
    for(let src = 0; src < V; src++) {
        let dist = new Array(V).fill(INF);
        dist[src] = 0;
        let pq = new PriorityQueue();
        pq.enqueue(src, 0);

        while(!pq.isEmpty()) {
            let {val: u, priority: d} = pq.dequeue();
            if(d > dist[u]) continue;

            for(let edge of adj[u]) {
                if(dist[u] + edge.weight < dist[edge.v]) {
                    dist[edge.v] = dist[u] + edge.weight;
                    pq.enqueue(edge.v, dist[edge.v]);
                }
            }
        }

        let res = [];
        for(let dest = 0; dest < V; dest++) {
            if(dist[dest] === INF) res.push("INF");
            else res.push(dist[dest] + h[dest] - h[src]);
        }
        console.log(res.join("\t"));
    }
}

let V = 4;
let edges = [
    {u: 0, v: 1, weight: -5},
    {u: 0, v: 2, weight: 2},
    {u: 1, v: 2, weight: 4},
    {u: 2, v: 3, weight: 1},
    {u: 3, v: 0, weight: 3}
];

johnsonsAlgorithm(V, edges);
```

## 10. Code Explanation
1.  **Graph Modification:** We take the input list of edges or adjacency list and augment it by adding an extra imaginary node `V` (since nodes are `0` to `V-1`). We add edges from `V` to all other nodes with weight `0`.
2.  **Bellman-Ford Execution:** We run the Bellman-Ford algorithm from node `V`. The array `h` stores the shortest distance from `V` to every node. If a relaxation occurs on the `V`th iteration, we flag a negative cycle and terminate.
3.  **Reweighting:** For each edge `(u, v)` with original weight `w`, we update its weight to `w + h[u] - h[v]`. Due to the triangle inequality property verified by Bellman-Ford, all these new weights are guaranteed to be non-negative.
4.  **Dijkstra Execution:** With non-negative edges, we loop through each original node `src` and run Dijkstra's algorithm to find distances to all other nodes.
5.  **Distance Readjustment:** The calculated shortest path from `u` to `v` uses reweighted edges. To get the true original distance, we reverse the mathematical transformation: `true_dist = reweighted_dist + h[v] - h[u]`.

## 11. Interactive Demo
*(Imagine an interactive web component here)*
Users could draw nodes on a canvas, add directed edges, and assign positive or negative weights. 
Clicking a "Run" button would animate:
1.  A star-shaped node dropping in, sending out 0-weight ripples.
2.  Nodes updating their $h$ values (changing colors based on value).
3.  Edges flashing as their weights are visually updated to $\ge 0$.
4.  Dijkstra's wavefront expanding from a selected source node, highlighting the shortest path tree.

## 12. Dry Run
**Input Graph:**
Vertices: 0, 1, 2
Edges: (0->1, weight -2), (1->2, weight 1), (0->2, weight 4)

**Step 1: Add Node 3**
Edges from 3 to 0, 1, 2 with weight 0.

**Step 2: Bellman-Ford from Node 3**
Init: `h = [INF, INF, INF, 0]`
Iter 1: 
`h[0] = 0` (via 3->0)
`h[1] = 0` (via 3->1)
`h[2] = 0` (via 3->2)
Iter 2:
`h[1] = min(0, h[0] + (-2)) = -2`
`h[2] = min(0, h[1] + 1) = -1`
Final `h`: `[0, -2, -1]`

**Step 3: Reweight Edges**
w'(0, 1) = -2 + h[0] - h[1] = -2 + 0 - (-2) = 0
w'(1, 2) = 1 + h[1] - h[2] = 1 + (-2) - (-1) = 0
w'(0, 2) = 4 + h[0] - h[2] = 4 + 0 - (-1) = 5

**Step 4: Dijkstra (e.g., from Node 0)**
Run Dijkstra with new weights:
Path 0->1 cost = 0
Path 0->1->2 cost = 0 + 0 = 0. Path 0->2 directly is 5. Min is 0.
`dist' = [0, 0, 0]`

**Step 5: Calculate Original Distances**
`dist(0, 1) = dist'(0, 1) + h[1] - h[0] = 0 + (-2) - 0 = -2`
`dist(0, 2) = dist'(0, 2) + h[2] - h[0] = 0 + (-1) - 0 = -1`
(Path 0->1->2 is -2 + 1 = -1, which matches).

## 13. Time & Space Complexity
*   **Time Complexity:** 
    *   Adding the extra node and edges takes $O(V)$.
    *   Running Bellman-Ford takes $O(V \cdot E)$.
    *   Reweighting edges takes $O(E)$.
    *   Running Dijkstra's algorithm $V$ times (using an adjacency list and a Fibonacci heap or Min-Heap) takes $O(V \cdot (V \log V + E))$.
    *   **Total Time Complexity:** $O(V^2 \log V + VE)$. 
*   **Space Complexity:** 
    *   Storing the graph and modified edges takes $O(V + E)$.
    *   Storing the distance matrix takes $O(V^2)$.
    *   The `h` array takes $O(V)$ space.
    *   **Total Space Complexity:** $O(V^2)$ to store the output, or $O(V + E)$ auxiliary space.

## 14. Advantages
1.  **Handles Negative Weights:** Unlike running Dijkstra $V$ times naively, it handles graphs with negative edge weights gracefully.
2.  **Efficient for Sparse Graphs:** For sparse graphs where $E \ll V^2$, Johnson's time complexity $O(V^2 \log V + VE)$ is significantly faster than Floyd-Warshall's $O(V^3)$.
3.  **Detects Negative Cycles:** Safely aborts and reports if the graph contains negative weight cycles, avoiding infinite loops.

## 15. Disadvantages
1.  **Complexity of Implementation:** Requires implementing two distinct shortest-path algorithms (Bellman-Ford and Dijkstra), making the codebase larger and more prone to bugs compared to the 4-line Floyd-Warshall algorithm.
2.  **Slow for Dense Graphs:** If the graph is very dense ($E \approx V^2$), the time complexity becomes $O(V^3 \log V)$, which is slower than Floyd-Warshall's $O(V^3)$.
3.  **Memory Overhead:** Requires constructing a temporary graph with an extra node and edges, allocating priority queues repeatedly, and managing multiple arrays.

## 16. Applications
*   Solving the All-Pairs Shortest Path problem in large, sparse networks where some connection costs might be negative.
*   Pre-computing routing tables for Autonomous Systems in the Internet where local policies might create virtual negative costs.
*   Operations research problems involving resource allocation networks.

## 17. Common Mistakes
*   **Forgetting to check for negative cycles:** Failing to verify the Bellman-Ford output can lead to incorrect Dijkstra results or infinite loops if not handled.
*   **Incorrect Reweighting Formula:** Mixing up the signs. It must be $w'(u,v) = w(u,v) + h(u) - h(v)$.
*   **Incorrect Readjustment Formula:** When calculating the final answer, it must be $d(u,v) = d'(u,v) + h(v) - h(u)$. It's the inverse of the reweighting step.
*   **Using Adjacency Matrix for Dijkstra:** Using an $O(V^2)$ Dijkstra implementation negates the efficiency of Johnson's algorithm on sparse graphs. You must use a priority queue and adjacency list.

## 18. Interview Questions
1.  Explain why we can't just add the minimum negative weight to all edges to make them positive.
2.  How does Johnson's Algorithm detect a negative cycle?
3.  Why do we need to add a fake node $q$ before running Bellman-Ford?
4.  Prove that the reweighting in Johnson's Algorithm does not change the shortest paths.
5.  What is the time complexity of Johnson's Algorithm? Compare it with Floyd-Warshall.
6.  When would you prefer Floyd-Warshall over Johnson's algorithm?
7.  Can Johnson's algorithm be used on undirected graphs with negative edges? (Hint: No, why?)
8.  Explain the concept of "telescoping sum" in the context of Johnson's reweighting.
9.  If all edge weights are already positive, does Johnson's algorithm still work? Is it optimal?
10. What data structure is crucial for achieving the $O(V^2 \log V + VE)$ bound?

## 19. Practice Problems
**Easy**
1.  Implement a function to just do the graph reweighting step given `h` values.
2.  Modify Bellman-Ford to return both the `h` array and a boolean indicating success.
3.  Write a script to generate random sparse graphs with a mix of positive and negative weights.
4.  Run a dry trace on a 3-node graph with all positive weights to see that `h` values become 0.

**Medium**
5.  Implement the full Johnson's algorithm in your language of choice.
6.  Given a routing network where some links offer bandwidth bonuses (negative costs), find the optimal routes between all server pairs.
7.  Compare the execution time of your Johnson's implementation vs. Floyd-Warshall on randomly generated graphs of size V=100, E=200.
8.  Modify the output to also return the actual paths, not just the distances.

**Hard**
9.  Adapt Johnson's algorithm to work on a graph where the graph is dynamically changing (edges added/removed).
10. Implement Johnson's using a Fibonacci Heap for Dijkstra and benchmark against a standard binary heap.
11. Design a system that detects arbitrage opportunities in currency exchange rates using the reweighting concepts of Johnson's algorithm.

## 20. Related Algorithms
*   **Dijkstra's Algorithm:** Single-source shortest path, requires non-negative weights.
*   **Bellman-Ford Algorithm:** Single-source shortest path, handles negative weights.
*   **Floyd-Warshall Algorithm:** All-pairs shortest path, DP approach, better for dense graphs.
*   **A* Search Algorithm:** Pathfinding with heuristics, mainly for point-to-point.

## 21. Summary
Johnson's algorithm is a masterclass in algorithm reduction. By using Bellman-Ford to compute a clever potential function, it transforms a graph with negative weights into one with non-negative weights without altering the shortest paths. This allows the highly efficient Dijkstra's algorithm to do the heavy lifting for the all-pairs computation, making Johnson's the go-to choice for sparse graphs containing negative edge weights.

## 22. Quiz

**Question 1:** What is the primary purpose of adding a new vertex $q$ in Johnson's algorithm?
A) To increase the graph size for better hashing.
B) To act as a universal source node to compute vertex potentials via Bellman-Ford.
C) To ensure the graph is strongly connected.
D) To absorb negative weight cycles.
**Correct Answer:** B
**Explanation:** Vertex $q$ is connected to all other nodes with 0-weight edges so Bellman-Ford can reach every node and calculate the $h(v)$ potentials needed for reweighting.

**Question 2:** Which algorithm is NOT used as a subroutine in Johnson's Algorithm?
A) Bellman-Ford
B) Dijkstra
C) Floyd-Warshall
D) None of the above
**Correct Answer:** C
**Explanation:** Johnson's algorithm relies on Bellman-Ford for reweighting and Dijkstra for pathfinding. Floyd-Warshall is a competing all-pairs algorithm.

**Question 3:** What is the time complexity of Johnson's algorithm using a Min-Heap for sparse graphs?
A) $O(V^3)$
B) $O(V E \log V)$
C) $O(V^2 \log V + V E)$
D) $O(V + E)$
**Correct Answer:** C
**Explanation:** Bellman-Ford takes $O(VE)$. Dijkstra takes $O(V \log V + E)$ and is run $V$ times, leading to $O(V^2 \log V + VE)$.

**Question 4:** If a graph has undirected negative edges, can we use Johnson's algorithm?
A) Yes, directly.
B) Yes, by replacing each undirected edge with two directed edges.
C) No, because an undirected negative edge immediately creates a negative-weight cycle.
D) No, because Bellman-Ford only works on directed graphs.
**Correct Answer:** C
**Explanation:** An undirected negative edge between A and B means you can go A->B->A for a negative cost, which is a negative cycle.

**Question 5:** How is the new weight $w'(u,v)$ calculated?
A) $w(u,v) + h(v) - h(u)$
B) $w(u,v) + h(u) - h(v)$
C) $w(u,v) + h(u) + h(v)$
D) $|w(u,v)|$
**Correct Answer:** B
**Explanation:** The formula is $w'(u,v) = w(u,v) + h(u) - h(v)$ to ensure the triangle inequality holds and new weights are non-negative.

**Question 6:** Why is Johnson's algorithm faster than Floyd-Warshall for sparse graphs?
A) Because Dijkstra is faster than DP.
B) Because it uses fewer memory allocations.
C) Floyd-Warshall is strictly $O(V^3)$, while Johnson's scales with $E$. If $E$ is small, $VE$ is much less than $V^3$.
D) It is not faster.
**Correct Answer:** C
**Explanation:** In sparse graphs, $E \ll V^2$, making $O(V^2 \log V + VE)$ dominate the $O(V^3)$ complexity of Floyd-Warshall.

**Question 7:** What happens if Bellman-Ford detects a negative cycle during Johnson's algorithm?
A) The algorithm ignores it and continues.
B) The algorithm halts and reports that shortest paths cannot be found.
C) It converts the cycle into positive weights.
D) It runs Floyd-Warshall instead.
**Correct Answer:** B
**Explanation:** If a negative cycle exists, shortest paths are undefined (you can infinitely loop to decrease cost). The algorithm must abort.

**Question 8:** To recover the original shortest path distance $d(u,v)$ from the reweighted distance $d'(u,v)$, which formula is used?
A) $d'(u,v) - h(u) + h(v)$
B) $d'(u,v) + h(u) - h(v)$
C) $d'(u,v) + h(v) - h(u)$
D) $d'(u,v)$
**Correct Answer:** C
**Explanation:** It reverses the reweighting process over the path. Telescoping leaves only the start and end potentials: $d(u,v) = d'(u,v) + h(v) - h(u)$.

**Question 9:** Does Johnson's algorithm work if all edge weights are already positive?
A) Yes, but it will be slower than just running Dijkstra $V$ times.
B) No, it will fail during the Bellman-Ford step.
C) Yes, and it becomes faster than Dijkstra.
D) Yes, the $h(v)$ values will simply all be 0.
**Correct Answer:** D
**Explanation:** Since $q$ connects to all nodes with weight 0, and all other edges are positive, the shortest path from $q$ to any node is just 0. Thus $h(v) = 0$ for all $v$.

**Question 10:** What property of the reweighting formula ensures the shortest path remains the same?
A) Commutativity
B) Telescoping sum
C) Transitivity
D) Associativity
**Correct Answer:** B
**Explanation:** When summing the reweighted edges along a path, the intermediate $h(x)$ terms cancel each other out (telescoping), leaving only the $h$ values of the source and destination.
