
# Question 09: Is Subsequence

**Question:** 392. Is Subsequence

## 1. Brute Force Solution (Two Pointer Approach — explain this)

**Approach:**
- Use two pointers to traverse both strings s and t.
- If s[i] == t[j], move i to the next character.
- Always move j to the next character.
- If i == len(s) at the end, return true.

**Time Complexity:** O(n), where n is the length of t.
**Space Complexity:** O(1)

## 2. Optimal Solution (Binary Search Approach for Multiple s Queries)

**Approach:**
- Preprocess t by storing indices of each character.
- For each character in s, use binary search to find the next valid index in t.

**Time Complexity:** O(m + n log m)
**Space Complexity:** O(n)

---

### 1. Brute Force Solution (CODE)

```java
import java.util.*;

public class IsSubsequence_BruteForce {
    public static boolean isSubsequence(String s, String t) {
        int i = 0, j = 0;
        while (i < s.length() && j < t.length()) {
            if (s.charAt(i) == t.charAt(j)) {
                i++;
            }
            j++;
        }
        return i == s.length();
    }

    public static void main(String[] args) {
        String s = "abc";
        String t = "ahbgdc";
        System.out.println(isSubsequence(s, t));  // Output: true

        s = "axc";
        t = "ahbgdc";
        System.out.println(isSubsequence(s, t));  // Output: false
    }
}
```

### 2. Optimal Solution (Binary Search) (CODE)

```java
import java.util.*;

public class IsSubsequence_Optimal {
    public static boolean isSubsequence(String s, String t) {
        // Preprocess the indices of characters in t
        Map<Character, List<Integer>> charIndicesMap = new HashMap<>();
        for (int i = 0; i < t.length(); i++) {
            charIndicesMap.putIfAbsent(t.charAt(i), new ArrayList<>());
            charIndicesMap.get(t.charAt(i)).add(i);
        }

        // Previous index in t
        int prevIndex = -1;

        // Iterate through each character in s
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (!charIndicesMap.containsKey(c)) {
                return false; // Character not found in t
            }

            // Find the next index in t using binary search
            List<Integer> indices = charIndicesMap.get(c);
            int nextIndex = findNextIndex(indices, prevIndex);
            if (nextIndex == -1) {
                return false; // No valid index found
            }
            prevIndex = nextIndex;
        }

        return true;
    }

    // Binary search to find the next valid index
    private static int findNextIndex(List<Integer> indices, int prevIndex) {
        int left = 0, right = indices.size();
        while (left < right) {
            int mid = (left + right) / 2;
            if (indices.get(mid) > prevIndex) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        return left == indices.size() ? -1 : indices.get(left);
    }

    public static void main(String[] args) {
        String s = "abc";
        String t = "ahbgdc";
        System.out.println(isSubsequence(s, t));  // Output: true

        s = "axc";
        t = "ahbgdc";
        System.out.println(isSubsequence(s, t));  // Output: false
    }
}
```
