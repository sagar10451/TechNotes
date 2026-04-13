# LeetCode 448 — Find All Numbers Disappeared in an Array

## Problem

Given an integer array of size `n` where each value is in `[1, n]`, some elements appear twice and others once. This means some numbers are **missing**. Return all numbers in `[1, n]` that are missing from the array.

**Example:**
`nums = [4, 3, 2, 7, 8, 2, 3, 1]` → `[5, 6]`

---

## 1. Brute Force

**Approach:** For each number from 1 to n, loop through the entire array to check if it exists.

| Time | Space |
|------|-------|
| O(n²) | O(1) |

```java
import java.util.*;

class Solution {
    public List<Integer> findDisappearedNumbers(int[] nums) {
        List<Integer> result = new ArrayList<>();

        for (int i = 1; i <= nums.length; i++) {
            boolean found = false;
            for (int j = 0; j < nums.length; j++) {
                if (nums[j] == i) {
                    found = true;
                    break;
                }
            }
            if (!found) result.add(i);
        }
        return result;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        int[] nums = {4, 3, 2, 7, 8, 2, 3, 1};
        System.out.println(s.findDisappearedNumbers(nums)); // [5, 6]
    }
}
```

---

## 2. Boolean Array

**Approach:** Create a `boolean[]` of size `n+1`. Mark each number as seen, then collect unmarked ones.

| Time | Space |
|------|-------|
| O(n) | O(n) |

```java
import java.util.*;

class Solution {
    public List<Integer> findDisappearedNumbers(int[] nums) {
        List<Integer> result = new ArrayList<>();
        boolean[] seen = new boolean[nums.length + 1];

        for (int num : nums) {
            seen[num] = true;
        }

        for (int i = 1; i < seen.length; i++) {
            if (!seen[i]) result.add(i);
        }
        return result;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        int[] nums = {4, 3, 2, 7, 8, 2, 3, 1};
        System.out.println(s.findDisappearedNumbers(nums)); // [5, 6]
    }
}
```

---

## 3. Set

**Approach:** Insert all elements into a Set (duplicates removed automatically). Then check each number from 1 to n — if not in the set, it's missing.

| Time | Space |
|------|-------|
| O(n) | O(n) |

### Why Set instead of Map?

We only care about **presence** — "is this number in the array or not?" A Set answers exactly that with `.contains()`. A Map stores key-value pairs (e.g., element → frequency), which is overkill here since we don't need the count. Set is simpler, cleaner, and the right tool for the job.

```java
import java.util.*;

class Solution {
    public List<Integer> findDisappearedNumbers(int[] nums) {
        List<Integer> result = new ArrayList<>();
        Set<Integer> set = new HashSet<>();

        for (int num : nums) {
            set.add(num);
        }

        for (int i = 1; i <= nums.length; i++) {
            if (!set.contains(i)) result.add(i);
        }
        return result;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        int[] nums = {4, 3, 2, 7, 8, 2, 3, 1};
        System.out.println(s.findDisappearedNumbers(nums)); // [5, 6]
    }
}
```

---

## 4. ✅ Optimal — Index Negation

**Key Insight:** Same trick as LeetCode 442 (Find All Duplicates). Values are in `[1, n]` → use the array itself as a marker.

**Approach:**
1. Iterate over array — for each element, mark index `abs(element) - 1` as negative.
2. Iterate again — any index still **positive** means `index + 1` is missing.

| Time | Space |
|------|-------|
| **O(n)** | **O(1)** — modifies input, no extra space |

```java
import java.util.*;

class Solution {
    public List<Integer> findDisappearedNumbers(int[] nums) {
        List<Integer> list = new ArrayList<>();

        // Mark visited indices as negative
        for (int i = 0; i < nums.length; i++) {
            int index = Math.abs(nums[i]) - 1;
            if (nums[index] > 0) {
                nums[index] *= -1;
            }
        }

        // Positive values = that index was never visited = missing number
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] > 0) {
                list.add(i + 1);
            }
        }
        return list;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        int[] nums = {4, 3, 2, 7, 8, 2, 3, 1};
        System.out.println(s.findDisappearedNumbers(nums)); // [5, 6]
    }
}
```

### Dry Run (`nums = [4, 3, 2, 7, 8, 2, 3, 1]`)

**Pass 1 — Mark indices:**

| i | nums[i] | index = abs-1 | Action |
|---|---------|---------------|--------|
| 0 | 4 | 3 | negate nums[3]: 7 → -7 |
| 1 | 3 | 2 | negate nums[2]: 2 → -2 |
| 2 | -2 | 1 | negate nums[1]: 3 → -3 |
| 3 | -7 | 6 | negate nums[6]: 3 → -3 |
| 4 | 8 | 7 | negate nums[7]: 1 → -1 |
| 5 | -2 | 1 | nums[1] already negative, skip |
| 6 | -3 | 2 | nums[2] already negative, skip |
| 7 | -1 | 0 | negate nums[0]: 4 → -4 |

Array after pass 1: `[-4, -3, -2, -7, 8, 2, -3, -1]`

**Pass 2 — Collect positives:**

| i | nums[i] | Positive? | Missing |
|---|---------|-----------|---------|
| 4 | 8 | ✅ | **5** |
| 5 | 2 | ✅ | **6** |

Result: `[5, 6]` ✅

---

## Quick Revision Summary

| # | Approach | Time | Space | Key Idea |
|---|----------|------|-------|----------|
| 1 | Brute Force | O(n²) | O(1) | Check each 1..n against array |
| 2 | Boolean Array | O(n) | O(n) | Mark seen in boolean[] |
| 3 | Set | O(n) | O(n) | Presence check (no frequency needed → Set > Map) |
| 4 | **Index Negation** | **O(n)** | **O(1)** | **Mark by sign, positive = missing** |

> 🔑 **Key takeaway:** Same index negation trick as 442. Mark visited indices as negative → positive indices at the end reveal missing numbers.
