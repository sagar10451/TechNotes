# Question 11: Rotten Oranges

**Question:** 994. Rotting Oranges

## Intuition:

The problem requires finding the minimum time for all fresh oranges to rot, given that rotting spreads to adjacent fresh oranges every minute. This is a classic multi-source BFS problem, where each initially rotten orange acts as a starting point for the spread.

## Approach-1: BFS

### Steps and Explanation:

1. **Initialization:** A Queue is created to perform BFS, and a variable `ans` is initialized to track the time taken.
2. **Adding Rotten Oranges to Queue:** The grid is traversed, and all initially rotten oranges (value 2) are added to the queue along with their initial time (0).
3. **BFS Process:** While the queue is not empty, for each rotten orange, the four possible directions are checked. If a fresh orange (value 1) is found, it is rotted (changed to 2), and its position along with the incremented time is added to the queue.
4. **Checking for Remaining Fresh Oranges:** After BFS completes, if any fresh oranges remain, return -1.
5. **Return Time Taken:** If all oranges can rot, return the total time.

### Time and Space Complexity:
- **Time:** O(m * n)
- **Space:** O(m * n)

### Dry Run:

Grid: `[[2,1,1],[1,1,0],[0,1,1]]`

- Queue starts with: [(0,0,0)]
- Minute 1: (0,1) and (1,0) rot → time=1
- Minute 2: (0,2) and (1,1) rot → time=2
- Minute 3: (2,1) rots → time=3
- Minute 4: (2,2) rots → time=4
- **Result: 4**

### Code:

```java
import java.util.*;

class Solution {
    public int orangesRotting(int[][] grid) {
        int rows = grid.length;
        int cols = grid[0].length;
        Queue<int[]> queue = new LinkedList<>();
        int ans = 0;

        // Add all initially rotten oranges to the queue
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (grid[i][j] == 2) {
                    queue.offer(new int[]{i, j, 0}); // row, col, time
                }
            }
        }

        int[][] directions = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};

        // BFS
        while (!queue.isEmpty()) {
            int[] current = queue.poll();
            int x = current[0], y = current[1], time = current[2];
            ans = Math.max(ans, time);

            for (int[] dir : directions) {
                int newX = x + dir[0];
                int newY = y + dir[1];

                if (newX >= 0 && newX < rows && newY >= 0 && newY < cols && grid[newX][newY] == 1) {
                    grid[newX][newY] = 2; // Rot the fresh orange
                    queue.offer(new int[]{newX, newY, time + 1});
                }
            }
        }

        // Check for remaining fresh oranges
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (grid[i][j] == 1) {
                    return -1; // Impossible to rot all oranges
                }
            }
        }

        return ans;
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        int[][] grid = {
            {2, 1, 1},
            {1, 1, 0},
            {0, 1, 1}
        };

        System.out.println("Time to rot all oranges: " + solution.orangesRotting(grid)); // Expected: 4
    }
}
```
