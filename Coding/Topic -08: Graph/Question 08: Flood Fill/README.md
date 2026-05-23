# Question 08: Flood Fill

**Question:** 733. Flood Fill

## Approach-1: DFS without visited array

### Steps and Explanation:

1. **Initial Setup:** We take a 2D array representing the image and starting coordinates (sr, sc) along with the newColor. If the starting pixel's current color is already the same as the new color, we don't need to perform the flood fill.

2. **Depth-First Search (DFS):** We use a recursive DFS function to explore the entire connected region (4-directionally) that has the same original color.

3. **Base Condition:** If the pixel is out of bounds, or its color is not equal to the original color (oldColor), we stop the recursion.

4. **Termination:** Once all connected pixels are filled, the recursion stops and we return the modified image.

### Time and Space Complexity:
- **Time Complexity:** O(m * n), where m is the number of rows and n is the number of columns.
- **Space Complexity:** O(m * n) due to the recursion stack.

### Dry Run:

Given: image = [[1,1,1],[1,1,0],[1,0,1]], sr=1, sc=1, newColor=2

- Check image[1][1] = 1, since 1 != 2, proceed with DFS.
- Change image[1][1] to 2, explore neighbors.
- Up (0,1)→change to 2. Down (2,1)→0, skip. Left (1,0)→change to 2. Right (1,2)→0, skip.
- Continue recursion from (0,1) and (1,0), changing connected 1s to 2.
- Final: [[2,2,2],[2,2,0],[2,0,1]]

### Code:

```java
class Solution {
    // Main function to initiate the flood fill process
    public int[][] floodFill(int[][] image, int sr, int sc, int newColor) {
        int oldColor = image[sr][sc]; // Store the original color at the starting point (sr, sc)

        // If the old color is the same as newColor, no need to do anything
        if (oldColor != newColor) {
            dfs(image, sr, sc, oldColor, newColor); // Perform DFS to change connected pixels
        }

        return image; // Return the modified image
    }

    // Helper DFS function to perform the flood fill
    private void dfs(int[][] image, int x, int y, int oldColor, int newColor) {
        int rows = image.length;  // Get the number of rows in the image
        int cols = image[0].length; // Get the number of columns in the image

        // Base case: if the pixel is out of bounds or not equal to oldColor, stop the recursion
        if (x < 0 || x >= rows || y < 0 || y >= cols || image[x][y] != oldColor) {
            return;
        }

        // Change the current pixel's color to the new color
        image[x][y] = newColor;

        // Recursively call DFS in all 4 directions: up, down, left, and right
        dfs(image, x - 1, y, oldColor, newColor); // up
        dfs(image, x + 1, y, oldColor, newColor); // down
        dfs(image, x, y - 1, oldColor, newColor); // left
        dfs(image, x, y + 1, oldColor, newColor); // right
    }

    // Main method to test the floodFill function
    public static void main(String[] args) {
        Solution solution = new Solution();

        int[][] image = {
            {1, 1, 1},
            {1, 1, 0},
            {1, 0, 1}
        };
        int sr = 1, sc = 1, newColor = 2;

        int[][] result = solution.floodFill(image, sr, sc, newColor);

        for (int[] row : result) {
            for (int pixel : row) {
                System.out.print(pixel + " ");
            }
            System.out.println();
        }
    }
}
```

## Approach-2: BFS

### Time and Space Complexity:
- **Time Complexity:** O(m * n)
- **Space Complexity:** O(m * n) for the queue

### Code:

```java
import java.util.LinkedList;
import java.util.Queue;

class Solution {
    // Main function to perform the flood fill using BFS
    public int[][] floodFill(int[][] image, int sr, int sc, int newColor) {
        int oldColor = image[sr][sc];

        if (oldColor == newColor) {
            return image;
        }

        bfs(image, sr, sc, oldColor, newColor);
        return image;
    }

    // BFS Helper function
    private void bfs(int[][] image, int x, int y, int oldColor, int newColor) {
        int rows = image.length;
        int cols = image[0].length;

        int[][] directions = { {-1, 0}, {1, 0}, {0, -1}, {0, 1} };

        Queue<int[]> queue = new LinkedList<>();
        queue.offer(new int[]{x, y});
        image[x][y] = newColor;

        while (!queue.isEmpty()) {
            int[] current = queue.poll();
            int currX = current[0];
            int currY = current[1];

            for (int[] dir : directions) {
                int newX = currX + dir[0];
                int newY = currY + dir[1];

                if (newX >= 0 && newX < rows && newY >= 0 && newY < cols && image[newX][newY] == oldColor) {
                    image[newX][newY] = newColor;
                    queue.offer(new int[]{newX, newY});
                }
            }
        }
    }

    // Main method for testing the BFS flood fill
    public static void main(String[] args) {
        Solution solution = new Solution();

        int[][] image = {
            {1, 1, 1},
            {1, 1, 0},
            {1, 0, 1}
        };
        int sr = 1, sc = 1, newColor = 2;

        int[][] result = solution.floodFill(image, sr, sc, newColor);

        for (int[] row : result) {
            for (int pixel : row) {
                System.out.print(pixel + " ");
            }
            System.out.println();
        }
    }
}
```


