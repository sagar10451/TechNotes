# Question 05: Cycle Detection in Undirected Graph

**Question:** https://www.geeksforgeeks.org/problems/detect-cycle-in-an-undirected-graph/1

## 1. Brute Force — BFS (Level Order Traversal)

**Approach:**
- Use BFS starting from each unvisited node.
- For each node, track its parent (the node from which we arrived).
- If we encounter a visited node that is NOT the parent of the current node, a cycle exists.
- Handle disconnected components by iterating through all nodes.

**Why it works:** In an undirected graph, if BFS reaches an already-visited node that isn't the parent, it means there's another path to that node — forming a cycle.

- **Time:** O(V + E)
- **Space:** O(V)

```java
import java.util.*;

class Solution {
    // Function to detect cycle in an undirected graph using BFS
    public boolean isCycle(int V, ArrayList<ArrayList<Integer>> adj) {
        boolean[] visited = new boolean[V];

        // Check for all components (graph may be disconnected)
        for (int i = 0; i < V; i++) {
            if (!visited[i]) {
                if (bfsCycleCheck(i, adj, visited)) {
                    return true; // Cycle found
                }
            }
        }
        return false; // No cycle found
    }

    // BFS helper to check cycle starting from a given node
    private boolean bfsCycleCheck(int start, ArrayList<ArrayList<Integer>> adj, boolean[] visited) {
        Queue<int[]> queue = new LinkedList<>(); // Each entry: [node, parent]
        queue.offer(new int[]{start, -1}); // Start node has no parent (-1)
        visited[start] = true;

        while (!queue.isEmpty()) {
            int[] current = queue.poll();
            int node = current[0];
            int parent = current[1];

            // Explore all neighbors
            for (int neighbor : adj.get(node)) {
                if (!visited[neighbor]) {
                    visited[neighbor] = true;
                    queue.offer(new int[]{neighbor, node}); // neighbor's parent is current node
                } else if (neighbor != parent) {
                    // Visited neighbor that is NOT the parent → cycle detected
                    return true;
                }
            }
        }
        return false; // No cycle from this component
    }

    public static void main(String[] args) {
        int V = 5;
        ArrayList<ArrayList<Integer>> adj = new ArrayList<>();
        for (int i = 0; i < V; i++) {
            adj.add(new ArrayList<>());
        }

        // Add undirected edges (forming a cycle: 0-1-2-0)
        adj.get(0).add(1); adj.get(1).add(0);
        adj.get(1).add(2); adj.get(2).add(1);
        adj.get(2).add(0); adj.get(0).add(2);
        adj.get(3).add(4); adj.get(4).add(3);

        Solution solution = new Solution();
        System.out.println("Does the graph contain a cycle? " + solution.isCycle(V, adj)); // Expected: true
    }
}
```

### Dry Run:

Graph: 0—1—2—0 (cycle), 3—4 (no cycle)

| Step | Node | Parent | Neighbors | Action |
|------|------|--------|-----------|--------|
| 1 | 0 | -1 | 1, 2 | Visit 1 (parent=0), Visit 2 (parent=0) |
| 2 | 1 | 0 | 0, 2 | 0 is visited but is parent → skip. 2 is visited and NOT parent → **Cycle!** |

Result: **true** ✅

---

## 2. Optimal — DFS (Same Complexity, Simpler Code)

**Approach:**
- Use DFS starting from each unvisited node.
- Track the parent of each node.
- If DFS reaches a visited node that is NOT the parent, a cycle exists.

- **Time:** O(V + E)
- **Space:** O(V)

```java
import java.util.*;

class Solution {
    // Function to detect cycle in an undirected graph using DFS
    public boolean isCycle(int V, ArrayList<ArrayList<Integer>> adj) {
        boolean[] visited = new boolean[V];

        // Check for all components (graph may be disconnected)
        for (int i = 0; i < V; i++) {
            if (!visited[i]) {
                if (dfsCycleCheck(i, -1, adj, visited)) {
                    return true; // Cycle found
                }
            }
        }
        return false; // No cycle found
    }

    // DFS helper to check cycle
    private boolean dfsCycleCheck(int node, int parent, ArrayList<ArrayList<Integer>> adj, boolean[] visited) {
        visited[node] = true; // Mark current node as visited

        // Explore all neighbors
        for (int neighbor : adj.get(node)) {
            if (!visited[neighbor]) {
                // If neighbor not visited, recurse
                if (dfsCycleCheck(neighbor, node, adj, visited)) {
                    return true; // Cycle found in deeper recursion
                }
            } else if (neighbor != parent) {
                // Visited neighbor that is NOT the parent → cycle detected
                return true;
            }
        }
        return false; // No cycle from this node
    }

    public static void main(String[] args) {
        int V = 5;
        ArrayList<ArrayList<Integer>> adj = new ArrayList<>();
        for (int i = 0; i < V; i++) {
            adj.add(new ArrayList<>());
        }

        // Add undirected edges (forming a cycle: 0-1-2-0)
        adj.get(0).add(1); adj.get(1).add(0);
        adj.get(1).add(2); adj.get(2).add(1);
        adj.get(2).add(0); adj.get(0).add(2);
        adj.get(3).add(4); adj.get(4).add(3);

        Solution solution = new Solution();
        System.out.println("Does the graph contain a cycle? " + solution.isCycle(V, adj)); // Expected: true
    }
}
```

### Dry Run:

Graph: 0—1—2—0 (cycle), 3—4 (no cycle)

```
DFS from 0 (parent=-1):
  Visit 0, go to neighbor 1
    DFS from 1 (parent=0):
      Visit 1, go to neighbor 0 → visited, is parent → skip
      Go to neighbor 2
        DFS from 2 (parent=1):
          Visit 2, go to neighbor 1 → visited, is parent → skip
          Go to neighbor 0 → visited, NOT parent → CYCLE DETECTED!
```

Result: **true** ✅

---

## Summary

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| BFS (Level Order) | O(V + E) | O(V) | Track parent in queue |
| DFS (Recursive) | O(V + E) | O(V) | Track parent in recursion |

**Interview tip:** Both BFS and DFS have the same complexity. The key insight: "In an undirected graph, if I reach a visited node that isn't my parent, there must be another path to it — that's a cycle." The parent check is crucial because in undirected graphs, every edge goes both ways, so without it you'd falsely detect the edge you came from as a cycle.
