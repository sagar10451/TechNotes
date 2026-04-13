# LeetCode 2022 — Convert 1D Array Into 2D Array

## Problem

Given a 1D array `original`, and two integers `m` (rows) and `n` (columns), convert it into a 2D array with `m` rows and `n` columns. Fill row by row from the original array. If `m * n != original.length`, return an empty 2D array.

**Example:**
`original = [1, 2, 3, 4, 5, 6]`, `m = 2`, `n = 3` → `[[1, 2, 3], [4, 5, 6]]`

---

## 1. Two Nested Loops

**Approach:**
1. Check if `m * n == original.length`. If not, return empty array.
2. Create `ans[m][n]`.
3. Use nested loops (row, col) and a running index `ind` to fill the 2D array.

| Time | Space |
|------|-------|
| O(m × n) | O(m × n) |

```java
import java.util.Arrays;

class Solution {
    public int[][] construct2DArray(int[] original, int m, int n) {
        int len = original.length;
        if (m * n != len) return new int[0][0];

        int ind = 0;
        int[][] ans = new int[m][n];

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                ans[i][j] = original[ind++];
            }
        }
        return ans;
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        int[] original = {1, 2, 3, 4, 5, 6};
        System.out.println(Arrays.deepToString(
            solution.construct2DArray(original, 2, 3)
        )); // [[1, 2, 3], [4, 5, 6]]
    }
}
```

### Dry Run (`original = [1,2,3,4,5,6]`, `m=2`, `n=3`)

| ind | i (row) | j (col) | ans[i][j] |
|-----|---------|---------|-----------|
| 0 | 0 | 0 | 1 |
| 1 | 0 | 1 | 2 |
| 2 | 0 | 2 | 3 |
| 3 | 1 | 0 | 4 |
| 4 | 1 | 1 | 5 |
| 5 | 1 | 2 | 6 |

Result: `[[1, 2, 3], [4, 5, 6]]` ✅

---

## 2. ✅ Single Loop (Cleaner)

**Approach:**
1. Same validation check.
2. Single loop over `original`. Use `i / n` for row and `i % n` for column.

| Time | Space |
|------|-------|
| O(m × n) | O(m × n) |

> Same complexity as above — just cleaner code using the **division/modulo trick** to map 1D index → 2D coordinates.

```java
import java.util.Arrays;

class Solution {
    public int[][] construct2DArray(int[] original, int m, int n) {
        int len = original.length;
        if (m * n != len) return new int[0][0];

        int[][] ans = new int[m][n];

        for (int i = 0; i < len; i++) {
            ans[i / n][i % n] = original[i];
        }
        return ans;
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        int[] original = {1, 2, 3, 4, 5, 6};
        System.out.println(Arrays.deepToString(
            solution.construct2DArray(original, 2, 3)
        )); // [[1, 2, 3], [4, 5, 6]]
    }
}
```

### Dry Run (`original = [1,2,3,4,5,6]`, `m=2`, `n=3`)

| i | i / 3 (row) | i % 3 (col) | ans[row][col] |
|---|-------------|-------------|---------------|
| 0 | 0 | 0 | 1 |
| 1 | 0 | 1 | 2 |
| 2 | 0 | 2 | 3 |
| 3 | 1 | 0 | 4 |
| 4 | 1 | 1 | 5 |
| 5 | 1 | 2 | 6 |

Result: `[[1, 2, 3], [4, 5, 6]]` ✅

---

## Quick Revision Summary

| # | Approach | Time | Space | Key Idea |
|---|----------|------|-------|----------|
| 1 | Nested Loops | O(m×n) | O(m×n) | Running index with row/col loops |
| 2 | **Single Loop** | **O(m×n)** | **O(m×n)** | **`i / n` = row, `i % n` = col** |

> 🔑 **Key takeaway:** `i / n` gives the row, `i % n` gives the column — a handy trick to convert any 1D index to 2D coordinates.
