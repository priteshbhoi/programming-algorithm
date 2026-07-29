# Kruskal's Algorithm

## 1. Introduction

Kruskal's Algorithm is a greedy Minimum Spanning Tree (MST) algorithm designed by Joseph Kruskal in 1956. It finds a subset of edges in a connected, weighted, undirected graph that connects all vertices together with the **minimum total edge weight** and without forming any cycles.

Imagine electrical engineers designing a power grid to connect several cities. Building power lines costs money proportional to cable distance. Instead of building expensive redundant loops, Kruskal's algorithm considers all potential power lines across the region from cheapest to most expensive, adding each line to the grid as long as it doesn't form a redundant loop, until every city is powered for the lowest total cost.

It was created to solve the Minimum Spanning Tree problem efficiently using a edge-centric greedy strategy.

You should use Kruskal's algorithm when you need to compute a Minimum Spanning Tree for sparse graphs ($E \ll V^2$), or when the graph edges are already sorted or easily sorted in memory.

## 2. Why Use This Algorithm?

Kruskal's algorithm relies on the elegant Disjoint-Set Union (DSU) / Union-Find data structure to achieve near-linear execution time.

**Benefits:**
- **Optimal MST Construction:** Guaranteed minimum edge weight sum for any connected undirected graph.
- **Edge-Centric Greedy Strategy:** Easily operates on edge lists without building complex adjacency structures.
- **Near-Linear Execution:** Runs in $\mathcal{O}(E \log E)$ or $\mathcal{O}(E \log V)$ time.
- **Handles Disconnected Graphs:** Naturally produces a Minimum Spanning Forest if the graph is disconnected.

**Performance:**
- **Time Complexity:** $\mathcal{O}(E \log E)$ or $\mathcal{O}(E \log V)$ due to edge sorting. (DSU operations run in near-constant $\mathcal{O}(\alpha(V))$ amortized time).
- **Space Complexity:** $\mathcal{O}(V + E)$ for storing edges and DSU parent/rank arrays.

**When it is better than Prim's algorithm:**
Kruskal's algorithm outperforms Prim's algorithm on **sparse graphs** (where $E \ll V^2$) and when edge data is already available as a simple edge list.

## 3. Real-World Applications

- **Telecommunications & Power Networks:** Laying optical fiber cables or high-voltage power lines between cities for minimal installation cost.
- **Cluster Analysis in Machine Learning:** Single-linkage hierarchical clustering builds MSTs using Kruskal's logic.
- **Circuit Design:** Minimizing wire lengths on printed circuit boards (VLSI).
- **Image Segmentation:** Graph-based image processing grouping pixels into coherent object regions.

## 4. Prerequisites

Before learning Kruskal's algorithm, you should understand:
- Concept of Spanning Trees and Minimum Spanning Trees (MST).
- [Disjoint-Set Union (DSU) / Union-Find](./README.md) data structure with Path Compression and Union by Rank.
- Edge sorting algorithms ($\mathcal{O}(E \log E)$).

## 5. Visualization

```text
Graph Edges (Sorted by Weight):
  (1,2, w=1), (2,3, w=2), (0,1, w=3), (0,2, w=4), (1,3, w=5)

Vertices: 4 (Nodes 0, 1, 2, 3)

Step 1: Pick Edge (1-2, w=1)
  DSU Find(1) != Find(2) -> Add Edge! MST Edges: {(1-2, 1)}

Step 2: Pick Edge (2-3, w=2)
  DSU Find(2) != Find(3) -> Add Edge! MST Edges: {(1-2, 1), (2-3, 2)}

Step 3: Pick Edge (0-1, w=3)
  DSU Find(0) != Find(1) -> Add Edge! MST Edges: {(1-2, 1), (2-3, 2), (0-1, 3)}

Step 4: Pick Edge (0-2, w=4)
  DSU Find(0) == Find(2) (Cycle formed!) -> REJECT!

MST Complete! Total Edges = V - 1 = 3. Total Weight = 1 + 2 + 3 = 6.
```

## 6. How It Works

1. Create a **Disjoint Set Union (DSU)** data structure containing $V$ isolated sets (one for each vertex).
2. Sort all $E$ graph edges in non-decreasing order of their weight.
3. Initialize `mst_weight = 0` and `mst_edges = []`.
4. Iterate through the sorted edges $(u, v, w)$:
   - Use DSU `find(u)` and `find(v)` to check if $u$ and $v$ belong to the same connected component.
   - If `find(u) != find(v)` (adding this edge does **not** create a cycle):
     - Perform `union(u, v)`.
     - Append $(u, v, w)$ to `mst_edges`.
     - `mst_weight += w`.
   - If `len(mst_edges) == V - 1`, break early!
5. Return `mst_edges` and `mst_weight`.

## 7. Step-by-Step Algorithm

1. Sort all edges in `edges` array by weight in ascending order.
2. Initialize DSU arrays: `parent[i] = i`, `rank[i] = 0` for all $i \in [0 \dots V-1]$.
3. Set `edges_count = 0`, `mst_cost = 0`.
4. For each edge `(u, v, w)` in sorted `edges`:
   - `root_u = find(u)`
   - `root_v = find(v)`
   - If `root_u != root_v`:
     - `unionSets(root_u, root_v)`
     - `mst_cost += w`
     - `edges_count++`
     - If `edges_count == V - 1`: break.
5. Return `mst_cost`.

## 8. Pseudocode

```text
function Kruskal(vertices, edges):
    sort edges in non-decreasing order of weight
    
    dsu = DisjointSet(vertices)
    mst = []
    mstCost = 0
    
    for each edge (u, v, w) in sorted edges:
        if dsu.find(u) != dsu.find(v):
            dsu.union(u, v)
            mst.append((u, v, w))
            mstCost = mstCost + w
            
            if length(mst) == vertices - 1:
                break
                
    return mst, mstCost
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>

struct Edge {
    int u, v, weight;
};

struct DSU {
    int* parent;
    int* rank;
};

struct DSU* createDSU(int n) {
    struct DSU* dsu = (struct DSU*)malloc(sizeof(struct DSU));
    dsu->parent = (int*)malloc(n * sizeof(int));
    dsu->rank = (int*)malloc(n * sizeof(int));
    for (int i = 0; i < n; i++) {
        dsu->parent[i] = i;
        dsu->rank[i] = 0;
    }
    return dsu;
}

int find(struct DSU* dsu, int i) {
    if (dsu->parent[i] == i)
        return i;
    return dsu->parent[i] = find(dsu, dsu->parent[i]);
}

void unionSets(struct DSU* dsu, int x, int y) {
    int rootX = find(dsu, x);
    int rootY = find(dsu, y);
    if (rootX != rootY) {
        if (dsu->rank[rootX] < dsu->rank[rootY]) {
            dsu->parent[rootX] = rootY;
        } else if (dsu->rank[rootX] > dsu->rank[rootY]) {
            dsu->parent[rootY] = rootX;
        } else {
            dsu->parent[rootY] = rootX;
            dsu->rank[rootX]++;
        }
    }
}

int compareEdges(const void* a, const void* b) {
    return ((struct Edge*)a)->weight - ((struct Edge*)b)->weight;
}

void kruskal(int V, int E, struct Edge edges[]) {
    qsort(edges, E, sizeof(struct Edge), compareEdges);

    struct DSU* dsu = createDSU(V);
    int mstCost = 0;
    int edgesCount = 0;

    printf("MST Edges:\n");
    for (int i = 0; i < E; i++) {
        int u = edges[i].u;
        int v = edges[i].v;
        int w = edges[i].weight;

        if (find(dsu, u) != find(dsu, v)) {
            unionSets(dsu, u, v);
            printf("%d - %d (weight: %d)\n", u, v, w);
            mstCost += w;
            edgesCount++;
            if (edgesCount == V - 1) break;
        }
    }
    printf("Total Minimum Spanning Tree Cost: %d\n", mstCost);
}

int main() {
    int V = 4, E = 5;
    struct Edge edges[] = {
        {1, 2, 1},
        {2, 3, 2},
        {0, 1, 3},
        {0, 2, 4},
        {1, 3, 5}
    };

    kruskal(V, E, edges);
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

struct Edge {
    int u, v, weight;
    bool operator<(const Edge& other) const {
        return weight < other.weight;
    }
};

class DSU {
private:
    std::vector<int> parent, rank;
public:
    DSU(int n) : parent(n), rank(n, 0) {
        for (int i = 0; i < n; i++) parent[i] = i;
    }
    int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]);
    }
    bool unite(int i, int j) {
        int rootI = find(i), rootJ = find(j);
        if (rootI != rootJ) {
            if (rank[rootI] < rank[rootJ]) std::swap(rootI, rootJ);
            parent[rootJ] = rootI;
            if (rank[rootI] == rank[rootJ]) rank[rootI]++;
            return true;
        }
        return false;
    }
};

void kruskal(int V, std::vector<Edge>& edges) {
    std::sort(edges.begin(), edges.end());
    DSU dsu(V);
    int mstCost = 0;
    int edgesCount = 0;

    std::cout << "MST Edges:\n";
    for (const auto& edge : edges) {
        if (dsu.unite(edge.u, edge.v)) {
            std::cout << edge.u << " - " << edge.v << " (weight: " << edge.weight << ")\n";
            mstCost += edge.weight;
            edgesCount++;
            if (edgesCount == V - 1) break;
        }
    }
    std::cout << "Total Minimum Spanning Tree Cost: " << mstCost << "\n";
}

int main() {
    int V = 4;
    std::vector<Edge> edges = {
        {1, 2, 1},
        {2, 3, 2},
        {0, 1, 3},
        {0, 2, 4},
        {1, 3, 5}
    };

    kruskal(V, edges);
    return 0;
}
```

### Java
```java
import java.util.*;

public class KruskalGraph {
    static class Edge implements Comparable<Edge> {
        int u, v, weight;
        Edge(int u, int v, int weight) {
            this.u = u; this.v = v; this.weight = weight;
        }
        public int compareTo(Edge o) { return Integer.compare(this.weight, o.weight); }
    }

    static class DSU {
        int[] parent, rank;
        DSU(int n) {
            parent = new int[n]; rank = new int[n];
            for (int i = 0; i < n; i++) parent[i] = i;
        }
        int find(int i) {
            if (parent[i] == i) return i;
            return parent[i] = find(parent[i]);
        }
        boolean union(int i, int j) {
            int rootI = find(i), rootJ = find(j);
            if (rootI != rootJ) {
                if (rank[rootI] < rank[rootJ]) parent[rootI] = rootJ;
                else if (rank[rootI] > rank[rootJ]) parent[rootJ] = rootI;
                else { parent[rootJ] = rootI; rank[rootI]++; }
                return true;
            }
            return false;
        }
    }

    public static void kruskal(int V, List<Edge> edges) {
        Collections.sort(edges);
        DSU dsu = new DSU(V);
        int mstCost = 0, count = 0;

        System.out.println("MST Edges:");
        for (Edge edge : edges) {
            if (dsu.union(edge.u, edge.v)) {
                System.out.println(edge.u + " - " + edge.v + " (weight: " + edge.weight + ")");
                mstCost += edge.weight;
                count++;
                if (count == V - 1) break;
            }
        }
        System.out.println("Total Minimum Spanning Tree Cost: " + mstCost);
    }

    public static void main(String[] args) {
        int V = 4;
        List<Edge> edges = new ArrayList<>();
        edges.add(new Edge(1, 2, 1));
        edges.add(new Edge(2, 3, 2));
        edges.add(new Edge(0, 1, 3));
        edges.add(new Edge(0, 2, 4));
        edges.add(new Edge(1, 3, 5));

        kruskal(V, edges);
    }
}
```

### Python
```python
class DSU:
    def __init__(self, n: int):
        self.parent = list(range(n))
        self.rank = [0] * n

    def find(self, i: int) -> int:
        if self.parent[i] == i:
            return i
        self.parent[i] = self.find(self.parent[i])
        return self.parent[i]

    def union(self, i: int, j: int) -> bool:
        root_i = self.find(i)
        root_j = self.find(j)
        if root_i != root_j:
            if self.rank[root_i] < self.rank[root_j]:
                self.parent[root_i] = root_j
            elif self.rank[root_i] > self.rank[root_j]:
                self.parent[root_j] = root_i
            else:
                self.parent[root_j] = root_i
                self.rank[root_i] += 1
            return True
        return False

def kruskal(V: int, edges: list[tuple[int, int, int]]) -> tuple[list[tuple[int, int, int]], int]:
    # Sort edges by weight
    sorted_edges = sorted(edges, key=lambda e: e[2])
    dsu = DSU(V)
    mst = []
    mst_cost = 0

    for u, v, w in sorted_edges:
        if dsu.union(u, v):
            mst.append((u, v, w))
            mst_cost += w
            if len(mst) == V - 1:
                break

    return mst, mst_cost

if __name__ == "__main__":
    V = 4
    edges = [
        (1, 2, 1),
        (2, 3, 2),
        (0, 1, 3),
        (0, 2, 4),
        (1, 3, 5)
    ]

    mst_edges, cost = kruskal(V, edges)
    print("MST Edges:", mst_edges)
    print("Total MST Cost:", cost)
```

### JavaScript
```javascript
class DSU {
    constructor(n) {
        this.parent = Array.from({ length: n }, (_, i) => i);
        this.rank = new Array(n).fill(0);
    }
    find(i) {
        if (this.parent[i] === i) return i;
        return this.parent[i] = this.find(this.parent[i]);
    }
    union(i, j) {
        const rootI = this.find(i), rootJ = this.find(j);
        if (rootI !== rootJ) {
            if (this.rank[rootI] < this.rank[rootJ]) this.parent[rootI] = rootJ;
            else if (this.rank[rootI] > this.rank[rootJ]) this.parent[rootJ] = rootI;
            else { this.parent[rootJ] = rootI; this.rank[rootI]++; }
            return true;
        }
        return false;
    }
}

function kruskal(V, edges) {
    edges.sort((a, b) => a.weight - b.weight);
    const dsu = new DSU(V);
    const mst = [];
    let mstCost = 0;

    for (const { u, v, weight } of edges) {
        if (dsu.union(u, v)) {
            mst.push({ u, v, weight });
            mstCost += weight;
            if (mst.length === V - 1) break;
        }
    }

    return { mst, mstCost };
}

const V = 4;
const edges = [
    { u: 1, v: 2, weight: 1 },
    { u: 2, v: 3, weight: 2 },
    { u: 0, v: 1, weight: 3 },
    { u: 0, v: 2, weight: 4 },
    { u: 1, v: 3, weight: 5 }
];

console.log(kruskal(V, edges));
```

## 10. Code Explanation

Kruskal's algorithm takes a greedy edge-centric approach. First, it sorts all $E$ edges in non-decreasing order of weight, taking $\mathcal{O}(E \log E)$ time. It then initializes a Disjoint-Set Union (DSU) structure to manage connected components. The algorithm iterates through sorted edges one by one. For each edge $(u, v, w)$, `dsu.find(u)` and `dsu.find(v)` determine whether $u$ and $v$ are already connected in the current forest. If they belong to different components, adding the edge will not create a cycle; `dsu.union(u, v)` merges the two components and the edge is added to the MST. If they belong to the same set, the edge is skipped to avoid forming a cycle. Execution stops as soon as $V - 1$ edges are accepted.

## 11. Interactive Demo

An interactive network layout canvas allows users to drag nodes and set edge costs.

- Clicking "Build MST (Kruskal)" highlights all candidate edges in gray.
- Edges glow Gold as they are evaluated in sorted order.
- Accepted edges turn Green, while cycle-creating rejected edges flash Red before disappearing.
- DSU set component trees are displayed visually underneath the network.

## 12. Dry Run

**Edges:** $(1-2, 1), (2-3, 2), (0-1, 3), (0-2, 4), (1-3, 5)$ ($V = 4$)

| Edge Evaluated | Weight | `find(u)` vs `find(v)` | DSU Action | Accepted into MST? | Cumulative Cost |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `1 - 2` | 1 | `1 != 2` | `union(1, 2)` | YES | 1 |
| `2 - 3` | 2 | `2 != 3` | `union(2, 3)` | YES | 3 |
| `0 - 1` | 3 | `0 != 1` | `union(0, 1)` | YES | 6 |
| `0 - 2` | 4 | `0 == 2` | Skip | NO (Cycle) | 6 |
| `1 - 3` | 5 | `1 == 3` | Skip | NO (Cycle) | 6 |

Final MST Cost: **6** (Edges: $1-2, 2-3, 0-1$).

## 13. Time & Space Complexity

| Operation | Time Complexity | Reason |
| :--- | :--- | :--- |
| **Edge Sorting** | $\mathcal{O}(E \log E)$ | Sorting $E$ edges by weight |
| **DSU Operations** | $\mathcal{O}(E \cdot \alpha(V))$ | $E$ find/union calls using Inverse Ackermann $\alpha(V) \le 4$ |
| **Overall Time** | $\mathcal{O}(E \log E)$ or $\mathcal{O}(E \log V)$ | Dominated by edge sorting step |
| **Space Complexity** | $\mathcal{O}(V + E)$ | Storage for edges and DSU parent/rank arrays |

## 14. Advantages

- **Optimal for Sparse Graphs:** Runs faster than Prim's algorithm when $E \ll V^2$.
- **Simple Data Structures:** Uses simple edge arrays and DSU instead of complex priority heaps.
- **Naturally Builds Forests:** Works out-of-the-box on disconnected graphs.

## 15. Disadvantages

- **Sorting Overhead:** Sorting $E$ edges can be slow on dense graphs ($E \approx V^2$).
- **Requires All Edges Upfront:** Cannot process dynamic graphs where vertices arrive incrementally.

## 16. Applications

- Telecommunication network cabling (minimum wire length).
- Single-linkage hierarchical clustering in data science.
- Computer circuit board track layout (VLSI design).
- Water pipe network distribution systems.

## 17. Common Mistakes

- **Forgetting DSU Path Compression:** Using a naive DSU implementation without path compression degrades time to $\mathcal{O}(E \cdot V)$.
- **Not Stopping at $V-1$ Edges:** Continuing loop checks unnecessarily after $V - 1$ edges are added.

## 18. Interview Questions

1. Why does Kruskal's algorithm require a Disjoint-Set Union (DSU) data structure?
2. What is the time complexity of DSU operations with Path Compression and Union by Rank?
3. Compare Kruskal's vs Prim's algorithm for dense graphs ($E \approx V^2$).
4. Can Kruskal's algorithm handle negative edge weights? (Answer: Yes, edge sorting and DSU cycle checks work identically regardless of sign).

## 19. Practice Problems

**Easy:**
1. Implement DSU with Path Compression and Union by Rank.
2. Run Kruskal's algorithm on a graph of 5 nodes to find MST cost.

**Medium:**
3. Solve "Min Cost to Connect All Points" (LeetCode 1584) using Manhattan distance and Kruskal's.
4. Find the Maximum Spanning Tree cost.

**Hard:**
5. Find the Second-Best Minimum Spanning Tree of a graph.

## 20. Related Algorithms

- [Prim's Algorithm](./38_prims_algorithm.md) (Vertex-centric MST algorithm)
- [Borůvka's Algorithm](./39_boruvkas_algorithm.md) (Parallel component-based MST algorithm)
- [Dijkstra's Algorithm](./33_dijkstras_algorithm.md) (Shortest path algorithm)

## 21. Summary

Kruskal's Algorithm is a greedy Minimum Spanning Tree (MST) algorithm that sorts all edges by weight and uses a Disjoint-Set Union (DSU) data structure to connect vertices while preventing cycles. Running in $\mathcal{O}(E \log E)$ time, it is the algorithm of choice for finding minimum spanning trees on sparse graphs.

## 22. Quiz

**Question 1:** Who invented Kruskal's Algorithm in 1956?
- A) Joseph Kruskal
- B) Robert Prim
- C) Otakar Borůvka
- D) Edsger Dijkstra
- **Correct Answer:** A
- **Explanation:** Joseph Kruskal published the algorithm in 1956.

**Question 2:** Which data structure is essential for fast cycle detection in Kruskal's Algorithm?
- A) Stack
- B) Disjoint-Set Union (DSU) / Union-Find
- C) Hash Map
- D) Binary Search Tree
- **Correct Answer:** B
- **Explanation:** DSU checks whether two vertices belong to the same component in near-constant time.

**Question 3:** What is the dominant step determining Kruskal's algorithm time complexity?
- A) DSU creation
- B) Sorting all graph edges by weight ($\mathcal{O}(E \log E)$)
- C) Matrix multiplication
- D) Queue popping
- **Correct Answer:** B
- **Explanation:** Sorting $E$ edges takes $\mathcal{O}(E \log E)$ time, which dominates the overall runtime.

**Question 4:** How many edges does a Minimum Spanning Tree of a connected graph with $V$ vertices contain?
- A) $V$
- B) $V - 1$
- C) $E / 2$
- D) $V + 1$
- **Correct Answer:** B
- **Explanation:** A spanning tree connecting $V$ vertices contains exactly $V - 1$ edges.

**Question 5:** Can Kruskal's algorithm handle graphs with negative edge weights?
- A) Yes, without any modifications
- B) No, edge weights must be positive
- C) Only if the graph is a tree
- D) Only on directed graphs
- **Correct Answer:** A
- **Explanation:** Sorting edges and DSU cycle checks operate correctly regardless of edge sign.

**Question 6:** What amortized time complexity do DSU operations achieve with Path Compression and Union by Rank?
- A) $\mathcal{O}(\log V)$
- B) $\mathcal{O}(\alpha(V))$ where $\alpha$ is the Inverse Ackermann function ($\le 4$)
- C) $\mathcal{O}(V)$
- D) $\mathcal{O}(1)$ exact
- **Correct Answer:** B
- **Explanation:** Path compression and rank union yield near $\mathcal{O}(1)$ amortized time bounded by $\alpha(V) \le 4$.

**Question 7:** For what type of graph is Kruskal's algorithm generally preferred over Prim's algorithm?
- A) Dense graphs ($E \approx V^2$)
- B) Sparse graphs ($E \ll V^2$)
- C) Bipartite graphs
- D) Complete graphs
- **Correct Answer:** B
- **Explanation:** On sparse graphs, sorting fewer edges ($E \ll V^2$) makes Kruskal's faster than Prim's.

**Question 8:** What happens if Kruskal's algorithm is run on a disconnected graph?
- A) It throws a runtime error
- B) It produces a Minimum Spanning Forest (a set of MSTs for each component)
- C) It loops infinitely
- D) It returns cost 0
- **Correct Answer:** B
- **Explanation:** It naturally connects vertices within each component, yielding a minimum spanning forest.

**Question 9:** Why does Kruskal's algorithm reject an edge if `find(u) == find(v)`?
- A) The edge is too long
- B) Adding the edge would create a cycle
- C) The edge is negative
- D) Node $v$ is full
- **Correct Answer:** B
- **Explanation:** If $u$ and $v$ are already in the same connected set, adding an edge between them creates a closed loop/cycle.

**Question 10:** What is the maximum value of the Inverse Ackermann function $\alpha(V)$ for any practical input size $V \le 10^{80}$?
- A) 100
- B) 4
- C) $V / 2$
- D) $\log_2 V$
- **Correct Answer:** B
- **Explanation:** $\alpha(V) \le 4$ for all physical universes and real-world inputs.
