# LeetCode 229 — Majority Element II

## Problem

Find all elements that appear **more than n/3 times** in the array. There can be **at most 2** such elements.

**Examples:**
- `[3, 2, 3]` → `[3]`
- `[1, 1, 1, 3, 3, 2, 2, 2]` → `[1, 2]`

---

## 1. Brute Force

**Approach:** For each element, count its occurrences. If count > n/3 and not already in result, add it.

| Time | Space |
|------|-------|
| O(n²) | O(1) |

```java
import java.util.*;

class Solution {
    public List<Integer> majorityElement(int[] nums) {
        List<Integer> result = new ArrayList<>();
        int n = nums.length;

        for (int i = 0; i < n; i++) {
            int count = 0;
            for (int j = 0; j < n; j++) {
                if (nums[j] == nums[i]) count++;
            }
            if (count > n / 3 && !result.contains(nums[i])) {
                result.add(nums[i]);
            }
        }
        return result;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.majorityElement(new int[]{3, 2, 3}));                 // [3]
        System.out.println(s.majorityElement(new int[]{1, 1, 1, 3, 3, 2, 2, 2})); // [1, 2]
    }
}
```

---

## 2. Sorting

**Approach:** Sort the array. Consecutive duplicates group together — count each group and check if count > n/3.

| Time | Space |
|------|-------|
| O(n log n) | O(1) |

```java
import java.util.*;

class Solution {
    public List<Integer> majorityElement(int[] nums) {
        List<Integer> result = new ArrayList<>();
        int n = nums.length;

        Arrays.sort(nums);
        int count = 1;

        for (int i = 1; i < n; i++) {
            if (nums[i] == nums[i - 1]) {
                count++;
            } else {
                if (count > n / 3) result.add(nums[i - 1]);
                count = 1;
            }
        }
        // Check last group
        if (count > n / 3) result.add(nums[n - 1]);

        return result;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.majorityElement(new int[]{3, 2, 3}));                 // [3]
        System.out.println(s.majorityElement(new int[]{1, 1, 1, 3, 3, 2, 2, 2})); // [1, 2]
    }
}
```

---

## 3. HashMap

**Approach:** Count frequency of each element in a map. Collect those with count > n/3.

| Time | Space |
|------|-------|
| O(n) | O(n) |

```java
import java.util.*;

class Solution {
    public List<Integer> majorityElement(int[] nums) {
        Map<Integer, Integer> map = new HashMap<>();
        List<Integer> result = new ArrayList<>();
        int n = nums.length;

        for (int num : nums) {
            map.put(num, map.getOrDefault(num, 0) + 1);
        }

        for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
            if (entry.getValue() > n / 3) {
                result.add(entry.getKey());
            }
        }
        return result;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.majorityElement(new int[]{3, 2, 3}));                 // [3]
        System.out.println(s.majorityElement(new int[]{1, 1, 1, 3, 3, 2, 2, 2})); // [1, 2]
    }
}
```

### Dry Run (`nums = [1, 1, 1, 3, 3, 2, 2, 2]`)

| Step | Element | Map |
|------|---------|-----|
| 1-3 | 1,1,1 | {1: 3} |
| 4-5 | 3,3 | {1: 3, 3: 2} |
| 6-8 | 2,2,2 | {1: 3, 3: 2, 2: 3} |

n/3 = 8/3 ≈ 2.67 → `1` (3 > 2.67) ✅, `3` (2 ≤ 2.67) ❌, `2` (3 > 2.67) ✅ → `[1, 2]`

---

## 4. ✅ Optimal — Extended Moore's Voting

**Key Idea:** At most **2 elements** can appear > n/3 times. So we track **2 candidates** instead of 1.

**Approach — Two passes:**

**Pass 1 — Find candidates:**
1. Maintain `candidate1`, `candidate2` with `count1`, `count2`.
2. For each element:
   - Matches candidate1 → count1++
   - Matches candidate2 → count2++
   - count1 == 0 → new candidate1
   - count2 == 0 → new candidate2
   - Else → decrement both counts

**Pass 2 — Validate candidates:**
Count actual occurrences of both candidates. Only add those with count > n/3.

| Time | Space |
|------|-------|
| **O(n)** | **O(1)** |

```java
import java.util.*;

class Solution {
    public List<Integer> majorityElement(int[] nums) {
        List<Integer> result = new ArrayList<>();
        int candidate1 = 0, candidate2 = 0;
        int count1 = 0, count2 = 0;

        // Pass 1: Find candidates
        for (int i = 0; i < nums.length; i++) {
            int num = nums[i];

            if (num == candidate1) {
                count1++;
            } else if (num == candidate2) {
                count2++;
            } else if (count1 == 0) {
                candidate1 = num;
                count1 = 1;
            } else if (count2 == 0) {
                candidate2 = num;
                count2 = 1;
            } else {
                count1--;
                count2--;
            }
        }

        // Pass 2: Validate candidates
        count1 = 0;
        count2 = 0;
        for (int num : nums) {
            if (num == candidate1) count1++;
            else if (num == candidate2) count2++;
        }

        int n = nums.length;
        if (count1 > n / 3) result.add(candidate1);
        if (count2 > n / 3) result.add(candidate2);

        return result;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.majorityElement(new int[]{3, 2, 3}));                 // [3]
        System.out.println(s.majorityElement(new int[]{1, 1, 1, 3, 3, 2, 2, 2})); // [1, 2]
    }
}
```

### Dry Run (`nums = [3, 2, 3]`)

**Pass 1:**

| i | num | c1 | cnt1 | c2 | cnt2 | Action |
|---|-----|----|------|----|------|--------|
| 0 | 3 | 3 | 1 | 0 | 0 | cnt1=0 → new c1=3 |
| 1 | 2 | 3 | 1 | 2 | 1 | cnt2=0 → new c2=2 |
| 2 | 3 | 3 | 2 | 2 | 1 | matches c1, cnt1++ |

**Pass 2:** c1=3 appears 2 times, c2=2 appears 1 time. n/3 = 1 → `3` (2 > 1) ✅, `2` (1 ≤ 1) ❌

Result: `[3]` ✅

---

## Quick Revision Summary

| # | Approach | Time | Space | Key Idea |
|---|----------|------|-------|----------|
| 1 | Brute Force | O(n²) | O(1) | Count each element's occurrences |
| 2 | Sorting | O(n log n) | O(1) | Sort, count consecutive groups |
| 3 | HashMap | O(n) | O(n) | Frequency map, find > n/3 |
| 4 | **Moore's Voting** | **O(n)** | **O(1)** | **Track 2 candidates, validate in 2nd pass** |

> 🔑 **Key takeaway:** Extension of Moore's Voting — for n/3, track **2 candidates** (for n/k, track k-1 candidates). Unlike Majority Element I, a **second validation pass is required** because candidates aren't guaranteed to be majority elements.
