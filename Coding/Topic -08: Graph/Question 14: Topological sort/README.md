# Question 14: Topological Sort

**What is topological sorting?**
- Only valid in case of Directed Acyclic Graph (graph directed ho aur koi cycle nai ho).
- If 1 comes before 2, and 2 comes before 3, then we have to print also 1, 2, 3 like this. Maintaining ordering.

**Topological sort you can do in 2 ways:**
1. Khan's algorithm (variant of BFS, concept of In Degree)
2. DFS

Jitne nodes hai utne size ke array bana lo to keep track of inDegree (initially all zero).
1. Start traversing from adj list 0th Index.
2. Update inDegree array first.
3. Then what Kahn's algorithm says is, nodes with 0 in degree, first place them in queue. To basically har iteration me, hum indegree -1 karenge for processed nodes and if inDegree==0, then print it and insert into queue.

**What happens when Kahn's algorithm is applied on undirected graph or directed cyclic graph?**
We will not get correct output. For example if we have two components of a graph and second component is a single Node, meaning inDegree==0, so we process it and insert it into queue. But since this component has single node meaning no neighbour. So we will not be able to process other components of graph.

## Approach-1: Kahn's Algorithm (Variant of BFS)

### Steps:
1. **Indegree Calculation:** Create an array `indegree[]` that stores the number of incoming edges for each vertex.
2. **Enqueue Vertices with Indegree 0:** Initialize a queue and add all vertices that have an indegree of 0.
3. **BFS-like Process:** Remove a vertex from the queue, add it to the result, and reduce the indegree of all its neighbors. If a neighbor's indegree becomes 0, add it to the queue.
4. **Cycle Detection:** If the number of processed vertices is less than V, a cycle exists.
5. **Return Result:** Convert the result list to an array and return it.

### Time and Space Complexity:
- **Time:** O(V + E)
- **Space:** O(V + E)

### DRY Run:

Graph: V=6, Edges: 5→2, 5→0, 4→0, 4→1, 2→3, 3→1

- indegree[] = [2, 2, 1, 1, 0, 0]
- Queue: [4, 5]
- Process 4: res=[4], indegree of 0,1 decrease → [1, 1, 1, 1, 0, 0]
- Process 5: res=[4,5], indegree of 2,0 decrease → [0, 1, 0, 1, 0, 0], enqueue 0,2
- Process 0: res=[4,5,0]
- Process 2: res=[4,5,0,2], indegree of 3 → 0, enqueue 3
- Process 3: res=[4,5,0,2,3], indegree of 1 → 0, enqueue 1
- Process 1: res=[4,5,0,2,3,1]
- **Result: [4, 5, 0, 2, 3, 1]**

### Code:

```java
import java.util.*;

class Solution {
    static int[] topoSort(int V, ArrayList<ArrayList<Integer>> adj) {
        // Step 1: Initialize an indegree array
        int indegree[] = new int[V];

        // Step 2: Calculate indegrees for all vertices
        for (int u = 0; u < adj.size(); u++) {
            for (int v : adj.get(u)) {
                indegree[v]++;
            }
        }

        // Step 3: Initialize a queue and add all vertices with indegree 0
        Queue<Integer> queue = new LinkedList<>();
        for (int i = 0; i < V; i++) {
            if (indegree[i] == 0) {
                queue.offer(i);
            }
        }

        // Step 4: Create a list to store the topological sort order
        ArrayList<Integer> res = new ArrayList<>();

        // Step 5: Perform BFS-like traversal
        while (!queue.isEmpty()) {
            int node = queue.poll();
            res.add(node);

            for (int neighbor : adj.get(node)) {
                indegree[neighbor]--;
                if (indegree[neighbor] == 0) {
                    queue.offer(neighbor);
                }
            }
        }

        // Step 6: Check for cycles
        if (res.size() != V) {
            return new int[V]; // Return empty array if graph contains a cycle
        }

        // Step 7: Convert result list to an array and return
        int ans[] = new int[V];
        for (int i = 0; i < V; i++) {
            ans[i] = res.get(i);
        }
        return ans;
    }

    public static void main(String[] args) {
        ArrayList<ArrayList<Integer>> graph = new ArrayList<>();
        int V = 6;
        for (int i = 0; i < V; i++) {
            graph.add(new ArrayList<>());
        }

        graph.get(5).add(2);
        graph.get(5).add(0);
        graph.get(4).add(0);
        graph.get(4).add(1);
        graph.get(2).add(3);
        graph.get(3).add(1);

        int[] result = topoSort(V, graph);
        System.out.println("Topological sort order: " + Arrays.toString(result));
    }
}
```
