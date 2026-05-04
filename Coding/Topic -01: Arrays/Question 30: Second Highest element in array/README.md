# Second Highest Element in Array

**Problem:** Given an integer array, find the second highest (second largest) element. If no valid second highest exists, return `Integer.MIN_VALUE`.

**Example:**
- Input: `[5, 12, 8, 22, 15]` → Output: `15`
- Input: `[3, 3, 3]` → Output: `Integer.MIN_VALUE` (all same, no second highest)

---

## Solution 1: Brute Force — Sort

**Approach:** Sort the array in descending order. The second distinct element is the answer.

- **Time:** O(n log n)
- **Space:** O(1) (in-place sort)

```java
import java.util.Arrays;

class Solution {
    public static int findSecondHighest(int[] arr) {
        if (arr == null || arr.length < 2) return Integer.MIN_VALUE;

        Arrays.sort(arr);

        int highest = arr[arr.length - 1];
        // Walk backwards to find first element different from highest
        for (int i = arr.length - 2; i >= 0; i--) {
            if (arr[i] != highest) {
                return arr[i];
            }
        }
        return Integer.MIN_VALUE; // all elements are same
    }
}
```

---

## Solution 2: Optimal — Single Pass

**Approach:** Track two variables: `highest` and `secondHighest`. In one pass:
- If current > highest → secondHighest becomes old highest, update highest
- Else if current > secondHighest and current ≠ highest → update secondHighest

- **Time:** O(n)
- **Space:** O(1)

```java
class Solution {
    public static int findSecondHighest(int[] arr) {
        if (arr == null || arr.length < 2) return Integer.MIN_VALUE;

        int highest = Integer.MIN_VALUE;
        int secondHighest = Integer.MIN_VALUE;

        for (int i = 0; i < arr.length; i++) {
            if (arr[i] > highest) {
                secondHighest = highest;
                highest = arr[i];
            } else if (arr[i] > secondHighest && arr[i] != highest) {
                secondHighest = arr[i];
            }
        }

        return secondHighest;
    }
}
```

**Dry Run** with `[5, 12, 8, 22, 15]`:

| i | arr[i] | Condition | highest | secondHighest |
|---|--------|-----------|---------|---------------|
| 0 | 5 | 5 > MIN → new highest | 5 | MIN_VALUE |
| 1 | 12 | 12 > 5 → new highest | 12 | 5 |
| 2 | 8 | 8 > 5 and 8 ≠ 12 → new second | 12 | 8 |
| 3 | 22 | 22 > 12 → new highest | 22 | 12 |
| 4 | 15 | 15 > 12 and 15 ≠ 22 → new second | 22 | 15 |

Result: `15` ✅

---

## Summary

| Solution | Time | Space | Notes |
|----------|------|-------|-------|
| Sort | O(n log n) | O(1) | Simple but modifies array |
| Single Pass | O(n) | O(1) | Best — one loop, two variables |

**Interview tip:** Mention sorting as the brute force, then immediately go to the single-pass solution. The key thing to explain clearly is the two cases: "if I find a new highest, the old highest becomes second highest. Otherwise, if the element beats second highest but isn't equal to highest, update second highest." That covers duplicates cleanly.
