# Search in Rotated Sorted Array II (With Duplicates)

**Problem:** Same as "Search in Rotated Sorted Array" but the array **may contain duplicates**. Return `true` if target exists, `false` otherwise.

**Example:**
- Input: `nums = [2, 5, 6, 0, 0, 1, 2]`, `target = 0` → Output: `true`
- Input: `nums = [2, 5, 6, 0, 0, 1, 2]`, `target = 3` → Output: `false`

---

## Solution 1: Brute Force — Linear Scan

**Approach:** Check each element.

- **Time:** O(n)
- **Space:** O(1)

```java
class Solution {
    public boolean search(int[] nums, int target) {
        for (int num : nums) {
            if (num == target) return true;
        }
        return false;
    }
}
```

---

## Solution 2: Optimal — Modified Binary Search (Handle Duplicates)

**Approach:** Same logic as Search in Rotated Sorted Array I, but with one extra case: when `nums[left] == nums[mid] == nums[right]`, we can't determine which half is sorted. In this case, shrink both ends by one.

- **Time:** O(log n) average, O(n) worst case (e.g., `[1,1,1,1,1]`)
- **Space:** O(1)

```java
class Solution {
    public boolean search(int[] nums, int target) {
        int left = 0, right = nums.length - 1;

        while (left <= right) {
            int mid = left + (right - left) / 2;

            if (nums[mid] == target) return true;

            // Can't determine sorted half — shrink both ends
            if (nums[left] == nums[mid] && nums[mid] == nums[right]) {
                left++;
                right--;
            }
            // Left half is sorted
            else if (nums[left] <= nums[mid]) {
                if (target >= nums[left] && target < nums[mid]) {
                    right = mid - 1;
                } else {
                    left = mid + 1;
                }
            }
            // Right half is sorted
            else {
                if (target > nums[mid] && target <= nums[right]) {
                    left = mid + 1;
                } else {
                    right = mid - 1;
                }
            }
        }

        return false;
    }
}
```

**Dry Run** with `[2, 5, 6, 0, 0, 1, 2]`, `target = 0`:

| Step | left | right | mid | nums[mid] | Action |
|------|------|-------|-----|-----------|--------|
| 1 | 0 | 6 | 3 | 0 | Found! Return true |

Result: **true** ✅

**Dry Run** with `[1, 0, 1, 1, 1]`, `target = 0` (worst case with duplicates):

| Step | left | right | mid | nums[l], nums[m], nums[r] | Action |
|------|------|-------|-----|--------------------------|--------|
| 1 | 0 | 4 | 2 | 1, 1, 1 | All equal → left++, right-- |
| 2 | 1 | 3 | 2 | 0, 1, 1 | Right sorted, 0 ∉ (1,1] → right = 1 |
| 3 | 1 | 1 | 1 | 0 | Found! Return true |

Result: **true** ✅

---

## Difference from Search in Rotated Sorted Array I

| Aspect | Version I (No Duplicates) | Version II (With Duplicates) |
|--------|--------------------------|------------------------------|
| Return type | `int` (index) | `boolean` (exists?) |
| Duplicates | None | Allowed |
| Extra case | — | `nums[left] == nums[mid] == nums[right]` → shrink |
| Worst case | O(log n) always | O(n) when all duplicates |

The **only code difference** is this block:
```java
if (nums[left] == nums[mid] && nums[mid] == nums[right]) {
    left++;
    right--;
}
```

---

## Why Duplicates Break the Original Approach

```
[1, 0, 1, 1, 1]
 ↑        ↑   ↑
left     mid  right

nums[left] = 1, nums[mid] = 1, nums[right] = 1
Is left half sorted? nums[left] <= nums[mid] → 1 <= 1 → YES
But left half is [1, 0, 1] — NOT actually sorted!

Duplicates make it impossible to determine the sorted half.
Solution: shrink both ends (left++, right--) to eliminate ambiguity.
```

---

## Summary

| Solution | Time | Space | Notes |
|----------|------|-------|-------|
| Linear Scan | O(n) | O(1) | Simple |
| Modified Binary Search | O(log n) avg, O(n) worst | O(1) | One extra case for duplicates |

**Interview tip:** Start by saying "this is the same as version I, but duplicates create ambiguity." Explain the problem case: "when left, mid, and right are all equal, I can't tell which half is sorted — so I shrink both ends by one." Mention the worst case: "if all elements are the same, I degrade to O(n), which is unavoidable since I must check each element." That shows you understand the tradeoff.
