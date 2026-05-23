
# Question 03: Course Schedule II

**Question:** 210. Course Schedule II

## Optimal Solution (Kahn's Algorithm - Topological Sorting)

**Changes from Course Schedule I:**
- We will store the topological order and return it if the sort is valid (no cycle).
- If a cycle is detected, return an empty array.

### Time and Space Complexity:
- **Time Complexity:** O(V + E)
- **Space Complexity:** O(V + E)

### DRY Run:

**Test Case 1:** numCourses = 4, prerequisites = [[1, 0], [2, 0], [3, 1], [3, 2]]
- Adjacency List: 0 → [1, 2], 1 → [3], 2 → [3]
- Indegree Array: [0, 1, 1, 2]
- Queue: Initially contains course 0
- Processing: Dequeue 0 → add 1,2 to queue → Dequeue 1 → Dequeue 2 → add 3 → Dequeue 3
- **Result: [0, 1, 2, 3]** (valid order)

**Test Case 2:** numCourses = 2, prerequisites = [[1, 0], [0, 1]]
- Cycle detected → **Result: []** (empty array)

### Code:

```java
import java.util.*;

class Solution {
    // Function to return the course order if possible (Course Schedule II)
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        // Step 1: Create an adjacency list to represent course dependencies
        ArrayList<ArrayList<Integer>> adj = new ArrayList<>();
        for (int i = 0; i < numCourses; i++) {
            adj.add(new ArrayList<>());  // Initialize an empty list for each course
        }

        // Populate the adjacency list using prerequisites array
        for (int i = 0; i < prerequisites.length; i++) {
            int course = prerequisites[i][0];   // Course that requires a prerequisite
            int prereqCourse = prerequisites[i][1]; // Prerequisite course
            adj.get(prereqCourse).add(course);  // prereqCourse → course (dependency direction)
        }

        // Step 2: Perform topological sorting (if no cycle, return the order)
        return topoSort(numCourses, adj);
    }

    // Function to perform topological sorting using BFS (Kahn's Algorithm)
    int[] topoSort(int V, ArrayList<ArrayList<Integer>> adj) {
        // Step 1: Calculate the indegree for each vertex
        int indegree[] = new int[V];
        for (int u = 0; u < adj.size(); u++) {
            for (int v : adj.get(u)) {
                indegree[v]++;
            }
        }

        // Step 2: Initialize a queue with all courses that have indegree 0
        Queue<Integer> queue = new LinkedList<>();
        for (int i = 0; i < V; i++) {
            if (indegree[i] == 0) {
                queue.offer(i);
            }
        }

        // Step 3: List to store the topological order
        ArrayList<Integer> res = new ArrayList<>();

        // Step 4: Process the courses in the queue
        while (!queue.isEmpty()) {
            int node = queue.poll();
            res.add(node);

            for (int i = 0; i < adj.get(node).size(); i++) {
                int neighbour = adj.get(node).get(i);
                indegree[neighbour]--;
                if (indegree[neighbour] == 0) {
                    queue.offer(neighbour);
                }
            }
        }

        // Step 5: If we processed all courses, return the topological order
        if (res.size() == V) {
            int[] order = new int[V];
            for (int i = 0; i < V; i++) {
                order[i] = res.get(i);
            }
            return order;
        } else {
            // Cycle detected, return an empty array
            return new int[0];
        }
    }

    // Main method to test the solution
    public static void main(String[] args) {
        // Test case 1: 4 courses with valid order
        int numCourses1 = 4;
        int[][] prerequisites1 = {
            {1, 0},  {2, 0},  {3, 1},  {3, 2}
        };

        Solution solution = new Solution();
        int[] order1 = solution.findOrder(numCourses1, prerequisites1);
        System.out.println("Course order for Test Case 1: " + Arrays.toString(order1));  // Expected: [0, 1, 2, 3]

        // Test case 2: 2 courses with a cycle
        int numCourses2 = 2;
        int[][] prerequisites2 = {
            {1, 0},  {0, 1}
        };

        int[] order2 = solution.findOrder(numCourses2, prerequisites2);
        System.out.println("Course order for Test Case 2: " + Arrays.toString(order2));  // Expected: []
    }
}
```
