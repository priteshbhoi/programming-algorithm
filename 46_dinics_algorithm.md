# Dinic's Algorithm for Max Flow

## 1. Introduction
Dinic's Algorithm is a strongly polynomial time algorithm for computing the maximum flow in a flow network, conceived in 1970 by Yefim Dinitz. It is a faster version of the Edmonds-Karp algorithm (which relies on Ford-Fulkerson). Dinic's algorithm improves upon Edmonds-Karp by using the concept of **Level Graphs** and **Blocking Flows**, allowing it to send multiple units of flow simultaneously along paths of the same length.

## 2. Why Use This Algorithm?
Dinic's algorithm is significantly faster than the Ford-Fulkerson and Edmonds-Karp algorithms on most networks. 
- Ford-Fulkerson: $O(E \cdot \text{max\_flow})$
- Edmonds-Karp: $O(V \cdot E^2)$
- Dinic's Algorithm: $O(V^2 E)$

For networks with unit capacities, Dinic's algorithm runs in $O(E \sqrt{V})$ time, making it the algorithm of choice for solving Bipartite Matching problems efficiently.

## 3. Real-World Applications
- **Telecommunications:** Routing data across a network without exceeding bandwidth capacities.
- **Traffic Networks:** Finding the maximum number of vehicles that can travel from point A to B.
- **Image Segmentation:** Dividing an image into foreground and background in computer vision.
- **Bipartite Matching:** Assigning tasks to workers where each worker can only do certain tasks.
- **Supply Chain Management:** Maximizing the transport of goods from factories to consumers.

## 4. Prerequisites
Before studying Dinic's Algorithm, you should be familiar with:
- Graphs (Vertices and Edges, Directed Graphs)
- Flow Networks (Source, Sink, Capacities, Flow)
- Residual Graphs (Forward and Backward edges)
- Breadth-First Search (BFS)
- Depth-First Search (DFS)
- Ford-Fulkerson Method

## 5. Visualization
Consider a flow network with Source (S=0) and Sink (T=3):
```text
      (10)
   0 ------> 1
   |         |
(10|         | (10)
   v         v
   2 ------> 3
      (10)
```
Capacity: S->1: 10, S->2: 10, 1->3: 10, 2->3: 10.
Level graph from BFS from S(Level 0):
- Level 0: S(0)
- Level 1: 1, 2
- Level 2: T(3)

Blocking flow finds paths S->1->T and S->2->T simultaneously.
Total Max Flow = 20.

## 6. How It Works
Dinic's Algorithm runs in phases. In each phase:
1. **Level Graph Creation:** It uses BFS to create a level graph from the source. The level of a vertex is its shortest distance (in terms of number of edges) from the source in the residual graph. If the sink cannot be reached, the algorithm terminates.
2. **Blocking Flow:** It uses DFS to find multiple augmenting paths in the level graph and send as much flow as possible. A blocking flow means we saturate at least one edge on every path from source to sink in the level graph.
3. Once a blocking flow is found, the residual capacities are updated, and a new phase starts (new level graph).

## 7. Step-by-Step Algorithm
1. Initialize flow to 0.
2. Construct a residual graph with initial capacities.
3. Loop while BFS can reach the sink from the source (meaning a level graph can be built):
    1. Reset the `start` array used for DFS optimization (to keep track of the next edge to explore for each node, preventing redundant work).
    2. Loop while a flow `f` > 0 can be sent from the source to the sink using DFS on the level graph:
        - Add `f` to the total flow.
        - The DFS only traverses edges `(u, v)` where `level[v] == level[u] + 1` and `residual_capacity(u, v) > 0`.
4. Return the total flow.

## 8. Pseudocode
```text
function BFS(S, T):
    level[] = -1
    level[S] = 0
    Q = Queue()
    Q.push(S)
    while Q is not empty:
        u = Q.pop()
        for each edge (u, v) in residual_graph:
            if level[v] < 0 and capacity[u,v] - flow[u,v] > 0:
                level[v] = level[u] + 1
                Q.push(v)
    return level[T] != -1

function DFS(u, T, current_flow, start[]):
    if u == T:
        return current_flow
    for i from start[u] to edges[u].length:
        start[u] = i
        v = edges[u][i].to
        if level[v] == level[u] + 1 and capacity[u,v] - flow[u,v] > 0:
            pushed = DFS(v, T, min(current_flow, capacity[u,v] - flow[u,v]), start)
            if pushed > 0:
                flow[u,v] += pushed
                flow[v,u] -= pushed // Add to backward edge
                return pushed
    return 0

function Dinic(S, T):
    total_flow = 0
    while BFS(S, T):
        start[] = 0
        while (pushed = DFS(S, T, INFINITY, start)) > 0:
            total_flow += pushed
    return total_flow
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAXV 100
#define INF 1000000000

typedef struct Edge {
    int v, flow, C, rev;
} Edge;

typedef struct Vector {
    Edge* arr;
    int size;
    int capacity;
} Vector;

void push_back(Vector* vec, Edge e) {
    if (vec->size == vec->capacity) {
        vec->capacity = vec->capacity == 0 ? 2 : vec->capacity * 2;
        vec->arr = realloc(vec->arr, vec->capacity * sizeof(Edge));
    }
    vec->arr[vec->size++] = e;
}

Vector adj[MAXV];
int level[MAXV];
int ptr[MAXV];

void addEdge(int u, int v, int C) {
    Edge a = {v, 0, C, adj[v].size};
    Edge b = {u, 0, 0, adj[u].size};
    push_back(&adj[u], a);
    push_back(&adj[v], b);
}

int bfs(int s, int t) {
    memset(level, -1, sizeof(level));
    level[s] = 0;
    int q[MAXV], head = 0, tail = 0;
    q[tail++] = s;
    while (head < tail) {
        int u = q[head++];
        for (int i = 0; i < adj[u].size; i++) {
            Edge e = adj[u].arr[i];
            if (level[e.v] < 0 && e.flow < e.C) {
                level[e.v] = level[u] + 1;
                q[tail++] = e.v;
            }
        }
    }
    return level[t] >= 0;
}

int dfs(int u, int t, int pushed) {
    if (pushed == 0) return 0;
    if (u == t) return pushed;
    for (int* cid = &ptr[u]; *cid < adj[u].size; ++(*cid)) {
        int id = *cid;
        Edge* e = &adj[u].arr[id];
        int tr = e->C - e->flow;
        if (level[u] + 1 != level[e->v] || tr == 0) continue;
        int push = dfs(e->v, t, pushed < tr ? pushed : tr);
        if (push == 0) continue;
        e->flow += push;
        adj[e->v].arr[e->rev].flow -= push;
        return push;
    }
    return 0;
}

int dinic(int s, int t) {
    int flow = 0;
    while (bfs(s, t)) {
        memset(ptr, 0, sizeof(ptr));
        int pushed;
        while ((pushed = dfs(s, t, INF))) {
            flow += pushed;
        }
    }
    return flow;
}

int main() {
    int V = 4;
    for(int i=0; i<V; i++) {
        adj[i].arr = NULL;
        adj[i].size = 0;
        adj[i].capacity = 0;
    }
    addEdge(0, 1, 10);
    addEdge(0, 2, 10);
    addEdge(1, 3, 10);
    addEdge(2, 3, 10);
    printf("Max Flow: %d\n", dinic(0, 3));
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

struct Edge {
    int v, flow, C, rev;
};

class Dinic {
    int V;
    vector<int> level, ptr;
    vector<vector<Edge>> adj;

public:
    Dinic(int V) : V(V), level(V), ptr(V), adj(V) {}

    void addEdge(int u, int v, int C) {
        adj[u].push_back({v, 0, C, (int)adj[v].size()});
        adj[v].push_back({u, 0, 0, (int)adj[u].size() - 1});
    }

    bool bfs(int s, int t) {
        fill(level.begin(), level.end(), -1);
        level[s] = 0;
        queue<int> q;
        q.push(s);
        while (!q.empty()) {
            int u = q.front();
            q.pop();
            for (auto& e : adj[u]) {
                if (level[e.v] < 0 && e.flow < e.C) {
                    level[e.v] = level[u] + 1;
                    q.push(e.v);
                }
            }
        }
        return level[t] >= 0;
    }

    int dfs(int u, int t, int pushed) {
        if (pushed == 0) return 0;
        if (u == t) return pushed;
        for (int& cid = ptr[u]; cid < adj[u].size(); ++cid) {
            auto& e = adj[u][cid];
            int tr = e.C - e.flow;
            if (level[u] + 1 != level[e.v] || tr == 0) continue;
            int push = dfs(e.v, t, min(pushed, tr));
            if (push == 0) continue;
            e.flow += push;
            adj[e.v][e.rev].flow -= push;
            return push;
        }
        return 0;
    }

    int maxFlow(int s, int t) {
        int flow = 0;
        while (bfs(s, t)) {
            fill(ptr.begin(), ptr.end(), 0);
            while (int pushed = dfs(s, t, 1e9)) {
                flow += pushed;
            }
        }
        return flow;
    }
};

int main() {
    Dinic dinic(4);
    dinic.addEdge(0, 1, 10);
    dinic.addEdge(0, 2, 10);
    dinic.addEdge(1, 3, 10);
    dinic.addEdge(2, 3, 10);
    cout << "Max Flow: " << dinic.maxFlow(0, 3) << endl;
    return 0;
}
```

### Java
```java
import java.util.*;

public class Dinic {
    static class Edge {
        int v, flow, C, rev;
        public Edge(int v, int flow, int C, int rev) {
            this.v = v; this.flow = flow; this.C = C; this.rev = rev;
        }
    }

    int V;
    int[] level, ptr;
    List<List<Edge>> adj;

    public Dinic(int V) {
        this.V = V;
        level = new int[V];
        ptr = new int[V];
        adj = new ArrayList<>();
        for (int i = 0; i < V; i++) adj.add(new ArrayList<>());
    }

    public void addEdge(int u, int v, int C) {
        adj.get(u).add(new Edge(v, 0, C, adj.get(v).size()));
        adj.get(v).add(new Edge(u, 0, 0, adj.get(u).size() - 1));
    }

    boolean bfs(int s, int t) {
        Arrays.fill(level, -1);
        level[s] = 0;
        Queue<Integer> q = new LinkedList<>();
        q.add(s);
        while (!q.isEmpty()) {
            int u = q.poll();
            for (Edge e : adj.get(u)) {
                if (level[e.v] < 0 && e.flow < e.C) {
                    level[e.v] = level[u] + 1;
                    q.add(e.v);
                }
            }
        }
        return level[t] >= 0;
    }

    int dfs(int u, int t, int pushed) {
        if (pushed == 0) return 0;
        if (u == t) return pushed;
        for (; ptr[u] < adj.get(u).size(); ++ptr[u]) {
            Edge e = adj.get(u).get(ptr[u]);
            int tr = e.C - e.flow;
            if (level[u] + 1 != level[e.v] || tr == 0) continue;
            int push = dfs(e.v, t, Math.min(pushed, tr));
            if (push == 0) continue;
            e.flow += push;
            adj.get(e.v).get(e.rev).flow -= push;
            return push;
        }
        return 0;
    }

    public int maxFlow(int s, int t) {
        int flow = 0;
        while (bfs(s, t)) {
            Arrays.fill(ptr, 0);
            while (true) {
                int pushed = dfs(s, t, Integer.MAX_VALUE);
                if (pushed == 0) break;
                flow += pushed;
            }
        }
        return flow;
    }

    public static void main(String[] args) {
        Dinic dinic = new Dinic(4);
        dinic.addEdge(0, 1, 10);
        dinic.addEdge(0, 2, 10);
        dinic.addEdge(1, 3, 10);
        dinic.addEdge(2, 3, 10);
        System.out.println("Max Flow: " + dinic.maxFlow(0, 3));
    }
}
```

### Python
```python
class Edge:
    def __init__(self, v, flow, C, rev):
        self.v = v
        self.flow = flow
        self.C = C
        self.rev = rev

class Dinic:
    def __init__(self, V):
        self.V = V
        self.adj = [[] for _ in range(V)]
        self.level = []
        self.ptr = []

    def add_edge(self, u, v, C):
        self.adj[u].append(Edge(v, 0, C, len(self.adj[v])))
        self.adj[v].append(Edge(u, 0, 0, len(self.adj[u]) - 1))

    def bfs(self, s, t):
        self.level = [-1] * self.V
        self.level[s] = 0
        q = [s]
        while q:
            u = q.pop(0)
            for e in self.adj[u]:
                if self.level[e.v] < 0 and e.flow < e.C:
                    self.level[e.v] = self.level[u] + 1
                    q.append(e.v)
        return self.level[t] >= 0

    def dfs(self, u, t, pushed):
        if pushed == 0:
            return 0
        if u == t:
            return pushed
        for cid in range(self.ptr[u], len(self.adj[u])):
            self.ptr[u] = cid
            e = self.adj[u][cid]
            tr = e.C - e.flow
            if self.level[u] + 1 != self.level[e.v] or tr == 0:
                continue
            push = self.dfs(e.v, t, min(pushed, tr))
            if push == 0:
                continue
            e.flow += push
            self.adj[e.v][e.rev].flow -= push
            return push
        return 0

    def max_flow(self, s, t):
        flow = 0
        while self.bfs(s, t):
            self.ptr = [0] * self.V
            while True:
                pushed = self.dfs(s, t, float('inf'))
                if not pushed:
                    break
                flow += pushed
        return flow

if __name__ == "__main__":
    dinic = Dinic(4)
    dinic.add_edge(0, 1, 10)
    dinic.add_edge(0, 2, 10)
    dinic.add_edge(1, 3, 10)
    dinic.add_edge(2, 3, 10)
    print("Max Flow:", dinic.max_flow(0, 3))
```

### JavaScript
```javascript
class Edge {
    constructor(v, flow, C, rev) {
        this.v = v;
        this.flow = flow;
        this.C = C;
        this.rev = rev;
    }
}

class Dinic {
    constructor(V) {
        this.V = V;
        this.adj = Array.from({ length: V }, () => []);
        this.level = new Array(V).fill(-1);
        this.ptr = new Array(V).fill(0);
    }

    addEdge(u, v, C) {
        this.adj[u].push(new Edge(v, 0, C, this.adj[v].length));
        this.adj[v].push(new Edge(u, 0, 0, this.adj[u].length - 1));
    }

    bfs(s, t) {
        this.level.fill(-1);
        this.level[s] = 0;
        let q = [s];
        while (q.length > 0) {
            let u = q.shift();
            for (let e of this.adj[u]) {
                if (this.level[e.v] < 0 && e.flow < e.C) {
                    this.level[e.v] = this.level[u] + 1;
                    q.push(e.v);
                }
            }
        }
        return this.level[t] >= 0;
    }

    dfs(u, t, pushed) {
        if (pushed === 0) return 0;
        if (u === t) return pushed;
        for (; this.ptr[u] < this.adj[u].length; this.ptr[u]++) {
            let e = this.adj[u][this.ptr[u]];
            let tr = e.C - e.flow;
            if (this.level[u] + 1 !== this.level[e.v] || tr === 0) continue;
            let push = this.dfs(e.v, t, Math.min(pushed, tr));
            if (push === 0) continue;
            e.flow += push;
            this.adj[e.v][e.rev].flow -= push;
            return push;
        }
        return 0;
    }

    maxFlow(s, t) {
        let flow = 0;
        while (this.bfs(s, t)) {
            this.ptr.fill(0);
            while (true) {
                let pushed = this.dfs(s, t, Infinity);
                if (pushed === 0) break;
                flow += pushed;
            }
        }
        return flow;
    }
}

// Driver
const dinic = new Dinic(4);
dinic.addEdge(0, 1, 10);
dinic.addEdge(0, 2, 10);
dinic.addEdge(1, 3, 10);
dinic.addEdge(2, 3, 10);
console.log("Max Flow: " + dinic.maxFlow(0, 3));
```

## 10. Code Explanation
- **Data Structures:** A custom `Edge` struct/class holds the destination vertex `v`, current `flow`, capacity `C`, and the index of the reverse edge `rev`. The graph is represented as an adjacency list of these edges.
- **`addEdge` function:** When adding a directed edge `(u, v)` with capacity `C`, we also add a backward edge `(v, u)` with capacity 0. `rev` stores the index in the adjacency list to quickly access the reverse edge.
- **`bfs` function:** Explores the residual graph using Breadth-First Search to find the shortest path distances from the source. It assigns a `level` to each reachable vertex. If the sink `t` gets a level, there is still an augmenting path.
- **`dfs` function:** Uses Depth-First Search to find augmenting paths along the level graph. It strictly moves to vertices where `level[v] == level[u] + 1`. The `ptr` array is crucial—it tracks which edges of a node have already been fully explored (saturated) in the current phase, preventing repeated redundant traversals. This is called the *dead-end optimization*.
- **`maxFlow` / `dinic` function:** The main loop that iteratively builds level graphs with `bfs` and pushes flow using `dfs` until `bfs` can no longer reach the sink.

## 11. Interactive Demo
### Specification for an interactive UI demo:
- **Canvas Area:** A graphical editor where users can click to create nodes and drag to create directed edges.
- **Edge Weights:** Double-clicking an edge allows the user to set its maximum capacity.
- **Source/Sink Selection:** Radio buttons to designate one node as Source and one as Sink.
- **Step-through Controls:** "Next Step" and "Play" buttons. 
    - **Step 1:** Animate BFS. Highlight edges layer by layer and display level numbers next to nodes.
    - **Step 2:** Animate DFS (Blocking flow). Show paths being discovered in red. Once a path reaches the sink, animate the flow pushing back through the network and update the flow values on the edges (e.g., `5/10`).
- **Residual Graph Toggle:** A switch to view the underlying residual graph (showing both forward and backward edges dynamically).
- **Code Trace Panel:** A side panel highlighting the current line of code executing (BFS or DFS).

## 12. Dry Run

| Phase | BFS (Level Graph) | DFS Path | Bottleneck | Flow Update (Edge: Flow/Cap) |
|---|---|---|---|---|
| 1 | S(0), 1(1), 2(1), T(2) | S -> 1 -> T | min(10, 10) = 10 | S->1: 10/10, 1->T: 10/10 |
| 1 | S(0), 1(1), 2(1), T(2) | S -> 2 -> T | min(10, 10) = 10 | S->2: 10/10, 2->T: 10/10 |
| 2 | S(0), T unreachable | None | N/A | Total Max Flow = 20 |

## 13. Time & Space Complexity

| Metric | Complexity | Explanation |
|---|---|---|
| **Time Complexity** | $O(V^2 E)$ | In each phase, creating the level graph takes $O(E)$. Finding blocking flows using DFS takes at most $O(VE)$. The distance to the sink increases by at least 1 in each phase, so there are at most $V$ phases. Thus, $V \times (E + VE) = O(V^2 E)$. |
| **Bipartite Matching** | $O(E \sqrt{V})$ | For unit network capacities (like bipartite matching), it is extremely fast. |
| **Space Complexity** | $O(V + E)$ | Storing the graph using an adjacency list takes $O(V+E)$. The `level` and `ptr` arrays take $O(V)$. Thus, overall space is linear w.r.t the graph size. |

## 14. Advantages
- Much faster than Edmonds-Karp in practice and theory.
- Capable of handling large sparse networks efficiently.
- Uses `ptr` (start array) optimization to drastically reduce redundant DFS calls.
- Excellent performance ($O(E \sqrt{V})$) for unit-capacity networks.

## 15. Disadvantages
- More complex to implement correctly compared to Edmonds-Karp.
- Pointers/indices management (like `rev` edge) can be error-prone for beginners.
- For dense graphs or networks with very large capacities where push-relabel algorithms (like Goldberg-Tarjan $O(V^3)$) might perform better theoretically.

## 16. Applications
- **Maximum Bipartite Matching:** E.g., Matching job applicants to jobs where each applicant is qualified for a subset of jobs.
- **Circulation with Demands:** Determining if it's possible to satisfy supply and demand at various nodes in a network.
- **Minimum Cut:** By the Max-Flow Min-Cut theorem, the capacity of the minimum cut equals the max flow. Dinic's helps find the bottleneck edges to disconnect the source from the sink.
- **Image Segmentation:** Used in graph cuts for computer vision tasks.

## 17. Common Mistakes
- **Forgetting the backward edge:** In flow networks, adding an edge `u -> v` with capacity `C` MUST involve adding `v -> u` with capacity `0`.
- **Not updating the backward edge flow:** When pushing flow along `u -> v`, you must subtract the same flow from the reverse edge `v -> u`.
- **Forgetting the `ptr` / dead-end optimization:** Without keeping track of the next edge to explore in DFS, the time complexity drops back down, making it much slower and failing competitive programming time limits.
- **Rebuilding the graph:** Only the flow values change. Don't rebuild the edges; just recalculate the level graph using BFS on residual capacities.

## 18. Interview Questions
1. How does Dinic's Algorithm improve upon the Edmonds-Karp algorithm?
2. What is a Level Graph, and why is it useful?
3. Explain the purpose of the `ptr` (or `start`) array in Dinic's DFS. What happens if we omit it?
4. What is the time complexity of Dinic's Algorithm for networks with unit capacities?
5. How can you use Dinic's Algorithm to solve Maximum Bipartite Matching?
6. What is a Blocking Flow, and how does it differ from a Maximum Flow?
7. How does Dinic's handle backward edges in the residual graph?
8. In the worst case, how many phases (level graph constructions) does Dinic's algorithm perform?
9. Contrast Dinic's algorithm with the Push-Relabel algorithm.
10. If the maximum flow is extremely large, does Dinic's time complexity depend on the maximum flow value (unlike Ford-Fulkerson)?

## 19. Practice Problems
- **Easy 1:** Implementation of Max Flow on a small graph.
- **Easy 2:** Bipartite Matching using Max Flow.
- **Easy 3:** Find the bottleneck edge in a simple network.
- **Easy 4:** Check if a given flow is a blocking flow.
- **Medium 5:** Police and Thieves (Maximum flow with multiple sources and sinks).
- **Medium 6:** Download Speed (CSES Problem Set).
- **Medium 7:** Maximum number of edge-disjoint paths from S to T.
- **Medium 8:** Circulation with Demands (Reduction to Max Flow).
- **Hard 9:** Image Segmentation (Graph Cut).
- **Hard 10:** Project Selection Problem (Max Flow with negative weights / Closure problem).
- **Hard 11:** dynamically updating max flow when one edge capacity increases by 1.

## 20. Related Algorithms
- [Ford-Fulkerson Algorithm](https://en.wikipedia.org/wiki/Ford%E2%80%93Fulkerson_algorithm): The foundational method for max flow.
- [Edmonds-Karp Algorithm](https://en.wikipedia.org/wiki/Edmonds%E2%80%93Karp_algorithm): An implementation of Ford-Fulkerson using BFS, running in $O(V E^2)$.
- [Push-Relabel Algorithm (Goldberg-Tarjan)](https://en.wikipedia.org/wiki/Push%E2%80%93relabel_maximum_flow_algorithm): An alternative to augmenting paths, operating locally on nodes.
- [Hopcroft-Karp Algorithm](https://en.wikipedia.org/wiki/Hopcroft%E2%80%93Karp_algorithm): Optimized $O(E \sqrt{V})$ specifically for bipartite matching.

## 21. Summary
Dinic's Algorithm is an efficient $O(V^2 E)$ maximum flow algorithm that uses a combination of BFS to construct level graphs and DFS to find blocking flows. By restricting augmenting paths to strictly advance along the level graph and using a pointer optimization to avoid dead-ends, it significantly outperforms Edmonds-Karp. Its speed and relative ease of implementation make it the go-to max flow algorithm in competitive programming and practical applications.

## 22. Quiz
**Q1. What is the worst-case time complexity of Dinic's Algorithm on general networks?**
- A) $O(V E^2)$
- B) $O(V^2 E)$
- C) $O(E \log V)$
- D) $O(V^3)$
**Correct Answer:** B
**Explanation:** Dinic's algorithm has a worst-case time complexity of $O(V^2 E)$ because there are at most $V$ phases, and each phase takes $O(VE)$ time.

**Q2. What defines a 'Level Graph' in Dinic's algorithm?**
- A) A graph containing only edges with maximum capacity.
- B) A graph where edges only exist between vertices at distance $d$ and $d+1$ from the source.
- C) A tree generated by Depth First Search.
- D) A graph with nodes sorted topologically.
**Correct Answer:** B
**Explanation:** A level graph consists of edges `(u, v)` such that the shortest path distance from the source to `v` is exactly one greater than the distance to `u`.

**Q3. Which algorithm is used to build the Level Graph in Dinic's algorithm?**
- A) Depth-First Search (DFS)
- B) Dijkstra's Algorithm
- C) Breadth-First Search (BFS)
- D) Bellman-Ford
**Correct Answer:** C
**Explanation:** BFS finds the shortest unweighted path distances, perfect for establishing levels from the source.

**Q4. What is a blocking flow?**
- A) The absolute maximum flow of the network.
- B) A flow where every path from source to sink contains at least one saturated edge in the level graph.
- C) A flow that exceeds edge capacities.
- D) A flow that visits all vertices.
**Correct Answer:** B
**Explanation:** A blocking flow means no more augmenting paths can be found strictly within the current level graph.

**Q5. Why is the `ptr` (or start) array crucial in the DFS phase of Dinic's algorithm?**
- A) It stores the maximum capacity.
- B) It prevents the algorithm from revisiting dead-end edges, optimizing the DFS to run in $O(VE)$.
- C) It keeps track of the parent node to reconstruct the path.
- D) It sorts the edges by capacity.
**Correct Answer:** B
**Explanation:** Without it, DFS might repeatedly explore saturated or dead-end paths, degrading the time complexity.

**Q6. For unit networks (where all edge capacities are 1), what is the time complexity of Dinic's Algorithm?**
- A) $O(V^2 E)$
- B) $O(E \sqrt{V})$
- C) $O(E \log V)$
- D) $O(V + E)$
**Correct Answer:** B
**Explanation:** On networks with unit capacities, the number of phases and the time per phase are tightly bounded, yielding $O(E \sqrt{V})$, matching Hopcroft-Karp for bipartite matching.

**Q7. When does Dinic's algorithm terminate?**
- A) When DFS explores all nodes.
- B) When the total flow reaches $V$.
- C) When the BFS can no longer reach the Sink vertex.
- D) After exactly $V$ iterations.
**Correct Answer:** C
**Explanation:** If BFS cannot reach the sink, there are no more augmenting paths in the residual graph, meaning max flow has been achieved.

**Q8. In the residual graph representation, what is the initial capacity of the backward edge `(v, u)` when adding a directed edge `(u, v)` with capacity `C`?**
- A) `C`
- B) `-C`
- C) `0`
- D) Infinity
**Correct Answer:** C
**Explanation:** Initially, no flow is pushed, so the residual capacity to push flow back along `(v, u)` is 0.

**Q9. Which of the following problems can be efficiently modeled and solved using Dinic's algorithm?**
- A) Minimum Spanning Tree
- B) Shortest Path
- C) Maximum Bipartite Matching
- D) Longest Increasing Subsequence
**Correct Answer:** C
**Explanation:** Maximum Bipartite Matching can be reduced to a Max Flow problem on a unit capacity network.

**Q10. What theorem provides the foundation for proving the correctness of max flow algorithms like Dinic's?**
- A) Master Theorem
- B) Max-Flow Min-Cut Theorem
- C) Euler's Theorem
- D) Bayes' Theorem
**Correct Answer:** B
**Explanation:** The Max-Flow Min-Cut theorem states that the maximum amount of flow from the source to the sink is equal to the capacity of the minimum cut separating them.
