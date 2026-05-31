# Question 02: Backspace String Compare

**Question:** 844. Backspace String Compare

## Approach-1: 2 Stack

### Explanation of Steps:

**buildString Method:**
- Processes the input string and simulates the effect of backspace characters (#).
- Uses a stack: normal characters are pushed, # causes a pop (if stack not empty).
- After processing, the stack contains the final characters.
- Stack is converted back into a string using StringBuilder.

**backspaceCompare Method:**
- Compares two strings after both are processed by buildString.

### Time Complexity:
- O(n + m), where n and m are the lengths of strings s and t.

### Space Complexity:
- O(n + m) for the stacks.

### Dry Run:

Test Case 1: s = "ab#c", t = "ad#c"
- Processing s: Stack: [] → ['a'] → ['a','b'] → # pop → ['a'] → ['a','c']. Result: "ac"
- Processing t: Stack: [] → ['a'] → ['a','d'] → # pop → ['a'] → ['a','c']. Result: "ac"
- Comparing "ac" and "ac": **true**

### Code (without comments):

```java
public class Solution {
    private String solve(String s) {
        Stack<Character> stack = new Stack<>();
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c != '#') {
                stack.push(c);
            } else {
                if(!stack.isEmpty()) stack.pop();
            }
        }
        StringBuilder result = new StringBuilder();
        for (int i = 0; i < stack.size(); i++) {
            result.append(stack.get(i));
        }
        return result.toString();
    }

    public boolean backspaceCompare(String s, String t) {
        return solve(s).equals(solve(t));
    }
}
```

### Code (with Comments):

```java
import java.util.Stack;

public class Solution {
    // Helper function to process the string and simulate backspaces
    private String buildString(String s) {
        Stack<Character> stack = new Stack<>();

        // Using normal for loop to iterate through the string
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c != '#') {
                stack.push(c); // Push the character if it's not a backspace
            } else {
                if(!stack.isEmpty()) stack.pop(); // Pop the last character for a backspace, if stack is not empty
            }
        }

        // Convert the stack into a string using StringBuilder
        StringBuilder result = new StringBuilder();
        for (int i = 0; i < stack.size(); i++) {
            result.append(stack.get(i));
        }
        return result.toString();
    }

    public boolean backspaceCompare(String s, String t) {
        // Process both strings using the helper function
        return buildString(s).equals(buildString(t));
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        // Test case 1
        String s1 = "ab#c";
        String t1 = "ad#c";
        System.out.println(solution.backspaceCompare(s1, t1)); // Expected output: true

        // Test case 2
        String s2 = "a#c";
        String t2 = "b";
        System.out.println(solution.backspaceCompare(s2, t2)); // Expected output: false
    }
}
```

## Approach-2: Two Pointers Approach (baad me dekhenge, bahut tough hai java me)

1. We have to iterate both the string from end. Why? Because from left there could be # we dont know.
2. If we encounter # we skip one element left to it. Because that will get deleted with backspace.
3. So we keep comparing.
