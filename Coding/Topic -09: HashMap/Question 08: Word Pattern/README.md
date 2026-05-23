# Question 08: Word Pattern

**Question:** 290. Word Pattern

Given a pattern and a string s, find if s follows the same pattern. Here follow means a full match, such that there is a bijection between a letter in pattern and a non-empty word in s. Specifically:
- Each letter in pattern maps to exactly one unique word in s.
- Each unique word in s maps to exactly one letter in pattern.
- No two letters map to the same word, and no two words map to the same letter.

Example 1:
- Input: pattern = "abba", s = "dog cat cat dog"
- Output: true
- Explanation: The bijection can be established as: 'a' maps to "dog". 'b' maps to "cat".

## 1. Brute Force Explanation and Steps:

The brute force approach involves checking each character in the pattern against each word in the string.

**Steps:**
1. Split the string s into words.
2. If the length of the pattern does not match the number of words, return false.
3. Use two hash maps to track the mappings from pattern characters to words and from words to pattern characters.
4. For each character in the pattern and the corresponding word:
   - Check if the character is already mapped to a different word or if the word is mapped to a different character.
   - If any mapping is inconsistent, return false.
5. If all mappings are consistent, return true.

**Time Complexity:** O(n), where n is the length of the pattern or the number of words in s.
**Space Complexity:** O(m + k), where m is the number of unique characters in the pattern and k is the number of unique words in s.

## 2. Optimal Explanation and Steps:

**Explanation:**

**Input Handling:**
- The string s is split into an array of words (arr).
- If the length of the pattern string and the array of words do not match, return false immediately.

**Mapping Creation:**
- We use a HashMap to map each character in pattern to a word in s.

**Iteration:**
- We loop through the pattern and the corresponding words:
  - If the current pattern character is already in the map, we check if it maps to the correct word. If not, return false.
  - If the current character is not in the map yet, we check if the word has already been mapped to another character. If it has, return false to avoid one-to-many mappings.
  - Otherwise, map the character to the word.

**Final Check:**
- If the loop completes without returning false, it means the pattern matches the word arrangement, and the method returns true.

**Time Complexity:** O(n): We loop through the pattern and words once, and HashMap operations take O(1) on average.
**Space Complexity:** O(n): We store up to n entries in the HashMap and the array from split() takes O(n) space.

---

### 1. Brute Force Solution (CODE)

```java
import java.util.*;

public class BruteForceSolution {
    public boolean wordPattern(String pattern, String s) {
        String[] words = s.split(" ");
        if (pattern.length() != words.length) return false;

        Map<Character, String> charToWord = new HashMap<>();
        Map<String, Character> wordToChar = new HashMap<>();

        for (int i = 0; i < pattern.length(); i++) {
            char ch = pattern.charAt(i);
            String word = words[i];

            if (charToWord.containsKey(ch)) {
                if (!charToWord.get(ch).equals(word)) {
                    return false;
                }
            } else {
                charToWord.put(ch, word);
            }

            if (wordToChar.containsKey(word)) {
                if (wordToChar.get(word) != ch) {
                    return false;
                }
            } else {
                wordToChar.put(word, ch);
            }
        }
        return true;
    }

    public static void main(String[] args) {
        BruteForceSolution bruteForce = new BruteForceSolution();
        String pattern = "abba";
        String s = "dog cat cat dog";
        boolean result = bruteForce.wordPattern(pattern, s);
        System.out.println("Does the string follow the pattern: " + result);
    }
}
```

### 2. Optimal Solution (CODE)

```java
import java.util.*;

public class Solution {
    public boolean wordPattern(String pattern, String s) {
        // Create a HashMap to store character-to-word mapping
        Map<Character, String> map = new HashMap<>();

        // Split the input string 's' into an array of words
        String[] arr = s.split(" ");

        // If the length of the pattern and the number of words do not match, return false
        if (pattern.length() != arr.length) {
            return false;
        }

        // Loop through each character in the pattern
        for (int i = 0; i < pattern.length(); i++) {
            char currentChar = pattern.charAt(i); // Get the current pattern character
            String currentWord = arr[i];          // Get the corresponding word

            // If the current character is already mapped in the map
            if (map.containsKey(currentChar)) {
                // Check if the mapped word is the same as the current word
                if (!currentWord.equals(map.get(currentChar))) {
                    return false; // If not, return false (mismatch in mapping)
                }
            }
            // If the character is not mapped yet
            else {
                // Check if the word is already mapped to another character
                if (map.containsValue(currentWord)) {
                    return false; // If it is, return false (no two characters can map to the same word)
                }
                // Otherwise, add the new character-word mapping to the map
                map.put(currentChar, currentWord);
            }
        }

        // If all checks passed, return true
        return true;
    }

    public static void main(String[] args) {
        Solution sol = new Solution();
        String pattern = "abba";
        String s = "dog cat cat dog";
        boolean result = sol.wordPattern(pattern, s);
        System.out.println(result);
    }
}
```
