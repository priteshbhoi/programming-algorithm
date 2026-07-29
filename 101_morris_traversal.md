# Morris Traversal Algorithm

## 1. Introduction
Morris Traversal is an ingenious algorithm used to traverse a binary tree without using recursion or an explicit stack. Unlike standard tree traversal methods (like recursive in-order traversal) which require $O(N)$ space in the worst case due to the call stack, Morris Traversal achieves this with $O(1)$ extra space by temporarily modifying the tree structure.

**Real-World Analogy:**
Imagine exploring a massive maze (a tree). Normally, to remember how to get back, you might carry a spool of thread or a map (a stack). However, what if you could just briefly tie a temporary string from your current location to your next destination, and then untie it once you successfully backtrack? Morris traversal does precisely this by creating temporary "threads" (pointers) from a node's in-order predecessor back to itself, allowing it to find its way back up the tree without a map.

## 2. Why Use This Algorithm?
The primary reason to use Morris Traversal is its **$O(1)$ space complexity**. In systems with severe memory constraints or deeply nested trees where a stack overflow could crash the program, Morris Traversal offers a robust alternative. It modifies the tree during the traversal but restores it to its original structure before the algorithm terminates.

## 3. Real-World Applications
- **Embedded Systems:** Where memory limits are strict and dynamic stack allocation is discouraged.
- **Operating Systems:** Traversal of internal kernel tree structures (like process trees or file system components) where memory safety and limits are paramount.
- **Large-Scale Data Processing:** Avoiding stack overflow errors in extremely unbalanced or deep binary trees.

## 4. Prerequisites
Before learning Morris Traversal, you should be familiar with:
- Binary Trees and standard terminology (root, left child, right child).
- In-order tree traversal (`Left -> Root -> Right`).
- Concept of an **In-order Predecessor** (the rightmost node in a node's left subtree).
- Basic pointer/reference manipulation.

## 5. Visualization
Imagine a binary tree:
```text
      1
    /   \
   2     3
  / \
 4   5
```

Morris traversal creates temporary threads:
1. From `4` (predecessor of `2`) to `2`.
2. From `5` (predecessor of `1`) to `1`.

During traversal, these threads guide the pointer back up to the parent. Once traversed, the threads are removed.

## 6. How It Works
Morris Traversal relies on the concept of a Threaded Binary Tree. When the current node has a left child, the algorithm finds the current node's in-order predecessor. 
- If the predecessor's right child is null, we set its right child to the current node (creating a thread) and move to the current node's left child.
- If the predecessor's right child already points to the current node, it means we have visited the left subtree. We print/visit the current node, remove the thread (setting the right child back to null), and move to the current node's right child.
- If the current node has no left child, we simply visit it and move to its right child.

## 7. Step-by-Step Algorithm
Initialize a pointer `curr` to the `root` of the tree.
While `curr` is not NULL:
1. If `curr.left` is NULL:
   a. Visit `curr`.
   b. Move `curr` to `curr.right`.
2. Else:
   a. Find the in-order predecessor of `curr` (rightmost node in `curr.left` subtree).
   b. If the predecessor's right child is NULL:
      - Set predecessor's right child to `curr` (create thread).
      - Move `curr` to `curr.left`.
   c. If the predecessor's right child is `curr`:
      - Set predecessor's right child to NULL (remove thread).
      - Visit `curr`.
      - Move `curr` to `curr.right`.

## 8. Pseudocode
```text
function morrisInorderTraversal(root):
    curr = root
    while curr is not null:
        if curr.left is null:
            print curr.value
            curr = curr.right
        else:
            predecessor = curr.left
            while predecessor.right is not null and predecessor.right != curr:
                predecessor = predecessor.right
            
            if predecessor.right is null:
                predecessor.right = curr
                curr = curr.left
            else:
                predecessor.right = null
                print curr.value
                curr = curr.right
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>

struct Node {
    int data;
    struct Node* left;
    struct Node* right;
};

struct Node* createNode(int data) {
    struct Node* newNode = (struct Node*)malloc(sizeof(struct Node));
    newNode->data = data;
    newNode->left = NULL;
    newNode->right = NULL;
    return newNode;
}

void morrisInorder(struct Node* root) {
    struct Node* curr = root;
    while (curr != NULL) {
        if (curr->left == NULL) {
            printf("%d ", curr->data);
            curr = curr->right;
        } else {
            struct Node* pre = curr->left;
            while (pre->right != NULL && pre->right != curr) {
                pre = pre->right;
            }
            if (pre->right == NULL) {
                pre->right = curr;
                curr = curr->left;
            } else {
                pre->right = NULL;
                printf("%d ", curr->data);
                curr = curr->right;
            }
        }
    }
}

int main() {
    struct Node* root = createNode(1);
    root->left = createNode(2);
    root->right = createNode(3);
    root->left->left = createNode(4);
    root->left->right = createNode(5);
    
    printf("Morris Inorder Traversal: ");
    morrisInorder(root);
    printf("\n");
    return 0;
}
```

### C++
```cpp
#include <iostream>
using namespace std;

struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};

void morrisInorder(TreeNode* root) {
    TreeNode* curr = root;
    while (curr != NULL) {
        if (curr->left == NULL) {
            cout << curr->val << " ";
            curr = curr->right;
        } else {
            TreeNode* pre = curr->left;
            while (pre->right != NULL && pre->right != curr) {
                pre = pre->right;
            }
            if (pre->right == NULL) {
                pre->right = curr;
                curr = curr->left;
            } else {
                pre->right = NULL;
                cout << curr->val << " ";
                curr = curr->right;
            }
        }
    }
}

int main() {
    TreeNode* root = new TreeNode(1);
    root->left = new TreeNode(2);
    root->right = new TreeNode(3);
    root->left->left = new TreeNode(4);
    root->left->right = new TreeNode(5);
    
    cout << "Morris Inorder Traversal: ";
    morrisInorder(root);
    cout << endl;
    return 0;
}
```

### Java
```java
class TreeNode {
    int val;
    TreeNode left, right;
    TreeNode(int item) {
        val = item;
        left = right = null;
    }
}

public class MorrisTraversal {
    public static void morrisInorder(TreeNode root) {
        TreeNode curr = root;
        while (curr != null) {
            if (curr.left == null) {
                System.out.print(curr.val + " ");
                curr = curr.right;
            } else {
                TreeNode pre = curr.left;
                while (pre.right != null && pre.right != curr) {
                    pre = pre.right;
                }
                if (pre.right == null) {
                    pre.right = curr;
                    curr = curr.left;
                } else {
                    pre.right = null;
                    System.out.print(curr.val + " ");
                    curr = curr.right;
                }
            }
        }
    }

    public static void main(String[] args) {
        TreeNode root = new TreeNode(1);
        root.left = new TreeNode(2);
        root.right = new TreeNode(3);
        root.left.left = new TreeNode(4);
        root.left.right = new TreeNode(5);

        System.out.print("Morris Inorder Traversal: ");
        morrisInorder(root);
        System.out.println();
    }
}
```

### Python
```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

def morris_inorder(root):
    curr = root
    result = []
    while curr:
        if curr.left is None:
            result.append(curr.val)
            curr = curr.right
        else:
            pre = curr.left
            while pre.right and pre.right != curr:
                pre = pre.right
            
            if pre.right is None:
                pre.right = curr
                curr = curr.left
            else:
                pre.right = None
                result.append(curr.val)
                curr = curr.right
    return result

if __name__ == "__main__":
    root = TreeNode(1)
    root.left = TreeNode(2)
    root.right = TreeNode(3)
    root.left.left = TreeNode(4)
    root.left.right = TreeNode(5)
    
    print("Morris Inorder Traversal:", " ".join(map(str, morris_inorder(root))))
```

### JavaScript
```javascript
class TreeNode {
    constructor(val, left = null, right = null) {
        this.val = val;
        this.left = left;
        this.right = right;
    }
}

function morrisInorder(root) {
    let curr = root;
    let result = [];
    while (curr !== null) {
        if (curr.left === null) {
            result.push(curr.val);
            curr = curr.right;
        } else {
            let pre = curr.left;
            while (pre.right !== null && pre.right !== curr) {
                pre = pre.right;
            }
            if (pre.right === null) {
                pre.right = curr;
                curr = curr.left;
            } else {
                pre.right = null;
                result.push(curr.val);
                curr = curr.right;
            }
        }
    }
    return result;
}

// Demo
let root = new TreeNode(1);
root.left = new TreeNode(2);
root.right = new TreeNode(3);
root.left.left = new TreeNode(4);
root.left.right = new TreeNode(5);

console.log("Morris Inorder Traversal:", morrisInorder(root).join(" "));
```

## 10. Code Explanation
- **`curr` initialization**: The traversal starts at the root node.
- **Checking `curr.left`**: If there's no left child, in-order rules say we visit `curr` and then go right.
- **Finding the predecessor**: We go to the left child and then as far right as possible.
- **Creating a thread**: If the predecessor's right child is null, we point it to `curr`. This allows us to return to `curr` after fully traversing the left subtree. We then proceed to `curr.left`.
- **Removing the thread**: If the predecessor's right child points back to `curr`, it means we've successfully returned from exploring the left subtree. We sever this temporary connection to restore the tree structure, visit `curr`, and then move on to `curr.right`.

## 11. Interactive Demo Description
A complete interactive web demo for Morris Traversal would feature:
1. **Tree Canvas**: A visual representation of a binary tree.
2. **Animation Controls**: Play, Pause, Next Step, Prev Step.
3. **Pointers Visualization**: Different colored arrows pointing to `curr` and `pre`.
4. **Temporary Threads**: Dashed lines dynamically drawn from predecessors to current nodes, appearing and disappearing to simulate the algorithm's state.
5. **Output Console**: A running array of visited node values.

## 12. Dry Run
Let's dry run the algorithm with this tree:
```text
      1
    /   \
   2     3
  / \
 4   5
```

**Trace Table:**

| Step | Curr | Predecessor | Action Taken | Output Array |
|---|---|---|---|---|
| 1 | 1 | 5 (from 2->5) | Thread 5->1 created, curr=2 | [] |
| 2 | 2 | 4 | Thread 4->2 created, curr=4 | [] |
| 3 | 4 | - | `curr.left` is null, visit 4, curr=4.right (2) | [4] |
| 4 | 2 | 4 | Pre.right is 2. Remove thread 4->null. Visit 2, curr=5 | [4, 2] |
| 5 | 5 | - | `curr.left` is null, visit 5, curr=5.right (1) | [4, 2, 5] |
| 6 | 1 | 5 | Pre.right is 1. Remove thread 5->null. Visit 1, curr=3 | [4, 2, 5, 1] |
| 7 | 3 | - | `curr.left` is null, visit 3, curr=3.right (null)| [4, 2, 5, 1, 3] |

## 13. Time & Space Complexity

| Case | Complexity | Reason |
|---|---|---|
| **Best Case Time** | $O(N)$ | Tree has no left children, traversing takes exactly N steps. |
| **Average Case Time**| $O(N)$ | Finding predecessors adds overhead, but every edge is traversed at most 3 times. |
| **Worst Case Time** | $O(N)$ | Same as average. The total number of edges traversed remains strictly proportional to N. |
| **Space Complexity** | $O(1)$ | No recursion stack or dynamic memory allocation is used, only a few pointers. |

## 14. Advantages
- **Optimal Space:** Achieves $O(1)$ space complexity, making it highly memory efficient.
- **Robust:** Immune to Stack Overflow errors regardless of the tree's depth or imbalance.

## 15. Disadvantages
- **Tree Modification:** Briefly alters the tree structure during execution, making it unsuitable for read-only trees.
- **Thread Safety:** Not safe for concurrent reads or modifications. If multiple threads run Morris Traversal on the same tree simultaneously, race conditions will corrupt the tree.
- **Slight Overhead:** Finding predecessors means some nodes/edges are visited multiple times, making the constant factor slightly higher than simple recursive approaches.

## 16. Applications
- Systems with strict memory limits (e.g., small embedded systems).
- Deep recursion problems where call stacks are small.
- Recovering Binary Search Trees (where Morris Traversal helps achieve $O(1)$ space).

## 17. Common Mistakes
- **Failing to restore the tree**: Forgetting the `predecessor.right = null` step leaves the tree permanently modified with cycles.
- **Infinite loops**: Incorrectly checking the condition `pre.right != curr` while finding the predecessor can result in an infinite loop.
- **Confusion between Preorder and Inorder**: Outputting the value during thread creation (instead of removal) yields Preorder, not Inorder.

## 18. Interview Questions
1. How does Morris Traversal achieve $O(1)$ space complexity?
2. Can you modify Morris Traversal to output a Preorder traversal?
3. Can you modify Morris Traversal to output a Postorder traversal?
4. Why is Morris Traversal generally not considered thread-safe?
5. How would you handle a tree structure that is read-only? (Answer: You cannot use standard Morris traversal).
6. What is the time complexity of Morris Traversal and why is it linear despite inner loops?
7. How many times is an edge visited in Morris Traversal?
8. Explain the concept of Threaded Binary Trees and how it relates.
9. What happens if the `curr` node has no left child?
10. Write the code for Morris Traversal on a whiteboard.

## 19. Practice Problems
- **Easy:** Inorder Traversal of a BST using Morris Traversal.
- **Medium:** Recover a Binary Search Tree (Two elements are swapped) using $O(1)$ space.
- **Hard:** Serialize and Deserialize a Binary Tree (evaluate if Morris is useful here).

## 20. Related Algorithms
- **Recursive Inorder Traversal:** The standard $O(N)$ space traversal.
- **Iterative Inorder Traversal:** Uses an explicit stack, $O(N)$ space.
- **Threaded Binary Trees:** A persistent data structure where the NULL pointers are permanently replaced with threads to predecessors/successors.

## 21. Summary
Morris Traversal is a brilliant technique leveraging the existing structure of a binary tree by temporarily reusing null right-child pointers. By setting up threads to in-order successors and carefully dismantling them upon return, it processes a full tree with strictly $O(1)$ extra memory. While slightly slower by a constant factor and not thread-safe due to intermediate state changes, its minimal memory footprint makes it an indispensable tool for systems engineering and competitive programming.

## 22. Quiz

**Q1: What is the space complexity of Morris Traversal?**
A) $O(N)$
B) $O(\log N)$
C) $O(1)$
D) $O(N \log N)$
*Correct Answer: C*
*Explanation: It uses only a couple of pointers for tracking state, eliminating the need for a call stack or external data structures.*

**Q2: How does Morris Traversal avoid using a stack?**
A) Using a queue instead.
B) Temporarily altering tree links.
C) Hashing node addresses.
D) Using a doubly linked list.
*Correct Answer: B*
*Explanation: It creates temporary links (threads) from predecessor nodes to their corresponding current nodes.*

**Q3: Is Morris Traversal thread-safe?**
A) Yes, completely safe.
B) No, because it temporarily modifies the tree structure.
C) Yes, if using locks.
D) Depends on the programming language.
*Correct Answer: B*
*Explanation: Concurrent processes traversing or reading the tree will encounter modified links and potential cycles.*

**Q4: Which traversal is easiest to implement with the Morris algorithm?**
A) Level-order
B) Inorder
C) Postorder
D) Boundary
*Correct Answer: B*
*Explanation: The natural threading pattern inherently aligns with `Left-Root-Right` inorder traversal, though preorder is also trivial.*

**Q5: What happens when the current node does not have a left child?**
A) The algorithm crashes.
B) The algorithm moves to the left child anyway.
C) The algorithm processes the node and moves to its right child.
D) The algorithm moves back to the root.
*Correct Answer: C*
*Explanation: Without a left subtree, there's no predecessor to thread, so it naturally visits the current node and proceeds right.*

**Q6: What is an In-order Predecessor?**
A) The parent node.
B) The rightmost node in a node's left subtree.
C) The leftmost node in a node's right subtree.
D) The root node.
*Correct Answer: B*
*Explanation: In an inorder traversal, the predecessor is the node visited immediately before the given node.*

**Q7: In the worst-case scenario, how many times is a single edge traversed?**
A) 1
B) 2
C) 3
D) 4
*Correct Answer: C*
*Explanation: Edges in the left subtrees are traversed to find the predecessor, to visit the node, and to remove the thread.*

**Q8: Can Morris Traversal be applied to read-only memory trees?**
A) Yes.
B) No.
C) Only if the tree is completely balanced.
D) Only if the tree has no right children.
*Correct Answer: B*
*Explanation: Modifying the right pointer of leaf nodes is required; read-only memory would throw a segmentation fault or error.*

**Q9: If we output the node value right BEFORE we create the thread, what traversal do we get?**
A) Inorder
B) Preorder
C) Postorder
D) Reverse Inorder
*Correct Answer: B*
*Explanation: Outputting when first visiting a node (before traversing left) yields a Preorder traversal.*

**Q10: Why do we have to remove the thread?**
A) To free memory.
B) To stop infinite loops.
C) To restore the original tree structure.
D) Both B and C.
*Correct Answer: D*
*Explanation: If threads are left, the tree contains cycles causing infinite loops in future basic traversals, and the original tree structure is corrupted.*
