# Breadth-First Search (BFS)

## 1. Introduction

Breadth-First Search (BFS) is a fundamental graph traversal algorithm that explores nodes level by level starting from a given source vertex. It visits all immediate neighbors of the current vertex before moving on to their unvisited neighbors, exploring the graph in expanding concentric waves.

Imagine dropping a pebble into a calm pond. Ripples expand outward evenly in concentric circles from the point of impact. That is how BFS explores a graph: first level 0 (the source), then level 1 (direct neighbors), then level 2 (neighbors of neighbors), and so on.

BFS was first invented by E. F. Moore in 1959 to find the shortest path through a maze, and independently rediscovered by C. Y. Lee in 1961 for routing wiring on printed circuit boards.

You should use BFS whenever you need to find the shortest path in an unweighted graph, explore all reachable nodes level-by-level, or analyze network connectivity.

## 2. Why Use This Algorithm?

BFS guarantees finding the shortest path (in terms of number of edges) in any unweighted graph.

**Benefits:**
- **Guaranteed Shortest Path on Unweighted Graphs:** Always finds the path with the minimum number of edges from the source to any target node.
- **Level-Order Traversal:** Processes nodes in increasing order of distance from the source.
- **Complete:** Will always find a goal node if one exists in a finite graph.
- **Optimal for Uniform Edge Weights:** Eliminates the need for complex priority queue management when edge weights are uniform (e.g., 1).

**Performance:**
- **Time Complexity:** $\mathcal{O}(V + E)$ where $V$ is the number of vertices and $E$ is the number of edges.
- **Space Complexity:** $\mathcal{O}(V)$ to store the queue, visited array, and parent tracking array.

**When it is better than DFS:**
BFS is superior to DFS when searching for targets close to the source, or when finding the shortest path on unweighted graphs. DFS can get trapped deep down an infinite or long branch.

## 3. Real-World Applications

- **GPS & Navigation:** Finding the fewest-step flight or transit connection between cities.
- **Social Networks:** Finding degree of separation (e.g., LinkedIn 1st, 2nd, 3rd connections or Facebook friends-of-friends).
- **Web Crawlers:** Exploring web pages level by level up to a maximum link depth.
- **Network Broadcasting:** Packet flooding in peer-to-peer networks and router discovery protocols.
- **Garbage Collection:** Cheney's copying garbage collector algorithm uses BFS to traverse reachable objects.

## 4. Prerequisites

Before studying BFS, you should understand:
- Graph representations (Adjacency List, Adjacency Matrix).
- Queue data structure (First-In, First-Out / FIFO operations: `enqueue`, `dequeue`).
- Boolean arrays / Hash sets for tracking visited nodes.

## 5. Visualization

```text
Graph:
    0
   / \
  1   2
 / \   \
3   4   5

BFS Order starting from Node 0:
Level 0: [0]
Level 1: [1, 2]
Level 2: [3, 4, 5]

Queue Traversal Trace:
1. Enqueue 0         -> Queue: [0]
2. Dequeue 0; Push 1, 2 -> Queue: [1, 2]       -> Visited: 0
3. Dequeue 1; Push 3, 4 -> Queue: [2, 3, 4]    -> Visited: 0, 1
4. Dequeue 2; Push 5    -> Queue: [3, 4, 5]    -> Visited: 0, 1, 2
5. Dequeue 3            -> Queue: [4, 5]       -> Visited: 0, 1, 2, 3
6. Dequeue 4            -> Queue: [5]          -> Visited: 0, 1, 2, 3, 4
7. Dequeue 5            -> Queue: []           -> Visited: 0, 1, 2, 3, 4, 5
```

## 6. How It Works

1. Initialize a `queue` and a boolean `visited` array initialized to `false`.
2. Mark the `source` vertex as `visited = true` and push it into `queue`.
3. While `queue` is not empty:
   - Dequeue front vertex `u`.
   - For each unvisited neighbor `v` of `u`:
     - Mark `v` as `visited = true`.
     - Set `parent[v] = u` and `dist[v] = dist[u] + 1` (optional for path tracking).
     - Push `v` into `queue`.
4. Repeat until the queue becomes empty.

## 7. Step-by-Step Algorithm

1. `queue = empty queue`, `visited = boolean array of size V initialized to false`.
2. `visited[start] = true`, `queue.enqueue(start)`.
3. Loop while `queue` is not empty:
   1. `u = queue.dequeue()`.
   2. Process `u` (print, search check, etc.).
   3. For each neighbor `v` in `adj[u]`:
      - If `visited[v] == false`:
        - `visited[v] = true`.
        - `queue.enqueue(v)`.
4. Return visited order / distances.

## 8. Pseudocode

```text
function BFS(graph, startVertex):
    create a queue Q
    create a boolean array visited of size graph.V filled with false
    create an array dist of size graph.V filled with infinity

    visited[startVertex] = true
    dist[startVertex] = 0
    Q.enqueue(startVertex)

    while Q is not empty:
        u = Q.dequeue()
        
        for each neighbor v of u in graph.adj[u]:
            if not visited[v]:
                visited[v] = true
                dist[v] = dist[u] + 1
                Q.enqueue(v)
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
    for (int i = 0; i < vertices; i++) graph->adjLists[i] = NULL;
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

void bfs(struct Graph* graph, int startVertex) {
    bool visited[MAX_VERTICES] = {false};
    int queue[MAX_VERTICES];
    int front = 0, rear = 0;

    visited[startVertex] = true;
    queue[rear++] = startVertex;

    printf("BFS Traversal: ");
    while (front < rear) {
        int currentVertex = queue[front++];
        printf("%d ", currentVertex);

        struct Node* temp = graph->adjLists[currentVertex];
        while (temp) {
            int adjVertex = temp->vertex;
            if (!visited[adjVertex]) {
                visited[adjVertex] = true;
                queue[rear++] = adjVertex;
            }
            temp = temp->next;
        }
    }
    printf("\n");
}

int main() {
    struct Graph* graph = createGraph(6);
    addEdge(graph, 0, 1);
    addEdge(graph, 0, 2);
    addEdge(graph, 1, 3);
    addEdge(graph, 1, 4);
    addEdge(graph, 2, 5);

    bfs(graph, 0);
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
#include <queue>

void bfs(int startVertex, const std::vector<std::vector<int>>& adj, int V) {
    std::vector<bool> visited(V, false);
    std::vector<int> dist(V, -1);
    std::queue<int> q;

    visited[startVertex] = true;
    dist[startVertex] = 0;
    q.push(startVertex);

    std::cout << "BFS Order: ";
    while (!q.empty()) {
        int u = q.front();
        q.pop();
        std::cout << u << " ";

        for (int v : adj[u]) {
            if (!visited[v]) {
                visited[v] = true;
                dist[v] = dist[u] + 1;
                q.push(v);
            }
        }
    }
    std::cout << "\n";
}

int main() {
    int V = 6;
    std::vector<std::vector<int>> adj(V);
    auto addEdge = [&](int u, int v) {
        adj[u].push_back(v);
        adj[v].push_back(u);
    };

    addEdge(0, 1);
    addEdge(0, 2);
    addEdge(1, 3);
    addEdge(1, 4);
    addEdge(2, 5);

    bfs(0, adj, V);
    return 0;
}
```

### Java
```java
import java.util.*;

public class BFSGraph {
    public static void bfs(int startVertex, List<List<Integer>> adj, int V) {
        boolean[] visited = new boolean[V];
        int[] dist = new int[V];
        Arrays.fill(dist, -1);
        Queue<Integer> queue = new LinkedList<>();

        visited[startVertex] = true;
        dist[startVertex] = 0;
        queue.add(startVertex);

        System.out.print("BFS Order: ");
        while (!queue.isEmpty()) {
            int u = queue.poll();
            System.out.print(u + " ");

            for (int v : adj.get(u)) {
                if (!visited[v]) {
                    visited[v] = true;
                    dist[v] = dist[u] + 1;
                    queue.add(v);
                }
            }
        }
        System.out.println();
    }

    public static void main(String[] args) {
        int V = 6;
        List<List<Integer>> adj = new ArrayList<>();
        for (int i = 0; i < V; i++) adj.add(new ArrayList<>());

        adj.get(0).add(1); adj.get(1).add(0);
        adj.get(0).add(2); adj.get(2).add(0);
        adj.get(1).add(3); adj.get(3).add(1);
        adj.get(1).add(4); adj.get(4).add(1);
        adj.get(2).add(5); adj.get(5).add(2);

        bfs(0, adj, V);
    }
}
```

### Python
```python
from collections import deque

def bfs(start_vertex: int, adj: list[list[int]], v_count: int) -> list[int]:
    visited = [False] * v_count
    dist = [-1] * v_count
    queue = deque([start_vertex])
    
    visited[start_vertex] = True
    dist[start_vertex] = 0
    order = []

    while queue:
        u = queue.popleft()
        order.append(u)

        for v in adj[u]:
            if not visited[v]:
                visited[v] = True
                dist[v] = dist[u] + 1
                queue.append(v)

    return order

if __name__ == "__main__":
    V = 6
    adj = [[] for _ in range(V)]
    edges = [(0, 1), (0, 2), (1, 3), (1, 4), (2, 5)]
    for u, v in edges:
        adj[u].append(v)
        adj[v].append(u)

    print("BFS Order:", bfs(0, adj, V))
```

### JavaScript
```javascript
function bfs(startVertex, adj, V) {
    const visited = new Array(V).fill(false);
    const dist = new Array(V).fill(-1);
    const queue = [startVertex];

    visited[startVertex] = true;
    dist[startVertex] = 0;
    const order = [];

    while (queue.length > 0) {
        const u = queue.shift();
        order.push(u);

        for (const v of adj[u]) {
            if (!visited[v]) {
                visited[v] = true;
                dist[v] = dist[u] + 1;
                queue.push(v);
            }
        }
    }

    return order;
}

const V = 6;
const adj = Array.from({ length: V }, () => []);
const addEdge = (u, v) => { adj[u].push(v); adj[v].push(u); };

addEdge(0, 1);
addEdge(0, 2);
addEdge(1, 3);
addEdge(1, 4);
addEdge(2, 5);

console.log("BFS Order:", bfs(0, adj, V));
```

## 10. Code Explanation

BFS uses a Queue (FIFO) to enforce level-by-level traversal. When node `startVertex` is enqueued, it is marked as `visited`. In each loop iteration, the node `u` at the front of the queue is dequeued and processed. All unvisited adjacent neighbors `v` of `u` are marked `visited`, assigned a distance `dist[u] + 1`, and pushed to the back of the queue. Because FIFO processes all distance $k$ nodes before any distance $k+1$ nodes, the first time a node is reached, it is via the shortest path.

## 11. Interactive Demo

An interactive grid maze generator allows users to set Source (Green) and Destination (Red) nodes.

- A "Step BFS" button animates expanding blue frontier waves expanding 1 unit outwards every tick.
- Once the destination is reached, the algorithm highlights the shortest path in gold.
- A stats counter displays Total Nodes Visited vs Path Length.

## 12. Dry Run

**Graph:** $0 - 1$, $0 - 2$, $1 - 3$

| Step | Current Node `u` | Queue Contents | Visited Array | Dist Array |
| :--- | :--- | :--- | :--- | :--- |
| **Init** | - | `[0]` | `[T, F, F, F]` | `[0, -1, -1, -1]` |
| 1 | `0` | Dequeue `0`, Enqueue `1, 2` -> `[1, 2]` | `[T, T, T, F]` | `[0, 1, 1, -1]` |
| 2 | `1` | Dequeue `1`, Enqueue `3` -> `[2, 3]` | `[T, T, T, T]` | `[0, 1, 1, 2]` |
| 3 | `2` | Dequeue `2`, no unvisited neighbors -> `[3]` | `[T, T, T, T]` | `[0, 1, 1, 2]` |
| 4 | `3` | Dequeue `3`, Queue empty -> `[]` | `[T, T, T, T]` | `[0, 1, 1, 2]` |

## 13. Time & Space Complexity

| Metric | Complexity | Reason |
| :--- | :--- | :--- |
| **Time Complexity** | $\mathcal{O}(V + E)$ | Every vertex $V$ is enqueued once, every edge $E$ is examined once |
| **Space Complexity** | $\mathcal{O}(V)$ | Queue and visited array hold at most $V$ elements |

## 14. Advantages

- **Guaranteed Shortest Path:** Finds the minimum edge path in unweighted graphs.
- **Level-Order Processing:** Naturally groups nodes by distance level.
- **Optimal for Simple Graphs:** Simple FIFO queue logic with low overhead.

## 15. Disadvantages

- **High Memory Overhead:** Stores all nodes of the current level in memory ($\mathcal{O}(V)$ space), which can be massive in wide graphs.
- **Unweighted Only:** Does not compute shortest paths correctly on weighted graphs (use Dijkstra's instead).

## 16. Applications

- Shortest path in unweighted graphs / 2D grid mazes.
- Peer-to-peer network discovery.
- Bipartite graph testing (2-coloring).
- Finding connected components.

## 17. Common Mistakes

- **Marking Visited on Dequeue instead of Enqueue:** Leads to duplicate node entries in the queue and exponential time complexity.
- **Using Stack instead of Queue:** Accidentally turns the traversal into Depth-First Search (DFS).
- **Forgetting Disconnected Components:** Not wrapping BFS in an outer loop over all vertices for disconnected graphs.

## 18. Interview Questions

1. Why must a node be marked as visited *when it is enqueued* rather than when it is dequeued?
2. How can BFS be used to detect cycles in an undirected graph?
3. How do you find the shortest path between two cells in a 2D grid using BFS?
4. What is the difference between BFS and Dijkstra's algorithm?

## 19. Practice Problems

**Easy:**
1. Implement BFS for an adjacency list representation.
2. Find the minimum distance from source to all nodes in an unweighted graph.

**Medium:**
3. Solve the "Word Ladder" problem using BFS.
4. Check if an undirected graph is bipartite using BFS.

**Hard:**
5. Implement Bidirectional BFS to find the shortest path between two nodes in a large graph.

## 20. Related Algorithms

- [Depth-First Search (DFS)](./32_depth_first_search.md) (Depth-oriented traversal)
- [Dijkstra's Algorithm](./33_dijkstras_algorithm.md) (Shortest path for weighted graphs)
- [A* Search](./36_a_star_search.md) (Heuristic-guided shortest path search)

## 21. Summary

Breadth-First Search (BFS) explores a graph level by level using a Queue (FIFO). It guarantees finding the shortest path (minimum edge count) from a source node in any unweighted graph with $\mathcal{O}(V + E)$ time and $\mathcal{O}(V)$ space complexity.

## 22. Quiz

**Question 1:** Which data structure is used to implement BFS?
- A) Stack
- B) Queue
- C) Priority Queue
- D) Hash Map
- **Correct Answer:** B
- **Explanation:** A FIFO Queue ensures level-order traversal.

**Question 2:** What is the time complexity of BFS on a graph with $V$ vertices and $E$ edges using an adjacency list?
- A) $\mathcal{O}(V^2)$
- B) $\mathcal{O}(E \log V)$
- C) $\mathcal{O}(V + E)$
- D) $\mathcal{O}(V \cdot E)$
- **Correct Answer:** C
- **Explanation:** Each vertex is enqueued once and each edge is inspected once.

**Question 3:** What property does BFS guarantee on unweighted graphs?
- A) Minimum cost spanning tree
- B) Shortest path from source in terms of number of edges
- C) Maximum flow
- D) Topological order
- **Correct Answer:** B
- **Explanation:** Level-order exploration guarantees the first time a node is reached is via the shortest edge path.

**Question 4:** When should a vertex be marked as visited in BFS?
- A) Before starting the loop
- B) As soon as it is enqueued into the queue
- C) When it is dequeued from the queue
- D) After all its neighbors are visited
- **Correct Answer:** B
- **Explanation:** Marking visited upon enqueue prevents duplicate node entries in the queue.

**Question 5:** What is the space complexity of BFS?
- A) $\mathcal{O}(1)$
- B) $\mathcal{O}(\log V)$
- C) $\mathcal{O}(V)$
- D) $\mathcal{O}(V^2)$
- **Correct Answer:** C
- **Explanation:** The queue and visited storage require at most $\mathcal{O}(V)$ memory.

**Question 6:** Can standard BFS be used to find shortest paths on graphs with non-negative edge weights?
- A) Yes, always
- B) No, standard BFS assumes all edge weights are equal (unweighted)
- C) Only if the graph has no cycles
- D) Only on directed graphs
- **Correct Answer:** B
- **Explanation:** Weighted graphs require priority-queue algorithms like Dijkstra's.

**Question 7:** What graph representation results in $\mathcal{O}(V^2)$ BFS time complexity?
- A) Adjacency List
- B) Adjacency Matrix
- C) Edge List
- D) Incidence Matrix
- **Correct Answer:** B
- **Explanation:** Checking neighbors for each vertex in an Adjacency Matrix takes $\mathcal{O}(V)$ time, leading to $\mathcal{O}(V^2)$ total.

**Question 8:** How does Bidirectional BFS improve search performance?
- A) Reduces space to $\mathcal{O}(1)$
- B) Runs two simultaneous BFS searches from source and target, meeting in the middle to reduce search space from $\mathcal{O}(b^d)$ to $\mathcal{O}(b^{d/2})$
- C) Eliminates the need for a queue
- D) Sorts edge weights
- **Correct Answer:** B
- **Explanation:** Meeting in the middle exponentially reduces expanded frontier states.

**Question 9:** Can BFS detect cycles in an undirected graph?
- A) No, only DFS can
- B) Yes, if an adjacent vertex is already visited and is not the parent of the current vertex
- C) Only if the graph is a tree
- D) Only using recursion
- **Correct Answer:** B
- **Explanation:** Encountering an already visited non-parent neighbor indicates a cycle.

**Question 10:** Who first invented BFS in 1959?
- A) Edsger Dijkstra
- B) E. F. Moore
- C) Robert Tarjan
- D) John von Neumann
- **Correct Answer:** B
- **Explanation:** E. F. Moore invented BFS to find paths through mazes.
