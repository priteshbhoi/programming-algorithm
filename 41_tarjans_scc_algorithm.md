# Tarjan's Strongly Connected Components Algorithm

## 1. Introduction
Tarjan's Algorithm is a highly efficient graph theory algorithm used to find the Strongly Connected Components (SCCs) of a directed graph. A directed graph is strongly connected if there is a path between all pairs of vertices. A strongly connected component of a directed graph is a maximal strongly connected subgraph. Discovered by Robert Tarjan in 1972, this algorithm uses depth-first search (DFS) to traverse the graph and identify these components in a single pass, making it exceptionally fast and practical for large networks.

## 2. Why Use This Algorithm?
Finding Strongly Connected Components is a fundamental problem in graph theory with numerous practical applications. Tarjan's algorithm is preferred over other methods (like Kosaraju's algorithm) because it requires only a single pass of Depth-First Search (DFS). Kosaraju's algorithm requires two DFS passes and computing the transpose of the graph. This single-pass nature of Tarjan's algorithm makes it more efficient in terms of constant factors, saving memory overhead and processing time, especially in massive, dense graphs. It naturally processes components in reverse topological order, which is highly beneficial for subsequent dynamic programming or scheduling tasks.

## 3. Real-World Applications
Tarjan's algorithm is not just a theoretical construct; it has profound real-world utility:
- **Social Networks:** Identifying tight-knit communities or clusters of friends where everyone knows everyone else (directly or indirectly).
- **Web Crawling & Search Engines:** Analyzing the structure of the World Wide Web. A set of web pages that all link to each other forms an SCC.
- **Software Engineering:** Detecting circular dependencies in software modules or packages. If module A depends on B, B on C, and C on A, they form an SCC and must be built/compiled together.
- **Circuit Design:** In electronic design automation, identifying feedback loops in logic circuits.
- **Ecology & Biology:** Analyzing food webs or metabolic networks to find self-sustaining cycles of organisms or chemical reactions.

## 4. Prerequisites
Before diving into Tarjan's Algorithm, you should have a solid grasp of:
- **Graph Theory Basics:** Vertices (nodes), edges, directed graphs, and paths.
- **Depth-First Search (DFS):** The traversal technique that explores as far as possible along each branch before backtracking.
- **Stacks:** The Last-In-First-Out (LIFO) data structure used to keep track of nodes currently being explored.
- **Arrays/Hash Maps:** For tracking visited states, discovery times, and lowest reachable times.
- **Recursion:** Understanding how recursive function calls operate on the call stack.

## 5. Visualization
Imagine exploring a maze where doors only open one way. As you walk, you drop a breadcrumb with a timestamp at each room. You also carry a notepad where you write down the earliest timestamp you can reach from your current room.
If you enter a room and find you can loop back to an earlier room you've already visited, you update your notepad with that earlier timestamp.
When you finish exploring all doors from a room, you check your notepad. If the earliest timestamp you can reach is exactly the timestamp of when you first entered this room, you know you've found the "root" or "head" of a strongly connected component! Everything you explored after this room (that hasn't already been assigned a component) belongs to this room's component. You then pull all these rooms off your stack to group them together.

## 6. How It Works
Tarjan's algorithm relies on three main concepts for each node `u`:
1.  **`discovery_time[u]` (or `ids[u]`):** The step number in the DFS traversal when node `u` was first visited.
2.  **`low_link[u]` (or `low[u]`):** The smallest `discovery_time` reachable from node `u`, including itself and any node reachable through its descendants in the DFS tree.
3.  **A Stack:** Keeps track of nodes currently in the active DFS path or nodes that belong to an SCC that hasn't been completely identified yet.

**The Core Logic:**
- Start DFS from an unvisited node. Assign it a `discovery_time` and set its `low_link` equal to its `discovery_time`. Push it onto the stack.
- For each neighbor `v` of `u`:
    - If `v` is unvisited, recursively call DFS on `v`. After returning, update `low_link[u] = min(low_link[u], low_link[v])`.
    - If `v` is already visited AND is currently on the stack (meaning it's part of the current active path, hence a back-edge), update `low_link[u] = min(low_link[u], discovery_time[v])`.
- After visiting all neighbors, check if `low_link[u] == discovery_time[u]`.
- If true, node `u` is the root of an SCC. Pop nodes from the stack until `u` is popped. All popped nodes form a single Strongly Connected Component.

## 7. Step-by-Step Algorithm
1. Initialize variables: a `time` counter to 0, arrays `discovery_time` and `low_link` initialized to unvisited (e.g., -1), a boolean array `on_stack` initialized to false, and an empty `stack`.
2. Iterate through all vertices in the graph. If a vertex `v` is unvisited (i.e., `discovery_time[v] == -1`), start a DFS traversal from `v`.
3. In the DFS function for a node `u`:
    a. Increment `time`.
    b. Set `discovery_time[u] = time` and `low_link[u] = time`.
    c. Push `u` onto the `stack` and set `on_stack[u] = true`.
4. Iterate over all adjacent nodes `v` of `u`:
    a. If `v` is unvisited:
        i. Recursively call DFS on `v`.
        ii. Upon return, update `low_link[u] = min(low_link[u], low_link[v])`.
    b. Else if `v` is visited AND `on_stack[v]` is true:
        i. Update `low_link[u] = min(low_link[u], discovery_time[v])`.
5. After visiting all neighbors of `u`, check if `low_link[u] == discovery_time[u]`.
6. If the condition is met, pop nodes from the `stack` and add them to a new SCC list until node `u` is popped. Set `on_stack[popped_node] = false` for each.
7. Repeat until all nodes are visited.

## 8. Pseudocode
```text
function TarjanSCC(Graph):
    time = 0
    ids = array of size V, initialized to -1
    low = array of size V, initialized to -1
    onStack = boolean array of size V, initialized to false
    stack = empty stack
    sccs = empty list of lists

    function dfs(at):
        stack.push(at)
        onStack[at] = true
        time = time + 1
        ids[at] = time
        low[at] = time

        for to in Graph.neighbors(at):
            if ids[to] == -1: // Unvisited
                dfs(to)
                low[at] = min(low[at], low[to])
            else if onStack[to]: // Back-edge
                low[at] = min(low[at], ids[to])

        // On recursive callback, if we're at the root node of an SCC
        if ids[at] == low[at]:
            scc = empty list
            while true:
                node = stack.pop()
                onStack[node] = false
                scc.append(node)
                if node == at:
                    break
            sccs.append(scc)

    for i from 0 to V - 1:
        if ids[i] == -1:
            dfs(i)

    return sccs
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>

#define MAX_VERTICES 1000

// Graph representation
typedef struct Node {
    int vertex;
    struct Node* next;
} Node;

Node* adjList[MAX_VERTICES];
int ids[MAX_VERTICES], low[MAX_VERTICES];
bool onStack[MAX_VERTICES];
int stack[MAX_VERTICES];
int stackTop = -1;
int timer = 0;
int numVertices;

void addEdge(int src, int dest) {
    Node* newNode = (Node*)malloc(sizeof(Node));
    newNode->vertex = dest;
    newNode->next = adjList[src];
    adjList[src] = newNode;
}

int min(int a, int b) { return (a < b) ? a : b; }

void dfs(int u) {
    stack[++stackTop] = u;
    onStack[u] = true;
    ids[u] = low[u] = ++timer;

    Node* temp = adjList[u];
    while (temp != NULL) {
        int v = temp->vertex;
        if (ids[v] == -1) {
            dfs(v);
            low[u] = min(low[u], low[v]);
        } else if (onStack[v]) {
            low[u] = min(low[u], ids[v]);
        }
        temp = temp->next;
    }

    if (low[u] == ids[u]) {
        printf("SCC: ");
        int w;
        do {
            w = stack[stackTop--];
            onStack[w] = false;
            printf("%d ", w);
        } while (w != u);
        printf("\n");
    }
}

void tarjanSCC() {
    for (int i = 0; i < numVertices; i++) {
        ids[i] = -1;
        low[i] = -1;
        onStack[i] = false;
        adjList[i] = NULL;
    }
    
    // Example graph setup
    numVertices = 5;
    addEdge(1, 0);
    addEdge(0, 2);
    addEdge(2, 1);
    addEdge(0, 3);
    addEdge(3, 4);
    
    timer = 0;
    stackTop = -1;

    for (int i = 0; i < numVertices; i++) {
        if (ids[i] == -1) {
            dfs(i);
        }
    }
}

int main() {
    tarjanSCC();
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
#include <stack>
#include <algorithm>

using namespace std;

class Tarjan {
    int timer;
    vector<int> ids, low;
    vector<bool> onStack;
    stack<int> st;
    vector<vector<int>> sccs;

    void dfs(int u, const vector<vector<int>>& adj) {
        ids[u] = low[u] = ++timer;
        st.push(u);
        onStack[u] = true;

        for (int v : adj[u]) {
            if (ids[v] == -1) {
                dfs(v, adj);
                low[u] = min(low[u], low[v]);
            } else if (onStack[v]) {
                low[u] = min(low[u], ids[v]);
            }
        }

        if (low[u] == ids[u]) {
            vector<int> scc;
            while (true) {
                int w = st.top();
                st.pop();
                onStack[w] = false;
                scc.push_back(w);
                if (w == u) break;
            }
            sccs.push_back(scc);
        }
    }

public:
    vector<vector<int>> findSCCs(int n, const vector<vector<int>>& adj) {
        timer = 0;
        ids.assign(n, -1);
        low.assign(n, -1);
        onStack.assign(n, false);
        while(!st.empty()) st.pop();
        sccs.clear();

        for (int i = 0; i < n; i++) {
            if (ids[i] == -1) {
                dfs(i, adj);
            }
        }
        return sccs;
    }
};

int main() {
    int n = 5;
    vector<vector<int>> adj(n);
    adj[1].push_back(0);
    adj[0].push_back(2);
    adj[2].push_back(1);
    adj[0].push_back(3);
    adj[3].push_back(4);

    Tarjan t;
    vector<vector<int>> sccs = t.findSCCs(n, adj);

    for (const auto& scc : sccs) {
        cout << "SCC: ";
        for (int node : scc) cout << node << " ";
        cout << "\n";
    }
    return 0;
}
```

### Java
```java
import java.util.*;

public class TarjanSCC {
    private int timer;
    private int[] ids, low;
    private boolean[] onStack;
    private Deque<Integer> stack;
    private List<List<Integer>> sccs;
    
    public List<List<Integer>> findSCCs(int n, List<List<Integer>> adj) {
        timer = 0;
        ids = new int[n];
        Arrays.fill(ids, -1);
        low = new int[n];
        Arrays.fill(low, -1);
        onStack = new boolean[n];
        stack = new ArrayDeque<>();
        sccs = new ArrayList<>();
        
        for (int i = 0; i < n; i++) {
            if (ids[i] == -1) {
                dfs(i, adj);
            }
        }
        return sccs;
    }
    
    private void dfs(int u, List<List<Integer>> adj) {
        ids[u] = low[u] = ++timer;
        stack.push(u);
        onStack[u] = true;
        
        for (int v : adj.get(u)) {
            if (ids[v] == -1) {
                dfs(v, adj);
                low[u] = Math.min(low[u], low[v]);
            } else if (onStack[v]) {
                low[u] = Math.min(low[u], ids[v]);
            }
        }
        
        if (low[u] == ids[u]) {
            List<Integer> scc = new ArrayList<>();
            while (true) {
                int w = stack.pop();
                onStack[w] = false;
                scc.add(w);
                if (w == u) break;
            }
            sccs.add(scc);
        }
    }

    public static void main(String[] args) {
        int n = 5;
        List<List<Integer>> adj = new ArrayList<>();
        for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
        
        adj.get(1).add(0);
        adj.get(0).add(2);
        adj.get(2).add(1);
        adj.get(0).add(3);
        adj.get(3).add(4);
        
        TarjanSCC t = new TarjanSCC();
        List<List<Integer>> sccs = t.findSCCs(n, adj);
        
        for (List<Integer> scc : sccs) {
            System.out.println("SCC: " + scc);
        }
    }
}
```

### Python
```python
class TarjanSCC:
    def __init__(self, n):
        self.n = n
        self.adj = [[] for _ in range(n)]
        self.timer = 0
        self.ids = [-1] * n
        self.low = [-1] * n
        self.on_stack = [False] * n
        self.stack = []
        self.sccs = []

    def add_edge(self, u, v):
        self.adj[u].append(v)

    def dfs(self, u):
        self.timer += 1
        self.ids[u] = self.low[u] = self.timer
        self.stack.append(u)
        self.on_stack[u] = True

        for v in self.adj[u]:
            if self.ids[v] == -1:
                self.dfs(v)
                self.low[u] = min(self.low[u], self.low[v])
            elif self.on_stack[v]:
                self.low[u] = min(self.low[u], self.ids[v])

        if self.low[u] == self.ids[u]:
            scc = []
            while True:
                w = self.stack.pop()
                self.on_stack[w] = False
                scc.append(w)
                if w == u:
                    break
            self.sccs.append(scc)

    def find_sccs(self):
        for i in range(self.n):
            if self.ids[i] == -1:
                self.dfs(i)
        return self.sccs

if __name__ == "__main__":
    t = TarjanSCC(5)
    t.add_edge(1, 0)
    t.add_edge(0, 2)
    t.add_edge(2, 1)
    t.add_edge(0, 3)
    t.add_edge(3, 4)

    sccs = t.find_sccs()
    for scc in sccs:
        print("SCC:", scc)
```

### JavaScript
```javascript
class TarjanSCC {
    constructor(n) {
        this.n = n;
        this.adj = Array.from({ length: n }, () => []);
        this.timer = 0;
        this.ids = new Array(n).fill(-1);
        this.low = new Array(n).fill(-1);
        this.onStack = new Array(n).fill(false);
        this.stack = [];
        this.sccs = [];
    }

    addEdge(u, v) {
        this.adj[u].push(v);
    }

    dfs(u) {
        this.timer++;
        this.ids[u] = this.low[u] = this.timer;
        this.stack.push(u);
        this.onStack[u] = true;

        for (let v of this.adj[u]) {
            if (this.ids[v] === -1) {
                this.dfs(v);
                this.low[u] = Math.min(this.low[u], this.low[v]);
            } else if (this.onStack[v]) {
                this.low[u] = Math.min(this.low[u], this.ids[v]);
            }
        }

        if (this.low[u] === this.ids[u]) {
            let scc = [];
            while (true) {
                let w = this.stack.pop();
                this.onStack[w] = false;
                scc.push(w);
                if (w === u) break;
            }
            this.sccs.push(scc);
        }
    }

    findSCCs() {
        for (let i = 0; i < this.n; i++) {
            if (this.ids[i] === -1) {
                this.dfs(i);
            }
        }
        return this.sccs;
    }
}

// Example usage
const t = new TarjanSCC(5);
t.addEdge(1, 0);
t.addEdge(0, 2);
t.addEdge(2, 1);
t.addEdge(0, 3);
t.addEdge(3, 4);

const sccs = t.findSCCs();
sccs.forEach(scc => console.log("SCC:", scc));
```

## 10. Code Explanation
The implementation follows a classic DFS template but adds tracking variables. Let's break down the key parts:
- **Initialization:** We initialize `ids`, `low`, and `onStack` arrays to track visited state and back-edges.
- **The DFS function (`dfs(u)`):** 
  - Upon entering node `u`, we increment a global `timer` and assign this value to both `ids[u]` and `low[u]`.
  - We push `u` onto a stack, marking it as `onStack`.
  - We iterate through all neighbors `v`.
    - If `v` hasn't been visited (`ids[v] == -1`), we recursively call `dfs(v)`. When that returns, it means we've explored everything reachable from `v`. If `v` can reach an earlier node, `u` can too! So, `low[u] = min(low[u], low[v])`.
    - If `v` is already visited AND is still on the stack, it means `v` is an ancestor of `u` in the current DFS tree. We found a cycle (a back-edge)! We update `low[u] = min(low[u], ids[v])`.
- **SCC Identification:** After visiting all neighbors, we check `if (low[u] == ids[u])`. If this is true, node `u` is the entry point (root) of an SCC. Everything above `u` on the stack belongs to this SCC. We pop nodes until we pop `u`, forming one component.

## 11. Interactive Demo
*(Imagine an interactive web interface here)*
1. **Draw Nodes:** Click to create nodes A, B, C, D on a canvas.
2. **Add Edges:** Drag to create directed edges: A->B, B->C, C->A, C->D.
3. **Run Tarjan's:** Click "Start Animation".
   - The system highlights A, assigns ids=1, low=1, pushes A.
   - Moves to B (ids=2, low=2, pushes B).
   - Moves to C (ids=3, low=3, pushes C).
   - C checks A (visited, on stack). C's low becomes min(3, 1) = 1.
   - C checks D (unvisited). Moves to D (ids=4, low=4, pushes D). D has no neighbors. D's low==ids (4==4). Pops D. **SCC {D} found.**
   - Back to C. C finishes. C's low != C's ids (1 != 3). Returns.
   - Back to B. B's low = min(2, C's low 1) = 1. B finishes. B's low != B's ids (1 != 2). Returns.
   - Back to A. A's low = min(1, B's low 1) = 1. A finishes. A's low == A's ids (1 == 1). Pops C, B, A. **SCC {C, B, A} found.**
4. **Result:** The graph highlights two clusters: {A,B,C} and {D}.

## 12. Dry Run
Let's manually trace the algorithm on a small graph: Nodes 0, 1, 2, 3. Edges: 0->1, 1->2, 2->0, 2->3.
- `timer = 0`, stack = `[]`
- Start at node 0.
  - `ids[0] = 1`, `low[0] = 1`, stack = `[0]`.
  - Neighbor 1: unvisited. Call dfs(1).
    - `ids[1] = 2`, `low[1] = 2`, stack = `[0, 1]`.
    - Neighbor 2: unvisited. Call dfs(2).
      - `ids[2] = 3`, `low[2] = 3`, stack = `[0, 1, 2]`.
      - Neighbor 0: visited, on stack! Update `low[2] = min(3, ids[0]=1) = 1`.
      - Neighbor 3: unvisited. Call dfs(3).
        - `ids[3] = 4`, `low[3] = 4`, stack = `[0, 1, 2, 3]`.
        - No neighbors. `low[3] == ids[3]` (4 == 4). Pop 3. **SCC: {3}**.
        - Return to dfs(2).
      - `low[2] = min(low[2], low[3]) = min(1, 4) = 1`.
      - Check `low[2] == ids[2]` (1 == 3)? False.
      - Return to dfs(1).
    - `low[1] = min(low[1], low[2]) = min(2, 1) = 1`.
    - Check `low[1] == ids[1]` (1 == 2)? False.
    - Return to dfs(0).
  - `low[0] = min(low[0], low[1]) = min(1, 1) = 1`.
  - Check `low[0] == ids[0]` (1 == 1)? True! Pop until 0. Stack gives 2, 1, 0. **SCC: {2, 1, 0}**.
- Graph is fully traversed. Final SCCs: `{3}` and `{2, 1, 0}`.

## 13. Time & Space Complexity
- **Time Complexity: O(V + E)**
  Where V is the number of vertices and E is the number of edges. The algorithm uses a standard Depth-First Search. Each node is visited exactly once, and each edge is examined at most once. Pushing and popping nodes from the stack takes O(1) time per operation, and each node is pushed and popped exactly once. Thus, the time is strictly linear.
- **Space Complexity: O(V)**
  We require several data structures: `ids` array of size V, `low` array of size V, `onStack` boolean array of size V, and the recursive call stack which can go up to depth V in the worst case (a skewed graph). The explicit stack also holds up to V elements. Total auxiliary space is linearly proportional to the number of vertices.

## 14. Advantages
1. **Single Pass:** Unlike Kosaraju's algorithm which requires two DFS passes and a graph transposition, Tarjan's algorithm completes in a single DFS pass.
2. **Efficiency:** Highly efficient in practice due to lower constant factors (less overhead in traversing the graph multiple times).
3. **No Graph Transpose:** It does not require building a reversed graph, saving memory and time overhead.
4. **Topological Sort:** The SCCs are naturally discovered in reverse topological order of the SCC condensation graph.

## 15. Disadvantages
1. **Recursive Depth:** Because it relies heavily on recursive DFS, it can cause a stack overflow error on very large graphs with deep paths if not implemented iteratively.
2. **Slightly Complex Logic:** The use of `low-link` values and tracking whether nodes are currently in the active recursion stack can be slightly harder to grasp initially compared to the two-pass Kosaraju's algorithm.

## 16. Applications
- **2-SAT Problem Solvers:** A classic Boolean satisfiability problem can be reduced to finding SCCs in an implication graph. If a variable and its negation are in the same SCC, the formula is unsatisfiable.
- **Cycle Detection:** Checking if there is any cycle in a directed graph. If the number of SCCs equals the number of vertices, it's a Directed Acyclic Graph (DAG) with no cycles.
- **Compiler Optimization:** Grouping mutually recursive functions or variables together for better register allocation and optimization.
- **Network Analysis:** Identifying resilient clusters of servers or routers in networking topologies.

## 17. Common Mistakes
1. **Using `low[v]` instead of `ids[v]` for back-edges:** When updating the low-link for a back-edge (node already on stack), you MUST use `min(low[u], ids[v])`, not `min(low[u], low[v])`. Using `low[v]` can mistakenly drag the low-link value outside the current SCC if `v` has connections to an already fully processed SCC, breaking the logic.
2. **Forgetting to check `onStack`:** A visited node is only a back-edge if it is currently in the stack (part of the active search path). If it's visited but not on the stack, it belongs to an already formed SCC (a cross-edge), and we should ignore it.
3. **Not looping through all nodes initially:** The graph might be disconnected. You must have an outer loop iterating over all vertices and calling DFS if `ids[i] == -1`.

## 18. Interview Questions
1. How does Tarjan's algorithm differ from Kosaraju's algorithm?
2. Explain the significance of the `low-link` value. How is it calculated?
3. Why do we need an explicit stack in Tarjan's algorithm when DFS already uses the call stack?
4. What happens if we update back-edges using `low[v]` instead of `ids[v]`?
5. How can Tarjan's algorithm be used to solve the 2-SAT problem?
6. Is Tarjan's algorithm stable? Does the order of finding SCCs matter?
7. In what order does Tarjan's algorithm output the Strongly Connected Components?
8. Write Tarjan's algorithm iteratively to avoid stack overflow on massive graphs.
9. How can you modify Tarjan's to find articulation points or bridges in an undirected graph?
10. Given an SCC condensation graph (where each SCC is contracted to a single node), what are the properties of this new graph?

## 19. Practice Problems
- **Easy 1:** Find if a directed graph has a cycle.
- **Easy 2:** Count the number of Strongly Connected Components in a graph.
- **Easy 3:** Print all nodes belonging to the largest SCC.
- **Easy 4:** Determine if a graph is Strongly Connected (only 1 SCC exists).
- **Medium 5:** Implement Tarjan's algorithm iteratively.
- **Medium 6:** Solve the 2-SAT problem using Tarjan's SCC algorithm.
- **Medium 7:** Find the "Capital" city in a kingdom (a node that can reach all other nodes, considering one-way roads).
- **Medium 8:** Critical Connections in a Network (Finding Bridges - related concept).
- **Hard 9:** Given a directed graph, what is the minimum number of edges to add to make it Strongly Connected?
- **Hard 10:** Dominator Tree construction using components.
- **Hard 11:** Check if a tournament graph has a unique Hamiltonian path using SCC condensation.

## 20. Related Algorithms
- **Kosaraju's Algorithm:** The alternative two-pass algorithm for finding SCCs. Easier to understand but slightly less efficient in practice due to the overhead of building a transpose graph.
- **Tarjan's Bridge-Finding Algorithm:** A modification of the same low-link concept used on undirected graphs to find bridges (edges whose removal disconnects the graph).
- **Tarjan's Articulation Point Algorithm:** Finds cut vertices in an undirected graph using discovery times and low-link values.
- **Gabow's Algorithm:** Another single-pass algorithm for SCCs that uses two stacks instead of low-link values, often considered a variation of Tarjan's.

## 21. Summary
Tarjan's Strongly Connected Components algorithm is a powerful, elegant, single-pass DFS based approach. By smartly tracking node discovery times and the lowest reachable discovery time (`low-link`), along with maintaining an active stack of nodes, it efficiently identifies maximal cyclic subgraphs in $O(V + E)$ time. Understanding the nuances of back-edges versus cross-edges and why we use `ids[v]` for back-edge updates is key to mastering this algorithm. It forms the backbone of many advanced graph processing tasks in computer science.

## 22. Quiz
**Question 1:** What is the primary data structure used implicitly and explicitly in Tarjan's Algorithm?
A) Queue
B) Stack
C) Priority Queue
D) Hash Map
*Correct Answer: B*
*Explanation: DFS uses the call stack implicitly, and an explicit stack is maintained to group nodes that belong to the same SCC.*

**Question 2:** In Tarjan's algorithm, what does the condition `low[u] == ids[u]` signify?
A) Node `u` is a leaf node.
B) Node `u` is an articulation point.
C) Node `u` is the root (entry point) of a Strongly Connected Component.
D) Node `u` is part of an acyclic graph.
*Correct Answer: C*
*Explanation: When a node's lowest reachable time is its own discovery time, it means there is no path from this node back to any earlier node. Thus, it is the root of an SCC.*

**Question 3:** What is the time complexity of Tarjan's Algorithm for a graph with V vertices and E edges?
A) O(V^2)
B) O(V log E)
C) O(V * E)
D) O(V + E)
*Correct Answer: D*
*Explanation: The algorithm processes each vertex and each directed edge exactly once during the Depth-First Search.*

**Question 4:** How does Tarjan's algorithm handle a "back-edge" to a node `v` that is already on the stack?
A) `low[u] = min(low[u], low[v])`
B) `low[u] = min(low[u], ids[v])`
C) `ids[u] = min(ids[u], low[v])`
D) `low[u] = ids[v]`
*Correct Answer: B*
*Explanation: For a back-edge to a node currently on the stack, we use its discovery time (`ids[v]`) to update the current node's low-link. Using `low[v]` might incorporate a time from a different SCC entirely.*

**Question 5:** Which algorithm requires reversing the edges of the graph to find SCCs?
A) Tarjan's Algorithm
B) Kosaraju's Algorithm
C) Dijkstra's Algorithm
D) Kruskal's Algorithm
*Correct Answer: B*
*Explanation: Kosaraju's algorithm is a two-pass algorithm that requires computing the transpose (reversed) graph for its second pass.*

**Question 6:** In what order are the Strongly Connected Components typically discovered and generated by Tarjan's algorithm?
A) Topological Order
B) Reverse Topological Order
C) Random Order
D) Alphabetical Order
*Correct Answer: B*
*Explanation: Because it uses post-order DFS logic to pop nodes from the stack, it outputs SCCs in reverse topological order of the SCC condensation graph.*

**Question 7:** If every node in a directed graph is an SCC by itself, what does this imply about the graph?
A) The graph is fully connected.
B) The graph is a tree.
C) The graph is a Directed Acyclic Graph (DAG).
D) The graph is undirected.
*Correct Answer: C*
*Explanation: If there are no cycles, no two nodes can reach each other mutually. Hence, every single node forms its own SCC.*

**Question 8:** Why do we need the boolean array `onStack` in Tarjan's algorithm?
A) To check if a node has been visited.
B) To check if an edge is a forward edge.
C) To differentiate between back-edges (node is in current active path) and cross-edges (node belongs to an already found SCC).
D) To limit recursion depth.
*Correct Answer: C*
*Explanation: If a node is visited but NOT on the stack, it belongs to an SCC that has already been completely identified and popped. We must ignore it. If it IS on the stack, it's a valid back-edge.*

**Question 9:** Can Tarjan's SCC algorithm be applied to an undirected graph?
A) Yes, without any modifications.
B) Yes, but it will just find connected components, though bridge-finding modifications exist.
C) No, it will crash.
D) No, it requires weights.
*Correct Answer: B*
*Explanation: In an undirected graph, an SCC is simply a connected component. The concepts of discovery time and low-link are, however, famously adapted by Tarjan to find bridges and articulation points in undirected graphs.*

**Question 10:** What happens to the nodes on the explicit stack when an SCC root is identified?
A) They are pushed into another stack.
B) They are sorted by discovery time.
C) All nodes are popped from the stack until the root node is popped, forming the SCC.
D) Only the root node is popped.
*Correct Answer: C*
*Explanation: Since the root was the first node of the SCC to be pushed, all nodes pushed after it that haven't been popped yet belong to its SCC. We pop until the root itself is extracted.*
