# B-Tree

## 1. Introduction
A B-Tree is a self-balancing search tree data structure that maintains sorted data and allows searches, sequential access, insertions, and deletions in logarithmic time. Unlike a binary search tree, where every node has at most two children, a B-Tree node can have more than two children. It is generalized to keep its leaves at the same depth.

**Real-world analogy:**
Imagine a multi-level index in a library catalog system or a phone book where entries are grouped together in blocks (pages). Instead of opening a single book (node) and picking left or right, you look at a block of ordered names, find the right gap between two names, and go to the specific cabinet (child node) that contains the next level of names. This minimizes the number of cabinets you have to open to find a specific entry.

## 2. Why Use This Algorithm?
B-Trees are optimized for systems that read and write large blocks of data, such as disk drives. Since disk access is significantly slower than RAM access, the B-Tree aims to minimize disk I/O operations by storing many keys in a single node, thus reducing the height of the tree. A shallower tree means fewer disk accesses to reach the desired data.

## 3. Real-World Applications
- **Databases:** Relational database management systems (like MySQL, PostgreSQL) use B-Trees and their variants (B+ Trees) to store indexes.
- **File Systems:** Used in file systems (e.g., NTFS, ext4, HFS+) to quickly locate files on a disk.
- **Key-Value Stores:** Some NoSQL databases use B-Trees for rapid key retrieval.

## 4. Prerequisites
To understand B-Trees, you should be familiar with:
- Binary Search Trees (BSTs)
- Self-balancing trees (like AVL or Red-Black trees)
- Arrays and pointers
- Disk I/O concepts (helpful for understanding the motivation behind B-Trees)

## 5. Visualization
```text
Example of a B-Tree (Degree t=2)
                     [ 10 , 20 ]
                   /      |      \
            [ 5, 8 ]  [ 15, 18 ]  [ 25, 30 ]
```
Every node can contain multiple keys (up to 2t-1) and have multiple children (up to 2t).

## 6. How It Works
A B-Tree of minimum degree `t` (where `t >= 2`) satisfies the following properties:
1. Every node has at most `2t - 1` keys.
2. Every node (except the root) has at least `t - 1` keys.
3. Every internal node with `k` keys has exactly `k + 1` children.
4. All leaves appear on the same level.
5. Keys within a node are stored in ascending order.
6. The keys in the child nodes are strictly bounded by the keys in the parent node.

**Insertion:**
- Find the appropriate leaf node.
- If the leaf is not full, insert the key in sorted order.
- If the leaf is full, split the node into two, moving the median key up to the parent. This splitting can cascade up to the root, potentially increasing the tree's height.

## 7. Step-by-Step Algorithm
**Insertion Algorithm:**
1. Check if the root is full (contains `2t - 1` keys).
2. If full, allocate a new root, make the old root its child, and split the old root.
3. Call `insertNonFull` on the root.
4. In `insertNonFull(node, key)`:
   - If `node` is a leaf, insert `key` into its correct sorted position.
   - If `node` is not a leaf, find the child pointer where `key` belongs.
   - Read the child node. If it is full, split it.
   - Recurse into the appropriate child.

**Search Algorithm:**
1. Start at the root.
2. Perform a linear or binary search within the node's keys to find the first key greater than or equal to the target.
3. If the key matches the target, return the node and index.
4. If the node is a leaf and key is not found, return null.
5. Otherwise, recursively search the corresponding child node.

## 8. Pseudocode
```text
B-Tree-Search(x, k)
  i = 1
  while i <= x.n and k > x.key[i]
      i = i + 1
  if i <= x.n and k == x.key[i]
      return (x, i)
  if x.leaf
      return NIL
  else
      Disk-Read(x.c[i])
      return B-Tree-Search(x.c[i], k)

B-Tree-Split-Child(x, i, y)
  z = Allocate-Node()
  z.leaf = y.leaf
  z.n = t - 1
  for j = 1 to t - 1
      z.key[j] = y.key[j+t]
  if not y.leaf
      for j = 1 to t
          z.c[j] = y.c[j+t]
  y.n = t - 1
  for j = x.n + 1 downto i + 1
      x.c[j+1] = x.c[j]
  x.c[i+1] = z
  for j = x.n downto i
      x.key[j+1] = x.key[j]
  x.key[i] = y.key[t]
  x.n = x.n + 1
  Disk-Write(y)
  Disk-Write(z)
  Disk-Write(x)
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>

#define T 2

typedef struct BTreeNode {
    int *keys;
    int t;
    struct BTreeNode **C;
    int n;
    bool leaf;
} BTreeNode;

BTreeNode* createNode(int t, bool leaf) {
    BTreeNode* node = (BTreeNode*)malloc(sizeof(BTreeNode));
    node->t = t;
    node->leaf = leaf;
    node->keys = (int*)malloc((2 * t - 1) * sizeof(int));
    node->C = (BTreeNode**)malloc((2 * t) * sizeof(BTreeNode*));
    node->n = 0;
    return node;
}

void splitChild(BTreeNode* x, int i, BTreeNode* y) {
    BTreeNode* z = createNode(y->t, y->leaf);
    z->n = y->t - 1;
    for (int j = 0; j < y->t - 1; j++)
        z->keys[j] = y->keys[j + y->t];
    if (!y->leaf) {
        for (int j = 0; j < y->t; j++)
            z->C[j] = y->C[j + y->t];
    }
    y->n = y->t - 1;
    for (int j = x->n; j >= i + 1; j--)
        x->C[j + 1] = x->C[j];
    x->C[i + 1] = z;
    for (int j = x->n - 1; j >= i; j--)
        x->keys[j + 1] = x->keys[j];
    x->keys[i] = y->keys[y->t - 1];
    x->n++;
}

void insertNonFull(BTreeNode* x, int k) {
    int i = x->n - 1;
    if (x->leaf) {
        while (i >= 0 && x->keys[i] > k) {
            x->keys[i + 1] = x->keys[i];
            i--;
        }
        x->keys[i + 1] = k;
        x->n++;
    } else {
        while (i >= 0 && x->keys[i] > k)
            i--;
        if (x->C[i + 1]->n == 2 * x->t - 1) {
            splitChild(x, i + 1, x->C[i + 1]);
            if (x->keys[i + 1] < k)
                i++;
        }
        insertNonFull(x->C[i + 1], k);
    }
}

BTreeNode* insert(BTreeNode* root, int k, int t) {
    if (root == NULL) {
        root = createNode(t, true);
        root->keys[0] = k;
        root->n = 1;
        return root;
    }
    if (root->n == 2 * t - 1) {
        BTreeNode* s = createNode(t, false);
        s->C[0] = root;
        splitChild(s, 0, root);
        insertNonFull(s, k);
        return s;
    } else {
        insertNonFull(root, k);
        return root;
    }
}

void traverse(BTreeNode* root) {
    if (root != NULL) {
        int i;
        for (i = 0; i < root->n; i++) {
            if (!root->leaf)
                traverse(root->C[i]);
            printf("%d ", root->keys[i]);
        }
        if (!root->leaf)
            traverse(root->C[i]);
    }
}

int main() {
    BTreeNode* root = NULL;
    int t = 2;
    int keys[] = {10, 20, 5, 6, 12, 30, 7, 17};
    int n = sizeof(keys) / sizeof(keys[0]);
    for (int i = 0; i < n; i++) {
        root = insert(root, keys[i], t);
    }
    printf("B-Tree traversal: ");
    traverse(root);
    printf("\n");
    return 0;
}
```

### C++
```cpp
#include <iostream>
using namespace std;

class BTreeNode {
    int *keys;
    int t;
    BTreeNode **C;
    int n;
    bool leaf;
public:
    BTreeNode(int _t, bool _leaf);
    void insertNonFull(int k);
    void splitChild(int i, BTreeNode *y);
    void traverse();
    friend class BTree;
};

class BTree {
    BTreeNode *root;
    int t;
public:
    BTree(int _t) { root = NULL; t = _t; }
    void traverse() { if (root != NULL) root->traverse(); }
    void insert(int k);
};

BTreeNode::BTreeNode(int t1, bool leaf1) {
    t = t1;
    leaf = leaf1;
    keys = new int[2*t-1];
    C = new BTreeNode *[2*t];
    n = 0;
}

void BTreeNode::traverse() {
    int i;
    for (i = 0; i < n; i++) {
        if (leaf == false) C[i]->traverse();
        cout << " " << keys[i];
    }
    if (leaf == false) C[i]->traverse();
}

void BTree::insert(int k) {
    if (root == NULL) {
        root = new BTreeNode(t, true);
        root->keys[0] = k;
        root->n = 1;
    } else {
        if (root->n == 2*t-1) {
            BTreeNode *s = new BTreeNode(t, false);
            s->C[0] = root;
            s->splitChild(0, root);
            s->insertNonFull(k);
            root = s;
        } else {
            root->insertNonFull(k);
        }
    }
}

void BTreeNode::insertNonFull(int k) {
    int i = n-1;
    if (leaf == true) {
        while (i >= 0 && keys[i] > k) {
            keys[i+1] = keys[i];
            i--;
        }
        keys[i+1] = k;
        n = n+1;
    } else {
        while (i >= 0 && keys[i] > k) i--;
        if (C[i+1]->n == 2*t-1) {
            splitChild(i+1, C[i+1]);
            if (keys[i+1] < k) i++;
        }
        C[i+1]->insertNonFull(k);
    }
}

void BTreeNode::splitChild(int i, BTreeNode *y) {
    BTreeNode *z = new BTreeNode(y->t, y->leaf);
    z->n = t - 1;
    for (int j = 0; j < t-1; j++) z->keys[j] = y->keys[j+t];
    if (y->leaf == false) {
        for (int j = 0; j < t; j++) z->C[j] = y->C[j+t];
    }
    y->n = t - 1;
    for (int j = n; j >= i+1; j--) C[j+1] = C[j];
    C[i+1] = z;
    for (int j = n-1; j >= i; j--) keys[j+1] = keys[j];
    keys[i] = y->keys[t-1];
    n = n + 1;
}

int main() {
    BTree t(2);
    int keys[] = {10, 20, 5, 6, 12, 30, 7, 17};
    for (int key : keys) t.insert(key);
    cout << "B-Tree traversal:";
    t.traverse();
    cout << endl;
    return 0;
}
```

### Java
```java
class BTree {
    private int T;
    private Node root;

    private class Node {
        int n;
        int key[] = new int[2 * T - 1];
        Node child[] = new Node[2 * T];
        boolean leaf = true;
    }

    public BTree(int t) {
        this.T = t;
        root = new Node();
        root.n = 0;
        root.leaf = true;
    }

    private void splitChild(Node x, int i, Node y) {
        Node z = new Node();
        z.leaf = y.leaf;
        z.n = T - 1;
        for (int j = 0; j < T - 1; j++) z.key[j] = y.key[j + T];
        if (!y.leaf) {
            for (int j = 0; j < T; j++) z.child[j] = y.child[j + T];
        }
        y.n = T - 1;
        for (int j = x.n; j >= i + 1; j--) x.child[j + 1] = x.child[j];
        x.child[i + 1] = z;
        for (int j = x.n - 1; j >= i; j--) x.key[j + 1] = x.key[j];
        x.key[i] = y.key[T - 1];
        x.n++;
    }

    private void insertNonFull(Node x, int k) {
        int i = x.n - 1;
        if (x.leaf) {
            while (i >= 0 && x.key[i] > k) {
                x.key[i + 1] = x.key[i];
                i--;
            }
            x.key[i + 1] = k;
            x.n++;
        } else {
            while (i >= 0 && x.key[i] > k) i--;
            i++;
            if (x.child[i].n == 2 * T - 1) {
                splitChild(x, i, x.child[i]);
                if (x.key[i] < k) i++;
            }
            insertNonFull(x.child[i], k);
        }
    }

    public void insert(int k) {
        Node r = root;
        if (r.n == 2 * T - 1) {
            Node s = new Node();
            root = s;
            s.leaf = false;
            s.n = 0;
            s.child[0] = r;
            splitChild(s, 0, r);
            insertNonFull(s, k);
        } else {
            insertNonFull(r, k);
        }
    }

    public void traverse() {
        if (root != null) traverse(root);
    }

    private void traverse(Node x) {
        int i;
        for (i = 0; i < x.n; i++) {
            if (!x.leaf) traverse(x.child[i]);
            System.out.print(x.key[i] + " ");
        }
        if (!x.leaf) traverse(x.child[i]);
    }

    public static void main(String[] args) {
        BTree btree = new BTree(2);
        int[] keys = {10, 20, 5, 6, 12, 30, 7, 17};
        for (int key : keys) btree.insert(key);
        System.out.print("B-Tree traversal: ");
        btree.traverse();
        System.out.println();
    }
}
```

### Python
```python
class BTreeNode:
    def __init__(self, leaf=False):
        self.leaf = leaf
        self.keys = []
        self.child = []

class BTree:
    def __init__(self, t):
        self.root = BTreeNode(True)
        self.t = t

    def insert(self, k):
        root = self.root
        if len(root.keys) == (2 * self.t) - 1:
            temp = BTreeNode()
            self.root = temp
            temp.child.insert(0, root)
            self.split_child(temp, 0)
            self.insert_non_full(temp, k)
        else:
            self.insert_non_full(root, k)

    def insert_non_full(self, x, k):
        i = len(x.keys) - 1
        if x.leaf:
            x.keys.append(None)
            while i >= 0 and k < x.keys[i]:
                x.keys[i + 1] = x.keys[i]
                i -= 1
            x.keys[i + 1] = k
        else:
            while i >= 0 and k < x.keys[i]:
                i -= 1
            i += 1
            if len(x.child[i].keys) == (2 * self.t) - 1:
                self.split_child(x, i)
                if k > x.keys[i]:
                    i += 1
            self.insert_non_full(x.child[i], k)

    def split_child(self, x, i):
        t = self.t
        y = x.child[i]
        z = BTreeNode(y.leaf)
        x.child.insert(i + 1, z)
        x.keys.insert(i, y.keys[t - 1])
        z.keys = y.keys[t: (2 * t) - 1]
        y.keys = y.keys[0: t - 1]
        if not y.leaf:
            z.child = y.child[t: 2 * t]
            y.child = y.child[0: t]

    def print_tree(self, x, l=0):
        print("Level", l, " ", len(x.keys), ":", end=" ")
        for i in x.keys:
            print(i, end=" ")
        print()
        l += 1
        if len(x.child) > 0:
            for i in x.child:
                self.print_tree(i, l)

if __name__ == '__main__':
    B = BTree(2)
    for i in [10, 20, 5, 6, 12, 30, 7, 17]:
        B.insert(i)
    B.print_tree(B.root)
```

### JavaScript
```javascript
class BTreeNode {
    constructor(t, leaf) {
        this.t = t;
        this.leaf = leaf;
        this.keys = [];
        this.child = [];
    }
}

class BTree {
    constructor(t) {
        this.t = t;
        this.root = new BTreeNode(t, true);
    }

    traverse(x = this.root) {
        let res = "";
        let i;
        for (i = 0; i < x.keys.length; i++) {
            if (!x.leaf) {
                res += this.traverse(x.child[i]);
            }
            res += x.keys[i] + " ";
        }
        if (!x.leaf) {
            res += this.traverse(x.child[i]);
        }
        return res;
    }

    splitChild(x, i) {
        let t = this.t;
        let y = x.child[i];
        let z = new BTreeNode(t, y.leaf);
        x.child.splice(i + 1, 0, z);
        x.keys.splice(i, 0, y.keys[t - 1]);
        z.keys = y.keys.splice(t - 1, t);
        z.keys.shift(); // remove the promoted key
        
        if (!y.leaf) {
            z.child = y.child.splice(t, t);
        }
    }

    insertNonFull(x, k) {
        let i = x.keys.length - 1;
        if (x.leaf) {
            while (i >= 0 && x.keys[i] > k) {
                i--;
            }
            x.keys.splice(i + 1, 0, k);
        } else {
            while (i >= 0 && x.keys[i] > k) {
                i--;
            }
            i++;
            if (x.child[i].keys.length === 2 * this.t - 1) {
                this.splitChild(x, i);
                if (x.keys[i] < k) {
                    i++;
                }
            }
            this.insertNonFull(x.child[i], k);
        }
    }

    insert(k) {
        let root = this.root;
        if (root.keys.length === 2 * this.t - 1) {
            let s = new BTreeNode(this.t, false);
            this.root = s;
            s.child[0] = root;
            this.splitChild(s, 0);
            this.insertNonFull(s, k);
        } else {
            this.insertNonFull(root, k);
        }
    }
}

// Demo
const btree = new BTree(2);
const keys = [10, 20, 5, 6, 12, 30, 7, 17];
for (let key of keys) {
    btree.insert(key);
}
console.log("B-Tree traversal:", btree.traverse());
```

## 10. Code Explanation
- **Node Structure:** Each node stores an array of keys and an array of child pointers. A boolean flag `leaf` dictates if it's at the bottom level.
- **Insert:** Always starts at the root. If the root is full (contains `2t-1` elements), it splits it and increases the tree height.
- **InsertNonFull:** Finds the right place for the new key. If it reaches a leaf, it performs a simple array insertion. If it traverses an internal node, it ensures no child it steps into is full.
- **SplitChild:** Extracts the right half of a full node's keys and children to form a new node. The median key is pushed up to the parent.

## 11. Interactive Demo Description
An interactive B-Tree demo should allow the user to adjust the degree `t`. It visually represents nodes as rectangular blocks containing horizontal arrays of keys. 
- When inserting a key, the algorithm should highlight the search path.
- If a node is full, an animation should show the node splitting in half and the median key elevating to the parent node.
- Controls should include `Play`, `Pause`, `Step Forward`, and `Reset`.

## 12. Dry Run
Let's build a B-Tree of degree `t = 2` (Max keys = 3).
Insert sequence: `10, 20, 5, 6, 12`

**Step 1:** Insert 10
`[ 10 ]`

**Step 2:** Insert 20
`[ 10, 20 ]`

**Step 3:** Insert 5
`[ 5, 10, 20 ]`

**Step 4:** Insert 6
Node is full! Split `[ 5, 10, 20 ]`. Median `10` goes up.
```text
      [ 10 ]
     /      \
  [ 5, 6 ]  [ 20 ]
```
(6 is inserted into the left child)

**Step 5:** Insert 12
`12` is compared with root `10`. Goes to the right child `[ 20 ]`.
Right child has space. Insert 12.
```text
       [ 10 ]
      /      \
   [ 5, 6 ]  [ 12, 20 ]
```

| Step | Action | Node Affected | Result Array / Tree Structure |
|---|---|---|---|
| 1 | Insert 10 | Root | `[10]` |
| 2 | Insert 20 | Root | `[10, 20]` |
| 3 | Insert 5 | Root | `[5, 10, 20]` |
| 4 | Insert 6 | Root split | Root `[10]`, children `[5,6]` and `[20]` |
| 5 | Insert 12 | Right child | Root `[10]`, children `[5,6]` and `[12, 20]` |

## 13. Time & Space Complexity
| Scenario | Time Complexity | Space Complexity |
|---|---|---|
| **Best Case (Search)** | $O(1)$ | $O(N)$ |
| **Average Case** | $O(\log N)$ | $O(N)$ |
| **Worst Case** | $O(\log N)$ | $O(N)$ |

**Reasoning:**
- The height of the tree is bounded by $O(\log_t N)$, where `t` is the minimum degree and `N` is the number of elements.
- Each node operation (search, insert, split) takes $O(t)$ time. Since `t` is a constant, it simplifies to $O(\log N)$ overall.
- Memory scales linearly with the number of keys inserted, hence $O(N)$ space.

## 14. Advantages
- Efficient for large amounts of data that don't fit in primary memory.
- Minimizes disk I/O operations because the tree is shallow.
- Self-balancing property ensures stable performance without degeneration.
- Supports both random searches and sequential traversal.

## 15. Disadvantages
- More complex to implement compared to simple Binary Search Trees.
- Space wastage because nodes may not be entirely full (nodes are guaranteed to be at least half full, but the other half might be empty).
- Updates (insertions and deletions) have high overhead due to frequent splitting or merging.

## 16. Applications
- Relational Database indices (e.g., SQLite, PostgreSQL).
- Disk-based data storage and file systems.
- Multilevel indexing applications in large-scale enterprise storage.

## 17. Common Mistakes
- Confusing B-Trees with B+ Trees (in B+ Trees, all data is in the leaves, internal nodes only store keys).
- Forgetting to handle the root split case, which is the only way a B-Tree grows in height.
- Incorrectly assigning child pointers during a node split.
- Failing to properly free memory in lower-level languages like C or C++.

## 18. Interview Questions
1. What is the difference between a B-Tree and a B+ Tree?
2. Why are B-Trees preferred over AVL trees for database indexing?
3. Explain the insertion mechanism in a B-Tree.
4. What is the minimum and maximum number of keys in a B-Tree node of degree `t`?
5. How does a B-Tree ensure it remains balanced?
6. Describe the deletion process in a B-Tree.
7. What happens when the root node of a B-Tree gets full?
8. Why is sequential access faster in a B+ Tree than a B-Tree?
9. Calculate the maximum height of a B-Tree with `N` keys and degree `t`.
10. Is it possible for a B-Tree to have empty internal nodes?

## 19. Practice Problems
- **Easy:** Implement a B-Tree search function.
- **Medium:** Implement B-Tree insertion.
- **Hard:** Implement B-Tree deletion (handling all cases of merging and borrowing).

## 20. Related Algorithms
- **B+ Tree:** A variant where data pointers are only kept in leaf nodes.
- **AVL Tree:** A strict height-balanced binary search tree.
- **Red-Black Tree:** A self-balancing binary search tree often used in memory data structures.
- **2-3 Tree:** A specific form of B-Tree where `t = 2`.

## 21. Summary
The B-Tree is a fundamental data structure designed to maintain large volumes of sorted data across slow storage mediums. By grouping multiple keys into a single node and ensuring a balanced structure, it drastically reduces the number of operations needed to locate any given element.

## 22. Quiz
**Q1: What is the main motivation behind using a B-Tree over a regular Binary Search Tree?**
A) Easier implementation
B) Reducing disk I/O
C) Faster in-memory sorting
D) Lower memory overhead
**Correct Answer:** B) Reducing disk I/O
*Explanation:* B-Trees keep trees shallow by packing more data in nodes, which minimizes slow disk reads.

**Q2: In a B-Tree of minimum degree t=3, what is the maximum number of children a node can have?**
A) 3
B) 5
C) 6
D) 7
**Correct Answer:** C) 6
*Explanation:* The maximum number of children is `2t`, which is `2 * 3 = 6`.

**Q3: Which node is the only one allowed to have fewer than `t-1` keys?**
A) Leaf node
B) Root node
C) Any internal node
D) None
**Correct Answer:** B) Root node
*Explanation:* The root node can have as few as 1 key.

**Q4: How does a B-Tree grow in height?**
A) By adding leaves
B) By splitting the root node
C) By rebalancing every step
D) By merging nodes
**Correct Answer:** B) By splitting the root node
*Explanation:* A B-Tree grows upwards. When the root splits, a new root is created above it.

**Q5: True or False: All leaf nodes in a B-Tree are at the same depth.**
A) True
B) False
**Correct Answer:** A) True
*Explanation:* This is a defining property of a B-Tree, ensuring it is perfectly balanced.

**Q6: What is the time complexity of searching in a B-Tree?**
A) O(1)
B) O(N)
C) O(N log N)
D) O(log N)
**Correct Answer:** D) O(log N)
*Explanation:* The height of the tree is logarithmic relative to the number of nodes.

**Q7: During insertion, if a node is full, what happens?**
A) The new element replaces the oldest
B) The node is split into two and the median key is pushed to the parent
C) The element is rejected
D) The tree is completely rebuilt
**Correct Answer:** B) The node is split into two and the median key is pushed to the parent
*Explanation:* This maintains the properties and balance of the B-Tree.

**Q8: What data structure is a 2-3-4 tree a specific instance of?**
A) AVL Tree
B) B-Tree
C) Trie
D) Heap
**Correct Answer:** B) B-Tree
*Explanation:* A 2-3-4 tree is a B-Tree of order 4 (or degree 2).

**Q9: Why are B-Trees not typically used for simple in-memory data storage compared to Red-Black Trees?**
A) They use too little memory
B) The node splitting and array shifting overhead is less efficient in cache than pointer manipulation in Red-Black Trees
C) They are not self-balancing
D) They cannot hold primitive data types
**Correct Answer:** B) The node splitting and array shifting overhead is less efficient in cache than pointer manipulation in Red-Black Trees
*Explanation:* While great for disks, the array manipulations inside large nodes can be slower in RAM compared to standard binary nodes.

**Q10: Are duplicate keys typically allowed in a standard B-Tree?**
A) Always
B) Never
C) Yes, but they must be placed in adjacent children
D) Usually no, or handled via satellite lists
**Correct Answer:** D) Usually no, or handled via satellite lists
*Explanation:* Standard B-Tree implementations assume unique keys or attach lists of records for duplicates.
