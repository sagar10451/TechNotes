
# Question 06: Evaluate Division

**Question:** 399. Evaluate Division

## Approach-1: DFS + Backtracking

We will use HashMap to store Graph.

### Steps and Explanation:

**Graph Representation:**
- We represent the problem as a graph where each variable is a node, and an edge between two nodes represents the division value between them.
- For each equation a / b = value, we add an edge a -> b = value and b -> a = 1 / value.

**DFS with Backtracking:**
- For each query, we use DFS to explore the graph starting from the numerator node.
- The DFS function recursively explores neighboring nodes until it finds the denominator or returns -1 if no path is found.

**Handling Cycles:**
- We use a `Set<String> seen` to track visited nodes during the DFS to avoid visiting the same node multiple times and entering a cycle.

**Base Case:**
- If the start and end nodes are the same (e.g., a/a), we return 1.0 since any variable divided by itself equals 1.

### Time and Space Complexity:
- **Time Complexity:** O(E + Q * V) — Building the graph takes O(E). For each query, DFS can visit all nodes O(V). Q queries total.
- **Space Complexity:** O(V + E) — Graph storage + DFS recursion stack.

### Dry Run:

**Test Case:** Equations: a/b = 2.0, b/c = 3.0. Queries: ["a","c"], ["b","a"], ["a","e"], ["a","a"], ["x","x"]

**Building the Graph:**
- a -> b = 2.0, b -> a = 0.5
- b -> c = 3.0, c -> b = 0.333

**Query 1: a/c** — Start DFS from a → b (×2.0) → c (×3.0) = 6.0
**Query 2: b/a** — Start DFS from b → a (×0.5) = 0.5
**Query 3: a/e** — e not in graph → -1.0
**Query 4: a/a** — Same variable → 1.0
**Query 5: x/x** — x not in graph → -1.0

**Final Output:** [6.0, 0.5, -1.0, 1.0, -1.0]

### Code:

```java
import java.util.*;

class Solution {
    // Main function to evaluate division using DFS and backtracking
    public double[] calcEquation(List<List<String>> equations, double[] values, List<List<String>> queries) {
        double[] ans = new double[queries.size()];  // Array to store results of queries

        // Step 1: Build the graph
        Map<String, Map<String, Double>> graph = new HashMap<>();
        for (int i = 0; i < equations.size(); ++i) {
            final String A = equations.get(i).get(0);  // numerator
            final String B = equations.get(i).get(1);  // denominator
            graph.putIfAbsent(A, new HashMap<>());     // Add A to the graph if not present
            graph.putIfAbsent(B, new HashMap<>());     // Add B to the graph if not present
            graph.get(A).put(B, values[i]);            // A -> B = value
            graph.get(B).put(A, 1.0 / values[i]);      // B -> A = 1 / value (reverse edge)
        }

        // Step 2: Process each query using DFS
        for (int i = 0; i < queries.size(); ++i) {
            final String A = queries.get(i).get(0);  // Start of the query (numerator)
            final String C = queries.get(i).get(1);  // End of the query (denominator)

            // If either A or C doesn't exist in the graph, return -1.0 for this query
            if (!graph.containsKey(A) || !graph.containsKey(C))
                ans[i] = -1.0;
            else
                ans[i] = dfs(graph, A, C, new HashSet<>());  // Perform DFS to find the result
        }

        return ans;  // Return the array of results
    }

    // DFS function to find the division result using backtracking
    private double dfs(Map<String, Map<String, Double>> graph, final String A, final String C, Set<String> seen) {
        if (A.equals(C))  // If the start and end nodes are the same, return 1.0
            return 1.0;

        seen.add(A);  // Mark the current node as visited

        // Explore all neighbors of A (adjacent nodes in the graph)
        for (final String B : graph.get(A).keySet()) {
            if (seen.contains(B))  // Skip if already visited to avoid cycles
                continue;
            final double res = dfs(graph, B, C, seen);  // Recursively perform DFS on B
            if (res > 0)  // If the path to C is found
                return graph.get(A).get(B) * res;  // Return the product of current edge and result
        }

        return -1.0;  // If no valid path is found, return -1.0
    }

    // Main method to test the DFS solution
    public static void main(String[] args) {
        Solution solution = new Solution();

        // Example test case
        List<List<String>> equations = Arrays.asList(
            Arrays.asList("a", "b"),
            Arrays.asList("b", "c")
        );
        double[] values = {2.0, 3.0};

        List<List<String>> queries = Arrays.asList(
            Arrays.asList("a", "c"),  // Should return 6.0
            Arrays.asList("b", "a"),  // Should return 0.5
            Arrays.asList("a", "e"),  // Should return -1.0 (since 'e' is not present)
            Arrays.asList("a", "a"),  // Should return 1.0 (same variable query)
            Arrays.asList("x", "x")   // Should return -1.0 (since 'x' is not present)
        );

        // Run the solution and print the results
        double[] result = solution.calcEquation(equations, values, queries);
        System.out.println(Arrays.toString(result));  // Expected output: [6.0, 0.5, -1.0, 1.0, -1.0]
    }
}
```
