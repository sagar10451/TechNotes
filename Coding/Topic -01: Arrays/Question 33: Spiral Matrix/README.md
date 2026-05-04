# Spiral Matrix

**Problem:** Given an `m x n` matrix, return all elements in **spiral order** (clockwise from outside in).

**Example:**
```
Input:              Output: [1, 2, 3, 6, 9, 8, 7, 4, 5]
1  2  3
4  5  6             Spiral: → → → ↓ ↓ ← ← ↑ →
7  8  9
```

---

## Solution: Boundary Shrinking (Layer by Layer)

**Approach:** Maintain four boundaries: `top`, `bottom`, `left`, `right`. Traverse one full layer of the spiral (right → down → left → up), then shrink the boundaries inward. Repeat until all elements are visited.

**Key detail:** After traversing right and down, check if `top <= bottom` before going left, and `left <= right` before going up. This prevents double-counting in non-square matrices.

- **Time:** O(m × n) — every element visited once
- **Space:** O(1) — excluding the output list

```java
class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
        List<Integer> result = new ArrayList<>();
        if (matrix == null || matrix.length == 0) return result;

        int top = 0, bottom = matrix.length - 1;
        int left = 0, right = matrix[0].length - 1;

        while (top <= bottom && left <= right) {
            // → Traverse top row, left to right
            for (int j = left; j <= right; j++) {
                result.add(matrix[top][j]);
            }
            top++;

            // ↓ Traverse right column, top to bottom
            for (int i = top; i <= bottom; i++) {
                result.add(matrix[i][right]);
            }
            right--;

            // ← Traverse bottom row, right to left (if rows remain)
            if (top <= bottom) {
                for (int j = right; j >= left; j--) {
                    result.add(matrix[bottom][j]);
                }
                bottom--;
            }

            // ↑ Traverse left column, bottom to top (if columns remain)
            if (left <= right) {
                for (int i = bottom; i >= top; i--) {
                    result.add(matrix[i][left]);
                }
                left++;
            }
        }

        return result;
    }
}
```

**Dry Run** with 3×3 matrix:

```
1  2  3
4  5  6
7  8  9
```

**Layer 1** (top=0, bottom=2, left=0, right=2):

| Direction | Traversal | Elements Added | Boundary Update |
|-----------|-----------|---------------|-----------------|
| → Top row | [0][0→2] | 1, 2, 3 | top = 1 |
| ↓ Right col | [1→2][2] | 6, 9 | right = 1 |
| ← Bottom row | [2][1→0] | 8, 7 | bottom = 1 |
| ↑ Left col | [1][0] | 4 | left = 1 |

Result so far: `[1, 2, 3, 6, 9, 8, 7, 4]`

**Layer 2** (top=1, bottom=1, left=1, right=1):

| Direction | Traversal | Elements Added | Boundary Update |
|-----------|-----------|---------------|-----------------|
| → Top row | [1][1] | 5 | top = 2 |
| ↓ Right col | (top > bottom, skip) | — | — |

Result: `[1, 2, 3, 6, 9, 8, 7, 4, 5]` ✅

---

**Dry Run** with non-square 3×4 matrix (to show why boundary checks matter):

```
1   2   3   4
5   6   7   8
9  10  11  12
```

**Layer 1** (top=0, bottom=2, left=0, right=3):

| Direction | Elements Added | Boundary Update |
|-----------|---------------|-----------------|
| → | 1, 2, 3, 4 | top = 1 |
| ↓ | 8, 12 | right = 2 |
| ← | 11, 10, 9 | bottom = 1 |
| ↑ | 5 | left = 1 |

**Layer 2** (top=1, bottom=1, left=1, right=2):

| Direction | Elements Added | Boundary Update |
|-----------|---------------|-----------------|
| → | 6, 7 | top = 2 |
| (top > bottom, done) | — | — |

Result: `[1, 2, 3, 4, 8, 12, 11, 10, 9, 5, 6, 7]` ✅

---

## Why the `if` Checks Are Critical

Without `if (top <= bottom)` before going left and `if (left <= right)` before going up, you'd double-count elements in single-row or single-column remaining layers:

```
Example: single row remaining → going right covers it,
         going left would re-traverse the same row backwards.
```

---

## Summary

| Aspect | Detail |
|--------|--------|
| Time | O(m × n) |
| Space | O(1) excluding output |
| Pattern | Boundary shrinking: top++, right--, bottom--, left++ |
| Key detail | Check boundaries before ← and ↑ to avoid double-counting |

**Interview tip:** Draw the spiral on the whiteboard with arrows showing the four directions. Label the four boundaries. Walk through one full layer, then show how boundaries shrink. The interviewer will likely ask "what about non-square matrices?" — that's where the `if` checks come in. Having the non-square dry run ready is a strong signal.
