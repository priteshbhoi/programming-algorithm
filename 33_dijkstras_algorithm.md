# Dijkstra's Algorithm

## 1. Introduction

Dijkstra's Algorithm is a greedy single-source shortest path algorithm designed by Dutch computer scientist Edsger W. Dijkstra in 1956 and published in 1959. It finds the shortest path from a starting source node to all other nodes in a weighted directed or undirected graph with **non-negative edge weights**.

Imagine navigating a road map where every highway segment has a toll or distance cost. Starting from your home city, Dijkstra's algorithm systematically expands outward, always selecting the unvisited intersection closest to home, updating tentative distance estimates to neighboring towns until all shortest travel routes are finalized.

Dijkstra conceived the algorithm in 20 minutes while drinking coffee on a shopping trip with his fiancée, designing it to demonstrate the power of the ARMAC computer.

You should use Dijkstra's algorithm whenever you need to compute shortest paths on graphs with non-negative edge weights (such as road maps, network routing tables, or game tile grids).

## 2. Why Use This Algorithm?

Dijkstra's algorithm guarantees the exact shortest distance to all vertices in non-negative weighted graphs.

**Benefits:**
- **Exact Shortest Path Guarantee:** Guaranteed optimal shortest paths for non-negative weighted graphs.
- **Single-Source All-Destinations:** Computes distances to all reachable nodes from a single source in one execution.
- **Efficient with Priority Queue:** Min-Heap implementation runs in $\mathcal{O}((V + E) \log V)$ time.
- **Monotonic Distance Growth:** Nodes are settled in strictly non-decreasing order of distance.

**Performance:**
- **Time Complexity:** $\mathcal{O}((V + E) \log V)$ using Min-Priority Queue / Min-Heap (or $\mathcal{O}(E + V \log V)$ with Fibonacci Heap).
- **Space Complexity:** $\mathcal{O}(V + E)$ for graph representation, distance array, and priority queue.

**When it is better than other algorithms:**
Dijkstra's is much faster than Bellman-Ford ($\mathcal{O}(V \cdot E)$) on graphs without negative edge weights, and more efficient than Floyd-Warshall ($\mathcal{O}(V^3)$) for single-source queries.

## 3. Real-World Applications

- **GPS Navigation & Mapping:** Google Maps, Apple Maps, and Garmin computing driving routes.
- **Network Routing Protocols:** OSPF (Open Shortest Path First) and IS-IS link-state routing protocols.
- **Robotic Motion Planning:** Finding collision-free paths in weighted configuration grids.
- **Game AI Pathfinding:** Unit movement pathfinding across terrain grids with varying movement costs (e.g., mud vs road).

## 4. Prerequisites

Before learning Dijkstra's algorithm, you should understand:
- Weighted graph representations (Adjacency List storing `(neighbor, weight)` pairs).
- Min-Priority Queue / Min-Heap data structure.
- Concept of Edge Relaxation (`if dist[u] + weight < dist[v]: dist[v] = dist[u] + weight`).

## 5. Visualization

```text
Graph:
    (4)
  0 ───> 1
  │      │ (2)
(1)│      v
  └────> 2 ───> 3
          (5)

Source = 0
Initial Distances: dist = [0, ∞, ∞, ∞]

Step 1: Pop node 0 (dist=0)
  Relax edge (0->1, w=4): dist[1] = min(∞, 0+4) = 4
  Relax edge (0->2, w=1): dist[2] = min(∞, 0+1) = 1
  Priority Queue: [(1, 2), (4, 1)]

Step 2: Pop node 2 (dist=1)  <-- Settled!
  Relax edge (2->3, w=5): dist[3] = min(∞, 1+5) = 6
  Priority Queue: [(4, 1), (6, 3)]

Step 3: Pop node 1 (dist=4)  <-- Settled!
  Relax edge (1->2, w=2): dist[2] is 1 <= 4+2 (no update)
  Priority Queue: [(6, 3)]

Step 4: Pop node 3 (dist=6)  <-- Settled!

Final Distances: dist = [0, 4, 1, 6]
```

## 6. How It Works

1. Set `dist[source] = 0` and `dist[v] = ∞` for all other vertices $v$.
2. Insert `(0, source)` into a Min-Priority Queue `PQ` (ordered by distance).
3. While `PQ` is not empty:
   - Extract pair `(d, u)` with minimum distance `d`.
   - If `d > dist[u]`, continue (skip stale priority queue entries).
   - For each neighbor `(v, weight)` of `u`:
     - **Edge Relaxation:** If `dist[u] + weight < dist[v]`:
       - `dist[v] = dist[u] + weight`.
       - `parent[v] = u` (optional for path reconstruction).
       - Push `(dist[v], v)` into `PQ`.
4. Return `dist` array.

## 7. Step-by-Step Algorithm

1. `dist = array of size V filled with ∞`, `dist[src] = 0`.
2. `PQ = MinPriorityQueue()`, `PQ.push((0, src))`.
3. Loop while `PQ` is not empty:
   1. `(d, u) = PQ.pop()`.
   2. If `d > dist[u]`: continue.
   3. For each `(v, weight)` in `adj[u]`:
      - If `dist[u] + weight < dist[v]`:
        - `dist[v] = dist[u] + weight`.
        - `PQ.push((dist[v], v))`.
4. Return `dist`.

## 8. Pseudocode

```text
function Dijkstra(graph, source):
    dist = array of size graph.V filled with infinity
    parent = array of size graph.V filled with null
    dist[source] = 0

    PQ = MinPriorityQueue()
    PQ.insert(0, source)

    while PQ is not empty:
        (d, u) = PQ.extractMin()

        if d > dist[u]:
            continue

        for each neighbor (v, weight) of u:
            if dist[u] + weight < dist[v]:
                dist[v] = dist[u] + weight
                parent[v] = u
                PQ.insert(dist[v], v)

    return dist, parent
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>
#include <limits.h>

#define MAX_V 100
#define INF INT_MAX

struct Edge {
    int to;
    int weight;
    struct Edge* next;
};

struct Graph {
    int numVertices;
    struct Edge* adj[MAX_V];
};

struct Graph* createGraph(int v) {
    struct Graph* g = (struct Graph*)malloc(sizeof(struct Graph));
    g->numVertices = v;
    for (int i = 0; i < v; i++) g->adj[i] = NULL;
    return g;
}

void addEdge(struct Graph* g, int u, int v, int w) {
    struct Edge* e = (struct Edge*)malloc(sizeof(struct Edge));
    e->to = v; e->weight = w; e->next = g->adj[u]; g->adj[u] = e;
}

int minDistance(int dist[], bool visited[], int V) {
    int min = INF, min_index = -1;
    for (int v = 0; v < V; v++) {
        if (!visited[v] && dist[v] <= min) {
            min = dist[v];
            min_index = v;
        }
    }
    return min_index;
}

void dijkstra(struct Graph* g, int src) {
    int V = g->numVertices;
    int dist[MAX_V];
    bool visited[MAX_V] = {false};

    for (int i = 0; i < V; i++) dist[i] = INF;
    dist[src] = 0;

    for (int count = 0; count < V - 1; count++) {
        int u = minDistance(dist, visited, V);
        if (u == -1) break;
        visited[u] = true;

        struct Edge* e = g->adj[u];
        while (e != NULL) {
            int v = e->to;
            int w = e->weight;
            if (!visited[v] && dist[u] != INF && dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
            }
            e = e->next;
        }
    }

    printf("Vertex Distance from Source %d:\n", src);
    for (int i = 0; i < V; i++) {
        printf("Node %d: %d\n", i, dist[i]);
    }
}

int main() {
    struct Graph* g = createGraph(4);
    addEdge(g, 0, 1, 4);
    addEdge(g, 0, 2, 1);
    addEdge(g, 2, 1, 2);
    addEdge(g, 1, 3, 1);
    addEdge(g, 2, 3, 5);

    dijkstra(g, 0);
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <limits>

const int INF = std::numeric_limits<int>::max();

void dijkstra(int src, const std::vector<std::vector<std::pair<int, int>>>& adj, int V) {
    std::vector<int> dist(V, INF);
    // Min-heap storing pairs of (distance, vertex)
    std::priority_queue<std::pair<int, int>, 
                        std::vector<std::pair<int, int>>, 
                        std::greater<std::pair<int, int>>> pq;

    dist[src] = 0;
    pq.push({0, src});

    while (!pq.empty()) {
        auto [d, u] = pq.top();
        pq.pop();

        if (d > dist[u]) continue;

        for (auto& edge : adj[u]) {
            int v = edge.first;
            int weight = edge.second;

            if (dist[u] + weight < dist[v]) {
                dist[v] = dist[u] + weight;
                pq.push({dist[v], v});
            }
        }
    }

    std::cout << "Shortest distances from source " << src << ":\n";
    for (int i = 0; i < V; i++) {
        std::cout << "Node " << i << ": " << dist[i] << "\n";
    }
}

int main() {
    int V = 4;
    std::vector<std::vector<std::pair<int, int>>> adj(V);

    auto addEdge = [&](int u, int v, int w) {
        adj[u].push_back({v, w});
    };

    addEdge(0, 1, 4);
    addEdge(0, 2, 1);
    addEdge(2, 1, 2);
    addEdge(1, 3, 1);
    addEdge(2, 3, 5);

    dijkstra(0, adj, V);
    return 0;
}
```

### Java
```java
import java.util.*;

public class DijkstraGraph {
    static class Edge {
        int to, weight;
        Edge(int to, int weight) { this.to = to; this.weight = weight; }
    }

    static class Node implements Comparable<Node> {
        int id, dist;
        Node(int id, int dist) { this.id = id; this.dist = dist; }
        public int compareTo(Node o) { return Integer.compare(this.dist, o.dist); }
    }

    public static void dijkstra(int src, List<List<Edge>> adj, int V) {
        int[] dist = new int[V];
        Arrays.fill(dist, Integer.MAX_VALUE);
        PriorityQueue<Node> pq = new PriorityQueue<>();

        dist[src] = 0;
        pq.add(new Node(src, 0));

        while (!pq.isEmpty()) {
            Node current = pq.poll();
            int u = current.id;
            int d = current.dist;

            if (d > dist[u]) continue;

            for (Edge edge : adj.get(u)) {
                int v = edge.to;
                int weight = edge.weight;

                if (dist[u] + weight < dist[v]) {
                    dist[v] = dist[u] + weight;
                    pq.add(new Node(v, dist[v]));
                }
            }
        }

        System.out.println("Shortest distances from source " + src + ":");
        for (int i = 0; i < V; i++) {
            System.out.println("Node " + i + ": " + dist[i]);
        }
    }

    public static void main(String[] args) {
        int V = 4;
        List<List<Edge>> adj = new ArrayList<>();
        for (int i = 0; i < V; i++) adj.add(new ArrayList<>());

        adj.get(0).add(new Edge(1, 4));
        adj.get(0).add(new Edge(2, 1));
        adj.get(2).add(new Edge(1, 2));
        adj.get(1).add(new Edge(3, 1));
        adj.get(2).add(new Edge(3, 5));

        dijkstra(0, adj, V);
    }
}
```

### Python
```python
import heapq

def dijkstra(src: int, adj: list[list[tuple[int, int]]], V: int) -> list[int]:
    dist = [float('inf')] * V
    dist[src] = 0
    pq = [(0, src)]  # (distance, node)

    while pq:
        d, u = heapq.heappop(pq)

        if d > dist[u]:
            continue

        for v, weight in adj[u]:
            if dist[u] + weight < dist[v]:
                dist[v] = dist[u] + weight
                heapq.heappush(pq, (dist[v], v))

    return dist

if __name__ == "__main__":
    V = 4
    adj = [[] for _ in range(V)]
    
    def add_edge(u, v, w):
        adj[u].append((v, w))

    add_edge(0, 1, 4)
    add_edge(0, 2, 1)
    add_edge(2, 1, 2)
    add_edge(1, 3, 1)
    add_edge(2, 3, 5)

    distances = dijkstra(0, adj, V)
    print("Distances from node 0:", distances)
```

### JavaScript
```javascript
class MinPriorityQueue {
    constructor() { this.heap = []; }
    push(item) {
        this.heap.push(item);
        this._bubbleUp();
    }
    pop() {
        if (this.size() === 0) return null;
        const top = this.heap[0];
        const bottom = this.heap.pop();
        if (this.size() > 0) {
            this.heap[0] = bottom;
            this._sinkDown();
        }
        return top;
    }
    size() { return this.heap.length; }
    _bubbleUp() {
        let idx = this.heap.length - 1;
        while (idx > 0) {
            let pIdx = Math.floor((idx - 1) / 2);
            if (this.heap[idx].dist >= this.heap[pIdx].dist) break;
            [this.heap[idx], this.heap[pIdx]] = [this.heap[pIdx], this.heap[idx]];
            idx = pIdx;
        }
    }
    _sinkDown() {
        let idx = 0;
        const len = this.heap.length;
        while (true) {
            let left = 2 * idx + 1, right = 2 * idx + 2, smallest = idx;
            if (left < len && this.heap[left].dist < this.heap[smallest].dist) smallest = left;
            if (right < len && this.heap[right].dist < this.heap[smallest].dist) smallest = right;
            if (smallest === idx) break;
            [this.heap[idx], this.heap[smallest]] = [this.heap[smallest], this.heap[idx]];
            idx = smallest;
        }
    }
}

function dijkstra(src, adj, V) {
    const dist = new Array(V).fill(Infinity);
    dist[src] = 0;
    const pq = new MinPriorityQueue();
    pq.push({ node: src, dist: 0 });

    while (pq.size() > 0) {
        const { node: u, dist: d } = pq.pop();

        if (d > dist[u]) continue;

        for (const { to: v, weight } of adj[u]) {
            if (dist[u] + weight < dist[v]) {
                dist[v] = dist[u] + weight;
                pq.push({ node: v, dist: dist[v] });
            }
        }
    }

    return dist;
}

const V = 4;
const adj = Array.from({ length: V }, () => []);
const addEdge = (u, v, w) => adj[u].push({ to: v, weight: w });

addEdge(0, 1, 4);
addEdge(0, 2, 1);
addEdge(2, 1, 2);
addEdge(1, 3, 1);
addEdge(2, 3, 5);

console.log("Distances from node 0:", dijkstra(0, adj, V));
```

## 10. Code Explanation

Dijkstra's algorithm maintains a distance array `dist` and a Min-Priority Queue ordered by path distance. Starting at `dist[src] = 0`, it repeatedly extracts the unvisited vertex `u` with the smallest tentative distance. For every outgoing edge $(u, v)$ with weight $w$, it checks if traveling to $v$ via $u$ ($dist[u] + w$) yields a shorter path than the current `dist[v]`. If so, `dist[v]` is updated (relaxed) and `(dist[v], v)` is pushed to the priority queue. Once a node is popped from the priority queue (and non-stale), its shortest distance is finalized and will never be updated again.

## 11. Interactive Demo

An interactive road network map editor lets users place cities and weighted roads.

- Users click "Find Route" from City A to City B.
- Priority queue min-heap operations are displayed live on a side panel.
- Cities glow Green as their shortest distances are finalized.
- The final shortest path is highlighted in a thick neon yellow line.

## 12. Dry Run

**Graph:** $0 \xrightarrow{4} 1$, $0 \xrightarrow{1} 2$, $2 \xrightarrow{2} 1$

| Step | Current `u` (`d`) | Priority Queue State | `dist` Array | Action |
| :--- | :--- | :--- | :--- | :--- |
| **Init** | - | `[(0, 0)]` | `[0, ∞, ∞]` | Initialize source |
| 1 | `0` (`0`) | `[(1, 2), (4, 1)]` | `[0, 4, 1]` | Relax $0\to1$ (4) and $0\to2$ (1) |
| 2 | `2` (`1`) | `[(3, 1), (4, 1)]` | `[0, 3, 1]` | Relax $2\to1$ ($1+2=3 < 4$). Update `dist[1]` |
| 3 | `1` (`3`) | `[(4, 1)]` | `[0, 3, 1]` | Settled node 1 |
| 4 | `1` (`4`) | `[]` | `[0, 3, 1]` | Skip stale entry (`4 > dist[1] = 3`) |

Final distance to node 1 is **3** (via path $0 \to 2 \to 1$).

## 13. Time & Space Complexity

| Data Structure | Time Complexity | Space Complexity |
| :--- | :--- | :--- |
| **Binary Min-Heap** | $\mathcal{O}((V + E) \log V)$ | $\mathcal{O}(V + E)$ |
| **Fibonacci Heap** | $\mathcal{O}(E + V \log V)$ | $\mathcal{O}(V + E)$ |
| **Array (Unoptimized)** | $\mathcal{O}(V^2)$ | $\mathcal{O}(V)$ |

## 14. Advantages

- **Optimal Shortest Paths:** Guarantees absolute shortest paths for all non-negative weighted graphs.
- **Fast with Priority Queue:** Runs efficiently in $\mathcal{O}((V + E) \log V)$ time.
- **Single Source All Targets:** Computes distances to all reachable nodes in one execution pass.

## 15. Disadvantages

- **Fails on Negative Edge Weights:** Can produce incorrect answers or infinite loops on graphs containing negative edge weights (use Bellman-Ford instead).
- **Unnecessary Explorations:** Explores nodes in all radial directions; A* Search is faster when a specific goal location is known.

## 16. Applications

- GPS map routing (Google Maps).
- Network routing protocols (OSPF, IS-IS).
- Game AI movement pathfinding.
- Flight connection fare minimization.

## 17. Common Mistakes

- **Using on Negative Weight Edges:** Expecting Dijkstra's to handle negative edges correctly.
- **Not Skipping Stale Entries:** Forgetting `if (d > dist[u]) continue;` leading to redundant work.
- **Using Queue instead of Priority Queue:** Reducing Dijkstra's to BFS, breaking shortest path correctness on weighted graphs.

## 18. Interview Questions

1. Why does Dijkstra's algorithm fail on graphs with negative edge weights?
2. What is the time complexity of Dijkstra's algorithm using a Min-Heap vs an Array?
3. How can you modify Dijkstra's algorithm to reconstruct the exact path taken?
4. How does A* Search optimize Dijkstra's algorithm for goal-directed searches?

## 19. Practice Problems

**Easy:**
1. Implement Dijkstra's algorithm for an adjacency list representation.
2. Find the shortest path from node 0 to node N-1 in a weighted DAG.

**Medium:**
3. Solve "Network Delay Time" (LeetCode 743).
4. Find the path with minimum probability of failure.

**Hard:**
5. Find the K-shortest paths between a source and destination.

## 20. Related Algorithms

- [Breadth-First Search (BFS)](./31_breadth_first_search.md) (Unweighted graph version of Dijkstra)
- [Bellman-Ford Algorithm](./34_bellman_ford_algorithm.md) (Handles negative edge weights)
- [A* Search](./36_a_star_search.md) (Heuristic-guided Dijkstra variant)

## 21. Summary

Dijkstra's Algorithm is the definitive greedy algorithm for computing single-source shortest paths on non-negative weighted graphs. By leveraging a Min-Priority Queue to settle nodes in order of increasing distance, it runs in $\mathcal{O}((V + E) \log V)$ time and powers modern navigation systems and network routing protocols.

## 22. Quiz

**Question 1:** Who invented Dijkstra's Algorithm in 1956?
- A) Edsger W. Dijkstra
- B) Robert Tarjan
- C) Richard Bellman
- D) Lester Ford
- **Correct Answer:** A
- **Explanation:** Edsger W. Dijkstra designed the algorithm in 1956 while drinking coffee.

**Question 2:** What condition MUST be met for Dijkstra's Algorithm to work correctly?
- A) All edge weights must be non-negative ($\ge 0$)
- B) Graph must be a tree
- C) Graph must be acyclic
- D) All edge weights must be 1
- **Correct Answer:** A
- **Explanation:** Negative edge weights break Dijkstra's greedy assumption that once a node is popped, its distance is finalized.

**Question 3:** What is the time complexity of Dijkstra's algorithm using a Binary Min-Heap?
- A) $\mathcal{O}(V^2)$
- B) $\mathcal{O}((V + E) \log V)$
- C) $\mathcal{O}(V \cdot E)$
- D) $\mathcal{O}(V^3)$
- **Correct Answer:** B
- **Explanation:** Heap operations take $\mathcal{O}(\log V)$ per vertex and edge update.

**Question 4:** Which data structure yields the theoretical minimum $\mathcal{O}(E + V \log V)$ time for Dijkstra's?
- A) Stack
- B) Fibonacci Heap
- C) Array
- D) Queue
- **Correct Answer:** B
- **Explanation:** Fibonacci Heap supports $\mathcal{O}(1)$ amortized `decrease-key` operations.

**Question 5:** What is "Edge Relaxation" in Dijkstra's algorithm?
- A) Removing an edge from the graph
- B) Checking if `dist[u] + weight < dist[v]` and updating `dist[v]`
- C) Setting edge weights to zero
- D) Doubling edge weights
- **Correct Answer:** B
- **Explanation:** Relaxation tests if traveling to $v$ through $u$ offers a shorter path.

**Question 6:** What algorithm should be used instead of Dijkstra's if a graph contains negative edge weights?
- A) Breadth-First Search
- B) Bellman-Ford Algorithm
- C) Kruskal's Algorithm
- D) Prim's Algorithm
- **Correct Answer:** B
- **Explanation:** Bellman-Ford correctly handles negative edge weights and detects negative cycles.

**Question 7:** What does `if (d > dist[u]) continue;` do in Min-Heap implementations?
- A) Throws an exception
- B) Skips stale, outdated priority queue entries for vertex `u`
- C) Resets the graph
- D) Exits the loop
- **Correct Answer:** B
- **Explanation:** Since C++/Java priority queues do not support efficient decrease-key, duplicate entries are pushed, and older stale entries must be skipped.

**Question 8:** How does Dijkstra's algorithm differ from BFS?
- A) BFS uses a Queue (FIFO), Dijkstra's uses a Min-Priority Queue for weighted edges
- B) BFS is $\mathcal{O}(V^3)$
- C) Dijkstra's only works on trees
- D) BFS handles negative weights
- **Correct Answer:** A
- **Explanation:** BFS assumes equal edge weights; Dijkstra's handles varying non-negative weights using a priority queue.

**Question 9:** How can you reconstruct the exact shortest path after running Dijkstra's?
- A) By reversing the priority queue
- B) By following `parent` pointers backward from destination to source
- C) By re-sorting the graph
- D) By running DFS
- **Correct Answer:** B
- **Explanation:** Maintaining a `parent[v] = u` array during relaxation allows tracing the path back to the source.

**Question 10:** In what field is Dijkstra's algorithm used in link-state network protocols?
- A) OSPF (Open Shortest Path First)
- B) HTTP
- C) FTP
- D) DNS
- **Correct Answer:** A
- **Explanation:** Routers use OSPF to run Dijkstra's algorithm on network link-state topologies to calculate shortest routing tables.
