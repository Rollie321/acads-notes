
### C++ :
```cpp

/**
 * @file avl_tree.cpp
 * @brief A self-balancing AVL tree (set of unique keys) with a small demo.
 *
 * An AVL tree is a binary search tree in which, for every node, the heights
 * of the left and right subtrees differ by at most 1. After each insertion or
 * removal the tree is rebalanced using rotations, which guarantees
 * O(log n) search, insert, and remove.
 *
 * Build:  g++ -std=c++17 -Wall -Wextra -o avl_tree avl_tree.cpp
 */

#include <algorithm>
#include <cstddef>
#include <cstdlib>
#include <functional>
#include <iostream>
#include <memory>
#include <string>
#include <vector>

/**
 * @brief A balanced binary search tree storing unique keys.
 *
 * @tparam KeyType Type of the stored keys. Must support operator<.
 */
template <typename KeyType>
class AVLTree {
private:
    /** @brief A single tree node. */
    struct Node {
        KeyType key;                         ///< Value stored in this node.
        int height;                          ///< Height of the subtree rooted here (leaf = 1).
        std::unique_ptr<Node> left_child;    ///< Subtree with smaller keys.
        std::unique_ptr<Node> right_child;   ///< Subtree with larger keys.

        explicit Node(const KeyType& node_key) : key(node_key), height(1) {}
    };

    using NodePtr = std::unique_ptr<Node>;

    NodePtr root_;             ///< Root of the tree (nullptr when empty).
    std::size_t element_count_ = 0;  ///< Number of keys currently stored.

    // ------------------------------------------------------------------
    // Height and balance helpers
    // ------------------------------------------------------------------

    /**
     * @brief Get the height of a subtree.
     * @param node Root of the subtree (may be nullptr).
     * @return 0 for an empty subtree, otherwise the node's stored height.
     */
    static int height_of(const NodePtr& node) {
        return node ? node->height : 0;
    }

    /**
     * @brief Compute the balance factor of a node.
     * @param node Root of the subtree (may be nullptr).
     * @return height(left) - height(right). Valid AVL values are -1, 0, 1.
     */
    static int balance_factor_of(const NodePtr& node) {
        return node ? height_of(node->left_child) - height_of(node->right_child) : 0;
    }

    /**
     * @brief Recompute a node's height from its children.
     * @param node Non-null node whose children already have correct heights.
     */
    static void update_height(NodePtr& node) {
        node->height = 1 + std::max(height_of(node->left_child),
                                    height_of(node->right_child));
    }

    // ------------------------------------------------------------------
    // Rotations
    // ------------------------------------------------------------------

    /**
     * @brief Rotate a subtree to the right (fixes a left-heavy imbalance).
     *
     *         unbalanced_node            new_root
     *            /      \                /      \
     *       new_root     C     ==>      A    unbalanced_node
     *        /    \                          /      \
     *       A      B                        B        C
     *
     * @param unbalanced_node Root of the subtree to rotate. Must have a left child.
     * @return The new root of the rotated subtree.
     */
    static NodePtr rotate_right(NodePtr unbalanced_node) {
        NodePtr new_root = std::move(unbalanced_node->left_child);
        unbalanced_node->left_child = std::move(new_root->right_child);
        update_height(unbalanced_node);
        new_root->right_child = std::move(unbalanced_node);
        update_height(new_root);
        return new_root;
    }

    /**
     * @brief Rotate a subtree to the left (fixes a right-heavy imbalance).
     *
     *       unbalanced_node                  new_root
     *          /      \                      /      \
     *         A      new_root     ==>  unbalanced_node  C
     *                 /    \             /      \
     *                B      C           A        B
     *
     * @param unbalanced_node Root of the subtree to rotate. Must have a right child.
     * @return The new root of the rotated subtree.
     */
    static NodePtr rotate_left(NodePtr unbalanced_node) {
        NodePtr new_root = std::move(unbalanced_node->right_child);
        unbalanced_node->right_child = std::move(new_root->left_child);
        update_height(unbalanced_node);
        new_root->left_child = std::move(unbalanced_node);
        update_height(new_root);
        return new_root;
    }

    /**
     * @brief Restore the AVL property at a node after a child subtree changed.
     *
     * Handles the four classic cases: left-left, left-right, right-right,
     * and right-left.
     *
     * @param node Non-null root of the subtree to rebalance.
     * @return The (possibly new) root of the balanced subtree.
     */
    static NodePtr rebalance(NodePtr node) {
        update_height(node);
        const int balance_factor = balance_factor_of(node);

        if (balance_factor > 1) {  // Left-heavy
            if (balance_factor_of(node->left_child) < 0) {  // Left-Right case
                node->left_child = rotate_left(std::move(node->left_child));
            }
            return rotate_right(std::move(node));  // Left-Left case
        }

        if (balance_factor < -1) {  // Right-heavy
            if (balance_factor_of(node->right_child) > 0) {  // Right-Left case
                node->right_child = rotate_right(std::move(node->right_child));
            }
            return rotate_left(std::move(node));  // Right-Right case
        }

        return node;  // Already balanced
    }

    // ------------------------------------------------------------------
    // Recursive insert / remove / lookup
    // ------------------------------------------------------------------

    /**
     * @brief Recursively insert a key into a subtree.
     * @param node Root of the subtree (may be nullptr).
     * @param key_to_insert Key to add.
     * @param was_inserted Set to true if the key was new, false if it already existed.
     * @return The new root of the subtree after insertion and rebalancing.
     */
    static NodePtr insert_into(NodePtr node, const KeyType& key_to_insert, bool& was_inserted) {
        if (!node) {
            was_inserted = true;
            return std::make_unique<Node>(key_to_insert);
        }

        if (key_to_insert < node->key) {
            node->left_child = insert_into(std::move(node->left_child), key_to_insert, was_inserted);
        } else if (node->key < key_to_insert) {
            node->right_child = insert_into(std::move(node->right_child), key_to_insert, was_inserted);
        } else {
            was_inserted = false;  // Duplicate key: tree is unchanged
            return node;
        }

        return rebalance(std::move(node));
    }

    /**
     * @brief Remove the smallest node of a subtree and report its key.
     * @param node Non-null root of the subtree.
     * @param removed_min_key Receives the key of the removed minimum node.
     * @return The new root of the subtree after removal and rebalancing.
     */
    static NodePtr extract_min(NodePtr node, KeyType& removed_min_key) {
        if (!node->left_child) {
            removed_min_key = node->key;
            return std::move(node->right_child);
        }
        node->left_child = extract_min(std::move(node->left_child), removed_min_key);
        return rebalance(std::move(node));
    }

    /**
     * @brief Recursively remove a key from a subtree.
     * @param node Root of the subtree (may be nullptr).
     * @param key_to_remove Key to delete.
     * @param was_removed Set to true if the key was found and removed.
     * @return The new root of the subtree after removal and rebalancing.
     */
    static NodePtr remove_from(NodePtr node, const KeyType& key_to_remove, bool& was_removed) {
        if (!node) {
            return nullptr;  // Key not found
        }

        if (key_to_remove < node->key) {
            node->left_child = remove_from(std::move(node->left_child), key_to_remove, was_removed);
        } else if (node->key < key_to_remove) {
            node->right_child = remove_from(std::move(node->right_child), key_to_remove, was_removed);
        } else {
            was_removed = true;

            // Zero or one child: replace this node with its only child.
            if (!node->left_child) return std::move(node->right_child);
            if (!node->right_child) return std::move(node->left_child);

            // Two children: replace key with the in-order successor
            // (smallest key in the right subtree), then delete that successor.
            node->right_child = extract_min(std::move(node->right_child), node->key);
        }

        return rebalance(std::move(node));
    }

    /**
     * @brief In-order traversal helper (visits keys in ascending order).
     * @param node Root of the subtree (may be nullptr).
     * @param visit Callback invoked with each key.
     */
    static void in_order_visit(const NodePtr& node, const std::function<void(const KeyType&)>& visit) {
        if (!node) return;
        in_order_visit(node->left_child, visit);
        visit(node->key);
        in_order_visit(node->right_child, visit);
    }

    /**
     * @brief Verify BST ordering, stored heights, and AVL balance of a subtree.
     * @param node Root of the subtree (may be nullptr).
     * @return True if every node in the subtree satisfies the AVL invariants.
     */
    static bool is_valid_subtree(const NodePtr& node) {
        if (!node) return true;

        if (node->left_child && !(node->left_child->key < node->key)) return false;
        if (node->right_child && !(node->key < node->right_child->key)) return false;

        const int expected_height = 1 + std::max(height_of(node->left_child),
                                                 height_of(node->right_child));
        if (node->height != expected_height) return false;
        if (std::abs(balance_factor_of(node)) > 1) return false;

        return is_valid_subtree(node->left_child) && is_valid_subtree(node->right_child);
    }

public:
    // ------------------------------------------------------------------
    // Public interface
    // ------------------------------------------------------------------

    /**
     * @brief Insert a key into the tree.
     * @param key_to_insert Key to add.
     * @return True if the key was inserted, false if it was already present.
     * @complexity O(log n)
     */
    bool insert(const KeyType& key_to_insert) {
        bool was_inserted = false;
        root_ = insert_into(std::move(root_), key_to_insert, was_inserted);
        if (was_inserted) ++element_count_;
        return was_inserted;
    }

    /**
     * @brief Remove a key from the tree.
     * @param key_to_remove Key to delete.
     * @return True if the key was found and removed, false otherwise.
     * @complexity O(log n)
     */
    bool remove(const KeyType& key_to_remove) {
        bool was_removed = false;
        root_ = remove_from(std::move(root_), key_to_remove, was_removed);
        if (was_removed) --element_count_;
        return was_removed;
    }

    /**
     * @brief Check whether a key is present.
     * @param key_to_find Key to search for.
     * @return True if the key exists in the tree.
     * @complexity O(log n)
     */
    bool contains(const KeyType& key_to_find) const {
        const Node* current_node = root_.get();
        while (current_node) {
            if (key_to_find < current_node->key) {
                current_node = current_node->left_child.get();
            } else if (current_node->key < key_to_find) {
                current_node = current_node->right_child.get();
            } else {
                return true;
            }
        }
        return false;
    }

    /**
     * @brief Get all keys in ascending order.
     * @return A sorted vector containing every key in the tree.
     * @complexity O(n)
     */
    std::vector<KeyType> to_sorted_vector() const {
        std::vector<KeyType> sorted_keys;
        sorted_keys.reserve(element_count_);
        in_order_visit(root_, [&sorted_keys](const KeyType& key) {
            sorted_keys.push_back(key);
        });
        return sorted_keys;
    }

    /** @brief Number of keys stored in the tree. */
    std::size_t size() const { return element_count_; }

    /** @brief True if the tree holds no keys. */
    bool empty() const { return element_count_ == 0; }

    /** @brief Height of the tree (0 when empty, 1 for a single node). */
    int height() const { return height_of(root_); }

    /**
     * @brief Check that the tree satisfies all AVL invariants.
     * @return True if ordering, heights, and balance are all correct.
     * @note Intended for testing/debugging; runs in O(n).
     */
    bool is_valid() const { return is_valid_subtree(root_); }
};

// ----------------------------------------------------------------------
// Demo
// ----------------------------------------------------------------------

/**
 * @brief Print all keys of a tree on one line.
 * @param label Text printed before the keys.
 * @param tree The tree to print.
 */
static void print_tree(const std::string& label, const AVLTree<int>& tree) {
    std::cout << label << ": ";
    for (int key : tree.to_sorted_vector()) std::cout << key << ' ';
    std::cout << "(size=" << tree.size()
              << ", height=" << tree.height()
              << ", valid=" << std::boolalpha << tree.is_valid() << ")\n";
}

int main() {
    AVLTree<int> tree;

    // Ascending inserts would degrade a plain BST into a linked list;
    // the AVL tree stays balanced (height ~ log2 n).
    for (int value = 1; value <= 15; ++value) tree.insert(value);
    print_tree("After inserting 1..15", tree);

    std::cout << "Duplicate insert of 7 accepted? " << tree.insert(7) << '\n';
    std::cout << "Contains 10? " << tree.contains(10) << '\n';
    std::cout << "Contains 99? " << tree.contains(99) << '\n';

    tree.remove(8);   // Root of the perfectly balanced tree (two children)
    tree.remove(1);
    tree.remove(15);
    print_tree("After removing 8, 1, 15", tree);

    std::cout << "Remove missing key 42 succeeded? " << tree.remove(42) << '\n';
    return 0;
}
```


### Javascript:
```javascript
/**
 * @file avl_tree.js
 * @brief A self-balancing AVL tree (set of unique keys) with a small demo.
 *
 * An AVL tree is a binary search tree in which, for every node, the heights
 * of the left and right subtrees differ by at most 1. After each insertion or
 * removal the tree is rebalanced using rotations, which guarantees
 * O(log n) search, insert, and remove.
 *
 * Run:  node avl_tree.js
 */

"use strict";

/**
 * A single node of the AVL tree.
 */
class AVLNode {
  /**
   * @param {*} key - Value stored in this node.
   */
  constructor(key) {
    /** @type {*} Value stored in this node. */
    this.key = key;
    /** @type {number} Height of the subtree rooted here (leaf = 1). */
    this.height = 1;
    /** @type {AVLNode|null} Subtree with smaller keys. */
    this.leftChild = null;
    /** @type {AVLNode|null} Subtree with larger keys. */
    this.rightChild = null;
  }
}

/**
 * Default comparison: works for numbers and strings.
 * @param {*} firstKey
 * @param {*} secondKey
 * @returns {number} Negative if first < second, positive if first > second, 0 if equal.
 */
function defaultCompare(firstKey, secondKey) {
  if (firstKey < secondKey) return -1;
  if (firstKey > secondKey) return 1;
  return 0;
}

/**
 * A balanced binary search tree storing unique keys.
 */
class AVLTree {
  /**
   * @param {(firstKey: *, secondKey: *) => number} [compareKeys=defaultCompare]
   *   Comparator returning a negative number, zero, or a positive number.
   *   Provide one to store objects or to change the sort order.
   */
  constructor(compareKeys = defaultCompare) {
    /** @private */ this.compareKeys = compareKeys;
    /** @private @type {AVLNode|null} */ this.root = null;
    /** @private @type {number} */ this.elementCount = 0;
  }

  // ------------------------------------------------------------------
  // Height and balance helpers
  // ------------------------------------------------------------------

  /**
   * Get the height of a subtree.
   * @private
   * @param {AVLNode|null} node - Root of the subtree.
   * @returns {number} 0 for an empty subtree, otherwise the node's stored height.
   */
  heightOf(node) {
    return node ? node.height : 0;
  }

  /**
   * Compute the balance factor of a node.
   * @private
   * @param {AVLNode|null} node - Root of the subtree.
   * @returns {number} height(left) - height(right). Valid AVL values are -1, 0, 1.
   */
  balanceFactorOf(node) {
    return node ? this.heightOf(node.leftChild) - this.heightOf(node.rightChild) : 0;
  }

  /**
   * Recompute a node's height from its children.
   * @private
   * @param {AVLNode} node - Node whose children already have correct heights.
   */
  updateHeight(node) {
    node.height = 1 + Math.max(this.heightOf(node.leftChild), this.heightOf(node.rightChild));
  }

  // ------------------------------------------------------------------
  // Rotations
  // ------------------------------------------------------------------

  /**
   * Rotate a subtree to the right (fixes a left-heavy imbalance).
   *
   *         unbalancedNode            newRoot
   *            /      \               /      \
   *        newRoot     C     ==>     A    unbalancedNode
   *        /    \                          /      \
   *       A      B                        B        C
   *
   * @private
   * @param {AVLNode} unbalancedNode - Root of the subtree to rotate. Must have a left child.
   * @returns {AVLNode} The new root of the rotated subtree.
   */
  rotateRight(unbalancedNode) {
    const newRoot = unbalancedNode.leftChild;
    unbalancedNode.leftChild = newRoot.rightChild;
    newRoot.rightChild = unbalancedNode;
    this.updateHeight(unbalancedNode);
    this.updateHeight(newRoot);
    return newRoot;
  }

  /**
   * Rotate a subtree to the left (fixes a right-heavy imbalance).
   *
   *       unbalancedNode                 newRoot
   *          /      \                    /      \
   *         A      newRoot     ==>  unbalancedNode  C
   *                 /    \            /      \
   *                B      C          A        B
   *
   * @private
   * @param {AVLNode} unbalancedNode - Root of the subtree to rotate. Must have a right child.
   * @returns {AVLNode} The new root of the rotated subtree.
   */
  rotateLeft(unbalancedNode) {
    const newRoot = unbalancedNode.rightChild;
    unbalancedNode.rightChild = newRoot.leftChild;
    newRoot.leftChild = unbalancedNode;
    this.updateHeight(unbalancedNode);
    this.updateHeight(newRoot);
    return newRoot;
  }

  /**
   * Restore the AVL property at a node after a child subtree changed.
   * Handles the four classic cases: left-left, left-right, right-right, right-left.
   * @private
   * @param {AVLNode} node - Root of the subtree to rebalance.
   * @returns {AVLNode} The (possibly new) root of the balanced subtree.
   */
  rebalance(node) {
    this.updateHeight(node);
    const balanceFactor = this.balanceFactorOf(node);

    if (balanceFactor > 1) {
      // Left-heavy
      if (this.balanceFactorOf(node.leftChild) < 0) {
        node.leftChild = this.rotateLeft(node.leftChild); // Left-Right case
      }
      return this.rotateRight(node); // Left-Left case
    }

    if (balanceFactor < -1) {
      // Right-heavy
      if (this.balanceFactorOf(node.rightChild) > 0) {
        node.rightChild = this.rotateRight(node.rightChild); // Right-Left case
      }
      return this.rotateLeft(node); // Right-Right case
    }

    return node; // Already balanced
  }

  // ------------------------------------------------------------------
  // Recursive insert / remove
  // ------------------------------------------------------------------

  /**
   * Recursively insert a key into a subtree.
   * @private
   * @param {AVLNode|null} node - Root of the subtree.
   * @param {*} keyToInsert - Key to add.
   * @returns {AVLNode} The new root of the subtree after insertion and rebalancing.
   */
  insertInto(node, keyToInsert) {
    if (node === null) {
      this.wasInserted = true;
      return new AVLNode(keyToInsert);
    }

    const comparison = this.compareKeys(keyToInsert, node.key);
    if (comparison < 0) {
      node.leftChild = this.insertInto(node.leftChild, keyToInsert);
    } else if (comparison > 0) {
      node.rightChild = this.insertInto(node.rightChild, keyToInsert);
    } else {
      return node; // Duplicate key: tree is unchanged
    }

    return this.rebalance(node);
  }

  /**
   * Find the smallest node in a subtree.
   * @private
   * @param {AVLNode} node - Non-null root of the subtree.
   * @returns {AVLNode} The node holding the minimum key.
   */
  findMinNode(node) {
    let currentNode = node;
    while (currentNode.leftChild !== null) {
      currentNode = currentNode.leftChild;
    }
    return currentNode;
  }

  /**
   * Recursively remove a key from a subtree.
   * @private
   * @param {AVLNode|null} node - Root of the subtree.
   * @param {*} keyToRemove - Key to delete.
   * @returns {AVLNode|null} The new root of the subtree after removal and rebalancing.
   */
  removeFrom(node, keyToRemove) {
    if (node === null) return null; // Key not found

    const comparison = this.compareKeys(keyToRemove, node.key);
    if (comparison < 0) {
      node.leftChild = this.removeFrom(node.leftChild, keyToRemove);
    } else if (comparison > 0) {
      node.rightChild = this.removeFrom(node.rightChild, keyToRemove);
    } else {
      this.wasRemoved = true;

      // Zero or one child: replace this node with its only child.
      if (node.leftChild === null) return node.rightChild;
      if (node.rightChild === null) return node.leftChild;

      // Two children: copy in the in-order successor (smallest key in the
      // right subtree), then delete that successor from the right subtree.
      const successorNode = this.findMinNode(node.rightChild);
      node.key = successorNode.key;
      node.rightChild = this.removeFrom(node.rightChild, successorNode.key);
    }

    return this.rebalance(node);
  }

  /**
   * Verify BST ordering, stored heights, and AVL balance of a subtree.
   * @private
   * @param {AVLNode|null} node - Root of the subtree.
   * @returns {boolean} True if every node in the subtree satisfies the AVL invariants.
   */
  isValidSubtree(node) {
    if (node === null) return true;

    if (node.leftChild && this.compareKeys(node.leftChild.key, node.key) >= 0) return false;
    if (node.rightChild && this.compareKeys(node.rightChild.key, node.key) <= 0) return false;

    const expectedHeight =
      1 + Math.max(this.heightOf(node.leftChild), this.heightOf(node.rightChild));
    if (node.height !== expectedHeight) return false;
    if (Math.abs(this.balanceFactorOf(node)) > 1) return false;

    return this.isValidSubtree(node.leftChild) && this.isValidSubtree(node.rightChild);
  }

  // ------------------------------------------------------------------
  // Public interface
  // ------------------------------------------------------------------

  /**
   * Insert a key into the tree. Complexity: O(log n).
   * @param {*} keyToInsert - Key to add.
   * @returns {boolean} True if the key was inserted, false if it was already present.
   */
  insert(keyToInsert) {
    this.wasInserted = false;
    this.root = this.insertInto(this.root, keyToInsert);
    if (this.wasInserted) this.elementCount += 1;
    return this.wasInserted;
  }

  /**
   * Remove a key from the tree. Complexity: O(log n).
   * @param {*} keyToRemove - Key to delete.
   * @returns {boolean} True if the key was found and removed, false otherwise.
   */
  remove(keyToRemove) {
    this.wasRemoved = false;
    this.root = this.removeFrom(this.root, keyToRemove);
    if (this.wasRemoved) this.elementCount -= 1;
    return this.wasRemoved;
  }

  /**
   * Check whether a key is present. Complexity: O(log n).
   * @param {*} keyToFind - Key to search for.
   * @returns {boolean} True if the key exists in the tree.
   */
  contains(keyToFind) {
    let currentNode = this.root;
    while (currentNode !== null) {
      const comparison = this.compareKeys(keyToFind, currentNode.key);
      if (comparison === 0) return true;
      currentNode = comparison < 0 ? currentNode.leftChild : currentNode.rightChild;
    }
    return false;
  }

  /**
   * Iterate over all keys in ascending order. Complexity: O(n) for a full pass.
   * Enables `for (const key of tree)` and `[...tree]`.
   * @returns {Generator<*>}
   */
  *[Symbol.iterator]() {
    const pendingNodes = [];
    let currentNode = this.root;
    while (currentNode !== null || pendingNodes.length > 0) {
      while (currentNode !== null) {
        pendingNodes.push(currentNode);
        currentNode = currentNode.leftChild;
      }
      currentNode = pendingNodes.pop();
      yield currentNode.key;
      currentNode = currentNode.rightChild;
    }
  }

  /**
   * Get all keys in ascending order.
   * @returns {Array<*>} A sorted array containing every key in the tree.
   */
  toSortedArray() {
    return [...this];
  }

  /** @returns {number} Number of keys stored in the tree. */
  get size() {
    return this.elementCount;
  }

  /** @returns {boolean} True if the tree holds no keys. */
  isEmpty() {
    return this.elementCount === 0;
  }

  /** @returns {number} Height of the tree (0 when empty, 1 for a single node). */
  get height() {
    return this.heightOf(this.root);
  }

  /**
   * Check that the tree satisfies all AVL invariants.
   * Intended for testing/debugging; runs in O(n).
   * @returns {boolean} True if ordering, heights, and balance are all correct.
   */
  isValid() {
    return this.isValidSubtree(this.root);
  }
}

// ----------------------------------------------------------------------
// Demo
// ----------------------------------------------------------------------

/**
 * Print all keys of a tree on one line along with basic statistics.
 * @param {string} label - Text printed before the keys.
 * @param {AVLTree} tree - The tree to print.
 */
function printTree(label, tree) {
  console.log(
    `${label}: ${tree.toSortedArray().join(" ")} ` +
      `(size=${tree.size}, height=${tree.height}, valid=${tree.isValid()})`
  );
}

if (require.main === module) {
  const tree = new AVLTree();

  // Ascending inserts would degrade a plain BST into a linked list;
  // the AVL tree stays balanced (height ~ log2 n).
  for (let value = 1; value <= 15; value += 1) tree.insert(value);
  printTree("After inserting 1..15", tree);

  console.log("Duplicate insert of 7 accepted?", tree.insert(7));
  console.log("Contains 10?", tree.contains(10));
  console.log("Contains 99?", tree.contains(99));

  tree.remove(8); // Root of the perfectly balanced tree (two children)
  tree.remove(1);
  tree.remove(15);
  printTree("After removing 8, 1, 15", tree);

  console.log("Remove missing key 42 succeeded?", tree.remove(42));

  // Custom comparator: store objects ordered by a field.
  const peopleTree = new AVLTree((a, b) => a.age - b.age);
  peopleTree.insert({ name: "Ana", age: 31 });
  peopleTree.insert({ name: "Ben", age: 24 });
  peopleTree.insert({ name: "Cleo", age: 45 });
  console.log("People by age:", [...peopleTree].map((person) => person.name).join(", "));
}

module.exports = { AVLTree };
```