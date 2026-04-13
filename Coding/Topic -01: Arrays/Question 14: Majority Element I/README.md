# LeetCode 169 — Majority Element

## Problem

Find the element that appears **more than n/2 times** in the array. It is guaranteed that a majority element always exists.

**Example:**
`nums = [2, 2, 1, 1, 1, 2, 2]` → `2` (appears 4 times, n/2 = 3)

---

## 1. Brute Force

**Approach:** For each element, count its occurrences using a second loop. If count > n/2, return it.

| Time | Space |
|------|-------|
| O(n²) | O(1) |

```java
class Solution {
    public int majorityElement(int[] nums) {
        int n = nums.length;

        for (int i = 0; i < n; i++) {
            int count = 0;
            for (int j = 0; j < n; j++) {
                if (nums[j] == nums[i]) {
                    count++;
                }
            }
            if (count > n / 2) {
                return nums[i];
            }
        }
        return -1;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.majorityElement(new int[]{2, 2, 1, 1, 1, 2, 2})); // 2
        System.out.println(s.majorityElement(new int[]{3, 2, 3}));             // 3
    }
}
```

---

## 2. HashMap

**Approach:** Count frequency of each element in a map. Return the one with count > n/2.

| Time | Space |
|------|-------|
| O(n) | O(n) |

```java
import java.util.*;

class Solution {
    public int majorityElement(int[] nums) {
        Map<Integer, Integer> map = new HashMap<>();
        int majorityCount = nums.length / 2;

        for (int num : nums) {
            map.put(num, map.getOrDefault(num, 0) + 1);
        }

        for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
            if (entry.getValue() > majorityCount) {
                return entry.getKey();
            }
        }
        return -1;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.majorityElement(new int[]{2, 2, 1, 1, 1, 2, 2})); // 2
        System.out.println(s.majorityElement(new int[]{3, 3, 4, 2, 3, 3}));    // 3
    }
}
```

### Dry Run (`nums = [2, 2, 1, 1, 1, 2, 2]`)

| Step | Element | Map | 
|------|---------|-----|
| 1 | 2 | {2: 1} |
| 2 | 2 | {2: 2} |
| 3 | 1 | {2: 2, 1: 1} |
| 4 | 1 | {2: 2, 1: 2} |
| 5 | 1 | {2: 2, 1: 3} |
| 6 | 2 | {2: 3, 1: 3} |
| 7 | 2 | {2: 4, 1: 3} |

Check: `4 > 3` → return `2` ✅

---

## 3. ✅ Optimal — Moore's Voting Algorithm

**Key Idea:** The majority element (> n/2) can never be fully "cancelled out" by all other elements combined.

**Approach:**
1. Start with first element as candidate, count = 1.
2. For each next element:
   - Same as candidate → `count++`
   - Different → `count--`
   - Count hits 0 → switch candidate to current element, reset count = 1.
3. The surviving candidate is the majority element.

| Time | Space |
|------|-------|
| **O(n)** | **O(1)** |

```java
class Solution {
    public int majorityElement(int[] nums) {
        int majority = nums[0];
        int count = 1;

        for (int i = 1; i < nums.length; i++) {
            if (nums[i] == majority) {
                count++;
            } else {
                count--;
                if (count == 0) {
                    majority = nums[i];
                    count = 1;
                }
            }
        }
        return majority;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.majorityElement(new int[]{3, 2, 3}));             // 3
        System.out.println(s.majorityElement(new int[]{2, 2, 1, 1, 1, 2, 2})); // 2
    }
}
```

### Dry Run (`nums = [2, 2, 1, 1, 1, 2, 2]`)

| i | nums[i] | majority | count | Action |
|---|---------|----------|-------|--------|
| — | — | 2 | 1 | init |
| 1 | 2 | 2 | 2 | same, count++ |
| 2 | 1 | 2 | 1 | diff, count-- |
| 3 | 1 | 2 | 0 → **1** | count=0, switch to **1** |
| 4 | 1 | 1 | 2 | same, count++ |
| 5 | 2 | 1 | 1 | diff, count-- |
| 6 | 2 | 1 | 0 → **2** | count=0, switch to **2** |

Result: `2` ✅

---

## Quick Revision Summary

| # | Approach | Time | Space | Key Idea |
|---|----------|------|-------|----------|
| 1 | Brute Force | O(n²) | O(1) | Count each element's occurrences |
| 2 | HashMap | O(n) | O(n) | Frequency map, find > n/2 |
| 3 | **Moore's Voting** | **O(n)** | **O(1)** | **Cancel out non-majority, survivor wins** |

> 🔑 **Key takeaway:** Moore's Voting works because the majority element (> n/2) can survive all cancellations. Think of it as a "battle" — the majority always has more soldiers than all others combined.
