# Question 03: Roman to Integer

**Question:** https://leetcode.com/problems/integer-to-roman/description/

## Approach-1: Use map to store mapping.

**Logic:** If a smaller numeral comes before a larger numeral, it should be subtracted (e.g., IV is 4, where I is subtracted from V). Otherwise, it should be added (e.g., VI is 6, where I is added to V).

1. Store character and values associated with it in hashmap
2. Iterate over string and apply above logic

**TC:** O(n) where n is length of string
**SC:** O(7) which can be treated as constant. O(1)

### Code:

```java
import java.util.*;

class Solution {
    public int romanToInt(String s) {
        // Map to store Roman numeral values
        Map<Character, Integer> m = new HashMap<>();
        m.put('I', 1);
        m.put('V', 5);
        m.put('X', 10);
        m.put('L', 50);
        m.put('C', 100);
        m.put('D', 500);
        m.put('M', 1000);

        int result = 0;
        int n = s.length();

        // Loop through the string
        for (int i = 0; i < n; i++) {
            //first we have condition to check out of bounds
            //m.get(s.charAt(i)) = from map we are getting, what is the value stored for current character
            // If the current numeral is smaller than the next one, subtract it
            if (i + 1 < n && m.get(s.charAt(i)) < m.get(s.charAt(i + 1))) {
                result -= m.get(s.charAt(i));
            } else {
                // Otherwise, add the current numeral value
                result += m.get(s.charAt(i));
            }
        }

        return result;
    }

    public static void main(String[] args) {
        Solution sol = new Solution();
        String roman = "MCMXCIV"; // Example: Roman numeral for 1994
        System.out.println(sol.romanToInt(roman));  // Output: 1994
    }
}
```
