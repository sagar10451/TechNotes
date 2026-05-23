# Question 12: Shortest Source to Destination Path

**Question:** https://www.geeksforgeeks.org/problems/shortest-source-to-destination-path3544/1

Given a 2D binary matrix (0 = blocked, 1 = open), find the shortest path from source (0,0) to destination (X,Y). You can move in 4 directions. Return -1 if no path exists.

## Approach: BFS (Shortest Path in Unweighted Grid)

**Why BFS?** BFS explores level by level, so the first time we reach the destination is guaranteed to be the shortest path.

### Steps:
1. If source or destination is blocked (value 0), return -1.
2. Use BFS starting from (0,0) with distance 0.
3. For each cell, explore 4 directions. If a neighbor is valid and unvisited, mark it visited and enqueue with distance+1.
4. If we reach (X,Y), return the distance.
5. If BFS completes without reaching destination, return -1.

### Time and Space Complexity:
- **Time:** O(N * M) — visit each cell at most once
- **Space:** O(N * M) — visited array + queue

### Code:

```java
import java.util.*;

class Solution {
    // Function to find the shortest path from (0,0) to (X,Y)
    int shortestDistance(int N, int M, int[][] grid, int X, int Y) {
        // If source or destination is blocked
        if (grid[0][0] == 0 || grid[X][Y] == 0) {
            return -1;
        }

        // If source is the destination
        if (X == 0 && Y == 0) {
            return 0;
        }

        // BFS
        boolean[][] visited = new boolean[N][M];
        Queue<int[]> queue = new LinkedList<>();
        queue.offer(new int[]{0, 0, 0}); // row, col, distance
        visited[0][0] = true;

        int[][] directions = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};

        while (!queue.isEmpty()) {
            int[] current = queue.poll();
            int x = current[0], y = current[1], dist = current[2];

            // Explore all 4 directions
            for (int[] dir : directions) {
                int newX = x + dir[0];
                int newY = y + dir[1];

                // Check bounds, not visited, and not blocked
                if (newX >= 0 && newX < N && newY >= 0 && newY < M
                    && !visited[newX][newY] && grid[newX][newY] == 1) {

                    // If destination reached
                    if (newX == X && newY == Y) {
                        return dist + 1;
                    }

                    visited[newX][newY] = true;
                    queue.offer(new int[]{newX, newY, dist + 1});
                }
            }
        }

        return -1; // No path found
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        int[][] grid = {
            {1, 1, 1, 1},
            {1, 1, 0, 1},
            {1, 1, 1, 1},
            {1, 1, 0, 0},
            {1, 0, 0, 1}
        };

        int N = 5, M = 4, X = 2, Y = 2;
        System.out.println("Shortest distance: " + solution.shortestDistance(N, M, grid, X, Y)); // Expected: 4
    }
}
```

### Dry Run:

Grid (5x4), source=(0,0), destination=(2,2):
```
1 1 1 1
1 1 0 1
1 1 1 1
1 1 0 0
1 0 0 1
```

- BFS from (0,0), dist=0
- Level 1: (0,1), (1,0) → dist=1
- Level 2: (0,2), (1,1), (2,0) → dist=2
- Level 3: (0,3), (2,1), (3,0) → dist=3
- Level 4: (2,2) reached! → **Return 4**

