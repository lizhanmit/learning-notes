# Data Structures and Algorithms Note 

## Data Structures 

### Linked List

A list of data items connected with links. 

Types of linked list: 

- Simple Linked List 
- Doubly Linked List 
- Circular Linked List

When to use a linked list:

- You do not need random access to any specific elements.
- You need to do constant insertions and deletions.
- You are not sure how many items will be in the list.

#### Simple Linked List

![simple-linked-list.jpg](img/simple-linked-list.jpg)

- Advantages: insertion and deletion can be very quick.
  - insertion (prepend) - **O(1)**
  - insertion (append) - **O(n)**
- Disadvantages 
  - Slow to access an element. **O(n)**
  - Memory is a concern. Require data and pointers. 

#### Doubly Linked List 

![doubly-linked-list.jpg](img/doubly-linked-list.jpg)

#### Circular Linked List

##### Singly Circular Linked List

![singly-circular-linked-list.jpg](img/singly-circular-linked-list.jpg)

##### Doubly Circular Linked List

![doubly-circular-linked-list.jpg](img/doubly-circular-linked-list.jpg)

---

### Stack 

An Abstract Data Type (ADT) used to store and retrieve values in Last In First Out method.

flexible size

time factor - O(n)

![stack.jpg](img/stack.jpg)

When to use stack: 

- recursive function calls
- expression parsing
- depth first traversal of graphs

Operations:

- push(): 
  - Firstly check if the stack is full. 
- pop(): 
  - Firstly check if the stack is empty. 
  - Return the top value. 
- peek(): get the top data element of the stack, without removing it.
  - `return stack[top]`
- isFull(): check if stack is full.
  - ```
    // array implementation 
    if (top == MAXSIZE - 1)
        return true;
    else
        return false;
    ```
- isEmpty(): check if stack is empty.
  - ```
    // array implementation 
    if (top == -1)
        return true;
    else
        return false;
    ```
    
---

### Queue

An Abstract Data Type (ADT) used to store and retrieve values in First In First Out method.

flexible size

![queue.jpg](img/queue.jpg)

When to use queue: 

- priority queues
- breadth first traversal of graphs 

Operations:

- enqueue():
  - Firstly check if the queue is full. 

- dequeue():
  - Firstly check if the queue is empty. 
  - Return the front value. 

- peek(): get the element at the front of the queue without removing it. 
  - `return queue[front]`

- isFull(): check if queue is full.
  - ```
    // array implementation 
    if (rear == MAXSIZE - 1)
        return true;
    else
        return false;
    ```

- isEmpty(): check if queue is empty.
  - ```
    // array implementation
    if (front < 0 || front > rear) 
      return true;
    else
      return false;
    ```

---

### Hash Table

Hashing is a technique to convert a range of key values into a range of indexes of an array.

- Use an array as a storage medium.
- Use hash technique to generate an index where an element is to be inserted or is to be located from.
- Insertion and search operations are very fast.

![hash-function.jpg](img/hash-function.jpg)

1. There are key-value pair data. 
2. Use hash function to get the hash code from the key of the data. Then convert the hash code to the index of the array where to store data. 
3. The hash code may be repeated, which is called collision. Ways to solve collision: 
   - Linear probing: search the next empty location in the array to store the data. 
   - Chaining: for data with the same hash code, store them in linked list. So, the whole data structure will be array of linked list. 

![hash-table.png](img/hash-table.png)

![hash-table-complexity.png](img/hash-table-complexity.png)

---

### Graph 

A pictorial representation of a set of objects where some pairs of objects are connected by links. Objects are represented by points termed as vertices, and the links that connect the vertices are called edges.

A graph is a pair of sets (V, E). 

Vertices - represent them using an array. 

Edges - represent them using a two-dimensional array. 

#### Depth First Search (DFS)  

Traverse a graph in a depthward motion. Use stack to remember to get the next vertex to start a search, when a dead end occurs in any iteration.

1. Visit the adjacent unvisited vertex. Mark it as visited. Display it. Push it in a stack.
2. If no adjacent vertex is found, pop up a vertex from the stack. (It will pop up all the vertices from the stack, which do not have adjacent vertices.)

3. Repeat Rule 1 and Rule 2 until the stack is empty.

![graph-depth-first-search.png](img/graph-depth-first-search.png)

#### Breadth First Search (BFS) - queue 

Traverse a graph in a breadthward motion. Use a queue to remember to get the next vertex to start a search, when a dead end occurs in any iteration.

1. Visit the adjacent unvisited vertex. Mark it as visited. Display it. Insert it in a queue.
2. If no adjacent vertex is found, remove the first vertex from the queue.

3. Repeat Rule 1 and Rule 2 until the queue is empty.

![graph-breadth-first-search.png](img/graph-breadth-first-search.png)

### Tree 

A tree is a minimally connected graph having no loops and circuits.

![binary-tree.jpg](img/binary-tree.jpg)

**In-order Traversal**: the left subtree is visited first, then the root and later the right sub-tree. 

![tree-in-order-traversal.jpg](img/tree-in-order-traversal.jpg)

**Pre-order Traversal**: the root node is visited first, then the left subtree and finally the right subtree. 

![tree-pre-order-traversal.jpg](img/tree-pre-order-traversal.jpg)

**Post-order Traversal**: first we traverse the left subtree, then the right subtree and finally the root node.

![tree-post-order-traversal.jpg](img/tree-post-order-traversal.jpg)

如何记忆：pre, in, post 是指root 的被访问顺序。

- pre: 先root -> left -> right
- in: left -> 中间root -> right
- post: left -> right -> 最后root 

#### Binary Tree 

A binary tree has a special condition that each node can have two children at maximum.

#### Binary Search Tree (BST)

- A node's left child must have a value less than or equal to its parent's value.
- A node's right child must have a value greater than its parent's value.
- Each node has a key and an associated value. 
- Binary search tree is a special binary tree.

![img/binary-search-tree.jpg](img/binary-search-tree.jpg)

#### AVL Tree

- Height balancing binary search tree.

- Check the height of the left and the right sub-trees and assures that the difference (balance factor) is not more than 1.
- If the difference in the height of left and right sub-trees is more than 1, the tree is unbalanced. 

Rotation techniques to balance the tree: 

- Left rotation
- Right rotation
- Left-Right rotation
- Right-Left rotation

#### Spanning Tree

A spanning tree is a subset of Graph G, which has all the vertices covered with minimum possible number of edges.

A complete undirected graph can have maximum 
$$
n^{n-2}
$$
number of spanning trees, where n is the number of nodes.  

- Removing one edge from the spanning tree will make the graph disconnected, i.e. the spanning tree is **minimally connected**.
- Adding one edge to the spanning tree will create a circuit or loop, i.e. the spanning tree is **maximally acyclic**.
- Used to find a minimum path to connect all nodes in a graph. 

Common application of spanning trees: 

- Civil Network Planning
- Computer Network Routing Protocol
- Cluster Analysis

##### Minimum Spanning Tree (MST)

In a weighted graph, a minimum spanning tree is a spanning tree that has minimum weight than all other spanning trees of the same graph. In real-world situations, this weight can be measured as distance, congestion, traffic load or any arbitrary value denoted to the edges. 

Minimum Spanning Tree algorithms: 

- Kruskal's Algorithm
- Prim's Algorithm

#### Heap

Heap is a special case of balanced binary tree data structure where root-node key is compared with its children and arranged accordingly.

##### Max Heap

The value of the parent node is greater than or equal to either of its children.

**Max Heap Construction** 

![max-heap-constructure.gif](img/max-heap-constructure.gif)

**Max Heap Deletion**

![max-heap-deletion.gif](img/max-heap-deletion.gif)

##### Min Heap

The value of the parent node is less than or equal to either of its children.

---

## Algorithms 

### Basic Concepts 

#### Algorithm Complexity 

Two main factors decide the efficiency of algorithm: 
- Time Factor: counting the number of key operations. 
- Space Factor: counting the maximum memory space required by the algorithm. 

#### Asymptotic Analysis 

Refers to computing the running time of any operation in mathematical units of computation. 

The time required by an algorithm falls under three types:

- Best Case - minimum time required for program execution.
- Average Case - average time required for program execution.
- Worst Case - maximum time required for program execution.

Asymptotic Notations:

- Omega Notation **Ω(n)** - measures the **best case** time complexity or the best amount of time. 
- Big O Notation **Ο(n)** - measures the **worst case** time complexity or the longest amount of time. 
- Theta Notation **θ(n)** - **average case**, express both the lower bound and the upper bound of an algorithm's running time. 

#### Big O Complexity Chart

![big-o-complexity-chart.png](img/big-o-complexity-chart.png)

#### Big O Cheat Sheet

![big-o-cheat-sheet.jpg](img/big-o-cheat-sheet.jpg)

#### Approaches to Develop Algorithms

##### Greedy Algorithms

localized optimum solution

Most networking algorithms use the greedy approach:

- Travelling Salesman Problem
- Prim's Minimal Spanning Tree Algorithm
- Kruskal's Minimal Spanning Tree Algorithm
- Dijkstra's Minimal Spanning Tree Algorithm
- Graph - Map Coloring
- Graph - Vertex Cover
- Knapsack Problem
- Job Scheduling Problem

##### Divide and Conquer

The algorithms based on divide-and-conquer programming approach:

- Merge Sort
- Quick Sort
- Binary Search
- Strassen's Matrix Multiplication
- Closest pair (points)

##### Dynamic Programming

overall optimization

For problems which can be divided into **similar** sub-problems, so that their results can be re-used.

Use the output of a smaller sub-problem and then try to optimize a bigger sub-problem. 

Use Memorization to remember the output of already solved sub-problems.

Problems solved using dynamic programming approach:

- Fibonacci number series
- Knapsack problem
- Tower of Hanoi
- All pair shortest path by Floyd-Warshall
- Shortest path by Dijkstra
- Project scheduling

---

### Expression Parsing 

- Infix Notation - human readable
- Prefix (Polish) Notation - operator is ahead
- Postfix (Reverse-Polish) Notation - operator is after

![expression-parsing.png](img/expression-parsing.png)

---

### Searching Algorithms 

#### Linear Search  

Find an item in a sequentially arranged data type. Compare expected data item with each of data items in list or array. 

average - Ο(n) 

worst - O(n^2)

Work better finding all matched values in an array.

#### Binary Search 

Select the middle which splits the entire list into two parts. Compare the target value to the mid of the list.

Ο(log n)

Work better finding one match if there is no duplicates in the array. 

**The array must be sorted in advance.** 

---

### Sorting Algorithms

- In-place Sorting vs. Not-in-place Sorting
- Stable vs. Not Stable Sorting
- Adaptive vs. Non-Adaptive Sorting 
  - Adaptive: if the source list has some element already sorted, it will try not to re-order them.
  - Non-Adaptive Sorting: force every single element to be re-ordered, not taking into account the elements which are already sorted.

#### Bubble Sort 

A comparison-based algorithm in which each pair of adjacent elements is compared and elements are swapped if they are not in order. 

O(n^2) - not suitable for large set of data.

#### Insertion Sort 

Divide the list into two sub-list: sorted and unsorted. Then it takes one element at a time and finds an appropriate location in sorted sub-list and insert it.

worst - O(n^2)

stable

1. a[2]跟a[1]比，if a[2] < a[1]，a[1]的值赋给a[2]。
2. a[2]跟a[0]比，if a[2] < a[0]，a[0]的值赋给a[1]。a[2]的值赋给a[0]。

把小的值向后覆盖掉大的值。最终把最小值赋给第一个值。

#### Selection Sort

Divide the list into two sub-lists: sorted and unsorted. Then it selects the minimum element from unsorted sub-list and places it into the sorted list. This iterates unless all the elements from unsorted sub-list are placed into sorted sub-list.

worst - O(n^2)

not stable

1. Mark a[0] as min。
2. a[0]跟a[1]比，if a[0] > a[1]，这时不要交换a[0]和a[1]。而是mark a[1] as min。
3. a[1]跟a[2]比，if a[1] > a[2]，mark a[2] as min。
4. 一趟loop完后，拿到min。这时将min和a[0]交换。再从a[1]开始重复这个过程。

找min，一趟loop完后再做交换。

#### Merge Sort 

Divide and conquer approach. Keep on dividing the list into smaller sub-list until all sub-list has only one element. Then it merges them in a sorted way until all sub-lists are sorted. 

O(n log n)

space - O(n)

#### Shell Sort 

A variant of insertion sort. Divide the list into smaller sub-list based on some gap variables. Sort each sub-list using insertion sort. 

best - O(n log n)

1. Initialize the value of interval h. (h = h * 3 + 1) (O(n^(3/2)) by Knuth, 1973: 1, 4, 13, 40, 121, ...)
2. Divide the list into smaller sub-list of equal interval h.
3. Sort these sub-lists using insertion sort. 
4. Reduce h, divide sub-lists into smaller one. Sort. 
5. Repeat step 4 until h is 1. Final insertion sort. 

![img/shell-sort.jpg](img/shell-sort.jpg)

![img/shell-sort-2.jpg](img/shell-sort-2.jpg)

#### Quick Sort

Divide and conquer approach. Divide the list in smaller "partitions" using "pivot". The values which are smaller than the pivot are arranged in the left partition and greater values are arranged in the right partition. Each partition is recursively sorted using quick sort.

1. Randomly choose a value in the array as pivot. 
2. Compare the a[0] and pivot, a[n] and pivot. If a[0] > pivot, a[n] < pivot, swap a[0] and a[n].
3. Compare a[1] and pivot, a[n-1] and pivot. If a[1] < pivot, a[n-1] < pivot, compare a[2] and pivot. If a[2] > pivot, swap a[2] and a[n-1].
4. When all elements <= pivot are at the left and all elements >= pivot are at the right (这里只是将array以pivot为基准分成大的小的两组，不一定是pivot左边的都小于pivot，右边的都大于pivot), split them as two sub-lists. Then do the above steps recursively until the sub-list cannot be split again.  

[Good explanation](https://www.youtube.com/watch?v=SLauY6PpjW4&list=PLX6IKgS15Ue02WDPRCmYKuZicQHit9kFt&t=0s&index=20)

---

#### Sorting Algorithms Complexity Table

![array-sorting-algorithms-complexity.png](img/array-sorting-algorithms-complexity.png)

![array-sorting-algorithms-complexity-2.png](img/array-sorting-algorithms-complexity-2.png)

---

### Recursion 

A recursive function is one which calls itself, directly or calls a function that in turn calls it.

Properties: 

- Base criteria: where function stops calling itself recursively.
- Progressive approach: where function tries to meet the base criteria in each iteration. 

Many programming languages implement recursion by means of stacks. 

![recursion.jpg](img/recursion.jpg)

**Advantages**:

- It makes a program more readable.
- More efficient than iterations because of latest enhanced CPU systems.

**Disadvantages**:

- Space complexity of recursive function may go higher than that of a function with iteration, because the system needs to store activation record each time a recursive call is made. 

#### Tower of Hanoi

![tower-of-hanoi.jpg](img/tower-of-hanoi.jpg)

Tower of Hanoi puzzle with n disks can be solved in minimum 
$$
2^n-1
$$
steps. 

Algorithm ideas: 

- Divide the stack of disks in two parts:
  - n^th disk 
  - all other n-1 disks 
- The ultimate aim is to move disk n^th from source to destination and then put all other n-1 disks onto it.

Steps:

1. Move n-1 disks from source to aux.
2. Move n^th disk from source to destination.
3. Move n-1 disks from aux to destination.

#### Fibonacci Series

$$
F_n = F_{n-1} + F_{n-2}
$$

```c
int fibbonacci(int n) {
   if(n == 0) {
      return 0;
   } else if(n == 1) {
      return 1;
   } else {
      return (fibbonacci(n-1) + fibbonacci(n-2));
   }
}
```

#### Factorial (n!)

```c
int factorial(int n) {
   //base case
   if(n == 0) {
      return 1;
   } else {
      return n * factorial(n-1);
   }
}
```

---

## Interview Questions

Q1: What is data structure?

- Data structure is a structural & systematic way to of defining, storing & retrieving of data. A data structure may contain different type of data items.

Q2: What is algorithm?

- Algorithm is a step by step procedure, which defines a set of instructions to be executed in a certain order to get the desired output.

Q3: Insertion sort vs. selection sort

- Both maintain two sub-lists, sorted and unsorted.
- Both take one element at a time and place it into sorted sub-list.
- Insertion sort works on the current element in hand and places it in the sorted list at appropriate location maintaining the properties of insertion sort.
- Selection sort searches the minimum from the unsorted sub-list and replaces it with the current element in hand.