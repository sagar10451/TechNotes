
# Question 15: Remove Duplicates (Preserve First Occurrence)

**Source:** https://www.geeksforgeeks.org/problems/remove-duplicates3034/1

## Approach-1: Using HashSet

**Time:** O(n), **Space:** O(m) where m = unique characters.

### Code:

```java
import java.util.HashSet;

public class Solution {
    public String removeDuplicates(String s) {
        HashSet<Character> seen = new HashSet<>();
        StringBuilder result = new StringBuilder();

        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (!seen.contains(c)) {
                seen.add(c);
                result.append(c);
            }
        }

        return result.toString();
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        System.out.println(solution.removeDuplicates("zvvo")); // "zvo"
        System.out.println(solution.removeDuplicates("gfg"));  // "gf"
    }
}
```

## Approach-2: Using Boolean Array (ASCII)

**Time:** O(n), **Space:** O(1) — fixed 128 size array.

### Code:

```java
import java.util.Vector;

public class Solution {
    public String removeDuplicates(String s) {
        Vector<Boolean> seen = new Vector<>(128);
        for (int i = 0; i < 128; i++) {
            seen.add(false);
        }

        StringBuilder result = new StringBuilder();

        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (!seen.get(c)) {
                seen.set(c, true);
                result.append(c);
            }
        }

        return result.toString();
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        System.out.println(solution.removeDuplicates("zvvo")); // "zvo"
        System.out.println(solution.removeDuplicates("gfg"));  // "gf"
    }
}
```
