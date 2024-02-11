# Prefix Sum
Certainly! Prefix sum is a technique commonly used in coding interviews and competitive programming to efficiently solve problems related to cumulative sums or running sums. It involves precomputing the cumulative sum of elements in an array and then using these precomputed values to answer queries about subarrays in constant time.

Here's a step-by-step explanation of how the prefix sum technique works:

### 1. **Compute the Prefix Sum Array:**
   - Given an array `arr` of size `n`, create a new array `prefix_sum` of size `n`.
   - Initialize `prefix_sum[0]` with the value of `arr[0]`.
   - For each index `i` from 1 to `n-1`, compute `prefix_sum[i] = prefix_sum[i-1] + arr[i]`.

   ```python
   def compute_prefix_sum(arr):
       n = len(arr)
       prefix_sum = [0] * n
       prefix_sum[0] = arr[0]

       for i in range(1, n):
           prefix_sum[i] = prefix_sum[i - 1] + arr[i]

       return prefix_sum
   ```

### 2. **Use Prefix Sum to Answer Queries:**
   - Once the prefix sum array is computed, it allows for quick computation of the sum of any subarray `[i, j]` in constant time.
   - The sum of elements in the subarray `[i, j]` can be calculated as `prefix_sum[j] - prefix_sum[i-1]` (if `i > 0`) or simply as `prefix_sum[j]` (if `i == 0`).

   ```python
   def query_sum(prefix_sum, i, j):
       if i == 0:
           return prefix_sum[j]
       else:
           return prefix_sum[j] - prefix_sum[i - 1]
   ```

### Example:
   Let's say we have an array `arr = [1, 2, 3, 4, 5]`. The corresponding prefix sum array would be `[1, 3, 6, 10, 15]`. Now, if we want to find the sum of elements from index 1 to 3 (inclusive), we can use the prefix sum array: `prefix_sum[3] - prefix_sum[0] = 10 - 1 = 9`.

The prefix sum technique is particularly useful for efficiently solving problems involving cumulative sums, such as range queries and subarray sum problems. It helps avoid repetitive calculations and improves the overall time complexity of the solution.

#Sliding Window
The sliding window technique is another powerful approach used to solve problems involving arrays, strings, or other linear data structures efficiently. It works by maintaining a window of elements within the array that satisfies certain conditions and then sliding the window across the array to find the optimal solution.

Here's how the sliding window technique works:

### 1. **Initialize the Window:**
   - Start with two pointers, typically initialized to the beginning of the array.
   - These pointers define the window boundaries.

### 2. **Expand the Window:**
   - Move the right pointer to expand the window while the window still satisfies the given conditions or constraints.

### 3. **Shrink the Window:**
   - Once the window no longer satisfies the conditions, move the left pointer to shrink the window, removing elements until the conditions are satisfied again.

### 4. **Update the Optimal Solution:**
   - While expanding and shrinking the window, update the optimal solution if necessary.

### 5. **Repeat:**
   - Continue the process until the right pointer reaches the end of the array.

The sliding window technique is particularly useful for solving problems that involve finding a subarray or substring satisfying certain conditions (e.g., maximum sum subarray, minimum length substring with all characters distinct, etc.).

### Example Problem: Maximum Sum Subarray of Size K
Given an array of integers and an integer k, find the maximum sum of a subarray of size k.

#### Approach using Sliding Window:
```python
def max_subarray_sum(arr, k):
    max_sum = float('-inf')
    current_sum = 0
    left = 0

    for right in range(len(arr)):
        current_sum += arr[right]

        if right - left + 1 == k:
            max_sum = max(max_sum, current_sum)
            current_sum -= arr[left]
            left += 1

    return max_sum

# Example usage:
arr = [1, 3, -1, -3, 5, 3, 6, 7]
k = 3
print(max_subarray_sum(arr, k))  # Output: 16 (subarray [5, 3, 6] has the maximum sum)
```

In this example, we maintain a window of size k and slide it across the array. As we slide the window, we update the current sum by adding the new element and subtracting the element that falls outside the window. We continuously track the maximum sum encountered during the process.

The sliding window technique provides an efficient solution to many problems and often reduces the time complexity from O(n^2) to O(n) or O(n log n).

The term "Two Pointers" generally refers to a technique in computer science and programming where two pointers or indices are used to traverse a data structure (like an array or a linked list) in a way that helps solve a particular problem more efficiently. This technique is often used for optimization purposes, reducing time complexity or space complexity.

There are two main variations of the Two Pointers technique:

1. **Two Pointers Moving Towards Each Other:**
   - Initialize two pointers at both ends of the array.
   - Move the pointers towards each other until they meet or cross each other.

   **Example Problem:**
   - Finding if a given array is a palindrome.

   ```java
   boolean isPalindrome(int[] array) {
       int left = 0;
       int right = array.length - 1;

       while (left < right) {
           if (array[left] != array[right]) {
               return false;
           }
           left++;
           right--;
       }
       return true;
   }
   ```

2. **Two Pointers Moving in the Same Direction:**
   - Initialize two pointers at the same end of the array.
   - Move one pointer faster than the other based on the problem requirements.

   **Example Problem:**
   - Finding pairs in a sorted array that sum up to a target value.

   ```java
   void findPairsWithSum(int[] array, int target) {
       int left = 0;
       int right = array.length - 1;

       while (left < right) {
           int currentSum = array[left] + array[right];
           if (currentSum == target) {
               System.out.println("Pair found: " + array[left] + ", " + array[right]);
               left++;
               right--;
           } else if (currentSum < target) {
               left++;
           } else {
               right--;
           }
       }
   }
   ```

In both variations, the key idea is to use the two pointers to navigate through the data structure in a way that is more efficient than alternative approaches. The specific problem will dictate how the pointers should move and interact with the data to find the solution. The Two Pointers technique is often used in conjunction with sorting or sliding window techniques.

#hashing
In the context of data structures and algorithms, hashing is a technique used to map data of arbitrary size to fixed-size values, typically for the purpose of fast data retrieval. The primary goal of hashing is to quickly locate a data record given its search key. Here's a basic overview of hashing in data structures and algorithms:

### Hash Function:

A hash function is a mathematical function that takes an input (or 'key') and produces a fixed-size string of characters, which is usually a hash code. The goal of a good hash function is to minimize collisions (two different inputs producing the same hash code).

### Hash Table:

A hash table is a data structure that uses a hash function to map keys to values. It is essentially an array where data elements are stored in locations determined by the hash code of their keys. The hash code is used as an index to access the elements. This allows for fast retrieval of data, assuming a good hash function and minimal collisions.

### Basic Steps:

1. **Hash Function:**
   - The hash function takes a key as input and produces a hash code.
   - Ideally, the hash function should distribute keys uniformly across the hash table to minimize collisions.

2. **Mapping to Array Index:**
   - The hash code is then used to map the key to an index in the array (hash table).
   - Sometimes, the hash code is too large for the array size, so a modulo operation is applied to get a valid index.

3. **Handling Collisions:**
   - Collisions occur when two different keys hash to the same index.
   - There are various techniques to handle collisions, such as chaining (using linked lists at each array index) or open addressing (finding the next open slot in the array).

4. **Retrieval and Insertion:**
   - To retrieve a value, the key is hashed, the index is calculated, and the value is found at that index.
   - To insert a new key-value pair, the key is hashed, the index is calculated, and the value is stored at that index.

### Example (using Python):

```python
class HashTable:
    def __init__(self, size):
        self.size = size
        self.table = [None] * size

    def hash_function(self, key):
        # Simple hash function for illustration purposes
        return len(key) % self.size

    def insert(self, key, value):
        index = self.hash_function(key)
        if self.table[index] is None:
            self.table[index] = [(key, value)]
        else:
            # Handle collision using chaining (appending to a linked list)
            self.table[index].append((key, value))

    def search(self, key):
        index = self.hash_function(key)
        if self.table[index] is not None:
            for k, v in self.table[index]:
                if k == key:
                    return v
        return None

# Example usage:
hash_table = HashTable(10)
hash_table.insert("apple", 5)
hash_table.insert("banana", 8)
hash_table.insert("cherry", 12)

print(hash_table.search("banana"))  # Output: 8
```

Keep in mind that this is a simple example, and real-world scenarios may involve more sophisticated hash functions and collision resolution strategies. Choosing an appropriate hash function is crucial for the efficiency of a hash table. Additionally, the size of the hash table should be chosen carefully to balance between space efficiency and the likelihood of collisions.

In a hash table, checking for the existence of a key involves using the hash function to determine the index where the key might be located, and then checking that specific index for the presence of the key. If collisions are handled using a method like chaining, you may need to traverse the linked list at that index.

Here's how you might implement a method to check for the existence of a key in the `HashTable` example I provided earlier:
```
import java.util.LinkedList;

class HashTable {
    private int size;
    private LinkedList<Pair>[] table;

    public HashTable(int size) {
        this.size = size;
        this.table = new LinkedList[size];
    }

    private int hashFunction(String key) {
        // Simple hash function for illustration purposes
        return key.length() % size;
    }

    public void insert(String key, int value) {
        int index = hashFunction(key);
        if (table[index] == null) {
            table[index] = new LinkedList<>();
        }
        table[index].add(new Pair(key, value));
    }

    public boolean containsKey(String key) {
        int index = hashFunction(key);
        if (table[index] != null) {
            for (Pair pair : table[index]) {
                if (pair.getKey().equals(key)) {
                    return true;
                }
            }
        }
        return false;
    }

    public int get(String key) {
        int index = hashFunction(key);
        if (table[index] != null) {
            for (Pair pair : table[index]) {
                if (pair.getKey().equals(key)) {
                    return pair.getValue();
                }
            }
        }
        // Return a default value or throw an exception if key not found
        return -1;
    }

    private static class Pair {
        private String key;
        private int value;

        public Pair(String key, int value) {
            this.key = key;
            this.value = value;
        }

        public String getKey() {
            return key;
        }

        public int getValue() {
            return value;
        }
    }
}

public class Main {
    public static void main(String[] args) {
        HashTable hashTable = new HashTable(10);
        hashTable.insert("apple", 5);
        hashTable.insert("banana", 8);
        hashTable.insert("cherry", 12);

        System.out.println(hashTable.containsKey("banana"));  // Output: true
        System.out.println(hashTable.containsKey("grape"));   // Output: false
        System.out.println(hashTable.get("apple"));           // Output: 5
        System.out.println(hashTable.get("orange"));          // Output: -1 (default value)
    }
}

```

In this example, the `contains_key` method checks whether a given key is present in the hash table. It calculates the index using the hash function, then searches the list at that index for the specified key. If the key is found, the method returns `True`; otherwise, it returns `False`.

Keep in mind that this is a basic example, and in a real-world scenario, you might want to consider more advanced techniques for handling collisions and optimizing the hash function for better distribution.

In competitive programming, hashing can be employed across various problem domains to optimize algorithms and solve problems efficiently. Here are several ways hashing is used in Java for competitive programming:

1. **Detecting Duplicates:**
   - Use a HashSet to store elements encountered while traversing an array. If a duplicate element is encountered, the HashSet's constant-time lookup helps in quickly identifying duplicates.

   ```java
   HashSet<Integer> set = new HashSet<>();
   for (int num : nums) {
       if (!set.add(num)) {
           // Duplicate found
           return true;
       }
   }
   ```

2. **Frequency Counting:**
   - Utilize HashMap to count the frequency of elements in an array or string. This is useful in problems where you need to find the most frequent element or analyze character occurrences.

   ```java
   HashMap<Character, Integer> frequencyMap = new HashMap<>();
   for (char ch : str.toCharArray()) {
       frequencyMap.put(ch, frequencyMap.getOrDefault(ch, 0) + 1);
   }
   ```

3. **Set Operations:**
   - Perform set operations such as union, intersection, and difference using HashSet. This can be valuable in problems involving set manipulation or finding common elements.

   ```java
   Set<Integer> set1 = new HashSet<>(Arrays.asList(1, 2, 3));
   Set<Integer> set2 = new HashSet<>(Arrays.asList(2, 3, 4));
   Set<Integer> union = new HashSet<>(set1);
   union.addAll(set2);
   ```

4. **Rapid Lookup:**
   - Use HashMap or HashSet to quickly lookup values based on keys. This is useful in problems requiring quick data retrieval or checking for the existence of certain elements.

   ```java
   HashMap<Integer, String> map = new HashMap<>();
   if (map.containsKey(key)) {
       String value = map.get(key);
   }
   ```

5. **Memoization:**
   - Employ HashMap for memoization in dynamic programming problems. This helps avoid redundant computations by storing intermediate results.

   ```java
   HashMap<Integer, Integer> memo = new HashMap<>();
   int fib(int n) {
       if (n <= 1) return n;
       if (!memo.containsKey(n)) {
           memo.put(n, fib(n - 1) + fib(n - 2));
       }
       return memo.get(n);
   }
   ```

6. **String Matching:**
   - Utilize rolling hash or other hashing techniques like Rabin-Karp for efficient string matching algorithms.

   ```java
   // Rolling hash implementation for substring matching
   long rollingHash(String s, int start, int end) {
       long hash = 0;
       for (int i = start; i <= end; i++) {
           hash = hash * base + s.charAt(i);
       }
       return hash;
   }
   ```

These examples demonstrate how hashing can be used in competitive programming using Java to optimize algorithms, enhance data retrieval, and solve various types of problems efficiently.

# Stack
Certainly! In computer science, a stack is a data structure that follows the Last In, First Out (LIFO) principle. This means that the last element added to the stack is the first one to be removed. Think of it like a stack of plates – you can only take the top plate off the stack.

In Java, you can implement a stack using the `Stack` class, which is part of the `java.util` package. However, it's more common to use the `Deque` interface, specifically the `ArrayDeque` or `LinkedList` classes that implement it, for stack operations. Here's a simple example using `Deque`:

```java
import java.util.ArrayDeque;
import java.util.Deque;

public class StackExample {
    public static void main(String[] args) {
        // Creating a stack using ArrayDeque
        Deque<Integer> stack = new ArrayDeque<>();

        // Pushing elements onto the stack
        stack.push(10);
        stack.push(20);
        stack.push(30);

        // Popping elements from the stack
        System.out.println("Popped element: " + stack.pop());
        System.out.println("Popped element: " + stack.pop());

        // Peek at the top element without removing it
        System.out.println("Top element: " + stack.peek());

        // Check if the stack is empty
        System.out.println("Is stack empty? " + stack.isEmpty());
    }
}
```

Here's a breakdown of the operations:

- `push(element)`: Adds an element to the top of the stack.
- `pop()`: Removes and returns the element from the top of the stack.
- `peek()`: Returns the element on the top of the stack without removing it.
- `isEmpty()`: Checks if the stack is empty.

In this example, the stack starts with elements 10, 20, and 30. We then pop elements from the stack and print the top element without removing it. Finally, we check if the stack is empty.

Remember to handle cases where the stack is empty before performing pop or peek operations to avoid exceptions.

# Queue  
In Java, you can implement a queue data structure using the `Queue` interface, which is part of the `java.util` package. A common implementation of the `Queue` interface is the `LinkedList` class. Here's an example of how to use a `LinkedList` as a queue:

```java
import java.util.LinkedList;
import java.util.Queue;

public class QueueExample {
    public static void main(String[] args) {
        // Creating a queue using LinkedList
        Queue<String> queue = new LinkedList<>();

        // Enqueue (add) elements to the queue
        queue.offer("Apple");
        queue.offer("Banana");
        queue.offer("Orange");

        // Dequeue (remove) elements from the queue
        System.out.println("Dequeued element: " + queue.poll());
        System.out.println("Dequeued element: " + queue.poll());

        // Peek at the front element without removing it
        System.out.println("Front element: " + queue.peek());

        // Check if the queue is empty
        System.out.println("Is queue empty? " + queue.isEmpty());
    }
}
```

Here's a breakdown of the operations:

- `offer(element)`: Adds an element to the end of the queue.
- `poll()`: Removes and returns the element from the front of the queue.
- `peek()`: Returns the element at the front of the queue without removing it.
- `isEmpty()`: Checks if the queue is empty.

In this example, the queue starts with elements "Apple," "Banana," and "Orange." We then dequeue elements from the queue and print the front element without removing it. Finally, we check if the queue is empty.

Remember to handle cases where the queue is empty before performing poll or peek operations to avoid exceptions. If you need a thread-safe implementation, you might want to consider `java.util.concurrent.LinkedBlockingQueue` or other concurrent queue implementations in the `java.util.concurrent` package.

# LinkedList
In Java, a linked list is a data structure that consists of nodes, where each node contains data and a reference (or link) to the next node in the sequence. Java provides the `LinkedList` class, which is part of the `java.util` package, to implement a doubly-linked list. Here's a simple example:

```java
import java.util.LinkedList;

public class LinkedListExample {
    public static void main(String[] args) {
        // Creating a linked list
        LinkedList<String> linkedList = new LinkedList<>();

        // Adding elements to the linked list
        linkedList.add("Apple");
        linkedList.add("Banana");
        linkedList.add("Orange");

        // Displaying the linked list elements
        System.out.println("Linked List: " + linkedList);

        // Adding an element at a specific position
        linkedList.add(1, "Grapes");

        // Removing an element by value
        linkedList.remove("Banana");

        // Displaying the updated linked list elements
        System.out.println("Updated Linked List: " + linkedList);

        // Accessing elements by index
        System.out.println("Element at index 2: " + linkedList.get(2));

        // Checking if the linked list contains an element
        System.out.println("Contains 'Apple'? " + linkedList.contains("Apple"));

        // Checking if the linked list is empty
        System.out.println("Is linked list empty? " + linkedList.isEmpty());
    }
}
```

Here's a breakdown of the operations:

- `add(element)`: Adds an element to the end of the linked list.
- `add(index, element)`: Inserts an element at the specified position in the linked list.
- `remove(element)`: Removes the first occurrence of the specified element from the linked list.
- `get(index)`: Returns the element at the specified position in the linked list.
- `contains(element)`: Checks if the linked list contains the specified element.
- `isEmpty()`: Checks if the linked list is empty.

In this example, we create a linked list, add elements to it, perform some operations like adding at a specific position and removing by value, and then display the updated linked list. You can adapt this example based on your specific requirements.

Certainly! To delve deeper into binary trees and tackle common problems in competitive programming, let's expand on specific concepts and strategies:

### Binary Tree Concepts:

1. **Complete Binary Trees:**
   - Every level, except possibly the last, is completely filled, and all nodes are as left as possible.

2. **Perfect Binary Trees:**
   - All internal nodes have exactly two children.
   - All leaf nodes are at the same level.

3. **Balanced Binary Trees:**
   - The height of the left and right subtrees of any node differs by at most one.

4. **Types of Binary Trees:**
   - **Full Binary Tree:**
     - Every node has 0 or 2 children.
   - **Degenerate (or pathological) Tree:**
     - Every parent node has only one associated child node.

### Binary Tree Traversal:

1. **In-order Traversal (LNR):**
   - Visit left subtree, visit root, visit right subtree.

2. **Pre-order Traversal (NLR):**
   - Visit root, visit left subtree, visit right subtree.

3. **Post-order Traversal (LRN):**
   - Visit left subtree, visit right subtree, visit root.

### Strategies for Problem Solving:

1. **Recursion:**
   - Many binary tree problems can be elegantly solved using recursive algorithms.
   - Divide the problem into smaller subproblems for left and right subtrees.

2. **Tree Construction:**
   - Given an array or list, construct a binary tree based on specific rules (e.g., inorder and preorder, or inorder and postorder).

3. **Height and Depth:**
   - Utilize the depth and height of nodes for efficient traversal and problem-solving.
   - Dynamic programming can be employed to optimize recursive solutions by memoization.

4. **Binary Search Tree (BST) Properties:**
   - Leverage the properties of BSTs for efficient searching, insertion, and deletion.

5. **Common Problems:**
   - **Binary Tree Diameter:**
     - Find the length of the longest path between any two nodes.
   - **Lowest Common Ancestor (LCA):**
     - Find the lowest common ancestor of two nodes in a binary tree.
   - **Binary Tree Level Order Traversal:**
     - Traverse the tree level by level, storing nodes at each level.
   - **Serialize and Deserialize a Binary Tree:**
     - Convert a binary tree to a string representation and reconstruct the tree from the string.


Remember, consistent practice and exposure to a variety of problems are key to becoming proficient in solving binary tree problems in competitive programming.

Certainly! Tackling binary tree problems effectively involves a systematic approach. Here's a step-by-step guide to help you navigate through similar problems:

### 1. **Understand the Problem:**
   - Carefully read the problem statement to understand the requirements and constraints.
   - Identify the key operations to be performed on the binary tree.

### 2. **Define the Tree Structure:**
   - If the problem doesn't provide a tree structure, decide how you'll represent it. You might need to create a `TreeNode` class or similar data structure.

### 3. **Choose the Right Traversal Technique:**
   - Different problems may require different traversal methods (in-order, pre-order, post-order, level-order). Choose the one that fits the problem's requirements.

### 4. **Use Recursion Effectively:**
   - Many binary tree problems can be elegantly solved using recursion.
   - Identify the base case and recursive cases.
   - Break down the problem into smaller subproblems for left and right subtrees.

### 5. **Leverage Properties of Binary Search Trees (BST):**
   - If the problem involves a BST, remember to exploit its properties:
     - In-order traversal of a BST results in a sorted list.
     - Use the property of left < root < right to optimize searches, insertions, and deletions.

### 6. **Handle Special Cases:**
   - Account for edge cases, such as an empty tree, a tree with only one node, or situations where specific nodes are not present.

### 7. **Optimize Time and Space Complexity:**
   - Analyze the time and space complexity of your solution. Optimize your code to run efficiently.
   - Consider using memoization or other techniques to avoid redundant computations.

### 8. **Test Thoroughly:**
   - Design test cases that cover various scenarios, including edge cases.
   - Verify that your solution works for trees of different sizes and shapes.

### 9. **Debugging:**
   - If your code doesn't work as expected, use debugging techniques to identify and fix issues.
   - Print intermediate values and trace the execution of your code step by step.

### 10. **Learn from Solutions:**
   - After solving a problem, review other solutions on platforms like LeetCode or GitHub.
   - Understand alternative approaches and learn from different coding styles.

### 11. **Practice Regularly:**
   - Practice is key to becoming proficient. Solve a variety of binary tree problems regularly to build your skills.
   - Participate in coding competitions to challenge yourself and gain exposure to different problem-solving scenarios.

### 12. **Expand Your Toolkit:**
   - Familiarize yourself with standard Java libraries for tree-related operations, such as `Queue` for level-order traversal or `Stack` for iterative solutions.

Remember that becoming adept at solving binary tree problems takes time and consistent practice. As you tackle more problems, you'll develop a strong intuition for recognizing patterns and applying appropriate techniques.

Certainly! In competitive programming, you'll encounter a variety of binary tree problems that test your understanding of tree structures, traversal algorithms, and problem-solving skills. Let's discuss a few common problems, the approaches to solve them, and provide Java code snippets.

### 1. **Binary Tree Diameter:**

**Problem:**
   - Find the length of the longest path between any two nodes in a binary tree.

**Approach:**
   - Use a recursive depth-first search (DFS) to calculate the depth of each node.
   - For each node, calculate the sum of depths of its left and right subtrees.
   - Update the diameter (the longest path) if it's greater than the current diameter.

**Java Code:**
  ```java
  class Solution {
      int diameter = 0;

      public int diameterOfBinaryTree(TreeNode root) {
          calculateDiameter(root);
          return diameter;
      }

      private int calculateDiameter(TreeNode node) {
          if (node == null) return 0;

          int leftDepth = calculateDiameter(node.left);
          int rightDepth = calculateDiameter(node.right);

          diameter = Math.max(diameter, leftDepth + rightDepth);

          return 1 + Math.max(leftDepth, rightDepth);
      }
  }
  ```

### 2. **Lowest Common Ancestor (LCA):**

**Problem:**
   - Given a binary tree and two nodes, find the lowest common ancestor (LCA) of the two nodes.

**Approach:**
   - Utilize recursion to traverse the tree.
   - For each subtree, check if both nodes are present.
   - If they are present in different subtrees, the current node is the LCA.
   - If they are present in the same subtree, continue the search in that subtree.

**Java Code:**
  ```java
  class Solution {
      public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
          if (root == null || root == p || root == q) {
              return root;
          }

          TreeNode left = lowestCommonAncestor(root.left, p, q);
          TreeNode right = lowestCommonAncestor(root.right, p, q);

          if (left != null && right != null) {
              return root;
          }

          return (left != null) ? left : right;
      }
  }
  ```

### 3. **Binary Tree Level Order Traversal:**

**Problem:**
   - Traverse a binary tree level by level and return the nodes at each level as separate lists.

**Approach:**
   - Use a breadth-first search (BFS) approach.
   - Maintain a queue to store nodes at each level.
   - For each level, add the nodes to the result list, and enqueue their children for the next level.

**Java Code:**
  ```java
  class Solution {
      public List<List<Integer>> levelOrder(TreeNode root) {
          List<List<Integer>> result = new ArrayList<>();
          if (root == null) return result;

          Queue<TreeNode> queue = new LinkedList<>();
          queue.offer(root);

          while (!queue.isEmpty()) {
              int levelSize = queue.size();
              List<Integer> levelNodes = new ArrayList<>();

              for (int i = 0; i < levelSize; i++) {
                  TreeNode current = queue.poll();
                  levelNodes.add(current.val);

                  if (current.left != null) queue.offer(current.left);
                  if (current.right != null) queue.offer(current.right);
              }

              result.add(levelNodes);
          }

          return result;
      }
  }
  ```

### 4. **Serialize and Deserialize a Binary Tree:**

**Problem:**
   - Serialize a binary tree to a string representation and reconstruct the tree from the string.

**Approach:**
   - For serialization, use a pre-order traversal to append values to a string, including "null" for null nodes.
   - For deserialization, use a queue to process the serialized string.
   - Recursively build the tree by dequeuing values from the string, creating nodes, and connecting them.

**Java Code:**
  ```java
  class Codec {
      public String serialize(TreeNode root) {
          if (root == null) {
              return "null";
          }

          return root.val + "," + serialize(root.left) + "," + serialize(root.right);
      }

      public TreeNode deserialize(String data) {
          Queue<String> nodes = new LinkedList<>(Arrays.asList(data.split(",")));
          return buildTree(nodes);
      }

      private TreeNode buildTree(Queue<String> nodes) {
          String val = nodes.poll();
          if (val.equals("null")) {
              return null;
          }

          TreeNode node = new TreeNode(Integer.parseInt(val));
          node.left = buildTree(nodes);
          node.right = buildTree(nodes);

          return node;
      }
  }
  ```

These examples cover a range of common binary tree problems encountered in competitive programming, providing you with a solid foundation for solving similar problems. Understanding the underlying algorithms and patterns will enhance your problem-solving skills in this domain.

Sure! Let's break down Binary Search Trees (BSTs), common problems encountered in competitive programming related to BSTs, and provide Java code snippets for each.

### Binary Search Tree (BST):

- **Definition:**
  - A binary tree where the left subtree of a node contains only nodes with keys lesser than the node's key, and the right subtree contains only nodes with keys greater than the node's key.
  - Provides fast search, insert, and delete operations compared to other data structures like arrays and linked lists.

- **Properties:**
  - In-order traversal of a BST yields elements in sorted order.
  - No duplicate elements are allowed.

### Common Problems on BST in Competitive Programming:

1. **Search in a BST:**
   - **Problem:** Given a BST and a target value, determine if the target is present in the BST.
   - **Approach:** Perform a standard BST search. Traverse left if the target is smaller than the current node's value, and traverse right if the target is larger.
   - **Java Code:**
     ```java
     public boolean searchBST(TreeNode root, int val) {
         if (root == null) return false;
         if (root.val == val) return true;
         if (val < root.val) return searchBST(root.left, val);
         else return searchBST(root.right, val);
     }
     ```

2. **Insert into a BST:**
   - **Problem:** Insert a new node with a given value into the BST while maintaining its properties.
   - **Approach:** Perform a standard BST insert operation. Traverse left if the value to insert is smaller than the current node's value, and traverse right if it's larger.
   - **Java Code:**
     ```java
     public TreeNode insertIntoBST(TreeNode root, int val) {
         if (root == null) return new TreeNode(val);
         if (val < root.val) root.left = insertIntoBST(root.left, val);
         else root.right = insertIntoBST(root.right, val);
         return root;
     }
     ```

3. **Delete Node in a BST:**
   - **Problem:** Delete a node with a given value from the BST while maintaining its properties.
   - **Approach:** Perform a standard BST delete operation. Three cases: node has no children, node has one child, node has two children.
   - **Java Code:**
     ```java
     public TreeNode deleteNode(TreeNode root, int key) {
         if (root == null) return null;
         if (key < root.val) root.left = deleteNode(root.left, key);
         else if (key > root.val) root.right = deleteNode(root.right, key);
         else {
             if (root.left == null) return root.right;
             else if (root.right == null) return root.left;
             TreeNode minNode = findMin(root.right);
             root.val = minNode.val;
             root.right = deleteNode(root.right, root.val);
         }
         return root;
     }

     private TreeNode findMin(TreeNode node) {
         while (node.left != null) node = node.left;
         return node;
     }
     ```

4. **Validate BST:**
   - **Problem:** Determine if a given binary tree is a valid BST.
   - **Approach:** Perform an in-order traversal and ensure that elements are in ascending order.
   - **Java Code:**
     ```java
     TreeNode prev;

     public boolean isValidBST(TreeNode root) {
         prev = null;
         return isValidInOrder(root);
     }

     private boolean isValidInOrder(TreeNode root) {
         if (root == null) return true;
         if (!isValidInOrder(root.left)) return false;
         if (prev != null && root.val <= prev.val) return false;
         prev = root;
         return isValidInOrder(root.right);
     }
     ```

These problems cover a range of scenarios involving BSTs in competitive programming. Understanding how to manipulate BSTs efficiently is crucial for solving many algorithmic problems. The provided Java code snippets demonstrate common approaches for each problem.



# Graph Basics:

### Definition:
- A graph is a collection of nodes (vertices) and edges that connect pairs of nodes.

### Types of Graphs:
1. **Undirected Graph:**
   - Edges have no direction.

2. **Directed Graph (Digraph):**
   - Edges have direction.

3. **Weighted Graph:**
   - Edges have weights/costs.

4. **Cyclic Graph:**
   - Contains a cycle (a path of edges that starts and ends at the same node).

5. **Acyclic Graph:**
   - Does not contain any cycles.

### Graph Representation:
1. **Adjacency Matrix:**
   - 2D array `graph[i][j]` represents the presence/absence of an edge between nodes `i` and `j`.

   ```java
   int[][] graph = new int[V][V]; // V is the number of vertices
   ```

2. **Adjacency List:**
   - Each node maintains a list of its neighbors.

   ```java
   List<Integer>[] adjList = new ArrayList[V];
   ```

## Common Problems and Approaches:

### 1. Depth-First Search (DFS):

#### Problem: Find connected components in an undirected graph.

#### Approach:

```java
import java.util.ArrayList;
import java.util.List;

class Graph {
    int V;
    List<Integer>[] adjList;

    public Graph(int V) {
        this.V = V;
        adjList = new ArrayList[V];
        for (int i = 0; i < V; ++i)
            adjList[i] = new ArrayList<>();
    }

    void addEdge(int v, int w) {
        adjList[v].add(w);
        adjList[w].add(v);
    }

    void DFSUtil(int v, boolean[] visited) {
        visited[v] = true;
        System.out.print(v + " ");

        for (int neighbor : adjList[v]) {
            if (!visited[neighbor])
                DFSUtil(neighbor, visited);
        }
    }

    void connectedComponents() {
        boolean[] visited = new boolean[V];

        for (int v = 0; v < V; ++v) {
            if (!visited[v]) {
                DFSUtil(v, visited);
                System.out.println();
            }
        }
    }

    public static void main(String[] args) {
        Graph g = new Graph(5);
        g.addEdge(0, 1);
        g.addEdge(2, 3);
        g.addEdge(3, 4);

        System.out.println("Connected Components:");
        g.connectedComponents();
    }
}
```

### 2. Breadth-First Search (BFS):

#### Problem: Shortest path in an unweighted graph.

#### Approach:

```java
import java.util.LinkedList;
import java.util.Queue;

class Graph {
    int V;
    List<Integer>[] adjList;

    public Graph(int V) {
        this.V = V;
        adjList = new ArrayList[V];
        for (int i = 0; i < V; ++i)
            adjList[i] = new ArrayList<>();
    }

    void addEdge(int v, int w) {
        adjList[v].add(w);
        adjList[w].add(v);
    }

    int shortestPath(int src, int dest) {
        Queue<Integer> queue = new LinkedList<>();
        boolean[] visited = new boolean[V];
        int[] distance = new int[V];

        queue.add(src);
        visited[src] = true;

        while (!queue.isEmpty()) {
            int current = queue.poll();

            for (int neighbor : adjList[current]) {
                if (!visited[neighbor]) {
                    queue.add(neighbor);
                    visited[neighbor] = true;
                    distance[neighbor] = distance[current] + 1;

                    if (neighbor == dest)
                        return distance[neighbor];
                }
            }
        }

        return -1; // Destination not reachable
    }

    public static void main(String[] args) {
        Graph g = new Graph(7);
        g.addEdge(0, 1);
        g.addEdge(0, 2);
        g.addEdge(1, 3);
        g.addEdge(2, 4);
        g.addEdge(3, 5);
        g.addEdge(4, 5);
        g.addEdge(5, 6);

        System.out.println("Shortest Path: " + g.shortestPath(0, 6));
    }
}
```

These are just a couple of examples to get you started. Graph theory in competitive programming encompasses a wide range of problems, from finding paths and cycles to solving network flow problems. Practice and explore various problems to strengthen your understanding.

## Common Graph Problems

### 1. Shortest Path

- **Problem**: Find the shortest path between two nodes in a graph.

- **Approach**: Use Dijkstra's algorithm or Bellman-Ford algorithm.

Sure, let's dive into the concept of finding the shortest path in a graph and explore one of the most well-known algorithms for this purpose: Dijkstra's algorithm.

## Dijkstra's Algorithm:

Dijkstra's algorithm is used to find the shortest path between two nodes in a weighted graph. It works for both directed and undirected graphs but assumes that all edge weights are non-negative.

### Algorithm Steps:

1. **Initialize:**
   - Create a set of unvisited nodes.
   - Assign tentative distances to all nodes (initialize all distances to infinity except the start node, which is set to 0).

2. **Select the Node:**
   - Choose the unvisited node with the smallest tentative distance as the current node.

3. **Update Distances:**
   - For each neighboring node not yet visited:
     - Calculate the tentative distance from the start node.
     - Update the tentative distance if it's smaller than the current assigned value.

4. **Mark as Visited:**
   - Mark the current node as visited.

5. **Repeat:**
   - Repeat steps 2-4 until the destination node is visited or the smallest tentative distance among the unvisited nodes is infinity.

### Java Code:

```java
import java.util.*;

class Graph {
    private int V;
    private List<List<Node>> adjList;

    public Graph(int V) {
        this.V = V;
        adjList = new ArrayList<>();

        for (int i = 0; i < V; ++i) {
            adjList.add(new ArrayList<>());
        }
    }

    public void addEdge(int source, int destination, int weight) {
        adjList.get(source).add(new Node(destination, weight));
        adjList.get(destination).add(new Node(source, weight)); // For undirected graph
    }

    public int[] dijkstra(int start) {
        int[] distances = new int[V];
        Arrays.fill(distances, Integer.MAX_VALUE);
        distances[start] = 0;

        PriorityQueue<Node> minHeap = new PriorityQueue<>(Comparator.comparingInt(a -> a.distance));
        minHeap.add(new Node(start, 0));

        while (!minHeap.isEmpty()) {
            Node current = minHeap.poll();

            for (Node neighbor : adjList.get(current.vertex)) {
                int newDistance = distances[current.vertex] + neighbor.distance;
                if (newDistance < distances[neighbor.vertex]) {
                    distances[neighbor.vertex] = newDistance;
                    minHeap.add(new Node(neighbor.vertex, newDistance));
                }
            }
        }

        return distances;
    }

    public static void main(String[] args) {
        int V = 6;
        Graph graph = new Graph(V);

        graph.addEdge(0, 1, 5);
        graph.addEdge(0, 2, 2);
        graph.addEdge(1, 3, 4);
        graph.addEdge(2, 4, 7);
        graph.addEdge(3, 5, 3);
        graph.addEdge(4, 5, 1);

        int startNode = 0;
        int[] distances = graph.dijkstra(startNode);

        System.out.println("Shortest distances from node " + startNode + ":");
        for (int i = 0; i < V; ++i) {
            System.out.println("To node " + i + ": " + distances[i]);
        }
    }
}

class Node {
    int vertex;
    int distance;

    public Node(int vertex, int distance) {
        this.vertex = vertex;
        this.distance = distance;
    }
}
```

In this example, the `Graph` class represents a weighted graph using an adjacency list. The `dijkstra` method finds the shortest distances from a specified start node using Dijkstra's algorithm. The `Node` class is a simple data structure to represent a vertex and its distance.

This code calculates the shortest distances from a specified start node to all other nodes in the graph. The result is then printed, showing the shortest distances.

Certainly! The Bellman-Ford algorithm is another algorithm for finding the shortest paths in a graph, even when it contains negative weight edges. Unlike Dijkstra's algorithm, Bellman-Ford can handle graphs with negative weight edges, but it cannot handle graphs with negative weight cycles.

## Bellman-Ford Algorithm:

### Algorithm Steps:

1. **Initialize:**
   - Assign tentative distances to all nodes (initialize all distances to infinity except the start node, which is set to 0).

2. **Relax Edges:**
   - Iterate through all edges and relax them. Relaxing an edge means updating the tentative distance to a node if a shorter path is found.

3. **Repeat Relaxation:**
   - Repeat step 2 for V-1 iterations, where V is the number of vertices in the graph. This ensures that the algorithm considers all possible paths.

4. **Check for Negative Cycles:**
   - After V-1 iterations, if there are still updates to the distances, it indicates the presence of a negative weight cycle.

### Java Code:

```java
import java.util.*;

class Graph {
    private int V;
    private int E;
    private List<Edge> edges;

    public Graph(int V, int E) {
        this.V = V;
        this.E = E;
        edges = new ArrayList<>();
    }

    public void addEdge(int source, int destination, int weight) {
        edges.add(new Edge(source, destination, weight));
    }

    public int[] bellmanFord(int start) {
        int[] distances = new int[V];
        Arrays.fill(distances, Integer.MAX_VALUE);
        distances[start] = 0;

        // Relax edges V-1 times
        for (int i = 1; i < V; ++i) {
            for (Edge edge : edges) {
                int u = edge.source;
                int v = edge.destination;
                int weight = edge.weight;

                if (distances[u] != Integer.MAX_VALUE && distances[u] + weight < distances[v]) {
                    distances[v] = distances[u] + weight;
                }
            }
        }

        // Check for negative cycles
        for (Edge edge : edges) {
            int u = edge.source;
            int v = edge.destination;
            int weight = edge.weight;

            if (distances[u] != Integer.MAX_VALUE && distances[u] + weight < distances[v]) {
                System.out.println("Graph contains negative weight cycle!");
                return null;
            }
        }

        return distances;
    }

    public static void main(String[] args) {
        int V = 5;
        int E = 8;
        Graph graph = new Graph(V, E);

        graph.addEdge(0, 1, -1);
        graph.addEdge(0, 2, 4);
        graph.addEdge(1, 2, 3);
        graph.addEdge(1, 3, 2);
        graph.addEdge(1, 4, 2);
        graph.addEdge(3, 2, 5);
        graph.addEdge(3, 1, 1);
        graph.addEdge(4, 3, -3);

        int startNode = 0;
        int[] distances = graph.bellmanFord(startNode);

        if (distances != null) {
            System.out.println("Shortest distances from node " + startNode + ":");
            for (int i = 0; i < V; ++i) {
                System.out.println("To node " + i + ": " + distances[i]);
            }
        }
    }
}

class Edge {
    int source;
    int destination;
    int weight;

    public Edge(int source, int destination, int weight) {
        this.source = source;
        this.destination = destination;
        this.weight = weight;
    }
}
```

In this example, the `Graph` class represents a weighted directed graph using an edge list. The `bellmanFord` method finds the shortest distances from a specified start node using the Bellman-Ford algorithm. The `Edge` class is a simple data structure to represent an edge with source, destination, and weight.

This code calculates the shortest distances from a specified start node to all other nodes in the graph, even when the graph contains negative weight edges. If there is a negative weight cycle, the algorithm detects it and prints a message.

### 2. Cycle Detection

- **Problem**: Detect whether a graph contains a cycle.

- **Approach**: Use Depth-First Search (DFS) with backtracking or Union-Find (Disjoint Set Union) data structure.

### 3. Minimum Spanning Tree (MST)

- **Problem**: Find the minimum spanning tree of a connected, undirected graph.

- **Approach**: Use Kruskal's algorithm or Prim's algorithm.

### 4. Topological Sorting

- **Problem**: Order the nodes in a directed acyclic graph (DAG) such that for every directed edge (u, v), node u comes before node v.

- **Approach**: Use Depth-First Search (DFS) or Kahn's algorithm.

These are just a few examples of graph problems in competitive programming. Each problem may have multiple variations and nuances, so it's essential to practice and understand the underlying concepts.

Feel free to ask for more specific problems or details on any of the mentioned topics!
