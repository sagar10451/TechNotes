# Question 16: Reverse Words in a String

**Question:** 151. Reverse Words in a String

## Approach-1: Brute Force

### Steps:
1. **Trim** the input string to remove leading/trailing whitespace.
2. **Split** the string into words using regex `\\s+` (one or more spaces).
3. **Iterate in reverse** through the words array, appending each word to StringBuilder with a space.
4. **Return** the result.

**Time Complexity:** O(n)
**Space Complexity:** O(n)

### Code:

```java
public class Solution {
    public String reverseWords(String s) {
        // Step 1: Trim and split the string into words
        String[] words = s.trim().split("\\s+");

        // Step 2: Create a StringBuilder to build the reversed words
        StringBuilder ans = new StringBuilder();

        // Step 3: Iterate through the array of words in reverse order
        for (int i = words.length - 1; i >= 0; i--) {
            ans.append(words[i]);
            if (i != 0) {
                ans.append(" ");
            }
        }

        // Step 4: Convert the StringBuilder to a String and return it
        return ans.toString();
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        System.out.println(solution.reverseWords("  hello world  ")); // "world hello"
        System.out.println(solution.reverseWords("a good   example")); // "example good a"
        System.out.println(solution.reverseWords("  Bob    Loves  Alice   ")); // "Alice Loves Bob"
        System.out.println(solution.reverseWords("Alice does not even like bob")); // "bob like even not does Alice"
    }
}
```

## Approach-2: Using Reverse (In-Place)

### Steps:
1. Convert string to char array.
2. Reverse the entire array.
3. Reverse each individual word.
4. Trim extra spaces and return.

**Time Complexity:** O(n)
**Space Complexity:** O(1) (in-place reversal)

### Code:

```java
public class Solution {
    public String reverseWords(String s) {
        char[] charArray = s.toCharArray();
        reverse(charArray, 0, charArray.length - 1);

        int start = 0;
        StringBuilder result = new StringBuilder();

        for (int end = 0; end < charArray.length; end++) {
            if (charArray[end] == ' ') {
                if (start < end) {
                    reverse(charArray, start, end - 1);
                    result.append(charArray, start, end - start).append(" ");
                }
                start = end + 1;
            }
        }

        if (start < charArray.length) {
            reverse(charArray, start, charArray.length - 1);
            result.append(charArray, start, charArray.length - start);
        }

        return result.toString().trim();
    }

    private void reverse(char[] arr, int left, int right) {
        while (left < right) {
            char temp = arr[left];
            arr[left] = arr[right];
            arr[right] = temp;
            left++;
            right--;
        }
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        System.out.println(solution.reverseWords("  hello world  ")); // "world hello"
        System.out.println(solution.reverseWords("a good   example")); // "example good a"
        System.out.println(solution.reverseWords("  Bob    Loves  Alice   ")); // "Alice Loves Bob"
        System.out.println(solution.reverseWords("Alice does not even like bob")); // "bob like even not does Alice"
    }
}
```
