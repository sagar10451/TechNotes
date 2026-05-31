# Question 12: Longest Common Prefix

**Question:** 14. Longest Common Prefix

## Approach-1: Brute Force

### Steps:
1. Edge case: if array is null or empty, return "".
2. Outer loop: iterate over each character of first string.
3. Inner loop: compare that character across all other strings.
4. If mismatch or string too short, return prefix found so far.
5. If no mismatch, return entire first string.

**Time Complexity:** O(n · m), where n = number of strings, m = length of shortest string.
**Space Complexity:** O(1)

### Code:

```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if (strs == null || strs.length == 0) {
            return "";
        }

        for (int i = 0; i < strs[0].length(); i++) {
            for (int j = 1; j < strs.length; j++) {
                if (i >= strs[j].length() || strs[j].charAt(i) != strs[0].charAt(i)) {
                    return strs[0].substring(0, i);
                }
            }
        }

        return strs[0];
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        String[] testStrings1 = {"flower", "flow", "flight"};
        System.out.println("Longest Common Prefix: " + solution.longestCommonPrefix(testStrings1)); // "fl"

        String[] testStrings2 = {"dog", "racecar", "car"};
        System.out.println("Longest Common Prefix: " + solution.longestCommonPrefix(testStrings2)); // ""

        String[] testStrings3 = {"single"};
        System.out.println("Longest Common Prefix: " + solution.longestCommonPrefix(testStrings3)); // "single"
    }
}
```

## Approach-2: Optimal (Sorting)

### Steps:
1. Sort the array lexicographically.
2. After sorting, only compare first (s1) and last (s2) strings — the max common prefix must be a prefix of both.
3. Compare characters of s1 and s2 until mismatch.

**Time Complexity:** O(n log n + m)
**Space Complexity:** O(1)

### Code:

```java
import java.util.Arrays;

class Solution {
    public String longestCommonPrefix(String[] strs) {
        Arrays.sort(strs);

        String s1 = strs[0];
        String s2 = strs[strs.length - 1];
        int idx = 0;

        while (idx < s1.length() && idx < s2.length()) {
            if (s1.charAt(idx) == s2.charAt(idx)) {
                idx++;
            } else {
                break;
            }
        }

        return s1.substring(0, idx);
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        String[] testStrings1 = {"flower", "flow", "flight"};
        System.out.println("Longest Common Prefix: " + solution.longestCommonPrefix(testStrings1)); // "fl"

        String[] testStrings2 = {"dog", "racecar", "car"};
        System.out.println("Longest Common Prefix: " + solution.longestCommonPrefix(testStrings2)); // ""
    }
}
```
