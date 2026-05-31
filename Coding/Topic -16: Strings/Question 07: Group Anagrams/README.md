# Question 07: Group Anagrams

**Question:** 49. Group Anagrams

## Approach:

Simply, if we can sort each character of each string, we can group them.
- "eat" → "aet"
- "tea" → "aet"
- "tan" → "ant"

We use a map and store key as the sorted value. The key is the sorted version of the string, the value is a list of strings that are anagrams of the key.

### Steps:
1. **Initialization:** Create a HashMap. Keys = sorted strings, Values = lists of anagrams.
2. **Iterate:** For each string, convert to char array, sort it, create key.
3. **Populate HashMap:** If key doesn't exist, create new entry. Add original string to list.
4. **Return:** Return values of HashMap as a list.

### Time and Space Complexity:
- **Time Complexity:** O(n · k log k), where n = number of strings, k = max length of a string.
- **Space Complexity:** O(n · k)

### Dry Run:

Input: ["eat","tea","tan","ate","nat","bat"]

| String | Sorted | Key | Map State |
|--------|--------|-----|-----------|
| "eat" | "aet" | "aet" | {"aet": ["eat"]} |
| "tea" | "aet" | "aet" | {"aet": ["eat","tea"]} |
| "tan" | "ant" | "ant" | {"aet": ["eat","tea"], "ant": ["tan"]} |
| "ate" | "aet" | "aet" | {"aet": ["eat","tea","ate"], "ant": ["tan"]} |
| "nat" | "ant" | "ant" | {"aet": ["eat","tea","ate"], "ant": ["tan","nat"]} |
| "bat" | "abt" | "abt" | {"aet": ["eat","tea","ate"], "ant": ["tan","nat"], "abt": ["bat"]} |

**Output:** [["eat","tea","ate"], ["tan","nat"], ["bat"]]

### Code:

```java
import java.util.*;

class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        // Create a HashMap to store grouped anagrams.
        Map<String, List<String>> ans = new HashMap<>();

        // Use a normal for loop to iterate through each string in the input array.
        for (int i = 0; i < strs.length; i++) {
            String s = strs[i]; // Get the current string

            // Convert the string to a character array.
            char[] chars = s.toCharArray();

            // Sort the character array. Anagrams will have the same sorted character array.
            Arrays.sort(chars);

            // Create a key from the sorted characters.
            String key = new String(chars);

            // If the key doesn't exist in the map, create a new entry with an empty list.
            if (!ans.containsKey(key)) {
                ans.put(key, new ArrayList<>());
            }

            // Add the original string to the list of its corresponding sorted key.
            ans.get(key).add(s);
        }

        // Return the list of grouped anagrams as a new ArrayList.
        return new ArrayList<>(ans.values());
    }

    // Main method to test the solution
    public static void main(String[] args) {
        Solution solution = new Solution();

        // Test case 1
        String[] input1 = {"eat", "tea", "tan", "ate", "nat", "bat"};
        System.out.println(solution.groupAnagrams(input1));
        // Expected output: [[bat], [nat, tan], [ate, eat, tea]]

        // Test case 2
        String[] input2 = {""};
        System.out.println(solution.groupAnagrams(input2));
        // Expected output: [[""]]

        // Test case 3
        String[] input3 = {"a"};
        System.out.println(solution.groupAnagrams(input3));
        // Expected output: [["a"]]
    }
}
```
