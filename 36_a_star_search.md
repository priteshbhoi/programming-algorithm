# A* Search Algorithm

## 1. Introduction

A* Search (A-Star) is an informed search and pathfinding algorithm introduced by Peter Hart, Nils Nilsson, and Bertram Raphael in 1968. It combines the guaranteed optimality of Dijkstra's Algorithm with the goal-directed speed of Greedy Best-First Search by incorporating a **heuristic function** $h(n)$ to estimate the cost from the current node $n$ to the goal.

Imagine navigating a city map from your hotel to a famous museum. Dijkstra's algorithm explores roads in every direction uniformly like an expanding circle. In contrast, A* uses a compass pointing toward the museum: it prioritizes roads that move physically closer to the destination while still factoring in the distance traveled so far.

It was created as part of the Shakey the Robot project at Stanford Research Institute to navigate a physical mobile robot through obstacle-filled rooms.

You should use A* Search whenever you need to find the shortest path between a specific start and goal node in a weighted graph or grid where a distance estimate (heuristic) can be computed.

## 2. Why Use This Algorithm?

A* Search is the gold standard for pathfinding in video games, robotics, and spatial networks.

**Benefits:**
- **Goal-Directed Efficiency:** Evaluates nodes closer to the destination first, drastically pruning the search space compared to Dijkstra's.
- **Guaranteed Optimal Shortest Path:** If the heuristic $h(n)$ is **admissible** (never overestimates remaining cost) and **consistent**, A* is guaranteed to find the absolute shortest path.
- **Optimally Efficient:** Expands fewer nodes than any other admissible search algorithm using the same heuristic.
- **Flexible Heuristics:** Supports Euclidean, Manhattan, Diagonal, or Chebyshev distance heuristics.

**Performance:**
- **Time Complexity:** $\mathcal{O}(E) = \mathcal{O}(b^d)$ worst-case, where $b$ is the branching factor and $d$ is path depth.
- **Space Complexity:** $\mathcal{O}(V)$ to store the Open and Closed sets.

**When it is better than Dijkstra's algorithm:**
A* is vastly faster than Dijkstra's for single-target pathfinding (e.g., character movement in video games or robot navigation) because it doesn't waste time exploring paths heading away from the goal.

## 3. Real-World Applications

- **Video Game AI Pathfinding:** Unit movement in strategy and RPG games (e.g., StarCraft, Age of Empires, Warcraft).
- **Autonomous Vehicles & Robotics:** Robot navigation, self-driving car path planning, and drone trajectory generation.
- **GPS Navigation:** Goal-focused route generation in mapping applications.
- **Puzzle Solvers:** 8-Puzzle, 15-Puzzle, and Rubik's Cube minimal move solving.

## 4. Prerequisites

Before learning A* Search, you should understand:
- [Dijkstra's Algorithm](./33_dijkstras_algorithm.md) and Priority Queues.
- Heuristic Functions $h(n)$ (Manhattan distance, Euclidean distance).
- The total cost evaluation formula: $f(n) = g(n) + h(n)$.

## 5. Visualization

```text
Evaluation Formula: f(n) = g(n) + h(n)
  - g(n): Actual cost from Start to node n
  - h(n): Estimated cost from node n to Goal
  - f(n): Total estimated path cost through node n

Grid Example (Manhattan Distance):
  S = Start (0,0), G = Goal (3,3)

  Node (1,0):
    g = 1 (1 step from S)
    h = |3-1| + |3-0| = 5
    f = 1 + 5 = 6

  Node (0,1):
    g = 1
    h = |3-0| + |3-1| = 5
    f = 1 + 5 = 6

  A* Priority Queue sorts candidates by lowest f(n) value first.
```

## 6. How It Works

1. Maintain an **Open Set** (Min-Priority Queue sorted by $f(n)$) and a **Closed Set** (Visited nodes).
2. For start node $s$: set $g(s) = 0$, $h(s) = \text{heuristic}(s, \text{goal})$, and $f(s) = g(s) + h(s)$. Push $s$ to Open Set.
3. While Open Set is not empty:
   - Pop node $u$ with lowest $f(u)$.
   - If $u == \text{goal}$, reconstruct and return path!
   - Move $u$ to Closed Set.
   - For each neighbor $v$ of $u$:
     - If $v$ is in Closed Set or impassable barrier, continue.
     - Calculate tentative $g_{\text{tentative}} = g(u) + \text{weight}(u, v)$.
     - If $v$ is not in Open Set OR $g_{\text{tentative}} < g(v)$:
       - Set $g(v) = g_{\text{tentative}}$.
       - Set $h(v) = \text{heuristic}(v, \text{goal})$.
       - Set $f(v) = g(v) + h(v)$.
       - Set $\text{parent}[v] = u$.
       - Add/Update $v$ in Open Set.

## 7. Step-by-Step Algorithm

1. `open_set = MinPriorityQueue()`, `closed_set = HashSet()`.
2. `g[start] = 0`, `f[start] = h(start, goal)`, `open_set.push(start)`.
3. Loop while `open_set` is not empty:
   1. `u = open_set.pop()`.
   2. If `u == goal`: Return `reconstruct_path(parent, goal)`.
   3. `closed_set.add(u)`.
   4. For each neighbor `v` of `u`:
      - If `v` in `closed_set`: Continue.
      - `tentative_g = g[u] + cost(u, v)`.
      - If `v` not in `open_set` OR `tentative_g < g[v]`:
        - `parent[v] = u`, `g[v] = tentative_g`.
        - `f[v] = g[v] + h(v, goal)`.
        - `open_set.push(v)`.
4. Return Failure (No path found).

## 8. Pseudocode

```text
function A_Star(start, goal, heuristic):
    openSet = MinPriorityQueue()
    closedSet = Set()
    
    gScore = map with default infinity
    fScore = map with default infinity
    parent = map()
    
    gScore[start] = 0
    fScore[start] = heuristic(start, goal)
    openSet.insert(start, fScore[start])
    
    while openSet is not empty:
        current = openSet.extractMin()
        
        if current == goal:
            return reconstructPath(parent, current)
            
        closedSet.add(current)
        
        for each neighbor of current:
            if neighbor in closedSet:
                continue
                
            tentative_g = gScore[current] + weight(current, neighbor)
            
            if tentative_g < gScore[neighbor]:
                parent[neighbor] = current
                gScore[neighbor] = tentative_g
                fScore[neighbor] = gScore[neighbor] + heuristic(neighbor, goal)
                
                if neighbor not in openSet:
                    openSet.insert(neighbor, fScore[neighbor])

    return failure
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>
#include <math.h>

#define ROWS 5
#define COLS 5

struct Node {
    int r, c;
    int g, h, f;
    struct Node* parent;
};

int heuristic(int r, int c, int gr, int gc) {
    return abs(r - gr) + abs(c - gc); // Manhattan distance
}

bool isValid(int r, int c, int grid[ROWS][COLS]) {
    return (r >= 0 && r < ROWS && c >= 0 && c < COLS && grid[r][c] == 0);
}

void aStar(int grid[ROWS][COLS], int sr, int sc, int gr, int gc) {
    bool closedSet[ROWS][COLS] = {false};
    struct Node* nodes[ROWS][COLS];

    for (int i = 0; i < ROWS; i++) {
        for (int j = 0; j < COLS; j++) {
            nodes[i][j] = (struct Node*)malloc(sizeof(struct Node));
            nodes[i][j]->r = i; nodes[i][j]->c = j;
            nodes[i][j]->g = 99999; nodes[i][j]->f = 99999;
            nodes[i][j]->parent = NULL;
        }
    }

    struct Node* start = nodes[sr][sc];
    start->g = 0;
    start->h = heuristic(sr, sc, gr, gc);
    start->f = start->g + start->h;

    while (1) {
        struct Node* current = NULL;
        int minF = 99999;

        for (int i = 0; i < ROWS; i++) {
            for (int j = 0; j < COLS; j++) {
                if (!closedSet[i][j] && nodes[i][j]->f < minF) {
                    minF = nodes[i][j]->f;
                    current = nodes[i][j];
                }
            }
        }

        if (current == NULL) {
            printf("No path found!\n");
            return;
        }

        if (current->r == gr && current->c == gc) {
            printf("Path found! Total Cost (g): %d\n", current->g);
            struct Node* temp = current;
            while (temp) {
                printf("(%d,%d) <- ", temp->r, temp->c);
                temp = temp->parent;
            }
            printf("START\n");
            return;
        }

        closedSet[current->r][current->c] = true;

        int dr[] = {-1, 1, 0, 0};
        int dc[] = {0, 0, -1, 1};

        for (int i = 0; i < 4; i++) {
            int nr = current->r + dr[i];
            int nc = current->c + dc[i];

            if (isValid(nr, nc, grid) && !closedSet[nr][nc]) {
                int tentative_g = current->g + 1;
                if (tentative_g < nodes[nr][nc]->g) {
                    nodes[nr][nc]->parent = current;
                    nodes[nr][nc]->g = tentative_g;
                    nodes[nr][nc]->h = heuristic(nr, nc, gr, gc);
                    nodes[nr][nc]->f = nodes[nr][nc]->g + nodes[nr][nc]->h;
                }
            }
        }
    }
}

int main() {
    int grid[ROWS][COLS] = {
        {0, 0, 0, 0, 0},
        {0, 1, 1, 1, 0},
        {0, 0, 0, 1, 0},
        {0, 1, 0, 0, 0},
        {0, 0, 0, 1, 0}
    };

    aStar(grid, 0, 0, 4, 4);
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <cmath>
#include <algorithm>

struct Point {
    int r, c;
    bool operator==(const Point& o) const { return r == o.r && c == o.c; }
};

struct Node {
    Point pt;
    int g, h, f;
    bool operator>(const Node& o) const { return f > o.f; }
};

int heuristic(Point p, Point goal) {
    return std::abs(p.r - goal.r) + std::abs(p.c - goal.c);
}

void aStar(const std::vector<std::vector<int>>& grid, Point start, Point goal) {
    int R = grid.size(), C = grid[0].size();
    std::vector<std::vector<int>> gScore(R, std::vector<int>(C, 1e9));
    std::vector<std::vector<Point>> parent(R, std::vector<Point>(C, {-1, -1}));
    std::vector<std::vector<bool>> closedSet(R, std::vector<bool>(C, false));

    std::priority_queue<Node, std::vector<Node>, std::greater<Node>> openSet;

    gScore[start.r][start.c] = 0;
    int hStart = heuristic(start, goal);
    openSet.push({start, 0, hStart, hStart});

    int dr[] = {-1, 1, 0, 0};
    int dc[] = {0, 0, -1, 1};

    while (!openSet.empty()) {
        Node curr = openSet.top();
        openSet.pop();
        Point u = curr.pt;

        if (u == goal) {
            std::cout << "Goal reached! Cost: " << gScore[u.r][u.c] << "\n";
            Point currPt = goal;
            while (!(currPt == Point{-1, -1})) {
                std::cout << "(" << currPt.r << "," << currPt.c << ") <- ";
                currPt = parent[currPt.r][currPt.c];
            }
            std::cout << "START\n";
            return;
        }

        if (closedSet[u.r][u.c]) continue;
        closedSet[u.r][u.c] = true;

        for (int i = 0; i < 4; i++) {
            int nr = u.r + dr[i], nc = u.c + dc[i];
            if (nr >= 0 && nr < R && nc >= 0 && nc < C && grid[nr][nc] == 0) {
                if (closedSet[nr][nc]) continue;
                int tentative_g = gScore[u.r][u.c] + 1;
                if (tentative_g < gScore[nr][nc]) {
                    gScore[nr][nc] = tentative_g;
                    parent[nr][nc] = u;
                    int h = heuristic({nr, nc}, goal);
                    openSet.push({{nr, nc}, tentative_g, h, tentative_g + h});
                }
            }
        }
    }
    std::cout << "No path found!\n";
}

int main() {
    std::vector<std::vector<int>> grid = {
        {0, 0, 0, 0, 0},
        {0, 1, 1, 1, 0},
        {0, 0, 0, 1, 0},
        {0, 1, 0, 0, 0},
        {0, 0, 0, 1, 0}
    };

    aStar(grid, {0, 0}, {4, 4});
    return 0;
}
```

### Java
```java
import java.util.*;

public class AStarGrid {
    static class Point {
        int r, c;
        Point(int r, int c) { this.r = r; this.c = c; }
        public boolean equals(Object o) {
            if (!(o instanceof Point)) return false;
            Point p = (Point) o;
            return r == p.r && c == p.c;
        }
    }

    static class Node implements Comparable<Node> {
        Point pt; int g, h, f;
        Node(Point pt, int g, int h) {
            this.pt = pt; this.g = g; this.h = h; this.f = g + h;
        }
        public int compareTo(Node o) { return Integer.compare(this.f, o.f); }
    }

    private static int heuristic(Point p, Point goal) {
        return Math.abs(p.r - goal.r) + Math.abs(p.c - goal.c);
    }

    public static void aStar(int[][] grid, Point start, Point goal) {
        int R = grid.length, C = grid[0].length;
        int[][] gScore = new int[R][C];
        for (int[] row : gScore) Arrays.fill(row, 1000000);
        boolean[][] closedSet = new boolean[R][C];

        PriorityQueue<Node> openSet = new PriorityQueue<>();
        gScore[start.r][start.c] = 0;
        openSet.add(new Node(start, 0, heuristic(start, goal)));

        int[] dr = {-1, 1, 0, 0};
        int[] dc = {0, 0, -1, 1};

        while (!openSet.isEmpty()) {
            Node curr = openSet.poll();
            Point u = curr.pt;

            if (u.equals(goal)) {
                System.out.println("Goal Reached with Cost: " + gScore[u.r][u.c]);
                return;
            }

            if (closedSet[u.r][u.c]) continue;
            closedSet[u.r][u.c] = true;

            for (int i = 0; i < 4; i++) {
                int nr = u.r + dr[i], nc = u.c + dc[i];
                if (nr >= 0 && nr < R && nc >= 0 && nc < C && grid[nr][nc] == 0) {
                    if (closedSet[nr][nc]) continue;
                    int tentative_g = gScore[u.r][u.c] + 1;
                    if (tentative_g < gScore[nr][nc]) {
                        gScore[nr][nc] = tentative_g;
                        openSet.add(new Node(new Point(nr, nc), tentative_g, heuristic(new Point(nr, nc), goal)));
                    }
                }
            }
        }
        System.out.println("No path found!");
    }

    public static void main(String[] args) {
        int[][] grid = {
            {0, 0, 0, 0, 0},
            {0, 1, 1, 1, 0},
            {0, 0, 0, 1, 0},
            {0, 1, 0, 0, 0},
            {0, 0, 0, 1, 0}
        };

        aStar(grid, new Point(0, 0), new Point(4, 4));
    }
}
```

### Python
```python
import heapq

def heuristic(p: tuple[int, int], goal: tuple[int, int]) -> int:
    return abs(p[0] - goal[0]) + abs(p[1] - goal[1])

def a_star(grid: list[list[int]], start: tuple[int, int], goal: tuple[int, int]) -> list[tuple[int, int]] | None:
    R, C = len(grid), len(grid[0])
    g_score = { (r, c): float('inf') for r in range(R) for c in range(C) }
    parent = {}
    closed_set = set()

    g_score[start] = 0
    f_start = g_score[start] + heuristic(start, goal)
    open_set = [(f_start, start)]

    dr = [-1, 1, 0, 0]
    dc = [0, 0, -1, 1]

    while open_set:
        f, u = heapq.heappop(open_set)

        if u == goal:
            path = []
            curr = goal
            while curr in parent:
                path.append(curr)
                curr = parent[curr]
            path.append(start)
            return path[::-1]

        if u in closed_set:
            continue
        closed_set.add(u)

        for i in range(4):
            nr, nc = u[0] + dr[i], u[1] + dc[i]
            v = (nr, nc)
            if 0 <= nr < R and 0 <= nc < C and grid[nr][nc] == 0:
                if v in closed_set:
                    continue
                tentative_g = g_score[u] + 1
                if tentative_g < g_score[v]:
                    parent[v] = u
                    g_score[v] = tentative_g
                    f_v = tentative_g + heuristic(v, goal)
                    heapq.heappush(open_set, (f_v, v))

    return None

if __name__ == "__main__":
    grid = [
        [0, 0, 0, 0, 0],
        [0, 1, 1, 1, 0],
        [0, 0, 0, 1, 0],
        [0, 1, 0, 0, 0],
        [0, 0, 0, 1, 0]
    ]

    path = a_star(grid, (0, 0), (4, 4))
    print("A* Path:", path)
```

### JavaScript
```javascript
function heuristic(p, goal) {
    return Math.abs(p.r - goal.r) + Math.abs(p.c - goal.c);
}

function aStar(grid, start, goal) {
    const R = grid.length, C = grid[0].length;
    const gScore = Array.from({ length: R }, () => new Array(C).fill(Infinity));
    const closedSet = Array.from({ length: R }, () => new Array(C).fill(false));
    const parent = Array.from({ length: R }, () => new Array(C).fill(null));

    gScore[start.r][start.c] = 0;
    const openSet = [{ pt: start, f: heuristic(start, goal) }];

    const dr = [-1, 1, 0, 0];
    const dc = [0, 0, -1, 1];

    while (openSet.length > 0) {
        openSet.sort((a, b) => a.f - b.f);
        const { pt: u } = openSet.shift();

        if (u.r === goal.r && u.c === goal.c) {
            const path = [];
            let curr = goal;
            while (curr) {
                path.push(`(${curr.r},${curr.c})`);
                curr = parent[curr.r][curr.c];
            }
            return path.reverse().join(" -> ");
        }

        if (closedSet[u.r][u.c]) continue;
        closedSet[u.r][u.c] = true;

        for (let i = 0; i < 4; i++) {
            const nr = u.r + dr[i], nc = u.c + dc[i];
            if (nr >= 0 && nr < R && nc >= 0 && nc < C && grid[nr][nc] === 0) {
                if (closedSet[nr][nc]) continue;
                const tentative_g = gScore[u.r][u.c] + 1;
                if (tentative_g < gScore[nr][nc]) {
                    gScore[nr][nc] = tentative_g;
                    parent[nr][nc] = u;
                    const f = tentative_g + heuristic({ r: nr, c: nc }, goal);
                    openSet.push({ pt: { r: nr, c: nc }, f });
                }
            }
        }
    }
    return null;
}

const grid = [
    [0, 0, 0, 0, 0],
    [0, 1, 1, 1, 0],
    [0, 0, 0, 1, 0],
    [0, 1, 0, 0, 0],
    [0, 0, 0, 1, 0]
];

console.log("Path:", aStar(grid, { r: 0, c: 0 }, { r: 4, c: 4 }));
```

## 10. Code Explanation

A* Search uses the priority equation $f(n) = g(n) + h(n)$ to steer path expansion toward the goal. $g(n)$ represents the exact cost to reach node $n$ from the start, while $h(n)$ estimates the remaining distance from $n$ to the goal. By placing candidate nodes into a Min-Priority Queue sorted by $f(n)$, A* evaluates nodes that look most promising first. When a node is popped, if its tentative cost $g$ improves upon previously recorded costs, its neighbors are updated and enqueued. When the goal node is dequeued, optimality is guaranteed (provided $h(n)$ is admissible).

## 11. Interactive Demo

An interactive 2D Grid Pathfinding Visualizer allows drawing walls and dragging Start/Goal handles.

- Dropdown selects Heuristic Mode (Manhattan, Euclidean, Chebyshev).
- Open Set nodes flash Cyan, Closed Set nodes turn Purple.
- Comparison metrics show A* expanded states ($N_{\text{A*}} = 24$) vs Dijkstra's expanded states ($N_{\text{Dijkstra}} = 142$).

## 12. Dry Run

**Grid (Manhattan Heuristic):** $S(0,0)$, $G(0,2)$, barrier at $(0,1)$

| Step | Open Set Candidates `(f = g + h)` | Dequeued `u` | Neighbors Evaluated |
| :--- | :--- | :--- | :--- |
| **Init** | `[(f=2, g=0, h=2, node=(0,0))]` | - | - |
| 1 | `[(f=2, node=(0,0))]` | `(0,0)` | $(0,1)$ is wall! Enqueue $(1,0)$: $g=1, h=3 \implies f=4$ |
| 2 | `[(f=4, node=(1,0))]` | `(1,0)` | Enqueue $(1,1)$: $g=2, h=2 \implies f=4$ |
| 3 | `[(f=4, node=(1,1))]` | `(1,1)` | Enqueue $(1,2)$: $g=3, h=1 \implies f=4$, $(0,1)$ is wall |
| 4 | `[(f=4, node=(1,2))]` | `(1,2)` | Enqueue $(0,2)$ Goal: $g=4, h=0 \implies f=4$ |
| 5 | `[(f=4, node=(0,2))]` | `(0,2)` Goal! | Reconstruct path: $(0,0)\to(1,0)\to(1,1)\to(1,2)\to(0,2)$ |

## 13. Time & Space Complexity

| Metric | Complexity | Reason |
| :--- | :--- | :--- |
| **Time Complexity** | $\mathcal{O}(E) = \mathcal{O}(b^d)$ | Worst case expands all edges; with good heuristic $h(n)$, expands far fewer nodes than Dijkstra |
| **Space Complexity** | $\mathcal{O}(V)$ | Open and Closed sets store generated nodes |

## 14. Advantages

- **Goal-Directed Speed:** Much faster than Dijkstra's for single-target pathfinding.
- **Guaranteed Optimal Path:** Guaranteed shortest path if $h(n)$ is admissible.
- **Flexible & Customizable:** Easily tuned using different heuristic distance functions.

## 15. Disadvantages

- **High Memory Usage:** Stores all generated nodes in memory ($\mathcal{O}(V)$ space), which can exhaust RAM on massive maps (use IDA* to mitigate).
- **Heuristic Sensitivity:** Performance degrades to Dijkstra's if $h(n) = 0$, or returns sub-optimal paths if $h(n)$ overestimates.

## 16. Applications

- Video game pathfinding (RTS, RPG, MOBAs).
- Autonomous vehicle trajectory planning.
- Robotic arm inverse kinematics motion planning.
- 15-Puzzle and Rubik's Cube solvers.

## 17. Common Mistakes

- **Using Inadmissible Heuristics:** Overestimating remaining distance (breaks shortest path optimality guarantee).
- **Ignoring Diagonal Movement Costs:** Using Manhattan distance when 8-directional or free-angle movement is permitted.

## 18. Interview Questions

1. What makes a heuristic function "admissible"?
2. What happens to A* Search if $h(n) = 0$ for all nodes? (Answer: It degenerates into Dijkstra's Algorithm).
3. What is the difference between Admissible heuristics and Consistent (Monotonic) heuristics?
4. How does IDA* (Iterative Deepening A*) solve the memory limitations of standard A*?

## 19. Practice Problems

**Easy:**
1. Implement A* Search on a 2D grid with Manhattan distance heuristic.
2. Compare node expansion count between Dijkstra and A* on a $20 \times 20$ grid.

**Medium:**
3. Solve the "Sliding Tile 8-Puzzle" using A* Search with Manhattan Distance.
4. Implement A* for 8-directional movement grid using Octile/Diagonal distance.

**Hard:**
5. Implement Hierarchical Pathfinding A* (HPA*) for massive open-world video game maps.

## 20. Related Algorithms

- [Dijkstra's Algorithm](./33_dijkstras_algorithm.md) (Special case where $h(n) = 0$)
- [Breadth-First Search (BFS)](./31_breadth_first_search.md) (Unweighted pathfinding)
- [IDA* Search](file:///D:/Pritesh/Learning%20Materials/Algorithm/README.md) (Memory-bounded variant)

## 21. Summary

A* Search is an informed shortest path algorithm that combines past path cost $g(n)$ with a goal-directed heuristic $h(n)$ using $f(n) = g(n) + h(n)$. With an admissible heuristic, it guarantees finding the optimal shortest path while expanding exponentially fewer states than Dijkstra's algorithm.

## 22. Quiz

**Question 1:** What does $f(n) = g(n) + h(n)$ stand for in A* Search?
- A) $g(n)$ is total cost, $h(n)$ is node count
- B) $g(n)$ is exact cost from start to $n$, $h(n)$ is estimated cost from $n$ to goal
- C) $g(n)$ is grid size, $h(n)$ is height
- D) $g(n)$ is time, $h(n)$ is memory
- **Correct Answer:** B
- **Explanation:** $g(n)$ measures path cost incurred so far; $h(n)$ estimates remaining distance to destination.

**Question 2:** What condition must a heuristic $h(n)$ satisfy to guarantee that A* finds the optimal shortest path?
- A) It must be equal to 0
- B) It must be admissible (never overestimate the true cost to reach the goal)
- C) It must overestimate the cost by at least 2x
- D) It must be negative
- **Correct Answer:** B
- **Explanation:** An admissible heuristic never overestimates actual remaining distance, guaranteeing optimality.

**Question 3:** What algorithm does A* Search degenerate into if $h(n) = 0$ for all nodes?
- A) Depth-First Search
- B) Dijkstra's Algorithm
- C) Bellman-Ford
- D) Kruskal's Algorithm
- **Correct Answer:** B
- **Explanation:** When $h(n) = 0$, $f(n) = g(n)$, which is identical to Dijkstra's priority queue ordering.

**Question 4:** Which heuristic is best suited for 4-directional grid movement (Up, Down, Left, Right)?
- A) Euclidean Distance ($\sqrt{\Delta x^2 + \Delta y^2}$)
- B) Manhattan Distance ($|\Delta x| + |\Delta y|$)
- C) Chebyshev Distance ($\max(|\Delta x|, |\Delta y|)$)
- D) Random distance
- **Correct Answer:** B
- **Explanation:** Manhattan distance calculates exact grid step counts along 4 orthogonal directions.

**Question 5:** What is the primary limitation of standard A* Search on huge state spaces?
- A) It cannot sort numbers
- B) High memory consumption (storing Open and Closed sets in memory)
- C) It requires negative edge weights
- D) It only works on 2D arrays
- **Correct Answer:** B
- **Explanation:** Storing millions of generated nodes in the Open/Closed sets can exhaust system RAM.

**Question 6:** Who invented A* Search in 1968?
- A) Peter Hart, Nils Nilsson, and Bertram Raphael
- B) Edsger Dijkstra
- C) Alan Turing
- D) Claude Shannon
- **Correct Answer:** A
- **Explanation:** Hart, Nilsson, and Raphael published A* at Stanford Research Institute in 1968.

**Question 7:** What heuristic distance should be used for 8-directional grid movement with equal diagonal cost?
- A) Manhattan distance
- B) Chebyshev distance ($\max(|\Delta x|, |\Delta y|)$)
- C) Hamming distance
- D) Levenshtein distance
- **Correct Answer:** B
- **Explanation:** Chebyshev distance measures movement steps when diagonal moves cost the same as orthogonal moves.

**Question 8:** What is an "inadmissible" heuristic?
- A) A heuristic that overestimates the true remaining cost to the goal
- B) A heuristic equal to 0
- C) A heuristic written in Python
- D) A heuristic that runs in $\mathcal{O}(1)$
- **Correct Answer:** A
- **Explanation:** Overestimating cost can cause A* to skip the true shortest path, returning a sub-optimal result.

**Question 9:** What is the role of the Closed Set in A* Search?
- A) Stores nodes that have already been evaluated to prevent redundant processing
- B) Stores wall obstacles
- C) Stores final path nodes only
- D) Stores negative weights
- **Correct Answer:** A
- **Explanation:** The Closed Set prevents re-evaluating nodes whose optimal path has already been processed.

**Question 10:** What pathfinding variant of A* reduces memory consumption to $\mathcal{O}(d)$?
- A) IDA* (Iterative Deepening A*)
- B) BFS
- C) Bellman-Ford
- D) Floyd-Warshall
- **Correct Answer:** A
- **Explanation:** IDA* uses depth-first search with increasing $f$-cost thresholds to achieve linear memory usage.
