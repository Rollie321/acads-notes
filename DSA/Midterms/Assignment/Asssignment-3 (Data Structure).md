###### Define and discuss the following:
1. Abstract data types(list, stack, and queue)
2. Trees/Tree data structure(binary tree, search tree, expression tree, and avl tree)
3. Graph data structure (shortest path algorithm, acyclic graph, and event node graph)

# 1. Abstract Data Types

### List

A **list** is an ordered collection of elements where each element has a specific position. Lists can usually be modified by adding, removing, or changing elements.

* **Usage:** Storing collections of data such as student names, products, or tasks.
* **Advantages:** Easy to organize, access, add, and remove data.
* **Disadvantages:** Searching for a specific element can be slow, especially in a large list.

### Stack

A **stack** is a data structure that follows **LIFO (Last In, First Out)**. The most recently added element is removed first.

* **Usage:** Used in function calls, undo/redo operations, and expression evaluation.
* **Advantages:** Simple and efficient insertion and removal from the top.
* **Disadvantages:** Only the top element can normally be accessed directly.

### Queue

A **queue** follows **FIFO (First In, First Out)**. The first element added is the first element removed.

* **Usage:** Used in printer queues, CPU task scheduling, and waiting-line systems.
* **Advantages:** Provides fair and organized processing of tasks.
* **Disadvantages:** Accessing elements in the middle is inefficient.

---

# 2. Trees / Tree Data Structure

### Binary Tree

A **binary tree** is a tree where each node can have a maximum of **two children**, called the left and right child.

* **Usage:** Representing hierarchical data and forming other tree structures.
* **Advantages:** Simple structure and useful for organizing hierarchical information.
* **Disadvantages:** An unbalanced binary tree can become inefficient for searching.

### Binary Search Tree (BST)

A **binary search tree** is a binary tree where values smaller than a node are placed on the left, while larger values are placed on the right.

* **Usage:** Fast searching, insertion, and deletion of ordered data.
* **Advantages:** Can perform operations efficiently when the tree is balanced.
* **Disadvantages:** Can become unbalanced and behave like a linked list, making operations slower.

### Expression Tree

An **expression tree** represents a mathematical or logical expression. Operators are usually internal nodes, while operands are leaf nodes.

Example:

```text
      +
     / \
    5   *
       / \
      2   3
```

This represents `5 + (2 * 3)`.

* **Usage:** Compilers, calculators, and expression evaluation.
* **Advantages:** Makes complex expressions easier for computers to process.
* **Disadvantages:** Requires additional memory and can be more complex to construct.

### AVL Tree (Adelson-Velsky and Landis)

An **AVL tree** is a **self-balancing binary search tree**. It automatically keeps the heights of its subtrees balanced after insertion or deletion.

* **Usage:** Applications that require consistently fast searching and updating.
* **Advantages:** Maintains efficient search, insertion, and deletion even with many operations.
* **Disadvantages:** More complex because the tree may need rotations to maintain balance.

---

# 3. Graph Data Structure

### Shortest Path Algorithm

A **shortest path algorithm** finds the path with the lowest distance or cost between vertices in a graph. Examples include **Dijkstra's algorithm** and **Floyd-Warshall algorithm**.

* **Usage:** GPS navigation, network routing, and finding the cheapest route.
* **Advantages:** Helps find efficient routes and minimize distance or cost.
* **Disadvantages:** Different algorithms have different limitations; for example, Dijkstra's algorithm does not work correctly with negative edge weights.

### Acyclic Graph

An **acyclic graph** is a graph that contains **no cycles**. In other words, there is no path that starts and returns to the same node.

* **Usage:** Task dependencies, scheduling, file structures, and project planning.
* **Advantages:** Makes dependencies easier to manage because there are no circular relationships.
* **Disadvantages:** Cannot represent situations where circular relationships are required.

### Event Node Graph

An **event node graph** represents events or activities as nodes, with connections showing the relationships or order between them.

* **Usage:** Project scheduling, event planning, workflow management, and activity networks.
* **Advantages:** Clearly shows the sequence and dependencies between events.
* **Disadvantages:** Large projects can produce complicated graphs that are difficult to read and manage.
