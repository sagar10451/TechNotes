# Question 03: Check if String is Rotated by 2 Places

Given two strings a and b. The task is to find if a string 'a' can be obtained by rotating another string 'b' by 2 places.

## Approach-1: Extra Space

### Explanation of Steps:

1. **Length Check:** If lengths of a and b are not equal, return 0.
2. **Rotation Logic:** Two new strings are constructed:
   - `rotatedClockwise`: Last two characters of b placed at the front.
   - `rotatedAntiClockwise`: First two characters of b placed at the end.
3. **Matching:** Check if a equals either rotated version.

### Time and Space Complexity:
- **Time Complexity:** O(n) for each test case.
- **Space Complexity:** O(n) for the rotated strings.

### Code:

```java
class Solution {
    // Method to check if string 'a' can be obtained by rotating string 'b' by 2 places
    public static int canBeObtainedByRotation(String a, String b) {
        // Step 1: Check if lengths of both strings are equal
        if (a.length() != b.length()) {
            return 0; // Return 0 if lengths are not equal
        }

        // Step 2: Create rotated versions of 'b'
        // Rotate 'b' clockwise by 2 places
        String rotatedClockwise = b.substring(b.length() - 2) + b.substring(0, b.length() - 2);
        // Rotate 'b' anti-clockwise by 2 places
        String rotatedAntiClockwise = b.substring(2) + b.substring(0, 2);

        // Step 3: Check if 'a' matches any of the rotated versions of 'b'
        if (a.equals(rotatedClockwise) || a.equals(rotatedAntiClockwise)) {
            return 1; // Return 1 if 'a' matches either rotation
        }

        return 0; // Return 0 if 'a' cannot be obtained by rotating 'b'
    }

    public static void main(String[] args) {
        // Hardcoded test cases
        String[][] testCases = {
            {"amazon", "azonam"},       // Expected output: 1
            {"geeksforgeeks", "geeksgeeksfor"}, // Expected output: 0
            {"hello", "llohe"},         // Expected output: 1
            {"world", "ldwor"},         // Expected output: 1
            {"test", "estt"},           // Expected output: 0
        };

        // Step 4: Process each test case
        for (String[] testCase : testCases) {
            String a = testCase[0]; // First string
            String b = testCase[1]; // Second string
            System.out.println("Input a: \"" + a + "\", b: \"" + b + "\" -> Output: " + canBeObtainedByRotation(a, b));
        }
    }
}
```

## Approach-2: No Extra Space

### Explanation:
Instead of creating new rotated strings, we directly check if a matches the rotated versions of b using substring comparisons inline.

### Time and Space Complexity:
- **Time Complexity:** O(n)
- **Space Complexity:** O(1) (constant space — no extra stored strings)

### Code:

```java
class Solution {
    public static int canBeObtainedByRotation(String a, String b) {
        if (a.length() != b.length()) {
            return 0;
        }

        int n = b.length();

        // Rotated Clockwise by 2: last 2 characters come to the front
        if (a.equals(b.substring(n - 2) + b.substring(0, n - 2))) {
            return 1;
        }

        // Rotated Anti-Clockwise by 2: first 2 characters go to the end
        if (a.equals(b.substring(2) + b.substring(0, 2))) {
            return 1;
        }

        return 0;
    }

    public static void main(String[] args) {
        String[][] testCases = {
            {"amazon", "azonam"},
            {"geeksforgeeks", "geeksgeeksfor"},
            {"hello", "llohe"},
            {"world", "ldwor"},
            {"test", "estt"},
        };

        for (String[] testCase : testCases) {
            String a = testCase[0];
            String b = testCase[1];
            System.out.println("Input a: \"" + a + "\", b: \"" + b + "\" -> Output: " + canBeObtainedByRotation(a, b));
        }
    }
}
```
