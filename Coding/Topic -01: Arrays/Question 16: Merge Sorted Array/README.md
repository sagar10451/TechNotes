# LeetCode 88 — Merge Sorted Array

## Problem

Given two sorted arrays `nums1` (size `m + n`, with last `n` slots empty) and `nums2` (size `n`), merge `nums2` into `nums1` in-place so that `nums1` is sorted.

**Example:**
`nums1 = [1, 2, 3, 0, 0, 0]`, `m = 3`, `nums2 = [2, 5, 6]`, `n = 3` → `[1, 2, 2, 3, 5, 6]`

---

## 1. Brute Force

**Approach:** Copy both arrays into a new array, sort it, copy back to `nums1`.

| Time | Space |
|------|-------|
| O((m+n) log(m+n)) | O(m+n) |

```java
import java.util.Arrays;

public class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int[] merged = new int[m + n];

        for (int i = 0; i < m; i++) merged[i] = nums1[i];
        for (int i = 0; i < n; i++) merged[m + i] = nums2[i];

        Arrays.sort(merged);

        for (int i = 0; i < m + n; i++) nums1[i] = merged[i];
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        int[] nums1 = {1, 2, 3, 0, 0, 0};
        s.merge(nums1, 3, new int[]{2, 5, 6}, 3);
        System.out.println(Arrays.toString(nums1)); // [1, 2, 2, 3, 5, 6]
    }
}
```

---

## 2. ✅ Optimal — Two Pointers (Merge from Back)

**Key Idea:** Start filling `nums1` from the **end** (position `m+n-1`). This avoids overwriting elements that haven't been processed yet.

**Approach:**
1. `i` = last initialized element in `nums1` (`m-1`)
2. `j` = last element in `nums2` (`n-1`)
3. `k` = last position in `nums1` (`m+n-1`)
4. Compare `nums1[i]` and `nums2[j]`, place the **larger** one at `nums1[k]`, move pointers.
5. Copy any remaining `nums2` elements.

| Time | Space |
|------|-------|
| **O(m+n)** | **O(1)** — in-place |

```java
import java.util.Arrays;

public class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int i = m - 1;     // last element in nums1
        int j = n - 1;     // last element in nums2
        int k = m + n - 1; // last position in nums1

        // Merge from back
        while (i >= 0 && j >= 0) {
            if (nums1[i] > nums2[j]) {
                nums1[k] = nums1[i];
                i--;
            } else {
                nums1[k] = nums2[j];
                j--;
            }
            k--;
        }

        // Copy remaining nums2 elements (if any)
        while (j >= 0) {
            nums1[k] = nums2[j];
            j--;
            k--;
        }
    }

    public static void main(String[] args) {
        Solution s = new Solution();

        int[] nums1 = {1, 2, 3, 0, 0, 0};
        s.merge(nums1, 3, new int[]{2, 5, 6}, 3);
        System.out.println(Arrays.toString(nums1)); // [1, 2, 2, 3, 5, 6]

        int[] nums3 = {1, 4, 0, 0};
        s.merge(nums3, 2, new int[]{2, 3}, 2);
        System.out.println(Arrays.toString(nums3)); // [1, 2, 3, 4]
    }
}
```

### Dry Run (`nums1 = [1,2,3,0,0,0]`, `nums2 = [2,5,6]`)

| Step | i | j | k | Compare | Place | nums1 |
|------|---|---|---|---------|-------|-------|
| 1 | 2 | 2 | 5 | 3 vs 6 | 6 | [1,2,3,0,0,**6**] |
| 2 | 2 | 1 | 4 | 3 vs 5 | 5 | [1,2,3,0,**5**,6] |
| 3 | 2 | 0 | 3 | 3 vs 2 | 3 | [1,2,3,**3**,5,6] |
| 4 | 1 | 0 | 2 | 2 vs 2 | 2 (nums2) | [1,2,**2**,3,5,6] |

`j = -1` → done. `nums1[0]=1, nums1[1]=2` already in place.

Result: `[1, 2, 2, 3, 5, 6]` ✅

> Note: We don't need to copy remaining `nums1` elements — they're already in the correct position.

---

## Quick Revision Summary

| # | Approach | Time | Space | Key Idea |
|---|----------|------|-------|----------|
| 1 | Brute Force | O((m+n) log(m+n)) | O(m+n) | Merge + sort |
| 2 | **Two Pointers (Back)** | **O(m+n)** | **O(1)** | **Fill from end, largest first → no overwrites** |

> 🔑 **Key takeaway:** Merging from the back is the trick — by placing the largest elements first at the end of `nums1`, you never overwrite unprocessed elements. No extra space needed.
