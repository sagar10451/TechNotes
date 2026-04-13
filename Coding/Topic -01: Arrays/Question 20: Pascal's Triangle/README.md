# LeetCode 118 — Pascal's Triangle

## Problem

Given `numRows`, generate the first `numRows` of **Pascal's Triangle**. Each number is the sum of the two numbers directly above it. First and last elements of every row are always `1`.

**Example (`numRows = 5`):**
```
    [1]
   [1,1]
  [1,2,1]
 [1,3,3,1]
[1,4,6,4,1]
```

---

## Solution — Build Row by Row

**Approach:**
1. For each row `i`, create a list of size `i + 1` filled with `1`s.
2. For inner elements (`j = 1` to `i - 1`): `row[j] = prevRow[j] + prevRow[j-1]`.
3. Add the row to the result.

> This is already optimal — you must generate all elements, so O(numRows²) is the best possible.

| Time | Space |
|------|-------|
| O(numRows²) | O(numRows²) |

```java
import java.util.*;

public class Solution {
    public List<List<Integer>> generate(int numRows) {
        List<List<Integer>> ans = new ArrayList<>();

        for (int i = 0; i < numRows; i++) {
            List<Integer> v = new ArrayList<>();

            // Fill with 1s
            for (int j = 0; j <= i; j++) {
                v.add(1);
            }

            // Calculate inner values from previous row
            for (int j = 1; j < i; j++) {
                v.set(j, ans.get(i - 1).get(j) + ans.get(i - 1).get(j - 1));
            }

            ans.add(v);
        }
        return ans;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        List<List<Integer>> triangle = s.generate(5);
        for (List<Integer> row : triangle) {
            System.out.println(row);
        }
        // [1]
        // [1, 1]
        // [1, 2, 1]
        // [1, 3, 3, 1]
        // [1, 4, 6, 4, 1]
    }
}
```

### Dry Run (`numRows = 5`)

| Row (i) | Init | Inner Calculation | Result |
|---------|------|-------------------|--------|
| 0 | [1] | — | [1] |
| 1 | [1, 1] | — | [1, 1] |
| 2 | [1, 1, 1] | v[1] = 1+1 = 2 | [1, 2, 1] |
| 3 | [1, 1, 1, 1] | v[1] = 1+2 = 3, v[2] = 2+1 = 3 | [1, 3, 3, 1] |
| 4 | [1, 1, 1, 1, 1] | v[1] = 1+3 = 4, v[2] = 3+3 = 6, v[3] = 3+1 = 4 | [1, 4, 6, 4, 1] |

✅

---

## Quick Revision Summary

| Time | Space | Key Idea |
|------|-------|----------|
| O(numRows²) | O(numRows²) | Each element = sum of two elements above it |

> 🔑 **Key takeaway:** Row `i`, element `j` = `prevRow[j] + prevRow[j-1]`. First and last are always 1. Already optimal — must touch every element.
