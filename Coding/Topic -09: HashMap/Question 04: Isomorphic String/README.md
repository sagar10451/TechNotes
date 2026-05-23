# Question 04: Isomorphic Strings

**Question:** 205. Isomorphic Strings

Given two strings s and t, determine if they are isomorphic. Two strings s and t are isomorphic if the characters in s can be replaced to get t. All occurrences of a character must be replaced with another character while preserving the order of characters. No two characters may map to the same character, but a character may map to itself.

## 1. Optimal Solution (Hash Map)

**Brief Explanation:**
- The function checks whether two strings s and t are isomorphic, meaning the characters in s can be mapped to characters in t consistently without conflicts.
- We use a HashMap to store character mappings from s to t.
- For each character in s, we check if:
  - The current character in s is already mapped to the correct character in t.
  - The character in t is not already mapped by another character from s.
- If these conditions hold for the entire string, the strings are isomorphic, otherwise they are not.

**Time and Space Complexity:**
- **Time Complexity:** O(n), where n is the length of the strings. We traverse the strings once.
- **Space Complexity:** O(n), due to the storage of mappings in the HashMap, which in the worst case stores one entry for each character.

### Code:

```java
import java.util.*;

class Solution {
    public static boolean isIsomorphic(String s, String t) {
        // If the lengths of the two strings are not the same, they can't be isomorphic
        if(s.length() != t.length()) return false;

        // Create a HashMap to store the mapping of characters from 's' to 't'
        HashMap<Character, Character> hm = new HashMap<>();

        // Iterate through each character in the strings
        for(int i = 0; i < s.length(); i++) {
            char sChar = s.charAt(i); // Get the character from string 's'
            char tChar = t.charAt(i); // Get the corresponding character from string 't'

            // If the character from 's' is already mapped, check if it maps to the correct character in 't'
            if(hm.containsKey(sChar)) {
                if(hm.get(sChar) != tChar) // If not, the strings are not isomorphic
                    return false;
            }
            // If the character from 't' is already mapped to a different character in 's', return false
            //This line checks if a character in t is already mapped by another character from s. If it is, then the mapping isn't one-to-one, and the strings cannot be isomorphic, so we return false.
            else if(hm.containsValue(tChar))
                return false;

            // Otherwise, create a new mapping of the character from 's' to 't'
            hm.put(sChar, tChar);
        }

        // If all checks are passed, the strings are isomorphic
        return true;
    }

    public static void main(String[] args) {
        String s = "egg";
        String t = "add";
        System.out.println(isIsomorphic(s, t));  // Output: true

        s = "foo";
        t = "bar";
        System.out.println(isIsomorphic(s, t));  // Output: false
    }
}
```
