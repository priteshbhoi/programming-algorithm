# Hopcroft-Karp Algorithm for Maximum Bipartite Matching

## 1. Introduction
The Hopcroft-Karp algorithm is an algorithm that takes a bipartite graph as input and produces a maximum cardinality matching as output — a set of as many edges as possible with the property that no two edges share an endpoint. It was discovered by John Hopcroft and Richard Karp in 1973. It improves upon the Ford-Fulkerson method (when applied to bipartite matching) and the naive alternating path algorithm by finding multiple augmenting paths simultaneously.

## 2. Why Use This Algorithm?
While simple augmenting path algorithms for bipartite matching take $O(V \cdot E)$ time, where $V$ is the number of vertices and $E$ is the number of edges, the Hopcroft-Karp algorithm optimizes this to $O(E \sqrt{V})$ time. This makes it significantly faster and the algorithm of choice for finding maximum matchings in dense or large bipartite graphs. It achieves this efficiency by using Breadth-First Search (BFS) to find a set of shortest augmenting paths simultaneously, followed by Depth-First Search (DFS) to apply them.

## 3. Real-World Applications
*   **Job Assignment:** Assigning a set of applicants to a set of available jobs where each applicant is qualified for certain jobs, aiming to maximize the number of filled positions.
*   **Dating Apps/Matchmaking:** Pairing users based on mutual compatibilities to maximize the total number of couples.
*   **Network Routing:** Assigning tasks to servers or routing paths in network topologies.
*   **Resource Allocation:** Distributing limited resources (like machines, rooms) to requests requiring specific resources.

## 4. Prerequisites
To fully understand the Hopcroft-Karp algorithm, you should be familiar with:
*   **Bipartite Graphs:** Graphs whose vertices can be divided into two disjoint and independent sets $U$ and $V$ such that every edge connects a vertex in $U$ to one in $V$.
*   **Matching:** A set of edges without common vertices.
*   **Maximum Matching:** A matching that contains the largest possible number of edges.
*   **Alternating Path:** A path that alternates between edges in the matching and edges not in the matching.
*   **Augmenting Path:** An alternating path that starts from an unmatched vertex and ends at an unmatched vertex.
*   **Graph Traversal Algorithms:** Breadth-First Search (BFS) and Depth-First Search (DFS).

## 5. Visualization

Consider a bipartite graph with sets $U = \{U1, U2, U3\}$ and $V = \{V1, V2, V3\}$.
Edges: (U1, V1), (U1, V2), (U2, V1), (U3, V2), (U3, V3)

Initial State: All vertices unmatched.

```text
U-vertices     V-vertices
   U1 ----------- V1
    | \
    |  \
   U2   --------- V2
         /
        /
   U3 ----------- V3
```

**Phase 1:**
- BFS from unmatched $U$ vertices ($U1, U2, U3$).
- Shortest augmenting paths found: $U1 \rightarrow V1$, $U2 \rightarrow V1$ (conflict), $U3 \rightarrow V2$, $U3 \rightarrow V3$.
- DFS applies non-overlapping shortest paths:
  - Add (U1, V1) to matching.
  - Add (U3, V2) to matching.
- Current Matching: `{(U1, V1), (U3, V2)}`

**Phase 2:**
- BFS from unmatched $U$ vertices ($U2$).
- Path: $U2 \rightarrow V1 \rightarrow U1 \rightarrow V2 \rightarrow U3 \rightarrow V3$ (This is an alternating path).
- Shortest augmenting path: $U2 \rightarrow V1 \rightarrow U1 \rightarrow V2 \rightarrow U3 \rightarrow V3$.
- DFS applies this path. Flips edges:
  - Remove (U1, V1), Add (U2, V1)
  - Remove (U3, V2), Add (U1, V2)
  - Add (U3, V3)
- Current Matching: `{(U2, V1), (U1, V2), (U3, V3)}`

No more unmatched $U$ vertices or paths. Max matching size = 3.

## 6. How It Works
The algorithm works in phases. Each phase consists of a BFS followed by a DFS.
1.  **BFS (Breadth-First Search):** Starts from all currently unmatched vertices in the left set $U$. It explores the graph to find the shortest augmenting paths to unmatched vertices in the right set $V$. It records the distances to ensure only shortest paths are considered.
2.  **DFS (Depth-First Search):** Uses the distance information computed by the BFS to find a maximal set of disjoint augmenting paths of that shortest length. It then augments the matching along these paths (flips matched and unmatched edges).
3.  **Termination:** The process repeats until a BFS phase cannot find any augmenting paths. The length of the shortest augmenting path strictly increases with each phase.

## 7. Step-by-Step Algorithm
1.  Initialize all vertices in $U$ and $V$ as unmatched.
2.  Initialize a distance array `dist` for vertices in $U$.
3.  **BFS Step:**
    *   Add all unmatched vertices in $U$ to a queue and set their `dist` to 0. Set `dist` of matched vertices in $U$ to infinity ($\infty$).
    *   Initialize `dist[NIL]` to $\infty$ (NIL represents an unmatched state/dummy vertex).
    *   While the queue is not empty:
        *   Dequeue a vertex $u$.
        *   If `dist[u] < dist[NIL]`:
            *   For each adjacent vertex $v$ of $u$:
                *   Let $u' = \text{pair}[v]$ (the vertex matched to $v$, or NIL if unmatched).
                *   If `dist[u'] == \infty`:
                    *   `dist[u'] = dist[u] + 1`
                    *   Enqueue $u'$
    *   If `dist[NIL] != \infty`, it means we found at least one augmenting path. Return true.
4.  **DFS Step (if BFS returned true):**
    *   For each unmatched vertex $u$ in $U$:
        *   If `dfs(u)` is true (meaning an augmenting path was successfully applied starting from $u$), increment the matching size.
    *   `dfs(u)` works as follows:
        *   If $u \neq NIL$:
            *   For each adjacent vertex $v$ of $u$:
                *   Let $u' = \text{pair}[v]$.
                *   If `dist[u'] == dist[u] + 1`:
                    *   If `dfs(u')` is true (recursive call):
                        *   Set $\text{pair}[v] = u$ and $\text{pair}[u] = v$.
                        *   Return true.
            *   `dist[u] = \infty` (Mark as visited for this DFS phase).
            *   Return false.
        *   Return true (Reached NIL).
5.  Repeat BFS and DFS steps until BFS returns false.

## 8. Pseudocode
```text
function HopcroftKarp(Graph G=(U,V,E)):
    for each u in U:
        pairU[u] = NIL
    for each v in V:
        pairV[v] = NIL
    matching_size = 0

    while BFS(U, V, E, pairU, pairV, dist):
        for each u in U:
            if pairU[u] == NIL:
                if DFS(u, U, V, E, pairU, pairV, dist):
                    matching_size = matching_size + 1

    return matching_size

function BFS(U, V, E, pairU, pairV, dist):
    queue Q = empty queue
    for each u in U:
        if pairU[u] == NIL:
            dist[u] = 0
            Q.enqueue(u)
        else:
            dist[u] = INFINITY
    dist[NIL] = INFINITY

    while Q is not empty:
        u = Q.dequeue()
        if dist[u] < dist[NIL]:
            for each v adjacent to u:
                u_next = pairV[v]
                if dist[u_next] == INFINITY:
                    dist[u_next] = dist[u] + 1
                    Q.enqueue(u_next)

    return dist[NIL] != INFINITY

function DFS(u, U, V, E, pairU, pairV, dist):
    if u != NIL:
        for each v adjacent to u:
            u_next = pairV[v]
            if dist[u_next] == dist[u] + 1:
                if DFS(u_next, U, V, E, pairU, pairV, dist):
                    pairV[v] = u
                    pairU[u] = v
                    return true
        dist[u] = INFINITY
        return false
    return true
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>

#define MAX_U 1000
#define MAX_V 1000
#define INF 1000000000

int U, V, E;
int head[MAX_U], next_edge[MAX_U * MAX_V], to[MAX_U * MAX_V], edge_cnt;
int pairU[MAX_U + 1], pairV[MAX_V + 1], dist[MAX_U + 1];

void add_edge(int u, int v) {
    to[++edge_cnt] = v;
    next_edge[edge_cnt] = head[u];
    head[u] = edge_cnt;
}

bool bfs() {
    int Q[MAX_U + 1], front = 0, rear = 0;
    for (int u = 1; u <= U; ++u) {
        if (pairU[u] == 0) {
            dist[u] = 0;
            Q[rear++] = u;
        } else {
            dist[u] = INF;
        }
    }
    dist[0] = INF;
    while (front < rear) {
        int u = Q[front++];
        if (dist[u] < dist[0]) {
            for (int e = head[u]; e; e = next_edge[e]) {
                int v = to[e];
                if (dist[pairV[v]] == INF) {
                    dist[pairV[v]] = dist[u] + 1;
                    Q[rear++] = pairV[v];
                }
            }
        }
    }
    return dist[0] != INF;
}

bool dfs(int u) {
    if (u != 0) {
        for (int e = head[u]; e; e = next_edge[e]) {
            int v = to[e];
            if (dist[pairV[v]] == dist[u] + 1) {
                if (dfs(pairV[v])) {
                    pairV[v] = u;
                    pairU[u] = v;
                    return true;
                }
            }
        }
        dist[u] = INF;
        return false;
    }
    return true;
}

int hopcroft_karp() {
    for (int i = 0; i <= U; ++i) pairU[i] = 0;
    for (int i = 0; i <= V; ++i) pairV[i] = 0;
    int matching = 0;
    while (bfs()) {
        for (int u = 1; u <= U; ++u) {
            if (pairU[u] == 0 && dfs(u)) {
                matching++;
            }
        }
    }
    return matching;
}

int main() {
    // Number of vertices in U, V, and number of edges
    U = 4; V = 4; E = 6;
    edge_cnt = 0;
    for(int i=0; i<=U; i++) head[i] = 0;

    add_edge(1, 1);
    add_edge(1, 3);
    add_edge(2, 3);
    add_edge(3, 4);
    add_edge(4, 3);
    add_edge(4, 2);

    printf("Maximum matching is %d\\n", hopcroft_karp());
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

const int INF = 1e9;

class HopcroftKarp {
    int u_nodes, v_nodes;
    vector<vector<int>> adj;
    vector<int> pairU, pairV, dist;

public:
    HopcroftKarp(int u, int v) : u_nodes(u), v_nodes(v) {
        adj.resize(u + 1);
        pairU.assign(u + 1, 0);
        pairV.assign(v + 1, 0);
        dist.assign(u + 1, 0);
    }

    void addEdge(int u, int v) {
        adj[u].push_back(v);
    }

    bool bfs() {
        queue<int> q;
        for (int u = 1; u <= u_nodes; u++) {
            if (pairU[u] == 0) {
                dist[u] = 0;
                q.push(u);
            } else {
                dist[u] = INF;
            }
        }
        dist[0] = INF;

        while (!q.empty()) {
            int u = q.front();
            q.pop();

            if (dist[u] < dist[0]) {
                for (int v : adj[u]) {
                    if (dist[pairV[v]] == INF) {
                        dist[pairV[v]] = dist[u] + 1;
                        q.push(pairV[v]);
                    }
                }
            }
        }
        return dist[0] != INF;
    }

    bool dfs(int u) {
        if (u != 0) {
            for (int v : adj[u]) {
                if (dist[pairV[v]] == dist[u] + 1) {
                    if (dfs(pairV[v])) {
                        pairV[v] = u;
                        pairU[u] = v;
                        return true;
                    }
                }
            }
            dist[u] = INF;
            return false;
        }
        return true;
    }

    int maxMatching() {
        int result = 0;
        while (bfs()) {
            for (int u = 1; u <= u_nodes; u++) {
                if (pairU[u] == 0 && dfs(u)) {
                    result++;
                }
            }
        }
        return result;
    }
};

int main() {
    HopcroftKarp hk(4, 4);
    hk.addEdge(1, 1);
    hk.addEdge(1, 3);
    hk.addEdge(2, 3);
    hk.addEdge(3, 4);
    hk.addEdge(4, 3);
    hk.addEdge(4, 2);

    cout << "Maximum matching is " << hk.maxMatching() << endl;
    return 0;
}
```

### Java
```java
import java.util.*;

public class HopcroftKarp {
    static final int INF = Integer.MAX_VALUE;
    int u_nodes, v_nodes;
    List<Integer>[] adj;
    int[] pairU, pairV, dist;

    public HopcroftKarp(int u, int v) {
        this.u_nodes = u;
        this.v_nodes = v;
        adj = new ArrayList[u + 1];
        for (int i = 0; i <= u; i++) {
            adj[i] = new ArrayList<>();
        }
        pairU = new int[u + 1];
        pairV = new int[v + 1];
        dist = new int[u + 1];
    }

    public void addEdge(int u, int v) {
        adj[u].add(v);
    }

    boolean bfs() {
        Queue<Integer> q = new LinkedList<>();
        for (int u = 1; u <= u_nodes; u++) {
            if (pairU[u] == 0) {
                dist[u] = 0;
                q.add(u);
            } else {
                dist[u] = INF;
            }
        }
        dist[0] = INF;

        while (!q.isEmpty()) {
            int u = q.poll();
            if (dist[u] < dist[0]) {
                for (int v : adj[u]) {
                    if (dist[pairV[v]] == INF) {
                        dist[pairV[v]] = dist[u] + 1;
                        q.add(pairV[v]);
                    }
                }
            }
        }
        return dist[0] != INF;
    }

    boolean dfs(int u) {
        if (u != 0) {
            for (int v : adj[u]) {
                if (dist[pairV[v]] == dist[u] + 1) {
                    if (dfs(pairV[v])) {
                        pairV[v] = u;
                        pairU[u] = v;
                        return true;
                    }
                }
            }
            dist[u] = INF;
            return false;
        }
        return true;
    }

    public int maxMatching() {
        int result = 0;
        while (bfs()) {
            for (int u = 1; u <= u_nodes; u++) {
                if (pairU[u] == 0 && dfs(u)) {
                    result++;
                }
            }
        }
        return result;
    }

    public static void main(String[] args) {
        HopcroftKarp hk = new HopcroftKarp(4, 4);
        hk.addEdge(1, 1);
        hk.addEdge(1, 3);
        hk.addEdge(2, 3);
        hk.addEdge(3, 4);
        hk.addEdge(4, 3);
        hk.addEdge(4, 2);

        System.out.println("Maximum matching is " + hk.maxMatching());
    }
}
```

### Python
```python
from collections import deque

class HopcroftKarp:
    def __init__(self, u_nodes, v_nodes):
        self.u_nodes = u_nodes
        self.v_nodes = v_nodes
        self.adj = [[] for _ in range(u_nodes + 1)]
        self.pairU = [0] * (u_nodes + 1)
        self.pairV = [0] * (v_nodes + 1)
        self.dist = [0] * (u_nodes + 1)
        self.INF = float('inf')

    def add_edge(self, u, v):
        self.adj[u].append(v)

    def bfs(self):
        queue = deque()
        for u in range(1, self.u_nodes + 1):
            if self.pairU[u] == 0:
                self.dist[u] = 0
                queue.append(u)
            else:
                self.dist[u] = self.INF
        
        self.dist[0] = self.INF

        while queue:
            u = queue.popleft()
            if self.dist[u] < self.dist[0]:
                for v in self.adj[u]:
                    if self.dist[self.pairV[v]] == self.INF:
                        self.dist[self.pairV[v]] = self.dist[u] + 1
                        queue.append(self.pairV[v])
                        
        return self.dist[0] != self.INF

    def dfs(self, u):
        if u != 0:
            for v in self.adj[u]:
                if self.dist[self.pairV[v]] == self.dist[u] + 1:
                    if self.dfs(self.pairV[v]):
                        self.pairV[v] = u
                        self.pairU[u] = v
                        return True
            self.dist[u] = self.INF
            return False
        return True

    def max_matching(self):
        result = 0
        while self.bfs():
            for u in range(1, self.u_nodes + 1):
                if self.pairU[u] == 0 and self.dfs(u):
                    result += 1
        return result

if __name__ == "__main__":
    hk = HopcroftKarp(4, 4)
    hk.add_edge(1, 1)
    hk.add_edge(1, 3)
    hk.add_edge(2, 3)
    hk.add_edge(3, 4)
    hk.add_edge(4, 3)
    hk.add_edge(4, 2)

    print("Maximum matching is", hk.max_matching())
```

### JavaScript
```javascript
class HopcroftKarp {
    constructor(u_nodes, v_nodes) {
        this.u_nodes = u_nodes;
        this.v_nodes = v_nodes;
        this.adj = Array.from({ length: u_nodes + 1 }, () => []);
        this.pairU = new Array(u_nodes + 1).fill(0);
        this.pairV = new Array(v_nodes + 1).fill(0);
        this.dist = new Array(u_nodes + 1).fill(0);
        this.INF = Infinity;
    }

    addEdge(u, v) {
        this.adj[u].push(v);
    }

    bfs() {
        let queue = [];
        for (let u = 1; u <= this.u_nodes; u++) {
            if (this.pairU[u] === 0) {
                this.dist[u] = 0;
                queue.push(u);
            } else {
                this.dist[u] = this.INF;
            }
        }
        this.dist[0] = this.INF;

        let front = 0;
        while (front < queue.length) {
            let u = queue[front++];
            if (this.dist[u] < this.dist[0]) {
                for (let v of this.adj[u]) {
                    let nextU = this.pairV[v];
                    if (this.dist[nextU] === this.INF) {
                        this.dist[nextU] = this.dist[u] + 1;
                        queue.push(nextU);
                    }
                }
            }
        }
        return this.dist[0] !== this.INF;
    }

    dfs(u) {
        if (u !== 0) {
            for (let v of this.adj[u]) {
                let nextU = this.pairV[v];
                if (this.dist[nextU] === this.dist[u] + 1) {
                    if (this.dfs(nextU)) {
                        this.pairV[v] = u;
                        this.pairU[u] = v;
                        return true;
                    }
                }
            }
            this.dist[u] = this.INF;
            return false;
        }
        return true;
    }

    maxMatching() {
        let result = 0;
        while (this.bfs()) {
            for (let u = 1; u <= this.u_nodes; u++) {
                if (this.pairU[u] === 0 && this.dfs(u)) {
                    result++;
                }
            }
        }
        return result;
    }
}

// Driver Code
const hk = new HopcroftKarp(4, 4);
hk.addEdge(1, 1);
hk.addEdge(1, 3);
hk.addEdge(2, 3);
hk.addEdge(3, 4);
hk.addEdge(4, 3);
hk.addEdge(4, 2);

console.log("Maximum matching is " + hk.maxMatching());
```

## 10. Code Explanation
The implementations use 1-based indexing for vertices, keeping `0` as the dummy `NIL` vertex.
- `pairU` and `pairV` arrays store the matching. `pairU[i] = j` means vertex $i \in U$ is matched to vertex $j \in V$.
- `bfs()` function calculates the shortest alternating paths from unmatched vertices in $U$ to unmatched vertices in $V$. It uses the `dist` array to keep track of path lengths. It sets `dist[0] = INF` and if it finds an unmatched $V$ vertex (whose pair in $U$ is `0`), it updates `dist[0]`, effectively finding an augmenting path. It returns `true` if at least one such path is found.
- `dfs(u)` function greedily follows the layered network created by the BFS distances (`dist[pairV[v]] == dist[u] + 1`) to find disjoint augmenting paths and applies them by updating `pairU` and `pairV`.
- `maxMatching()` orchestrates the algorithm: as long as `bfs()` finds augmenting paths, it calls `dfs()` for all unmatched vertices in $U$ to apply them and increment the matching size.

## 11. Interactive Demo
### Specification for an Interactive UI Demo
A web-based interactive visualization should include:
- **Canvas Area:** A visual representation of bipartite sets $U$ and $V$, with nodes arranged in two vertical columns.
- **Controls:**
  - `Add Node`: Buttons to add nodes to U or V.
  - `Add Edge`: Mechanism to drag and drop edges between nodes.
  - `Randomize`: Generates a random bipartite graph.
  - `Step`: Executes the algorithm one step at a time (switching between BFS and DFS phases).
  - `Play/Pause`: Automatically animates the steps.
- **Visual Feedback:**
  - Edges change color based on their state (e.g., gray for unconsidered, thick green for matched, red for currently exploring path).
  - Nodes change color to indicate matched/unmatched status.
  - A side panel showing the `dist` array and the queue content during BFS.
  - Text commentary explaining the current action (e.g., "BFS found shortest paths of length 3", "DFS applying path U2->V1->U1->V3").

## 12. Dry Run
Let's trace the algorithm with a small graph:
$U = \{1, 2\}$, $V = \{1, 2\}$
Edges: (1,1), (1,2), (2,1)

| Phase | Operation | State Changes & Explanations |
| :--- | :--- | :--- |
| **Initial** | Setup | `pairU=[0,0,0]`, `pairV=[0,0,0]` |
| **Phase 1** | BFS | Q: `[1, 2]`. `dist[1]=0`, `dist[2]=0`. <br> Pop 1: adj=1, 2. `pairV[1]=0`. `dist[0]=1`. `Q=[2,0]` <br> Pop 2: adj=1. `pairV[1]=0` (already in Q). <br> BFS returns True. |
| | DFS(1) | `dfs(1)`: adj=1,2. Check 1. `pairV[1]=0`. `dist[0]==dist[1]+1` (1==1) is true. `dfs(0)` returns true. <br> Update: `pairV[1]=1`, `pairU[1]=1`. Returns true. Matches=1. |
| | DFS(2) | `dfs(2)`: adj=1. Check 1. `pairV[1]=1`. `dist[1]` is 0, not `dist[2]+1`. Fails. Returns false. |
| **Phase 2** | BFS | Q: `[2]`. `dist[1]=INF`, `dist[2]=0`. <br> Pop 2: adj=1. `pairV[1]=1`. `dist[1]=1`. Q: `[1]`. <br> Pop 1: adj=1,2. `pairV[1]=1`, `pairV[2]=0`. `dist[0]=2`. Q: `[0]`. <br> BFS returns True. |
| | DFS(1) | Skipped, already matched. |
| | DFS(2) | `dfs(2)`: adj=1. `pairV[1]=1`. `dist[1]==dist[2]+1` (1==1) is true. <br> `dfs(1)`: adj=1,2. `pairV[2]=0`. `dist[0]==dist[1]+1` (2==2) is true. <br> `dfs(0)` returns true. <br> Update: `pairV[2]=1`, `pairU[1]=2`. <br> Update: `pairV[1]=2`, `pairU[2]=1`. <br> Returns true. Matches=2. |
| **Phase 3** | BFS | Q: `[]`. Queue is empty. `dist[0]=INF`. BFS returns False. |
| **End** | Terminate | Total Matching Size = 2. Matching: (1,2), (2,1). |

## 13. Time & Space Complexity

| Complexity | Bound | Explanation |
| :--- | :--- | :--- |
| **Time (Worst Case)** | $O(E \sqrt{V})$ | Each phase (BFS + DFS) takes $O(E)$ time. Hopcroft and Karp proved that the length of the shortest augmenting path increases strictly in each phase, and the number of phases is bounded by $O(\sqrt{V})$. Therefore, the overall time complexity is $O(E \sqrt{V})$. |
| **Space** | $O(V + E)$ | $O(V+E)$ for adjacency list representation of the graph. Arrays `pairU`, `pairV`, `dist`, and the BFS queue require $O(V)$ space. Total space is $O(V + E)$. |

## 14. Advantages
- **Optimal Time Complexity:** It is theoretically and practically one of the fastest algorithms for maximum bipartite matching in unweighted graphs, outperforming Ford-Fulkerson $O(V \cdot E)$.
- **Works well on sparse graphs:** Due to the efficient BFS/DFS traversal.
- **Guaranteed Bounds:** The $O(\sqrt{V})$ bound on the number of phases is strict and provides a strong performance guarantee.

## 15. Disadvantages
- **Complexity of Implementation:** It is more complex to implement and understand compared to the naive DFS-based alternating path algorithm.
- **Unweighted Only:** It is designed only for unweighted maximum cardinality matching. For maximum weight bipartite matching, algorithms like the Hungarian algorithm are required.
- **Bipartite Restriction:** Cannot be used for general graph matching (which requires algorithms like Edmonds' Blossom algorithm).

## 16. Applications
- **Job Assignment Problem:** Assigning people to tasks they are qualified for.
- **Maximum Independent Set:** In bipartite graphs, König's theorem states that the size of the maximum independent set is equal to $|V| - (\text{size of maximum matching})$.
- **Minimum Vertex Cover:** Also via König's theorem, in a bipartite graph, the size of the minimum vertex cover equals the size of the maximum matching.
- **Network Flows:** It is a special case of Dinic's algorithm for maximum flow applied to a unit-capacity network model of a bipartite graph.

## 17. Common Mistakes
- **Incorrect BFS distances:** Failing to correctly reset or update the `dist` array during the BFS phase, which leads the DFS to explore incorrect or non-shortest paths.
- **Forgetting dummy vertex (NIL):** The algorithm heavily relies on a dummy vertex (index 0) to represent the unmatched state and track distances to available slots. Mismanaging index 0 leads to out-of-bounds or infinite loops.
- **1-based vs 0-based indexing:** Graph algorithms in competitive programming often use 1-based indexing to reserve 0 for NIL. Mixing them up causes subtle bugs.

## 18. Interview Questions
1.  **How does Hopcroft-Karp differ from the Ford-Fulkerson algorithm for bipartite matching?**
    *   *Hopcroft-Karp finds multiple shortest augmenting paths in a single phase using BFS and DFS, taking $O(E \sqrt{V})$ time. Ford-Fulkerson finds one path at a time, taking $O(V \cdot E)$.*
2.  **Why does the algorithm use both BFS and DFS?**
    *   *BFS is used to find the shortest augmenting paths and create a layered network. DFS uses this layered network to efficiently find a maximal set of disjoint augmenting paths.*
3.  **What is the maximum number of phases in the Hopcroft-Karp algorithm?**
    *   *It is bounded by $2 \sqrt{V}$.*
4.  **Can Hopcroft-Karp be used for maximum weight bipartite matching?**
    *   *No, it only finds the maximum cardinality matching. The Hungarian algorithm is used for weighted matching.*
5.  **How is Hopcroft-Karp related to Dinic's Algorithm?**
    *   *It is exactly equivalent to Dinic's algorithm applied to a specialized network flow graph (with source and sink connected to $U$ and $V$).*
6.  **Explain the significance of the `dist` array.**
    *   *It ensures that DFS only explores paths of the shortest possible length, which is crucial for achieving the $O(E \sqrt{V})$ time complexity bound.*
7.  **What does a dummy vertex (NIL/0) achieve in the implementation?**
    *   *It provides a clean way to represent an unmatched node and acts as the destination in the BFS search for an augmenting path.*
8.  **How would you find the Minimum Vertex Cover in a bipartite graph after running this algorithm?**
    *   *By applying König's theorem. Run a final alternating BFS from all unmatched $U$ vertices. The vertex cover consists of all unvisited $U$ vertices and visited $V$ vertices.*
9.  **What happens if the bipartite graph is already perfectly matched?**
    *   *The very first BFS will find no unmatched vertices in $U$, return false immediately, and the algorithm terminates in $O(V)$ time.*
10. **Describe the space complexity and what components contribute to it.**
    *   *$O(V+E)$ dominated by the adjacency list to store the graph and $O(V)$ arrays for pairs and distances.*

## 19. Practice Problems
*   **Easy:**
    1.  Maximum Bipartite Matching (GeeksforGeeks basic implementation)
    2.  Check if a bipartite graph has a perfect matching.
    3.  Assigning students to available dorm rooms.
    4.  Bipartite matching in a grid (chess board style).
*   **Medium:**
    5.  SPOJ MATCHING - Fast Maximum Bipartite Matching
    6.  UVA 10080 - Gopher II (Distance based bipartite matching)
    7.  Codeforces 498C - Array and Operations
    8.  Find the minimum vertex cover in a given bipartite graph.
*   **Hard:**
    9.  UVA 12083 - Guardian of Decency
    10. Minimum Path Cover in a Directed Acyclic Graph (DAG) (Reduces to bipartite matching).
    11. Codeforces 1139E - Maximize Mex (Dynamic bipartite matching).

## 20. Related Algorithms
- **Ford-Fulkerson Algorithm:** [Ford-Fulkerson](https://en.wikipedia.org/wiki/Ford%E2%80%93Fulkerson_algorithm) (General max flow, can be used for bipartite matching).
- **Hungarian Algorithm:** [Hungarian Algorithm](https://en.wikipedia.org/wiki/Hungarian_algorithm) (Maximum weight bipartite matching).
- **Edmonds' Blossom Algorithm:** [Blossom Algorithm](https://en.wikipedia.org/wiki/Blossom_algorithm) (Maximum matching in general non-bipartite graphs).
- **Dinic's Algorithm:** [Dinic's Algorithm](https://en.wikipedia.org/wiki/Dinic%27s_algorithm) (Fast maximum flow, conceptually similar to Hopcroft-Karp).

## 21. Summary
The Hopcroft-Karp algorithm is a highly efficient algorithm for solving the maximum cardinality matching problem in bipartite graphs. By combining BFS to find shortest augmenting paths and DFS to apply them simultaneously, it drastically reduces the required iterations compared to simpler alternating path methods, achieving an optimal $O(E \sqrt{V})$ time complexity. It is an essential algorithm in combinatorial optimization and graph theory.

## 22. Quiz

**Q1. What is the time complexity of the Hopcroft-Karp algorithm?**
A) $O(V \cdot E)$
B) $O(E \sqrt{V})$
C) $O(V^3)$
D) $O(E^2)$
**Correct Answer: B**
*Explanation: Hopcroft and Karp proved that finding augmenting paths in phases of shortest lengths limits the number of phases to $O(\sqrt{V})$, each taking $O(E)$ time.*

**Q2. Which two graph traversal algorithms are utilized in each phase of Hopcroft-Karp?**
A) BFS and Dijkstra
B) DFS and Kruskal
C) BFS and DFS
D) Prim and DFS
**Correct Answer: C**
*Explanation: BFS is used to build a layered network of shortest paths, and DFS is used to find disjoint augmenting paths within that network.*

**Q3. Hopcroft-Karp algorithm is used for which specific type of problem?**
A) Maximum flow in general graphs
B) Maximum weight matching in bipartite graphs
C) Maximum cardinality matching in bipartite graphs
D) Minimum spanning tree
**Correct Answer: C**
*Explanation: It finds the maximum number of edges in a matching for a bipartite graph, but does not consider edge weights.*

**Q4. What is the role of the dummy vertex (often index 0 or NIL) in the implementation?**
A) It represents the root of the tree.
B) It represents an unmatched state for a vertex.
C) It stores the maximum matching size.
D) It acts as a visited flag.
**Correct Answer: B**
*Explanation: It is used as a convenient destination during the search for augmenting paths, indicating that an unmatched node on the opposite side has been reached.*

**Q5. By König's theorem, the size of the maximum matching in a bipartite graph is equal to:**
A) Size of maximum independent set
B) Size of minimum vertex cover
C) Size of minimum edge cover
D) Size of maximum clique
**Correct Answer: B**
*Explanation: König's theorem states that in any bipartite graph, the number of edges in a maximum matching equals the number of vertices in a minimum vertex cover.*

**Q6. What happens during the BFS phase if an augmenting path is found?**
A) The algorithm immediately terminates.
B) The distance of the dummy node `dist[NIL]` becomes finite.
C) The matching size is immediately incremented.
D) The graph is reversed.
**Correct Answer: B**
*Explanation: The BFS explores from unmatched $U$ nodes. When it hits an unmatched $V$ node (represented by its pair being NIL), it sets the distance of NIL, signaling success.*

**Q7. Why is Hopcroft-Karp faster than the simple DFS augmenting path algorithm?**
A) It uses adjacency matrices instead of lists.
B) It finds multiple augmenting paths simultaneously in one phase.
C) It randomizes the edge order.
D) It only processes half of the graph.
**Correct Answer: B**
*Explanation: The naive DFS method finds one path at a time. Hopcroft-Karp groups shortest paths and applies a maximal set of them in one go.*

**Q8. The Hopcroft-Karp algorithm is equivalent to applying which flow algorithm on a unit-capacity network?**
A) Ford-Fulkerson
B) Edmonds-Karp
C) Dinic's Algorithm
D) Push-Relabel
**Correct Answer: C**
*Explanation: Dinic's algorithm also builds layered networks with BFS and finds blocking flows with DFS, which precisely mirrors the mechanics of Hopcroft-Karp on unit networks.*

**Q9. If a bipartite graph has 100 vertices and 200 edges, what is the maximum number of BFS/DFS phases the algorithm might execute?**
A) 200
B) 100
C) Approximately 10
D) Approximately 20
**Correct Answer: D**
*Explanation: The number of phases is bounded by $2\sqrt{V}$. Here $V = 100$, so the phases are roughly bounded by $2 \times 10 = 20$.*

**Q10. Can Hopcroft-Karp be used to find a matching in a cycle graph of 5 vertices ($C_5$)?**
A) Yes, directly.
B) No, because $C_5$ is not a bipartite graph.
C) Yes, if we double the edges.
D) No, because it is an unweighted graph.
**Correct Answer: B**
*Explanation: Odd cycles are not bipartite graphs, and Hopcroft-Karp only works on bipartite graphs.*
