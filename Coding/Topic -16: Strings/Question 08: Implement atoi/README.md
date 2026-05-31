
# Question 08: Implement atoi

**Question:** 8. String to Integer (atoi)

## Approach-1:

### Explanation:

1. **Input Check:** If string is empty, return 0.
2. **Skip Leading Spaces:** While loop to skip whitespace.
3. **Sign Detection:** Check for '+' or '-'. Set sign accordingly.
4. **Digit Conversion:** While loop converts digits. Check overflow before adding.
5. **Return:** result * sign.

### Time Complexity: O(n)
### Space Complexity: O(1)

### Dry Run:

Input: "   -42"
- Skip spaces: i goes from 0 to 3
- Sign detection: '-' found, sign = -1, i = 4
- Digit conversion: '4' → result=4, '2' → result=42
- Return: 42 * -1 = **-42**

### Code:

```java
class Solution {
    public int myAtoi(String s) {
        // Check if the input string is empty
        if (s.length() == 0) {
            return 0;
        }

        int i = 0;
        int sign = 1;
        int result = 0;

        // Skip leading spaces
        while (i < s.length() && s.charAt(i) == ' ') {
            i++;
        }

        // Determine the sign of the number
        if (i < s.length() && (s.charAt(i) == '+' || s.charAt(i) == '-')) {
            if (s.charAt(i) == '-') {
                sign = -1;
            }
            i++;
        }

        // Convert the string to an integer until a non-digit character is encountered
        while (i < s.length() && Character.isDigit(s.charAt(i))) {
            int digit = s.charAt(i) - '0';

            // Check for overflow before adding the next digit
            if (result > (Integer.MAX_VALUE - digit) / 10) {
                return sign == 1 ? Integer.MAX_VALUE : Integer.MIN_VALUE;
            }

            result = result * 10 + digit;
            i++;
        }

        return result * sign;
    }

    // Main method to test the myAtoi function
    public static void main(String[] args) {
        Solution solution = new Solution();

        System.out.println(solution.myAtoi("42")); // Expected output: 42
        System.out.println(solution.myAtoi("   -42")); // Expected output: -42
        System.out.println(solution.myAtoi("4193 with words")); // Expected output: 4193
        System.out.println(solution.myAtoi("2147483648")); // Expected output: 2147483647 (Integer.MAX_VALUE)
        System.out.println(solution.myAtoi("-2147483649")); // Expected output: -2147483648 (Integer.MIN_VALUE)
        System.out.println(solution.myAtoi("")); // Expected output: 0
        System.out.println(solution.myAtoi("    ")); // Expected output: 0
    }
}
```

