# Question 10: Number of Islands

**Question:** 200. Number of Islands

## Approach-1: DFS with visited array

### Steps and Explanation:
1. **Initialization:** Initialize the visited array. Iterate through every cell in the grid.
2. **DFS Traversal:** When a land cell ('1') is found that hasn't been visited, DFS is initiated from that cell. The DFS marks the cell as visited and recursively explores its 4 adjacent cells.
3. **Count Islands:** For every new unvisited land cell found, the number of islands is incremented by 1.
4. **Termination:** The process continues until all cells have been visited.

### Time and Space Complexity:
- **Time Complexity:** O(m * n)
- **Space Complexity:** O(m * n) due to the visited array and recursion stack.

### Code:

```java
class Solution {
    // Main function to count the number of islands
    public int numIslands(char[][] grid) {
        int rows = grid.length;
        int cols = grid[0].length;

        boolean[][] visited = new boolean[rows][cols];
        int numOfIslands = 0;

        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (grid[i][j] == '1' && !visited[i][j]) {
                    dfs(grid, visited, i, j, rows, cols);
                    numOfIslands++;
                }
            }
        }

        return numOfIslands;
    }

    // Helper DFS function
    private void dfs(char[][] grid, boolean[][] visited, int x, int y, int rows, int cols) {
        if (x < 0 || x >= rows || y < 0 || y >= cols || grid[x][y] == '0' || visited[x][y]) {
            return;
        }

        visited[x][y] = true;

        dfs(grid, visited, x - 1, y, rows, cols); // up
        dfs(grid, visited, x + 1, y, rows, cols); // down
        dfs(grid, visited, x, y - 1, rows, cols); // left
        dfs(grid, visited, x, y + 1, rows, cols); // right
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        char[][] grid = {
            {'1', '1', '0', '0', '0'},
            {'1', '1', '0', '0', '0'},
            {'0', '0', '1', '0', '0'},
            {'0', '0', '0', '1', '1'}
        };

        int result = solution.numIslands(grid);
        System.out.println("Number of islands: " + result); // Expected: 3
    }
}
```

## Approach-2: DFS without visited array

**Key difference:** Instead of a separate visited array, we mark visited cells by changing '1' to '0' directly in the grid.

### Code:

```java
class Solution {
    public int numIslands(char[][] grid) {
        int rows = grid.length;
        int cols = grid[0].length;
        int numOfIslands = 0;

        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (grid[i][j] == '1') {
                    dfs(grid, i, j, rows, cols);
                    numOfIslands++;
                }
            }
        }

        return numOfIslands;
    }

    private void dfs(char[][] grid, int x, int y, int rows, int cols) {
        if (x < 0 || x >= rows || y < 0 || y >= cols || grid[x][y] != '1') {
            return;
        }

        grid[x][y] = '0'; // Mark as visited by changing to '0'

        dfs(grid, x - 1, y, rows, cols); // up
        dfs(grid, x + 1, y, rows, cols); // down
        dfs(grid, x, y - 1, rows, cols); // left
        dfs(grid, x, y + 1, rows, cols); // right
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        char[][] grid = {
            {'1', '1', '0', '0', '0'},
            {'1', '1', '0', '0', '0'},
            {'0', '0', '1', '0', '0'},
            {'0', '0', '0', '1', '1'}
        };

        int result = solution.numIslands(grid);
        System.out.println("Number of islands: " + result); // Expected: 3
    }
}
```

## Approach-3: BFS

### Code:

```java
import java.util.LinkedList;
import java.util.Queue;

class Solution {
    public int numIslands(char[][] grid) {
        int rows = grid.length;
        int cols = grid[0].length;
        int numOfIslands = 0;

        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (grid[i][j] == '1') {
                    bfs(grid, i, j, rows, cols);
                    numOfIslands++;
                }
            }
        }

        return numOfIslands;
    }

    private void bfs(char[][] grid, int x, int y, int rows, int cols) {
        int[][] directions = { {-1, 0}, {1, 0}, {0, -1}, {0, 1} };

        Queue<int[]> queue = new LinkedList<>();
        queue.offer(new int[]{x, y});
        grid[x][y] = '0'; // Mark as visited

        while (!queue.isEmpty()) {
            int[] current = queue.poll();
            int currX = current[0];
            int currY = current[1];

            for (int[] dir : directions) {
                int newX = currX + dir[0];
                int newY = currY + dir[1];

                if (newX >= 0 && newX < rows && newY >= 0 && newY < cols && grid[newX][newY] == '1') {
                    grid[newX][newY] = '0'; // Mark as visited
                    queue.offer(new int[]{newX, newY});
                }
            }
        }
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        char[][] grid = {
            {'1', '1', '0', '0', '0'},
            {'1', '1', '0', '0', '0'},
            {'0', '0', '1', '0', '0'},
            {'0', '0', '0', '1', '1'}
        };

        int result = solution.numIslands(grid);
        System.out.println("Number of islands: " + result); // Expected: 3
    }
}
```
