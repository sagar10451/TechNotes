# Find Minimum in Rotated Sorted Array

**Problem:** Given a sorted array that has been rotated between 1 and n times, find the minimum element. All elements are unique.

**Example:**
- Input: `[3, 4, 5, 1, 2]` → Output: `1`
- Input: `[4, 5, 6, 7, 0, 1, 2]` → Output: `0`
- Input: `[11, 13, 15, 17]` → Output: `11` (not rotated)

---

## Solution 1: Brute Force — Linear Scan

**Approach:** Scan the entire array and track the minimum.

- **Time:** O(n)
- **Space:** O(1)

```java
class Solution {
    public int findMin(int[] nums) {
        int min = nums[0];
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] < min) min = nums[i];
        }
        return min;
    }
}
```

---

## Solution 2: Optimal — Binary Search

**Approach:** Compare `nums[mid]` with `nums[right]`:
- If `nums[mid] > nums[right]` → minimum is in the **right half** (the rotation point is there)
- Otherwise → minimum is in the **left half** (including mid)

**Key insight:** In a rotated sorted array, the minimum is at the "rotation point" — where the order breaks. By comparing mid with right, we can determine which half contains this break.

- **Time:** O(log n)
- **Space:** O(1)

```java
class Solution {
    public int findMin(int[] nums) {
        int left = 0, right = nums.length - 1;

        while (left < right) {
            int mid = left + (right - left) / 2;

            if (nums[mid] > nums[right]) {
                left = mid + 1;  // min is in right half
            } else {
                right = mid;     // min is in left half (could be mid)
            }
        }

        return nums[left];
    }
}
```

**Dry Run** with `[3, 4, 5, 1, 2]`:

| Step | left | right | mid | nums[mid] | nums[right] | Action |
|------|------|-------|-----|-----------|-------------|--------|
| 1 | 0 | 4 | 2 | 5 | 2 | 5 > 2, left = 3 |
| 2 | 3 | 4 | 3 | 1 | 2 | 1 ≤ 2, right = 3 |
| 3 | left == right (3) | Exit | | | | |

Result: `nums[3]` = **1** ✅

**Dry Run** with `[4, 5, 6, 7, 0, 1, 2]`:

| Step | left | right | mid | nums[mid] | nums[right] | Action |
|------|------|-------|-----|-----------|-------------|--------|
| 1 | 0 | 6 | 3 | 7 | 2 | 7 > 2, left = 4 |
| 2 | 4 | 6 | 5 | 1 | 2 | 1 ≤ 2, right = 5 |
| 3 | 4 | 5 | 4 | 0 | 1 | 0 ≤ 1, right = 4 |
| 4 | left == right (4) | Exit | | | | |

Result: `nums[4]` = **0** ✅

---

## Why Compare With `nums[right]` (Not `nums[left]`)?

Comparing with `nums[left]` doesn't work reliably:
```
[3, 4, 5, 1, 2] → nums[mid]=5, nums[left]=3
  5 > 3 → but this is true even in a non-rotated sorted array!
  We can't tell if the min is left or right.

Comparing with nums[right]:
  5 > 2 → the right side has the break point (min is there)
  This always correctly identifies which half has the minimum.
```

---

## Note on Loop Condition: `left < right` (not `left <= right`)

We use `left < right` because:
- We're searching for a **position**, not a specific value
- When `left == right`, we've converged on the answer
- Using `left <= right` would cause an infinite loop since we set `right = mid` (not `mid - 1`)

---

## Summary

| Solution | Time | Space | Notes |
|----------|------|-------|-------|
| Linear Scan | O(n) | O(1) | Doesn't use sorted/rotated property |
| Binary Search | O(log n) | O(1) | Best — compare mid with right |

**Interview tip:** Start with brute force (trivial). Then: "The array is sorted but rotated — the minimum is at the rotation point. I can binary search for it by comparing `nums[mid]` with `nums[right]`. If mid is greater, the break is on the right. Otherwise, the break is on the left or at mid itself." Two details to explain: (1) why compare with right not left, and (2) why `right = mid` not `mid - 1` (because mid could be the answer).
