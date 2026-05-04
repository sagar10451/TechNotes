# Rotate Image

**Problem:** Given an `n x n` 2D matrix, rotate it 90 degrees clockwise **in-place**.

**Example:**
```
Input:          Output:
1  2  3         7  4  1
4  5  6    →    8  5  2
7  8  9         9  6  3
```

---

## Solution 1: Brute Force — Extra Matrix

**Approach:** Create a new matrix. For each element `matrix[i][j]`, place it at `rotated[j][n-i-1]`. Copy back.

- **Time:** O(n²)
- **Space:** O(n²)

```java
class Solution {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        int[][] rotated = new int[n][n];

        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                rotated[j][n - i - 1] = matrix[i][j];
            }
        }

        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                matrix[i][j] = rotated[i][j];
            }
        }
    }
}
```

**Dry Run** with 3x3 matrix:

| i | j | matrix[i][j] | Goes to rotated[j][n-i-1] |
|---|---|-------------|--------------------------|
| 0 | 0 | 1 | rotated[0][2] = 1 |
| 0 | 1 | 2 | rotated[1][2] = 2 |
| 0 | 2 | 3 | rotated[2][2] = 3 |
| 1 | 0 | 4 | rotated[0][1] = 4 |
| 1 | 1 | 5 | rotated[1][1] = 5 |
| 1 | 2 | 6 | rotated[2][1] = 6 |
| 2 | 0 | 7 | rotated[0][0] = 7 |
| 2 | 1 | 8 | rotated[1][0] = 8 |
| 2 | 2 | 9 | rotated[2][0] = 9 |

Result: `[[7,4,1],[8,5,2],[9,6,3]]` ✅

---

## Solution 2: Four-Way Swap (In-Place)

**Approach:** Process the matrix layer by layer. For each position, rotate 4 elements in a cycle: top→right→bottom→left→top. Only need to iterate over top-left quarter.

- **Time:** O(n²)
- **Space:** O(1)

```java
class Solution {
    public void rotate(int[][] matrix) {
        int n = matrix.length;

        for (int i = 0; i < n / 2; i++) {
            for (int j = 0; j < (n + 1) / 2; j++) {
                int temp = matrix[i][j];
                matrix[i][j] = matrix[n - j - 1][i];
                matrix[n - j - 1][i] = matrix[n - i - 1][n - j - 1];
                matrix[n - i - 1][n - j - 1] = matrix[j][n - i - 1];
                matrix[j][n - i - 1] = temp;
            }
        }
    }
}
```

**Dry Run** with 3x3 matrix (`n/2 = 1`, `(n+1)/2 = 2`):

| i | j | 4-Way Swap | Matrix After |
|---|---|-----------|-------------|
| 0 | 0 | temp=1, [0][0]←7, [2][0]←9, [2][2]←3, [0][2]←1 | `[[7,2,1],[4,5,6],[9,8,3]]` |
| 0 | 1 | temp=2, [0][1]←4, [1][0]←8, [2][1]←6, [1][2]←2 | `[[7,4,1],[8,5,2],[9,6,3]]` |

Result: `[[7,4,1],[8,5,2],[9,6,3]]` ✅

---

## Solution 3: Transpose + Reverse Rows (Easiest to Explain)

**Approach:** Two simple steps:
1. **Transpose** the matrix (swap `matrix[i][j]` with `matrix[j][i]`)
2. **Reverse** each row

This achieves a 90° clockwise rotation. Easy to remember and explain.

- **Time:** O(n²)
- **Space:** O(1)

```java
class Solution {
    public void rotate(int[][] matrix) {
        int n = matrix.length;

        // Step 1: Transpose (swap across diagonal)
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                int temp = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = temp;
            }
        }

        // Step 2: Reverse each row
        for (int i = 0; i < n; i++) {
            int left = 0, right = n - 1;
            while (left < right) {
                int temp = matrix[i][left];
                matrix[i][left] = matrix[i][right];
                matrix[i][right] = temp;
                left++;
                right--;
            }
        }
    }
}
```

**Dry Run** with 3x3 matrix:

| Step | Operation | Matrix State |
|------|-----------|-------------|
| Start | — | `[[1,2,3],[4,5,6],[7,8,9]]` |
| Transpose | Swap (0,1)↔(1,0), (0,2)↔(2,0), (1,2)↔(2,1) | `[[1,4,7],[2,5,8],[3,6,9]]` |
| Reverse row 0 | [1,4,7] → [7,4,1] | `[[7,4,1],[2,5,8],[3,6,9]]` |
| Reverse row 1 | [2,5,8] → [8,5,2] | `[[7,4,1],[8,5,2],[3,6,9]]` |
| Reverse row 2 | [3,6,9] → [9,6,3] | `[[7,4,1],[8,5,2],[9,6,3]]` |

Result: `[[7,4,1],[8,5,2],[9,6,3]]` ✅

---

## Summary

| Solution | Time | Space | Notes |
|----------|------|-------|-------|
| Extra Matrix | O(n²) | O(n²) | Simple but uses extra space |
| Four-Way Swap | O(n²) | O(1) | In-place, but index math is tricky |
| Transpose + Reverse | O(n²) | O(1) | In-place, easiest to explain and remember |

**Interview tip:** Start with Solution 1 to show you understand the rotation mapping `[i][j] → [j][n-i-1]`. Then optimize to in-place. Solution 3 (transpose + reverse) is the easiest to explain on a whiteboard — two clear steps, no complex index math. Solution 2 (four-way swap) is impressive but harder to get right under pressure. Lead with Solution 3 for the optimal answer.
