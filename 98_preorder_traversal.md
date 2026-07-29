# Preorder Traversal

## 1. Introduction

Preorder Traversal is one of the three classic ways to visit every node in a binary tree. The name tells you the order: you process the **current node first (Pre)**, then recursively visit the **left subtree**, then the **right subtree**. This Root → Left → Right pattern is why it is called *pre*order — the root comes before the children.

Imagine you are a manager walking through a company org chart. Before speaking to any of your subordinates, you introduce yourself first. Then you walk down the left branch of your team, and after that, the right branch. Each manager at every level does the same. That is preorder traversal in real life.

It was formalized alongside other traversal strategies (inorder and postorder) as computer scientists needed systematic methods to process tree-structured data. It is especially valuable when the root must be encountered before its children, such as when copying a tree or printing a directory hierarchy.

You should use Preorder Traversal when the parent node needs to be processed before its children, when you want to serialize or reconstruct a tree, or when you are building an expression from a parse tree.

## 2. Why Use This Algorithm?

Preorder Traversal is the natural choice when a node's information is needed before you descend into its subtrees.

**Benefits:**
- Processes the root first, which is critical for serialization and tree copying
- Simple recursive structure that maps directly to the recursive definition of a binary tree
- Works on both binary trees and general n-ary trees
- Useful for generating prefix expressions from expression trees
- The iterative version is straightforward using an explicit stack

**Performance:**
Every node is visited exactly once. For a tree with n nodes, this guarantees O(n) time with O(h) stack space, where h is the height of the tree. In a balanced tree, h = O(log n); in a skewed tree, h = O(n).

**When it is better than other traversals:**
Preorder is preferred over inorder when you need to reconstruct the tree later (preorder gives you the root first, which is essential for reconstruction). It is preferred over postorder when you need to clone a tree (you must create the parent before you can attach children).

## 3. Real-World Applications

- **File system directory printing:** When you run `tree` or `ls -R` in a terminal, directories are printed before their contents — a preorder walk of the file system tree.
- **XML/HTML DOM serialization:** Writing a DOM tree to an XML string requires visiting each element node before its children.
- **Expression tree evaluation (prefix notation):** A compiler evaluates operator nodes before operand nodes when generating prefix (Polish notation) expressions.
- **Copying a binary tree:** To duplicate a tree, you must create the root before you can attach left and right subtrees.
- **Trie prefix matching:** Autocomplete engines traverse a trie from root to leaf in preorder to build candidate words letter by letter.

## 4. Prerequisites

Before learning Preorder Traversal, you should be comfortable with:
- Binary tree structure (nodes, left child, right child, parent)
- Recursion and the call stack
- Stack data structure (for the iterative version)
- Null/None checks and base cases
- Basic pointer or reference concepts (depending on language)

## 5. Visualization

Picture a binary tree drawn with the root at the top:

```
        1
       / \
      2   3
     / \ / \
    4  5 6  7
```

A preorder walk visits nodes in this order: **1 → 2 → 4 → 5 → 3 → 6 → 7**

Think of it as reading the tree top-down, left-to-right, but always printing the node the moment you first arrive at it — before going anywhere else. You stamp each node on arrival, not on departure.

```
Visit 1 (root)
  Go left → Visit 2
    Go left  → Visit 4 (leaf, backtrack)
    Go right → Visit 5 (leaf, backtrack)
  Go right → Visit 3
    Go left  → Visit 6 (leaf, backtrack)
    Go right → Visit 7 (leaf, backtrack)
```

## 6. How It Works

Preorder Traversal follows three strict steps at every node:
1. **Process the current node** (print it, store it, etc.)
2. **Recurse into the left subtree** — repeat the same three steps for the left child
3. **Recurse into the right subtree** — repeat the same three steps for the right child

The base case is simple: if the current node is null (or None), do nothing and return. Recursion naturally handles every node in the tree because each subtree is itself a valid binary tree.

For the iterative version, a stack replaces the call stack. The root is pushed first, and at each step, you pop a node, process it, then push its **right child first** (so it is processed after left), then push its **left child**. Since stacks are LIFO, the left child will be popped and processed first.

## 7. Step-by-Step Algorithm

**Recursive approach:**
1. If the current node is null, return immediately (base case).
2. Process (visit) the current node — for example, print its value.
3. Recursively call preorder on the left child.
4. Recursively call preorder on the right child.

**Iterative approach (using a stack):**
1. If the root is null, return.
2. Push the root onto the stack.
3. While the stack is not empty:
   a. Pop the top node and process (visit) it.
   b. If the node has a right child, push it onto the stack.
   c. If the node has a left child, push it onto the stack.
4. Continue until the stack is empty.

The reason right is pushed before left in the iterative version is that the stack is LIFO, so the left child (pushed last) will be popped and processed first.

## 8. Pseudocode

**Recursive:**
```
function preorderRecursive(node):
    if node is null:
        return
    visit(node)
    preorderRecursive(node.left)
    preorderRecursive(node.right)
```

**Iterative:**
```
function preorderIterative(root):
    if root is null:
        return
    stack = empty stack
    push root onto stack
    while stack is not empty:
        node = stack.pop()
        visit(node)
        if node.right is not null:
            push node.right onto stack
        if node.left is not null:
            push node.left onto stack
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
    node->data  = data;
    node->left  = NULL;
    node->right = NULL;
    return node;
}

/* Recursive Preorder */
void preorderRecursive(Node* root) {
    if (root == NULL) return;
    printf("%d ", root->data);
    preorderRecursive(root->left);
    preorderRecursive(root->right);
}

/* Iterative Preorder using a manual stack */
#define MAX_SIZE 100
void preorderIterative(Node* root) {
    if (root == NULL) return;
    Node* stack[MAX_SIZE];
    int top = -1;
    stack[++top] = root;
    while (top >= 0) {
        Node* node = stack[top--];
        printf("%d ", node->data);
        if (node->right) stack[++top] = node->right;
        if (node->left)  stack[++top] = node->left;
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
    Node* root    = createNode(1);
    root->left    = createNode(2);
    root->right   = createNode(3);
    root->left->left  = createNode(4);
    root->left->right = createNode(5);

    printf("Recursive Preorder: ");
    preorderRecursive(root);   /* Output: 1 2 4 5 3 */

    printf("\nIterative Preorder: ");
    preorderIterative(root);   /* Output: 1 2 4 5 3 */

    printf("\n");
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <stack>
#include <vector>
using namespace std;

struct Node {
    int data;
    Node* left;
    Node* right;
    Node(int val) : data(val), left(nullptr), right(nullptr) {}
};

/* Recursive Preorder */
void preorderRecursive(Node* root, vector<int>& result) {
    if (!root) return;
    result.push_back(root->data);
    preorderRecursive(root->left, result);
    preorderRecursive(root->right, result);
}

/* Iterative Preorder */
vector<int> preorderIterative(Node* root) {
    vector<int> result;
    if (!root) return result;
    stack<Node*> st;
    st.push(root);
    while (!st.empty()) {
        Node* node = st.top(); st.pop();
        result.push_back(node->data);
        if (node->right) st.push(node->right);
        if (node->left)  st.push(node->left);
    }
    return result;
}

int main() {
    Node* root         = new Node(1);
    root->left         = new Node(2);
    root->right        = new Node(3);
    root->left->left   = new Node(4);
    root->left->right  = new Node(5);

    vector<int> rec;
    preorderRecursive(root, rec);
    cout << "Recursive: ";
    for (int v : rec) cout << v << " ";   // 1 2 4 5 3
    cout << endl;

    vector<int> itr = preorderIterative(root);
    cout << "Iterative: ";
    for (int v : itr) cout << v << " ";   // 1 2 4 5 3
    cout << endl;

    return 0;
}
```

### Java
```java
import java.util.*;

public class PreorderTraversal {

    static class Node {
        int data;
        Node left, right;
        Node(int val) { data = val; }
    }

    /* Recursive Preorder */
    static void preorderRecursive(Node root, List<Integer> result) {
        if (root == null) return;
        result.add(root.data);
        preorderRecursive(root.left, result);
        preorderRecursive(root.right, result);
    }

    /* Iterative Preorder */
    static List<Integer> preorderIterative(Node root) {
        List<Integer> result = new ArrayList<>();
        if (root == null) return result;
        Deque<Node> stack = new ArrayDeque<>();
        stack.push(root);
        while (!stack.isEmpty()) {
            Node node = stack.pop();
            result.add(node.data);
            if (node.right != null) stack.push(node.right);
            if (node.left  != null) stack.push(node.left);
        }
        return result;
    }

    public static void main(String[] args) {
        Node root         = new Node(1);
        root.left         = new Node(2);
        root.right        = new Node(3);
        root.left.left    = new Node(4);
        root.left.right   = new Node(5);

        List<Integer> rec = new ArrayList<>();
        preorderRecursive(root, rec);
        System.out.println("Recursive: " + rec);   // [1, 2, 4, 5, 3]

        System.out.println("Iterative: " + preorderIterative(root)); // [1, 2, 4, 5, 3]
    }
}
```

### Python
```python
from collections import deque

class Node:
    def __init__(self, data):
        self.data  = data
        self.left  = None
        self.right = None

# Recursive Preorder
def preorder_recursive(root, result=None):
    if result is None:
        result = []
    if root is None:
        return result
    result.append(root.data)
    preorder_recursive(root.left, result)
    preorder_recursive(root.right, result)
    return result

# Iterative Preorder
def preorder_iterative(root):
    if root is None:
        return []
    result = []
    stack = [root]
    while stack:
        node = stack.pop()
        result.append(node.data)
        if node.right:
            stack.append(node.right)
        if node.left:
            stack.append(node.left)
    return result

# Build tree:   1
#              / \
#             2   3
#            / \
#           4   5
root              = Node(1)
root.left         = Node(2)
root.right        = Node(3)
root.left.left    = Node(4)
root.left.right   = Node(5)

print("Recursive:", preorder_recursive(root))   # [1, 2, 4, 5, 3]
print("Iterative:", preorder_iterative(root))   # [1, 2, 4, 5, 3]
```

### JavaScript
```javascript
class Node {
    constructor(data) {
        this.data  = data;
        this.left  = null;
        this.right = null;
    }
}

// Recursive Preorder
function preorderRecursive(root, result = []) {
    if (!root) return result;
    result.push(root.data);
    preorderRecursive(root.left, result);
    preorderRecursive(root.right, result);
    return result;
}

// Iterative Preorder
function preorderIterative(root) {
    if (!root) return [];
    const result = [];
    const stack  = [root];
    while (stack.length > 0) {
        const node = stack.pop();
        result.push(node.data);
        if (node.right) stack.push(node.right);
        if (node.left)  stack.push(node.left);
    }
    return result;
}

// Build tree
const root         = new Node(1);
root.left          = new Node(2);
root.right         = new Node(3);
root.left.left     = new Node(4);
root.left.right    = new Node(5);

console.log("Recursive:", preorderRecursive(root));  // [1, 2, 4, 5, 3]
console.log("Iterative:", preorderIterative(root));  // [1, 2, 4, 5, 3]
```

## 10. Code Explanation

The **recursive version** is a direct translation of the algorithm definition. When called on a node, it immediately records (visits) that node's value, then makes two recursive calls. The call stack automatically manages the backtracking — when the left subtree is fully exhausted, control returns and the right subtree call begins. The base case (`if root is null, return`) stops the recursion at leaves.

The **iterative version** replaces the implicit call stack with an explicit stack. The key insight is that the **right child must be pushed before the left child**. Because a stack pops in LIFO order, the left child (pushed last) will be processed first — preserving the Root → Left → Right order. Processing a node means adding its value to the result immediately upon popping, which mirrors the "visit before recursing" behaviour of the recursive version.

## 11. Interactive Demo

The demo should display a binary tree drawn with nodes as circles connected by lines. A control panel below offers a "Start Preorder" button, a "Reset" button, and a speed slider.

When Start is clicked, nodes are highlighted one by one in preorder sequence. The currently visited node glows orange. Previously visited nodes turn green. Edges traversed so far are highlighted in blue. A side panel shows the current result list building up in real-time. A call-stack visualization on the right shows recursive calls being pushed and popped. Users can also toggle between Recursive and Iterative mode, which changes the visualization to show either the call stack or the explicit stack respectively.

## 12. Dry Run

**Sample Tree:**
```
        1
       / \
      2   3
     / \
    4   5
```

**Recursive Trace (Root → Left → Right):**

| Step | Action                       | Current Node | Result So Far |
|------|------------------------------|--------------|---------------|
| 1    | Visit root                   | 1            | [1]           |
| 2    | Go left, visit node          | 2            | [1, 2]        |
| 3    | Go left, visit node          | 4            | [1, 2, 4]     |
| 4    | 4's left is null → return    | null         | [1, 2, 4]     |
| 5    | 4's right is null → return   | null         | [1, 2, 4]     |
| 6    | Back at 2, go right, visit   | 5            | [1, 2, 4, 5]  |
| 7    | 5's left is null → return    | null         | [1, 2, 4, 5]  |
| 8    | 5's right is null → return   | null         | [1, 2, 4, 5]  |
| 9    | Back at 1, go right, visit   | 3            | [1, 2, 4, 5, 3] |
| 10   | 3's left is null → return    | null         | [1, 2, 4, 5, 3] |
| 11   | 3's right is null → return   | null         | [1, 2, 4, 5, 3] |

**Final Output:** `[1, 2, 4, 5, 3]`

**Iterative Stack Trace:**

| Step | Stack (top→bottom) | Action                  | Result So Far   |
|------|--------------------|-------------------------|-----------------|
| 1    | [1]                | Push root               | []              |
| 2    | []                 | Pop 1, visit 1          | [1]             |
| 3    | [3, 2]             | Push right(3), left(2)  | [1]             |
| 4    | [3]                | Pop 2, visit 2          | [1, 2]          |
| 5    | [3, 5, 4]          | Push right(5), left(4)  | [1, 2]          |
| 6    | [3, 5]             | Pop 4, visit 4 (leaf)   | [1, 2, 4]       |
| 7    | [3]                | Pop 5, visit 5 (leaf)   | [1, 2, 4, 5]    |
| 8    | []                 | Pop 3, visit 3 (leaf)   | [1, 2, 4, 5, 3] |

## 13. Time & Space Complexity

| Case             | Complexity | Reason                                                     |
|------------------|------------|------------------------------------------------------------|
| Best Case Time   | O(n)       | Every node must be visited at least once                   |
| Average Case Time| O(n)       | Always visits all n nodes regardless of tree shape         |
| Worst Case Time  | O(n)       | Even a degenerate (skewed) tree requires visiting all n nodes |
| Space (Recursive)| O(h)       | Call stack depth equals tree height h                      |
| Space (Iterative)| O(h)       | Explicit stack holds at most h nodes at once               |
| Space Best Case  | O(log n)   | Perfectly balanced tree: h = log₂(n)                      |
| Space Worst Case | O(n)       | Completely skewed tree: h = n                              |

## 14. Advantages

- **Simple recursive structure:** The code directly mirrors the definition of a binary tree, making it easy to read and reason about.
- **Root-first processing:** Ideal whenever the parent must be known before the children (tree copying, serialization).
- **Enables tree reconstruction:** Given a preorder sequence and an inorder sequence, the original tree can be uniquely reconstructed.
- **Low overhead:** No sorting, no auxiliary arrays, no hashing — just a stack (implicit or explicit).
- **Works on n-ary trees:** Generalizes naturally by processing children left-to-right after visiting the parent.

## 15. Disadvantages

- **Not suitable for sorted order:** If you need nodes visited in ascending order (e.g., for BST processing), inorder traversal is required.
- **Stack overflow risk for deep trees:** The recursive version will overflow the call stack for very deep (millions of nodes) or completely skewed trees.
- **No direct path information:** The traversal visits nodes but does not inherently track the path from root to current node (you need extra bookkeeping for that).

## 16. Applications

- Generating prefix (Polish) notation from an expression parse tree
- Serializing a binary tree to a string for storage or transmission
- Cloning / deep-copying a binary tree
- Building a directory listing from a file system tree
- Printing structured outlines (chapters → sections → subsections)
- Evaluating abstract syntax trees (ASTs) in compilers and interpreters

## 17. Common Mistakes

- **Forgetting the null check:** The most common bug is not returning early when the node is null. This causes a null-pointer/dereference crash.
- **Wrong child push order in iterative version:** Pushing left before right results in right being processed first (LIFO). Always push right first so left is processed first.
- **Confusing with inorder:** Inorder visits Left → Root → Right; preorder visits Root → Left → Right. Mixing them up produces the wrong output.
- **Not initializing the result list outside recursion:** In Python/JavaScript, passing a mutable default argument can cause state to persist across calls. Always initialize result as `None` and set it to `[]` inside the function.
- **Assuming preorder gives sorted output for a BST:** Only inorder traversal of a BST yields a sorted sequence; preorder does not.

## 18. Interview Questions

1. What is the order of node visits in Preorder Traversal?
2. How does Preorder Traversal differ from Inorder and Postorder?
3. Write an iterative implementation of Preorder Traversal without using recursion.
4. Given a preorder traversal sequence, can you reconstruct the original binary tree? What additional information do you need?
5. What is the space complexity of recursive Preorder Traversal and what determines it?
6. How would you implement Preorder Traversal on an n-ary tree?
7. Is Preorder Traversal of a BST guaranteed to produce a sorted sequence? Explain.
8. How do you use Preorder Traversal to serialize and deserialize a binary tree?
9. What happens to the call stack if you perform recursive Preorder Traversal on a completely skewed tree with 10,000 nodes?
10. Explain why right child is pushed before left child in the iterative stack approach.

## 19. Practice Problems

**Easy:**
1. Print all values of a binary tree in preorder.
2. Collect all preorder values into a list and return it.
3. Count the total number of nodes visited during a preorder traversal.
4. Find the first node in preorder sequence whose value equals a given target.

**Medium:**
5. Reconstruct a binary tree from given Preorder and Inorder traversal sequences.
6. Implement Preorder Traversal iteratively without using any additional data structure (Morris Traversal variant).
7. Serialize a binary tree to a string using preorder traversal, then write a deserializer to rebuild the tree.
8. Find the k-th node in preorder traversal.

**Hard:**
9. Given two preorder traversal sequences of two different binary trees, determine if the trees are identical.
10. Implement a level-aware preorder traversal that also outputs the depth of each node.
11. Extend preorder traversal to a general n-ary tree and verify the result matches the expected prefix order.

## 20. Related Algorithms

- **Inorder Traversal** (Left → Root → Right) — produces sorted output for a BST
- **Postorder Traversal** (Left → Right → Root) — useful for deleting a tree or evaluating postfix expressions
- **Level Order Traversal** (BFS) — visits nodes level by level using a queue
- **Morris Traversal** — achieves O(1) space inorder/preorder traversal by modifying tree links temporarily
- **DFS on graphs** — generalizes tree preorder traversal to graphs using a visited set

## 21. Summary

Preorder Traversal visits each node of a binary tree in the order Root → Left → Right. It is the go-to traversal whenever the parent must be processed before its children — for serialization, cloning, or prefix-expression generation. It runs in O(n) time and O(h) space, where h is the tree height. The recursive version is concise and elegant; the iterative version uses an explicit stack with right pushed before left. Avoid confusing it with inorder (which gives sorted BST output) or postorder (used for deletion). Mastering preorder traversal is essential for understanding more advanced tree algorithms and compiler design.

## 22. Quiz

**Question 1:** In Preorder Traversal, which node is visited first?
- A) Left child
- B) Right child
- C) Root
- D) The leaf with the smallest value
- **Correct Answer:** C
- **Explanation:** Preorder means Root → Left → Right. The root is always the first node visited.

**Question 2:** What is the time complexity of Preorder Traversal on a tree with n nodes?
- A) O(log n)
- B) O(n log n)
- C) O(n²)
- D) O(n)
- **Correct Answer:** D
- **Explanation:** Every node is visited exactly once, so the total work is proportional to n.

**Question 3:** What is the space complexity of recursive Preorder Traversal?
- A) O(1)
- B) O(n)
- C) O(h), where h is the height of the tree
- D) O(n²)
- **Correct Answer:** C
- **Explanation:** The recursive call stack depth equals the height of the tree. For a balanced tree this is O(log n); for a skewed tree it is O(n).

**Question 4:** In the iterative version, why is the right child pushed onto the stack before the left child?
- A) Because right children have larger values in a BST
- B) Because stacks are LIFO, so the left child pushed after right will be processed first
- C) Because left children are always null
- D) To avoid stack overflow
- **Correct Answer:** B
- **Explanation:** Since a stack pops in Last-In-First-Out order, pushing right first and left second ensures left is popped (processed) first, maintaining the correct Root → Left → Right order.

**Question 5:** Which traversal produces sorted output when applied to a Binary Search Tree?
- A) Preorder
- B) Postorder
- C) Level Order
- D) Inorder
- **Correct Answer:** D
- **Explanation:** Inorder traversal visits Left → Root → Right. For a BST, this yields nodes in ascending order. Preorder does not produce sorted output.

**Question 6:** Given the tree below, what is the Preorder traversal output?
```
    A
   / \
  B   C
 /
D
```
- A) D B A C
- B) A B C D
- C) A B D C
- D) D B C A
- **Correct Answer:** C
- **Explanation:** Preorder: Visit A → go left, visit B → go left, visit D → D has no children, backtrack → B has no right child, backtrack → go right from A, visit C. Result: A B D C.

**Question 7:** What is Preorder Traversal most suitable for?
- A) Producing sorted output from a BST
- B) Deleting all nodes of a tree safely
- C) Serializing or copying a binary tree
- D) Finding the height of a tree
- **Correct Answer:** C
- **Explanation:** Because the root is processed before its children, preorder is ideal for serialization (you know the root before its subtrees) and tree copying (create parent before attaching children).

**Question 8:** Which of the following is the correct Preorder sequence for this tree?
```
    1
   / \
  2   3
     / \
    4   5
```
- A) 2 1 4 3 5
- B) 1 2 3 4 5
- C) 2 4 5 3 1
- D) 1 3 4 5 2
- **Correct Answer:** B
- **Explanation:** Preorder: visit 1 → go left, visit 2 (leaf) → go right, visit 3 → go left of 3, visit 4 → go right of 3, visit 5. Result: 1 2 3 4 5.

**Question 9:** What base case terminates the recursion in Preorder Traversal?
- A) When the node has no right child
- B) When the node's value equals zero
- C) When the current node is null
- D) When the stack is empty
- **Correct Answer:** C
- **Explanation:** The recursion stops when it reaches a null node (beyond a leaf). This is the universal base case for all recursive binary tree algorithms.

**Question 10:** Given only the Preorder traversal of a binary tree, can you always reconstruct the original tree uniquely?
- A) Yes, always
- B) No, you need additional information such as the Inorder traversal
- C) Only if the tree is a BST
- D) Only if the tree is perfectly balanced
- **Correct Answer:** B
- **Explanation:** Preorder alone is ambiguous — multiple different trees can share the same preorder sequence. You need both Preorder and Inorder (or Preorder with null markers for missing nodes) to reconstruct the tree uniquely.
