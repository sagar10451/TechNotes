# LeetCode 16 — 3Sum Closest

## Problem

Given an integer array and a `target`, find three numbers whose sum is **closest to the target**. So out of all triplets, we have to find the one whose sum is closest to the target. Return that sum. Exactly one solution is guaranteed.

**Example:**
`nums = [-1, 2, 1, -4]`, `target = 1` → output: `2` (because `-1 + 2 + 1 = 2` is closest to `1`).

---

## 1. Brute Force

**Approach:** Three nested loops to try every triplet. Track the sum closest to the target.

| Time | Space |
|------|-------|
| O(n³) | O(1) |

```java
import java.util.*;

public class ThreeSumClosestBruteForce {
    public static int threeSumClosest(int[] nums, int target) {
        int closestSum = nums[0] + nums[1] + nums[2];

        for (int i = 0; i < nums.length - 2; i++) {
            for (int j = i + 1; j < nums.length - 1; j++) {
                for (int k = j + 1; k < nums.length; k++) {
                    int sum = nums[i] + nums[j] + nums[k];

                    if (Math.abs(target - sum) < Math.abs(target - closestSum)) {
                        closestSum = sum;
                    }
                }
            }
        }
        return closestSum;
    }

    public static void main(String[] args) {
        int[] nums = {-1, 2, 1, -4};
        int target = 1;
        System.out.println(threeSumClosest(nums, target)); // Output: 2
    }
}
```

**Why it's slow:** Three nested loops → O(n³).

---

## 2. ✅ Optimal — Sort + Two Pointers

**Approach:** Similar to 3Sum problem.
1. **Sort the array.**
2. **Fix one element** (`nums[i]`), then use two pointers on the rest.
3. Track the **closest sum** to the target as you go.
4. If `currentSum == target` → return immediately (can't get closer).

| Time | Space |
|------|-------|
| **O(n²)** | **O(1)** |

> Sorting is O(n log n), two-pointer pass for each element is O(n) → overall **O(n²)**.

```java
import java.util.*;

public class ThreeSumClosestOptimal {
    public static int threeSumClosest(int[] nums, int target) {
        Arrays.sort(nums);
        int closestSum = nums[0] + nums[1] + nums[2];

        for (int i = 0; i < nums.length - 2; i++) {
            int left = i + 1;
            int right = nums.length - 1;

            while (left < right) {
                int currentSum = nums[i] + nums[left] + nums[right];

                // Update closest if current is nearer to target
                if (Math.abs(currentSum - target) < Math.abs(closestSum - target)) {
                    closestSum = currentSum;
                }

                if (currentSum < target) {
                    left++;       // need a bigger sum
                } else if (currentSum > target) {
                    right--;      // need a smaller sum
                } else {
                    return currentSum; // exact match, can't do better
                }
            }
        }
        return closestSum;
    }

    public static void main(String[] args) {
        int[] nums = {-1, 2, 1, -4};
        int target = 1;
        System.out.println(threeSumClosest(nums, target)); // Output: 2
    }
}
```

---

## Quick Revision Summary

| # | Approach | Time | Space | Key Idea |
|---|----------|------|-------|----------|
| 1 | Brute Force | O(n³) | O(1) | Try every triplet, track closest sum |
| 2 | **Sort + Two Pointers** | **O(n²)** | **O(1)** | **Fix one, two-pointer the rest, track closest** |

> 🔑 **Key takeaway:** Almost identical to 3Sum — instead of checking `sum == 0`, track which sum is closest to the target. Early return if exact match found.
