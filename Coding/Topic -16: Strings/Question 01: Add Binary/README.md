# Question 01: Add Binary

**Question:** 67. Add Binary

## Approach-1:

### Steps and Explanation:

1. **Initialization:** Initialize carry to 0. Use a StringBuilder to build the result. Initialize two pointers i and j to point to the last characters of strings a and b.

2. **Loop through the Strings:** Continue looping as long as there are characters left in either string or there is a carry to process.
   - Set sum to the current value of carry.
   - If there are characters left in string a, convert the current character to its integer value and add to sum. Decrement pointer i.
   - Do the same for string b using pointer j.

3. **Calculate Binary Digit:** The last binary digit is computed using `sum % 2`. Append to result.

4. **Update Carry:** Update carry using `sum / 2`.

5. **Final Output:** Reverse the result since we built it in reverse order.

### Time and Space Complexity:
- **Time Complexity:** O(max(m, n)), where m and n are the lengths of the input strings.
- **Space Complexity:** O(max(m, n)), due to the StringBuilder.

### Dry Run:

Input: a = "1010", b = "1011"

| Iteration | i | j | sum | lastBinaryDigit | carry | result |
|-----------|---|---|-----|-----------------|-------|--------|
| 1 | 3 | 3 | 0+0+1=1 | 1 | 0 | "1" |
| 2 | 2 | 2 | 0+1+1=2 | 0 | 1 | "10" |
| 3 | 1 | 1 | 1+0+0=1 | 1 | 0 | "101" |
| 4 | 0 | 0 | 0+1+1=2 | 0 | 1 | "1010" |
| 5 | — | — | 1 | 1 | 0 | "10101" |

After reversing: **"10101"**

### Code (without comments):

```java
public class Solution {
    public String addBinary(String a, String b) {
        int carry = 0;
        StringBuilder result = new StringBuilder();
        int i = a.length() - 1;
        int j = b.length() - 1;

        while (i >= 0 || j >= 0 || carry > 0) {
            int sum = carry;
            if (i >= 0) {
                sum += a.charAt(i) - '0';
                i--;
            }
            if (j >= 0) {
                sum += b.charAt(j) - '0';
                j--;
            }
            int lastBinaryDigit = sum % 2;
            result.append(lastBinaryDigit);
            carry = sum / 2;
        }
        return result.reverse().toString();
    }
}
```

### Code (with comments and main method):

```java
public class Solution {
    // Method to add two binary strings
    public String addBinary(String a, String b) {
        int carry = 0; // Initialize carry for addition
        StringBuilder result = new StringBuilder(); // StringBuilder to store the result
        int i = a.length() - 1; // Pointer for string a
        int j = b.length() - 1; // Pointer for string b

        // Loop until both strings are processed and there's no carry left
        while (i >= 0 || j >= 0 || carry > 0) {
            int sum = carry; // Start with the carry value

            if (i >= 0) { // If there are still digits in a
                sum += a.charAt(i) - '0'; // Convert char to int and add to sum
                i--; // Move the pointer left
            }
            if (j >= 0) { // If there are still digits in b
                sum += b.charAt(j) - '0'; // Convert char to int and add to sum
                j--; // Move the pointer left
            }

            int lastBinaryDigit = sum % 2; // Get the last binary digit (0 or 1)
            result.append(lastBinaryDigit); // Append the last binary digit to result
            carry = sum / 2; // Calculate the carry for the next iteration
        }

        return result.reverse().toString(); // Reverse the result and return as string
    }

    // Main method to test the addBinary method
    public static void main(String[] args) {
        Solution solution = new Solution();

        // Test case 1
        String a1 = "1010";
        String b1 = "1011";
        System.out.println(solution.addBinary(a1, b1)); // Expected output: "10101"

        // Test case 2
        String a2 = "11";
        String b2 = "1";
        System.out.println(solution.addBinary(a2, b2)); // Expected output: "100"

        // Test case 3
        String a3 = "0";
        String b3 = "0";
        System.out.println(solution.addBinary(a3, b3)); // Expected output: "0"
    }
}
```
