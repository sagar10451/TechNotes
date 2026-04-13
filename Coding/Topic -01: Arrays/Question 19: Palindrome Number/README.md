# LeetCode 9 — Palindrome Number

## Problem

Check if an integer is a **palindrome** — reads the same forwards and backwards. Negative numbers are **not** palindromes.

**Examples:**
- `121` → `true`
- `-121` → `false`
- `10` → `false`
- `0` → `true`

---

## 1. String Conversion

**Approach:** Convert to string, compare characters from both ends towards the middle.

| Time | Space |
|------|-------|
| O(n) | O(n) — string storage |

> Where n = number of digits.

```java
class Solution {
    public boolean isPalindrome(int x) {
        String s = String.valueOf(x);
        int n = s.length();

        for (int i = 0; i < n / 2; i++) {
            if (s.charAt(i) != s.charAt(n - i - 1)) return false;
        }
        return true;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.isPalindrome(121));   // true
        System.out.println(s.isPalindrome(-121));  // false
        System.out.println(s.isPalindrome(10));    // false
        System.out.println(s.isPalindrome(12321)); // true
        System.out.println(s.isPalindrome(0));     // true
    }
}
```

---

## 2. ✅ Optimal — Reverse Half the Number

**Key Idea:** Reverse only the **second half** of the number and compare with the first half. No string conversion, no overflow risk.

**Approach:**
1. **Edge cases:** Negative → false. Ends with 0 (and not 0 itself) → false.
2. **Reverse half:** Keep extracting last digit of `x` and building `reversedHalf` until `reversedHalf >= x`.
3. **Compare:**
   - Even length: `x == reversedHalf`
   - Odd length: `x == reversedHalf / 10` (ignore middle digit)

| Time | Space |
|------|-------|
| **O(log₁₀(n))** | **O(1)** |

```java
class Solution {
    public boolean isPalindrome(int x) {
        // Negative or ends with 0 (but not 0 itself)
        if (x < 0) return false;
        if (x % 10 == 0 && x != 0) return false;

        int reversedHalf = 0;

        // Reverse second half
        while (x > reversedHalf) {
            reversedHalf = reversedHalf * 10 + x % 10;
            x /= 10;
        }

        // Even length: x == reversedHalf
        // Odd length: x == reversedHalf / 10 (drop middle digit)
        return x == reversedHalf || x == reversedHalf / 10;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.isPalindrome(121));   // true
        System.out.println(s.isPalindrome(-121));  // false
        System.out.println(s.isPalindrome(10));    // false
        System.out.println(s.isPalindrome(12321)); // true
        System.out.println(s.isPalindrome(0));     // true
    }
}
```

### Dry Run (`x = 12321`, odd length)

| Iteration | x | reversedHalf | Action |
|-----------|---|-------------|--------|
| start | 12321 | 0 | — |
| 1 | 1232 | 1 | extract 1 |
| 2 | 123 | 12 | extract 2 |
| 3 | 12 | 123 | extract 3 |

Loop ends: `x (12) < reversedHalf (123)`

Check: `x == reversedHalf / 10` → `12 == 123 / 10` → `12 == 12` ✅ Palindrome!

### Dry Run (`x = 1221`, even length)

| Iteration | x | reversedHalf | Action |
|-----------|---|-------------|--------|
| start | 1221 | 0 | — |
| 1 | 122 | 1 | extract 1 |
| 2 | 12 | 12 | extract 2 |

Loop ends: `x (12) == reversedHalf (12)`

Check: `x == reversedHalf` → `12 == 12` ✅ Palindrome!

### Dry Run (`x = 10`)

Edge case: `x % 10 == 0 && x != 0` → `false` immediately ✅

---

## Quick Revision Summary

| # | Approach | Time | Space | Key Idea |
|---|----------|------|-------|----------|
| 1 | String | O(n) | O(n) | Convert, compare from both ends |
| 2 | **Reverse Half** | **O(log₁₀(n))** | **O(1)** | **Reverse second half, compare with first half** |

> 🔑 **Key takeaway:** Only reverse half the number — when `reversedHalf >= x`, stop. Compare directly for even length, or `reversedHalf / 10` for odd length (drops the middle digit). No overflow, no extra space.
