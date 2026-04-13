# LeetCode 287 — Find the Duplicate Number

## Problem

Given an array of `n + 1` integers where each integer is in `[1, n]`, there is **only one repeated number**. Return it.

**Constraints:** Must solve **without modifying** the array and using only **constant extra space**.

**Example:**
`nums = [1, 3, 4, 2, 2]` → `2`

---

## 1. Brute Force

**Approach:** Compare each element with every other element.

| Time | Space | Modifies Array? |
|------|-------|-----------------|
| O(n²) | O(1) | ❌ No |

```java
class Solution {
    public int findDuplicate(int[] nums) {
        for (int i = 0; i < nums.length; i++) {
            for (int j = i + 1; j < nums.length; j++) {
                if (nums[i] == nums[j]) {
                    return nums[i];
                }
            }
        }
        return -1;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        int[] nums = {1, 3, 4, 2, 2};
        System.out.println(s.findDuplicate(nums)); // 2
    }
}
```

---

## 2. Sorting

**Approach:** Sort the array, then check adjacent elements. Duplicates will be next to each other.

| Time | Space | Modifies Array? |
|------|-------|-----------------|
| O(n log n) | O(1) | ⚠️ Yes (sorts in place) |

```java
import java.util.*;

class Solution {
    public int findDuplicate(int[] nums) {
        Arrays.sort(nums);

        for (int i = 1; i < nums.length; i++) {
            if (nums[i] == nums[i - 1]) {
                return nums[i];
            }
        }
        return -1;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        int[] nums = {1, 3, 4, 2, 2};
        System.out.println(s.findDuplicate(nums)); // 2
    }
}
```

---

## 3. HashSet

**Approach:** Traverse the array, add elements to a Set. If element already exists → it's the duplicate.

| Time | Space | Modifies Array? |
|------|-------|-----------------|
| O(n) | O(n) | ❌ No |

```java
import java.util.*;

class Solution {
    public int findDuplicate(int[] nums) {
        Set<Integer> set = new HashSet<>();

        for (int i = 0; i < nums.length; i++) {
            if (set.contains(nums[i])) return nums[i];
            set.add(nums[i]);
        }
        return -1;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        int[] nums = {1, 3, 4, 2, 2};
        System.out.println(s.findDuplicate(nums)); // 2
    }
}
```

---

## 4. Index Negation

**Approach:** Same trick as 442/448 — mark visited indices as negative.

| Time | Space | Modifies Array? |
|------|-------|-----------------|
| O(n) | O(1) | ⚠️ Yes (negates values) |

```java
class Solution {
    public int findDuplicate(int[] nums) {
        for (int i = 0; i < nums.length; i++) {
            int ind = Math.abs(nums[i]);

            if (nums[ind] < 0) {
                return ind;  // already visited = duplicate
            }
            nums[ind] = -nums[ind];
        }
        return -1;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        int[] nums = {1, 3, 4, 2, 2};
        System.out.println(s.findDuplicate(nums)); // 2
    }
}
```

---

## 5. ✅ Optimal — Floyd's Tortoise and Hare

**Approach:** Treat the array as a linked list where `nums[i]` points to the next node. Since there's a duplicate, there **must be a cycle**. Use Floyd's cycle detection to find the entry point of the cycle — that's the duplicate.

| Time | Space | Modifies Array? |
|------|-------|-----------------|
| **O(n)** | **O(1)** | **❌ No** |

> This is the **only approach** that satisfies both constraints: O(1) space + no modification.

**Two phases:**
1. **Detect cycle:** Slow moves 1 step, fast moves 2 steps. They meet inside the cycle.
2. **Find entry point:** Reset fast to start. Both move 1 step at a time. They meet at the duplicate.

```java
class Solution {
    public int findDuplicate(int[] nums) {
        int slow = nums[0];
        int fast = nums[0];

        // Phase 1: detect cycle
        do {
            slow = nums[slow];
            fast = nums[nums[fast]];
        } while (slow != fast);

        // Phase 2: find entry point of cycle
        fast = nums[0];
        while (slow != fast) {
            slow = nums[slow];
            fast = nums[fast];
        }
        return fast;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        int[] nums = {1, 3, 4, 2, 2};
        System.out.println(s.findDuplicate(nums)); // 2
    }
}
```

---

## Quick Revision Summary

| # | Approach | Time | Space | Modifies? | Notes |
|---|----------|------|-------|-----------|-------|
| 1 | Brute Force | O(n²) | O(1) | ❌ | Slow but valid |
| 2 | Sorting | O(n log n) | O(1) | ⚠️ Yes | Violates "no modify" constraint |
| 3 | HashSet | O(n) | O(n) | ❌ | Violates "O(1) space" constraint |
| 4 | Index Negation | O(n) | O(1) | ⚠️ Yes | Violates "no modify" constraint |
| 5 | **Floyd's Cycle** | **O(n)** | **O(1)** | **❌** | **Only solution satisfying both constraints** |

> 🔑 **Key takeaway:** The natural progression — brute force → sort → hashset → index negation → Floyd's. Each fixes a limitation of the previous. Floyd's is the only one that satisfies both O(1) space and no array modification.
