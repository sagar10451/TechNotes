# LeetCode 442 — Find All Duplicates in an Array

## Problem

Given an array of `n` integers where each value is in the range `[1, n]`, each element appears **once or twice**. Return all integers that appear **twice**. Array is **not sorted**.

Must be done in **O(n) time** and **O(1) extra space** (not counting output).

**Example:**
`nums = [4, 3, 2, 7, 8, 2, 3, 1]` → `[2, 3]`

---

## 1. Brute Force

**Approach:** Nested loop — compare every element with every other. If duplicate found, add to result.

| Time | Space |
|------|-------|
| O(n²) | O(n) for result |

```java
import java.util.*;

public class Solution {
    public List<Integer> findDuplicates(int[] nums) {
        List<Integer> result = new ArrayList<>();

        for (int i = 0; i < nums.length; i++) {
            for (int j = i + 1; j < nums.length; j++) {
                if (nums[i] == nums[j]) {
                    if (!result.contains(nums[i])) {
                        result.add(nums[i]);
                    }
                }
            }
        }
        return result;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        int[] nums = {4, 3, 2, 7, 8, 2, 3, 1};
        System.out.println(s.findDuplicates(nums)); // [2, 3]
    }
}
```

---

## 2. HashMap

**Approach:** Store frequency of each element in a map, then collect those with frequency == 2.

Can also use a **Set** — keep inserting, if element already exists → it's a duplicate. Same time/space complexity.

| Time | Space |
|------|-------|
| O(n) | O(n) |

```java
import java.util.*;

class Solution {
    public List<Integer> findDuplicates(int[] nums) {
        Map<Integer, Integer> m = new HashMap<>();
        List<Integer> result = new ArrayList<>();

        // Count frequencies
        for (int num : nums) {
            m.put(num, m.getOrDefault(num, 0) + 1);
        }

        // Collect duplicates
        for (Map.Entry<Integer, Integer> entry : m.entrySet()) {
            if (entry.getValue() == 2) {
                result.add(entry.getKey());
            }
        }
        return result;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        int[] nums = {4, 3, 2, 7, 8, 2, 3, 1};
        System.out.println(s.findDuplicates(nums)); // [2, 3]
    }
}
```

---

## 3. ✅ Optimal — Index Negation

**Key Insight:** Values are in `[1, n]`, so each value can map to a valid index (`value - 1`). Use the **sign** of the element at that index as a visited marker.

**Approach:**
1. Traverse the array.
2. For each element, go to index `abs(element) - 1`.
3. If the value there is **positive** → mark it negative (first visit).
4. If already **negative** → this element is a duplicate.

| Time | Space |
|------|-------|
| **O(n)** | **O(1)** — no extra space (modifies input) |

```java
import java.util.*;

class Solution {
    public List<Integer> findDuplicates(int[] nums) {
        List<Integer> result = new ArrayList<>();

        for (int i = 0; i < nums.length; i++) {
            int n = Math.abs(nums[i]);

            if (nums[n - 1] > 0) {
                nums[n - 1] *= -1;   // mark as visited
            } else {
                result.add(n);       // already visited = duplicate
            }
        }
        return result;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        int[] nums = {4, 3, 2, 7, 8, 2, 3, 1};
        System.out.println(s.findDuplicates(nums)); // [2, 3]
    }
}
```

### Dry Run (`nums = [4, 3, 2, 7, 8, 2, 3, 1]`)

| i | nums[i] | n = abs | nums[n-1] | Action | result |
|---|---------|---------|-----------|--------|--------|
| 0 | 4 | 4 | 7 (pos) | negate → -7 | [] |
| 1 | 3 | 3 | 2 (pos) | negate → -2 | [] |
| 2 | 2 | 2 | 3 (pos) | negate → -3 | [] |
| 3 | -7 | 7 | 3 (pos) | negate → -3 | [] |
| 4 | 8 | 8 | 1 (pos) | negate → -1 | [] |
| 5 | -2 | 2 | -3 (neg) | **duplicate** | [2] |
| 6 | -3 | 3 | -2 (neg) | **duplicate** | [2, 3] |
| 7 | -1 | 1 | 4 (pos) | negate → -4 | [2, 3] |

Result: `[2, 3]` ✅

---

## Quick Revision Summary

| # | Approach | Time | Space | Key Idea |
|---|----------|------|-------|----------|
| 1 | Brute Force | O(n²) | O(n) | Compare every pair |
| 2 | HashMap | O(n) | O(n) | Count frequencies |
| 3 | **Index Negation** | **O(n)** | **O(1)** | **Values in [1,n] → use index as hash, sign as marker** |

> 🔑 **Key takeaway:** When values are in `[1, n]`, you can use the array itself as a hash map — map each value to an index and use the sign to track visits. No extra space needed.
