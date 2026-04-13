# LeetCode 202 — Happy Number

## Problem

A number is **happy** if repeatedly replacing it with the **sum of squares of its digits** eventually reaches `1`. If it loops endlessly in a cycle that never includes `1`, it's not happy.

**Example:**
`19` → 1² + 9² = `82` → 8² + 2² = `68` → 6² + 8² = `100` → 1² + 0² + 0² = `1` ✅ Happy!

---

## 1. HashSet (Cycle Detection with Set)

**Approach:**
1. Keep replacing `n` with the sum of squares of its digits.
2. Track every number seen in a **Set**.
3. If `n` becomes `1` → happy.
4. If `n` repeats (already in set) → cycle → not happy.

| Time | Space |
|------|-------|
| O(log n) | O(log n) — for the set |

```java
import java.util.*;

public class Solution {
    public boolean isHappy(int n) {
        Set<Integer> s = new HashSet<>();

        while (n != 1 && !s.contains(n)) {
            s.add(n);
            n = getNext(n);
        }
        return n == 1;
    }

    private int getNext(int n) {
        int totalSum = 0;
        while (n > 0) {
            int digit = n % 10;
            totalSum += digit * digit;
            n /= 10;
        }
        return totalSum;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.isHappy(19)); // true
        System.out.println(s.isHappy(2));  // false
    }
}
```

---

## 2. ✅ Optimal — Floyd's Tortoise and Hare

**Approach:** Same cycle detection as LeetCode 287 (Find Duplicate), but applied to the digit-sum sequence.
1. `slow` moves **one step** (one digit-sum).
2. `fast` moves **two steps** (two digit-sums).
3. If `fast` reaches `1` → happy.
4. If `slow == fast` (and not 1) → cycle → not happy.

| Time | Space |
|------|-------|
| **O(log n)** | **O(1)** — no extra storage |

```java
public class Solution {
    public boolean isHappy(int n) {
        int slow = n;
        int fast = getNext(n);

        while (fast != 1 && slow != fast) {
            slow = getNext(slow);           // 1 step
            fast = getNext(getNext(fast));   // 2 steps
        }
        return fast == 1;
    }

    private int getNext(int n) {
        int totalSum = 0;
        while (n > 0) {
            int digit = n % 10;
            totalSum += digit * digit;
            n /= 10;
        }
        return totalSum;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.isHappy(19)); // true
        System.out.println(s.isHappy(2));  // false
    }
}
```

### Dry Run (`n = 19`)

```
slow: 19 → 82 → 68 → ...
fast: 82 → 100 → 1
fast == 1 → return true ✅
```

### Dry Run (`n = 2`)

```
2 → 4 → 16 → 37 → 58 → 89 → 145 → 42 → 20 → 4 (cycle!)
slow and fast eventually meet → return false ❌
```

---

## Quick Revision Summary

| # | Approach | Time | Space | Key Idea |
|---|----------|------|-------|----------|
| 1 | HashSet | O(log n) | O(log n) | Track seen numbers, detect repeat |
| 2 | **Floyd's Cycle** | **O(log n)** | **O(1)** | **Slow/fast pointers on digit-sum sequence** |

> 🔑 **Key takeaway:** The digit-sum sequence either reaches `1` or enters a cycle — same pattern as linked list cycle detection. Floyd's algorithm eliminates the need for a set.
