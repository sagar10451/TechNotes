# Rotate Array

**Problem:** Given an integer array `nums`, rotate it to the right by `k` steps in-place.

**Example:**
- Input: `nums = [1, 2, 3, 4, 5, 6, 7]`, `k = 3` → Output: `[5, 6, 7, 1, 2, 3, 4]`
- Input: `nums = [-1, -100, 3, 99]`, `k = 2` → Output: `[3, 99, -1, -100]`

---

## Solution 1: Brute Force — Extra Array

**Approach:** Create a new array. Place each element at its rotated position using `(i + k) % n`. Copy back to original.

- **Time:** O(n)
- **Space:** O(n)

```java
class Solution {
    public void rotate(int[] nums, int k) {
        int n = nums.length;
        k = k % n;
        int[] rotated = new int[n];

        for (int i = 0; i < n; i++) {
            rotated[(i + k) % n] = nums[i];
        }

        for (int i = 0; i < n; i++) {
            nums[i] = rotated[i];
        }
    }
}
```

**Dry Run** with `[1, 2, 3, 4, 5, 6, 7]`, `k = 3`:

| i | nums[i] | New Index (i+3)%7 | rotated |
|---|---------|-------------------|---------|
| 0 | 1 | 3 | [_, _, _, 1, _, _, _] |
| 1 | 2 | 4 | [_, _, _, 1, 2, _, _] |
| 2 | 3 | 5 | [_, _, _, 1, 2, 3, _] |
| 3 | 4 | 6 | [_, _, _, 1, 2, 3, 4] |
| 4 | 5 | 0 | [5, _, _, 1, 2, 3, 4] |
| 5 | 6 | 1 | [5, 6, _, 1, 2, 3, 4] |
| 6 | 7 | 2 | [5, 6, 7, 1, 2, 3, 4] |

Copy back → `nums = [5, 6, 7, 1, 2, 3, 4]` ✅

---

## Solution 2: Optimal — Three Reversals

**Approach:** Rotate in-place using three reversals:
1. Reverse the entire array
2. Reverse the first `k` elements
3. Reverse the remaining `n - k` elements

**Why it works:** Reversing the whole array puts the last `k` elements at the front but in wrong order. The two partial reversals fix the order within each half.

- **Time:** O(n)
- **Space:** O(1)

```java
class Solution {
    public void rotate(int[] nums, int k) {
        int n = nums.length;
        k = k % n;

        reverse(nums, 0, n - 1);     // reverse all
        reverse(nums, 0, k - 1);     // reverse first k
        reverse(nums, k, n - 1);     // reverse rest
    }

    private void reverse(int[] nums, int start, int end) {
        while (start < end) {
            int temp = nums[start];
            nums[start] = nums[end];
            nums[end] = temp;
            start++;
            end--;
        }
    }
}
```

**Dry Run** with `[1, 2, 3, 4, 5, 6, 7]`, `k = 3`:

| Step | Operation | Array State |
|------|-----------|-------------|
| Start | — | [1, 2, 3, 4, 5, 6, 7] |
| 1. Reverse all | reverse(0, 6) | [7, 6, 5, 4, 3, 2, 1] |
| 2. Reverse first 3 | reverse(0, 2) | [5, 6, 7, 4, 3, 2, 1] |
| 3. Reverse last 4 | reverse(3, 6) | [5, 6, 7, 1, 2, 3, 4] |

Result: `[5, 6, 7, 1, 2, 3, 4]` ✅

---

## Summary

| Solution | Time | Space | Notes |
|----------|------|-------|-------|
| Extra Array | O(n) | O(n) | Simple, easy to explain |
| Three Reversals | O(n) | O(1) | Best — in-place, no extra space |

**Interview tip:** Start with Solution 1 to show you understand the index math `(i + k) % n`. Then say "but this uses O(n) extra space — I can do it in O(1) with three reversals." Walk through the three-step visual: reverse all → reverse first k → reverse rest. It's a clean story.
