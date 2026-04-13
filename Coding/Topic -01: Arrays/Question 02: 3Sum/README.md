# LeetCode 15 — 3Sum

## Problem

Given an integer array, find all **unique triplets** that sum to **zero**. Each triplet must use **three distinct indices**, and the result must not contain duplicate triplets.

**Example:**
`nums = [-1, 0, 1, 2, -1, -4]` → `[[-1, -1, 2], [-1, 0, 1]]`

---

## 1. Brute Force

**Approach:** Three nested loops to try every possible triplet. Use a `Set` with sorted triplets to avoid duplicates.

| Time | Space |
|------|-------|
| O(n³) | O(k) — where k = number of unique triplets |

```java
import java.util.*;

public class ThreeSumBruteForce {
    public static List<List<Integer>> threeSum(int[] nums) {
        Set<List<Integer>> result = new HashSet<>();

        for (int i = 0; i < nums.length - 2; i++) {
            for (int j = i + 1; j < nums.length - 1; j++) {
                for (int k = j + 1; k < nums.length; k++) {
                    if (nums[i] + nums[j] + nums[k] == 0) {
                        List<Integer> triplet = Arrays.asList(nums[i], nums[j], nums[k]);
                        Collections.sort(triplet); // sort to handle duplicates
                        result.add(triplet);
                    }
                }
            }
        }
        return new ArrayList<>(result);
    }

    public static void main(String[] args) {
        int[] nums = {-1, 0, 1, 2, -1, -4};
        System.out.println(threeSum(nums)); // [[-1, -1, 2], [-1, 0, 1]]
    }
}
```

**Why it's slow:** Three nested loops → O(n³). Works but will TLE on large inputs.

---

## 2. ✅ Optimal — Sort + Two Pointers

**Approach:**
1. **Sort the array** — enables two-pointer technique and easy duplicate skipping.
2. **Fix one element** (`nums[i]`), then use two pointers on the remaining subarray.
3. **Skip duplicates** at all three levels — `i`, `left`, and `right`.

| Time | Space |
|------|-------|
| **O(n²)** | **O(k)** — where k = number of unique triplets |

> Sorting is O(n log n), two-pointer pass for each element is O(n) → overall **O(n²)**.

**Algorithm:**
1. Sort the array.
2. Loop `i` from `0` to `n-2`:
   - **Skip** if `nums[i] == nums[i-1]` (avoid duplicate triplets).
   - Set `left = i + 1`, `right = end`.
   - While `left < right`:
     - `sum < 0` → `left++` (need bigger).
     - `sum > 0` → `right--` (need smaller).
     - `sum == 0` → **found a triplet**, skip duplicates on both sides, move both pointers.

```java
import java.util.*;

class Solution {
    public static List<List<Integer>> threeSum(int[] nums) {
        Arrays.sort(nums);
        List<List<Integer>> result = new ArrayList<>();

        for (int i = 0; i < nums.length - 2; i++) {
            // Skip duplicates for i
            if (i > 0 && nums[i] == nums[i - 1]) {
                continue;
            }

            int left = i + 1, right = nums.length - 1;

            while (left < right) {
                int sum = nums[i] + nums[left] + nums[right];

                if (sum < 0) {
                    left++;
                } else if (sum > 0) {
                    right--;
                } else {
                    result.add(Arrays.asList(nums[i], nums[left], nums[right]));

                    // Skip duplicates for left and right
                    while (left < right && nums[left] == nums[left + 1]) left++;
                    while (left < right && nums[right] == nums[right - 1]) right--;

                    left++;
                    right--;
                }
            }
        }
        return result;
    }

    public static void main(String[] args) {
        int[] nums = {-1, 0, 1, 2, -1, -4};
        System.out.println(threeSum(nums)); // [[-1, -1, 2], [-1, 0, 1]]
    }
}
```

---

## Quick Revision Summary

| # | Approach | Time | Space | Key Idea |
|---|----------|------|-------|----------|
| 1 | Brute Force | O(n³) | O(k) | Try every triplet, use Set for uniqueness |
| 2 | **Sort + Two Pointers** | **O(n²)** | **O(k)** | **Fix one, two-pointer the rest, skip duplicates** |

> 🔑 **Key takeaway:** Sort first → fix one element → two-pointer on the rest. Handle duplicates at every level (`i`, `left`, `right`).
