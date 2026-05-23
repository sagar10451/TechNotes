
# Question 02: Course Schedule I

**Question:** 207. Course Schedule

## Optimal Solution (Kahn's Algorithm - Topological Sorting)

### Steps and Explanation:

1. **Create an adjacency list:** We represent each course as a vertex in a graph, where edges denote prerequisites. An edge from course A to course B (A → B) means that course A must be completed before course B.

2. **Topological Sort using BFS (Kahn's Algorithm):**
   - We calculate the indegree of each vertex (number of incoming edges).
   - Courses with indegree = 0 (no prerequisites) are added to a queue.
   - We repeatedly dequeue a course, mark it as processed, and reduce the indegree of its dependent courses. If any dependent course's indegree becomes 0, it's added to the queue.
   - This continues until all vertices are processed or a cycle is detected.

3. **Detect a cycle:** If a cycle exists, the topological sort will not process all the courses. The condition `res.size() != V` checks this; if true, a cycle is present, meaning it's not possible to finish all courses.

### Time and Space Complexity:
- **Time Complexity:** O(V + E) — V is the number of courses, E is the number of prerequisites.
- **Space Complexity:** O(V + E) — adjacency list + indegree array + queue.

### DRY Run:

**Test Case 1:** numCourses = 2, prerequisites = [[1, 0], [0, 1]]
- Adjacency List: 0 → [1], 1 → [0]
- Indegree Array: [1, 1] (Both courses have one prerequisite)
- Queue: Empty (no course has indegree = 0)
- Cycle Detected: Since no course can be processed, res.size() = 0
- **Output: false** (cannot finish courses)

**Test Case 2:** numCourses = 4, prerequisites = [[1, 0], [2, 1], [3, 2]]
- Adjacency List: 0 → [1], 1 → [2], 2 → [3]
- Indegree Array: [0, 1, 1, 1]
- Queue: Initially contains course 0
- Processing: Course 0 → 1 → 2 → 3 (all processed)
- **Output: true** (can finish courses)

### Code:

```java
import java.util.*;

class Solution {
    // Function to check if it is possible to finish all courses (Course Schedule)
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        // Step 1: Create an adjacency list to represent course dependencies
        ArrayList<ArrayList<Integer>> adj = new ArrayList<>();
        for (int i = 0; i < numCourses; i++) {
            adj.add(new ArrayList<>());  // Initialize an empty list for each course
        }

        // Step 2: Populate the adjacency list using prerequisites array
        // prereqCourse → course indicates prereqCourse must be taken before course
        for (int i = 0; i < prerequisites.length; i++) {
            int course = prerequisites[i][0];   // Course that requires a prerequisite
            int prereqCourse = prerequisites[i][1]; // Prerequisite course
            adj.get(prereqCourse).add(course);  // prereqCourse → course (dependency direction)
        }

        // Step 3: Check if a topological sort is possible (no cycle exists)
        // If topoSort detects a cycle, return false (courses cannot be finished)
        if (topoSort(numCourses, adj)) {
            return false; // Cycle detected, cannot finish all courses
        } else {
            return true;  // No cycle, all courses can be finished
        }
    }

    // Function to perform topological sorting using BFS (Kahn's Algorithm)
    // This will help detect if there's a cycle in the course dependency graph
    boolean topoSort(int V, ArrayList<ArrayList<Integer>> adj) {
        // Step 1: Calculate the indegree (number of incoming edges) for each vertex (course)
        int indegree[] = new int[V];  // Array to store indegree for each course
        for (int u = 0; u < adj.size(); u++) {
            for (int v : adj.get(u)) {
                indegree[v]++;  // Increment indegree for each dependent course
            }
        }

        // Step 2: Initialize a queue with all courses that have indegree 0 (no prerequisites)
        Queue<Integer> queue = new LinkedList<>();
        for (int i = 0; i < V; i++) {
            if (indegree[i] == 0) {  // Course i has no incoming edges (prerequisites)
                queue.offer(i);
            }
        }

        // Step 3: List to store the topological order (or to check if all courses are processed)
        ArrayList<Integer> res = new ArrayList<>();

        // Step 4: Process the courses in the queue
        while (!queue.isEmpty()) {
            int node = queue.poll();  // Dequeue a course
            res.add(node);  // Add it to the topological order (or processed list)

            // For each dependent course (neighbour) of the current course
            for (int i = 0; i < adj.get(node).size(); i++) {
                int neighbour = adj.get(node).get(i);
                indegree[neighbour]--;  // Reduce indegree of the neighbor course
                if (indegree[neighbour] == 0) {  // If indegree becomes 0, add to queue
                    queue.offer(neighbour);
                }
            }
        }

        // Step 5: If we processed all courses (no cycle), res should have all vertices
        // If res.size() != V, it means a cycle exists in the graph
        if (res.size() != V) {
            return true;  // Cycle detected
        } else {
            return false;  // No cycle detected
        }
    }

    // Main method to test the solution with sample test cases
    public static void main(String[] args) {
        // Test case 1: 2 courses with a cycle
        int numCourses1 = 2;
        int[][] prerequisites1 = {
            {1, 0},  // Course 1 depends on course 0
            {0, 1}   // Course 0 depends on course 1 (Cycle)
        };

        Solution solution = new Solution();
        boolean canFinishCourses1 = solution.canFinish(numCourses1, prerequisites1);
        System.out.println("Can finish all courses for Test Case 1? " + canFinishCourses1);  // Expected: false

        // Test case 2: 4 courses without a cycle
        int numCourses2 = 4;
        int[][] prerequisites2 = {
            {1, 0},  // Course 1 depends on course 0
            {2, 1},  // Course 2 depends on course 1
            {3, 2}   // Course 3 depends on course 2 (No Cycle)
        };

        boolean canFinishCourses2 = solution.canFinish(numCourses2, prerequisites2);
        System.out.println("Can finish all courses for Test Case 2? " + canFinishCourses2);  // Expected: true
    }
}
```
