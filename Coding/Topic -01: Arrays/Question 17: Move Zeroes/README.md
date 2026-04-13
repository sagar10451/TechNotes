# LeetCode 283 — Move Zeroes

## Problem

Move all `0`s to the **end** of the array while maintaining the **relative order** of non-zero elements. Must be done **in-place**.

**Example:**
`[0, 1, 0, 3, 12]` → `[1, 3, 12, 0, 0]`

---

## 1. Count Variable (Two Pass)

**Approach:**
1. First pass: copy all non-zero elements to the front, tracking position with `count`.
2. Second pass: fill remaining positions with `0`.

| Time | Space |
|------|-------|
| O(n) | O(1) |

```java
import java.util.Arrays;

class Solution {
    public void moveZeroes(int[] nums) {
        int count = 0;

        // Move non-zeros to front
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != 0) {
                nums[count++] = nums[i];
            }
        }

        // Fill rest with zeros
        for (int i = count; i < nums.length; i++) {
            nums[i] = 0;
        }
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        int[] nums = {0, 1, 0, 3, 12};
        s.moveZeroes(nums);
        System.out.println(Arrays.toString(nums)); // [1, 3, 12, 0, 0]
    }
}
```

---

## 2. ✅ Optimal — Two Pointers (Single Pass)

**Approach:**
1. `left` points to where the next non-zero should go.
2. `right` scans through the array.
3. When `right` finds a non-zero → **swap** with `left`, then `left++`.
4. Zeros naturally bubble to the end.

| Time | Space |
|------|-------|
| **O(n)** | **O(1)** |

> Single pass, in-place, maintains relative order.

```java
import java.util.Arrays;

public class Solution {
    public void moveZeroes(int[] nums) {
        int left = 0;

        for (int right = 0; right < nums.length; right++) {
            if (nums[right] != 0) {
                int temp = nums[left];
                nums[left] = nums[right];
                nums[right] = temp;
                left++;
            }
        }
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        int[] nums = {0, 1, 0, 3, 12};
        s.moveZeroes(nums);
        System.out.println(Arrays.toString(nums)); // [1, 3, 12, 0, 0]
    }
}
```

### Dry Run (`nums = [0, 1, 0, 3, 12]`)

| right | nums[right] | Non-zero? | Swap with left | left | Array |
|-------|-------------|-----------|----------------|------|-------|
| 0 | 0 | ❌ | — | 0 | [0, 1, 0, 3, 12] |
| 1 | 1 | ✅ | swap(0,1) | 1 | [1, 0, 0, 3, 12] |
| 2 | 0 | ❌ | — | 1 | [1, 0, 0, 3, 12] |
| 3 | 3 | ✅ | swap(1,3) | 2 | [1, 3, 0, 0, 12] |
| 4 | 12 | ✅ | swap(2,4) | 3 | [1, 3, 12, 0, 0] |

Result: `[1, 3, 12, 0, 0]` ✅

---

## Quick Revision Summary

| # | Approach | Time | Space | Key Idea |
|---|----------|------|-------|----------|
| 1 | Count (Two Pass) | O(n) | O(1) | Copy non-zeros front, fill zeros at end |
| 2 | **Two Pointers** | **O(n)** | **O(1)** | **Swap non-zeros to left, zeros bubble right** |

> 🔑 **Key takeaway:** `left` always points to the first zero (or next available slot). When `right` finds a non-zero, swap it into place. One pass, done.
