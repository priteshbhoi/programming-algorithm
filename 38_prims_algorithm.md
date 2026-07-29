# Prim's Algorithm

## 1. Introduction

Prim's Algorithm is a greedy Minimum Spanning Tree (MST) algorithm developed by Vojtěch Jarník in 1930 and independently rediscovered by Robert C. Prim in 1957 and Edsger W. Dijkstra in 1959. Starting from an arbitrary root vertex, Prim's algorithm grows a single Minimum Spanning Tree one vertex at a time by always picking the cheapest connecting edge that links an unvisited vertex to the growing tree structure.

Imagine building a national high-speed rail network starting from the capital city. Rather than sorting all proposed tracks across the whole continent at once (like Kruskal's), Prim's algorithm looks at all outward track expansion options directly connected to the current rail network, picks the shortest extension, connects that new city, and repeats until all cities are connected.

It was created to solve the Minimum Spanning Tree problem using a vertex-growing greedy strategy.

You should use Prim's algorithm when you need to compute a Minimum Spanning Tree for **dense graphs** ($E \approx V^2$) where an adjacency matrix or list allows fast priority queue neighbor exploration.

## 2. Why Use This Algorithm?

Prim's algorithm grows a single connected tree component outward, avoiding the need to sort all global graph edges.

**Benefits:**
- **Optimal for Dense Graphs:** Achieves $\mathcal{O}(V^2)$ time with adjacency matrices or $\mathcal{O}(E + V \log V)$ with Fibonacci heaps.
- **Tree-Growing Strategy:** Always maintains a single connected tree component during execution.
- **No Disjoint Set Data Structure Required:** Uses standard Min-Priority Queues instead of DSU.
- **Efficient for Dense Matrices:** Avoids sorting $E = \mathcal{O}(V^2)$ edges upfront.

**Performance:**
- **Time Complexity:** $\mathcal{O}((V + E) \log V)$ using Min-Binary Heap / Priority Queue (or $\mathcal{O}(V^2)$ using Adjacency Matrix).
- **Space Complexity:** $\mathcal{O}(V + E)$ for graph representation, key array, and priority queue.

**When it is better than Kruskal's algorithm:**
Prim's algorithm is significantly faster than Kruskal's on **dense graphs** where $E \approx V^2$, because it avoids sorting $\mathcal{O}(V^2)$ edges upfront.

## 3. Real-World Applications

- **Infrastructure & Utility Networks:** Expanding gas, water, or fiber-optic networks outward from a central hub.
- **Circuit Board Wiring:** Minimum length tree wiring on integrated circuit layout pins.
- **Clustering & Pattern Recognition:** Building spatial proximity trees for data clustering.
- **Computer Graphics:** Generating procedural maze structures.

## 4. Prerequisites

Before learning Prim's algorithm, you should understand:
- Spanning Trees and Minimum Spanning Trees (MST).
- Min-Priority Queue / Min-Heap data structure.
- Weighted graph representations (Adjacency List, Adjacency Matrix).

## 5. Visualization

```text
Graph:
    (4)
  0 ─── 1
  │     │ (2)
(1)│     │
  └──── 2 ─── 3
         (5)

Start Root = Node 0
Init: key = [0, ∞, ∞, ∞], inMST = [F, F, F, F]

Step 1: Pick Node 0 (key=0)  <-- Add 0 to MST
  Examine edges from 0:
    To 1 (w=4): update key[1]=4, parent[1]=0
    To 2 (w=1): update key[2]=1, parent[2]=0
  inMST = [T, F, F, F], PQ = [(1, 2), (4, 1)]

Step 2: Pick Node 2 (key=1, edge 0-2)  <-- Add 2 to MST
  Examine edges from 2:
    To 1 (w=2 < 4): update key[1]=2, parent[1]=2
    To 3 (w=5): update key[3]=5, parent[3]=2
  inMST = [T, F, T, F], PQ = [(2, 1), (5, 3)]

Step 3: Pick Node 1 (key=2, edge 2-1)  <-- Add 1 to MST
  inMST = [T, T, T, F]

Step 4: Pick Node 3 (key=5, edge 2-3)  <-- Add 3 to MST
  inMST = [T, T, T, T]

Final MST Edges: (0-2, w=1), (2-1, w=2), (2-3, w=5). Total Cost = 8.
```

## 6. How It Works

1. Initialize `key[v] = ∞` and `inMST[v] = false` for all vertices. Set `key[src] = 0`.
2. Insert `(0, src)` into a Min-Priority Queue `PQ`.
3. While `PQ` is not empty:
   - Extract pair `(w, u)` with minimum key weight `w`.
   - If `inMST[u]` is true, continue (skip stale entries).
   - Mark `inMST[u] = true` (add $u$ to MST).
   - For each neighbor `(v, weight)` of `u`:
     - If `!inMST[v]` and `weight < key[v]`:
       - `key[v] = weight`.
       - `parent[v] = u`.
       - Push `(key[v], v)` into `PQ`.
4. Return MST edges tracked by `parent` array and total cost sum of `key`.

## 7. Step-by-Step Algorithm

1. `key = array of size V filled with ∞`, `inMST = array of size V filled with false`.
2. `parent = array of size V filled with -1`.
3. `key[start] = 0`, `PQ.push((0, start))`.
4. Loop while `PQ` is not empty:
   1. `(w, u) = PQ.pop()`.
   2. If `inMST[u] == true`: continue.
   3. `inMST[u] = true`.
   4. For each `(v, weight)` in `adj[u]`:
      - If `inMST[v] == false` and `weight < key[v]`:
        - `key[v] = weight`.
        - `parent[v] = u`.
        - `PQ.push((key[v], v))`.
5. Compute total MST cost = $\sum \text{key}[i]$.

## 8. Pseudocode

```text
function Prim(graph, startVertex):
    key = array of size graph.V filled with infinity
    parent = array of size graph.V filled with null
    inMST = boolean array of size graph.V filled with false

    key[startVertex] = 0
    PQ = MinPriorityQueue()
    PQ.insert(0, startVertex)

    while PQ is not empty:
        (w, u) = PQ.extractMin()

        if inMST[u]:
            continue

        inMST[u] = true

        for each neighbor (v, weight) of u:
            if not inMST[v] and weight < key[v]:
                key[v] = weight
                parent[v] = u
                PQ.insert(key[v], v)

    return parent, key
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
    struct Edge* e1 = (struct Edge*)malloc(sizeof(struct Edge));
    e1->to = v; e1->weight = w; e1->next = g->adj[u]; g->adj[u] = e1;

    struct Edge* e2 = (struct Edge*)malloc(sizeof(struct Edge));
    e2->to = u; e2->weight = w; e2->next = g->adj[v]; g->adj[v] = e2;
}

int minKey(int key[], bool inMST[], int V) {
    int min = INF, min_index = -1;
    for (int v = 0; v < V; v++) {
        if (!inMST[v] && key[v] < min) {
            min = key[v];
            min_index = v;
        }
    }
    return min_index;
}

void prim(struct Graph* g, int src) {
    int V = g->numVertices;
    int parent[MAX_V];
    int key[MAX_V];
    bool inMST[MAX_V] = {false};

    for (int i = 0; i < V; i++) key[i] = INF;
    key[src] = 0;
    parent[src] = -1;

    for (int count = 0; count < V - 1; count++) {
        int u = minKey(key, inMST, V);
        if (u == -1) break;
        inMST[u] = true;

        struct Edge* e = g->adj[u];
        while (e != NULL) {
            int v = e->to;
            int w = e->weight;
            if (!inMST[v] && w < key[v]) {
                parent[v] = u;
                key[v] = w;
            }
            e = e->next;
        }
    }

    int mstCost = 0;
    printf("MST Edges:\n");
    for (int i = 0; i < V; i++) {
        if (parent[i] != -1) {
            printf("%d - %d (weight: %d)\n", parent[i], i, key[i]);
            mstCost += key[i];
        }
    }
    printf("Total MST Cost: %d\n", mstCost);
}

int main() {
    struct Graph* g = createGraph(4);
    addEdge(g, 0, 1, 4);
    addEdge(g, 0, 2, 1);
    addEdge(g, 1, 2, 2);
    addEdge(g, 2, 3, 5);

    prim(g, 0);
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

void prim(int src, const std::vector<std::vector<std::pair<int, int>>>& adj, int V) {
    std::vector<int> key(V, INF);
    std::vector<int> parent(V, -1);
    std::vector<bool> inMST(V, false);

    // Min-heap: (weight, node)
    std::priority_queue<std::pair<int, int>, 
                        std::vector<std::pair<int, int>>, 
                        std::greater<std::pair<int, int>>> pq;

    key[src] = 0;
    pq.push({0, src});

    while (!pq.empty()) {
        auto [w, u] = pq.top();
        pq.pop();

        if (inMST[u]) continue;
        inMST[u] = true;

        for (auto& edge : adj[u]) {
            int v = edge.first;
            int weight = edge.second;

            if (!inMST[v] && weight < key[v]) {
                key[v] = weight;
                parent[v] = u;
                pq.push({key[v], v});
            }
        }
    }

    int mstCost = 0;
    std::cout << "MST Edges:\n";
    for (int i = 0; i < V; i++) {
        if (parent[i] != -1) {
            std::cout << parent[i] << " - " << i << " (weight: " << key[i] << ")\n";
            mstCost += key[i];
        }
    }
    std::cout << "Total MST Cost: " << mstCost << "\n";
}

int main() {
    int V = 4;
    std::vector<std::vector<std::pair<int, int>>> adj(V);

    auto addEdge = [&](int u, int v, int w) {
        adj[u].push_back({v, w});
        adj[v].push_back({u, w});
    };

    addEdge(0, 1, 4);
    addEdge(0, 2, 1);
    addEdge(1, 2, 2);
    addEdge(2, 3, 5);

    prim(0, adj, V);
    return 0;
}
```

### Java
```java
import java.util.*;

public class PrimGraph {
    static class Edge {
        int to, weight;
        Edge(int to, int weight) { this.to = to; this.weight = weight; }
    }

    static class Node implements Comparable<Node> {
        int id, weight;
        Node(int id, int weight) { this.id = id; this.weight = weight; }
        public int compareTo(Node o) { return Integer.compare(this.weight, o.weight); }
    }

    public static void prim(int src, List<List<Edge>> adj, int V) {
        int[] key = new int[V];
        int[] parent = new int[V];
        boolean[] inMST = new boolean[V];
        Arrays.fill(key, Integer.MAX_VALUE);
        Arrays.fill(parent, -1);

        PriorityQueue<Node> pq = new PriorityQueue<>();
        key[src] = 0;
        pq.add(new Node(src, 0));

        while (!pq.isEmpty()) {
            Node curr = pq.poll();
            int u = curr.id;

            if (inMST[u]) continue;
            inMST[u] = true;

            for (Edge edge : adj.get(u)) {
                int v = edge.to;
                int weight = edge.weight;

                if (!inMST[v] && weight < key[v]) {
                    key[v] = weight;
                    parent[v] = u;
                    pq.add(new Node(v, key[v]));
                }
            }
        }

        int mstCost = 0;
        System.out.println("MST Edges:");
        for (int i = 0; i < V; i++) {
            if (parent[i] != -1) {
                System.out.println(parent[i] + " - " + i + " (weight: " + key[i] + ")");
                mstCost += key[i];
            }
        }
        System.out.println("Total MST Cost: " + mstCost);
    }

    public static void main(String[] args) {
        int V = 4;
        List<List<Edge>> adj = new ArrayList<>();
        for (int i = 0; i < V; i++) adj.add(new ArrayList<>());

        adj.get(0).add(new Edge(1, 4)); adj.get(1).add(new Edge(0, 4));
        adj.get(0).add(new Edge(2, 1)); adj.get(2).add(new Edge(0, 1));
        adj.get(1).add(new Edge(2, 2)); adj.get(2).add(new Edge(1, 2));
        adj.get(2).add(new Edge(3, 5)); adj.get(3).add(new Edge(2, 5));

        prim(0, adj, V);
    }
}
```

### Python
```python
import heapq

def prim(src: int, adj: list[list[tuple[int, int]]], V: int) -> tuple[list[tuple[int, int, int]], int]:
    key = [float('inf')] * V
    parent = [-1] * V
    in_mst = [False] * V

    key[src] = 0
    pq = [(0, src)]  # (weight, node)

    while pq:
        w, u = heapq.heappop(pq)

        if in_mst[u]:
            continue
        in_mst[u] = True

        for v, weight in adj[u]:
            if not in_mst[v] and weight < key[v]:
                key[v] = weight
                parent[v] = u
                heapq.heappush(pq, (weight, v))

    mst_edges = []
    mst_cost = 0
    for i in range(V):
        if parent[i] != -1:
            mst_edges.append((parent[i], i, key[i]))
            mst_cost += key[i]

    return mst_edges, mst_cost

if __name__ == "__main__":
    V = 4
    adj = [[] for _ in range(V)]
    
    def add_edge(u, v, w):
        adj[u].append((v, w))
        adj[v].append((u, w))

    add_edge(0, 1, 4)
    add_edge(0, 2, 1)
    add_edge(1, 2, 2)
    add_edge(2, 3, 5)

    mst, cost = prim(0, adj, V)
    print("MST Edges:", mst)
    print("Total MST Cost:", cost)
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
            if (this.heap[idx].weight >= this.heap[pIdx].weight) break;
            [this.heap[idx], this.heap[pIdx]] = [this.heap[pIdx], this.heap[idx]];
            idx = pIdx;
        }
    }
    _sinkDown() {
        let idx = 0;
        const len = this.heap.length;
        while (true) {
            let left = 2 * idx + 1, right = 2 * idx + 2, smallest = idx;
            if (left < len && this.heap[left].weight < this.heap[smallest].weight) smallest = left;
            if (right < len && this.heap[right].weight < this.heap[smallest].weight) smallest = right;
            if (smallest === idx) break;
            [this.heap[idx], this.heap[smallest]] = [this.heap[smallest], this.heap[idx]];
            idx = smallest;
        }
    }
}

function prim(src, adj, V) {
    const key = new Array(V).fill(Infinity);
    const parent = new Array(V).fill(-1);
    const inMST = new Array(V).fill(false);

    key[src] = 0;
    const pq = new MinPriorityQueue();
    pq.push({ node: src, weight: 0 });

    while (pq.size() > 0) {
        const { node: u } = pq.pop();

        if (inMST[u]) continue;
        inMST[u] = true;

        for (const { to: v, weight } of adj[u]) {
            if (!inMST[v] && weight < key[v]) {
                key[v] = weight;
                parent[v] = u;
                pq.push({ node: v, weight: key[v] });
            }
        }
    }

    let mstCost = 0;
    const mst = [];
    for (let i = 0; i < V; i++) {
        if (parent[i] !== -1) {
            mst.push({ u: parent[i], v: i, weight: key[i] });
            mstCost += key[i];
        }
    }
    return { mst, mstCost };
}

const V = 4;
const adj = Array.from({ length: V }, () => []);
const addEdge = (u, v, w) => {
    adj[u].push({ to: v, weight: w });
    adj[v].push({ to: u, weight: w });
};

addEdge(0, 1, 4);
addEdge(0, 2, 1);
addEdge(1, 2, 2);
addEdge(2, 3, 5);

console.log(prim(0, adj, V));
```

## 10. Code Explanation

Prim's algorithm operates by growing a single connected tree component. It maintains a `key` array where `key[v]` represents the minimum weight of any edge connecting vertex $v$ to the currently growing MST. Initially, `key[root] = 0` and all other keys are $\infty$. A Min-Priority Queue extracts the unvisited vertex $u$ with the minimum key weight. Once $u$ is added to the MST (`inMST[u] = true`), the algorithm examines all outgoing edges $(u, v)$ with weight $w$. For any neighbor $v$ not yet in the MST, if $w < \text{key}[v]$, it updates `key[v] = w`, records `parent[v] = u`, and pushes the updated key to the priority queue.

## 11. Interactive Demo

An interactive graph workspace lets users place nodes and weighted edges.

- The root node glows Blue as the tree origin.
- As the algorithm runs, candidate expansion edges extending outward from the active tree flash Yellow.
- The cheapest edge is chosen, turning Green as the new vertex is absorbed into the growing MST tree component.

## 12. Dry Run

**Graph:** $0-1 (4)$, $0-2 (1)$, $1-2 (2)$, $2-3 (5)$

| Step | `u` Popped | `inMST` State | `key` Updates | Outward Edges Evaluated | Action |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Init** | - | `[F, F, F, F]` | `[0, ∞, ∞, ∞]` | - | Start at node 0 |
| 1 | `0` | `[T, F, F, F]` | `[0, 4, 1, ∞]` | $0-1 (w=4), 0-2 (w=1)$ | Add node 0 |
| 2 | `2` | `[T, F, T, F]` | `[0, 2, 1, 5]` | $2-1 (w=2 < 4), 2-3 (w=5)$ | Add node 2, edge 0-2 ($w=1$) |
| 3 | `1` | `[T, T, T, F]` | `[0, 2, 1, 5]` | $1-0$ (inMST), $1-2$ (inMST) | Add node 1, edge 2-1 ($w=2$) |
| 4 | `3` | `[T, T, T, T]` | `[0, 2, 1, 5]` | $3-2$ (inMST) | Add node 3, edge 2-3 ($w=5$) |

Total MST Weight: $1 + 2 + 5 = \mathbf{8}$.

## 13. Time & Space Complexity

| Data Structure | Time Complexity | Reason |
| :--- | :--- | :--- |
| **Binary Min-Heap** | $\mathcal{O}((V + E) \log V)$ | Queue insertions take $\mathcal{O}(\log V)$ time |
| **Fibonacci Heap** | $\mathcal{O}(E + V \log V)$ | Amortized $\mathcal{O}(1)$ key reduction operations |
| **Adjacency Matrix (Unoptimized)** | $\mathcal{O}(V^2)$ | Scanning min key array takes $\mathcal{O}(V)$ time per step |

## 14. Advantages

- **Superior on Dense Graphs:** Runs in $\mathcal{O}(V^2)$ using adjacency matrices, beating Kruskal's $\mathcal{O}(V^2 \log V)$ on dense graphs.
- **Maintains Connected Component:** Always produces a valid connected subtree at every step.
- **No Union-Find Required:** Uses standard priority queues without DSU structures.

## 15. Disadvantages

- **Slower on Sparse Graphs:** Slower than Kruskal's when $E \ll V^2$.
- **Requires Single Connected Component:** Fails if the graph is disconnected (must be wrapped in a component loop).

## 16. Applications

- Laying physical distribution networks (water, gas, electricity).
- Distance-based spatial clustering algorithms.
- Maze generation algorithms.

## 17. Common Mistakes

- **Confusing Prim's and Dijkstra's:** In Prim's, `key[v]` is updated to `weight` (edge cost). In Dijkstra's, `dist[v]` is updated to `dist[u] + weight` (path cost).
- **Not Skipping Stale Heap Entries:** Omitting `if (inMST[u]) continue;` leading to redundant processing.

## 18. Interview Questions

1. What is the fundamental difference between Prim's algorithm and Dijkstra's algorithm?
2. Why is Prim's algorithm preferred over Kruskal's for dense graphs?
3. How does Prim's algorithm handle negative edge weights? (Answer: Correctly, as long as no negative weight cycles exist, though MST definition applies to any weights).

## 19. Practice Problems

**Easy:**
1. Implement Prim's algorithm using a Min-Heap.
2. Calculate total MST cost for a 5-node graph.

**Medium:**
3. Adapt Prim's algorithm for dense graphs using an Adjacency Matrix in $\mathcal{O}(V^2)$ time.

**Hard:**
4. Implement Prim's algorithm using Fibonacci Heap in C++ to achieve $\mathcal{O}(E + V \log V)$ time.

## 20. Related Algorithms

- [Kruskal's Algorithm](./37_kruskals_algorithm.md) (Edge-centric MST algorithm)
- [Borůvka's Algorithm](./39_boruvkas_algorithm.md) (Component-merging MST algorithm)
- [Dijkstra's Algorithm](./33_dijkstras_algorithm.md) (Shortest path counterpart)

## 21. Summary

Prim's Algorithm is a greedy vertex-growing Minimum Spanning Tree (MST) algorithm. By expanding outward from a root vertex and always selecting the cheapest edge connecting an unvisited vertex to the tree, it runs in $\mathcal{O}((V + E) \log V)$ time (or $\mathcal{O}(V^2)$ on dense matrices), making it optimal for dense networks.

## 22. Quiz

**Question 1:** Who originally published Prim's Algorithm in 1930?
- A) Vojtěch Jarník
- B) Robert C. Prim
- C) Edsger Dijkstra
- D) Joseph Kruskal
- **Correct Answer:** A
- **Explanation:** Czech mathematician Vojtěch Jarník published the algorithm in 1930.

**Question 2:** How does Prim's algorithm choose the next vertex to add to the MST?
- A) By picking the edge with smallest weight globally across all edges
- B) By picking the unvisited vertex connected to the growing tree by the smallest edge weight
- C) By picking a random vertex
- D) By running BFS
- **Correct Answer:** B
- **Explanation:** Prim's grows a single tree by picking the cheapest edge crossing the tree cut.

**Question 3:** What is the difference in key relaxation between Prim's and Dijkstra's?
- A) Prim's uses `key[v] = weight`, Dijkstra's uses `dist[v] = dist[u] + weight`
- B) Prim's uses stacks
- C) Dijkstra's handles negative graphs
- D) There is no difference
- **Correct Answer:** A
- **Explanation:** Prim's stores minimum connecting edge weight, while Dijkstra's stores accumulated path distance from source.

**Question 4:** What is the time complexity of Prim's algorithm using an Adjacency Matrix and simple array lookup?
- A) $\mathcal{O}(V^2)$
- B) $\mathcal{O}(V \log V)$
- C) $\mathcal{O}(E \log E)$
- D) $\mathcal{O}(V^3)$
- **Correct Answer:** A
- **Explanation:** Scanning the $V$-element key array $V$ times takes $\mathcal{O}(V^2)$ time.

**Question 5:** For what graph type is Prim's $\mathcal{O}(V^2)$ implementation faster than Kruskal's?
- A) Dense graphs ($E \approx V^2$)
- B) Sparse graphs ($E \ll V^2$)
- C) Trees
- D) Linear graphs
- **Correct Answer:** A
- **Explanation:** On dense graphs, Kruskal's spends $\mathcal{O}(V^2 \log V)$ sorting edges, whereas Prim's runs in $\mathcal{O}(V^2)$.

**Question 6:** What data structure yields $\mathcal{O}(E + V \log V)$ time for Prim's algorithm?
- A) Binary Heap
- B) Fibonacci Heap
- C) Array
- D) Queue
- **Correct Answer:** B
- **Explanation:** Fibonacci heaps support $\mathcal{O}(1)$ amortized `decrease-key` operations.

**Question 7:** Does Prim's algorithm require a Disjoint-Set Union (DSU) structure?
- A) Yes
- B) No
- **Correct Answer:** B
- **Explanation:** Prim's tracks tree membership using a simple boolean `inMST` array.

**Question 8:** What happens if Prim's algorithm is run on a disconnected graph without outer loops?
- A) It visits all nodes
- B) It builds an MST only for the connected component containing the start node
- C) It crashes
- D) It returns cost 0
- **Correct Answer:** B
- **Explanation:** Prim's grows outward from a single root and cannot cross disconnected component gaps.

**Question 9:** Can Prim's algorithm handle negative edge weights?
- A) Yes, edge weights can be negative for MST algorithms
- B) No, edge weights must be positive
- C) Only if there are no cycles
- D) Only on directed graphs
- **Correct Answer:** A
- **Explanation:** MST definitions hold for negative weights; Prim's greedy choice property remains valid.

**Question 10:** What array is maintained during Prim's algorithm to reconstruct the MST edges?
- A) `visited` array
- B) `parent` array
- C) `color` array
- D) `depth` array
- **Correct Answer:** B
- **Explanation:** `parent[v] = u` tracks which vertex $u$ connected $v$ to the MST.
