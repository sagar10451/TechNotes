# Question 11: N-Queens

**Question:** https://leetcode.com/problems/n-queens/description/

## Process Explanation (for the interview):

The N-Queens problem is solved using backtracking. We place queens row by row and recursively try all possible configurations. Here's the step-by-step process:

1. **Chessboard Representation:** We represent the board as a 2D matrix of characters, where each cell is either `.` (empty) or `Q` (queen).
2. **Backtracking:** We place queens one by one, starting from the first row. For each row, we try placing a queen in every column. After placing a queen in a column, we check if the position is valid (i.e., no queens in the same column or diagonals).
3. **Validation:** We check if a queen can be placed at a given position by ensuring that no other queens threaten it from the previous rows. If placing a queen leads to a valid configuration, we move to the next row.
4. **Backtrack:** If placing a queen at any position leads to a conflict later, we backtrack by removing the queen and trying the next possible column.
5. **Base Case:** When all rows have been processed, and queens are placed successfully, the configuration is added to the result list.

### Time and Space Complexity:

**Time Complexity:**
- Each recursive call processes a row, and for each row, we try placing queens in n possible columns. In the worst case, we explore all n! possible board configurations, leading to a time complexity of O(n!).

**Space Complexity:**
- We use an n x n board to store the queen positions, which takes O(n^2) space.
- Additionally, the recursive call stack can go up to n levels deep, so the overall space complexity is O(n^2).

### DRY RUN of the Code:

Let's take n = 4 (4-Queens problem) for a dry run.

**Initial State:**
- Matrix: `[['.', '.', '.', '.'], ['.', '.', '.', '.'], ['.', '.', '.', '.'], ['.', '.', '.', '.']]`
- Start with the first row, row = 0.

**First Row (row = 0):**
- Try placing a queen at (0, 0).
- Matrix: `[['Q', '.', '.', '.'], ['.', '.', '.', '.'], ['.', '.', '.', '.'], ['.', '.', '.', '.']]`
- Proceed to the next row.

**Second Row (row = 1):**
- Try placing a queen at (1, 0): Invalid (same column as (0, 0)).
- Try (1, 1): Invalid (same diagonal as (0, 0)).
- Try (1, 2): Valid.
- Matrix: `[['Q', '.', '.', '.'], ['.', '.', 'Q', '.'], ['.', '.', '.', '.'], ['.', '.', '.', '.']]`
- Proceed to the next row.

**Third Row (row = 2):**
- Try all columns; only (2, 3) is valid.
- Matrix: `[['Q', '.', '.', '.'], ['.', '.', 'Q', '.'], ['.', '.', '.', 'Q'], ['.', '.', '.', '.']]`
- Proceed to the next row.

**Fourth Row (row = 3):**
- Try (3, 0): Invalid.
- Try (3, 1): Valid.
- Matrix: `[['Q', '.', '.', '.'], ['.', '.', 'Q', '.'], ['.', '.', '.', 'Q'], ['.', 'Q', '.', '.']]`
- Found a valid solution! Add to result.
- Backtrack and explore other configurations.

### Code:

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    // Method to solve the N-Queens problem
    public static List<List<String>> solveNQueens(int n) {
        List<List<String>> result = new ArrayList<>(); // Stores all valid N-Queens board configurations

        // If n is less than or equal to 0, return an empty result
        if (n <= 0) {
            return result;
        }

        // Create an empty n x n chessboard filled with '.'
        char[][] matrix = new char[n][n];
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                matrix[i][j] = '.';
            }
        }

        // Start the recursive backtracking process
        nQueensDFSHelper(matrix, 0, result);

        return result;
    }

    // Recursive method to place queens row by row
    private static void nQueensDFSHelper(char[][] matrix, int row, List<List<String>> result) {
        // Base case: if all rows have queens, add the board configuration to the result
        if (row == matrix.length) {
            List<String> possibleResult = resultBuilder(matrix);
            result.add(possibleResult);
            return;
        }

        // Try placing a queen in each column of the current row
        for (int col = 0; col < matrix.length; col++) {
            matrix[row][col] = 'Q'; // Place a queen in the current position

            // If the position is valid, recurse to place queens in the next row
            if (isValidPosition(matrix, row, col)) {
                nQueensDFSHelper(matrix, row + 1, result); // Recurse to the next row
            }

            matrix[row][col] = '.'; // Backtrack (remove the queen from the current position)
        }
    }

    // Method to check if the current queen position is valid
    private static boolean isValidPosition(char[][] matrix, int x, int y) {
        // Check all previous rows to ensure no conflicts
        for (int row = 0; row < x; row++) {
            for (int col = 0; col < matrix.length; col++) {
                // Check if there's a queen in the same column or diagonals
                if ((y == col || Math.abs(x - row) == Math.abs(y - col)) && matrix[row][col] == 'Q') {
                    return false;
                }
            }
        }
        return true; // Position is valid if no conflicts found
    }

    // Helper method to convert the board matrix into a list of strings
    private static List<String> resultBuilder(char[][] matrix) {
        List<String> list = new ArrayList<>();
        for (int i = 0; i < matrix.length; i++) {
            list.add(new String(matrix[i])); // Add each row as a string to the list
        }
        return list;
    }

    // Main method to test the solution
    public static void main(String[] args) {
        Solution solution = new Solution();
        int n = 4; // Test case: Solve 4-Queens problem

        // Get the list of valid board configurations
        List<List<String>> result = solution.solveNQueens(n);

        // Print all solutions
        for (List<String> board : result) {
            for (String row : board) {
                System.out.println(row);
            }
            System.out.println();
        }
    }
}
```
