# Set Matrix Zeroes

**Problem:** Given an `m x n` matrix, if an element is `0`, set its entire row and column to `0`. Do it **in-place**.

**Example:**
```
Input:          Output:
1  1  1         1  0  1
1  0  1    →    0  0  0
1  1  1         1  0  1
```

---

## Solution 1: Brute Force — Extra Arrays

**Approach:** Use two boolean arrays to track which rows and columns contain a zero. Then zero out those rows and columns.

- **Time:** O(m × n)
- **Space:** O(m + n)

```java
class Solution {
    public void setZeroes(int[][] matrix) {
        int m = matrix.length, n = matrix[0].length;
        boolean[] zeroRows = new boolean[m];
        boolean[] zeroCols = new boolean[n];

        // Mark rows and columns that contain zero
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] == 0) {
                    zeroRows[i] = true;
                    zeroCols[j] = true;
                }
            }
        }

        // Zero out marked rows
        for (int i = 0; i < m; i++) {
            if (zeroRows[i]) {
                for (int j = 0; j < n; j++) matrix[i][j] = 0;
            }
        }

        // Zero out marked columns
        for (int j = 0; j < n; j++) {
            if (zeroCols[j]) {
                for (int i = 0; i < m; i++) matrix[i][j] = 0;
            }
        }
    }
}
```

**Dry Run** with `[[1,1,1],[1,0,1],[1,1,1]]`:

| Step | Action | zeroRows | zeroCols |
|------|--------|----------|----------|
| Scan | Found 0 at (1,1) | [F, T, F] | [F, T, F] |
| Zero rows | Row 1 → all zeros | — | — |
| Zero cols | Col 1 → all zeros | — | — |

```
Result:
1  0  1
0  0  0
1  0  1  ✅
```

---

## Solution 2: Optimal — Use First Row/Column as Markers

**Approach:** Instead of extra arrays, use the first row and first column of the matrix itself to store the markers. Two extra variables (`x`, `y`) track whether the first row/column themselves need to be zeroed.

**Steps:**
1. Check if first row has any zero → save in `x`
2. Check if first column has any zero → save in `y`
3. Scan rest of matrix (from [1][1]). If zero found, mark `matrix[i][0] = 0` and `matrix[0][j] = 0`
4. Use marks in first row to zero out columns
5. Use marks in first column to zero out rows
6. Finally, zero out first row/column if `x`/`y` say so

- **Time:** O(m × n)
- **Space:** O(1)

```java
class Solution {
    public void setZeroes(int[][] matrix) {
        int m = matrix.length, n = matrix[0].length;
        int x = 1; // does first row need zeroing?
        int y = 1; // does first column need zeroing?

        // Step 1: Check first row
        for (int j = 0; j < n; j++) {
            if (matrix[0][j] == 0) { x = 0; break; }
        }

        // Step 2: Check first column
        for (int i = 0; i < m; i++) {
            if (matrix[i][0] == 0) { y = 0; break; }
        }

        // Step 3: Mark zeros using first row/column
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (matrix[i][j] == 0) {
                    matrix[i][0] = 0; // mark row
                    matrix[0][j] = 0; // mark column
                }
            }
        }

        // Step 4: Zero columns based on first row marks
        for (int j = 1; j < n; j++) {
            if (matrix[0][j] == 0) {
                for (int i = 1; i < m; i++) matrix[i][j] = 0;
            }
        }

        // Step 5: Zero rows based on first column marks
        for (int i = 1; i < m; i++) {
            if (matrix[i][0] == 0) {
                for (int j = 0; j < n; j++) matrix[i][j] = 0;
            }
        }

        // Step 6: Zero first row/column if needed
        if (x == 0) {
            for (int j = 0; j < n; j++) matrix[0][j] = 0;
        }
        if (y == 0) {
            for (int i = 0; i < m; i++) matrix[i][0] = 0;
        }
    }
}
```

**Dry Run** with `[[0,1,2,0],[3,4,5,2],[1,3,1,5]]`:

```
Original:
0  1  2  0
3  4  5  2
1  3  1  5
```

| Step | Action | State |
|------|--------|-------|
| 1 | First row has 0 → x = 0 | x = 0 |
| 2 | First col has 0 → y = 0 | y = 0 |
| 3 | Scan [1][1]–[2][3], no zeros found | No new marks |
| 4 | Check first row marks: matrix[0][1]=1, [0][2]=2, [0][3]=0 → zero col 3 | Col 3 zeroed |
| 5 | Check first col marks: matrix[1][0]=3, [2][0]=1 → no rows marked | — |
| 6 | x=0 → zero first row, y=0 → zero first col | Done |

```
Result:
0  0  0  0
0  4  5  0
0  3  1  0  ✅
```

---

## Summary

| Solution | Time | Space | Notes |
|----------|------|-------|-------|
| Extra Arrays | O(m × n) | O(m + n) | Simple, two boolean arrays |
| First Row/Col as Markers | O(m × n) | O(1) | Best — no extra space |

**Interview tip:** Start with Solution 1 — it's clean and shows you understand the problem. Then say "I can eliminate the extra arrays by using the first row and column as markers — I just need two variables to remember whether the first row/column themselves had zeros." The tricky part is the order: zero out inner cells first, then first row/column last. Explain that clearly and you're golden.
