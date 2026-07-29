# Kahn's Algorithm

## 1. Introduction
Topological sorting for Directed Acyclic Graphs (DAGs) is a linear ordering of vertices such that for every directed edge *uv*, vertex *u* comes before *v* in the ordering. It has numerous applications in scheduling, data processing, and task management. Kahn's Algorithm is one of the most popular and intuitive methods to achieve this topological ordering. Discovered by Arthur B. Kahn in 1962, this algorithm relies on the simple concept of in-degrees to systematically determine the order of nodes.

## 2. What is Kahn's Algorithm?
Kahn's Algorithm is an iterative algorithm used to find a topological sort of a Directed Acyclic Graph (DAG). The fundamental idea is to repeatedly remove nodes that have zero in-degree (i.e., nodes that have no incoming edges or dependencies) and then update the in-degrees of their neighboring nodes. If a neighboring node's in-degree drops to zero, it is then added to the pool of nodes ready to be processed. This process continues until all nodes are processed or until no more nodes with zero in-degree can be found (which indicates the presence of a cycle).

## 3. How Does It Work?
The algorithm maintains a data structure (typically a queue) to store all nodes with an in-degree of 0.
1. **Calculate In-degrees:** First, traverse the graph and calculate the in-degree for every node. The in-degree of a node is the number of directed edges pointing to it.
2. **Initialize Queue:** Enqueue all nodes that have an in-degree of 0.
3. **Process Nodes:** While the queue is not empty:
   - Dequeue a node *u*.
   - Append *u* to the topological sort result.
   - For every outgoing edge from *u* to a neighbor *v*, conceptually "remove" this edge by decrementing the in-degree of *v* by 1.
   - If the in-degree of *v* becomes 0, enqueue *v*.
4. **Cycle Detection:** After the loop, if the number of nodes in the topological sort result is equal to the total number of nodes in the graph, the graph is a DAG and the sort is complete. Otherwise, the graph contains at least one cycle, and topological sorting is not possible.

## 4. Real-world Applications
* **Task Scheduling:** When compiling code, some files depend on others. Build tools like `make`, `Maven`, or `npm` use topological sorting to determine the order in which files must be compiled.
* **Course Prerequisites:** In academic planning, determining a valid sequence of courses to take where some courses require others to be completed first.
* **Data Processing Pipelines:** ETL (Extract, Transform, Load) pipelines where data transformations have strict dependencies.
* **Package Management:** Software package managers use this to resolve dependency graphs before installing packages.
* **Spreadsheet Evaluation:** Determining the order of cells to evaluate in a spreadsheet where cells contain formulas referencing other cells.

## 5. Prerequisites
To fully understand Kahn's Algorithm, one should be familiar with:
* **Graph Representation:** Adjacency list and adjacency matrix representations of graphs.
* **Graph Theory Basics:** Concepts like vertices (nodes), edges, directed graphs, acyclic graphs, and paths.
* **In-degree and Out-degree:** Understanding how to count incoming and outgoing connections for a node.
* **Queue Data Structure:** How a First-In-First-Out (FIFO) queue operates, as it's the core structure used in the algorithm.

## 6. Intuition
Imagine a set of tasks you need to complete, some of which depend on others. Obviously, you can only start a task if all its prerequisites have been completed. In graph terms, a task with no prerequisites is a node with an in-degree of 0. Kahn's Algorithm simply states: find any task with no pending prerequisites, do it, and then mark its dependents as having one less prerequisite to wait for. Keep doing this until all tasks are done. If you get stuck and there are still tasks left, it means there's a circular dependency (a cycle), and you can never finish.

## 7. Step-by-Step Execution
Let's trace the algorithm on a small DAG.
Graph edges: (0 -> 1), (0 -> 2), (1 -> 3), (2 -> 3)
Vertices: 0, 1, 2, 3

**Initialization:**
- In-degrees:
  Node 0: 0
  Node 1: 1 (from 0)
  Node 2: 1 (from 0)
  Node 3: 2 (from 1, 2)
- Queue: [0] (since Node 0 has in-degree 0)
- Result: []

**Iteration 1:**
- Dequeue 0. Queue is now [].
- Result: [0]
- Neighbors of 0 are 1 and 2.
  - Decrement in-degree of 1 -> becomes 0. Enqueue 1.
  - Decrement in-degree of 2 -> becomes 0. Enqueue 2.
- Queue: [1, 2]

**Iteration 2:**
- Dequeue 1. Queue is now [2].
- Result: [0, 1]
- Neighbors of 1 is 3.
  - Decrement in-degree of 3 -> becomes 1. (Do not enqueue).

**Iteration 3:**
- Dequeue 2. Queue is now [].
- Result: [0, 1, 2]
- Neighbors of 2 is 3.
  - Decrement in-degree of 3 -> becomes 0. Enqueue 3.
- Queue: [3]

**Iteration 4:**
- Dequeue 3. Queue is now [].
- Result: [0, 1, 2, 3]
- Neighbors of 3: none.

**End:**
Result has 4 elements, matching total vertices. Valid sort: [0, 1, 2, 3].

## 8. Pseudocode
```text
function KahnTopologicalSort(Graph, numVertices):
    in_degree = array of size numVertices, initialized to 0
    
    // Step 1: Calculate in-degrees
    for each u from 0 to numVertices - 1:
        for each neighbor v of u:
            in_degree[v] = in_degree[v] + 1
            
    // Step 2: Initialize queue with 0 in-degree nodes
    queue = empty Queue
    for i from 0 to numVertices - 1:
        if in_degree[i] == 0:
            queue.enqueue(i)
            
    // Step 3: Process the nodes
    count = 0
    top_order = empty list
    
    while not queue.isEmpty():
        u = queue.dequeue()
        top_order.append(u)
        
        for each neighbor v of u:
            in_degree[v] = in_degree[v] - 1
            if in_degree[v] == 0:
                queue.enqueue(v)
                
        count = count + 1
        
    // Step 4: Check for cycles
    if count != numVertices:
        return "Cycle detected! Topological sort not possible."
    else:
        return top_order
```

## 9. Time Complexity Analysis
The time complexity of Kahn's Algorithm is **O(V + E)**, where V is the number of vertices and E is the number of edges.
* Calculating the in-degrees requires traversing all adjacency lists, which takes O(V + E) time.
* Finding all vertices with 0 in-degree takes O(V) time.
* In the while loop, each vertex is enqueued and dequeued exactly once, taking O(V) time.
* The inner loop iterates over the outgoing edges of each dequeued vertex. Over the entire execution, the inner loop runs exactly E times (once for each edge), taking O(E) time.
Therefore, the overall time complexity is strictly linear with respect to the size of the graph: O(V + E).

## 10. Space Complexity Analysis
The space complexity is **O(V + E)** or **O(V)** depending on what we include.
* **Graph representation:** The adjacency list requires O(V + E) space.
* **In-degree array:** The array to store in-degrees requires O(V) space.
* **Queue:** The queue can contain at most V vertices at a time, requiring O(V) space.
* **Output list:** The list to store the final topological sort requires O(V) space.
If the graph representation is provided and we only count auxiliary space, the space complexity is **O(V)**. Including the graph representation, it is **O(V + E)**.

## 11. Advantages
* **Simplicity and Intuition:** Kahn's Algorithm perfectly models real-world dependency resolution, making it very intuitive to understand and implement.
* **Cycle Detection:** It naturally detects cycles in a directed graph. If the output doesn't contain all vertices, a cycle exists. This is often an easier way to detect cycles than using Depth-First Search (DFS) with recursion stack tracking.
* **Iterative Approach:** Since it uses a queue instead of recursion, it avoids potential stack overflow issues that can occur with deep DFS-based topological sorting on very large graphs.
* **Lexicographical Sorting:** By replacing the standard queue with a priority queue (min-heap), Kahn's Algorithm can easily be modified to return the lexicographically smallest topological sort when multiple valid sorts exist.

## 12. Disadvantages
* **State Maintenance:** It requires explicit state management (an in-degree array and a queue), whereas DFS-based approaches can sometimes rely solely on the call stack and visited arrays.
* **Multiple Valid Sorts:** Like any topological sort, it only produces one of many possible valid orderings. Without a priority queue, the exact ordering depends on the order nodes are added to the queue, which is an implementation detail.

## 13. Edge Cases
* **Disconnected Graphs:** The algorithm handles disconnected DAGs flawlessly. Nodes from different components with an in-degree of 0 will be enqueued and processed correctly.
* **Empty Graph:** If the graph has 0 vertices, the algorithm safely returns an empty list.
* **Single Node Graph:** A graph with 1 vertex and 0 edges will correctly return just that node.
* **Graph with Cycles:** The most critical edge case. The algorithm will process nodes until no nodes with an in-degree of 0 remain. At this point, the queue becomes empty. If the total number of processed nodes is less than the total vertices, a cycle is correctly detected.

## 14. C Implementation
```c
#include <stdio.h>
#include <stdlib.h>

#define MAX_VERTICES 100

typedef struct Node {
    int vertex;
    struct Node* next;
} Node;

typedef struct Graph {
    int numVertices;
    Node** adjLists;
    int* inDegree;
} Graph;

Node* createNode(int v) {
    Node* newNode = (Node*)malloc(sizeof(Node));
    newNode->vertex = v;
    newNode->next = NULL;
    return newNode;
}

Graph* createGraph(int vertices) {
    Graph* graph = (Graph*)malloc(sizeof(Graph));
    graph->numVertices = vertices;
    graph->adjLists = (Node**)malloc(vertices * sizeof(Node*));
    graph->inDegree = (int*)calloc(vertices, sizeof(int));

    for (int i = 0; i < vertices; i++) {
        graph->adjLists[i] = NULL;
    }
    return graph;
}

void addEdge(Graph* graph, int src, int dest) {
    Node* newNode = createNode(dest);
    newNode->next = graph->adjLists[src];
    graph->adjLists[src] = newNode;
    graph->inDegree[dest]++;
}

void kahnTopologicalSort(Graph* graph) {
    int queue[MAX_VERTICES];
    int front = 0, rear = 0;
    int count = 0;
    int topOrder[MAX_VERTICES];

    // Enqueue nodes with in-degree 0
    for (int i = 0; i < graph->numVertices; i++) {
        if (graph->inDegree[i] == 0) {
            queue[rear++] = i;
        }
    }

    while (front < rear) {
        int u = queue[front++];
        topOrder[count++] = u;

        Node* temp = graph->adjLists[u];
        while (temp != NULL) {
            int v = temp->vertex;
            graph->inDegree[v]--;
            if (graph->inDegree[v] == 0) {
                queue[rear++] = v;
            }
            temp = temp->next;
        }
    }

    if (count != graph->numVertices) {
        printf("Graph contains a cycle. Topological sort not possible.\n");
    } else {
        printf("Topological Sort (C): ");
        for (int i = 0; i < count; i++) {
            printf("%d ", topOrder[i]);
        }
        printf("\n");
    }
}

int main() {
    int V = 6;
    Graph* graph = createGraph(V);
    
    addEdge(graph, 5, 2);
    addEdge(graph, 5, 0);
    addEdge(graph, 4, 0);
    addEdge(graph, 4, 1);
    addEdge(graph, 2, 3);
    addEdge(graph, 3, 1);

    kahnTopologicalSort(graph);

    return 0;
}
```

## 15. C++ Implementation
```cpp
#include <iostream>
#include <vector>
#include <queue>

using namespace std;

class Graph {
    int V;
    vector<vector<int>> adj;

public:
    Graph(int V) {
        this->V = V;
        adj.resize(V);
    }

    void addEdge(int u, int v) {
        adj[u].push_back(v);
    }

    void kahnTopologicalSort() {
        vector<int> inDegree(V, 0);

        for (int i = 0; i < V; i++) {
            for (auto neighbor : adj[i]) {
                inDegree[neighbor]++;
            }
        }

        queue<int> q;
        for (int i = 0; i < V; i++) {
            if (inDegree[i] == 0) {
                q.push(i);
            }
        }

        int count = 0;
        vector<int> topOrder;

        while (!q.empty()) {
            int u = q.front();
            q.pop();
            topOrder.push_back(u);

            for (auto v : adj[u]) {
                if (--inDegree[v] == 0) {
                    q.push(v);
                }
            }
            count++;
        }

        if (count != V) {
            cout << "Graph contains a cycle. Topological sort not possible.\n";
            return;
        }

        cout << "Topological Sort (C++): ";
        for (int i = 0; i < topOrder.size(); i++) {
            cout << topOrder[i] << " ";
        }
        cout << endl;
    }
};

int main() {
    Graph g(6);
    g.addEdge(5, 2);
    g.addEdge(5, 0);
    g.addEdge(4, 0);
    g.addEdge(4, 1);
    g.addEdge(2, 3);
    g.addEdge(3, 1);

    g.kahnTopologicalSort();

    return 0;
}
```

## 16. Java Implementation
```java
import java.util.*;

public class KahnAlgorithm {
    static class Graph {
        int V;
        List<List<Integer>> adj;

        Graph(int V) {
            this.V = V;
            adj = new ArrayList<>(V);
            for (int i = 0; i < V; i++) {
                adj.add(new ArrayList<>());
            }
        }

        public void addEdge(int u, int v) {
            adj.get(u).add(v);
        }

        public void kahnTopologicalSort() {
            int[] inDegree = new int[V];

            for (int i = 0; i < V; i++) {
                for (int node : adj.get(i)) {
                    inDegree[node]++;
                }
            }

            Queue<Integer> q = new LinkedList<>();
            for (int i = 0; i < V; i++) {
                if (inDegree[i] == 0) {
                    q.add(i);
                }
            }

            int count = 0;
            List<Integer> topOrder = new ArrayList<>();

            while (!q.isEmpty()) {
                int u = q.poll();
                topOrder.add(u);

                for (int node : adj.get(u)) {
                    if (--inDegree[node] == 0) {
                        q.add(node);
                    }
                }
                count++;
            }

            if (count != V) {
                System.out.println("Graph contains a cycle. Topological sort not possible.");
                return;
            }

            System.out.print("Topological Sort (Java): ");
            for (int i : topOrder) {
                System.out.print(i + " ");
            }
            System.out.println();
        }
    }

    public static void main(String args[]) {
        Graph g = new Graph(6);
        g.addEdge(5, 2);
        g.addEdge(5, 0);
        g.addEdge(4, 0);
        g.addEdge(4, 1);
        g.addEdge(2, 3);
        g.addEdge(3, 1);
        
        g.kahnTopologicalSort();
    }
}
```

## 17. Python Implementation
```python
from collections import deque, defaultdict

class Graph:
    def __init__(self, vertices):
        self.graph = defaultdict(list)
        self.V = vertices

    def addEdge(self, u, v):
        self.graph[u].append(v)

    def kahnTopologicalSort(self):
        in_degree = [0] * self.V
        
        for i in self.graph:
            for j in self.graph[i]:
                in_degree[j] += 1

        queue = deque()
        for i in range(self.V):
            if in_degree[i] == 0:
                queue.append(i)

        count = 0
        top_order = []

        while queue:
            u = queue.popleft()
            top_order.append(u)

            for i in self.graph[u]:
                in_degree[i] -= 1
                if in_degree[i] == 0:
                    queue.append(i)
            
            count += 1

        if count != self.V:
            print("Graph contains a cycle. Topological sort not possible.")
        else:
            print("Topological Sort (Python):", " ".join(map(str, top_order)))

if __name__ == '__main__':
    g = Graph(6)
    g.addEdge(5, 2)
    g.addEdge(5, 0)
    g.addEdge(4, 0)
    g.addEdge(4, 1)
    g.addEdge(2, 3)
    g.addEdge(3, 1)

    g.kahnTopologicalSort()
```

## 18. JavaScript Implementation
```javascript
class Graph {
    constructor(vertices) {
        this.V = vertices;
        this.adj = new Map();
        for (let i = 0; i < vertices; i++) {
            this.adj.set(i, []);
        }
    }

    addEdge(u, v) {
        this.adj.get(u).push(v);
    }

    kahnTopologicalSort() {
        let inDegree = new Array(this.V).fill(0);

        for (let [u, neighbors] of this.adj.entries()) {
            for (let v of neighbors) {
                inDegree[v]++;
            }
        }

        let queue = [];
        for (let i = 0; i < this.V; i++) {
            if (inDegree[i] === 0) {
                queue.push(i);
            }
        }

        let count = 0;
        let topOrder = [];

        while (queue.length > 0) {
            let u = queue.shift();
            topOrder.push(u);

            let neighbors = this.adj.get(u);
            for (let v of neighbors) {
                inDegree[v]--;
                if (inDegree[v] === 0) {
                    queue.push(v);
                }
            }
            count++;
        }

        if (count !== this.V) {
            console.log("Graph contains a cycle. Topological sort not possible.");
        } else {
            console.log("Topological Sort (JavaScript):", topOrder.join(" "));
        }
    }
}

// Example usage
let g = new Graph(6);
g.addEdge(5, 2);
g.addEdge(5, 0);
g.addEdge(4, 0);
g.addEdge(4, 1);
g.addEdge(2, 3);
g.addEdge(3, 1);

g.kahnTopologicalSort();
```

## 19. Related Algorithms
* **DFS-based Topological Sort:** The primary alternative to Kahn's Algorithm. It utilizes a Depth-First Search and a stack to push nodes after all their adjacent nodes have been explored. It produces a valid topological sort but doesn't easily detect cycles on the fly without extra logic (keeping track of the recursion stack).
* **Kosaraju's Algorithm:** Used for finding Strongly Connected Components (SCCs) in a directed graph. It uses a DFS approach heavily reliant on the finishing times of nodes.
* **Tarjan's Algorithm:** Another algorithm for finding SCCs. It's often compared to Kosaraju's but operates in a single pass of DFS.
* **Cycle Detection in Directed Graphs:** While Kahn's Algorithm does this naturally, disjoint-set (Union-Find) is another popular method for detecting cycles in undirected graphs, and DFS is used for directed ones.
* **Shortest Path in DAGs:** Topological sorting is a prerequisite step for finding the shortest (or longest) paths in a DAG in O(V+E) time, which is faster than Dijkstra's algorithm.

## 20. Practice Problems
Here are some excellent problems to test your understanding of Kahn's Algorithm:
1. **Course Schedule (LeetCode 207):** Given a list of courses and prerequisites, determine if it's possible to finish all courses. (Direct application of cycle detection).
2. **Course Schedule II (LeetCode 210):** Similar to the above, but return the ordering of courses.
3. **Alien Dictionary (LeetCode 269 - Premium):** Given a list of words from an alien language sorted lexicographically, derive the order of letters in this language.
4. **Find Eventual Safe States (LeetCode 802):** Find all nodes that are eventually safe (every path starting from that node leads to a terminal node or another safe node).
5. **Parallel Courses (LeetCode 1136 - Premium):** Find the minimum number of semesters needed to take all courses, where multiple courses can be taken in a single semester if prerequisites are met.

## 21. Frequently Asked Questions
**Q: Can Kahn's Algorithm be used on undirected graphs?**
A: No, topological sorting is only defined for Directed Acyclic Graphs (DAGs). The concept of dependencies implies a direction.

**Q: What if there are multiple nodes with an in-degree of 0 at the same time?**
A: Any of them can be processed next. This is why a single DAG can have multiple valid topological sorts. The order depends on the queue implementation and how nodes are added to it.

**Q: How do we get the lexicographically smallest topological sort?**
A: Instead of a standard FIFO queue, use a Priority Queue (Min-Heap). When you have multiple nodes with in-degree 0, the priority queue will automatically ensure the smallest one is processed next.

**Q: Does it matter which node we start traversing from to find in-degrees?**
A: Calculating in-degrees requires checking every edge in the graph. The order in which you iterate over the vertices to calculate in-degrees doesn't matter, as long as all edges are processed.

**Q: What happens if the graph is not connected?**
A: The algorithm handles it perfectly. All components will be processed correctly since nodes with in-degree 0 from all disconnected components will be enqueued initially.

## 22. Quiz
Test your knowledge with these multiple-choice questions:

**1. What data structure is fundamentally used in the standard implementation of Kahn's Algorithm?**
a) Stack
b) Queue
c) Priority Queue
d) Tree
*Correct Answer: b) Queue*

**2. What does an in-degree of 0 signify for a node in a task scheduling context?**
a) The task cannot be completed.
b) The task depends on many other tasks.
c) The task has no prerequisites and can be started immediately.
d) The task is the final task in the project.
*Correct Answer: c) The task has no prerequisites and can be started immediately.*

**3. If Kahn's Algorithm finishes and the number of nodes in the sorted list is less than the total number of nodes in the graph, what does this indicate?**
a) The graph is disconnected.
b) The graph has a cycle.
c) The graph is a valid DAG.
d) The algorithm failed due to a bug.
*Correct Answer: b) The graph has a cycle.*

**4. What is the time complexity of Kahn's Algorithm for a graph with V vertices and E edges?**
a) O(V^2)
b) O(V log E)
c) O(V + E)
d) O(E^2)
*Correct Answer: c) O(V + E)*

**5. Which algorithm is an alternative to Kahn's Algorithm for finding a topological sort?**
a) Dijkstra's Algorithm
b) DFS-based Topological Sort
c) Kruskal's Algorithm
d) Bellman-Ford Algorithm
*Correct Answer: b) DFS-based Topological Sort*
