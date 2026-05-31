# Question 18: Valid Anagram

**Question:** 242. Valid Anagram

## Approach 1: Sorting, T:O(n log n)

1. Store string into char array for both strings.
2. Sort both strings.
3. Compare if both are equal.

```java
import java.util.Arrays;

class Solution {
    public boolean isAnagram(String s, String t) {
        char[] sChars = s.toCharArray();
        char[] tChars = t.toCharArray();
        Arrays.sort(sChars);
        Arrays.sort(tChars);
        return Arrays.equals(sChars, tChars);
    }
}
```

## Approach-2: Map Based, T:O(n), S:O(n)

**Steps:**
1. Iterate through string 1 and increment frequency in map.
2. Iterate through string 2 and decrement frequency in map.
3. At the end check if for any character count != 0 then return false.

```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    public boolean isAnagram(String s, String t) {
        Map<Character, Integer> count = new HashMap<>();

        for (int i = 0; i < s.length(); i++) {
            char x = s.charAt(i);
            count.put(x, count.getOrDefault(x, 0) + 1);
        }

        for (int i = 0; i < t.length(); i++) {
            char x = t.charAt(i);
            count.put(x, count.getOrDefault(x, 0) - 1);
        }

        for (int val : count.values()) {
            if (val != 0) {
                return false;
            }
        }

        return true;
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        System.out.println(solution.isAnagram("anagram", "nagaram")); // true
        System.out.println(solution.isAnagram("rat", "car")); // false
        System.out.println(solution.isAnagram("listen", "silent")); // true
        System.out.println(solution.isAnagram("a", "ab")); // false
    }
}
```

## Approach-3: T:O(n), S:O(26)

Instead of hashmap we use char array of size 26, same logic as Approach-2.

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        int[] count = new int[26];

        for (int i = 0; i < s.length(); i++) {
            char x = s.charAt(i);
            count[x - 'a']++;
        }

        for (int i = 0; i < t.length(); i++) {
            char x = t.charAt(i);
            count[x - 'a']--;
        }

        for (int i = 0; i < count.length; i++) {
            if (count[i] != 0) {
                return false;
            }
        }

        return true;
    }
}
```
