# Lowest Common Ancestor (LCA)

## 1. Introduction
The **Lowest Common Ancestor (LCA)** of two nodes `p` and `q` in a tree is defined as the lowest node in the tree that has both `p` and `q` as descendants (where we allow a node to be a descendant of itself).

**Real-World Analogy**: Imagine a family tree. You and your cousin want to find your closest shared relative. You trace your ancestry back: your parent, your grandparent, etc. Your cousin does the same. The first ancestor you both share in common (in this case, your grandparent) is your Lowest Common Ancestor.

## 2. Why Use This Algorithm?
The LCA algorithm is crucial for understanding relationships and distances within hierarchical data structures. It provides a direct way to find the shortest path between two nodes in a tree, which is exactly the path from the first node up to the LCA, and then down to the second node. 

## 3. Real-World Applications
- **Version Control Systems (Git)**: Finding the "merge base" of two branches to perform a three-way merge.
- **DOM Tree**: Finding the closest common parent element of two nodes in an HTML document to optimize event delegation or UI updates.
- **Taxonomy and Phylogenetics**: Determining the most recent common ancestor of two species in an evolutionary tree.
- **Computer Networks**: Routing packets between two nodes in a network topology structured as a tree.

## 4. Prerequisites
To fully understand the LCA algorithm, you should be familiar with:
- **Binary Trees**: Structure, nodes, left and right children.
- **Tree Traversal (DFS)**: Specifically Post-order or In-order traversal using recursion.
- **Recursion**: Base cases, recursive calls, and returning values up the call stack.

## 5. Visualization

Consider the following binary tree:

```text
        3
      /   \
     5     1
    / \   / \
   6   2 0   8
      / \
     7   4
```

- **LCA of 5 and 1** is **3**.
- **LCA of 5 and 2** is **5** (since a node can be a descendant of itself).
- **LCA of 7 and 8** is **3**.
- **LCA of 7 and 4** is **2**.

## 6. How It Works
The most common approach for a standard binary tree uses a single Depth-First Search (DFS) traversal:
1. We start at the root and traverse down.
2. If we hit a `null` node, we return `null`.
3. If we find either node `p` or node `q`, we return that node to our caller, indicating "I found one of the targets in this subtree."
4. We recursively search the left and right subtrees.
5. Once the left and right searches return, we check their results:
   - If **both** left and right subtrees return a non-null value, it means one target is in the left subtree and the other is in the right subtree. Therefore, the **current node is the LCA**.
   - If only **one** subtree returns a non-null value, it means both targets are in that same subtree (or we only found one target so far). We pass this non-null result up the tree.

## 7. Step-by-Step Algorithm
1. Create a function `findLCA(root, p, q)`.
2. **Base Case 1**: If `root` is `null`, return `null`.
3. **Base Case 2**: If `root` is `p` or `root` is `q`, return `root`.
4. Recursively call `findLCA` for `root.left`, storing the result in `left`.
5. Recursively call `findLCA` for `root.right`, storing the result in `right`.
6. If both `left` and `right` are not `null`, return `root` (this is the LCA).
7. Otherwise, return the non-null value among `left` and `right` (if both are null, return null).

## 8. Pseudocode

```text
FUNCTION findLCA(node, p, q):
    IF node IS NULL:
        RETURN NULL
    
    IF node == p OR node == q:
        RETURN node
        
    left_result = findLCA(node.left, p, q)
    right_result = findLCA(node.right, p, q)
    
    IF left_result IS NOT NULL AND right_result IS NOT NULL:
        RETURN node
        
    IF left_result IS NOT NULL:
        RETURN left_result
    ELSE:
        RETURN right_result
```

## 9. Code Examples

### C

```c
#include <stdio.h>
#include <stdlib.h>

struct TreeNode {
    int val;
    struct TreeNode *left;
    struct TreeNode *right;
};

struct TreeNode* createNode(int val) {
    struct TreeNode* newNode = (struct TreeNode*)malloc(sizeof(struct TreeNode));
    newNode->val = val;
    newNode->left = NULL;
    newNode->right = NULL;
    return newNode;
}

struct TreeNode* lowestCommonAncestor(struct TreeNode* root, struct TreeNode* p, struct TreeNode* q) {
    if (root == NULL || root == p || root == q) {
        return root;
    }
    
    struct TreeNode* left = lowestCommonAncestor(root->left, p, q);
    struct TreeNode* right = lowestCommonAncestor(root->right, p, q);
    
    if (left != NULL && right != NULL) {
        return root;
    }
    
    return left != NULL ? left : right;
}

int main() {
    struct TreeNode* root = createNode(3);
    root->left = createNode(5);
    root->right = createNode(1);
    root->left->left = createNode(6);
    root->left->right = createNode(2);
    root->right->left = createNode(0);
    root->right->right = createNode(8);
    root->left->right->left = createNode(7);
    root->left->right->right = createNode(4);
    
    struct TreeNode* p = root->left; // Node 5
    struct TreeNode* q = root->right; // Node 1
    
    struct TreeNode* lca = lowestCommonAncestor(root, p, q);
    if (lca) printf("LCA of %d and %d is %d\n", p->val, q->val, lca->val);
    
    return 0;
}
```

### C++

```cpp
#include <iostream>

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};

class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (!root || root == p || root == q) return root;
        
        TreeNode* left = lowestCommonAncestor(root->left, p, q);
        TreeNode* right = lowestCommonAncestor(root->right, p, q);
        
        if (left && right) return root;
        
        return left ? left : right;
    }
};

int main() {
    TreeNode* root = new TreeNode(3);
    root->left = new TreeNode(5);
    root->right = new TreeNode(1);
    root->left->left = new TreeNode(6);
    root->left->right = new TreeNode(2);
    root->right->left = new TreeNode(0);
    root->right->right = new TreeNode(8);
    root->left->right->left = new TreeNode(7);
    root->left->right->right = new TreeNode(4);
    
    Solution sol;
    TreeNode* lca = sol.lowestCommonAncestor(root, root->left, root->right);
    if (lca) std::cout << "LCA of 5 and 1 is " << lca->val << std::endl;
    
    return 0;
}
```

### Java

```java
class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode(int x) { val = x; }
}

public class LowestCommonAncestor {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || root == p || root == q) {
            return root;
        }
        
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        
        if (left != null && right != null) {
            return root;
        }
        
        return left != null ? left : right;
    }
    
    public static void main(String[] args) {
        TreeNode root = new TreeNode(3);
        root.left = new TreeNode(5);
        root.right = new TreeNode(1);
        root.left.left = new TreeNode(6);
        root.left.right = new TreeNode(2);
        root.right.left = new TreeNode(0);
        root.right.right = new TreeNode(8);
        root.left.right.left = new TreeNode(7);
        root.left.right.right = new TreeNode(4);
        
        LowestCommonAncestor solution = new LowestCommonAncestor();
        TreeNode lca = solution.lowestCommonAncestor(root, root.left, root.right);
        System.out.println("LCA of 5 and 1 is " + lca.val);
    }
}
```

### Python

```python
class TreeNode:
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None

class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        if not root or root == p or root == q:
            return root
        
        left = self.lowestCommonAncestor(root.left, p, q)
        right = self.lowestCommonAncestor(root.right, p, q)
        
        if left and right:
            return root
            
        return left if left else right

if __name__ == "__main__":
    root = TreeNode(3)
    root.left = TreeNode(5)
    root.right = TreeNode(1)
    root.left.left = TreeNode(6)
    root.left.right = TreeNode(2)
    root.right.left = TreeNode(0)
    root.right.right = TreeNode(8)
    root.left.right.left = TreeNode(7)
    root.left.right.right = TreeNode(4)
    
    sol = Solution()
    lca = sol.lowestCommonAncestor(root, root.left, root.right)
    print(f"LCA of 5 and 1 is {lca.val}")
```

### JavaScript

```javascript
class TreeNode {
    constructor(val) {
        this.val = val;
        this.left = this.right = null;
    }
}

function lowestCommonAncestor(root, p, q) {
    if (root === null || root === p || root === q) {
        return root;
    }
    
    const left = lowestCommonAncestor(root.left, p, q);
    const right = lowestCommonAncestor(root.right, p, q);
    
    if (left !== null && right !== null) {
        return root;
    }
    
    return left !== null ? left : right;
}

// Demo
const root = new TreeNode(3);
root.left = new TreeNode(5);
root.right = new TreeNode(1);
root.left.left = new TreeNode(6);
root.left.right = new TreeNode(2);
root.right.left = new TreeNode(0);
root.right.right = new TreeNode(8);
root.left.right.left = new TreeNode(7);
root.left.right.right = new TreeNode(4);

const lca = lowestCommonAncestor(root, root.left, root.right);
console.log(`LCA of 5 and 1 is ${lca.val}`);
```

## 10. Code Explanation
The core logic relies on post-order traversal (visiting left, then right, then processing the root). 
- `if (!root || root == p || root == q) return root;` ensures that as soon as we find `p` or `q`, we bubble that node back up. It also naturally stops recursion at leaf nodes (returning `null`).
- We then make our recursive calls on the left and right children.
- If both left and right calls return a non-null object, it means `p` is in one subtree and `q` is in the other. Thus, the current `root` is their Lowest Common Ancestor. We return `root`.
- If only one call returns non-null, it implies either both nodes are located in that one subtree (and the returned node is the LCA), or we've only found one of the nodes so far. In either case, we pass that non-null node further up the tree.

## 11. Interactive Demo Description
An ideal interactive demo for the LCA algorithm would feature a drag-and-drop interface to build a binary tree. Users can click to select node `p` and node `q` (highlighted in blue and green). A "Find LCA" button initiates a visual step-through of the DFS traversal. As the recursive function traverses down, edges are highlighted in yellow. When nodes bubble up their results, the path is colored to represent the found nodes. When the LCA is identified, it pulses and turns red, displaying the call stack dynamically on the side to help visualize the recursion unwinding.

## 12. Dry Run

**Target**: Find LCA of node 6 and node 4.
**Tree**:
```text
        3
      /   \
     5     1
    / \
   6   2
      / \
     7   4
```

| Step | Current Node | Call | Result / Action |
|---|---|---|---|
| 1 | 3 | `LCA(3, 6, 4)` | Call `LCA(5, 6, 4)` for left child. |
| 2 | 5 | `LCA(5, 6, 4)` | Call `LCA(6, 6, 4)` for left child. |
| 3 | 6 | `LCA(6, 6, 4)` | Node is `p` (6). Returns 6. |
| 4 | 5 | back in 5 | Left returned 6. Call `LCA(2, 6, 4)` for right child. |
| 5 | 2 | `LCA(2, 6, 4)` | Call `LCA(7, 6, 4)` for left child. |
| 6 | 7 | `LCA(7, 6, 4)` | Leaves return null. Returns null. |
| 7 | 2 | back in 2 | Left returned null. Call `LCA(4, 6, 4)` for right. |
| 8 | 4 | `LCA(4, 6, 4)` | Node is `q` (4). Returns 4. |
| 9 | 2 | back in 2 | Left: null, Right: 4. Returns 4. |
| 10| 5 | back in 5 | Left: 6, Right: 4. **Both non-null!** Returns 5 (LCA).|
| 11| 3 | back in 3 | Left returned 5. Call `LCA(1, 6, 4)` for right child.|
| 12| 1 | `LCA(1, 6, 4)` | Neither 6 nor 4 in this subtree. Returns null. |
| 13| 3 | back in 3 | Left: 5, Right: null. Returns 5. |

**Final Result**: 5

## 13. Time & Space Complexity

| Case | Complexity | Reason |
|---|---|---|
| **Best Case Time** | $O(1)$ | If the root is `p` or `q`, the function returns immediately. |
| **Average Case Time**| $O(N)$ | $N$ is the number of nodes. Might need to traverse most of the tree. |
| **Worst Case Time** | $O(N)$ | In the worst case (skewed tree or targets at the deepest leaves), we visit every node once. |
| **Space Complexity** | $O(H)$ | $H$ is the height of the tree. The implicit call stack requires $O(H)$ space. In a balanced tree, $O(\log N)$. In a worst-case skewed tree, $O(N)$. |

## 14. Advantages
- **Simplicity**: The recursive approach is elegant and concise (just a few lines of code).
- **Generality**: Works on *any* binary tree (does not require a Binary Search Tree property).
- **Efficiency**: Explores the tree in a single pass $O(N)$ time.

## 15. Disadvantages
- **Space Overhead**: Recursive calls use the call stack, which can lead to stack overflow on very deep/skewed trees ($O(N)$ space).
- **No Parent Pointers**: If the nodes have parent pointers, LCA could be found in $O(H)$ time without full traversal, but this standard algorithm requires top-down traversal.

## 16. Applications
- Network routing to find the closest hub connecting two endpoints.
- Computing the distance between two nodes in a tree: `Dist(n1, n2) = Depth(n1) + Depth(n2) - 2 * Depth(LCA(n1, n2))`.
- Determining biological ancestors in taxonomic tree structures.

## 17. Common Mistakes
- **Assuming both nodes are in the tree**: The standard algorithm returns one of the nodes if the other is not present. If there's a risk one node is missing, a separate search is required to verify existence first.
- **Confusing with BST LCA**: In a Binary Search Tree, LCA can be found faster by utilizing the left/right size properties (comparing values). Using the standard BT algorithm on a BST is correct but suboptimal.
- **Handling Null Root improperly**: Forgetting the base case for `null` which causes runtime exceptions.

## 18. Interview Questions
1. How does the LCA algorithm change if the tree is a Binary Search Tree (BST)?
2. What if nodes have a `parent` pointer? How does this change time and space complexity?
3. How would you handle the case where one of the nodes might not exist in the tree?
4. Can you implement the LCA algorithm iteratively without recursion?
5. How would you find the LCA of more than two nodes?
6. Explain how finding the LCA helps in finding the shortest path between two nodes in a tree.
7. What is Binary Lifting and how does it relate to LCA?
8. In an N-ary tree, how would the recursive LCA approach differ?
9. Analyze the space complexity if the tree is perfectly balanced vs. a linked list.
10. Is Tarjan's offline LCA algorithm better than the recursive approach? When would you use it?

## 19. Practice Problems
- **Easy**: Lowest Common Ancestor of a Binary Search Tree (LeetCode 235)
- **Medium**: Lowest Common Ancestor of a Binary Tree (LeetCode 236)
- **Hard**: Lowest Common Ancestor of a Binary Tree III (Nodes have parent pointers) (LeetCode 1650)

## 20. Related Algorithms
- **LCA in BST**: Optimizes the search to $O(H)$ by discarding subtrees based on node values.
- **Tarjan's Offline LCA Algorithm**: Uses Union-Find to answer a batch of LCA queries in nearly $O(1)$ time per query after a single traversal.
- **Binary Lifting**: A technique to answer online LCA queries in $O(\log N)$ time after $O(N \log N)$ preprocessing.
- **RMQ (Range Minimum Query)**: LCA can be reduced to RMQ using Euler tour traversal.

## 21. Summary
The Lowest Common Ancestor (LCA) algorithm efficiently finds the deepest node that is a common ancestor to two given nodes in a tree. Using a simple Depth-First Search recursion, we can solve this for any standard binary tree in $O(N)$ time and $O(H)$ space. While simple, it forms the foundation for many complex tree-related distance and routing algorithms.

## 22. Quiz

**Q1. What is the worst-case time complexity of finding the LCA in a general binary tree using the recursive approach?**
A) $O(1)$
B) $O(\log N)$
C) $O(N)$
D) $O(N \log N)$
**Correct Answer**: C
**Explanation**: In the worst case, you may have to visit every node in the tree to find both target nodes.

**Q2. If `p` is the parent of `q`, what is their LCA?**
A) `q`
B) `p`
C) The root of the tree
D) Depends on the sibling of `q`
**Correct Answer**: B
**Explanation**: A node is considered a descendant of itself, so the higher node `p` is the lowest common ancestor.

**Q3. What traversal method does the standard recursive LCA algorithm implicitly use?**
A) Pre-order
B) In-order
C) Post-order
D) Level-order
**Correct Answer**: C
**Explanation**: It processes the left child, then the right child, and then makes a decision at the current root node based on the children's results (post-order).

**Q4. If both `left` and `right` recursive calls return a non-null value, what does this indicate?**
A) Both target nodes were found in the left subtree.
B) Both target nodes were found in the right subtree.
C) One node is in the left subtree, the other is in the right subtree.
D) The target nodes do not exist in the tree.
**Correct Answer**: C
**Explanation**: Receiving non-null from both sides means the current node is the split point, making it the LCA.

**Q5. How much auxiliary space does the recursive LCA algorithm use in the worst case (skewed tree)?**
A) $O(1)$
B) $O(\log N)$
C) $O(N)$
D) $O(N^2)$
**Correct Answer**: C
**Explanation**: In a skewed tree, the height $H$ is $N$, meaning the call stack will grow to $N$ frames, taking $O(N)$ space.

**Q6. If the tree is a Binary Search Tree (BST), can the LCA be found more efficiently?**
A) Yes, in $O(H)$ time without visiting every node.
B) No, it still requires visiting every node.
C) Yes, in $O(1)$ time.
D) No, BSTs do not support LCA queries.
**Correct Answer**: A
**Explanation**: In a BST, you can use the node values to decide whether to search left, right, or stop, resulting in an $O(H)$ traversal.

**Q7. What happens in the standard algorithm if only node `p` exists in the tree, and `q` is absent?**
A) Returns `null`
B) Returns `q`
C) Returns `p`
D) Throws an exception
**Correct Answer**: C
**Explanation**: As soon as it finds `p`, it returns `p` and stops searching that branch. Since `q` is never found, `p` bubbles all the way up to the top.

**Q8. Which data structure is best for answering MULTIPLE LCA queries quickly on a static tree?**
A) Queue
B) Binary Lifting table or Euler Tour with Segment Tree
C) Hash Map
D) Stack
**Correct Answer**: B
**Explanation**: For multiple queries, preprocessing the tree using Binary Lifting or RMQ over an Euler Tour allows answering queries in $O(\log N)$ or $O(1)$ time.

**Q9. The distance between node `u` and node `v` can be calculated using their LCA. Which formula is correct? (where D is depth/level from root)**
A) `D(u) + D(v) - D(LCA(u,v))`
B) `D(u) + D(v) - 2 * D(LCA(u,v))`
C) `D(u) * D(v) / D(LCA(u,v))`
D) `D(u) - D(v) + D(LCA(u,v))`
**Correct Answer**: B
**Explanation**: Adding depths of `u` and `v` double-counts the path from the root to their LCA. Subtracting `2 * D(LCA)` removes this overlapping path completely.

**Q10. In an interactive UI demo, if node 5 and node 1 are selected as targets, and 3 is their parent, what color should node 3 ultimately become to signify it is the LCA?**
A) Blue
B) Green
C) Red
D) Yellow
**Correct Answer**: C
**Explanation**: According to the interactive demo description in section 11, the LCA pulses and turns red when identified.
