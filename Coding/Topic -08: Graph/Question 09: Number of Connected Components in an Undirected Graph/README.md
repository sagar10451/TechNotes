# Question 09: Number of Connected Components in an Undirected Graph

**Question:** 323. Number of Connected Components in an Undirected Graph

Given n nodes labeled from 0 to n-1 and a list of undirected edges, find the number of connected components in an undirected graph.

## 1. Brute Force — DFS

**Approach:**
1. Build an adjacency list from the edges.
2. Maintain a visited array.
3. For each unvisited node, perform DFS to visit all nodes in its component.
4. Each DFS call from the main loop = one connected component.

- **Time:** O(V + E)
- **Space:** O(V + E)

```java
import java.util.*;

class Solution {
    public int countComponents(int n, int[][] edges) {
        // Step 1: Build adjacency list
        ArrayList<ArrayList<Integer>> adj = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            adj.add(new ArrayList<>());
        }

        for (int[] edge : edges) {
            adj.get(edge[0]).add(edge[1]);
            adj.get(edge[1]).add(edge[0]);
        }

        // Step 2: DFS to count components
        boolean[] visited = new boolean[n];
        int components = 0;

        for (int i = 0; i < n; i++) {
            if (!visited[i]) {
                dfs(adj, visited, i);
                components++; // Each DFS from main loop = one component
            }
        }

        return components;
    }

    private void dfs(ArrayList<ArrayList<Integer>> adj, boolean[] visited, int node) {
        visited[node] = true;

        for (int neighbor : adj.get(node)) {
            if (!visited[neighbor]) {
                dfs(adj, visited, neighbor);
            }
        }
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        // Test case 1: 5 nodes, 3 edges → 2 components
        int n = 5;
        int[][] edges = {{0, 1}, {1, 2}, {3, 4}};
        System.out.println("Number of components: " + solution.countComponents(n, edges)); // Expected: 2

        // Test case 2: 5 nodes, 4 edges → 1 component
        int[][] edges2 = {{0, 1}, {1, 2}, {2, 3}, {3, 4}};
        System.out.println("Number of components: " + solution.countComponents(n, edges2)); // Expected: 1
    }
}
```

## 2. Optimal — Union Find (Disjoint Set Union)

**Approach:**
1. Initialize each node as its own parent (n separate components).
2. For each edge, union the two nodes.
3. Each successful union reduces the component count by 1.
4. Return the final component count.

- **Time:** O(V + E × α(V)) ≈ O(V + E) with path compression
- **Space:** O(V)

```java
import java.util.*;

class Solution {
    int[] parent;
    int[] rank;

    public int countComponents(int n, int[][] edges) {
        parent = new int[n];
        rank = new int[n];
        int components = n; // Initially, each node is its own component

        // Initialize parent array (each node is its own parent)
        for (int i = 0; i < n; i++) {
            parent[i] = i;
            rank[i] = 0;
        }

        // For each edge, try to union the two nodes
        for (int[] edge : edges) {
            if (union(edge[0], edge[1])) {
                components--; // Successful union means one less component
            }
        }

        return components;
    }

    // Find with path compression
    private int find(int x) {
        if (parent[x] != x) {
            parent[x] = find(parent[x]); // Path compression
        }
        return parent[x];
    }

    // Union by rank
    private boolean union(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);

        if (rootX == rootY) return false; // Already in same component

        if (rank[rootX] < rank[rootY]) {
            parent[rootX] = rootY;
        } else if (rank[rootX] > rank[rootY]) {
            parent[rootY] = rootX;
        } else {
            parent[rootY] = rootX;
            rank[rootX]++;
        }

        return true; // Successfully merged two components
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        int n = 5;
        int[][] edges = {{0, 1}, {1, 2}, {3, 4}};
        System.out.println("Number of components: " + solution.countComponents(n, edges)); // Expected: 2
    }
}
```
