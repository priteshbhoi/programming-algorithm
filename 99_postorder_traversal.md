# Postorder Traversal

## 1. Introduction

Postorder Traversal is one of the three foundational depth-first search (DFS) algorithms used to traverse binary trees. The name specifies the order of operations: you process the **left subtree**, then the **right subtree**, and finally the **current node (Post)**. This Left → Right → Root pattern means the root is always visited *after* (post) all of its descendants.

Imagine a file system directory size calculator. Before you can calculate the total size of a parent folder, you must first visit and compute the sizes of all files and subfolders inside it. Only after completing the children can you aggregate and process the parent. That bottom-up evaluation is postorder traversal in real life.

Postorder traversal is indispensable when operations depend on sub-results from child nodes before the parent node can be evaluated.

You should use Postorder Traversal when you need to delete/free nodes in a tree (bottom-up cleanup), compute tree metrics like height or size, or evaluate mathematical expression trees written in postfix (Reverse Polish) notation.

## 2. Why Use This Algorithm?

Postorder Traversal guarantees that a node's children are fully visited and processed before the node itself is visited.

**Benefits:**
- Bottom-up processing guarantees child dependencies are satisfied before parent computation.
- Essential for safe node deletion and memory deallocation without losing pointer references.
- Enables evaluation of expression trees (postfix notation / Reverse Polish Notation).
- Natural fit for tree DP problems (e.g., calculating tree height, maximum path sum).
- Can be implemented recursively or iteratively (using 2 stacks or 1 stack with tracking).

**Performance:**
Like all standard tree traversals, postorder visits every node exactly once. For a tree with $n$ nodes, it achieves $O(n)$ time complexity and $O(h)$ auxiliary space complexity, where $h$ is the height of the tree ($O(\log n)$ for balanced trees, $O(n)$ for skewed trees).

**When it is better than other traversals:**
Postorder is superior to preorder and inorder when deleting a tree (preorder would delete the root first, losing access to children) or when evaluating expressions where operators depend on evaluated left and right operands.

## 3. Real-World Applications

- **Memory Deallocation / Tree Deletion:** In languages without automatic garbage collection (like C), freeing a tree requires destroying children before freeing the parent pointer.
- **Directory Size Calculation:** Calculating disk space usage (`du` command in Unix) requires summing subfolder sizes before reporting the parent folder total.
- **Expression Tree Evaluation:** Evaluating arithmetic trees (e.g., $(3 + 4) \times 5$) evaluates leaf operands first, then operators.
- **Dependency Resolution:** Build systems (like Make or Gradle) evaluate leaf dependencies before building top-level targets.
- **Abstract Syntax Tree (AST) Code Generation:** Compilers process child expressions first to compute registers/types before emitting code for parent nodes.

## 4. Prerequisites

Before learning Postorder Traversal, you should be familiar with:
- Binary tree concepts (nodes, children, root, leaves)
- Basic recursion and the call stack
- Stack data structure (for iterative implementation)
- Pointer / reference handling and null safety

## 5. Visualization

Consider the following binary tree:

```
        1
       / \
      2   3
     / \ / \
    4  5 6  7
```

A postorder walk visits nodes in this exact sequence: **4 → 5 → 2 → 6 → 7 → 3 → 1**

```
Explore Left Subtree of 1:
  Explore Left Subtree of 2:
    Node 4 has no children → Visit 4
    Backtrack to 2
  Explore Right Subtree of 2:
    Node 5 has no children → Visit 5
  Both subtrees of 2 done → Visit 2
Backtrack to 1

Explore Right Subtree of 1:
  Explore Left Subtree of 3:
    Node 6 has no children → Visit 6
  Explore Right Subtree of 3:
    Node 7 has no children → Visit 7
  Both subtrees of 3 done → Visit 3

Both subtrees of 1 done → Visit 1
```

## 6. How It Works

Postorder Traversal follows three strict steps recursively for every node:
1. **Recurse into the left subtree** (Left)
2. **Recurse into the right subtree** (Right)
3. **Process the current node** (Root)

The base case occurs when encountering a `null` node, returning control to the caller.

For the **iterative approach**, two main techniques exist:
1. **Two Stacks Method:** Push nodes in a modified preorder manner (Root → Right → Left) onto Stack 1, popping and pushing them to Stack 2. Popping Stack 2 produces the exact postorder sequence (Left → Right → Root).
2. **One Stack Method:** Use a single stack while keeping track of the `lastVisitedNode` to distinguish between returning from the left subtree versus the right subtree.

## 7. Step-by-Step Algorithm

**Recursive Approach:**
1. If current node is `null`, return.
2. Call `postorder(node.left)`.
3. Call `postorder(node.right)`.
4. Process/visit `node`.

**Iterative Approach (2 Stacks - Easiest to understand):**
1. If root is `null`, return empty list.
2. Push `root` to `stack1`.
3. While `stack1` is not empty:
   a. Pop `node` from `stack1` and push it to `stack2`.
   b. If `node.left` exists, push to `stack1`.
   c. If `node.right` exists, push to `stack1`.
4. While `stack2` is not empty, pop nodes from `stack2` and visit them.

## 8. Pseudocode

**Recursive:**
```
function postorderRecursive(node):
    if node is null:
        return
    postorderRecursive(node.left)
    postorderRecursive(node.right)
    visit(node)
```

**Iterative (Two Stacks):**
```
function postorderIterative(root):
    if root is null:
        return
    stack1 = empty stack
    stack2 = empty stack
    push root onto stack1
    
    while stack1 is not empty:
        node = stack1.pop()
        stack2.push(node)
        if node.left is not null:
            stack1.push(node.left)
        if node.right is not null:
            stack1.push(node.right)
            
    while stack2 is not empty:
        node = stack2.pop()
        visit(node)
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node {
    int data;
    struct Node* left;
    struct Node* right;
} Node;

Node* createNode(int data) {
    Node* node = (Node*)malloc(sizeof(Node));
    node->data = data;
    node->left = NULL;
    node->right = NULL;
    return node;
}

/* Recursive Postorder */
void postorderRecursive(Node* root) {
    if (root == NULL) return;
    postorderRecursive(root->left);
    postorderRecursive(root->right);
    printf("%d ", root->data);
}

/* Iterative Postorder using Two Stacks */
#define MAX_SIZE 100
void postorderIterative(Node* root) {
    if (root == NULL) return;
    Node* s1[MAX_SIZE];
    Node* s2[MAX_SIZE];
    int top1 = -1, top2 = -1;

    s1[++top1] = root;
    while (top1 >= 0) {
        Node* curr = s1[top1--];
        s2[++top2] = curr;
        if (curr->left)  s1[++top1] = curr->left;
        if (curr->right) s1[++top1] = curr->right;
    }

    while (top2 >= 0) {
        printf("%d ", s2[top2--]->data);
    }
}

int main() {
    /*
         1
        / \
       2   3
      / \
     4   5
    */
    Node* root = createNode(1);
    root->left = createNode(2);
    root->right = createNode(3);
    root->left->left = createNode(4);
    root->left->right = createNode(5);

    printf("Recursive Postorder: ");
    postorderRecursive(root); /* Output: 4 5 2 3 1 */

    printf("\nIterative Postorder: ");
    postorderIterative(root); /* Output: 4 5 2 3 1 */

    printf("\n");
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;

struct Node {
    int data;
    Node* left;
    Node* right;
    Node(int val) : data(val), left(nullptr), right(nullptr) {}
};

/* Recursive Postorder */
void postorderRecursive(Node* root, vector<int>& result) {
    if (!root) return;
    postorderRecursive(root->left, result);
    postorderRecursive(root->right, result);
    result.push_back(root->data);
}

/* Iterative Postorder (2 Stacks) */
vector<int> postorderIterative(Node* root) {
    vector<int> result;
    if (!root) return result;
    stack<Node*> s1, s2;
    s1.push(root);

    while (!s1.empty()) {
        Node* curr = s1.top(); s1.pop();
        s2.push(curr);
        if (curr->left)  s1.push(curr->left);
        if (curr->right) s1.push(curr->right);
    }

    while (!s2.empty()) {
        result.push_back(s2.top()->data);
        s2.pop();
    }
    return result;
}

int main() {
    Node* root = new Node(1);
    root->left = new Node(2);
    root->right = new Node(3);
    root->left->left = new Node(4);
    root->left->right = new Node(5);

    vector<int> rec;
    postorderRecursive(root, rec);
    cout << "Recursive: ";
    for (int v : rec) cout << v << " "; // Output: 4 5 2 3 1
    cout << endl;

    vector<int> itr = postorderIterative(root);
    cout << "Iterative: ";
    for (int v : itr) cout << v << " "; // Output: 4 5 2 3 1
    cout << endl;

    return 0;
}
```

### Java
```java
import java.util.*;

public class PostorderTraversal {

    static class Node {
        int data;
        Node left, right;
        Node(int val) { data = val; }
    }

    /* Recursive Postorder */
    static void postorderRecursive(Node root, List<Integer> result) {
        if (root == null) return;
        postorderRecursive(root.left, result);
        postorderRecursive(root.right, result);
        result.add(root.data);
    }

    /* Iterative Postorder (2 Stacks) */
    static List<Integer> postorderIterative(Node root) {
        List<Integer> result = new ArrayList<>();
        if (root == null) return result;

        Deque<Node> s1 = new ArrayDeque<>();
        Deque<Node> s2 = new ArrayDeque<>();
        s1.push(root);

        while (!s1.isEmpty()) {
            Node curr = s1.pop();
            s2.push(curr);
            if (curr.left != null)  s1.push(curr.left);
            if (curr.right != null) s1.push(curr.right);
        }

        while (!s2.isEmpty()) {
            result.add(s2.pop().data);
        }
        return result;
    }

    public static void main(String[] args) {
        Node root = new Node(1);
        root.left = new Node(2);
        root.right = new Node(3);
        root.left.left = new Node(4);
        root.left.right = new Node(5);

        List<Integer> rec = new ArrayList<>();
        postorderRecursive(root, rec);
        System.out.println("Recursive: " + rec); // [4, 5, 2, 3, 1]

        System.out.println("Iterative: " + postorderIterative(root)); // [4, 5, 2, 3, 1]
    }
}
```

### Python
```python
class Node:
    def __init__(self, data):
        self.data = data
        self.left = None
        self.right = None

# Recursive Postorder
def postorder_recursive(root, result=None):
    if result is None:
        result = []
    if root is None:
        return result
    postorder_recursive(root.left, result)
    postorder_recursive(root.right, result)
    result.append(root.data)
    return result

# Iterative Postorder (2 Stacks)
def postorder_iterative(root):
    if root is None:
        return []
    s1 = [root]
    s2 = []
    while s1:
        curr = s1.pop()
        s2.append(curr)
        if curr.left:
            s1.append(curr.left)
        if curr.right:
            s1.append(curr.right)
            
    return [node.data for node in reversed(s2)]

# Build Tree
root = Node(1)
root.left = Node(2)
root.right = Node(3)
root.left.left = Node(4)
root.left.right = Node(5)

print("Recursive:", postorder_recursive(root)) # [4, 5, 2, 3, 1]
print("Iterative:", postorder_iterative(root)) # [4, 5, 2, 3, 1]
```

### JavaScript
```javascript
class Node {
    constructor(data) {
        this.data = data;
        this.left = null;
        this.right = null;
    }
}

// Recursive Postorder
function postorderRecursive(root, result = []) {
    if (!root) return result;
    postorderRecursive(root.left, result);
    postorderRecursive(root.right, result);
    result.push(root.data);
    return result;
}

// Iterative Postorder (2 Stacks)
function postorderIterative(root) {
    if (!root) return [];
    const s1 = [root];
    const s2 = [];
    
    while (s1.length > 0) {
        const curr = s1.pop();
        s2.push(curr);
        if (curr.left)  s1.push(curr.left);
        if (curr.right) s1.push(curr.right);
    }
    
    const result = [];
    while (s2.length > 0) {
        result.push(s2.pop().data);
    }
    return result;
}

// Build Tree
const root = new Node(1);
root.left = new Node(2);
root.right = new Node(3);
root.left.left = new Node(4);
root.left.right = new Node(5);

console.log("Recursive:", postorderRecursive(root)); // [4, 5, 2, 3, 1]
console.log("Iterative:", postorderIterative(root)); // [4, 5, 2, 3, 1]
```

## 10. Code Explanation

In the **recursive version**, execution flows naturally down the tree. The function delays visiting the current node until both left and right recursive calls complete. When reaching a leaf, both child calls return immediately, allowing the node itself to append its data to the result list.

In the **two-stack iterative version**:
- `s1` is used to traverse the tree in **Root → Right → Left** order.
- Each node popped from `s1` is pushed into `s2`.
- When `s2` is popped sequentially at the end, the **Root → Right → Left** sequence is reversed into **Left → Right → Root**, achieving exact postorder output without complex parent/visited tracking.

## 11. Interactive Demo

The demo visualizes a binary tree using circular node elements. Control parameters include a "Run Postorder", "Reset", and speed adjustment slider.

During execution:
- The currently active node is highlighted in yellow while traversing down.
- Once both left and right subtrees of a node are completed, the node flashes green and its value is emitted to the output bar.
- Visited nodes remain dimmed green to signal completion.
- Users can toggle between **Recursive (Call Stack)** and **Iterative (Two Stacks)** tabs to watch internal stack states update frame-by-frame.

## 12. Dry Run

**Sample Tree:**
```
        1
       / \
      2   3
     / \
    4   5
```

**Iterative Dry Run (2 Stacks):**

| Step | `s1` (Top to Bottom) | `s2` (Top to Bottom) | Action |
|------|----------------------|----------------------|--------|
| 1 | `[1]` | `[]` | Push root to `s1` |
| 2 | `[]` | `[1]` | Pop 1 from `s1`, push to `s2` |
| 3 | `[3, 2]` | `[1]` | Push 1's left (2) and right (3) to `s1` |
| 4 | `[3]` | `[2, 1]` | Pop 2 from `s1`, push to `s2` |
| 5 | `[3, 5, 4]` | `[2, 1]` | Push 2's left (4) and right (5) to `s1` |
| 6 | `[3, 5]` | `[4, 2, 1]` | Pop 4 from `s1`, push to `s2` (No children) |
| 7 | `[3]` | `[5, 4, 2, 1]` | Pop 5 from `s1`, push to `s2` (No children) |
| 8 | `[]` | `[3, 5, 4, 2, 1]` | Pop 3 from `s1`, push to `s2` (No children) |
| Finish | Empty | Pop `s2` sequence: | **Output: `[4, 5, 2, 3, 1]`** |

## 13. Time & Space Complexity

| Case | Time Complexity | Space Complexity | Reason |
|------|-----------------|------------------|--------|
| Best Case | $O(n)$ | $O(\log n)$ | Balanced tree height is $\log_2(n)$ |
| Average Case | $O(n)$ | $O(h)$ | Visits all $n$ nodes; stack size depends on tree height $h$ |
| Worst Case | $O(n)$ | $O(n)$ | Skewed tree height becomes $n$ |

## 14. Advantages

- **Bottom-up ordering:** Ensures children are processed before parents.
- **Safe deletion:** Ideal for releasing memory without creating dangling references.
- **Expression evaluation:** Fits postfix evaluation seamlessly.
- **Simplicity:** Dual-stack iterative approach requires zero complex conditional state logic.

## 15. Disadvantages

- **Higher iterative complexity (1-stack):** Implementing postorder with 1 stack requires tricky pointer tracking (`lastVisitedNode`).
- **Memory footprint:** The 2-stack iterative method stores all $n$ nodes in memory inside `s2` simultaneously.
- **Stack overflow risk:** Deep recursion can trigger stack overflow on highly unbalanced trees.

## 16. Applications

- Evaluation of postfix mathematical expressions (RPN).
- Safe binary tree memory deallocation in C/C++.
- Calculating heights/depths/sizes of subtrees.
- Lowest Common Ancestor (LCA) algorithms.
- Finding diameter of a binary tree.

## 17. Common Mistakes

- **Deleting parent before children:** Trying to delete nodes using preorder traversal causes loss of child pointers.
- **Confusing child order in 2-stack approach:** Pushing right before left into `s1` produces wrong order. Remember to push `left` first, then `right`.
- **Modifying stack state without null checks:** Pushing null children onto the stack leads to null dereference exceptions.

## 18. Interview Questions

1. What is the order of node visits in Postorder Traversal?
2. Why is Postorder Traversal used for deleting binary trees in C/C++?
3. How do you implement Postorder Traversal using only 1 stack?
4. How can you evaluate a postfix expression tree using postorder traversal?
5. What is the relationship between Preorder and Postorder traversals?
6. Can you reconstruct a tree given only its Postorder traversal?
7. Write code to calculate the height of a binary tree using postorder traversal.
8. How does Postorder Traversal apply to general $n$-ary trees?
9. Compare space complexities of 1-stack vs 2-stack iterative postorder traversals.
10. What is the Postorder sequence of a BST compared to its Inorder sequence?

## 19. Practice Problems

**Easy:**
1. Return the postorder traversal of a binary tree as an array.
2. Calculate total leaf count of a binary tree using postorder traversal.
3. Compute the maximum depth / height of a binary tree.

**Medium:**
4. Implement Postorder Traversal iteratively using only 1 stack.
5. Reconstruct a binary tree given Inorder and Postorder traversal arrays.
6. Evaluate an expression tree using postorder traversal.
7. Find the Lowest Common Ancestor (LCA) of two nodes using postorder DP.

**Hard:**
8. Find Maximum Path Sum in a Binary Tree (LeetCode 124).
9. Serialize and deserialize a binary tree using postorder traversal.
10. Construct Binary Tree from Preorder and Postorder Traversal.

## 20. Related Algorithms

- **Preorder Traversal** (Root → Left → Right)
- **Inorder Traversal** (Left → Root → Right)
- **Level Order Traversal** (BFS)
- **Morris Traversal** (Threaded tree traversal for $O(1)$ space)

## 21. Summary

Postorder Traversal processes nodes in **Left → Right → Root** order. It is the definitive technique for bottom-up operations where child node results are mandatory before parent execution. It is widely applied in memory management, tree deletion, subtree size/height calculations, and expression evaluations.

## 22. Quiz

**Question 1:** What is the visit order in Postorder Traversal?
- A) Root → Left → Right
- B) Left → Root → Right
- C) Left → Right → Root
- D) Right → Left → Root
- **Correct Answer:** C
- **Explanation:** Postorder visits the left subtree, right subtree, and then the root node.

**Question 2:** Why is Postorder Traversal used when deleting nodes of a tree in C?
- A) It is faster than Preorder.
- B) It ensures children are deleted before the parent, preventing loss of child pointers.
- C) It requires $O(1)$ memory.
- D) It sorts the nodes.
- **Correct Answer:** B
- **Explanation:** Processing children before the parent prevents losing references to subtrees.

**Question 3:** What is the time complexity of Postorder Traversal?
- A) $O(\log n)$
- B) $O(n)$
- C) $O(n^2)$
- D) $O(1)$
- **Correct Answer:** B
- **Explanation:** Every node in the tree is visited once.

**Question 4:** In the 2-stack iterative implementation, what order does Stack 1 traverse the tree?
- A) Root → Left → Right
- B) Root → Right → Left
- C) Left → Right → Root
- D) Right → Left → Root
- **Correct Answer:** B
- **Explanation:** Stack 1 processes Root → Right → Left, so pushing to Stack 2 and popping reverses it to Left → Right → Root.

**Question 5:** What notation corresponds to Postorder Traversal of an expression tree?
- A) Infix
- B) Prefix
- C) Postfix (Reverse Polish)
- D) Binary
- **Correct Answer:** C
- **Explanation:** Postorder produces postfix expressions where operators follow operands.

**Question 6:** Given a tree with Root 1, Left 2, Right 3, what is the Postorder sequence?
- A) 1, 2, 3
- B) 2, 1, 3
- C) 2, 3, 1
- D) 3, 2, 1
- **Correct Answer:** C
- **Explanation:** Left (2) → Right (3) → Root (1).

**Question 7:** Can a binary tree be uniquely reconstructed using ONLY its postorder sequence?
- A) Yes, always
- B) No, an additional traversal (like Inorder) or null indicators are required
- C) Only if it is a balanced tree
- D) Only if it has an even number of nodes
- **Correct Answer:** B
- **Explanation:** Postorder alone is ambiguous without additional structure information or another traversal array.

**Question 8:** What is the space complexity of Postorder Traversal on a completely skewed tree?
- A) $O(1)$
- B) $O(\log n)$
- C) $O(n)$
- D) $O(n^2)$
- **Correct Answer:** C
- **Explanation:** The height of a skewed tree equals $n$, leading to an $O(n)$ call stack / explicit stack depth.

**Question 9:** Which problem naturally uses Postorder Traversal logic?
- A) Printing tree directory levels top-down
- B) Calculating maximum depth / height of a tree
- C) Searching a sorted BST for a target key
- D) Breadth-first level scanning
- **Correct Answer:** B
- **Explanation:** Height calculation requires knowing the height of left and right subtrees first: $\max(h_{left}, h_{right}) + 1$.

**Question 10:** In 2-stack iterative implementation, which child node should be pushed FIRST to `s1`?
- A) Right child
- B) Left child
- C) Neither
- D) Whichever has larger value
- **Correct Answer:** B
- **Explanation:** Push `left` first so `right` sits on top of `s1`. `right` is popped first and pushed to `s2`, placing `right` under `left` when `s2` is popped.
