# 50. Fleury's Algorithm

## 1. Introduction
Graph theory provides foundational solutions for a variety of routing and traversal problems. One of the classic problems is finding an Euler path or an Euler circuit. An Euler path is a trail in a finite graph that visits every edge exactly once. An Euler circuit is an Euler path that starts and ends on the same vertex. Fleury's Algorithm, named after the French mathematician Carl Hierholzer, is an elegant and classical approach to finding these Eulerian trails and circuits.

The problem of finding an Eulerian trail traces its roots back to the famous Seven Bridges of Königsberg problem, solved by Leonhard Euler in 1736. While Euler proved the conditions under which such paths exist, he did not provide a definitive algorithm for finding the path itself. Decades later, in 1883, Fleury devised his elegant, rule-based approach to construct the sequence of edges. 

The primary importance of understanding Fleury's algorithm lies in its simplicity and greed-like strategy. By meticulously avoiding edges that would disconnect the remaining graph prematurely (bridges), it guarantees a valid traversal sequence whenever one is mathematically possible.

## 2. What is Fleury's Algorithm?
Fleury's algorithm is a simple and elegant algorithm used to find an Euler path or an Euler circuit in a given graph. It was proposed by Fleury in 1883. The algorithm works for both directed and undirected graphs, provided that an Eulerian trail exists. 

The core principle of Fleury's algorithm is simple: traverse the graph by removing edges as you go, and always prefer picking a non-bridge edge over a bridge edge unless no other choice exists. A bridge is an edge whose removal would disconnect the graph into two or more components. 

If you imagine being in a maze of islands connected by bridges, and you must cross every bridge exactly once, Fleury's algorithm tells you: "Never cross a bridge that is your only way back to unvisited islands, unless it is the only bridge left in front of you." This simple heuristic ensures that you don't trap yourself in an isolated component with edges left to visit in another part of the graph.

## 3. Real-world Applications
Fleury's algorithm, and the concept of Eulerian paths in general, has numerous real-world applications across various domains:
*   **Routing Algorithms**: Designing optimal routes for garbage collection, mail delivery, snowplows, and street sweeping, where every street (edge) must be traversed.
*   **DNA Sequencing**: In bioinformatics, Eulerian paths are used to assemble short fragments of DNA into longer sequences using De Bruijn graphs, helping decode genetic information efficiently.
*   **Network Design**: Ensuring network resilience and checking paths in telecommunications to verify physical link connectivity.
*   **Manufacturing and Robotics**: Optimizing the path of a laser cutter, a robotic welding arm, or a 3D printer nozzle to minimize wasted movement (cutting or moving without doing work) by finding continuous drawing paths.
*   **Computer Graphics**: Polygon rendering, circuit board layout tracing, and drawing continuous lines or shapes without lifting the digital pen or repeating segments.

## 4. Key Concepts and Terminology
To fully grasp Fleury's Algorithm, it is vital to understand several foundational terms in graph theory:
*   **Euler Path (Trail)**: A path in a graph which visits every edge exactly once. Vertices can be visited multiple times.
*   **Euler Circuit (Tour)**: An Euler path which starts and ends on the same vertex.
*   **Bridge (Cut-edge)**: An edge of a graph whose deletion increases the graph's number of connected components. In simpler terms, if removing an edge breaks the graph into two unreachable parts, that edge is a bridge.
*   **Degree of a Vertex**: The number of edges incident to (connecting to) a vertex. 
*   **Euler's Theorem**: 
    *   For an Eulerian circuit to exist, **every** vertex must have an even degree. 
    *   For an Eulerian path to exist, exactly **zero or two** vertices can have an odd degree.

## 5. How the Algorithm Works
The algorithm proceeds in a step-by-step greedy fashion:
1.  **Check Prerequisites**: Ensure the graph has either 0 or 2 vertices of odd degree. If it has 0, you can start at any vertex. If it has 2, you must start at one of the odd-degree vertices.
2.  **Start traversal**: Begin at a valid starting vertex.
3.  **Choose next edge**: From the current vertex, look at all adjacent unvisited edges.
4.  **Avoid Bridges**: If there is a choice between a bridge and a non-bridge edge, choose the non-bridge edge. If the only available edge is a bridge, you have no choice but to take it.
5.  **Remove Edge**: Traverse the chosen edge, add it to your path, and remove it from the graph (or mark it as visited).
6.  **Repeat**: Repeat steps 3-5 until all edges have been traversed. The sequence of vertices visited forms the Eulerian Path/Circuit.

## 6. Step-by-Step Example
Consider an undirected graph with vertices {A, B, C, D, E} and edges: 
(A,B), (B,C), (C,D), (D,E), (E,C), (C,A).

**Initial Step:**
*   Calculate degrees: A(2), B(2), C(4), D(2), E(2).
*   Since all vertices have even degrees, an Eulerian circuit exists. We can start from any vertex. Let's start at **A**.

**Traversal:**
1.  **Current Vertex = A**. Adjacent edges: (A,B), (A,C).
    *   Removing (A,B) doesn't disconnect the remaining graph. It's not a bridge.
    *   Removing (A,C) doesn't disconnect either.
    *   Let's pick **(A,B)**. Path: `A -> B`. Remove (A,B).
2.  **Current Vertex = B**. Adjacent edge: (B,C).
    *   It is the only edge. We must take it.
    *   Path: `A -> B -> C`. Remove (B,C).
3.  **Current Vertex = C**. Adjacent edges: (C,D), (C,E), (C,A).
    *   Is (C,A) a bridge? If we remove it, vertex A becomes isolated because its only remaining edge is (C,A). Thus, (C,A) is a bridge.
    *   Is (C,D) a bridge? Removing it leaves the rest connected.
    *   We avoid the bridge (C,A) and pick **(C,D)**.
    *   Path: `A -> B -> C -> D`. Remove (C,D).
4.  **Current Vertex = D**. Adjacent edge: (D,E).
    *   Only one choice. Pick it.
    *   Path: `A -> B -> C -> D -> E`. Remove (D,E).
5.  **Current Vertex = E**. Adjacent edge: (E,C).
    *   Only one choice. Pick it.
    *   Path: `A -> B -> C -> D -> E -> C`. Remove (E,C).
6.  **Current Vertex = C**. Adjacent edge: (C,A).
    *   It is the last remaining edge. Even though it was a bridge before, it is now our only choice. Pick it.
    *   Path: `A -> B -> C -> D -> E -> C -> A`. Remove (C,A).

**Result:**
The graph has no more edges. The Eulerian Circuit is `A -> B -> C -> D -> E -> C -> A`.

## 7. Pseudocode
```
function Fleury(Graph G):
    // 1. Find a valid starting node
    start_node = find_start_node(G)
    current_node = start_node
    path = []
    
    // 2. Loop until no edges are left
    while edges_remaining(G) > 0:
        for each neighbor in adjacent(current_node):
            // 3. Prefer non-bridges over bridges
            if is_valid_next_edge(current_node, neighbor):
                add neighbor to path
                remove edge(current_node, neighbor) from G
                current_node = neighbor
                break

function is_valid_next_edge(u, v):
    // If it's the only edge, it's valid by default
    if v is the only neighbor of u:
        return true
    
    // Check if it's a bridge using DFS
    count1 = DFS_count(u) // Count reachable vertices from u
    
    remove edge(u, v)
    count2 = DFS_count(u) // Count reachable vertices after removing edge
    add edge(u, v) // Backtrack
    
    if count1 > count2:
        return false // Removing it reduced connectivity -> It's a bridge
    else:
        return true // Not a bridge
```

## 8. Complexity Analysis (Time & Space)
*   **Time Complexity**: $O(E^2)$. 
    *   The outer loop runs $E$ times, as we traverse exactly $E$ edges.
    *   In the worst case, for each edge we consider, we must check if it is a bridge. 
    *   Bridge checking via Depth First Search (DFS) or Breadth First Search (BFS) takes $O(V+E)$ time. 
    *   Since we do this for up to $E$ edges, the overall time complexity is roughly $O(E \times (V+E))$. For connected graphs where $E \ge V-1$, this simplifies to $O(E^2)$.
*   **Space Complexity**: $O(V + E)$.
    *   Representing the graph using an adjacency list takes $O(V+E)$ space.
    *   The recursion stack during the DFS/BFS traversal for bridge detection takes $O(V)$ space.
    *   Therefore, the total space complexity is bounded by $O(V + E)$.

## 9. Advantages
*   **Intuitive and Simple to Understand**: The logic is highly intuitive, modeling how a human would naturally solve a maze or draw a continuous figure by avoiding dead-ends (bridges) until absolutely necessary.
*   **Correctness Guarantee**: It strictly guarantees finding an Eulerian path/circuit if one logically exists based on Euler's criteria.
*   **Space Efficient**: It does not require complex auxiliary data structures besides a standard graph representation and a visited array.

## 10. Disadvantages
*   **Highly Inefficient for Dense Graphs**: The $O(E^2)$ time complexity makes it far too slow for large graphs with many edges. It doesn't scale well in competitive programming or large real-world networks.
*   **Massive Bridge Detection Overhead**: Constantly recalculating whether an edge is a bridge after every edge deletion is computationally redundant and expensive.
*   **Destructive by Default**: The standard implementation destroys the graph by removing edges as it traverses. If the original graph topology needs to be retained, a deep copy must be created beforehand.

## 11. Comparison with other algorithms
**Fleury's vs. Hierholzer's Algorithm**
*   **Time Complexity**: Hierholzer's algorithm operates in $O(E)$ time, whereas Fleury's is $O(E^2)$. This is a massive difference for graphs with millions of edges.
*   **Methodology**: Hierholzer's algorithm works by finding cycles (tours) and iteratively merging them together. Fleury's algorithm walks edge by edge, actively verifying bridge conditions.
*   **Practical Use**: In almost all practical applications, software engineering, and competitive programming, Hierholzer's algorithm is overwhelmingly preferred due to its superior linear time complexity. Fleury's algorithm is mostly taught for historical and educational purposes.

## 12. C Implementation
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_VERTICES 100

int graph[MAX_VERTICES][MAX_VERTICES];
int vertices;

// Add an undirected edge
void addEdge(int u, int v) {
    graph[u][v]++;
    graph[v][u]++;
}

// Remove an undirected edge
void removeEdge(int u, int v) {
    graph[u][v]--;
    graph[v][u]--;
}

// Count reachable nodes using DFS
int dfsCount(int v, int visited[]) {
    visited[v] = 1;
    int count = 1;
    for (int i = 0; i < vertices; i++) {
        if (graph[v][i] && !visited[i]) {
            count += dfsCount(i, visited);
        }
    }
    return count;
}

// Check if edge u-v is a valid next choice
int isValidNextEdge(int u, int v) {
    int count = 0;
    for (int i = 0; i < vertices; i++) {
        if (graph[u][i]) count++;
    }
    // If it's the only edge, it's valid
    if (count == 1) return 1;

    // Count reachable vertices before removing the edge
    int visited1[MAX_VERTICES] = {0};
    int count1 = dfsCount(u, visited1);

    // Remove edge, count reachable vertices, add edge back
    removeEdge(u, v);
    int visited2[MAX_VERTICES] = {0};
    int count2 = dfsCount(u, visited2);
    addEdge(u, v);

    // If count1 > count2, then it's a bridge
    return (count1 > count2) ? 0 : 1;
}

// Print the Eulerian path/circuit
void printEulerUtil(int u) {
    for (int v = 0; v < vertices; v++) {
        if (graph[u][v] && isValidNextEdge(u, v)) {
            printf("%d - %d  ", u, v);
            removeEdge(u, v);
            printEulerUtil(v);
        }
    }
}

// Entry point
void printEulerTour() {
    int u = 0;
    for (int i = 0; i < vertices; i++) {
        int degree = 0;
        for (int j = 0; j < vertices; j++) {
            if (graph[i][j]) degree++;
        }
        // Start from an odd degree vertex if one exists
        if (degree % 2 != 0) {
            u = i;
            break;
        }
    }
    printEulerUtil(u);
    printf("\n");
}

int main() {
    vertices = 4;
    memset(graph, 0, sizeof(graph));
    addEdge(0, 1);
    addEdge(0, 2);
    addEdge(1, 2);
    addEdge(2, 3);
    printf("Eulerian Path is:\n");
    printEulerTour();
    return 0;
}
```

## 13. C++ Implementation
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

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
        adj[v].push_back(u);
    }

    void rmvEdge(int u, int v) {
        auto it = find(adj[u].begin(), adj[u].end(), v);
        if (it != adj[u].end()) adj[u].erase(it);

        it = find(adj[v].begin(), adj[v].end(), u);
        if (it != adj[v].end()) adj[v].erase(it);
    }

    int DFSCount(int v, vector<bool>& visited) {
        visited[v] = true;
        int count = 1;
        for (int i : adj[v])
            if (!visited[i])
                count += DFSCount(i, visited);
        return count;
    }

    bool isValidNextEdge(int u, int v) {
        int count = 0;
        for (int i : adj[u])
            if (i != -1) count++;
        if (count == 1) return true;

        vector<bool> visited(V, false);
        int count1 = DFSCount(u, visited);

        rmvEdge(u, v);
        fill(visited.begin(), visited.end(), false);
        int count2 = DFSCount(u, visited);
        addEdge(u, v);

        return (count1 > count2) ? false : true;
    }

    void printEulerUtil(int u) {
        for (int i = 0; i < adj[u].size(); i++) {
            int v = adj[u][i];
            if (v != -1 && isValidNextEdge(u, v)) {
                cout << u << "-" << v << " ";
                rmvEdge(u, v);
                printEulerUtil(v);
            }
        }
    }

    void printEulerTour() {
        int u = 0;
        for (int i = 0; i < V; i++) {
            if (adj[i].size() % 2 != 0) {
                u = i;
                break;
            }
        }
        printEulerUtil(u);
        cout << endl;
    }
};

int main() {
    Graph g1(4);
    g1.addEdge(0, 1);
    g1.addEdge(0, 2);
    g1.addEdge(1, 2);
    g1.addEdge(2, 3);
    cout << "Eulerian Path is:" << endl;
    g1.printEulerTour();
    return 0;
}
```

## 14. Java Implementation
```java
import java.util.ArrayList;

public class FleuryAlgorithm {
    private int vertices;
    private ArrayList<Integer>[] adj;

    @SuppressWarnings("unchecked")
    public FleuryAlgorithm(int vertices) {
        this.vertices = vertices;
        adj = new ArrayList[vertices];
        for (int i = 0; i < vertices; i++) {
            adj[i] = new ArrayList<>();
        }
    }

    public void addEdge(int u, int v) {
        adj[u].add(v);
        adj[v].add(u);
    }

    private void removeEdge(int u, int v) {
        adj[u].remove(Integer.valueOf(v));
        adj[v].remove(Integer.valueOf(u));
    }

    private int dfsCount(int v, boolean[] visited) {
        visited[v] = true;
        int count = 1;
        for (int adjVertex : adj[v]) {
            if (!visited[adjVertex]) {
                count += dfsCount(adjVertex, visited);
            }
        }
        return count;
    }

    private boolean isValidNextEdge(int u, int v) {
        if (adj[u].size() == 1) return true;

        boolean[] visited1 = new boolean[vertices];
        int count1 = dfsCount(u, visited1);

        removeEdge(u, v);
        boolean[] visited2 = new boolean[vertices];
        int count2 = dfsCount(u, visited2);
        addEdge(u, v);

        return count1 <= count2;
    }

    private void printEulerUtil(int u) {
        for (int i = 0; i < adj[u].size(); i++) {
            int v = adj[u].get(i);
            if (isValidNextEdge(u, v)) {
                System.out.print(u + "-" + v + " ");
                removeEdge(u, v);
                printEulerUtil(v);
                // Break to avoid ConcurrentModificationException since the list was modified
                break; 
            }
        }
    }

    public void printEulerTour() {
        int u = 0;
        for (int i = 0; i < vertices; i++) {
            if (adj[i].size() % 2 != 0) {
                u = i;
                break;
            }
        }
        printEulerUtil(u);
        System.out.println();
    }

    public static void main(String[] args) {
        FleuryAlgorithm g1 = new FleuryAlgorithm(4);
        g1.addEdge(0, 1);
        g1.addEdge(0, 2);
        g1.addEdge(1, 2);
        g1.addEdge(2, 3);
        System.out.println("Eulerian Path is:");
        g1.printEulerTour();
    }
}
```

## 15. Python Implementation
```python
from collections import defaultdict

class Graph:
    def __init__(self, vertices):
        self.V = vertices
        self.graph = defaultdict(list)

    def add_edge(self, u, v):
        self.graph[u].append(v)
        self.graph[v].append(u)

    def remove_edge(self, u, v):
        for index, key in enumerate(self.graph[u]):
            if key == v:
                self.graph[u].pop(index)
                break
        for index, key in enumerate(self.graph[v]):
            if key == u:
                self.graph[v].pop(index)
                break

    def dfs_count(self, v, visited):
        count = 1
        visited[v] = True
        for i in self.graph[v]:
            if not visited[i]:
                count = count + self.dfs_count(i, visited)
        return count

    def is_valid_next_edge(self, u, v):
        # Condition 1: If v is the only adjacent vertex of u
        if len(self.graph[u]) == 1:
            return True

        # Condition 2: If there are multiple adjacents, check if u-v is a bridge
        visited = [False] * self.V
        count1 = self.dfs_count(u, visited)

        # Remove edge and calculate connectivity
        self.remove_edge(u, v)
        visited = [False] * self.V
        count2 = self.dfs_count(u, visited)

        # Add the edge back
        self.add_edge(u, v)

        # If connectivity decreased, it was a bridge
        return False if count1 > count2 else True

    def print_euler_util(self, u):
        for v in self.graph[u]:
            if self.is_valid_next_edge(u, v):
                print(f"{u}-{v}", end=" ")
                self.remove_edge(u, v)
                self.print_euler_util(v)
                break # Avoid changing list during iteration

    def print_euler_tour(self):
        u = 0
        for i in range(self.V):
            if len(self.graph[i]) % 2 != 0:
                u = i
                break
        self.print_euler_util(u)
        print()

if __name__ == "__main__":
    g1 = Graph(4)
    g1.add_edge(0, 1)
    g1.add_edge(0, 2)
    g1.add_edge(1, 2)
    g1.add_edge(2, 3)
    print("Eulerian Path is:")
    g1.print_euler_tour()
```

## 16. JavaScript Implementation
```javascript
class Graph {
    constructor(vertices) {
        this.vertices = vertices;
        this.adj = new Map();
        for (let i = 0; i < vertices; i++) {
            this.adj.set(i, []);
        }
    }

    addEdge(u, v) {
        this.adj.get(u).push(v);
        this.adj.get(v).push(u);
    }

    removeEdge(u, v) {
        let uList = this.adj.get(u);
        uList.splice(uList.indexOf(v), 1);
        
        let vList = this.adj.get(v);
        vList.splice(vList.indexOf(u), 1);
    }

    dfsCount(v, visited) {
        visited[v] = true;
        let count = 1;
        for (let neighbor of this.adj.get(v)) {
            if (!visited[neighbor]) {
                count += this.dfsCount(neighbor, visited);
            }
        }
        return count;
    }

    isValidNextEdge(u, v) {
        if (this.adj.get(u).length === 1) return true;

        let visited1 = new Array(this.vertices).fill(false);
        let count1 = this.dfsCount(u, visited1);

        this.removeEdge(u, v);
        let visited2 = new Array(this.vertices).fill(false);
        let count2 = this.dfsCount(u, visited2);
        this.addEdge(u, v); // Backtrack

        return count1 <= count2;
    }

    printEulerUtil(u) {
        // Create a copy to iterate to avoid concurrent modification issues
        let neighbors = [...this.adj.get(u)];
        for (let v of neighbors) {
            if (this.isValidNextEdge(u, v)) {
                process.stdout.write(`${u}-${v} `);
                this.removeEdge(u, v);
                this.printEulerUtil(v);
                break;
            }
        }
    }

    printEulerTour() {
        let u = 0;
        // Find a vertex with an odd degree
        for (let i = 0; i < this.vertices; i++) {
            if (this.adj.get(i).length % 2 !== 0) {
                u = i;
                break;
            }
        }
        this.printEulerUtil(u);
        console.log();
    }
}

// Example usage
const g1 = new Graph(4);
g1.addEdge(0, 1);
g1.addEdge(0, 2);
g1.addEdge(1, 2);
g1.addEdge(2, 3);
console.log("Eulerian Path is:");
g1.printEulerTour();
```

## 17. Common Pitfalls and Mistakes
*   **Modifying Collections during Iteration**: In programming languages like Java, Python, and C++, removing edges from an adjacency list while simultaneously iterating over that same list can cause runtime errors (like a `ConcurrentModificationException`) or logic flaws. The implementations above use a `break` statement to exit the loop immediately after a successful traversal step to circumvent this.
*   **Not Restoring Edges Properly (Backtracking)**: When verifying whether an edge is a bridge, the algorithm tentatively deletes the edge and tests the connectivity. A massive pitfall is failing to add that exact edge back into the graph structure before proceeding.
*   **Incorrect Odd Degree Verification**: Starting the traversal arbitrarily from an even-degree node when odd-degree nodes exist will inevitably cause the algorithm to reach a dead end without covering all edges. The pre-check to find the correct starting node is non-negotiable.
*   **Ignoring Disconnected Graphs**: Fleury's algorithm operates on the assumption that the graph has a single connected component (ignoring isolated vertices with 0 degree). If you run it on a disconnected graph, it will only traverse the connected component of the starting node.

## 18. Optimization Techniques
*   **Tarjan's Bridge-Finding Algorithm**: In the brute-force approach, DFS is run from scratch for every adjacent edge, leading to a slow $O(E^2)$ time. This can be marginally optimized by maintaining bridge information using Tarjan's bridge-finding algorithm. However, updating bridges dynamically as edges are actively deleted is complex and often still highly constrained.
*   **Adjacency Matrix Fast Lookups**: For dense graphs, using an adjacency matrix combined with adjacency lists can speed up edge removal and bridge status updates marginally.
*   **Switching Paradigms**: The ultimate optimization for routing and Eulerian path problems is to discard Fleury's algorithmic approach entirely in favor of **Hierholzer's algorithm**, which naturally constructs the path in $O(E)$ time without ever needing to verify bridges. 

## 19. Related Algorithms
*   **Hierholzer's Algorithm**: The linear-time $O(E)$ counterpart to Fleury's algorithm. It works by finding disconnected sub-tours (cycles) and pasting them into the main tour.
*   **Hamiltonian Path Algorithms (e.g., Backtracking, DP with Bitmasking)**: While Euler paths focus on traversing every *edge* exactly once, Hamiltonian paths aim to traverse every *vertex* exactly once. This is a significantly harder problem (NP-Complete).
*   **Tarjan's Bridge-Finding Algorithm**: Used to find all bridges in a graph efficiently in $O(V+E)$ time using a single DFS pass.

## 20. Practice Problems
1.  **Reconstruct Itinerary (LeetCode 332)**: Given a list of airline tickets, reconstruct the route in order. This is a classic application of finding an Eulerian path on a directed graph.
2.  **Valid Arrangement of Pairs (LeetCode 2097)**: Given a 2D integer array of pairs `[start, end]`, find a valid arrangement such that the end of one pair matches the start of the next.
3.  **Draw a Shape without Retracing**: Given an undirected graph representing a figure, output the sequence of vertices to draw the figure completely without lifting your pen or retracing any line.

## 21. Summary
Fleury's algorithm is an elegant, fundamental algorithm for uncovering Eulerian paths and circuits within a graph. By strictly adhering to the simple rule—start at the right vertex and never burn a bridge unless it's your final option—it gracefully walks through a graph traversing every single edge exactly once. 

Although its quadratic time complexity $O(E^2)$ makes it far less viable than Hierholzer's algorithm for modern large-scale applications and competitive programming, its highly intuitive approach remains significant. It serves as an excellent educational tool for understanding graph traversal, connectivity concepts, and bridge detection in graph theory.

## 22. Quiz
**Q1: What is the worst-case time complexity of standard Fleury's Algorithm?**
A) $O(V+E)$
B) $O(V^2)$
C) $O(E^2)$
D) $O(E \log V)$
*Answer: C*

**Q2: Which condition mandates picking a specific starting vertex in Fleury's algorithm for an undirected graph?**
A) All vertices have even degrees.
B) Exactly two vertices have an odd degree.
C) The graph is fully connected.
D) The graph contains at least one bridge.
*Answer: B*

**Q3: According to Fleury's Algorithm, when should you cross a bridge edge?**
A) Whenever it is encountered to avoid cycles.
B) Never cross a bridge edge under any circumstances.
C) Only when there is no other non-bridge adjacent edge available.
D) Only at the start of the path to minimize future disconnections.
*Answer: C*

**Q4: Which algorithm is generally preferred over Fleury's for finding Eulerian paths due to its superior linear time complexity?**
A) Dijkstra's Algorithm
B) Kruskal's Algorithm
C) Hierholzer's Algorithm
D) Tarjan's Algorithm
*Answer: C*

**Q5: An Eulerian circuit (tour) can only exist in an undirected graph if:**
A) All vertices have an even degree.
B) Exactly two vertices have an odd degree.
C) The graph has no bridges.
D) The graph is bipartite.
*Answer: A*
