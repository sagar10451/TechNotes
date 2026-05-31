# Question 13: Ransom Note

**Question:** 383. Ransom Note

## 1. Brute Force Solution (using HashMap)

### Steps:
1. Create HashMap with character frequencies from magazine.
2. For each character in ransomNote, check if available in map. If not, return false. If yes, decrement count.
3. If all characters matched, return true.

**Time Complexity:** O(m + n)
**Space Complexity:** O(m)

### Code:

```java
import java.util.HashMap;

class Solution {
    public boolean canConstruct(String ransomNote, String magazine) {
        HashMap<Character, Integer> magazineMap = new HashMap<>();

        char[] magazineChars = magazine.toCharArray();
        for (int i = 0; i < magazineChars.length; i++) {
            char c = magazineChars[i];
            if (magazineMap.containsKey(c)) {
                magazineMap.put(c, magazineMap.get(c) + 1);
            } else {
                magazineMap.put(c, 1);
            }
        }

        char[] ransomNoteChars = ransomNote.toCharArray();
        for (int i = 0; i < ransomNoteChars.length; i++) {
            char c = ransomNoteChars[i];
            if (!magazineMap.containsKey(c) || magazineMap.get(c) == 0) {
                return false;
            }
            magazineMap.put(c, magazineMap.get(c) - 1);
        }

        return true;
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        System.out.println(solution.canConstruct("a", "b")); // false
        System.out.println(solution.canConstruct("aa", "ab")); // false
        System.out.println(solution.canConstruct("aa", "aab")); // true
    }
}
```

## 2. Optimal Solution (Using Frequency Count Array)

### Steps:
1. Create int array of size 26 for character frequencies.
2. Populate from magazine.
3. For each character in ransomNote, check if count is 0 → return false. Otherwise decrement.

**Time Complexity:** O(m + n)
**Space Complexity:** O(1) — fixed size array of 26.

### Code:

```java
class Solution {
    public boolean canConstruct(String ransomNote, String magazine) {
        int[] charCount = new int[26];

        char[] magazineChars = magazine.toCharArray();
        for (int i = 0; i < magazineChars.length; i++) {
            char c = magazineChars[i];
            charCount[c - 'a']++;
        }

        char[] ransomNoteChars = ransomNote.toCharArray();
        for (int i = 0; i < ransomNoteChars.length; i++) {
            char c = ransomNoteChars[i];
            if (charCount[c - 'a'] == 0) {
                return false;
            }
            charCount[c - 'a']--;
        }

        return true;
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        System.out.println(solution.canConstruct("a", "b")); // false
        System.out.println(solution.canConstruct("aa", "ab")); // false
        System.out.println(solution.canConstruct("aa", "aab")); // true
    }
}
```
