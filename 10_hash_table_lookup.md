# Hash Table Lookup

## 1. Introduction

Hash Table Lookup is not a search algorithm in the traditional comparison-based sense. Instead of scanning through elements or dividing ranges, it uses a mathematical function called a hash function to compute the exact storage location of a value. Imagine a massive library where instead of searching shelf by shelf, you have a magical formula that tells you the exact shelf, row, and position of any book instantly. That is the power of hash tables.

It was created to solve the fundamental problem of fast data retrieval. When you need to check if an item exists, find its associated value, or update it, hash tables offer near-instant access regardless of how much data you have. They are the backbone of databases, caches, compilers, and countless other systems.

You should use Hash Table Lookup whenever you need frequent lookups, insertions, and deletions, and when the order of elements does not matter. It is the go-to data structure for exact-match searches.

## 2. Why Use This Algorithm?

Hash Table Lookup offers the fastest average-case performance for exact-match searching among all data structures.

**Benefits:**
- Average O(1) time complexity for lookups, insertions, and deletions
- No need for sorted data
- Extremely simple API: put, get, remove
- Scales efficiently to millions or billions of entries

**Performance:**
In the average case, a hash table computes an index in constant time and retrieves the value directly. Even with collisions (when two keys hash to the same index), well-designed hash tables maintain O(1) average performance with proper load factor management.

**When it is better than other algorithms:**
Hash Table Lookup dominates Binary Search, Linear Search, and all comparison-based searches when you only need exact matches and do not care about ordering. It is the reason why programming languages offer built-in hash maps and dictionaries.

## 3. Real-World Applications

- **Database indexing:** Hash indexes provide lightning-fast exact-match lookups.
- **Caching systems:** Redis and Memcached use hash tables to store and retrieve cached data.
- **Symbol tables in compilers:** Compilers use hash tables to look up variable and function names.
- **Phone books and contact lists:** Mapping names to phone numbers is a classic hash table use case.
- **Password verification:** Storing and checking password hashes securely.
- **URL shortening services:** Mapping short codes to full URLs.
- **Counting word frequencies:** Hash tables map words to their occurrence counts.
- **Duplicate detection:** Checking if an item has already been seen in a stream.

## 4. Prerequisites

Before learning Hash Table Lookup, you should know:
- Arrays and how indexing works
- Basic understanding of functions and mathematical operations
- What collisions are and why they happen
- The concept of time complexity and Big-O notation
- Linked lists (helpful for understanding collision resolution)

## 5. Visualization

Imagine a long row of mailboxes, each labeled with a number. When a letter arrives, a clerk uses a formula on the recipient's name to determine which mailbox number to open. Ideally, every name maps to a unique mailbox. But sometimes two names produce the same number. In that case, the clerk puts a small basket inside that mailbox to hold multiple letters. When retrieving, the clerk goes to the computed mailbox, checks the basket, and finds the right letter by comparing names.

## 6. How It Works

A hash table stores key-value pairs. When you want to store a value, you pass the key through a hash function, which converts it into an integer index. You store the value at that index in an underlying array. When you want to retrieve it, you hash the key again, go to that index, and retrieve the value. If multiple keys hash to the same index (a collision), the hash table uses a strategy like chaining (storing a list at that index) or open addressing (finding another empty slot) to handle it.

## 7. Step-by-Step Algorithm

1. Choose a hash function that maps keys to integer indices.
2. Choose an underlying array size (preferably a prime number or power of 2).
3. To insert a key-value pair:
   1. Compute `index = hash(key) % array_size`.
   2. If the slot is empty, store the pair there.
   3. If occupied, resolve the collision using chaining or probing.
4. To lookup a key:
   1. Compute `index = hash(key) % array_size`.
   2. If using chaining, traverse the list at that index and compare keys.
   3. If using open addressing, probe until the key is found or an empty slot is reached.
5. To delete a key:
   1. Find the key using the lookup process.
   2. Remove it and mark the slot appropriately (especially important for open addressing).

## 8. Pseudocode

```
function hash(key):
    return some_integer_based_on_key

function put(hashTable, key, value):
    index = hash(key) % hashTable.size
    if hashTable[index] is empty:
        hashTable[index] = new list containing (key, value)
    else:
        for each pair in hashTable[index]:
            if pair.key == key:
                pair.value = value
                return
        append (key, value) to hashTable[index]

function get(hashTable, key):
    index = hash(key) % hashTable.size
    for each pair in hashTable[index]:
        if pair.key == key:
            return pair.value
    return null
```

## 9. Code Examples

### C
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define TABLE_SIZE 10

typedef struct Node {
    int key;
    int value;
    struct Node* next;
} Node;

Node* table[TABLE_SIZE];

int hash(int key) {
    return key % TABLE_SIZE;
}

void put(int key, int value) {
    int index = hash(key);
    Node* current = table[index];
    while (current != NULL) {
        if (current->key == key) {
            current->value = value;
            return;
        }
        current = current->next;
    }
    Node* newNode = (Node*)malloc(sizeof(Node));
    newNode->key = key;
    newNode->value = value;
    newNode->next = table[index];
    table[index] = newNode;
}

int get(int key, int* found) {
    int index = hash(key);
    Node* current = table[index];
    while (current != NULL) {
        if (current->key == key) {
            *found = 1;
            return current->value;
        }
        current = current->next;
    }
    *found = 0;
    return -1;
}

int main() {
    put(1, 100);
    put(11, 200);  // collision with key 1
    int found;
    int val = get(11, &found);
    if (found)
        printf("Value: %d\n", val);
    else
        printf("Key not found\n");
    return 0;
}
```

### C++
```cpp
#include <iostream>
#include <unordered_map>
using namespace std;

int main() {
    unordered_map<string, int> phoneBook;
    phoneBook["Alice"] = 12345;
    phoneBook["Bob"] = 67890;

    string name = "Alice";
    auto it = phoneBook.find(name);
    if (it != phoneBook.end()) {
        cout << name << "'s number: " << it->second << endl;
    } else {
        cout << "Not found" << endl;
    }
    return 0;
}
```

### Java
```java
import java.util.HashMap;

public class HashTableLookup {
    public static void main(String[] args) {
        HashMap<String, Integer> phoneBook = new HashMap<>();
        phoneBook.put("Alice", 12345);
        phoneBook.put("Bob", 67890);

        String name = "Alice";
        if (phoneBook.containsKey(name)) {
            System.out.println(name + "'s number: " + phoneBook.get(name));
        } else {
            System.out.println("Not found");
        }
    }
}
```

### Python
```python
phone_book = {
    "Alice": 12345,
    "Bob": 67890
}

name = "Alice"
if name in phone_book:
    print(f"{name}'s number: {phone_book[name]}")
else:
    print("Not found")
```

### JavaScript
```javascript
const phoneBook = {
    "Alice": 12345,
    "Bob": 67890
};

const name = "Alice";
if (phoneBook.hasOwnProperty(name)) {
    console.log(`${name}'s number: ${phoneBook[name]}`);
} else {
    console.log("Not found");
}
```

## 10. Code Explanation

The C example shows a manual implementation using separate chaining. Each array slot holds a linked list of entries that hashed to the same index. When inserting, we first check if the key already exists to update it. When retrieving, we traverse the linked list at the computed index until we find a matching key. The C++, Java, Python, and JavaScript examples use built-in hash table implementations, which handle all the complexity internally. The key takeaway is that the user only needs to call `put` and `get`; the hash function and collision resolution are hidden.

## 11. Interactive Demo

The demo displays a grid of mailboxes representing the hash table array. The user can type a key and value, then click "Insert." The demo shows the hash calculation, highlights the target mailbox, and if a collision occurs, shows a small chain of items forming at that mailbox. For lookup, the user enters a key, clicks "Find," and the demo animates the hash computation, jumps to the mailbox, and traverses the chain if needed. A counter tracks the number of probes. The user can also click "Resize" to see the rehashing process when the table becomes too full.

## 12. Dry Run

**Sample Operations:**
Table size: `10`
Hash function: `key % 10`

| Operation | Key | Hash | Index | Action |
|-----------|-----|------|-------|--------|
| Insert | 15 | 15 % 10 = 5 | 5 | Store at index 5 |
| Insert | 25 | 25 % 10 = 5 | 5 | Collision! Chain after 15 |
| Insert | 7 | 7 % 10 = 7 | 7 | Store at index 7 |
| Lookup | 25 | 25 % 10 = 5 | 5 | Check chain: 15 != 25, next is 25. Found! |
| Lookup | 99 | 99 % 10 = 9 | 9 | Empty. Not found. |

**Final Output:** Lookup 25 returns value; Lookup 99 returns not found.

## 13. Time & Space Complexity

| Case | Time Complexity | Reason |
|------|-----------------|--------|
| Best Case | O(1) | No collision; direct array access |
| Average Case | O(1) | With a good hash function and load factor < 0.75, chains are short |
| Worst Case | O(n) | All keys collide; degenerates to a linked list |
| Space Complexity | O(n) | Stores all n key-value pairs plus array overhead |

## 14. Advantages

- **Fastest average-case lookups:** O(1) is unbeatable for exact matches.
- **No sorting required:** Insert data in any order.
- **Flexible keys:** Can hash strings, objects, or any data type.
- **Efficient updates:** Insert, delete, and update are all O(1) average.
- **Built into most languages:** Easy to use without implementing from scratch.

## 15. Disadvantages

- **No ordering:** Hash tables do not maintain any order of elements.
- **Slow worst case:** Poor hash functions or malicious inputs can cause O(n) behavior.
- **Memory overhead:** Requires extra space for the array and pointers.
- **Not ideal for range queries:** Finding all keys between A and B is inefficient.
- **Hash function dependency:** Performance heavily depends on the quality of the hash function.

## 16. Applications

- Database indexing for exact-match queries
- Caching and memoization
- Compiler symbol tables
- Counting frequencies (word count, vote tallying)
- Detecting duplicates in large datasets
- Implementing sets for fast membership testing
- Session storage in web applications
- Routing tables in networking

## 17. Common Mistakes

- **Using a poor hash function:** A bad hash function causes excessive collisions.
- **Ignoring the load factor:** When the table is too full, performance degrades; resizing is necessary.
- **Forgetting to handle collisions:** Without chaining or probing, data will be overwritten.
- **Mutating keys after insertion:** If a key object changes its hash value after being inserted, it becomes unfindable.
- **Not overriding hashCode and equals in Java:** Custom objects must implement these correctly to work as hash table keys.

## 18. Interview Questions

1. What is the average-case time complexity of hash table lookup, insertion, and deletion?
2. What causes collisions in a hash table, and how are they resolved?
3. What is the difference between separate chaining and open addressing?
4. Why is the load factor important in hash tables?
5. What makes a good hash function?
6. Can you use a mutable object as a hash table key? Why or why not?
7. What is rehashing, and when does it occur?
8. How would you implement a hash table from scratch?
9. What is the worst-case time complexity, and when does it happen?
10. Compare hash tables with balanced binary search trees for dictionary implementations.

## 19. Practice Problems

**Easy:**
1. Implement a basic hash table with integer keys using separate chaining.
2. Count the frequency of each character in a string using a hash table.
3. Find the first non-repeating character in a string.
4. Check if two arrays have the same elements (ignoring order).

**Medium:**
5. Implement a hash table that supports resizing when the load factor exceeds 0.75.
6. Design a hash table for storing student records with string IDs.
7. Find all pairs in an array that sum to a given target using a hash table.
8. Implement an LRU (Least Recently Used) cache using a hash table and linked list.

**Hard:**
9. Implement a perfect hash function for a static set of known keys.
10. Design a concurrent hash table that supports thread-safe operations.
11. Implement consistent hashing for distributed caching systems.

## 20. Related Algorithms

- Binary Search Tree (ordered lookups)
- Trie (prefix-based string lookups)
- Bloom Filter (probabilistic membership testing)
- Linear Search (fallback when hash tables fail)
- Direct Addressing Table (hash table with no collisions, when keys are small integers)

## 21. Summary

Hash Table Lookup is the cornerstone of fast data retrieval. By using a hash function to map keys directly to array indices, it achieves O(1) average-case performance for lookups, insertions, and deletions. While collisions and memory overhead are concerns, a well-designed hash table with a good hash function and proper load factor management is one of the most powerful tools in a programmer's arsenal. Use it whenever you need fast exact-match access and do not require ordered traversal.

## 22. Quiz

**Question 1:** What is the average-case time complexity of a hash table lookup?
- A) O(log n)
- B) O(n)
- C) O(1)
- D) O(n log n)
- **Correct Answer:** C
- **Explanation:** A good hash function maps keys directly to indices, enabling constant-time access.

**Question 2:** What happens when two keys produce the same hash index?
- A) The program crashes
- B) A collision occurs
- C) The table automatically sorts
- D) The second key is rejected
- **Correct Answer:** B
- **Explanation:** This is called a collision and must be handled by chaining or probing.

**Question 3:** Which of these is a collision resolution strategy?
- A) Binary Search
- B) Separate chaining
- C) Quick Sort
- D) Merge Sort
- **Correct Answer:** B
- **Explanation:** Separate chaining stores multiple items in a linked list at the same index.

**Question 4:** What is the load factor of a hash table?
- A) The size of the hash function
- B) The ratio of stored elements to table size
- C) The number of collisions
- D) The memory usage in bytes
- **Correct Answer:** B
- **Explanation:** Load factor = n / table_size; high values trigger resizing.

**Question 5:** Why should hash table keys be immutable?
- A) To save memory
- B) Changing the key changes its hash, making it unfindable
- C) To allow sorting
- D) To prevent collisions
- **Correct Answer:** B
- **Explanation:** If a key's hash changes after insertion, the lookup will compute a different index.

**Question 6:** What is the worst-case time complexity?
- A) O(1)
- B) O(log n)
- C) O(n)
- D) O(n^2)
- **Correct Answer:** C
- **Explanation:** If all keys collide, the table degenerates to a linked list.

**Question 7:** What is rehashing?
- A) Searching for a key again
- B) Resizing the table and recomputing all hash indices
- C) Sorting the hash table
- D) Deleting all entries
- **Correct Answer:** B
- **Explanation:** When the table grows, all existing entries must be placed into the new larger array.

**Question 8:** Which data structure is better for finding all elements in a range [A, B]?
- A) Hash table
- B) Balanced binary search tree
- C) Array
- D) Stack
- **Correct Answer:** B
- **Explanation:** Hash tables do not maintain order; trees support ordered range queries.

**Question 9:** What does a hash function do?
- A) Sorts the keys
- B) Maps a key to an integer index
- C) Deletes old entries
- D) Resizes the array
- **Correct Answer:** B
- **Explanation:** The hash function converts a key into an array index.

**Question 10:** Which language feature is a hash table implementation?
- A) Java ArrayList
- B) Python dict
- C) C++ vector
- D) JavaScript Array
- **Correct Answer:** B
- **Explanation:** Python dictionaries are implemented as hash tables.
