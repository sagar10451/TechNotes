# Question 11: Length of Last Word

**Question:** 58. Length of Last Word

## Approach-1: Traverse from End

### Steps and Explanation:

1. **Initialization:** Variable `length` = 0, pointer `i` = s.length() - 1.
2. **Skip Trailing Spaces:** While loop decrements i until non-space character found.
3. **Count Last Word Length:** Second while loop counts characters until space or beginning of string.
4. **Return Result:** Return length.

**Time Complexity:** O(n)
**Space Complexity:** O(1)

### Dry Run:

Input: " fly me to the moon "
- Skip trailing spaces: i goes from end to 'm' of "moon"
- Count: 'm','o','o','n' → length = 4
- **Return 4**

### Code:

```java
class Solution {
    // Function to find the length of the last word in a given string.
    public int lengthOfLastWord(String s) {
        int length = 0; // Variable to store the length of the last word
        int i = s.length() - 1; // Start from the end of the string

        // Skip trailing spaces to find the end of the last word
        while (i >= 0 && s.charAt(i) == ' ') {
            i--;
        }

        // Count the length of the last word
        while (i >= 0 && s.charAt(i) != ' ') {
            length++; // Increase the length for each character in the last word
            i--; // Move to the previous character
        }

        return length; // Return the length of the last word
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        String test1 = "Hello World"; // Expected output: 5
        String test2 = "   fly me   to   the moon  "; // Expected output: 4
        String test3 = "luffy is still joyboy"; // Expected output: 6
        String test4 = ""; // Expected output: 0
        String test5 = "   "; // Expected output: 0

        System.out.println("Length of last word in '" + test1 + "': " + solution.lengthOfLastWord(test1));
        System.out.println("Length of last word in '" + test2 + "': " + solution.lengthOfLastWord(test2));
        System.out.println("Length of last word in '" + test3 + "': " + solution.lengthOfLastWord(test3));
        System.out.println("Length of last word in '" + test4 + "': " + solution.lengthOfLastWord(test4));
        System.out.println("Length of last word in '" + test5 + "': " + solution.lengthOfLastWord(test5));
    }
}
```
