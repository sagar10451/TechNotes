# Question 13: Shortest Path in Binary Matrix

**Question:** 1091. Shortest Path in Binary Matrix

Given an n×n binary matrix, find the shortest path from top-left (0,0) to bottom-right (n-1,n-1). You can move in **8 directions** (including diagonals). Cells with value 0 are open, cells with value 1 are blocked. Return -1 if no path exists.

## Approach: BFS (8-directional)

**Key difference from other grid BFS:** We can move in 8 directions (including diagonals), not just 4.

### Steps:
1. If source or destination is blocked (value 1), return -1.
2. BFS from (0,0) with path length 1.
3. Explore 8 directions for each cell.
4. First time we reach (n-1, n-1) is the shortest path.

### Time and Space Complexity:
- **Time:** O(n²)
- **Space:** O(n²)

### Code:

```java
import java.util.*;

class Solution {
    public int shortestPathBinaryMatrix(int[][] grid) {
        int n = grid.length;

        // If source or destination is blocked
        if (grid[0][0] == 1 || grid[n - 1][n - 1] == 1) {
            return -1;
        }

        // If grid is 1x1
        if (n == 1) {
            return 1;
        }

        // 8 directions: up, down, left, right, and 4 diagonals
        int[][] directions = {
            {-1, 0}, {1, 0}, {0, -1}, {0, 1},
            {-1, -1}, {-1, 1}, {1, -1}, {1, 1}
        };

        // BFS
        Queue<int[]> queue = new LinkedList<>();
        queue.offer(new int[]{0, 0, 1}); // row, col, path length
        grid[0][0] = 1; // Mark as visited by setting to 1

        while (!queue.isEmpty()) {
            int[] current = queue.poll();
            int x = current[0], y = current[1], pathLen = current[2];

            // Explore all 8 directions
            for (int[] dir : directions) {
                int newX = x + dir[0];
                int newY = y + dir[1];

                // Check bounds and if cell is open (0)
                if (newX >= 0 && newX < n && newY >= 0 && newY < n && grid[newX][newY] == 0) {

                    // If destination reached
                    if (newX == n - 1 && newY == n - 1) {
                        return pathLen + 1;
                    }

                    grid[newX][newY] = 1; // Mark as visited
                    queue.offer(new int[]{newX, newY, pathLen + 1});
                }
            }
        }

        return -1; // No path found
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        int[][] grid = {
            {0, 0, 0},
            {1, 1, 0},
            {1, 1, 0}
        };

        System.out.println("Shortest path length: " + solution.shortestPathBinaryMatrix(grid)); // Expected: 4
    }
}
```

### Dry Run:

Grid:
```
0 0 0
1 1 0
1 1 0
```

- Start at (0,0), pathLen=1
- From (0,0): can go to (0,1) and (0,2) → pathLen=2
- From (0,1): can go to (0,2) already visited
- From (0,2): can go to (1,2) → pathLen=3
- From (1,2): can go to (2,2) → pathLen=4
- (2,2) is destination → **Return 4**

