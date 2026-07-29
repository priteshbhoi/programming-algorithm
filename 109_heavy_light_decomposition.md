# Heavy-Light Decomposition (HLD)

## 1. Introduction
Heavy-Light Decomposition (HLD) is an advanced algorithmic technique used to break down (decompose) a tree into a collection of vertex-disjoint paths. By classifying edges as either "heavy" or "light," HLD ensures that any path between two nodes in a tree of $n$ vertices consists of at most $O(\log n)$ light edges and $O(\log n)$ heavy paths. This property allows us to map the tree onto 1D arrays and efficiently perform path queries (like finding the maximum, minimum, or sum of weights on a path between two nodes) and path updates, typically achieving $O(\log^2 n)$ time complexity when combined with a Segment Tree.

**Real-World Analogy:**
Imagine a large, complex corporate hierarchy (a tree structure). If you want to send a message from a low-level employee to the CEO, passing through every manager one by one is slow. However, suppose we identify the "main chains of command" (heavy paths) where the majority of subordinates are clustered. If we create express communication channels for these main chains, a message can zoom up a heavy chain instantly, occasionally hopping between different chains (light edges). Because we mathematically ensured there aren't too many chain-hops, the message reaches the top significantly faster!

## 2. Why Use This Algorithm?
Traditional tree traversal techniques like DFS or BFS can take $O(n)$ time to query or update a path between two nodes. For multiple queries (say, $q$ queries), this takes $O(q \times n)$, which is too slow for large trees ($n \approx 10^5$). 
HLD is used when we need to handle dynamic operations on trees (updates and queries on paths) efficiently. By flattening the tree into contiguous arrays based on heavy chains, we can leverage powerful 1D data structures like Segment Trees to answer queries in $O(\log^2 n)$ time.

## 3. Real-World Applications
- **Network Routing:** Optimizing and querying the bandwidth or latency along the path between two routers in a network tree.
- **Organization Management:** Quickly calculating statistics (like total salary or max performance score) across a direct chain of command in a company.
- **Transportation Networks:** Finding the most congested road (maximum edge weight) on a route between two cities connected in a tree topology.
- **Computational Biology:** Analyzing hierarchical clustering trees and phylogenetic trees to find genetic similarities along evolutionary paths.

## 4. Prerequisites
Before learning Heavy-Light Decomposition, you should be comfortable with:
- **Trees and Graphs:** Understanding of basic tree properties and representations (adjacency lists).
- **Depth-First Search (DFS):** Traversing trees to compute properties like subtree sizes and depths.
- **Lowest Common Ancestor (LCA):** Understanding how to find the LCA, though HLD can inherently compute LCA!
- **Segment Trees / Fenwick Trees:** Essential for performing the actual $O(\log n)$ range queries and updates on the decomposed paths.

## 5. Visualization
Consider a tree where each node has a subtree size. 
For any node $u$, we look at all its children. The edge to the child $v$ with the **largest subtree size** is marked as a **Heavy Edge**. Edges to all other children are marked as **Light Edges**.

```text
       (1) Size: 7
      /   \
  L /       \ H
  (2)s:2    (3)s:4
   | H      / H \ L
  (4)s:1  (5)s:2 (6)s:1
           | H
          (7)s:1
```
*(H = Heavy Edge, L = Light Edge, s = Subtree Size)*

- Heavy paths: (1)-(3)-(5)-(7), and (2)-(4), and (6).
- Light edges connect these paths: (1)-(2) and (3)-(6).

## 6. How It Works
HLD operates in two main phases (usually implemented as two DFS traversals):
1. **First Phase (Size and Heavy Child Calculation):** 
   A DFS computes the depth, parent, and subtree size for every node. During this, it identifies the "heavy child" for each node—the child with the maximum subtree size.
2. **Second Phase (Decomposition and Flattening):**
   A second DFS traverses the tree, prioritizing the heavy child. It assigns a "head" (the topmost node of the current heavy path) to each node and gives each node a unique 1D index (`pos`). Because heavy children are visited first, nodes on the same heavy path receive contiguous indices.
3. **Data Structure Integration:**
   A Segment Tree is built over the 1D array of size $n$. When querying a path from $u$ to $v$, we jump up the heavy chains. We query the Segment Tree for the contiguous segments of each heavy chain we traverse, combining the results.

## 7. Step-by-Step Algorithm
Let's define the arrays we need:
- `parent[u]`: Parent of node $u$.
- `depth[u]`: Depth of node $u$ from the root.
- `heavy[u]`: The heavy child of node $u$ (-1 if it's a leaf).
- `head[u]`: The topmost node of the heavy chain containing $u$.
- `pos[u]`: The position of node $u$ in the 1D flattened array (Segment Tree index).

**Step 1:** Run `dfs1(root)`.
- For current node $u$, set `size = 1`.
- Iterate through children $v$. If $v \neq parent[u]$:
  - Recursively call `dfs1(v)`.
  - Add $v$'s size to $u$'s size.
  - If $v$'s size is strictly greater than the current max child's size, update `heavy[u] = v`.

**Step 2:** Run `dfs2(root, head_node)`.
- Assign `head[u] = head_node` and `pos[u] = current_timer++`.
- If `heavy[u]` exists, call `dfs2(heavy[u], head_node)`.
- For other children $v$ of $u$ ($v \neq parent$ and $v \neq heavy[u]$):
  - Call `dfs2(v, v)` (starting a new heavy chain).

**Step 3:** Path Query ($u, v$).
- While `head[u] \neq head[v]`:
  - If `depth[head[u]] < depth[head[v]]`, swap $u$ and $v$.
  - Query the Segment Tree from `pos[head[u]]` to `pos[u]`.
  - Move $u$ up: `u = parent[head[u]]`.
- Now $u$ and $v$ are on the same heavy chain. If `depth[u] > depth[v]`, swap $u$ and $v$.
- Query Segment Tree from `pos[u]` to `pos[v]`.
- Combine all queried results.

## 8. Pseudocode
```text
function dfs1(v, p, d):
    size[v] = 1
    parent[v] = p
    depth[v] = d
    heavy[v] = -1
    max_sub = 0
    for u in adj[v]:
        if u != p:
            sub_size = dfs1(u, v, d + 1)
            size[v] += sub_size
            if sub_size > max_sub:
                max_sub = sub_size
                heavy[v] = u
    return size[v]

function dfs2(v, p, h):
    head[v] = h
    pos[v] = current_pos++
    if heavy[v] != -1:
        dfs2(heavy[v], v, h)
    for u in adj[v]:
        if u != p and u != heavy[v]:
            dfs2(u, v, u)

function query(u, v):
    res = 0
    while head[u] != head[v]:
        if depth[head[u]] < depth[head[v]]:
            swap(u, v)
        res += seg_tree_query(pos[head[u]], pos[u])
        u = parent[head[u]]
    if depth[u] > depth[v]:
        swap(u, v)
    res += seg_tree_query(pos[u], pos[v])
    return res
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>

#define MAXN 10005

int head_adj[MAXN], to[MAXN*2], nxt[MAXN*2], edge_cnt;
int parent[MAXN], depth[MAXN], heavy[MAXN], head[MAXN], pos[MAXN], sz[MAXN];
int seg[MAXN*4], val[MAXN];
int current_pos = 0;

void add_edge(int u, int v) {
    to[++edge_cnt] = v; nxt[edge_cnt] = head_adj[u]; head_adj[u] = edge_cnt;
    to[++edge_cnt] = u; nxt[edge_cnt] = head_adj[v]; head_adj[v] = edge_cnt;
}

int dfs1(int v, int p, int d) {
    sz[v] = 1; parent[v] = p; depth[v] = d; heavy[v] = -1;
    int max_sub = 0;
    for (int i = head_adj[v]; i; i = nxt[i]) {
        int u = to[i];
        if (u != p) {
            int sub_sz = dfs1(u, v, d + 1);
            sz[v] += sub_sz;
            if (sub_sz > max_sub) { max_sub = sub_sz; heavy[v] = u; }
        }
    }
    return sz[v];
}

void dfs2(int v, int p, int h) {
    head[v] = h; pos[v] = ++current_pos;
    if (heavy[v] != -1) dfs2(heavy[v], v, h);
    for (int i = head_adj[v]; i; i = nxt[i]) {
        int u = to[i];
        if (u != p && u != heavy[v]) dfs2(u, v, u);
    }
}

void update(int node, int l, int r, int idx, int v) {
    if (l == r) { seg[node] = v; return; }
    int mid = (l + r) / 2;
    if (idx <= mid) update(2*node, l, mid, idx, v);
    else update(2*node+1, mid+1, r, idx, v);
    seg[node] = seg[2*node] + seg[2*node+1];
}

int query_seg(int node, int l, int r, int ql, int qr) {
    if (ql > r || qr < l) return 0;
    if (ql <= l && r <= qr) return seg[node];
    int mid = (l + r) / 2;
    return query_seg(2*node, l, mid, ql, qr) + query_seg(2*node+1, mid+1, r, ql, qr);
}

void swap(int *a, int *b) { int t = *a; *a = *b; *b = t; }

int query_path(int u, int v, int n) {
    int res = 0;
    while (head[u] != head[v]) {
        if (depth[head[u]] < depth[head[v]]) swap(&u, &v);
        res += query_seg(1, 1, n, pos[head[u]], pos[u]);
        u = parent[head[u]];
    }
    if (depth[u] > depth[v]) swap(&u, &v);
    res += query_seg(1, 1, n, pos[u], pos[v]);
    return res;
}

int main() {
    int n = 5;
    add_edge(1, 2); add_edge(1, 3); add_edge(3, 4); add_edge(3, 5);
    dfs1(1, 1, 0);
    dfs2(1, 1, 1);
    
    val[1] = 10; val[2] = 20; val[3] = 30; val[4] = 40; val[5] = 50;
    for (int i = 1; i <= n; i++) update(1, 1, n, pos[i], val[i]);
    
    printf("Sum from 2 to 5: %d\n", query_path(2, 5, n)); // 10+20+30+50 = 110
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
using namespace std;

class HLD {
    int n, timer;
    vector<vector<int>> adj;
    vector<int> parent, depth, heavy, head, pos, sz, seg;

    int dfs1(int v, int p, int d) {
        sz[v] = 1; parent[v] = p; depth[v] = d;
        int max_sub = 0;
        for (int u : adj[v]) {
            if (u != p) {
                int sub_sz = dfs1(u, v, d + 1);
                sz[v] += sub_sz;
                if (sub_sz > max_sub) { max_sub = sub_sz; heavy[v] = u; }
            }
        }
        return sz[v];
    }

    void dfs2(int v, int p, int h) {
        head[v] = h; pos[v] = ++timer;
        if (heavy[v] != -1) dfs2(heavy[v], v, h);
        for (int u : adj[v]) {
            if (u != p && u != heavy[v]) dfs2(u, v, u);
        }
    }

    void update_seg(int node, int l, int r, int idx, int val) {
        if (l == r) { seg[node] = val; return; }
        int mid = l + (r - l) / 2;
        if (idx <= mid) update_seg(2 * node, l, mid, idx, val);
        else update_seg(2 * node + 1, mid + 1, r, idx, val);
        seg[node] = seg[2 * node] + seg[2 * node + 1];
    }

    int query_seg(int node, int l, int r, int ql, int qr) {
        if (ql > r || qr < l) return 0;
        if (ql <= l && r <= qr) return seg[node];
        int mid = l + (r - l) / 2;
        return query_seg(2 * node, l, mid, ql, qr) + query_seg(2 * node + 1, mid + 1, r, ql, qr);
    }

public:
    HLD(int nodes) : n(nodes), timer(0) {
        adj.resize(n + 1); parent.resize(n + 1); depth.resize(n + 1);
        heavy.assign(n + 1, -1); head.resize(n + 1); pos.resize(n + 1);
        sz.resize(n + 1); seg.assign(4 * n + 1, 0);
    }

    void add_edge(int u, int v) {
        adj[u].push_back(v); adj[v].push_back(u);
    }

    void build() {
        dfs1(1, 1, 0);
        dfs2(1, 1, 1);
    }

    void update(int v, int val) {
        update_seg(1, 1, n, pos[v], val);
    }

    int query(int u, int v) {
        int res = 0;
        while (head[u] != head[v]) {
            if (depth[head[u]] < depth[head[v]]) swap(u, v);
            res += query_seg(1, 1, n, pos[head[u]], pos[u]);
            u = parent[head[u]];
        }
        if (depth[u] > depth[v]) swap(u, v);
        res += query_seg(1, 1, n, pos[u], pos[v]);
        return res;
    }
};

int main() {
    HLD hld(5);
    hld.add_edge(1, 2); hld.add_edge(1, 3); hld.add_edge(3, 4); hld.add_edge(3, 5);
    hld.build();
    
    hld.update(1, 10); hld.update(2, 20); hld.update(3, 30); 
    hld.update(4, 40); hld.update(5, 50);
    
    cout << "Path sum 2 to 5: " << hld.query(2, 5) << "\n";
    return 0;
}
```

### Java
```java
import java.util.*;

public class HeavyLightDecomposition {
    static class HLD {
        int n, timer;
        List<Integer>[] adj;
        int[] parent, depth, heavy, head, pos, sz, seg;

        @SuppressWarnings("unchecked")
        HLD(int nodes) {
            n = nodes; timer = 0;
            adj = new ArrayList[n + 1];
            for (int i = 0; i <= n; i++) adj[i] = new ArrayList<>();
            parent = new int[n + 1]; depth = new int[n + 1];
            heavy = new int[n + 1]; Arrays.fill(heavy, -1);
            head = new int[n + 1]; pos = new int[n + 1];
            sz = new int[n + 1]; seg = new int[4 * n + 1];
        }

        void addEdge(int u, int v) {
            adj[u].add(v); adj[v].add(u);
        }

        int dfs1(int v, int p, int d) {
            sz[v] = 1; parent[v] = p; depth[v] = d;
            int maxSub = 0;
            for (int u : adj[v]) {
                if (u != p) {
                    int subSz = dfs1(u, v, d + 1);
                    sz[v] += subSz;
                    if (subSz > maxSub) { maxSub = subSz; heavy[v] = u; }
                }
            }
            return sz[v];
        }

        void dfs2(int v, int p, int h) {
            head[v] = h; pos[v] = ++timer;
            if (heavy[v] != -1) dfs2(heavy[v], v, h);
            for (int u : adj[v]) {
                if (u != p && u != heavy[v]) dfs2(u, v, u);
            }
        }

        void build() {
            dfs1(1, 1, 0);
            dfs2(1, 1, 1);
        }

        void updateSeg(int node, int l, int r, int idx, int val) {
            if (l == r) { seg[node] = val; return; }
            int mid = l + (r - l) / 2;
            if (idx <= mid) updateSeg(2 * node, l, mid, idx, val);
            else updateSeg(2 * node + 1, mid + 1, r, idx, val);
            seg[node] = seg[2 * node] + seg[2 * node + 1];
        }

        int querySeg(int node, int l, int r, int ql, int qr) {
            if (ql > r || qr < l) return 0;
            if (ql <= l && r <= qr) return seg[node];
            int mid = l + (r - l) / 2;
            return querySeg(2 * node, l, mid, ql, qr) + querySeg(2 * node + 1, mid + 1, r, ql, qr);
        }

        void update(int v, int val) {
            updateSeg(1, 1, n, pos[v], val);
        }

        int query(int u, int v) {
            int res = 0;
            while (head[u] != head[v]) {
                if (depth[head[u]] < depth[head[v]]) { int temp = u; u = v; v = temp; }
                res += querySeg(1, 1, n, pos[head[u]], pos[u]);
                u = parent[head[u]];
            }
            if (depth[u] > depth[v]) { int temp = u; u = v; v = temp; }
            res += querySeg(1, 1, n, pos[u], pos[v]);
            return res;
        }
    }

    public static void main(String[] args) {
        HLD hld = new HLD(5);
        hld.addEdge(1, 2); hld.addEdge(1, 3);
        hld.addEdge(3, 4); hld.addEdge(3, 5);
        hld.build();

        hld.update(1, 10); hld.update(2, 20); hld.update(3, 30);
        hld.update(4, 40); hld.update(5, 50);

        System.out.println("Path sum 2 to 5: " + hld.query(2, 5));
    }
}
```

### Python
```python
import sys
sys.setrecursionlimit(200000)

class HLD:
    def __init__(self, n):
        self.n = n
        self.adj = [[] for _ in range(n + 1)]
        self.parent = [0] * (n + 1)
        self.depth = [0] * (n + 1)
        self.heavy = [-1] * (n + 1)
        self.head = [0] * (n + 1)
        self.pos = [0] * (n + 1)
        self.sz = [0] * (n + 1)
        self.seg = [0] * (4 * n + 1)
        self.timer = 0

    def add_edge(self, u, v):
        self.adj[u].append(v)
        self.adj[v].append(u)

    def dfs1(self, v, p, d):
        self.sz[v] = 1
        self.parent[v] = p
        self.depth[v] = d
        max_sub = 0
        for u in self.adj[v]:
            if u != p:
                sub_sz = self.dfs1(u, v, d + 1)
                self.sz[v] += sub_sz
                if sub_sz > max_sub:
                    max_sub = sub_sz
                    self.heavy[v] = u
        return self.sz[v]

    def dfs2(self, v, p, h):
        self.head[v] = h
        self.timer += 1
        self.pos[v] = self.timer
        if self.heavy[v] != -1:
            self.dfs2(self.heavy[v], v, h)
        for u in self.adj[v]:
            if u != p and u != self.heavy[v]:
                self.dfs2(u, v, u)

    def build(self):
        self.dfs1(1, 1, 0)
        self.dfs2(1, 1, 1)

    def update_seg(self, node, l, r, idx, val):
        if l == r:
            self.seg[node] = val
            return
        mid = (l + r) // 2
        if idx <= mid:
            self.update_seg(2 * node, l, mid, idx, val)
        else:
            self.update_seg(2 * node + 1, mid + 1, r, idx, val)
        self.seg[node] = self.seg[2 * node] + self.seg[2 * node + 1]

    def query_seg(self, node, l, r, ql, qr):
        if ql > r or qr < l:
            return 0
        if ql <= l and r <= qr:
            return self.seg[node]
        mid = (l + r) // 2
        return self.query_seg(2 * node, l, mid, ql, qr) + self.query_seg(2 * node + 1, mid + 1, r, ql, qr)

    def update(self, v, val):
        self.update_seg(1, 1, self.n, self.pos[v], val)

    def query(self, u, v):
        res = 0
        while self.head[u] != self.head[v]:
            if self.depth[self.head[u]] < self.depth[self.head[v]]:
                u, v = v, u
            res += self.query_seg(1, 1, self.n, self.pos[self.head[u]], self.pos[u])
            u = self.parent[self.head[u]]
        if self.depth[u] > self.depth[v]:
            u, v = v, u
        res += self.query_seg(1, 1, self.n, self.pos[u], self.pos[v])
        return res

if __name__ == "__main__":
    hld = HLD(5)
    hld.add_edge(1, 2)
    hld.add_edge(1, 3)
    hld.add_edge(3, 4)
    hld.add_edge(3, 5)
    hld.build()

    hld.update(1, 10)
    hld.update(2, 20)
    hld.update(3, 30)
    hld.update(4, 40)
    hld.update(5, 50)

    print(f"Path sum 2 to 5: {hld.query(2, 5)}")
```

### JavaScript
```javascript
class HLD {
    constructor(n) {
        this.n = n;
        this.timer = 0;
        this.adj = Array.from({length: n + 1}, () => []);
        this.parent = new Array(n + 1).fill(0);
        this.depth = new Array(n + 1).fill(0);
        this.heavy = new Array(n + 1).fill(-1);
        this.head = new Array(n + 1).fill(0);
        this.pos = new Array(n + 1).fill(0);
        this.sz = new Array(n + 1).fill(0);
        this.seg = new Array(4 * n + 1).fill(0);
    }

    addEdge(u, v) {
        this.adj[u].push(v);
        this.adj[v].push(u);
    }

    dfs1(v, p, d) {
        this.sz[v] = 1;
        this.parent[v] = p;
        this.depth[v] = d;
        let maxSub = 0;
        for (let u of this.adj[v]) {
            if (u !== p) {
                let subSz = this.dfs1(u, v, d + 1);
                this.sz[v] += subSz;
                if (subSz > maxSub) {
                    maxSub = subSz;
                    this.heavy[v] = u;
                }
            }
        }
        return this.sz[v];
    }

    dfs2(v, p, h) {
        this.head[v] = h;
        this.pos[v] = ++this.timer;
        if (this.heavy[v] !== -1) this.dfs2(this.heavy[v], v, h);
        for (let u of this.adj[v]) {
            if (u !== p && u !== this.heavy[v]) this.dfs2(u, v, u);
        }
    }

    build() {
        this.dfs1(1, 1, 0);
        this.dfs2(1, 1, 1);
    }

    updateSeg(node, l, r, idx, val) {
        if (l === r) { this.seg[node] = val; return; }
        let mid = Math.floor((l + r) / 2);
        if (idx <= mid) this.updateSeg(2 * node, l, mid, idx, val);
        else this.updateSeg(2 * node + 1, mid + 1, r, idx, val);
        this.seg[node] = this.seg[2 * node] + this.seg[2 * node + 1];
    }

    querySeg(node, l, r, ql, qr) {
        if (ql > r || qr < l) return 0;
        if (ql <= l && r <= qr) return this.seg[node];
        let mid = Math.floor((l + r) / 2);
        return this.querySeg(2 * node, l, mid, ql, qr) + this.querySeg(2 * node + 1, mid + 1, r, ql, qr);
    }

    update(v, val) {
        this.updateSeg(1, 1, this.n, this.pos[v], val);
    }

    query(u, v) {
        let res = 0;
        while (this.head[u] !== this.head[v]) {
            if (this.depth[this.head[u]] < this.depth[this.head[v]]) {
                [u, v] = [v, u];
            }
            res += this.querySeg(1, 1, this.n, this.pos[this.head[u]], this.pos[u]);
            u = this.parent[this.head[u]];
        }
        if (this.depth[u] > this.depth[v]) {
            [u, v] = [v, u];
        }
        res += this.querySeg(1, 1, this.n, this.pos[u], this.pos[v]);
        return res;
    }
}

// Demo
const hld = new HLD(5);
hld.addEdge(1, 2); hld.addEdge(1, 3);
hld.addEdge(3, 4); hld.addEdge(3, 5);
hld.build();

hld.update(1, 10); hld.update(2, 20); hld.update(3, 30);
hld.update(4, 40); hld.update(5, 50);

console.log(`Path sum 2 to 5: ${hld.query(2, 5)}`);
```

## 10. Code Explanation
- **dfs1**: Initializes subtree sizes, parent pointers, depth, and finds the heavy child (the child with the largest subtree).
- **dfs2**: Assigns positions (`pos`) sequentially. It visits the heavy child first, ensuring all nodes on a heavy chain have contiguous `pos` indices. It also tracks the `head` of the heavy chain for each node.
- **Segment Tree**: A standard array-backed segment tree that supports point updates and range sum queries.
- **query(u, v)**: Navigates up the tree. We check if $u$ and $v$ are on the same heavy chain (`head[u] == head[v]`). If not, we take the node with the deeper heavy chain head, query its path to the head of its chain, add the result, and jump to the parent of the head. When they are on the same chain, we do a single final query between them.

## 11. Interactive Demo Description
An interactive demo for Heavy-Light Decomposition would feature a visual tree where users can add nodes or change edge weights. 
1. **Visualization Toggle:** A button to highlight "Heavy Edges" in bold red and "Light Edges" in dashed blue.
2. **Flattened View:** A panel showing the 1D Array (Segment Tree) and how heavy chains map contiguously to it.
3. **Query Animation:** When a user selects two nodes to query, the tree highlights the path. Simultaneously, the 1D Array highlights the segments being queried, demonstrating the $O(\log n)$ chain jumps and segment tree lookups.

## 12. Dry Run
Let's trace `query(2, 5)` on the provided 5-node tree:
Edges: (1-2), (1-3), (3-4), (3-5)
Values: 1:10, 2:20, 3:30, 4:40, 5:50

**dfs1 & dfs2 Initialization:**
- Node 1: sz=5, heavy=3
- Node 3: sz=3, heavy=5
- Node 5: sz=1, heavy=-1
- Node 2: sz=1, heavy=-1
- Node 4: sz=1, heavy=-1
Chains (head): {1, 3, 5} have head 1. {2} has head 2. {4} has head 4.
Pos in SegTree: 1->1, 3->2, 5->3, 2->4, 4->5.

**Query(2, 5):**
- `head[2] = 2` (depth 1), `head[5] = 1` (depth 0).
- `head[2] != head[5]`. `depth[head[2]] > depth[head[5]]`, so we query for node 2 to `head[2]`.
  - `querySeg(pos[2], pos[2])` -> pos=4. Value = 20.
  - `u = parent[head[2]] = parent[2] = 1`.
- `head[1] = 1`, `head[5] = 1`. They match!
- Now `u = 1`, `v = 5`.
- We query from `pos[1]` to `pos[5]` -> positions 1 to 3.
  - Values at pos 1, 2, 3 are for nodes 1, 3, 5 -> 10 + 30 + 50 = 90.
- Total sum = 20 + 90 = 110.

| Step | u | v | head[u] | head[v] | Action | Result Accumulated |
|---|---|---|---|---|---|---|
| 1 | 2 | 5 | 2 | 1 | Query SegTree for chain {2}, jump `u` to 1 | 20 |
| 2 | 1 | 5 | 1 | 1 | Query SegTree for {1,3,5} from depth 0 to 2 | 20 + 90 = 110 |

## 13. Time & Space Complexity

| Metric | Complexity | Reason |
|---|---|---|
| **Best Time (Query)** | $O(1)$ | If both nodes are exactly the same or directly adjacent in the segment tree. |
| **Average Time (Query)** | $O(\log^2 n)$ | Jumping up chains takes $O(\log n)$ and querying SegTree takes $O(\log n)$. |
| **Worst Time (Query)** | $O(\log^2 n)$ | Max number of light edges on a path is $\log n$. Each heavy chain takes $O(\log n)$ to query. |
| **Space Complexity** | $O(n)$ | Arrays for parent, depth, heavy, head, pos, sz, and SegTree all scale linearly. |

## 14. Advantages
- **Optimal Path Queries:** Reduces $O(n)$ path queries to $O(\log^2 n)$ or even $O(\log n)$ for certain operations.
- **Dynamic Updates:** Can easily handle updates to vertex or edge weights.
- **Versatility:** Can be combined with various data structures (Segment Trees, Fenwick Trees, Treaps) for diverse query types.

## 15. Disadvantages
- **High Implementation Complexity:** Long, error-prone code requiring two DFS traversals plus a custom data structure.
- **Memory Overhead:** Requires multiple auxiliary arrays (`parent`, `head`, `depth`, `sz`, `heavy`, `pos`) which consume significant memory.
- **Constant Factor:** The $O(\log^2 n)$ comes with a relatively high constant factor due to tree traversals and recursive calls.

## 16. Applications
- Subtree sum/max and path sum/max queries simultaneously.
- Used in competitive programming (e.g., IOI, ICPC) for advanced tree data structure problems.
- Processing hierarchical data where updates and aggregate queries happen intermixed.

## 17. Common Mistakes
- **Incorrect SegTree Mapping:** Forgetting to use `pos[u]` when updating or querying the segment tree. `u` is the original node ID; `pos[u]` is its location in the flattened 1D array.
- **Swapping `u` and `v` incorrectly:** During the query phase, always ensure you jump the node whose `head` is deeper. If you check `depth[u] < depth[v]` instead of `depth[head[u]] < depth[head[v]]`, you'll jump the wrong chain.
- **LCA Missing:** If doing edge-based queries instead of vertex-based, the LCA should not be included in the final segment tree query because the edge connecting the LCA to its parent isn't on the path.

## 18. Interview Questions
1. **What dictates whether an edge is heavy or light?**
   *Answer:* The edge leading to the child with the maximum subtree size is heavy.
2. **What is the maximum number of light edges on a path from root to leaf?**
   *Answer:* $O(\log n)$. Every light edge traversed halves the remaining subtree size.
3. **Can we do subtree queries with HLD?**
   *Answer:* Yes, `dfs2` ensures that an entire subtree is mapped to a contiguous range in the 1D array.
4. **How do we handle edge weights instead of node weights?**
   *Answer:* Push the edge weight to the deeper node of the edge. During a query, exclude the LCA from the final segment.
5. **Why is a Segment Tree used with HLD?**
   *Answer:* HLD flattens chains into contiguous arrays; Segment Trees excel at contiguous array queries and updates.
6. **Can HLD be done iteratively?**
   *Answer:* Yes, BFS can compute depths/parents, and reverse BFS order can compute subtree sizes to avoid deep recursion.
7. **Is HLD commutative?**
   *Answer:* The queries depend on the Segment Tree. If the SegTree operation is commutative (like sum, max), yes. If non-commutative (like matrix multiplication), order matters and chains must be queried carefully.
8. **What is the space complexity of HLD?**
   *Answer:* $O(n)$ for auxiliary arrays and the segment tree.
9. **How does HLD compare to Link-Cut Trees?**
   *Answer:* Link-Cut Trees support dynamic tree structures (adding/removing edges), while HLD works only on static tree topologies.
10. **Does HLD compute the LCA?**
    *Answer:* Yes, the query loop naturally terminates with the LCA as the highest reached node.

## 19. Practice Problems
- **Easy:** Given a tree, answer queries: "Is the edge between $u$ and $v$ heavy or light?"
- **Medium:** Path Sum and Path Max Queries with point updates (Standard HLD).
- **Hard:** Path queries with Range Updates (Requires Lazy Propagation in the Segment Tree).

## 20. Related Algorithms
- **Centroid Decomposition:** Decomposes a tree for path queries that do not require specific directionality, in $O(\log n)$ layers.
- **Link-Cut Tree:** Handles dynamic forests (adding/cutting edges) while supporting path queries.
- **Euler Tour Technique:** Flattens a tree into an array for subtree queries, but is less efficient for general path queries.

## 21. Summary
Heavy-Light Decomposition is an elegant technique that bridges the gap between tree traversals and 1D data structures. By isolating the heaviest chains in a tree, it bounds the number of "jumps" required to travel between any two nodes. While structurally complex, it serves as an indispensable tool for optimally handling dynamic path updates and queries on static trees.

## 22. Quiz

**Q1: What determines a heavy child?**
A) The child with the largest value.
B) The child with the largest subtree size.
C) The child with the deepest depth.
D) The child with the most children.
*Correct Answer: B*
*Explanation: The heavy child is defined purely by the size of the subtree rooted at it.*

**Q2: What is the maximum number of light edges from the root to any leaf?**
A) $O(1)$
B) $O(\sqrt{n})$
C) $O(\log n)$
D) $O(n)$
*Correct Answer: C*
*Explanation: Moving down a light edge implies the child's subtree size is at most half of the parent's, leading to at most $\log n$ light edges.*

**Q3: Which data structure is most commonly paired with HLD?**
A) Hash Table
B) Priority Queue
C) Segment Tree
D) Bloom Filter
*Correct Answer: C*
*Explanation: Segment Trees are used over the flattened arrays created by HLD for $O(\log n)$ queries.*

**Q4: Time complexity for a path query in standard HLD (with SegTree)?**
A) $O(1)$
B) $O(\log n)$
C) $O(\log^2 n)$
D) $O(n)$
*Correct Answer: C*
*Explanation: $O(\log n)$ chains to jump, and $O(\log n)$ per Segment Tree query.*

**Q5: Can HLD support changing the structure of the tree (adding/removing edges)?**
A) Yes, optimally.
B) No, it requires rebuilding.
C) Yes, but only leaves.
D) No, trees cannot be changed ever.
*Correct Answer: B*
*Explanation: HLD is for static topologies. Link-Cut Trees are used for dynamic topologies.*

**Q6: What does the `head` array store in HLD?**
A) The root of the entire tree.
B) The top-most node of the current heavy chain.
C) The parent of the node.
D) The heaviest child.
*Correct Answer: B*
*Explanation: The `head` is used to jump up heavy chains efficiently.*

**Q7: In HLD, contiguous Segment Tree indices are guaranteed for:**
A) Nodes at the same depth.
B) Nodes in the same subtree.
C) Nodes on the same heavy chain.
D) Both B and C.
*Correct Answer: D*
*Explanation: `dfs2` visits the heavy child first, then other children, ensuring both heavy chains and subtrees are contiguous in the 1D array.*

**Q8: If queries are on edge weights, where do we store the weight?**
A) At the parent node.
B) At the deeper node of the edge.
C) At the root node.
D) In a separate hash map.
*Correct Answer: B*
*Explanation: Every node except the root has exactly one parent edge, so we store the edge weight in the child node.*

**Q9: When jumping chains in a query from $u$ to $v$, which node do we move up first?**
A) The one with the smaller ID.
B) The one with the larger depth.
C) The one whose heavy chain head is deeper.
D) The one with the heavy child.
*Correct Answer: C*
*Explanation: We jump from the chain that is lower down in the tree to avoid overshooting the LCA.*

**Q10: Why do we need `dfs1` before `dfs2`?**
A) To assign segment tree values.
B) To calculate subtree sizes and find the heavy child.
C) To balance the tree.
D) To find the shortest path.
*Correct Answer: B*
*Explanation: `dfs1` gathers the subtree size information necessary to determine the heavy child, which `dfs2` uses to form heavy chains.*
