# Question 15: Word Ladder

**Question:** 127. Word Ladder

We have to find the minimum number of steps required for transformation. Since it is minimum we should use BFS. The main tricky part is to generate the adjacency list. For each word we have to generate all possible words, then check if the generated word is in wordList (dictionary). If yes then we add it as a neighbour.

## Approach-1: BFS

### Steps:
1. **Word Set Creation:** Convert wordList into a HashSet for O(1) lookup.
2. **BFS Initialization:** Start BFS from beginWord with level 1.
3. **BFS Execution:** For each word, generate all valid neighbors (words differing by one character). If endWord is found, return current level.
4. **Neighbor Generation:** For each character position, try replacing with every letter 'a' to 'z'. If resulting word is in wordSet, it's a valid neighbor.

### Time and Space Complexity:
- **Time:** O(N * L * 26) ≈ O(N * L), where N = number of words, L = length of each word.
- **Space:** O(N * L)

### Dry Run:

beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log","cog"]

- Level 1: "hit"
- Level 2: "hot" (hit→hot)
- Level 3: "dot", "lot" (hot→dot, hot→lot)
- Level 4: "dog" (dot→dog)
- Level 5: "cog" (dog→cog) → **Return 5**

### Code:

```java
import java.util.*;

class Solution {
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        Set<String> wordSet = new HashSet<>(wordList);

        if (!wordSet.contains(endWord)) {
            return 0;
        }

        Queue<String> queue = new LinkedList<>();
        queue.offer(beginWord);

        Set<String> visited = new HashSet<>();
        visited.add(beginWord);

        int level = 1;

        while (!queue.isEmpty()) {
            int size = queue.size();

            for (int i = 0; i < size; i++) {
                String currentWord = queue.poll();

                if (currentWord.equals(endWord)) {
                    return level;
                }

                List<String> neighbors = getNeighbors(currentWord, wordSet);

                for (String neighbor : neighbors) {
                    if (!visited.contains(neighbor)) {
                        visited.add(neighbor);
                        queue.offer(neighbor);
                    }
                }
            }
            level++;
        }

        return 0;
    }

    private List<String> getNeighbors(String word, Set<String> wordSet) {
        List<String> neighbors = new ArrayList<>();
        char[] wordChars = word.toCharArray();

        for (int i = 0; i < wordChars.length; i++) {
            char originalChar = wordChars[i];

            for (char c = 'a'; c <= 'z'; c++) {
                if (c == originalChar) continue;
                wordChars[i] = c;
                String newWord = new String(wordChars);

                if (wordSet.contains(newWord)) {
                    neighbors.add(newWord);
                }
            }

            wordChars[i] = originalChar;
        }

        return neighbors;
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        String beginWord = "hit";
        String endWord = "cog";
        List<String> wordList = Arrays.asList("hot", "dot", "dog", "lot", "log", "cog");

        int result = solution.ladderLength(beginWord, endWord, wordList);
        System.out.println("Length of the shortest transformation sequence: " + result); // Expected: 5
    }
}
```
