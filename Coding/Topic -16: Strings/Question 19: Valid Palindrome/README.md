# Question 19: Valid Palindrome

**Question:** 125. Valid Palindrome

## 1. Brute Force:

**Steps:**
1. Create a filtered string with only alphanumeric characters (lowercase).
2. Compare filtered string to its reverse.

**Time:** O(n), **Space:** O(n)

## 2. Optimal (Two Pointers):

**Steps:**
1. Initialize left and right pointers.
2. Skip non-alphanumeric characters.
3. Compare characters (case-insensitive).
4. If mismatch → return false. If pointers cross → return true.

**Time:** O(n), **Space:** O(1)

### Code:

```java
public class Solution {
    public boolean isPalindrome(String s) {
        int left = 0, right = s.length() - 1;

        while (left < right) {
            while (left < right && !isAlphanumeric(s.charAt(left))) {
                left++;
            }
            while (left < right && !isAlphanumeric(s.charAt(right))) {
                right--;
            }

            if (Character.toLowerCase(s.charAt(left)) != Character.toLowerCase(s.charAt(right))) {
                return false;
            }
            left++;
            right--;
        }
        return true;
    }

    private boolean isAlphanumeric(char c) {
        return (c >= 'a' && c <= 'z') || (c >= 'A' && c <= 'Z') || (c >= '0' && c <= '9');
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        System.out.println(solution.isPalindrome("A man, a plan, a canal: Panama")); // true
        System.out.println(solution.isPalindrome("race a car")); // false
        System.out.println(solution.isPalindrome(" ")); // true
    }
}
```
