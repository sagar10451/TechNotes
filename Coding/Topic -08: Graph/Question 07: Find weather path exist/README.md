# Question 07: Find Whether Path Exists

**Question:** https://www.geeksforgeeks.org/problems/find-whether-path-exist5238/1

Given a grid of size n×n filled with 0, 1, 2, 3. Check whether there is a path possible from the source (value = 1) to the destination (value = 2). You can traverse up, down, right, and left. A cell with value 3 means it's a blank cell (can traverse). A cell with value 0 means it's a wall (cannot traverse).

## Approach-1: BFS

**Approach:**
1. Find the source cell (value = 1) in the grid.
2. Use BFS starting from the source.
3. Explore all 4 directions. A cell is traversable if it's 3 (blank) or 2 (destination).
4. If we reach a cell with value 2, return true.
5. If BFS completes without finding destination, return false.

- **Time:** O(n²) — visit each cell at most once
- **Space:** O(n²) — visited array + queue

```java
import java.util.*;

class Solution {
    // Function to find whether a path exists from the source to destination
    public boolean is_Possible(int[][] grid) {
        int n = grid.length;
        int srcX = -1, srcY = -1;

        // Step 1: Find the source cell (value = 1)
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {
                    srcX = i;
                    srcY = j;
                    break;
                }
            }
            if (srcX != -1) break;
        }

        // Step 2: BFS from source
        boolean[][] visited = new boolean[n][n];
        Queue<int[]> queue = new LinkedList<>();
        queue.offer(new int[]{srcX, srcY});
        visited[srcX][srcY] = true;

        int[][] directions = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};

        while (!queue.isEmpty()) {
            int[] current = queue.poll();
            int x = current[0], y = current[1];

            // If we reached the destination
            if (grid[x][y] == 2) {
                return true;
            }

            // Explore all 4 directions
            for (int[] dir : directions) {
                int newX = x + dir[0];
                int newY = y + dir[1];

                // Check bounds, not visited, and traversable (value != 0)
                if (newX >= 0 && newX < n && newY >= 0 && newY < n
                    && !visited[newX][newY] && grid[newX][newY] != 0) {
                    visited[newX][newY] = true;
                    queue.offer(new int[]{newX, newY});
                }
            }
        }

        return false; // No path found
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        int[][] grid = {
            {3, 0, 3, 0, 0},
            {3, 0, 0, 0, 3},
            {3, 3, 3, 3, 3},
            {0, 2, 3, 0, 0},
            {3, 0, 0, 1, 3}
        };

        System.out.println("Path exists: " + solution.is_Possible(grid)); // Expected: true
    }
}
```

## Approach-2: DFS

**Approach:** Same logic but using recursive DFS instead of BFS.

- **Time:** O(n²)
- **Space:** O(n²) — visited array + recursion stack

```java
import java.util.*;

class Solution {
    // Function to find whether a path exists from the source to destination
    public boolean is_Possible(int[][] grid) {
        int n = grid.length;

        // Find the source cell (value = 1)
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {
                    boolean[][] visited = new boolean[n][n];
                    return dfs(grid, visited, i, j, n);
                }
            }
        }
        return false;
    }

    // DFS helper function
    private boolean dfs(int[][] grid, boolean[][] visited, int x, int y, int n) {
        // Base cases: out of bounds, wall, or already visited
        if (x < 0 || x >= n || y < 0 || y >= n || grid[x][y] == 0 || visited[x][y]) {
            return false;
        }

        // If destination found
        if (grid[x][y] == 2) {
            return true;
        }

        // Mark as visited
        visited[x][y] = true;

        // Explore all 4 directions
        return dfs(grid, visited, x - 1, y, n) ||  // up
               dfs(grid, visited, x + 1, y, n) ||  // down
               dfs(grid, visited, x, y - 1, n) ||  // left
               dfs(grid, visited, x, y + 1, n);    // right
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        int[][] grid = {
            {3, 0, 3, 0, 0},
            {3, 0, 0, 0, 3},
            {3, 3, 3, 3, 3},
            {0, 2, 3, 0, 0},
            {3, 0, 0, 1, 3}
        };

        System.out.println("Path exists: " + solution.is_Possible(grid)); // Expected: true
    }
}
```
