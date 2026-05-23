# Question 06: Maximum Frequency Stack

**Question:** 895. Maximum Frequency Stack

## Logic Explanation:

**Key Components:**

**Data Members:**
- `frequencyMap`: A HashMap<Integer, Integer> that tracks how many times each element has been pushed. The key is the element, and the value is its frequency.
- `frequencyStacks`: A HashMap<Integer, Stack<Integer>> that maps frequencies to stacks of elements that have that frequency. This allows easy retrieval of the most recent element with the highest frequency.
- `maxFrequency`: An integer that keeps track of the current maximum frequency of any element in the stack.

**Constructor (FreqStack):**
- Initializes the frequencyMap, frequencyStacks, and sets maxFrequency to 0.

**Push Method (push(int val)):**
- Increments the frequency of the element val in frequencyMap.
- Updates maxFrequency if the new frequency exceeds the current max.
- Checks if a stack exists for the current frequency; if not, creates a new stack.
- Pushes the element val onto the stack corresponding to its frequency.

**Pop Method (pop()):**
- Retrieves the stack for the maxFrequency and pops the top element.
- Decrements the frequency of the popped element in frequencyMap.
- If the stack for maxFrequency is empty after popping, it removes that stack and decrements maxFrequency.
- Returns the most frequent element that was popped.

### Time Complexity:
- Both the push and pop operations run in O(1) time due to the use of hash maps and stacks.

### Space Complexity:
- O(n), where n is the number of unique elements in the frequency stack.

### Code:

```java
import java.util.HashMap;
import java.util.Stack;

class FreqStack {
    // Map to store the frequency of each element
    HashMap<Integer, Integer> frequencyMap;
    // Map to store stacks of elements grouped by their frequency
    HashMap<Integer, Stack<Integer>> frequencyStacks;
    // Variable to keep track of the current highest frequency
    int maxFrequency;

    // Constructor to initialize the data structures
    public FreqStack() {
        // Initialize the frequency map and the frequency stacks map
        frequencyMap = new HashMap<>();
        frequencyStacks = new HashMap<>();
        maxFrequency = 0; // No elements in the stack initially
    }

    // Method to push an element onto the stack
    public void push(int val) {
        // Get the current frequency of the element, default to 0 if it doesn't exist
        int currentFrequency = frequencyMap.getOrDefault(val, 0) + 1;

        // Update the frequency map with the new frequency
        frequencyMap.put(val, currentFrequency);

        // Update the maximum frequency if the current frequency is greater
        if (currentFrequency > maxFrequency) {
            maxFrequency = currentFrequency;
        }

        // Retrieve the stack for the current frequency
        Stack<Integer> stackForCurrentFrequency = frequencyStacks.get(currentFrequency);

        // If no stack exists for this frequency, create a new stack
        if (stackForCurrentFrequency == null) {
            stackForCurrentFrequency = new Stack<>();
            frequencyStacks.put(currentFrequency, stackForCurrentFrequency);
        }

        // Push the element onto the stack for the current frequency
        stackForCurrentFrequency.push(val);
    }

    // Method to pop and return the most frequent element
    public int pop() {
        // Get the stack of elements with the highest frequency
        Stack<Integer> stack = frequencyStacks.get(maxFrequency);

        // Pop the most frequent element from the stack
        int mostFrequentElement = stack.pop();

        // Decrease the frequency count of the popped element
        frequencyMap.put(mostFrequentElement, frequencyMap.get(mostFrequentElement) - 1);

        // If the stack for the max frequency becomes empty, decrease max frequency
        if (stack.isEmpty()) {
            frequencyStacks.remove(maxFrequency); // Clean up the map
            maxFrequency--; // Decrease the maximum frequency
        }

        // Return the most frequent element that was just popped
        return mostFrequentElement;
    }

    public static void main(String[] args) {
        FreqStack stack = new FreqStack();
        stack.push(5);
        stack.push(7);
        stack.push(5);
        stack.push(7);
        stack.push(4);
        stack.push(5);

        System.out.println(stack.pop()); // Outputs: 5 (most frequent)
        System.out.println(stack.pop()); // Outputs: 7 (next most frequent)
        System.out.println(stack.pop()); // Outputs: 5 (most recent of frequency 2)
        System.out.println(stack.pop()); // Outputs: 4 (only one of frequency 1)
    }
}
```

