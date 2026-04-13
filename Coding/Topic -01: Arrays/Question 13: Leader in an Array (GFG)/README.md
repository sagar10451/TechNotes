# Leader in an Array (GFG)

## Problem

An element is a **leader** if it is **greater than all elements to its right**. The rightmost element is always a leader. Return all leaders in the array.

**Example:**
`arr = [16, 17, 4, 3, 5, 2]` → `[17, 5, 2]`

---

## 1. Brute Force

**Approach:** For each element, check all elements to its right. If none are greater, it's a leader.

| Time | Space |
|------|-------|
| O(n²) | O(n) for result |

```java
import java.util.*;

public class Solution {
    public List<Integer> findLeaders(int[] arr) {
        List<Integer> leaders = new ArrayList<>();
        int n = arr.length;

        for (int i = 0; i < n; i++) {
            boolean isLeader = true;

            for (int j = i + 1; j < n; j++) {
                if (arr[i] <= arr[j]) {
                    isLeader = false;
                    break;
                }
            }

            if (isLeader) {
                leaders.add(arr[i]);
            }
        }
        return leaders;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        int[] arr = {16, 17, 4, 3, 5, 2};
        System.out.println(s.findLeaders(arr)); // [17, 5, 2]
    }
}
```

> Note: The rightmost element is automatically handled — when `i` is the last index, the inner loop doesn't run, so it's always added as a leader.

---

## 2. ✅ Optimal — Traverse from Right

**Approach:** Traverse right to left, tracking the max seen so far. If current element > max → it's a leader.

| Time | Space |
|------|-------|
| **O(n)** | **O(n)** for result |

```java
import java.util.*;

public class Solution {
    public List<Integer> findLeaders(int[] arr) {
        List<Integer> leaders = new ArrayList<>();
        int n = arr.length;
        if (n == 0) return leaders;

        int maxFromRight = arr[n - 1];
        leaders.add(maxFromRight); // rightmost is always a leader

        // Traverse right to left
        for (int i = n - 2; i >= 0; i--) {
            if (arr[i] > maxFromRight) {
                leaders.add(arr[i]);
                maxFromRight = arr[i];
            }
        }

        // Reverse since we added in right-to-left order
        List<Integer> result = new ArrayList<>();
        for (int i = leaders.size() - 1; i >= 0; i--) {
            result.add(leaders.get(i));
        }
        return result;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        int[] arr = {16, 17, 4, 3, 5, 2};
        System.out.println(s.findLeaders(arr)); // [17, 5, 2]
    }
}
```

### Dry Run (`arr = [16, 17, 4, 3, 5, 2]`)

| i | arr[i] | maxFromRight | Leader? | leaders |
|---|--------|-------------|---------|---------|
| 5 | 2 | 2 | ✅ (rightmost) | [2] |
| 4 | 5 | 2 → 5 | ✅ (5 > 2) | [2, 5] |
| 3 | 3 | 5 | ❌ (3 < 5) | [2, 5] |
| 2 | 4 | 5 | ❌ (4 < 5) | [2, 5] |
| 1 | 17 | 5 → 17 | ✅ (17 > 5) | [2, 5, 17] |
| 0 | 16 | 17 | ❌ (16 < 17) | [2, 5, 17] |

After reversing: `[17, 5, 2]` ✅

---

## Quick Revision Summary

| # | Approach | Time | Space | Key Idea |
|---|----------|------|-------|----------|
| 1 | Brute Force | O(n²) | O(n) | Check all right elements for each |
| 2 | **Right Traversal** | **O(n)** | **O(n)** | **Track max from right, bigger = leader** |

> 🔑 **Key takeaway:** Traverse from right, maintain a running max. Any element greater than the current max is a leader. One pass, O(n).
