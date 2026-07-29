# 49. Hierholzer's Algorithm

## 1. Introduction
Graph theory is a foundational area of computer science, enabling the modeling of complex relationships and networks. Among the classical problems in graph theory is finding an Eulerian Path or an Eulerian Circuit. An Eulerian Circuit is a trail in a finite graph that visits every edge exactly once and starts and ends on the same vertex. An Eulerian Path visits every edge exactly once but starts and ends on different vertices. Hierholzer's Algorithm is a highly efficient, linear-time algorithm designed to find Eulerian Circuits or Paths in a graph, provided one exists. First published by Carl Hierholzer in 1873, this algorithm remains the standard method for this problem due to its simplicity and optimal performance.

## 2. What is Hierholzer's Algorithm?
Hierholzer's Algorithm is a specialized graph traversal algorithm used to find an Eulerian Circuit or Eulerian Path in a directed or undirected graph. Unlike finding a Hamiltonian cycle (which visits every vertex exactly once and is NP-complete), finding an Eulerian cycle (which visits every edge exactly once) is solvable in linear time, $O(V + E)$, where $V$ is the number of vertices and $E$ is the number of edges.
The algorithm works by greedily following unvisited edges from a starting vertex until it gets stuck. Since Eulerian graphs have specific degree properties, getting "stuck" implies we have completed a cycle. The algorithm then backtracks along the cycle, identifying vertices that still have unvisited edges, and starts new sub-cycles from those vertices, eventually splicing all the cycles together into a single, continuous Eulerian circuit or path.

## 3. Real-world Applications
Hierholzer's Algorithm is not just a theoretical construct; it has numerous practical applications across various domains:
- **Bioinformatics (DNA Sequencing):** Modern DNA sequencing techniques (like De Novo assembly using De Bruijn graphs) reduce the problem of sequence assembly to finding an Eulerian path in a large directed graph.
- **Circuit Design (CMOS VLSI):** In designing physical layouts for logic gates, finding Eulerian paths helps in minimizing the number of breaks in the silicon layout, optimizing space and performance.
- **Routing and Network Operations:** Planning routes for garbage collection, street sweeping, snowplows, or mail delivery, where every street (edge) must be traversed at least once. (This is related to the Chinese Postman Problem, which often uses Eulerian circuits as a subroutine).
- **Computer Graphics:** Generating continuous line drawings for pen plotters or rendering continuous meshes in computer graphics pipelines.

## 4. Key Terminology
Understanding Hierholzer's Algorithm requires familiarity with several graph theory terms:
- **Graph ($G = (V, E)$):** A collection of vertices ($V$) and edges ($E$) connecting them.
- **Directed vs. Undirected Graph:** In a directed graph, edges have a direction (from $u$ to $v$). In an undirected graph, edges have no direction.
- **Degree:** In undirected graphs, the degree is the number of edges incident to a vertex. In directed graphs, we have *in-degree* (number of incoming edges) and *out-degree* (number of outgoing edges).
- **Eulerian Path:** A trail in a graph which visits every edge exactly once.
- **Eulerian Circuit (or Cycle):** An Eulerian path that starts and ends on the same vertex.
- **Strongly Connected Component (SCC):** A maximal subgraph where there is a path between every pair of vertices.

### Conditions for Eulerian Circuits/Paths:
**Undirected Graph:**
- *Eulerian Circuit:* Every vertex has an even degree, and all vertices with non-zero degree belong to a single connected component.
- *Eulerian Path:* Exactly zero or two vertices have an odd degree, and all vertices with non-zero degree belong to a single connected component. (If two odd vertices exist, they must be the start and end points).

**Directed Graph:**
- *Eulerian Circuit:* For every vertex, in-degree equals out-degree, and all vertices with non-zero degree belong to a single strongly connected component.
- *Eulerian Path:* At most one vertex has `out-degree - in-degree = 1` (start vertex), at most one vertex has `in-degree - out-degree = 1` (end vertex), and all other vertices have `in-degree == out-degree`. All vertices with non-zero degree belong to a single connected component of the underlying undirected graph.

## 5. How Hierholzer's Algorithm Works (Step-by-Step)
This step-by-step guide assumes we are working with a directed graph that is guaranteed to have an Eulerian Circuit or Path.

**Step 1: Determine the Starting Vertex**
- Calculate the in-degree and out-degree of all vertices.
- If finding a path, find the vertex where `out-degree - in-degree == 1`. This is your starting vertex.
- If finding a circuit (or if all in-degrees equal out-degrees), you can start at any vertex with a non-zero degree.

**Step 2: Depth-First Traversal (DFS)**
- Use a stack to maintain the current path. Push the starting vertex onto the stack.
- While the stack is not empty, look at the top vertex, $u$.
- If $u$ has outgoing edges, pick one, remove it from the graph (or mark it as visited), and push the destination vertex, $v$, onto the stack. Repeat this until you reach a vertex with no remaining outgoing edges (you get "stuck").

**Step 3: Backtracking and Circuit Splicing**
- When a vertex $u$ has no outgoing unvisited edges, it means a cycle (or the final path segment) is complete.
- Pop $u$ from the stack and add it to the result list.
- Continue this process. If a vertex lower in the stack still has unvisited outgoing edges, the DFS (Step 2) will naturally explore them, effectively building a new sub-cycle that gets spliced into the main cycle during the popping phase.

**Step 4: Reverse the Result**
- Because we add vertices to the result list when they are popped from the stack (i.e., when they have no more edges to explore), the result list contains the Eulerian path in reverse order.
- Reverse the result list to get the final Eulerian path or circuit.

## 6. Pseudo-code
Here is the high-level pseudo-code for Hierholzer's algorithm using an explicit stack.

```text
function Hierholzer(Graph G):
    // 1. Find start vertex
    start_vertex = any vertex with edges
    for each vertex v in G:
        if out_degree[v] - in_degree[v] == 1:
            start_vertex = v
            break
            
    stack.push(start_vertex)
    path = empty list
    
    // 2. Explore and build path
    while stack is not empty:
        u = stack.top()
        if out_degree[u] > 0: // or if u has unvisited outgoing edges
            v = get_next_unvisited_neighbor(u)
            remove_edge(u, v)
            out_degree[u] -= 1
            stack.push(v)
        else:
            path.append(stack.pop())
            
    // 3. Reverse path
    reverse(path)
    return path
```

## 7. Dry Run / Tracing
Let's trace the algorithm on a simple directed graph.
**Graph Edges:**
- $A \to B$
- $B \to C$
- $C \to A$
- $A \to D$
- $D \to A$

**Degrees:**
- $A$: in=2, out=2
- $B$: in=1, out=1
- $C$: in=1, out=1
- $D$: in=1, out=1
All in-degrees equal out-degrees, so an Eulerian Circuit exists. We can start anywhere. Let's start at $A$.

**Trace:**
- **Initialization:** Stack: `[A]`, Path: `[]`
- **Top of stack is A.** $A$ has neighbors $B, D$. Pick $B$. Edge $A \to B$ removed. Stack: `[A, B]`
- **Top of stack is B.** $B$ has neighbor $C$. Pick $C$. Edge $B \to C$ removed. Stack: `[A, B, C]`
- **Top of stack is C.** $C$ has neighbor $A$. Pick $A$. Edge $C \to A$ removed. Stack: `[A, B, C, A]`
- **Top of stack is A.** $A$ has neighbor $D$. Pick $D$. Edge $A \to D$ removed. Stack: `[A, B, C, A, D]`
- **Top of stack is D.** $D$ has neighbor $A$. Pick $A$. Edge $D \to A$ removed. Stack: `[A, B, C, A, D, A]`
- **Top of stack is A.** $A$ has no remaining edges. Pop $A$, add to Path. Stack: `[A, B, C, A, D]`, Path: `[A]`
- **Top of stack is D.** $D$ has no edges. Pop $D$. Stack: `[A, B, C, A]`, Path: `[A, D]`
- **Top of stack is A.** $A$ has no edges. Pop $A$. Stack: `[A, B, C]`, Path: `[A, D, A]`
- **Top of stack is C.** $C$ has no edges. Pop $C$. Stack: `[A, B]`, Path: `[A, D, A, C]`
- **Top of stack is B.** $B$ has no edges. Pop $B$. Stack: `[A]`, Path: `[A, D, A, C, B]`
- **Top of stack is A.** $A$ has no edges. Pop $A$. Stack: `[]`, Path: `[A, D, A, C, B, A]`
- **Stack empty.**
- **Reverse Path:** `[A, B, C, A, D, A]`
This is a valid Eulerian circuit!

## 8. Complexity Analysis
Hierholzer's Algorithm is highly efficient and optimal for finding Eulerian paths.
- **Time Complexity:** $O(V + E)$
  - Calculating degrees takes $O(V + E)$ time.
  - The while loop processes each edge exactly once. Pushing and popping from the stack takes $O(1)$ time. Removing an edge (or advancing an adjacency list pointer) takes $O(1)$ amortized time. Thus, the traversal takes $O(E)$ time.
  - Reversing the final path of size $E+1$ takes $O(E)$ time.
  - Total Time Complexity: $O(V + E)$.
- **Space Complexity:** $O(V + E)$
  - We need to store the graph, usually as an adjacency list, which takes $O(V + E)$ space.
  - The stack can hold up to $E+1$ vertices in the worst case (a single long cycle).
  - The result path array stores $E+1$ vertices.
  - Total Space Complexity: $O(V + E)$.

## 9. Advantages
- **Optimal Time Complexity:** It runs in linear time $O(V + E)$, which is the theoretical lower bound for this problem since we must at least read the input and output the path.
- **Simplicity:** The algorithm is remarkably simple to implement using a standard stack data structure.
- **Space Efficiency:** The memory footprint is very manageable, requiring only arrays/lists proportional to the graph size.
- **Versatility:** It can be adapted easily for both directed and undirected graphs, and for finding both circuits and paths.

## 10. Disadvantages
- **Destructive Nature (Implementation dependent):** Standard implementations often remove edges from the graph as they are traversed. If the original graph must be preserved, extra overhead is required to copy the graph or maintain complex state arrays (like pointer indices for each adjacency list).
- **Assumes Validity:** The core algorithm assumes the graph actually *has* an Eulerian path/circuit. Checking the degree conditions beforehand is strictly necessary to prevent infinite loops or incorrect results in invalid graphs.
- **Recursion Limits:** If implemented using recursion instead of an explicit stack, deeply connected graphs can cause stack overflow errors in languages like Python or Java.

## 11. Comparison with other algorithms
### Fleury's Algorithm
The other well-known algorithm for finding Eulerian paths is Fleury's Algorithm.
- **Approach:** Fleury's algorithm also traverses edges one by one but adds a constraint: it avoids traversing a "bridge" (an edge whose removal would disconnect the unvisited graph) unless there is no other choice.
- **Time Complexity:** $O(E^2)$. In the worst case, checking if an edge is a bridge takes $O(E)$ time, and this might be done for every edge.
- **Verdict:** Hierholzer's algorithm ($O(V+E)$) is vastly superior to Fleury's ($O(E^2)$) in terms of performance and is the standard choice in competitive programming and practical applications.

## 12. C Implementation
Below is a full, runnable C implementation for a directed graph using an adjacency matrix (for simplicity, though an adjacency list is better for sparse graphs).

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_VERTICES 100

int adj[MAX_VERTICES][MAX_VERTICES];
int out_degree[MAX_VERTICES];
int in_degree[MAX_VERTICES];
int n; // Number of vertices

// Stack implementation
int stack[MAX_VERTICES * MAX_VERTICES];
int top = -1;

void push(int v) { stack[++top] = v; }
int pop() { return stack[top--]; }
int peek() { return stack[top]; }
int is_empty() { return top == -1; }

void findEulerianPath() {
    int start_node = 0;
    int odd_out_nodes = 0;
    
    // Find starting node
    for (int i = 0; i < n; i++) {
        if (out_degree[i] - in_degree[i] == 1) {
            start_node = i;
            odd_out_nodes++;
        }
    }
    
    if (odd_out_nodes == 0) {
        // If Eulerian circuit, start anywhere with edges
        for (int i = 0; i < n; i++) {
            if (out_degree[i] > 0) {
                start_node = i;
                break;
            }
        }
    }

    int path[MAX_VERTICES * MAX_VERTICES];
    int path_size = 0;
    
    push(start_node);
    
    while (!is_empty()) {
        int u = peek();
        if (out_degree[u] > 0) {
            // Find next unvisited edge
            int v = 0;
            for (; v < n; v++) {
                if (adj[u][v] > 0) break;
            }
            adj[u][v]--;
            out_degree[u]--;
            push(v);
        } else {
            path[path_size++] = pop();
        }
    }

    printf("Eulerian Path/Circuit: ");
    for (int i = path_size - 1; i >= 0; i--) {
        printf("%d ", path[i]);
    }
    printf("\n");
}

int main() {
    n = 3;
    // Edges: 0->1, 1->2, 2->0
    adj[0][1] = 1; out_degree[0]++; in_degree[1]++;
    adj[1][2] = 1; out_degree[1]++; in_degree[2]++;
    adj[2][0] = 1; out_degree[2]++; in_degree[0]++;
    
    findEulerianPath();
    return 0;
}
```

## 13. C++ Implementation
A more optimized C++ approach using `std::vector` and adjacency lists. We use an array of indices to avoid deleting edges from the middle of vectors, ensuring $O(1)$ edge removal.

```cpp
#include <iostream>
#include <vector>
#include <stack>
#include <algorithm>

using namespace std;

void findEulerianPath(int n, vector<vector<int>>& edges) {
    vector<vector<int>> adj(n);
    vector<int> out_degree(n, 0), in_degree(n, 0);
    
    for (auto& edge : edges) {
        int u = edge[0];
        int v = edge[1];
        adj[u].push_back(v);
        out_degree[u]++;
        in_degree[v]++;
    }
    
    int start_node = 0;
    for (int i = 0; i < n; i++) {
        if (out_degree[i] - in_degree[i] == 1) {
            start_node = i;
            break;
        }
    }
    
    // if all are even, just start at a node with edges
    if (start_node == 0 && out_degree[0] == 0) {
        for(int i = 0; i < n; i++) {
            if(out_degree[i] > 0) {
                start_node = i;
                break;
            }
        }
    }

    stack<int> curr_path;
    vector<int> circuit;
    vector<int> edge_idx(n, 0); // Keeps track of next unvisited edge for each node
    
    curr_path.push(start_node);
    
    while (!curr_path.empty()) {
        int curr_v = curr_path.top();
        
        if (edge_idx[curr_v] < adj[curr_v].size()) {
            int next_v = adj[curr_v][edge_idx[curr_v]];
            edge_idx[curr_v]++;
            curr_path.push(next_v);
        } else {
            circuit.push_back(curr_v);
            curr_path.pop();
        }
    }
    
    reverse(circuit.begin(), circuit.end());
    
    cout << "Eulerian Path/Circuit: ";
    for (int v : circuit) cout << v << " ";
    cout << endl;
}

int main() {
    int n = 4;
    vector<vector<int>> edges = {{0, 1}, {1, 2}, {2, 0}, {0, 3}, {3, 0}};
    findEulerianPath(n, edges);
    return 0;
}
```

## 14. Java Implementation
Similar logic implemented in Java using `HashMap` to manage adjacency lists and degrees.

```java
import java.util.*;

public class HierholzerAlgorithm {
    
    public static List<Integer> findEulerianPath(int n, int[][] edges) {
        Map<Integer, Queue<Integer>> adj = new HashMap<>();
        int[] inDegree = new int[n];
        int[] outDegree = new int[n];
        
        for (int[] edge : edges) {
            adj.putIfAbsent(edge[0], new LinkedList<>());
            adj.get(edge[0]).offer(edge[1]);
            outDegree[edge[0]]++;
            inDegree[edge[1]]++;
        }
        
        int startNode = 0;
        for (int i = 0; i < n; i++) {
            if (outDegree[i] - inDegree[i] == 1) {
                startNode = i;
                break;
            }
        }
        
        if (startNode == 0 && outDegree[0] == 0) {
            for(int i=0; i<n; i++) {
                if(outDegree[i] > 0) {
                    startNode = i;
                    break;
                }
            }
        }
        
        Deque<Integer> stack = new ArrayDeque<>();
        List<Integer> result = new ArrayList<>();
        stack.push(startNode);
        
        while (!stack.isEmpty()) {
            int currNode = stack.peek();
            if (adj.containsKey(currNode) && !adj.get(currNode).isEmpty()) {
                int nextNode = adj.get(currNode).poll();
                stack.push(nextNode);
            } else {
                result.add(stack.pop());
            }
        }
        
        Collections.reverse(result);
        return result;
    }

    public static void main(String[] args) {
        int n = 3;
        int[][] edges = {{0, 1}, {1, 2}, {2, 0}};
        List<Integer> path = findEulerianPath(n, edges);
        System.out.println("Eulerian Path/Circuit: " + path);
    }
}
```

## 15. Python Implementation
Python allows for very clean implementation using `collections.defaultdict` and list methods. `pop()` on lists provides $O(1)$ edge removal if we pop from the end.

```python
from collections import defaultdict

def find_eulerian_path(n, edges):
    adj = defaultdict(list)
    in_degree = [0] * n
    out_degree = [0] * n
    
    for u, v in edges:
        adj[u].append(v)
        out_degree[u] += 1
        in_degree[v] += 1
        
    start_node = 0
    for i in range(n):
        if out_degree[i] - in_degree[i] == 1:
            start_node = i
            break
    
    if start_node == 0 and out_degree[0] == 0:
        for i in range(n):
            if out_degree[i] > 0:
                start_node = i
                break
                
    stack = [start_node]
    path = []
    
    while stack:
        curr = stack[-1]
        if adj[curr]:
            next_node = adj[curr].pop() # O(1) removal from end of list
            stack.append(next_node)
        else:
            path.append(stack.pop())
            
    path.reverse()
    return path

if __name__ == "__main__":
    n = 4
    edges = [[0, 1], [1, 2], [2, 0], [0, 3], [3, 0]]
    path = find_eulerian_path(n, edges)
    print(f"Eulerian Path/Circuit: {path}")
```

## 16. JavaScript Implementation
JavaScript implementation utilizing arrays as stacks and the `pop()` method for efficiently removing trailing elements.

```javascript
function findEulerianPath(n, edges) {
    const adj = Array.from({ length: n }, () => []);
    const inDegree = new Array(n).fill(0);
    const outDegree = new Array(n).fill(0);
    
    for (const [u, v] of edges) {
        adj[u].push(v);
        outDegree[u]++;
        inDegree[v]++;
    }
    
    let startNode = 0;
    for (let i = 0; i < n; i++) {
        if (outDegree[i] - inDegree[i] === 1) {
            startNode = i;
            break;
        }
    }
    
    if (startNode === 0 && outDegree[0] === 0) {
        for(let i = 0; i < n; i++) {
            if (outDegree[i] > 0) {
                startNode = i;
                break;
            }
        }
    }
    
    const stack = [startNode];
    const path = [];
    
    while (stack.length > 0) {
        const curr = stack[stack.length - 1];
        if (adj[curr].length > 0) {
            const nextNode = adj[curr].pop(); // Efficiently removes last edge
            stack.push(nextNode);
        } else {
            path.push(stack.pop());
        }
    }
    
    return path.reverse();
}

const n = 3;
const edges = [[0, 1], [1, 2], [2, 0]];
const result = findEulerianPath(n, edges);
console.log("Eulerian Path/Circuit:", result.join(" -> "));
```

## 17. Common Pitfalls / Edge Cases
- **Disconnected Graphs:** The algorithm assumes all edges belong to a single connected component. If there are isolated components with edges, the algorithm will only traverse one component and terminate, leaving an incomplete path. Always check if the length of the result path is $E+1$.
- **Starting at the Wrong Node (Paths):** If an Eulerian path exists (but not a circuit), starting at an arbitrary node instead of the specific node where `out_degree = in_degree + 1` will cause the algorithm to fail or produce a partial path.
- **Modifying the Adjacency List:** The algorithm fundamentally destroys edges as it traverses. Attempting to run it multiple times on the same graph without rebuilding the graph will result in immediate failure on subsequent runs.
- **Recursive Overflow:** In some tutorial implementations, Hierholzer's is taught recursively. For large graphs (e.g., $E = 10^5$), this will easily hit the recursion depth limit (Stack Overflow). Always use an explicit iterative stack approach.

## 18. Best Practices
- **Use $O(1)$ Edge Deletion:** When using adjacency lists (like vectors or arrays), instead of doing an expensive $O(V)$ erase operation from the middle of the list, either maintain an index array (as shown in the C++ example) pointing to the next valid edge, or just `pop` from the back of the list if edge order doesn't matter (as shown in Python/JS).
- **Validate Graph Properties First:** Before running the DFS, calculate in-degrees and out-degrees and ensure the graph strictly meets Eulerian criteria.
- **Check Path Length:** After generating the result path, ensure `path.length == Total Edges + 1`. If it isn't, the graph had disconnected components containing edges, and no valid Eulerian path spans the whole graph.

## 19. Interview Questions
1. **Can you explain the difference between Hierholzer's Algorithm and Fleury's Algorithm?**
   - *Answer:* Hierholzer's algorithm uses a stack-based DFS and runs in $O(V+E)$ time by building sub-cycles and splicing them together. Fleury's algorithm traverses the graph avoiding bridges, but checking for bridges takes time, making it $O(E^2)$. Hierholzer is much faster and preferred.
2. **What conditions must hold true for a directed graph to have an Eulerian Path?**
   - *Answer:* At most one vertex has out-degree = in-degree + 1, at most one vertex has in-degree = out-degree + 1, and all other vertices have equal in-degrees and out-degrees. The graph must also be weakly connected (ignoring isolated vertices).
3. **Why do we need a stack and not just a simple recursive traversal array?**
   - *Answer:* While recursion uses the call stack implicitly, large graphs exceed standard recursion limits. An explicit stack allows us to process graphs with hundreds of thousands of edges safely.

## 20. Practice Problems
- **LeetCode 332:** Reconstruct Itinerary (Classic Hierholzer's application with strings/lexicographical sorting).
- **LeetCode 753:** Cracking the Safe (Constructing a De Bruijn sequence using Hierholzer's).
- **LeetCode 2097:** Valid Arrangement of Pairs.
- **CSES Problem Set:** Eulerian Subgraphs.

## 21. Summary / Conclusion
Hierholzer's algorithm is an elegant and exceptionally optimal algorithm for identifying Eulerian circuits and paths in linear time $O(V+E)$. By employing a clever stack-based depth-first search that backtracks to splice untraversed cycles, it outclasses naive approaches and serves as a fundamental building block in algorithms related to network routing and sequence assembly. Understanding its degree preconditions, iterative implementation, and edge destruction mechanics is essential for any advanced student of graph theory.

## 22. Quiz
**Q1. What is the time complexity of Hierholzer's algorithm?**
A) $O(V^2)$
B) $O(E^2)$
C) $O(V + E)$
D) $O(V \cdot E)$
*Answer: C*

**Q2. When finding an Eulerian Path in a directed graph, what condition identifies the starting vertex?**
A) In-degree is zero
B) Out-degree is zero
C) In-degree - Out-degree == 1
D) Out-degree - In-degree == 1
*Answer: D*

**Q3. Why is Hierholzer's Algorithm preferred over Fleury's Algorithm?**
A) It uses less memory.
B) It runs in linear time compared to quadratic time.
C) It works for undirected graphs only.
D) It can find Hamiltonian paths.
*Answer: B*

**Q4. If the final path length output by the algorithm is less than $E + 1$, what does this imply?**
A) The graph has a Hamiltonian cycle.
B) The graph contains isolated disconnected components with edges.
C) The algorithm was implemented incorrectly.
D) The starting node was wrong.
*Answer: B*

**Q5. True or False: Hierholzer's algorithm modifies the original graph structure during execution.**
A) True (in standard implementations, edges are consumed/removed).
B) False.
*Answer: True*
