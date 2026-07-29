# Topological Sort

## 1. Introduction

Topological Sort (or Topological Ordering) is an algorithm that produces a linear ordering of vertices in a **Directed Acyclic Graph (DAG)** such that for every directed edge $u \to v$, vertex $u$ comes before vertex $v$ in the ordering.

Imagine a university degree curriculum. To take Advanced Algorithms (Course C), you must first complete Data Structures (Course B), which requires Intro to Programming (Course A). Topological Sort lists all courses in a valid sequence $A \to B \to C$ such that every prerequisite course is completed before any dependent course begins.

Topological sorting was first investigated in the early 1960s in the context of the PERT (Program Evaluation and Review Technique) network scheduling method for managing large industrial projects.

You should use Topological Sort whenever you need to schedule tasks, compile software dependencies, resolve build order, or evaluate spreadsheet formulas with linear prerequisites.

## 2. Why Use This Algorithm?

Topological Sort resolves linear dependency orders and identifies cyclic dependency deadlocks.

**Benefits:**
- **Valid Dependency Ordering:** Guarantees all prerequisites precede dependent items.
- **Cycle Detection:** Fails or reports an error if the graph contains a directed cycle (which would make valid ordering impossible).
- **Linear Time Complexity:** Computes valid ordering in $\mathcal{O}(V + E)$ time.
- **Multiple Approaches:** Can be implemented using DFS (Post-order Reversal) or Kahn's Algorithm (In-degree Queue).

**Performance:**
- **Time Complexity:** $\mathcal{O}(V + E)$ where $V$ is vertices and $E$ is edges.
- **Space Complexity:** $\mathcal{O}(V)$ for storing in-degrees, stack, and visited flags.

**When it is better than standard BFS/DFS:**
Topological Sort specifically solves directed prerequisite ordering problems, which standard BFS/DFS traversals cannot resolve without post-processing.

## 3. Real-World Applications

- **Build Systems & Compilers:** `make`, `npm`, `pip`, and `cargo` resolving package build and compilation order.
- **Task Scheduling (PERT/GANTT):** Project management task sequencing with dependency constraints.
- **Spreadsheet Formula Evaluation:** Re-evaluating Excel/Google Sheets cells when dependent values change.
- **Course Prerequisites:** University course scheduling.
- **Data Pipeline Orchestration:** Airflow / Apache Spark DAG task execution pipelines.

## 4. Prerequisites

Before learning Topological Sort, you should understand:
- Directed Acyclic Graphs (DAGs).
- In-degree of a vertex (number of incoming directed edges).
- [Depth-First Search (DFS)](./32_depth_first_search.md) or [Breadth-First Search (BFS)](./31_breadth_first_search.md).

## 5. Visualization

```text
Directed Acyclic Graph (DAG):
  5 ───> 0 <─── 4
  │             │
  v             v
  2 ───> 3 ───> 1

Dependencies:
- 5 -> 0, 5 -> 2
- 4 -> 0, 4 -> 1
- 2 -> 3
- 3 -> 1

Valid Topological Sort Order:
  [5, 4, 2, 3, 1, 0]

Notice: Every directed edge u -> v has u appearing BEFORE v in the list!
```

## 6. How It Works

There are two primary methods for Topological Sort:

### Method 1: DFS-Based (Post-Order Reversal)
1. Maintain a boolean `visited` array and a result `stack`.
2. For each unvisited vertex $u$:
   - Call `DFS_Util(u)`.
   - In `DFS_Util(u)`: mark `u` as visited, recursively call `DFS_Util` for all unvisited neighbors $v$, then **push $u$ onto the result stack**.
3. Pop all elements from the result stack to get the topological order.

### Method 2: Kahn's Algorithm (In-Degree Queue)
1. Compute the `inDegree` of every vertex.
2. Push all vertices with `inDegree == 0` into a `queue`.
3. While `queue` is not empty:
   - Dequeue vertex $u$, add it to `result`.
   - For each neighbor $v$ of $u$:
     - Decrement `inDegree[v]`.
     - If `inDegree[v] == 0`, push $v$ into `queue`.
4. If `result.length != V`, the graph contains a **cycle**!

## 7. Step-by-Step Algorithm (Kahn's Algorithm)

1. Compute `inDegree[i]` for all $i \in [0 \dots V-1]$.
2. Initialize `queue = Queue()`, `result = []`.
3. For `i = 0` to `V - 1`:
   - If `inDegree[i] == 0`: `queue.enqueue(i)`.
4. Loop while `queue` is not empty:
   1. `u = queue.dequeue()`.
   2. `result.append(u)`.
   3. For each neighbor `v` in `adj[u]`:
      - `inDegree[v]--`.
      - If `inDegree[v] == 0`: `queue.enqueue(v)`.
5. If `result.length == V`: Return `result`. Else: Return "Graph contains a Cycle!".

## 8. Pseudocode

```text
function TopologicalSort_Kahn(graph):
    inDegree = array of size graph.V filled with 0
    
    for u = 0 to graph.V - 1:
        for each v in graph.adj[u]:
            inDegree[v] = inDegree[v] + 1
            
    Q = Queue()
    for u = 0 to graph.V - 1:
        if inDegree[u] == 0:
            Q.enqueue(u)
            
    topoOrder = []
    
    while Q is not empty:
        u = Q.dequeue()
        topoOrder.append(u)
        
        for each neighbor v of u:
            inDegree[v] = inDegree[v] - 1
            if inDegree[v] == 0:
                Q.enqueue(v)
                
    if length(topoOrder) != graph.V:
        return "Cycle Detected! Topological Sort impossible."
        
    return topoOrder
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>

#define MAX_V 100

struct Node {
    int to;
    struct Node* next;
};

struct Graph {
    int V;
    struct Node* adj[MAX_V];
};

struct Graph* createGraph(int v) {
    struct Graph* g = (struct Graph*)malloc(sizeof(struct Graph));
    g->V = v;
    for (int i = 0; i < v; i++) g->adj[i] = NULL;
    return g;
}

void addEdge(struct Graph* g, int u, int v) {
    struct Node* newNode = (struct Node*)malloc(sizeof(struct Node));
    newNode->to = v;
    newNode->next = g->adj[u];
    g->adj[u] = newNode;
}

void topologicalSortKahn(struct Graph* g) {
    int V = g->V;
    int inDegree[MAX_V] = {0};

    for (int u = 0; u < V; u++) {
        struct Node* temp = g->adj[u];
        while (temp) {
            inDegree[temp->to]++;
            temp = temp->next;
        }
    }

    int queue[MAX_V];
    int front = 0, rear = 0;

    for (int i = 0; i < V; i++) {
        if (inDegree[i] == 0) {
            queue[rear++] = i;
        }
    }

    int topoOrder[MAX_V];
    int count = 0;

    while (front < rear) {
        int u = queue[front++];
        topoOrder[count++] = u;

        struct Node* temp = g->adj[u];
        while (temp) {
            int v = temp->to;
            inDegree[v]--;
            if (inDegree[v] == 0) {
                queue[rear++] = v;
            }
            temp = temp->next;
        }
    }

    if (count != V) {
        printf("Cycle Detected! Graph is not a DAG.\n");
        return;
    }

    printf("Topological Order: ");
    for (int i = 0; i < V; i++) printf("%d ", topoOrder[i]);
    printf("\n");
}

int main() {
    struct Graph* g = createGraph(6);
    addEdge(g, 5, 0);
    addEdge(g, 5, 2);
    addEdge(g, 4, 0);
    addEdge(g, 4, 1);
    addEdge(g, 2, 3);
    addEdge(g, 3, 1);

    topologicalSortKahn(g);
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
#include <queue>

void topologicalSortKahn(int V, const std::vector<std::vector<int>>& adj) {
    std::vector<int> inDegree(V, 0);
    for (int u = 0; u < V; u++) {
        for (int v : adj[u]) {
            inDegree[v]++;
        }
    }

    std::queue<int> q;
    for (int i = 0; i < V; i++) {
        if (inDegree[i] == 0) q.push(i);
    }

    std::vector<int> topoOrder;
    while (!q.empty()) {
        int u = q.front();
        q.pop();
        topoOrder.push_back(u);

        for (int v : adj[u]) {
            inDegree[v]--;
            if (inDegree[v] == 0) q.push(v);
        }
    }

    if (topoOrder.size() != V) {
        std::cout << "Cycle Detected! Graph is not a DAG.\n";
        return;
    }

    std::cout << "Topological Order: ";
    for (int x : topoOrder) std::cout << x << " ";
    std::cout << "\n";
}

int main() {
    int V = 6;
    std::vector<std::vector<int>> adj(V);
    auto addEdge = [&](int u, int v) { adj[u].push_back(v); };

    addEdge(5, 0);
    addEdge(5, 2);
    addEdge(4, 0);
    addEdge(4, 1);
    addEdge(2, 3);
    addEdge(3, 1);

    topologicalSortKahn(V, adj);
    return 0;
}
```

### Java
```java
import java.util.*;

public class TopologicalSortGraph {
    public static void topologicalSortKahn(int V, List<List<Integer>> adj) {
        int[] inDegree = new int[V];
        for (int u = 0; u < V; u++) {
            for (int v : adj.get(u)) {
                inDegree[v]++;
            }
        }

        Queue<Integer> q = new LinkedList<>();
        for (int i = 0; i < V; i++) {
            if (inDegree[i] == 0) q.add(i);
        }

        List<Integer> topoOrder = new ArrayList<>();
        while (!q.isEmpty()) {
            int u = q.poll();
            topoOrder.add(u);

            for (int v : adj.get(u)) {
                inDegree[v]--;
                if (inDegree[v] == 0) q.add(v);
            }
        }

        if (topoOrder.size() != V) {
            System.out.println("Cycle Detected! Graph is not a DAG.");
            return;
        }

        System.out.println("Topological Order: " + topoOrder);
    }

    public static void main(String[] args) {
        int V = 6;
        List<List<Integer>> adj = new ArrayList<>();
        for (int i = 0; i < V; i++) adj.add(new ArrayList<>());

        adj.get(5).add(0);
        adj.get(5).add(2);
        adj.get(4).add(0);
        adj.get(4).add(1);
        adj.get(2).add(3);
        adj.get(3).add(1);

        topologicalSortKahn(V, adj);
    }
}
```

### Python
```python
from collections import deque

def topological_sort_kahn(V: int, adj: list[list[int]]) -> list[int] | None:
    in_degree = [0] * V
    for u in range(V):
        for v in adj[u]:
            in_degree[v] += 1

    queue = deque([i for i in range(V) if in_degree[i] == 0])
    topo_order = []

    while queue:
        u = queue.popleft()
        topo_order.append(u)

        for v in adj[u]:
            in_degree[v] -= 1
            if in_degree[v] == 0:
                queue.append(v)

    if len(topo_order) != V:
        print("Cycle Detected! Graph is not a DAG.")
        return None

    return topo_order

if __name__ == "__main__":
    V = 6
    adj = [[] for _ in range(V)]
    edges = [(5, 0), (5, 2), (4, 0), (4, 1), (2, 3), (3, 1)]
    for u, v in edges:
        adj[u].append(v)

    print("Topological Order:", topological_sort_kahn(V, adj))
```

### JavaScript
```javascript
function topologicalSortKahn(V, adj) {
    const inDegree = new Array(V).fill(0);
    for (let u = 0; u < V; u++) {
        for (const v of adj[u]) {
            inDegree[v]++;
        }
    }

    const queue = [];
    for (let i = 0; i < V; i++) {
        if (inDegree[i] === 0) queue.push(i);
    }

    const topoOrder = [];
    while (queue.length > 0) {
        const u = queue.shift();
        topoOrder.push(u);

        for (const v of adj[u]) {
            inDegree[v]--;
            if (inDegree[v] === 0) queue.push(v);
        }
    }

    if (topoOrder.length !== V) {
        console.log("Cycle Detected! Graph is not a DAG.");
        return null;
    }

    return topoOrder;
}

const V = 6;
const adj = Array.from({ length: V }, () => []);
const addEdge = (u, v) => adj[u].push(v);

addEdge(5, 0);
addEdge(5, 2);
addEdge(4, 0);
addEdge(4, 1);
addEdge(2, 3);
addEdge(3, 1);

console.log("Topological Order:", topologicalSortKahn(V, adj));
```

## 10. Code Explanation

Topological Sort orders vertices such that all incoming prerequisite edges arrive from earlier positions in the list. In Kahn's Algorithm (in-degree queue), any node with `inDegree == 0` has no remaining prerequisites, meaning it is safe to process immediately. Adding a node $u$ to the result stream satisfies one prerequisite for all its outgoing neighbors $v$, so `inDegree[v]` is decremented. When `inDegree[v]` drops to 0, $v$ becomes eligible for processing and enters the queue. If the final ordered list contains fewer than $V$ vertices, it proves a cyclic dependency exists that prevented some in-degrees from reaching zero.

## 11. Interactive Demo

An interactive Task Dependency Planner allows users to add tasks (e.g., "Boil Water", "Add Pasta", "Serve") with prerequisite arrows.

- A "Compile Build Order" button runs Kahn's algorithm step-by-step.
- Nodes with `inDegree == 0` glow Green and enter the "Available Tasks" queue.
- Completed tasks vanish into the "Sequential Execution Stream".
- Adding a circular arrow (e.g., $A \to B \to A$) highlights the deadlock cycle in Red.

## 12. Dry Run

**Graph:** $5 \to 0$, $5 \to 2$, $2 \to 3$ ($V = 4$)

| Step | Queue Contents (`inDegree == 0`) | Dequeued `u` | In-Degree Updates | Output Array |
| :--- | :--- | :--- | :--- | :--- |
| **Init** | `[5]` (`inDegree`: `0:1, 1:0, 2:1, 3:1, 5:0`) | - | - | `[]` |
| 1 | `[5]` | `5` | Decrement `inDegree[0]` (0), `inDegree[2]` (0) | `[5]` |
| 2 | `[0, 2]` | `0` | No outgoing edges | `[5, 0]` |
| 3 | `[2]` | `2` | Decrement `inDegree[3]` (0) | `[5, 0, 2]` |
| 4 | `[3]` | `3` | Queue empty | `[5, 0, 2, 3]` |

Topological Order: **`[5, 0, 2, 3]`**.

## 13. Time & Space Complexity

| Metric | Complexity | Reason |
| :--- | :--- | :--- |
| **Time Complexity** | $\mathcal{O}(V + E)$ | Every node and edge is examined once |
| **Space Complexity** | $\mathcal{O}(V)$ | `inDegree` array, queue, and result array store $V$ elements |

## 14. Advantages

- **Guaranteed Valid Execution Schedule:** Pre-computes valid build sequences for complex dependency trees.
- **Cycle Detection:** Automatically detects impossible circular dependencies.
- **Linear Time Complexity:** Fast $\mathcal{O}(V + E)$ execution.

## 15. Disadvantages

- **DAG Required:** Cannot produce a topological ordering if the graph contains any directed cycles.
- **Non-Unique Solutions:** Multiple valid topological orderings often exist for the same graph.

## 16. Applications

- Package managers (`npm`, `pip`, `apt`, `cargo`) computing package installation order.
- Build system task scheduling (`make`, `Gradle`, `Bazel`).
- Data pipeline DAG orchestration (Apache Airflow).
- Course prerequisite verification.

## 17. Common Mistakes

- **Running on Undirected Graphs:** Attempting topological sort on undirected graphs (undirected edges act as 2-cycles).
- **Not Checking for Cycles:** Assuming the graph is a DAG without verifying `result.length == V`.

## 18. Interview Questions

1. Can a graph have more than one valid topological ordering? Give an example.
2. How do you detect a cycle using Kahn's algorithm vs DFS?
3. What is the prerequisite graph condition for Topological Sort to be valid? (Answer: Must be a Directed Acyclic Graph / DAG).

## 19. Practice Problems

**Easy:**
1. Implement Topological Sort using Kahn's algorithm.
2. Check if a course schedule is possible given prerequisite pairs (LeetCode 207).

**Medium:**
3. Return the full Course Schedule order (LeetCode 210).
4. Find all Alien Dictionary character ordering from a sorted dictionary.

**Hard:**
5. Find the longest path in a Directed Acyclic Graph (DAG) using Topological Sort.

## 20. Related Algorithms

- [Kahn's Algorithm](./51_kahns_algorithm.md) (In-degree queue implementation of Topological Sort)
- [Depth-First Search (DFS)](./32_depth_first_search.md) (Post-order stack implementation of Topological Sort)
- [Tarjan's SCC Algorithm](./41_tarjans_scc_algorithm.md) (Strongly connected components)

## 21. Summary

Topological Sort orders the vertices of a Directed Acyclic Graph (DAG) such that for every edge $u \to v$, $u$ appears before $v$. Running in $\mathcal{O}(V + E)$ time using either Kahn's Algorithm (in-degree queue) or DFS post-order stack reversal, it forms the foundation of package compilation, task scheduling, and build systems.

## 22. Quiz

**Question 1:** What type of graph is required for Topological Sort to exist?
- A) Undirected Tree
- B) Directed Acyclic Graph (DAG)
- C) Complete Graph
- D) Bipartite Graph
- **Correct Answer:** B
- **Explanation:** A Topological Sort can only exist on a Directed Acyclic Graph (DAG).

**Question 2:** What is the in-degree of a vertex?
- A) Total number of outgoing edges
- B) Total number of incoming directed edges
- C) Sum of edge weights
- D) Number of connected components
- **Correct Answer:** B
- **Explanation:** In-degree counts how many directed edges point into a vertex.

**Question 3:** What is the time complexity of Topological Sort?
- A) $\mathcal{O}(V^2)$
- B) $\mathcal{O}(V + E)$
- C) $\mathcal{O}(E \log V)$
- D) $\mathcal{O}(V^3)$
- **Correct Answer:** B
- **Explanation:** Processing all vertices and edges takes $\mathcal{O}(V + E)$ time.

**Question 4:** In Kahn's Algorithm, which vertices are pushed into the queue first?
- A) Vertices with the highest out-degree
- B) Vertices with `inDegree == 0` (no incoming prerequisites)
- C) Random vertices
- D) Leaf nodes
- **Correct Answer:** B
- **Explanation:** Nodes with in-degree 0 have no prerequisites and can be processed immediately.

**Question 5:** How does Kahn's algorithm detect a directed cycle?
- A) If the queue overflows
- B) If the final output array contains fewer than $V$ vertices
- C) If all edge weights are 0
- D) If in-degrees become negative
- **Correct Answer:** B
- **Explanation:** Nodes in a cycle never reach `inDegree == 0`, leaving output size $< V$.

**Question 6:** In DFS-based Topological Sort, how is the final ordering constructed?
- A) By reversing the DFS post-order finishing times (popping from the DFS result stack)
- B) By printing pre-order traversal
- C) By sorting edge weights
- D) By running BFS
- **Correct Answer:** A
- **Explanation:** A node is pushed to the stack after all its dependencies are visited; popping the stack reverses this to yield topological order.

**Question 7:** Can a DAG have multiple valid topological orderings?
- A) No, every DAG has exactly 1 ordering
- B) Yes, if multiple nodes have 0 in-degree simultaneously, multiple valid orderings exist
- C) Only if all weights are equal
- D) Only on trees
- **Correct Answer:** B
- **Explanation:** Multiple independent nodes with 0 in-degree can be processed in any relative order.

**Question 8:** Which real-world tool relies directly on Topological Sort?
- A) `npm` / `make` build systems
- B) Photoshop brush tool
- C) MP3 audio decoder
- D) Hard drive defragmenter
- **Correct Answer:** A
- **Explanation:** Build systems use topological sort to compile prerequisites before dependent packages.

**Question 9:** What happens if you run Topological Sort on a graph containing a cycle $A \to B \to A$?
- A) It returns a valid order
- B) It detects the cycle and fails to produce a full $V$-length ordering
- C) It loops infinitely in Java
- D) It deletes the cycle
- **Correct Answer:** B
- **Explanation:** Cyclic dependencies make valid prerequisite ordering impossible.

**Question 10:** What is the space complexity of Topological Sort?
- A) $\mathcal{O}(1)$
- B) $\mathcal{O}(V)$
- C) $\mathcal{O}(V \cdot E)$
- D) $\mathcal{O}(V^2)$
- **Correct Answer:** B
- **Explanation:** In-degree array, queue, and result storage require $\mathcal{O}(V)$ memory.
