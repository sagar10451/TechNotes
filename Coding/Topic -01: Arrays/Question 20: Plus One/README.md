# LeetCode 66 — Plus One

## Problem

Given a large integer represented as an array of digits (most significant first), **add 1** to it and return the resulting array. No leading zeros.

**Examples:**
- `[1, 2, 3]` → `[1, 2, 4]`
- `[9]` → `[1, 0]`
- `[9, 9, 9]` → `[1, 0, 0, 0]`

---

## Solution — Iterate from Right

**Approach:**
1. Start from the **last digit**.
2. If digit < 9 → increment it, **return immediately** (no carry).
3. If digit == 9 → set to 0 (carry over to next digit).
4. If loop finishes → all digits were 9 → create new array of size `n+1`, set first element to `1` (rest are 0 by default in Java).

| Time | Space |
|------|-------|
| O(n) | O(1) — or O(n) only when all 9s |

```java
import java.util.Arrays;

class Solution {
    public int[] plusOne(int[] digits) {
        int n = digits.length;

        for (int i = n - 1; i >= 0; i--) {
            if (digits[i] < 9) {
                digits[i]++;
                return digits; // no carry, done
            }
            digits[i] = 0; // carry over
        }

        // Only reaches here if all digits were 9 (e.g., 999 → 1000)
        int[] extra = new int[n + 1];
        extra[0] = 1; // rest are 0 by default
        return extra;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(Arrays.toString(s.plusOne(new int[]{1, 2, 3}))); // [1, 2, 4]
        System.out.println(Arrays.toString(s.plusOne(new int[]{9})));       // [1, 0]
        System.out.println(Arrays.toString(s.plusOne(new int[]{9, 9, 9}))); // [1, 0, 0, 0]
        System.out.println(Arrays.toString(s.plusOne(new int[]{1, 9, 9}))); // [2, 0, 0]
    }
}
```

### Dry Run (`digits = [1, 2, 9]`)

| i | digits[i] | < 9? | Action | Array |
|---|-----------|------|--------|-------|
| 2 | 9 | ❌ | set to 0 | [1, 2, 0] |
| 1 | 2 | ✅ | increment, return | [1, 3, 0] |

Result: `[1, 3, 0]` ✅

### Dry Run (`digits = [9, 9, 9]`)

| i | digits[i] | < 9? | Action | Array |
|---|-----------|------|--------|-------|
| 2 | 9 | ❌ | set to 0 | [9, 9, 0] |
| 1 | 9 | ❌ | set to 0 | [9, 0, 0] |
| 0 | 9 | ❌ | set to 0 | [0, 0, 0] |

Loop ends → all were 9 → create `[1, 0, 0, 0]` ✅

---

## Quick Revision Summary

| Time | Space | Key Idea |
|------|-------|----------|
| O(n) | O(1) | Iterate right to left. < 9 → increment and return. == 9 → set 0, carry. All 9s → new array with leading 1. |

> 🔑 **Key takeaway:** Early return when digit < 9 makes this very efficient — most cases return in O(1). The all-9s case is the only time you need a new array.
