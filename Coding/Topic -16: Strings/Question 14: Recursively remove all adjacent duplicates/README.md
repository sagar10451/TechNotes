# Question 14: Recursively Remove All Adjacent Duplicates

**2 Variants:**
1. **LeetCode (1047):** Remove TWO adjacent duplicates. E.g., "abccbccba" → "abbba" → "aba"
2. **GeeksforGeeks:** Remove ALL adjacent duplicates. E.g., "abccbccba" → "abbba" → "aa" → "" (empty)

---

## LeetCode Variant — Approach-1: Stack and StringBuilder

### Steps:
1. Use stack. For each character: if top matches, pop (remove duplicate). Otherwise push.
2. After processing, pop all from stack and reverse to get result.

**Time Complexity:** O(n)
**Space Complexity:** O(n)

### Code:

```java
import java.util.Stack;

class Solution {
    public String removeDuplicates(String s) {
        Stack<Character> st = new Stack<>();

        for(int i = 0; i < s.length(); i++) {
            if(!st.isEmpty()) {
                if(st.peek() == s.charAt(i)) {
                    st.pop();
                } else {
                    st.push(s.charAt(i));
                }
            } else {
                st.push(s.charAt(i));
            }
        }

        StringBuilder res = new StringBuilder();
        while(!st.isEmpty()) {
            res.append(st.pop());
        }

        return res.reverse().toString();
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        System.out.println(solution.removeDuplicates("abbaca"));  // "ca"
        System.out.println(solution.removeDuplicates("azxxzy"));  // "ay"
        System.out.println(solution.removeDuplicates("abccbccba"));  // "aba"
    }
}
```

## LeetCode Variant — Approach-2: Only StringBuilder

### Code:

```java
class Solution {
    public String removeDuplicates(String S) {
        StringBuilder sb = new StringBuilder();
        int index = 0;

        for (int i = 0; i < S.length(); i++) {
            char ch = S.charAt(i);

            if (sb.length() == 0) {
                sb.append(ch);
            } else {
                if (sb.charAt(index - 1) == ch) {
                    sb.deleteCharAt(index - 1);
                } else {
                    sb.append(ch);
                }
            }
            index = sb.length();
        }

        return sb.toString();
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        System.out.println(solution.removeDuplicates("abbaca")); // "ca"
        System.out.println(solution.removeDuplicates("azxxzy")); // "ay"
    }
}
```

## GeeksforGeeks Variant — Approach: Recursion

### Code:

```java
class Solution {
    String rremove(String s) {
        StringBuilder sb = new StringBuilder();

        if (s.length() == 1) {
            return s;
        }

        for (int i = 0; i < s.length(); i++) {
            if (i == 0) {
                if (s.charAt(i) != s.charAt(i + 1)) {
                    sb.append(s.charAt(i));
                }
            } else if (i == s.length() - 1) {
                if (s.charAt(i) != s.charAt(i - 1)) {
                    sb.append(s.charAt(i));
                }
            } else {
                if (s.charAt(i) != s.charAt(i + 1) && s.charAt(i) != s.charAt(i - 1)) {
                    sb.append(s.charAt(i));
                }
            }
        }

        if (s.equals(sb.toString())) {
            return s;
        } else {
            return rremove(sb.toString());
        }
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        System.out.println(solution.rremove("abbaca"));  // "ca"
        System.out.println(solution.rremove("azxxzy"));  // "ay"
        System.out.println(solution.rremove("abccbccba"));  // ""
    }
}
```
