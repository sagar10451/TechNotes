
# Question 17: Roman to Integer

**Question:** 13. Roman to Integer

## Approach-1: Use Map to Store Mapping

**Logic:** If a smaller numeral comes before a larger numeral, it should be subtracted (e.g., IV is 4). Otherwise, it should be added (e.g., VI is 6).

**Steps:**
1. Store character and values in hashmap.
2. Iterate over string and apply above logic.

**TC:** O(n), **SC:** O(1)

### Dry Run:

Input: "MCMXCIV"
- M(1000) + C<M(-100) + M(1000) + X<C(-10) + C(100) + I<V(-1) + V(5)
- = 1000 - 100 + 1000 - 10 + 100 - 1 + 5 = **1994**

### Code:

```java
import java.util.HashMap;

class Solution {
    public int romanToInt(String s) {
        HashMap<Character, Integer> m = new HashMap<>();
        m.put('I', 1);
        m.put('V', 5);
        m.put('X', 10);
        m.put('L', 50);
        m.put('C', 100);
        m.put('D', 500);
        m.put('M', 1000);

        int result = 0;
        int n = s.length();

        for (int i = 0; i < n; i++) {
            if (i + 1 < n && m.get(s.charAt(i)) < m.get(s.charAt(i + 1))) {
                result -= m.get(s.charAt(i));
            } else {
                result += m.get(s.charAt(i));
            }
        }

        return result;
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        System.out.println(solution.romanToInt("III"));       // 3
        System.out.println(solution.romanToInt("IV"));        // 4
        System.out.println(solution.romanToInt("IX"));        // 9
        System.out.println(solution.romanToInt("LVIII"));     // 58
        System.out.println(solution.romanToInt("MCMXCIV"));   // 1994
    }
}
```
