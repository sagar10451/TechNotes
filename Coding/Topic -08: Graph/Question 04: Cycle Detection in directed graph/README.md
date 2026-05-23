
# Question 04: Cycle Detection in Directed Graph

**Question:** https://www.geeksforgeeks.org/problems/detect-cycle-in-a-directed-graph/1

## Approach-1: Topological Sorting / Kahn's Algorithm (Variant of BFS)

**What is topological sorting?**
- Only valid in case of Directed Acyclic Graph (graph directed ho aur koi cycle nai ho).
- If 1 comes before 2, and 2 comes before 3, then we have to print also 1, 2, 3 like this. Maintaining ordering.

**How to find directed graph has cycle?**
- Apply topological sorting, if topological sort cannot be generated then we can say that directed graph has cycle.

### Steps and Explanation:

1. **Indegree Calculation:** Calculate the indegree for each vertex. This helps identify vertices that have no dependencies.
2. **Enqueue Vertices with Indegree 0:** All vertices with an indegree of 0 are added to a queue.
3. **BFS Process (Topological Sorting):** Process each vertex in the queue, reduce the indegree of its neighbors by 1, and add any neighbors whose indegree becomes 0 to the queue.
4. **Cycle Detection:** If the size of the topological order list is not equal to the number of vertices, then there is a cycle in the graph.
5. **Return Result:** If we detect a cycle, return true. Otherwise, return false.

### Time and Space Complexity:
- **Time Complexity:** O(V + E)
- **Space Complexity:** O(V + E)

### DRY Run:

**Graph with Cycle:** V = 4, Edges: 0→1, 1→2, 2→3, 3→1 (Cycle)
- indegree[] = [0, 2, 1, 1]
- Queue: [0] (Only vertex 0 has indegree 0)
- Process Vertex 0: res = [0], indegree[1] becomes 1
- No more vertices with indegree 0 → queue empty
- res.size() = 1 ≠ V = 4 → **Cycle Detected: true**

### Code:

```java
import java.util.*;

class Solution {
    // Function to detect cycle in a directed graph.
    public boolean isCyclic(int V, ArrayList<ArrayList<Integer>> adj) {
        return topoSort(V, adj);
    }

    // Function to perform topological sorting using BFS (Kahn's Algorithm)
    boolean topoSort(int V, ArrayList<ArrayList<Integer>> adj) {
        // Step 1: Calculate indegree for each vertex
        int indegree[] = new int[V];
        for (int u = 0; u < adj.size(); u++) {
            for (int v : adj.get(u)) {
                indegree[v]++;
            }
        }

        // Step 2: Initialize the queue with all vertices having indegree 0
        Queue<Integer> queue = new LinkedList<>();
        for (int i = 0; i < V; i++) {
            if (indegree[i] == 0) {
                queue.offer(i);
            }
        }

        // Step 3: List to store the topological order
        ArrayList<Integer> res = new ArrayList<>();

        // Step 4: Process the vertices in the queue
        while (!queue.isEmpty()) {
            int node = queue.poll();
            res.add(node);

            for (int neighbour : adj.get(node)) {
                indegree[neighbour]--;
                if (indegree[neighbour] == 0) {
                    queue.offer(neighbour);
                }
            }
        }

        // Step 5: If not all vertices processed, cycle exists
        if (res.size() != V) {
            return true;  // Cycle detected
        }
        return false;  // No cycle detected
    }

    // Main method to test the solution
    public static void main(String[] args) {
        int V = 4;
        ArrayList<ArrayList<Integer>> adj = new ArrayList<>();

        for (int i = 0; i < V; i++) {
            adj.add(new ArrayList<>());
        }

        // Add directed edges (forming a cycle)
        adj.get(0).add(1);
        adj.get(1).add(2);
        adj.get(2).add(3);
        adj.get(3).add(1);  // This creates a cycle (1 → 2 → 3 → 1)

        Solution solution = new Solution();
        boolean hasCycle = solution.isCyclic(V, adj);
        System.out.println("Does the graph contain a cycle? " + hasCycle);  // Expected: true
    }
}
```
