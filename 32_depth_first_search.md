# Depth-First Search (DFS)

## 1. Introduction

Depth-First Search (DFS) is a foundational graph traversal algorithm that explores as deep as possible along each branch before backtracking. Starting from a source node, it selects an unvisited neighbor, moves to that neighbor, and continues plunging deeper until it hits a dead end, at which point it backtracks to explore alternative paths.

Imagine exploring a complex maze. You follow a single corridor as far as it goes until you hit a dead-end wall. You then backtrack to the most recent intersection and try a different unexplored path. That is Depth-First Search.

Formally investigated by Charles Pierre Trémaux in the 19th century for solving physical mazes, DFS was later analyzed in modern computer science by Robert Tarjan and John Hopcroft for graph algorithms.

You should use DFS when analyzing graph topology, discovering connected components, finding strongly connected components, detecting cycles, or solving maze/puzzle search problems with limited memory.

## 2. Why Use This Algorithm?

DFS uses minimal memory on deep graphs and powers advanced topological algorithms.

**Benefits:**
- **Low Memory Overhead:** Requires space proportional to the maximum search depth $\mathcal{O}(h)$ rather than maximum width, making it ideal for narrow, deep trees/graphs.
- **Backtracking Framework:** Excellent for combinatorial optimization, constraint satisfaction, and puzzle solving (e.g., Sudoku, N-Queens).
- **Core Building Block:** Foundation for Topological Sort, Tarjan's SCC, Kosaraju's SCC, and Articulation Point discovery.
- **Simple Recursive Syntax:** Clean implementation using function call stacks.

**Performance:**
- **Time Complexity:** $\mathcal{O}(V + E)$ where $V$ is vertices and $E$ is edges.
- **Space Complexity:** $\mathcal{O}(V)$ worst-case call stack depth.

**When it is better than BFS:**
DFS is better when memory is limited (since stack space $\mathcal{O}(h)$ is often much smaller than BFS queue width $\mathcal{O}(w)$), or when looking for any valid path rather than specifically the shortest path.

## 3. Real-World Applications

- **Topological Sorting:** Ordering build tasks, package compilation dependencies, or course prerequisites.
- **Cycle Detection:** Detecting deadlocks in operating systems or cyclic dependencies in build systems.
- **Maze & Puzzle Solvers:** Solving Sudoku, N-Queens, and pathfinding in games.
- **Connected Components:** Identifying isolated networks or island clusters in image processing.
- **Articulations & Bridges:** Network vulnerability analysis (identifying single points of failure).

## 4. Prerequisites

Before learning DFS, you should understand:
- Graph representations (Adjacency List, Adjacency Matrix).
- Recursion and Function Call Stack mechanics.
- Explicit Stack data structure (LIFO - Last-In, First-Out).
- Visited flags and vertex state coloring (White, Gray, Black).

## 5. Visualization

```text
Graph:
    0
   / \
  1   2
 / \
3   4

DFS Traversal Order (starting at 0):
0 -> 1 -> 3 (Dead end, backtrack to 1) -> 4 (Dead end, backtrack to 0) -> 2 (Finished)

Recursive Call Stack Trace:
Call DFS(0) -> Mark 0
  Call DFS(1) -> Mark 1
    Call DFS(3) -> Mark 3 (Pop 3)
    Call DFS(4) -> Mark 4 (Pop 4)
  (Pop 1)
  Call DFS(2) -> Mark 2 (Pop 2)
(Pop 0)

Traversal Path: 0, 1, 3, 4, 2
```

## 6. How It Works

1. Initialize a `visited` boolean array initialized to `false`.
2. Call `DFS_Util(u)` starting from the source vertex `u`.
3. In `DFS_Util(u)`:
   - Mark `u` as `visited = true`.
   - Process vertex `u` (print value, record arrival time, etc.).
   - For each adjacent neighbor `v` of `u`:
     - If `visited[v]` is `false`, recursively call `DFS_Util(v)`.
4. When all neighbors of `u` are processed, function returns (backtracks to previous caller).

## 7. Step-by-Step Algorithm

### Recursive Version
1. Mark current node `u` as `visited[u] = true`.
2. Process node `u`.
3. For each neighbor `v` in `adj[u]`:
   - If `visited[v] == false`:
     - Recursively call `DFS(v)`.
4. Return (backtrack).

### Iterative Version (Using Stack)
1. Initialize an empty stack `S` and `visited` array.
2. Push `start` onto `S`.
3. While `S` is not empty:
   1. `u = S.pop()`.
   2. If `visited[u] == false`:
      - Mark `visited[u] = true`.
      - Process `u`.
      - For each neighbor `v` of `u`:
        - If `visited[v] == false`: Push `v` to `S`.

## 8. Pseudocode

```text
function DFS(graph, startVertex):
    visited = boolean array of size graph.V initialized to false
    DFSUtil(graph, startVertex, visited)

function DFSUtil(graph, u, visited):
    visited[u] = true
    process(u)

    for each neighbor v in graph.adj[u]:
        if not visited[v]:
            DFSUtil(graph, v, visited)
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>

#define MAX_VERTICES 100

struct Node {
    int vertex;
    struct Node* next;
};

struct Graph {
    int numVertices;
    struct Node** adjLists;
    bool* visited;
};

struct Node* createNode(int v) {
    struct Node* newNode = (struct Node*)malloc(sizeof(struct Node));
    newNode->vertex = v;
    newNode->next = NULL;
    return newNode;
}

struct Graph* createGraph(int vertices) {
    struct Graph* graph = (struct Graph*)malloc(sizeof(struct Graph));
    graph->numVertices = vertices;
    graph->adjLists = (struct Node**)malloc(vertices * sizeof(struct Node*));
    graph->visited = (bool*)malloc(vertices * sizeof(bool));

    for (int i = 0; i < vertices; i++) {
        graph->adjLists[i] = NULL;
        graph->visited[i] = false;
    }
    return graph;
}

void addEdge(struct Graph* graph, int src, int dest) {
    struct Node* newNode = createNode(dest);
    newNode->next = graph->adjLists[src];
    graph->adjLists[src] = newNode;

    newNode = createNode(src);
    newNode->next = graph->adjLists[dest];
    graph->adjLists[dest] = newNode;
}

void dfs(struct Graph* graph, int vertex) {
    graph->visited[vertex] = true;
    printf("%d ", vertex);

    struct Node* temp = graph->adjLists[vertex];
    while (temp) {
        int connectedVertex = temp->vertex;
        if (!graph->visited[connectedVertex]) {
            dfs(graph, connectedVertex);
        }
        temp = temp->next;
    }
}

int main() {
    struct Graph* graph = createGraph(5);
    addEdge(graph, 0, 1);
    addEdge(graph, 0, 2);
    addEdge(graph, 1, 3);
    addEdge(graph, 1, 4);

    printf("DFS Order: ");
    dfs(graph, 0);
    printf("\n");
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>

void dfsUtil(int u, const std::vector<std::vector<int>>& adj, std::vector<bool>& visited) {
    visited[u] = true;
    std::cout << u << " ";

    for (int v : adj[u]) {
        if (!visited[v]) {
            dfsUtil(v, adj, visited);
        }
    }
}

void dfs(int startVertex, const std::vector<std::vector<int>>& adj, int V) {
    std::vector<bool> visited(V, false);
    std::cout << "DFS Order: ";
    dfsUtil(startVertex, adj, visited);
    std::cout << "\n";
}

int main() {
    int V = 5;
    std::vector<std::vector<int>> adj(V);
    auto addEdge = [&](int u, int v) {
        adj[u].push_back(v);
        adj[v].push_back(u);
    };

    addEdge(0, 1);
    addEdge(0, 2);
    addEdge(1, 3);
    addEdge(1, 4);

    dfs(0, adj, V);
    return 0;
}
```

### Java
```java
import java.util.*;

public class DFSGraph {
    private static void dfsUtil(int u, List<List<Integer>> adj, boolean[] visited) {
        visited[u] = true;
        System.out.print(u + " ");

        for (int v : adj.get(u)) {
            if (!visited[v]) {
                dfsUtil(v, adj, visited);
            }
        }
    }

    public static void dfs(int startVertex, List<List<Integer>> adj, int V) {
        boolean[] visited = new boolean[V];
        System.out.print("DFS Order: ");
        dfsUtil(startVertex, adj, visited);
        System.out.println();
    }

    public static void main(String[] args) {
        int V = 5;
        List<List<Integer>> adj = new ArrayList<>();
        for (int i = 0; i < V; i++) adj.add(new ArrayList<>());

        adj.get(0).add(1); adj.get(1).add(0);
        adj.get(0).add(2); adj.get(2).add(0);
        adj.get(1).add(3); adj.get(3).add(1);
        adj.get(1).add(4); adj.get(4).add(1);

        dfs(0, adj, V);
    }
}
```

### Python
```python
def dfs_util(u: int, adj: list[list[int]], visited: list[bool], order: list[int]) -> None:
    visited[u] = True
    order.append(u)

    for v in adj[u]:
        if not visited[v]:
            dfs_util(v, adj, visited, order)

def dfs(start_vertex: int, adj: list[list[int]], v_count: int) -> list[int]:
    visited = [False] * v_count
    order = []
    dfs_util(start_vertex, adj, visited, order)
    return order

if __name__ == "__main__":
    V = 5
    adj = [[] for _ in range(V)]
    edges = [(0, 1), (0, 2), (1, 3), (1, 4)]
    for u, v in edges:
        adj[u].append(v); adj[v].append(u)

    print("DFS Order:", dfs(0, adj, V))
```

### JavaScript
```javascript
function dfsUtil(u, adj, visited, order) {
    visited[u] = true;
    order.push(u);

    for (const v of adj[u]) {
        if (!visited[v]) {
            dfsUtil(v, adj, visited, order);
        }
    }
}

function dfs(startVertex, adj, V) {
    const visited = new Array(V).fill(false);
    const order = [];
    dfsUtil(startVertex, adj, visited, order);
    return order;
}

const V = 5;
const adj = Array.from({ length: V }, () => []);
const addEdge = (u, v) => { adj[u].push(v); adj[v].push(u); };

addEdge(0, 1);
addEdge(0, 2);
addEdge(1, 3);
addEdge(1, 4);

console.log("DFS Order:", dfs(0, adj, V));
```

## 10. Code Explanation

DFS uses the call stack (or an explicit LIFO Stack) to navigate graph depth. When `dfsUtil(u)` is invoked, `u` is marked as visited. It immediately picks the first unvisited neighbor `v` and makes a recursive call to `dfsUtil(v)`. This pauses processing of node `u` and plunges deeper along branch `v`. The recursion continues down until a node with no unvisited neighbors is reached. The function call then finishes and pops off the call stack, returning control to parent node `u` to explore its next unvisited neighbor.

## 11. Interactive Demo

An interactive tree/graph drawer lets users click any node to initiate DFS traversal.

- The active node glows Red while being explored down a branch.
- Backtracking is animated with a orange shrinking arrow retracting back up to the parent junction node.
- A call-stack side panel lists active recursive stack frames in real-time.

## 12. Dry Run

**Graph:** $0 - 1$, $0 - 2$, $1 - 3$

| Step | Function Call | Current Node | Visited Array | Stack Action |
| :--- | :--- | :--- | :--- | :--- |
| 1 | `DFS(0)` | `0` | `[T, F, F, F]` | Push frame `0` |
| 2 | `DFS(1)` | `1` | `[T, T, F, F]` | Push frame `1` |
| 3 | `DFS(3)` | `3` | `[T, T, F, T]` | Push frame `3` |
| 4 | Return `DFS(3)` | `3` | `[T, T, F, T]` | Dead end, Pop frame `3` |
| 5 | Return `DFS(1)` | `1` | `[T, T, F, T]` | All neighbors visited, Pop frame `1` |
| 6 | `DFS(2)` | `2` | `[T, T, T, T]` | Push frame `2` |
| 7 | Return `DFS(2)` | `2` | `[T, T, T, T]` | Pop frame `2` |
| 8 | Return `DFS(0)` | `0` | `[T, T, T, T]` | Complete! |

## 13. Time & Space Complexity

| Metric | Complexity | Reason |
| :--- | :--- | :--- |
| **Time Complexity** | $\mathcal{O}(V + E)$ | Every vertex $V$ and edge $E$ is visited once |
| **Space Complexity** | $\mathcal{O}(V)$ | Call stack depth can reach $V$ in skewed linear graphs |

## 14. Advantages

- **Low Memory for Deep Trees:** Requires memory proportional to height $\mathcal{O}(h)$ rather than width.
- **Natural Backtracking Engine:** Ideal for constraint satisfaction (N-Queens, Sudoku).
- **Enables Graph Classification:** Distinguishes Tree Edges, Back Edges, Forward Edges, and Cross Edges.

## 15. Disadvantages

- **No Shortest Path Guarantee:** May explore a long 100-step path to a node when a 1-step direct edge exists.
- **Stack Overflow Risk:** Deep recursion can cause stack overflow exceptions if graph depth is huge.

## 16. Applications

- Topological sorting of directed acyclic graphs (DAGs).
- Detecting cycles in directed and undirected graphs.
- Finding Strongly Connected Components (Tarjan's and Kosaraju's algorithms).
- Solving Sudoku, mazes, and N-Queens puzzles.

## 17. Common Mistakes

- **Recursion Stack Overflow:** Failing to convert to an iterative stack for graphs with deep paths ($> 100,000$ vertices).
- **Confusing DFS Path with Shortest Path:** Incorrectly using DFS to calculate minimum distance in unweighted graphs.

## 18. Interview Questions

1. How do you classify edges (Tree, Back, Forward, Cross edges) during a DFS traversal?
2. How can DFS be used to detect a cycle in a directed graph?
3. How do you prevent stack overflow in a recursive DFS implementation?
4. Compare DFS call-stack space complexity against BFS queue space complexity on a balanced binary tree.

## 19. Practice Problems

**Easy:**
1. Implement recursive DFS for an adjacency list graph.
2. Count the number of connected components in an undirected graph using DFS.

**Medium:**
3. Solve the "Number of Islands" grid problem using DFS.
4. Detect a cycle in a directed graph using 3-color DFS (White, Gray, Black).

**Hard:**
5. Find all Critical Connections (Bridges) in a Network using Tarjan's DFS low-link value algorithm.

## 20. Related Algorithms

- [Breadth-First Search (BFS)](./31_breadth_first_search.md) (Level-order traversal)
- [Topological Sort](./40_topological_sort.md) (DAG ordering based on DFS finishing times)
- [Tarjan's SCC Algorithm](./41_tarjans_scc_algorithm.md) (Strongly connected components)

## 21. Summary

Depth-First Search (DFS) explores graph paths as deeply as possible before backtracking using a Stack (or recursion). Running in $\mathcal{O}(V + E)$ time and $\mathcal{O}(V)$ space, it serves as the core foundation for topological sorting, cycle detection, connected components, and backtracking solvers.

## 22. Quiz

**Question 1:** Which data structure implicitly powers recursive DFS?
- A) Queue
- B) Call Stack
- C) Heap
- D) Hash Set
- **Correct Answer:** B
- **Explanation:** Function recursion uses the runtime call stack (LIFO).

**Question 2:** What is the time complexity of DFS using an adjacency list?
- A) $\mathcal{O}(V^2)$
- B) $\mathcal{O}(V + E)$
- C) $\mathcal{O}(E \log V)$
- D) $\mathcal{O}(V \cdot E)$
- **Correct Answer:** B
- **Explanation:** Every node and edge is examined once.

**Question 3:** What type of edge in a directed graph DFS indicates the presence of a cycle?
- A) Tree Edge
- B) Back Edge (pointing to a ancestor in the current call stack)
- C) Cross Edge
- D) Forward Edge
- **Correct Answer:** B
- **Explanation:** A back edge points from a descendant to a Gray ancestor currently on the active stack.

**Question 4:** What is the worst-case space complexity of DFS call stack?
- A) $\mathcal{O}(1)$
- B) $\mathcal{O}(V)$
- C) $\mathcal{O}(E)$
- D) $\mathcal{O}(V^2)$
- **Correct Answer:** B
- **Explanation:** In a path graph ($0 \to 1 \to 2 \dots \to V-1$), stack depth reaches $V$.

**Question 5:** Which algorithm uses DFS finishing times in reverse to order DAG vertices?
- A) Dijkstra's Algorithm
- B) Topological Sort
- C) Prim's Algorithm
- D) Kruskal's Algorithm
- **Correct Answer:** B
- **Explanation:** Reversing post-order DFS finishing times yields a valid topological sort.

**Question 6:** Does DFS guarantee finding the shortest path in an unweighted graph?
- A) Yes, always
- B) No
- **Correct Answer:** B
- **Explanation:** DFS explores deep paths first and can return a longer path before finding a shorter one.

**Question 7:** In 3-color DFS state tracking, what does the color GRAY represent?
- A) Unvisited node
- B) Node currently being visited (on active recursive stack)
- C) Fully visited node (popped from stack)
- D) Isolated node
- **Correct Answer:** B
- **Explanation:** Gray nodes are currently active on the recursion call stack.

**Question 8:** How does DFS behave on a 2D grid matrix problem like "Number of Islands"?
- A) It explores 4-directional or 8-directional adjacent land cells recursively
- B) It requires sorting cells first
- C) It converts matrix to a binary tree
- D) It runs in $\mathcal{O}(1)$ time
- **Correct Answer:** A
- **Explanation:** Grid cells act as graph vertices with adjacent neighbors (Up, Down, Left, Right).

**Question 9:** Who developed the modern theoretical graph applications of DFS in the 1970s?
- A) Robert Tarjan and John Hopcroft
- B) Edsger Dijkstra
- C) Tim Peters
- D) E. F. Moore
- **Correct Answer:** A
- **Explanation:** Tarjan and Hopcroft formalized DFS for SCCs, biconnectivity, and planarity.

**Question 10:** How can you avoid Stack Overflow errors during DFS on extremely deep graphs?
- A) Increase CPU clock speed
- B) Use an explicit iterative Stack data structure on the heap instead of system call stack recursion
- C) Use an adjacency matrix
- D) Disable visited tracking
- **Correct Answer:** B
- **Explanation:** An explicit stack in heap memory avoids system stack overflow limits.
