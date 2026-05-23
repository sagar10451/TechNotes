# Question 16: Graph Valid Tree

**Question:** 261. Graph Valid Tree

A graph is a valid tree if: (1) it has no cycles, and (2) all nodes are connected. A tree with n nodes must have exactly n-1 edges.

## 1. Brute Force Solution (Union-Find)

**Approach:**
- A tree must have exactly n-1 edges. If not, return false immediately.
- Use Union-Find to detect cycles. For each edge, if two nodes already have the same root, a cycle exists.

**Time Complexity:** O(E * α(N)) ≈ O(E)
**Space Complexity:** O(N)

### Code:

```java
import java.util.*;

public class Solution {
    public boolean validTree(int n, int[][] edges) {
        if (edges.length != n - 1) return false; // A tree must have exactly n-1 edges

        int[] parent = new int[n];
        for (int i = 0; i < n; i++) {
            parent[i] = i; // Initialize parent
        }

        for (int[] edge : edges) {
            if (!union(parent, edge[0], edge[1])) {
                return false; // Cycle detected
            }
        }

        return true; // No cycles and all nodes are connected
    }

    private int find(int[] parent, int x) {
        if (parent[x] != x) {
            parent[x] = find(parent, parent[x]); // Path compression
        }
        return parent[x];
    }

    private boolean union(int[] parent, int x, int y) {
        int rootX = find(parent, x);
        int rootY = find(parent, y);
        if (rootX == rootY) return false; // Cycle detected
        parent[rootX] = rootY; // Union
        return true;
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        int n = 5;
        int[][] edges = {{0, 1}, {0, 2}, {0, 3}, {1, 4}};
        System.out.println(solution.validTree(n, edges)); // Output: true
    }
}
```

## 2. Optimal Solution (DFS)

**Approach:**
- Check if edges.length == n-1 (necessary condition for a tree).
- Build adjacency list and run DFS from node 0.
- If we find a visited node that isn't the parent → cycle exists → not a tree.
- After DFS, check if all nodes were visited (connectivity check).

**Time Complexity:** O(V + E)
**Space Complexity:** O(V)

### Code:

```java
import java.util.*;

public class Solution {
    public boolean validTree(int n, int[][] edges) {
        if (edges.length != n - 1) return false; // A tree must have exactly n-1 edges

        List<List<Integer>> graph = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            graph.add(new ArrayList<>()); // Initialize adjacency list
        }

        // Build the graph
        for (int[] edge : edges) {
            graph.get(edge[0]).add(edge[1]);
            graph.get(edge[1]).add(edge[0]);
        }

        boolean[] visited = new boolean[n];
        if (hasCycle(graph, visited, 0, -1)) return false; // Start DFS from node 0

        for (boolean visit : visited) {
            if (!visit) return false; // Not all nodes are connected
        }

        return true; // No cycles and all nodes are connected
    }

    private boolean hasCycle(List<List<Integer>> graph, boolean[] visited, int node, int parent) {
        visited[node] = true; // Mark the current node as visited

        for (int neighbor : graph.get(node)) {
            if (!visited[neighbor]) {
                if (hasCycle(graph, visited, neighbor, node)) return true;
            } else if (neighbor != parent) {
                return true; // Cycle detected
            }
        }

        return false;
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        int n = 5;
        int[][] edges = {{0, 1}, {0, 2}, {0, 3}, {1, 4}};
        System.out.println(solution.validTree(n, edges)); // Output: true
    }
}
```
