# LeetCode 41 — First Missing Positive

## Problem

Given an unsorted integer array (can contain negatives, zeros, and positives), find the **smallest missing positive integer**.

Must run in **O(n) time** and **O(1) extra space**.

**Examples:**
- `[1, 2, 0]` → `3`
- `[3, 4, -1, 1]` → `2`
- `[7, 8, 9, 11, 12]` → `1`

---

## 1. Sorting

**Approach:** Sort the array, then iterate looking for the first missing positive starting from 1.

| Time | Space |
|------|-------|
| O(n log n) | O(1) |

```java
import java.util.Arrays;

public class Solution {
    public int firstMissingPositive(int[] nums) {
        Arrays.sort(nums);

        int expected = 1;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == expected) {
                expected++;
            }
        }
        return expected;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.firstMissingPositive(new int[]{1, 2, 0}));        // 3
        System.out.println(s.firstMissingPositive(new int[]{3, 4, -1, 1}));    // 2
        System.out.println(s.firstMissingPositive(new int[]{7, 8, 9, 11, 12})); // 1
    }
}
```

---

## 2. HashSet

**Approach:** Insert all positive numbers into a set. Then check 1, 2, 3... until one is not in the set.

| Time | Space |
|------|-------|
| O(n) | O(n) |

```java
import java.util.HashSet;

public class Solution {
    public int firstMissingPositive(int[] nums) {
        HashSet<Integer> set = new HashSet<>();

        for (int num : nums) {
            if (num > 0) set.add(num);
        }

        int expected = 1;
        while (set.contains(expected)) {
            expected++;
        }
        return expected;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.firstMissingPositive(new int[]{1, 2, 0}));        // 3
        System.out.println(s.firstMissingPositive(new int[]{3, 4, -1, 1}));    // 2
        System.out.println(s.firstMissingPositive(new int[]{7, 8, 9, 11, 12})); // 1
    }
}
```

---

## 3. ✅ Optimal — Cyclic Sort (Index Placement)

**Key Insight:** Array can have negatives/zeros, so the negation trick from 442/448 won't work. Instead, **swap each element to its correct position** — value `k` should sit at index `k - 1`.

**Approach:**
1. For each element, if it's in range `[1, n]` and not in its correct spot → **swap** it there.
2. After rearranging, the first index where `nums[i] != i + 1` → answer is `i + 1`.
3. If all positions are correct → answer is `n + 1`.

| Time | Space |
|------|-------|
| **O(n)** | **O(1)** — in-place swaps |

> Each element is swapped at most once → O(n) total despite the `while` loop.

```java
public class Solution {
    public int firstMissingPositive(int[] nums) {
        int n = nums.length;

        // Step 1: Place each number at its correct index (value k → index k-1)
        for (int i = 0; i < n; i++) {
            while (nums[i] > 0 && nums[i] <= n && nums[nums[i] - 1] != nums[i]) {
                int temp = nums[i];
                nums[i] = nums[nums[i] - 1];
                nums[temp - 1] = temp;
            }
        }

        // Step 2: First index where value != index+1 is the answer
        for (int i = 0; i < n; i++) {
            if (nums[i] != i + 1) {
                return i + 1;
            }
        }

        // All 1..n present → answer is n+1
        return n + 1;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.firstMissingPositive(new int[]{1, 2, 0}));        // 3
        System.out.println(s.firstMissingPositive(new int[]{3, 4, -1, 1}));    // 2
        System.out.println(s.firstMissingPositive(new int[]{7, 8, 9, 11, 12})); // 1
    }
}
```

### Dry Run (`nums = [3, 4, -1, 1]`)

**Step 1 — Cyclic sort:**

| i | nums[i] | In range? | Correct spot? | Action | Array |
|---|---------|-----------|---------------|--------|-------|
| 0 | 3 | ✅ | nums[2]≠3 | swap(0,2) | [-1, 4, 3, 1] |
| 0 | -1 | ❌ | — | skip | [-1, 4, 3, 1] |
| 1 | 4 | ✅ | nums[3]≠4 | swap(1,3) | [-1, 1, 3, 4] |
| 1 | 1 | ✅ | nums[0]≠1 | swap(1,0) | [1, -1, 3, 4] |
| 1 | -1 | ❌ | — | skip | [1, -1, 3, 4] |
| 2 | 3 | ✅ | nums[2]==3 | already correct | [1, -1, 3, 4] |
| 3 | 4 | ✅ | nums[3]==4 | already correct | [1, -1, 3, 4] |

**Step 2 — Find mismatch:**

| i | nums[i] | Expected | Match? |
|---|---------|----------|--------|
| 0 | 1 | 1 | ✅ |
| 1 | -1 | 2 | ❌ → **return 2** |

Result: `2` ✅

---

## Quick Revision Summary

| # | Approach | Time | Space | Key Idea |
|---|----------|------|-------|----------|
| 1 | Sorting | O(n log n) | O(1) | Sort, scan for first gap |
| 2 | HashSet | O(n) | O(n) | Presence check from 1 upward |
| 3 | **Cyclic Sort** | **O(n)** | **O(1)** | **Swap value k to index k-1, find first mismatch** |

> 🔑 **Key takeaway:** Can't use negation trick here (array has negatives/zeros). Instead, use cyclic sort — place each value at its "home" index, then scan for the first mismatch. This is the go-to pattern when you need O(n) time + O(1) space on arrays with values in [1, n].
