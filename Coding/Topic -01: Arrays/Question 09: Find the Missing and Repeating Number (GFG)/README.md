# Find the Missing and Repeating Number (GFG)

## Problem

Given an array of size `n` containing numbers from `1` to `n`, one number appears **twice** (repeating) and one number is **missing**. Find both.

**Example:**
`arr = [3, 1, 3, 4, 2]` → Repeating: `3`, Missing: `5`

---

## 1. Brute Force

**Approach:** For each number from 1 to N, loop through the array to count occurrences. If count == 2 → repeating. If count == 0 → missing.

| Time | Space |
|------|-------|
| O(n²) | O(1) |

```java
class Solution {
    public int[] findMissingAndRepeating(int[] arr) {
        int n = arr.length;
        int repeating = -1;
        int missing = -1;

        for (int i = 1; i <= n; i++) {
            boolean found = false;

            for (int j = 0; j < n; j++) {
                if (arr[j] == i) {
                    if (found) {
                        repeating = i; // found second time = repeating
                    }
                    found = true;
                    // no break — need to check for second occurrence
                }
            }

            if (!found) {
                missing = i;
            }
        }
        return new int[]{repeating, missing};
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        int[] nums = {3, 1, 3, 4, 2};
        int[] result = s.findMissingAndRepeating(nums);
        System.out.println("Repeating: " + result[0] + ", Missing: " + result[1]); // 3, 5
    }
}
```

---

## 2. Sorting

**Approach:** Sort the array. Check adjacent elements for the duplicate. Check index vs value for the missing number.

| Time | Space |
|------|-------|
| O(n log n) | O(1) |

```java
import java.util.Arrays;

class Solution {
    public int[] findMissingAndRepeating(int[] arr) {
        int n = arr.length;
        int[] result = new int[2];

        Arrays.sort(arr);

        // Find repeating — adjacent duplicates
        for (int i = 1; i < n; i++) {
            if (arr[i] == arr[i - 1]) {
                result[0] = arr[i];
            }
        }

        // Find missing — index mismatch
        for (int i = 1; i <= n; i++) {
            if (i != arr[i - 1]) {
                result[1] = i;
                break;
            }
        }
        return result;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        int[] nums = {3, 1, 3, 4, 2};
        int[] result = s.findMissingAndRepeating(nums);
        System.out.println("Repeating: " + result[0] + ", Missing: " + result[1]); // 3, 5
    }
}
```

---

## 3. HashMap

**Approach:** Count occurrences using a map. Frequency 2 → repeating. Not in map → missing.

| Time | Space |
|------|-------|
| O(n) | O(n) |

```java
import java.util.HashMap;

class Solution {
    public int[] findMissingAndRepeating(int[] arr) {
        HashMap<Integer, Integer> map = new HashMap<>();
        int n = arr.length;
        int[] result = new int[2];

        for (int num : arr) {
            map.put(num, map.getOrDefault(num, 0) + 1);
        }

        for (int i = 1; i <= n; i++) {
            if (map.containsKey(i)) {
                if (map.get(i) == 2) result[0] = i; // repeating
            } else {
                result[1] = i; // missing
            }
        }
        return result;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        int[] nums = {3, 1, 3, 4, 2};
        int[] result = s.findMissingAndRepeating(nums);
        System.out.println("Repeating: " + result[0] + ", Missing: " + result[1]); // 3, 5
    }
}
```

---

## 4. XOR Approach

*(Bahut tough hai, go to next approach)*

Uses XOR properties to find both numbers. O(n) time, O(1) space — but complex to implement and explain.

---

## 5. ✅ Optimal — Index Negation

**Approach:** Same trick as 442/448/287. Mark visited indices as negative.
1. Traverse array — for each element, go to index `abs(element) - 1` and negate it. If already negative → **repeating**.
2. Traverse again — any index still positive → `index + 1` is **missing**.

| Time | Space |
|------|-------|
| **O(n)** | **O(1)** — modifies input |

```java
class Solution {
    public int[] findMissingAndRepeating(int[] arr) {
        int n = arr.length;
        int repeating = -1;
        int missing = -1;

        // Step 1: Mark visited indices
        for (int i = 0; i < n; i++) {
            int index = Math.abs(arr[i]) - 1;

            if (arr[index] < 0) {
                repeating = Math.abs(arr[i]); // already visited = repeating
            } else {
                arr[index] = -arr[index];     // mark as visited
            }
        }

        // Step 2: Positive index = missing number
        for (int i = 0; i < n; i++) {
            if (arr[i] > 0) {
                missing = i + 1;
                break;
            }
        }

        return new int[]{repeating, missing};
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        int[] nums = {3, 1, 3, 4, 2};
        int[] result = s.findMissingAndRepeating(nums);
        System.out.println("Repeating: " + result[0] + ", Missing: " + result[1]); // 3, 5
    }
}
```

### Dry Run (`arr = [3, 1, 3, 4, 2]`)

**Pass 1 — Mark visited:**

| i | arr[i] | index | arr[index] | Action | arr state |
|---|--------|-------|------------|--------|-----------|
| 0 | 3 | 2 | 3 (pos) | negate | [3, 1, -3, 4, 2] |
| 1 | 1 | 0 | 3 (pos) | negate | [-3, 1, -3, 4, 2] |
| 2 | -3 | 2 | -3 (neg) | **repeating = 3** | [-3, 1, -3, 4, 2] |
| 3 | 4 | 3 | 4 (pos) | negate | [-3, 1, -3, -4, 2] |
| 4 | 2 | 1 | 1 (pos) | negate | [-3, -1, -3, -4, 2] |

**Pass 2 — Find positive:**

| i | arr[i] | Positive? | Missing |
|---|--------|-----------|---------|
| 4 | 2 | ✅ | **5** |

Result: Repeating = `3`, Missing = `5` ✅

---

## Quick Revision Summary

| # | Approach | Time | Space | Key Idea |
|---|----------|------|-------|----------|
| 1 | Brute Force | O(n²) | O(1) | Check each 1..n against array |
| 2 | Sorting | O(n log n) | O(1) | Adjacent duplicates + index mismatch |
| 3 | HashMap | O(n) | O(n) | Count frequencies |
| 4 | XOR | O(n) | O(1) | Complex XOR math |
| 5 | **Index Negation** | **O(n)** | **O(1)** | **Negate to mark visited, positive = missing** |

> 🔑 **Key takeaway:** Index negation solves three related problems — find duplicates (442), find missing (448), and find both (this problem). Same core trick: values in [1,n] → use array as hash, sign as marker.
