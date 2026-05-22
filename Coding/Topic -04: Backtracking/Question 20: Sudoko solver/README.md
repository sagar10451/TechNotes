# Question 20: Sudoku Solver

**Question:** https://leetcode.com/problems/sudoku-solver/

## Detailed Process Explanation:

**Objective:** Solve a given incomplete 9x9 Sudoku board by filling in digits from 1 to 9 so that each row, column, and 3x3 subgrid contains every digit exactly once.

**Approach:**
- Start solving the board from the top-left cell (0,0) and traverse row by row.
- Use backtracking to fill empty cells. If placing a number leads to an invalid board, revert the changes (backtrack) and try another number.
- For each cell, check if a number can be placed without violating Sudoku rules: no duplicate numbers in the current row, column, or 3x3 subgrid.
- If a valid number is found for a cell, move to the next cell. If a solution for the entire board is found, return true.
- If a number doesn't work, backtrack by resetting the cell to '.' and continue trying other numbers.
- Stop when the entire board is filled.

### Time and Space Complexity:

**Time Complexity:**
- In the worst case, we try all numbers (1-9) for each cell. There are 81 cells in total, so the worst case is O(9^81). However, this is an upper bound as the backtracking prunes invalid branches.
- Realistically, the complexity is much lower due to the constraints of Sudoku.

**Space Complexity:**
- The space complexity is O(1) as we modify the board in place.
- The recursion depth is at most 81 (number of cells), so the stack space is O(81), which is effectively O(1).

### DRY RUN:

```
[ ['5', '3', '.', '.', '7', '.', '.', '.', '.'],
  ['6', '.', '.', '1', '9', '5', '.', '.', '.'],
  ['.', '9', '8', '.', '.', '.', '.', '6', '.'],
  ['8', '.', '.', '.', '6', '.', '.', '.', '3'],
  ['4', '.', '.', '8', '.', '3', '.', '.', '1'],
  ['7', '.', '.', '.', '2', '.', '.', '.', '6'],
  ['.', '6', '.', '.', '.', '.', '2', '8', '.'],
  ['.', '.', '.', '4', '1', '9', '.', '.', '5'],
  ['.', '.', '.', '.', '8', '.', '.', '7', '9'] ]
```

**Step-by-Step Dry Run:**
- Start from (0,0), move to (0,2). Place 1-9, but only '4' is valid.
- Continue to (0,3). Try 1-9, but only '6' is valid.
- Proceed row by row, placing numbers where valid, backtrack if needed.
- Eventually fill in all valid numbers, resulting in a solved Sudoku.

### Code:

```java
import java.util.Arrays;

class Solution {
    // Main method to start solving the Sudoku board
    public void solveSudoku(char[][] board) {
        solve(board, 0, 0); // Start solving from the first cell
    }

    // Recursive method to fill the board cell by cell
    private boolean solve(char[][] board, int row, int col) {
        // Base case: If we've reached beyond the last row, the board is solved
        if (row == board.length) {
            return true;
        }

        // Move to the next row when we finish a column
        if (col == board[0].length) {
            return solve(board, row + 1, 0);
        }

        // If the current cell is already filled, skip it
        if (board[row][col] != '.') {
            return solve(board, row, col + 1);
        }

        // Try placing digits 1 to 9 in the current cell
        for (char num = '1'; num <= '9'; num++) {
            // Check if the number can be placed in this cell
            if (isValidPlacement(board, row, col, num)) {
                board[row][col] = num; // Place the number

                // Recursively solve for the next cell
                if (solve(board, row, col + 1)) {
                    return true;
                }

                // If no solution found, backtrack by resetting the cell
                board[row][col] = '.';
            }
        }

        // Return false if no valid number can be placed in this cell
        return false;
    }

    // Method to check if placing 'num' at board[row][col] is valid
    private boolean isValidPlacement(char[][] board, int row, int col, char num) {
        for (int i = 0; i < board.length; i++) {
            // Check if 'num' exists in the current row or column
            if (board[i][col] == num || board[row][i] == num) {
                return false;
            }

            // Check the 3x3 subgrid containing the cell (row, col)
            int subgridRow = 3 * (row / 3) + i / 3;
            int subgridCol = 3 * (col / 3) + i % 3;
            if (board[subgridRow][subgridCol] == num) {
                return false;
            }
        }
        return true; // Placement is valid
    }

    // Main method to run the sample test case
    public static void main(String[] args) {
        Solution solution = new Solution();

        char[][] board = {
            {'5', '3', '.', '.', '7', '.', '.', '.', '.'},
            {'6', '.', '.', '1', '9', '5', '.', '.', '.'},
            {'.', '9', '8', '.', '.', '.', '.', '6', '.'},
            {'8', '.', '.', '.', '6', '.', '.', '.', '3'},
            {'4', '.', '.', '8', '.', '3', '.', '.', '1'},
            {'7', '.', '.', '.', '2', '.', '.', '.', '6'},
            {'.', '6', '.', '.', '.', '.', '2', '8', '.'},
            {'.', '.', '.', '4', '1', '9', '.', '.', '5'},
            {'.', '.', '.', '.', '8', '.', '.', '7', '9'}
        };

        // Solve the Sudoku
        solution.solveSudoku(board);

        // Print the solved Sudoku board
        for (char[] row : board) {
            System.out.println(Arrays.toString(row));
        }
    }
}
```
