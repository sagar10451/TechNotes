# Question 01: Clone Graph

**Question:** 133. Clone Graph

## Approach-1: DFS

**Approach:**
- **Graph Representation:** Each node in the graph is represented by the Node class, which contains the node's value and a list of its neighbors.
- **DFS Strategy:**
  - Start at any given node.
  - For each node, if it hasn't been visited before (i.e., cloned), create a clone and recursively clone all its neighbors.
  - Use a HashMap to map each original node to its corresponding cloned node.
  - Recursively traverse the graph using DFS, and for each node, clone its neighbors.
  - Return the cloned node of the starting node as the result.

**Steps in DFS:**
1. **Base Case:** If the node is null, return null.
2. **Cloning the Node:** If the node has already been cloned (i.e., exists in the visited map), return the cloned node.
3. **Recursive DFS for Neighbors:** For each neighbor of the current node, perform DFS to clone it if it hasn't been cloned yet. Add the cloned neighbor to the cloned node's neighbor list.
4. **Return the Cloned Graph:** After cloning all the neighbors, return the cloned node as the result of the function.

### Time and Space Complexity:
- **Time Complexity:** O(N + M) — N is the number of nodes, M is the number of edges. We visit each node and each edge once during the DFS traversal.
- **Space Complexity:** O(N) — We store the cloned nodes in the visited HashMap, and the recursion stack will hold up to N recursive calls.

### Dry Run:

Given a graph with four nodes:
```
1 - 2
|   |
4 - 3
```

- Start from node 1. Clone it.
- Clone node 2 and node 4 (neighbors of node 1).
- For node 2, recursively clone node 3.
- For node 4, clone node 3 if it hasn't been cloned.
- Finally, return the cloned graph starting from the cloned node 1.

### Code:

```java
import java.util.*;

class Node {
    public int val;
    public List<Node> neighbors;

    public Node() {
        val = 0;
        neighbors = new ArrayList<Node>();
    }

    public Node(int _val) {
        val = _val;
        neighbors = new ArrayList<Node>();
    }

    public Node(int _val, ArrayList<Node> _neighbors) {
        val = _val;
        neighbors = _neighbors;
    }
}

class Solution {
    // HashMap to store the original node -> cloned node mapping
    private Map<Node, Node> visited = new HashMap<>();

    // Main function to clone the graph using DFS
    public Node cloneGraph(Node node) {
        if (node == null) {
            return null; // Base case: if the input node is null, return null
        }

        // If the node is already visited, return the cloned node
        if (visited.containsKey(node)) {
            return visited.get(node);
        }

        // Step 1: Create a new clone node with the value of the original node
        Node cloneNode = new Node(node.val);

        // Step 2: Store the clone in the visited map
        visited.put(node, cloneNode);

        // Step 3: Recursively clone all the neighbors of the node
        for (Node neighbor : node.neighbors) {
            cloneNode.neighbors.add(cloneGraph(neighbor)); // Clone each neighbor and add to the clone's neighbors
        }

        return cloneNode; // Return the cloned node
    }

    // Main method for testing the DFS graph clone
    public static void main(String[] args) {
        Solution solution = new Solution();

        // Test case: Build the original graph manually for testing
        Node node1 = new Node(1);
        Node node2 = new Node(2);
        Node node3 = new Node(3);
        Node node4 = new Node(4);

        node1.neighbors.add(node2);
        node1.neighbors.add(node4);
        node2.neighbors.add(node1);
        node2.neighbors.add(node3);
        node3.neighbors.add(node2);
        node3.neighbors.add(node4);
        node4.neighbors.add(node1);
        node4.neighbors.add(node3);

        // Clone the graph
        Node clonedGraph = solution.cloneGraph(node1);

        // Print the cloned graph
        System.out.println("Cloned graph's node value: " + clonedGraph.val);  // Output should be 1
    }
}
```

---

## Approach-2: BFS

**Approach:**
- Start at the given node and create a clone for it.
- Use a queue to explore each node and a HashMap to keep track of the original node to its cloned node mapping.
- For each node, create its clone, and for each of its neighbors, if it hasn't been cloned yet, clone it and add it to the queue for further exploration.

**Steps in BFS:**
1. **Base Case:** If the node is null, return null.
2. **Cloning the Node:** Create a clone of the starting node and put it in the visited map.
3. **BFS Traversal:** While the queue is not empty: Dequeue the front node. For each neighbor: If it hasn't been cloned (not in visited), clone it and enqueue it. Add the cloned neighbor to the current cloned node's neighbor list.
4. **Return the Cloned Graph:** After all nodes are processed, return the cloned node.

### Time and Space Complexity:
- **Time Complexity:** O(N + M) — Same as DFS.
- **Space Complexity:** O(N) — HashMap + queue.

### Code:

```java
import java.util.*;

class Solution {
    // Main function to clone the graph using BFS
    public Node cloneGraph(Node node) {
        if (node == null) {
            return null; // Base case: if the input node is null, return null
        }

        // HashMap to map original nodes to cloned nodes
        Map<Node, Node> visited = new HashMap<>();

        // Create the clone for the starting node
        Node cloneNode = new Node(node.val);
        visited.put(node, cloneNode);

        // Use a queue for BFS
        Queue<Node> queue = new LinkedList<>();
        queue.offer(node); // Start BFS from the original node

        // Perform BFS
        while (!queue.isEmpty()) {
            Node current = queue.poll(); // Get the current node from the queue

            // Explore all neighbors of the current node
            for (Node neighbor : current.neighbors) {
                // If the neighbor hasn't been cloned yet, create a clone
                if (!visited.containsKey(neighbor)) {
                    visited.put(neighbor, new Node(neighbor.val)); // Clone the neighbor
                    queue.offer(neighbor); // Add the original neighbor to the queue for further exploration
                }
                // Add the cloned neighbor to the current cloned node's neighbors
                visited.get(current).neighbors.add(visited.get(neighbor));
            }
        }

        return cloneNode; // Return the cloned node
    }

    // Main method for testing the BFS graph clone
    public static void main(String[] args) {
        Solution solution = new Solution();

        // Test case: Build the original graph manually for testing
        Node node1 = new Node(1);
        Node node2 = new Node(2);
        Node node3 = new Node(3);
        Node node4 = new Node(4);

        node1.neighbors.add(node2);
        node1.neighbors.add(node4);
        node2.neighbors.add(node1);
        node2.neighbors.add(node3);
        node3.neighbors.add(node2);
        node3.neighbors.add(node4);
        node4.neighbors.add(node1);
        node4.neighbors.add(node3);

        // Clone the graph
        Node clonedGraph = solution.cloneGraph(node1);

        // Print the cloned graph
        System.out.println("Cloned graph's node value: " + clonedGraph.val);  // Output should be 1
    }
}
```

