
# Question 04: Compare Version Numbers

**Question:** 165. Compare Version Numbers

## Approach-1:

### Steps and Explanation:

1. **Input Handling:** Takes two version strings separated by dots.
2. **Initialization:** Pointers i and j to 0. Variables num1 and num2 for extracted numbers.
3. **Main While Loop:** Continues while characters remain in either string. Extracts numbers between dots.
4. **Extracting Numbers:** Inner while loops extract numbers character by character.
5. **Comparison:** After extracting, compare num1 and num2. Return 1, -1, or continue.
6. **Return Value:** If loop finishes without inequality, return 0.

### Time and Space Complexity:
- **Time Complexity:** O(n + m)
- **Space Complexity:** O(1)

### DRY Run:

Input: "1.01" vs "1.001"
- First iteration: num1=1, num2=1 → equal, continue
- Second iteration: num1=01=1, num2=001=1 → equal, continue
- End of strings → **Return 0**

### Code:

```java
class Solution {
    // Method to compare two version numbers represented as strings
    public int compareVersion(String version1, String version2) {
        int size1 = version1.length(); // Length of version1 string
        int size2 = version2.length(); // Length of version2 string
        int i = 0, j = 0; // Pointers to traverse both version strings

        // Continue looping while there are characters left in either version1 or version2
        while (i < size1 || j < size2) {
            int num1 = 0; // To store the number extracted from version1
            int num2 = 0; // To store the number extracted from version2

            // Extract number from version1 until we hit a '.' or reach the end
            while (i < size1 && version1.charAt(i) != '.') {
                num1 = num1 * 10 + (version1.charAt(i) - '0'); // Convert character to integer
                i++;
            }

            // Extract number from version2 until we hit a '.' or reach the end
            while (j < size2 && version2.charAt(j) != '.') {
                num2 = num2 * 10 + (version2.charAt(j) - '0'); // Convert character to integer
                j++;
            }

            // Compare the two numbers
            if (num1 > num2) {
                return 1; // If version1's number is greater, return 1
            } else if (num1 < num2) {
                return -1; // If version2's number is greater, return -1
            }

            // If the numbers are equal, move past the '.' in both strings
            i++;
            j++;
        }

        // If all version numbers are equal, return 0
        return 0;
    }

    // Main method to test the solution
    public static void main(String[] args) {
        Solution solution = new Solution();

        // Test case 1: Version 1.01 and 1.001 should be equal
        String version1 = "1.01";
        String version2 = "1.001";
        System.out.println(solution.compareVersion(version1, version2)); // Output: 0

        // Test case 2: Version 0.1 is smaller than version 1.1
        String version1_2 = "0.1";
        String version2_2 = "1.1";
        System.out.println(solution.compareVersion(version1_2, version2_2)); // Output: -1
    }
}
```

