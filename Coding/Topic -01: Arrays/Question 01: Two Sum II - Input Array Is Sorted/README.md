# LeetCode 167 — Two Sum II (Input Array Is Sorted)

## Problem

Given a **1-indexed** array sorted in **non-decreasing order**, find two numbers that add up to a specific `target`. Return their **1-based indices**.

> **Guaranteed:** Exactly one solution exists. You may not use the same element twice.

---

## 1. Brute Force

**Approach:** Try every pair using two nested loops.

| Time | Space |
|------|-------|
| O(n²) | O(1) |

```java
import java.util.*;

public class TwoSumIIBruteForce {
    public static int[] twoSum(int[] numbers, int target) {
        for (int i = 0; i < numbers.length; i++) {
            for (int j = i + 1; j < numbers.length; j++) {
                if (numbers[i] + numbers[j] == target) {
                    return new int[]{i + 1, j + 1}; // 1-based index
                }
            }
        }
        return new int[]{};
    }

    public static void main(String[] args) {
        int[] numbers = {2, 7, 11, 15};
        int target = 9;
        System.out.println("Indices: " + Arrays.toString(twoSum(numbers, target))); // [1, 2]
    }
}
```

**Why it's slow:** For every element, we check all subsequent elements → nested loops → O(n²).

---

## 2. HashMap (Same as Two Sum I)

**Approach:**
1. Iterate over the array.
2. For each element, check if `target - current` exists in the map.
3. If yes → return both indices.
4. If no → insert current element with its index into the map.

| Time | Space |
|------|-------|
| O(n) | O(n) |

```java
import java.util.*;

public class TwoSumIIHashMap {
    public static int[] twoSum(int[] numbers, int target) {
        Map<Integer, Integer> map = new HashMap<>();

        for (int i = 0; i < numbers.length; i++) {
            int complement = target - numbers[i];

            if (map.containsKey(complement)) {
                return new int[]{map.get(complement) + 1, i + 1}; // 1-based index
            }

            map.put(numbers[i], i);
        }
        return new int[]{};
    }

    public static void main(String[] args) {
        int[] numbers = {2, 7, 11, 15};
        int target = 9;
        System.out.println("Indices: " + Arrays.toString(twoSum(numbers, target))); // [1, 2]
    }
}
```

**Why we can do better:** The array is **already sorted** — we're not using that info. We can drop the extra O(n) space with two pointers.

---

## 3. ✅ Optimal — Two Pointers

**Approach:**
1. Set `left = 0`, `right = end of array`.
2. Compute `sum = numbers[left] + numbers[right]`.
   - `sum == target` → return indices.
   - `sum < target` → move `left++` (need a bigger number).
   - `sum > target` → move `right--` (need a smaller number).

| Time | Space |
|------|-------|
| **O(n)** | **O(1)** |

```java
import java.util.*;

public class TwoSumIIOptimal {
    public static int[] twoSum(int[] numbers, int target) {
        int left = 0;
        int right = numbers.length - 1;

        while (left < right) {
            int sum = numbers[left] + numbers[right];

            if (sum == target) {
                return new int[]{left + 1, right + 1}; // 1-based index
            } else if (sum < target) {
                left++;
            } else {
                right--;
            }
        }
        return new int[]{};
    }

    public static void main(String[] args) {
        int[] numbers = {2, 7, 11, 15};
        int target = 9;
        System.out.println("Indices: " + Arrays.toString(twoSum(numbers, target))); // [1, 2]
    }
}
```

**Why it works:** Sorted array means moving `left` always increases the sum, moving `right` always decreases it. We converge to the answer in one pass.

---

## Quick Revision Summary

| # | Approach | Time | Space | Key Idea |
|---|----------|------|-------|----------|
| 1 | Brute Force | O(n²) | O(1) | Try every pair |
| 2 | HashMap | O(n) | O(n) | Lookup complement in map |
| 3 | **Two Pointers** | **O(n)** | **O(1)** | **Sorted → shrink window from both ends** |

> 🔑 **Key takeaway:** When the array is sorted, always think **two pointers** before reaching for a hashmap.
