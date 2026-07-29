# Borůvka's Algorithm

## 1. Introduction

Borůvka's Algorithm is the oldest Minimum Spanning Tree (MST) algorithm in computer science, published by Czech mathematician Otakar Borůvka in 1926. Designed decades before the invention of digital computers, it was created to find an efficient, low-cost layout for the electrical power grid of Moravia.

Imagine a cluster of isolated islands in an archipelago. Instead of building bridges one by one sequentially, every island simultaneously identifies the single closest neighboring island and builds a bridge to it. Once these initial bridges connect islands into larger island groups, the groups simultaneously identify the closest neighboring group and build bridges between them. The number of independent groups halves at every step until a single unified bridge network connects all islands. That is Borůvka's Algorithm.

It was created as a naturally parallelizable, component-merging approach to building Minimum Spanning Trees.

You should study Borůvka's Algorithm for its historical significance, component-oriented merge design, and suitability for parallel/distributed MST computation.

## 2. Why Use This Algorithm?

Borůvka's algorithm is naturally parallel and guarantees log-bound convergence cycles.

**Benefits:**
- **Massively Parallelizable:** Every connected component can independently find its minimum outgoing edge concurrently without inter-thread locks.
- **Logarithmic Phase Bound:** Guarantees completion in at most $\mathcal{O}(\log V)$ contraction phases because the number of components drops by at least half each phase.
- **Historically Foundational:** The original MST algorithm that inspired modern parallel computing models.
- **Efficient for Distributed Systems:** Ideal for MapReduce and distributed graph frameworks (GraphX, Pregel).

**Performance:**
- **Time Complexity:** $\mathcal{O}(E \log V)$ for sequential implementation.
- **Space Complexity:** $\mathcal{O}(V + E)$ for graph representation and DSU components.

**When it is better than Kruskal's or Prim's:**
Borůvka's algorithm is superior on **massively parallel processors** and distributed graph computing clusters, where all components can evaluate minimum outgoing edges simultaneously.

## 3. Real-World Applications

- **Distributed Graph Processing:** Distributed MST construction in Apache Spark GraphX and Pregel.
- **Parallel Supercomputing:** High-performance parallel graph algorithms on GPU/MPI architectures.
- **Electrical & Communication Grid Layout:** Historical and modern regional infrastructure planning.

## 4. Prerequisites

Before learning Borůvka's algorithm, you should understand:
- Spanning Trees and Minimum Spanning Trees (MST).
- Disjoint-Set Union (DSU) / Union-Find data structure.
- Connected components in graphs.

## 5. Visualization

```text
Initial State (4 isolated components): [0], [1], [2], [3]
Edges: (0-1, w=1), (1-2, w=3), (2-3, w=2), (0-3, w=4)

Phase 1:
- Component [0] cheapest outgoing edge: (0-1, w=1)
- Component [1] cheapest outgoing edge: (0-1, w=1)
- Component [2] cheapest outgoing edge: (2-3, w=2)
- Component [3] cheapest outgoing edge: (2-3, w=2)

Add edges (0-1, w=1) and (2-3, w=2) to MST!
Merged Components: Component A {0, 1}, Component B {2, 3}. (Num components: 4 -> 2)

Phase 2:
- Component A {0, 1} cheapest outgoing edge: (1-2, w=3)
- Component B {2, 3} cheapest outgoing edge: (1-2, w=3)

Add edge (1-2, w=3) to MST!
Merged Components: Single Component {0, 1, 2, 3}. (Num components: 2 -> 1)

MST Complete! Total Cost = 1 + 2 + 3 = 6.
```

## 6. How It Works

1. Initialize a DSU structure where every vertex is its own component (`numComponents = V`).
2. While `numComponents > 1`:
   - Initialize an array `cheapest` of size $V$ with `null`/`INF` (to store the cheapest outgoing edge for each component).
   - For each edge $(u, v, w)$ in the graph:
     - Find components `setU = find(u)` and `setV = find(v)`.
     - If `setU == setV`, skip (edge is internal to the same component).
     - If `w < weight(cheapest[setU])`, set `cheapest[setU] = (u, v, w)`.
     - If `w < weight(cheapest[setV])`, set `cheapest[setV] = (u, v, w)`.
   - For each component $i$ from $0$ to $V - 1$:
     - If `cheapest[i]` exists:
       - Extract edge $(u, v, w) = \text{cheapest}[i]$.
       - If `find(u) != find(v)`:
         - `union(u, v)`.
         - Add $(u, v, w)$ to MST.
         - `numComponents--`.
3. Return MST.

## 7. Step-by-Step Algorithm

1. `numComponents = V`, `mstCost = 0`.
2. `cheapest = array of size V initialized to -1`.
3. Loop while `numComponents > 1`:
   1. Reset `cheapest` array to `-1`.
   2. Traverse all edges `e = (u, v, w)`:
      - `setU = find(u)`, `setV = find(v)`.
      - If `setU == setV`: continue.
      - If `cheapest[setU] == -1` or `edges[cheapest[setU]].w > w`: `cheapest[setU] = edge_index`.
      - If `cheapest[setV] == -1` or `edges[cheapest[setV]].w > w`: `cheapest[setV] = edge_index`.
   3. `addedCount = 0`.
   4. For `i = 0` to `V - 1`:
      - If `cheapest[i] != -1`:
        - `(u, v, w) = edges[cheapest[i]]`.
        - If `find(u) != find(v)`:
          - `union(u, v)`.
          - `mstCost += w`.
          - `numComponents--`.
          - `addedCount++`.
   5. If `addedCount == 0`: break (graph is disconnected).
4. Return `mstCost`.

## 8. Pseudocode

```text
function Boruvka(V, edges):
    dsu = DisjointSet(V)
    numComponents = V
    mstCost = 0
    mst = []

    while numComponents > 1:
        cheapest = array of size V filled with null

        for each edge (u, v, w) in edges:
            setU = dsu.find(u)
            setV = dsu.find(v)
            if setU != setV:
                if cheapest[setU] is null or w < cheapest[setU].weight:
                    cheapest[setU] = (u, v, w)
                if cheapest[setV] is null or w < cheapest[setV].weight:
                    cheapest[setV] = (u, v, w)

        edgesAdded = 0
        for i = 0 to V - 1:
            if cheapest[i] is not null:
                (u, v, w) = cheapest[i]
                if dsu.find(u) != dsu.find(v):
                    dsu.union(u, v)
                    mst.append((u, v, w))
                    mstCost = mstCost + w
                    numComponents = numComponents - 1
                    edgesAdded = edgesAdded + 1

        if edgesAdded == 0:
            break // Graph is disconnected

    return mst, mstCost
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>

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
    if (dsu->parent[i] == i) return i;
    return dsu->parent[i] = find(dsu, dsu->parent[i]);
}

bool unionSets(struct DSU* dsu, int x, int y) {
    int rx = find(dsu, x), ry = find(dsu, y);
    if (rx != ry) {
        if (dsu->rank[rx] < dsu->rank[ry]) dsu->parent[rx] = ry;
        else if (dsu->rank[rx] > dsu->rank[ry]) dsu->parent[ry] = rx;
        else { dsu->parent[ry] = rx; dsu->rank[rx]++; }
        return true;
    }
    return false;
}

void boruvka(int V, int E, struct Edge edges[]) {
    struct DSU* dsu = createDSU(V);
    int numComponents = V;
    int mstCost = 0;
    int cheapest[V];

    while (numComponents > 1) {
        for (int i = 0; i < V; i++) cheapest[i] = -1;

        for (int i = 0; i < E; i++) {
            int setU = find(dsu, edges[i].u);
            int setV = find(dsu, edges[i].v);
            if (setU != setV) {
                if (cheapest[setU] == -1 || edges[i].weight < edges[cheapest[setU]].weight)
                    cheapest[setU] = i;
                if (cheapest[setV] == -1 || edges[i].weight < edges[cheapest[setV]].weight)
                    cheapest[setV] = i;
            }
        }

        int edgesAdded = 0;
        for (int i = 0; i < V; i++) {
            if (cheapest[i] != -1) {
                int u = edges[cheapest[i]].u;
                int v = edges[cheapest[i]].v;
                int w = edges[cheapest[i]].weight;

                if (unionSets(dsu, u, v)) {
                    mstCost += w;
                    numComponents--;
                    edgesAdded++;
                    printf("Edge Added: %d - %d (weight: %d)\n", u, v, w);
                }
            }
        }
        if (edgesAdded == 0) break;
    }
    printf("Total MST Cost: %d\n", mstCost);
}

int main() {
    int V = 4, E = 5;
    struct Edge edges[] = {
        {0, 1, 1},
        {1, 2, 3},
        {2, 3, 2},
        {0, 3, 4},
        {0, 2, 5}
    };

    boruvka(V, E, edges);
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>

struct Edge {
    int u, v, weight;
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
            if (rank[rootI] < rank[rootJ]) parent[rootI] = rootJ;
            else if (rank[rootI] > rank[rootJ]) parent[rootJ] = rootI;
            else { parent[rootJ] = rootI; rank[rootI]++; }
            return true;
        }
        return false;
    }
};

void boruvka(int V, const std::vector<Edge>& edges) {
    DSU dsu(V);
    int numComponents = V;
    int mstCost = 0;

    while (numComponents > 1) {
        std::vector<int> cheapest(V, -1);

        for (int i = 0; i < edges.size(); i++) {
            int setU = dsu.find(edges[i].u);
            int setV = dsu.find(edges[i].v);
            if (setU != setV) {
                if (cheapest[setU] == -1 || edges[i].weight < edges[cheapest[setU]].weight)
                    cheapest[setU] = i;
                if (cheapest[setV] == -1 || edges[i].weight < edges[cheapest[setV]].weight)
                    cheapest[setV] = i;
            }
        }

        int edgesAdded = 0;
        for (int i = 0; i < V; i++) {
            if (cheapest[i] != -1) {
                int u = edges[cheapest[i]].u;
                int v = edges[cheapest[i]].v;
                int w = edges[cheapest[i]].weight;

                if (dsu.unite(u, v)) {
                    mstCost += w;
                    numComponents--;
                    edgesAdded++;
                    std::cout << "Edge Added: " << u << " - " << v << " (weight: " << w << ")\n";
                }
            }
        }
        if (edgesAdded == 0) break;
    }
    std::cout << "Total MST Cost: " << mstCost << "\n";
}

int main() {
    int V = 4;
    std::vector<Edge> edges = {
        {0, 1, 1},
        {1, 2, 3},
        {2, 3, 2},
        {0, 3, 4},
        {0, 2, 5}
    };

    boruvka(V, edges);
    return 0;
}
```

### Java
```java
import java.util.*;

public class BoruvkaGraph {
    static class Edge {
        int u, v, weight;
        Edge(int u, int v, int weight) {
            this.u = u; this.v = v; this.weight = weight;
        }
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

    public static void boruvka(int V, List<Edge> edges) {
        DSU dsu = new DSU(V);
        int numComponents = V;
        int mstCost = 0;

        while (numComponents > 1) {
            int[] cheapest = new int[V];
            Arrays.fill(cheapest, -1);

            for (int i = 0; i < edges.size(); i++) {
                int setU = dsu.find(edges.get(i).u);
                int setV = dsu.find(edges.get(i).v);
                if (setU != setV) {
                    if (cheapest[setU] == -1 || edges.get(i).weight < edges.get(cheapest[setU]).weight)
                        cheapest[setU] = i;
                    if (cheapest[setV] == -1 || edges.get(i).weight < edges.get(cheapest[setV]).weight)
                        cheapest[setV] = i;
                }
            }

            int edgesAdded = 0;
            for (int i = 0; i < V; i++) {
                if (cheapest[i] != -1) {
                    Edge e = edges.get(cheapest[i]);
                    if (dsu.union(e.u, e.v)) {
                        mstCost += e.weight;
                        numComponents--;
                        edgesAdded++;
                        System.out.println("Edge Added: " + e.u + " - " + e.v + " (weight: " + e.weight + ")");
                    }
                }
            }
            if (edgesAdded == 0) break;
        }
        System.out.println("Total MST Cost: " + mstCost);
    }

    public static void main(String[] args) {
        int V = 4;
        List<Edge> edges = new ArrayList<>();
        edges.add(new Edge(0, 1, 1));
        edges.add(new Edge(1, 2, 3));
        edges.add(new Edge(2, 3, 2));
        edges.add(new Edge(0, 3, 4));
        edges.add(new Edge(0, 2, 5));

        boruvka(V, edges);
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

def boruvka(V: int, edges: list[tuple[int, int, int]]) -> tuple[list[tuple[int, int, int]], int]:
    dsu = DSU(V)
    num_components = V
    mst = []
    mst_cost = 0

    while num_components > 1:
        cheapest = [-1] * V

        for i, (u, v, w) in enumerate(edges):
            set_u = dsu.find(u)
            set_v = dsu.find(v)
            if set_u != set_v:
                if cheapest[set_u] == -1 or w < edges[cheapest[set_u]][2]:
                    cheapest[set_u] = i
                if cheapest[set_v] == -1 or w < edges[cheapest[set_v]][2]:
                    cheapest[set_v] = i

        edges_added = 0
        for i in range(V):
            if cheapest[i] != -1:
                u, v, w = edges[cheapest[i]]
                if dsu.union(u, v):
                    mst.append((u, v, w))
                    mst_cost += w
                    num_components -= 1
                    edges_added += 1

        if edges_added == 0:
            break

    return mst, mst_cost

if __name__ == "__main__":
    V = 4
    edges = [
        (0, 1, 1),
        (1, 2, 3),
        (2, 3, 2),
        (0, 3, 4),
        (0, 2, 5)
    ]

    mst, cost = boruvka(V, edges)
    print("MST Edges:", mst)
    print("Total Cost:", cost)
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

function boruvka(V, edges) {
    const dsu = new DSU(V);
    let numComponents = V;
    const mst = [];
    let mstCost = 0;

    while (numComponents > 1) {
        const cheapest = new Array(V).fill(-1);

        for (let i = 0; i < edges.length; i++) {
            const { u, v, weight: w } = edges[i];
            const setU = dsu.find(u), setV = dsu.find(v);
            if (setU !== setV) {
                if (cheapest[setU] === -1 || w < edges[cheapest[setU]].weight)
                    cheapest[setU] = i;
                if (cheapest[setV] === -1 || w < edges[cheapest[setV]].weight)
                    cheapest[setV] = i;
            }
        }

        let edgesAdded = 0;
        for (let i = 0; i < V; i++) {
            if (cheapest[i] !== -1) {
                const { u, v, weight: w } = edges[cheapest[i]];
                if (dsu.union(u, v)) {
                    mst.push({ u, v, weight: w });
                    mstCost += w;
                    numComponents--;
                    edgesAdded++;
                }
            }
        }
        if (edgesAdded === 0) break;
    }

    return { mst, mstCost };
}

const V = 4;
const edges = [
    { u: 0, v: 1, weight: 1 },
    { u: 1, v: 2, weight: 3 },
    { u: 2, v: 3, weight: 2 },
    { u: 0, v: 3, weight: 4 },
    { u: 0, v: 2, weight: 5 }
];

console.log(boruvka(V, edges));
```

## 10. Code Explanation

Borůvka's algorithm takes a component-contraction approach. It starts with $V$ individual components. In each phase iteration, every component $C_i$ scans all edges connected to it and identifies its cheapest outgoing edge. Then, all identified cheapest edges across all components are added to the MST simultaneously, merging the components using DSU `union`. Because every component merges with at least one other component during a phase, the total number of connected components drops by at least half ($\ge 50\%$) in every phase. Therefore, the algorithm is guaranteed to complete in at most $\mathcal{O}(\log V)$ phases.

## 11. Interactive Demo

An interactive multi-component graph visualizer displays components grouped in distinct background colors.

- Step 1: Each colored component glows, highlighting its minimum outward boundary edge in Yellow.
- Step 2: Selected outward edges flash simultaneously, and component regions merge together into a single enlarged color region.
- Phase counter logs $V \to V/2 \to V/4 \dots \to 1$.

## 12. Dry Run

**Graph:** $V = 4$, Edges: $(0-1, 1), (1-2, 3), (2-3, 2)$

| Phase | Component | Cheapest Outgoing Edge Selected | Component Merges | Active Components Remaining |
| :--- | :--- | :--- | :--- | :--- |
| **Phase 1** | `[0]` | $(0-1, 1)$ | Merge `[0]` and `[1]` | `{0, 1}` |
| **Phase 1** | `[1]` | $(0-1, 1)$ | Already merged | `{0, 1}` |
| **Phase 1** | `[2]` | $(2-3, 2)$ | Merge `[2]` and `[3]` | `{2, 3}` |
| **Phase 1** | `[3]` | $(2-3, 2)$ | Already merged | `{0, 1}`, `{2, 3}` (Count: 2) |
| **Phase 2** | `{0, 1}` | $(1-2, 3)$ | Merge `{0, 1}` and `{2, 3}` | `{0, 1, 2, 3}` (Count: 1) |

Total MST Cost: $1 + 2 + 3 = \mathbf{6}$.

## 13. Time & Space Complexity

| Operation | Complexity | Reason |
| :--- | :--- | :--- |
| **Phase Count** | $\mathcal{O}(\log V)$ | Components drop by at least half per phase |
| **Work Per Phase** | $\mathcal{O}(E)$ | Scanning $E$ edges to find component minimums |
| **Overall Sequential Time** | $\mathcal{O}(E \log V)$ | $\log V$ phases $\times E$ edge scans |
| **Parallel Time** | $\mathcal{O}(\log V)$ | $\mathcal{O}(\log V)$ parallel steps with $E$ processors |
| **Space Complexity** | $\mathcal{O}(V + E)$ | Storage for graph edges and DSU tracking |

## 14. Advantages

- **Massively Parallelizable:** Components can find cheapest outgoing edges independently without locks.
- **Logarithmic Phase Guarantee:** Completes in at most $\mathcal{O}(\log V)$ phase steps.
- **Ideal for MapReduce & Distributed Frameworks:** Natural fit for GraphX / Pregel distributed graph processing.

## 15. Disadvantages

- **Edge Duplication in Selections:** Multiple components can pick the same edge, requiring deduplication checks.
- **Sequential Overhead:** On a single thread, Kruskal's or Prim's are often faster due to lower constant factors.

## 16. Applications

- Parallel supercomputing MST calculations.
- Apache Spark GraphX distributed graph algorithms.
- Regional electrical grid optimization.

## 17. Common Mistakes

- **Not Checking `find(u) != find(v)` During Union Phase:** Adding duplicate edge instances twice when two components select the same undirected edge.
- **Infinite Loop on Disconnected Graphs:** Forgetting to break if a phase finishes without adding any edges.

## 18. Interview Questions

1. When was Borůvka's Algorithm created and what was its original practical purpose?
2. Why is the maximum number of contraction phases bounded by $\mathcal{O}(\log V)$?
3. How does Borůvka's Algorithm differ from Kruskal's and Prim's?

## 19. Practice Problems

**Easy:**
1. Implement Borůvka's algorithm for a graph of 4 nodes.
2. Count the number of contraction phases required for a given graph.

**Medium:**
3. Implement a multi-threaded parallel Borůvka's algorithm using C++ `std::async` or OpenMP.

## 20. Related Algorithms

- [Kruskal's Algorithm](./37_kruskals_algorithm.md) (Edge-sorting MST algorithm)
- [Prim's Algorithm](./38_prims_algorithm.md) (Vertex-growing MST algorithm)

## 21. Summary

Borůvka's Algorithm is the earliest Minimum Spanning Tree algorithm, invented by Otakar Borůvka in 1926. It operates by concurrently finding the cheapest outgoing edge for every connected component, contracting components until a single MST remains in at most $\mathcal{O}(\log V)$ phases and $\mathcal{O}(E \log V)$ time.

## 22. Quiz

**Question 1:** Who published the first Minimum Spanning Tree algorithm in 1926?
- A) Otakar Borůvka
- B) Joseph Kruskal
- C) Robert Prim
- D) Edsger Dijkstra
- **Correct Answer:** A
- **Explanation:** Czech mathematician Otakar Borůvka published the algorithm in 1926.

**Question 2:** Why is the number of phases in Borůvka's Algorithm bounded by $\mathcal{O}(\log V)$?
- A) Edges are sorted in binary
- B) Every phase contracts the number of components by at least $50\%$
- C) It uses binary search
- D) Nodes are prime
- **Correct Answer:** B
- **Explanation:** Merging components in pairs reduces total component count by at least half per phase.

**Question 3:** What is the main advantage of Borůvka's Algorithm over Kruskal's?
- A) It uses less memory
- B) Component minimum edge scans can be executed concurrently in parallel
- C) It works on directed graphs
- D) It does not require edge weights
- **Correct Answer:** B
- **Explanation:** Every component can find its cheapest outgoing edge independently in parallel.

**Question 4:** What is the overall sequential time complexity of Borůvka's Algorithm?
- A) $\mathcal{O}(V + E)$
- B) $\mathcal{O}(E \log V)$
- C) $\mathcal{O}(V^3)$
- D) $\mathcal{O}(E^2)$
- **Correct Answer:** B
- **Explanation:** $\mathcal{O}(\log V)$ contraction phases $\times \mathcal{O}(E)$ edge scanning per phase.

**Question 5:** What happens if two components select the exact same edge $(u, v, w)$ in the same phase?
- A) The program crashes
- B) The DSU `union` succeeds for the first pick and safely ignores the second duplicate pick
- C) The cost is added twice
- D) A cycle is formed
- **Correct Answer:** B
- **Explanation:** DSU `find(u) != find(v)` check ensures the second attempt is safely skipped.

**Question 6:** What distributed computing paradigm uses Borůvka's algorithm principles?
- A) MapReduce / Pregel / Apache Spark GraphX
- B) Relational SQL
- C) Assembly
- D) CSS grid
- **Correct Answer:** A
- **Explanation:** Component-based message passing matches Pregel / GraphX distributed graph frameworks.

**Question 7:** What structure tracks component merging in Borůvka's algorithm?
- A) Stack
- B) Disjoint-Set Union (DSU)
- C) Matrix
- D) Queue
- **Correct Answer:** B
- **Explanation:** DSU maintains component set identifiers.

**Question 8:** What is the space complexity of Borůvka's Algorithm?
- A) $\mathcal{O}(1)$
- B) $\mathcal{O}(V + E)$
- C) $\mathcal{O}(V^2)$
- D) $\mathcal{O}(2^V)$
- **Correct Answer:** B
- **Explanation:** Requires space to store graph edges, component maps, and DSU arrays.

**Question 9:** For what original application did Otakar Borůvka design the algorithm in 1926?
- A) Computer networks
- B) Electric power grid layout for Moravia
- C) Flight routes
- D) Internet routing
- **Correct Answer:** B
- **Explanation:** Borůvka designed it to construct an economical power grid layout for Moravia.

**Question 10:** What happens if Borůvka's algorithm runs on a disconnected graph?
- A) It loops infinitely unless an `addedCount == 0` check is included
- B) It automatically connects the graph
- C) It converts edges to negative
- D) It sorts vertices
- **Correct Answer:** A
- **Explanation:** If no outward edges exist between disconnected components, `addedCount == 0` must break the loop.
