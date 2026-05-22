# Question 16: Rat in a Maze

**Question:** https://www.geeksforgeeks.org/problems/rat-in-a-maze-problem/1

## Detailed Explanation (for Interview):

**Problem:** You are given a matrix mat where 1 represents a path and 0 represents a blocked cell. You need to find all paths from the top-left corner to the bottom-right corner of the matrix, moving only in four directions: Down (D), Right (R), Up (U), and Left (L).

**Recursive Function (solve):**
- We start from the top-left corner and try to move in all possible directions (Down, Right, Up, Left).
- For each move, we check:
  - If the current cell is out of bounds.
  - If the cell is already visited.
  - If the cell is blocked (value is 0).
- If we reach the bottom-right corner, we add the current path to the result.
- Backtracking is used to explore all paths by unmarking the current cell as visited before returning to the previous call.

**findPath Function:**
- We check if the start cell is blocked (mat[0][0] == 0). If so, return -1.
- We call the recursive solve function to explore all paths from (0,0).

### Time and Space Complexity:

**Time Complexity:**
- The time complexity is approximately O(4^(n*m)) because for each cell, we can explore up to 4 directions. However, backtracking ensures that we do not revisit the same path repeatedly, making it faster in practical scenarios.

**Space Complexity:**
- O(n*m) for the vis array and the recursion stack used for backtracking.

### DRY RUN of Code:

```
{ {1, 0, 0, 0},
  {1, 1, 0, 1},
  {0, 1, 0, 0},
  {1, 1, 1, 1} }
```

- Start at (0,0). It's 1, so we proceed. Possible moves: Down.
- Move to (1,0). It's 1. Possible moves: Down, Right.
- Move to (1,1). It's 1. Possible moves: Down, Right.
- Move to (2,1). It's 1. Possible moves: Down.
- Move to (3,1). It's 1. Possible moves: Down, Right.
- Move to (3,2). It's 1. Possible moves: Right.
- Move to (3,3). It's the destination. We add "DRDDRR" to the result.
- We backtrack and explore any other possible paths. In this case, this is the only valid path.

### Code:

```java
import java.util.ArrayList;

class Solution {
    ArrayList<String> ans = new ArrayList<>();  // List to store all the possible paths.

    // Recursive function to explore all possible paths in the maze.
    void solve(int i, int j, int n, int m, String s, int[][] vis, int[][] mat) {
        // Base Case: If the current cell is out of bounds, visited, or blocked (value = 0), return.
        if(i < 0 || j < 0 || i >= n || j >= m || vis[i][j] == 1 || mat[i][j] == 0) {
            return;
        }

        // If we reach the destination cell, add the current path to the answer list.
        if(i == n-1 && j == m-1) {
            ans.add(s);
            return;
        }

        // Mark the current cell as visited.
        vis[i][j] = 1;

        // Explore all four possible directions (Down, Right, Up, Left).
        solve(i+1, j, n, m, s+'D', vis, mat); // Move Down
        solve(i, j+1, n, m, s+'R', vis, mat); // Move Right
        solve(i-1, j, n, m, s+'U', vis, mat); // Move Up
        solve(i, j-1, n, m, s+'L', vis, mat); // Move Left

        // Backtrack: Unmark the current cell as visited for other possible paths.
        vis[i][j] = 0;
    }

    // Function to find all the paths in the maze.
    public ArrayList<String> findPath(int[][] mat) {
        int n = mat.length;    // Number of rows
        int m = mat[0].length; // Number of columns

        // If the starting cell is blocked, no path is possible.
        if(mat[0][0] == 0) {
            ArrayList<String> result = new ArrayList<>();
            result.add("-1");
            return result;
        }

        int[][] vis = new int[n][m];  // To track visited cells.
        solve(0, 0, n, m, "", vis, mat);  // Start from the top-left corner (0,0).

        // If no path is found, add "-1" to indicate no possible path.
        if (ans.isEmpty()) {
            ans.add("-1");
        }

        return ans;
    }
}

// Main method to test the solution.
public class Main {
    public static void main(String[] args) {
        Solution solution = new Solution();

        int[][] maze = {
            {1, 0, 0, 0},
            {1, 1, 0, 1},
            {0, 1, 0, 0},
            {1, 1, 1, 1}
        };

        // Get all possible paths from the maze.
        ArrayList<String> paths = solution.findPath(maze);

        // Print the result.
        for (String path : paths) {
            System.out.println(path);
        }
    }
}
```
