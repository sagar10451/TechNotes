# Question 02: Copy List with Random Pointer

**Question:** 138. Copy List with Random Pointer

## 1. Brute Force Solution (using hashmap and two pass) — explain this in interview

**Approach:**
- Create a mapping of original nodes to their copied nodes using a HashMap.
- First, iterate through the original list to create copies of each node and store them in the map.
- Then, in a second pass, set the next and random pointers for each copied node using the map.

**Time Complexity:**
- O(n): We traverse the list twice, where n is the number of nodes.

**Space Complexity:**
- O(n): We use a HashMap to store the mapping of original nodes to their copies.

## 2. Optimal Solution (Without Extra Space)

**Explanation:**

**First Pass (Interweaving the nodes):**
- For each original node, create a new node and insert it right after the original node.
- Example: Original list: 1 -> 2. After first pass: 1 -> 1' -> 2 -> 2'

**Second Pass (Setting the random pointers):**
- Use the original node's random pointer to set the random pointer for the corresponding new node.
- If node.random != null, then node.next.random = node.random.next.

**Third Pass (Restoring the original list and separating the new list):**
- Restore the original list and extract the copied list by separating the interleaved nodes.

**Time Complexity:**
- O(n): We make three passes through the list, each of which is linear in time.

**Space Complexity:**
- O(1): We are not using any extra space other than the new nodes (ignoring the output space).

---

### 1. Brute Force Solution (CODE) — go with this approach in interview

```java
import java.util.*;

class Node {
    int val;
    Node next;
    Node random;

    Node(int val) {
        this.val = val;
        this.next = null;
        this.random = null;
    }
}

public class Solution {
    // Method to copy the list with random pointers
    public static Node copyRandomList(Node head) {
        // HashMap to store old nodes as keys and new nodes as values
        HashMap<Node, Node> map = new HashMap<Node, Node>();

        // First pass: Copy all nodes and store them in the HashMap
        Node p = head;
        while (p != null) {
            map.put(p, new Node(p.val)); // Create new node and store it
            p = p.next;
        }

        // Second pass: Set next and random pointers for copied nodes
        Node q = head;
        while (q != null) {
            map.get(q).next = map.get(q.next);      // Set next pointer
            map.get(q).random = map.get(q.random);  // Set random pointer
            q = q.next;
        }

        // Return the head of the new copied list
        return map.get(head);
    }

    public static void main(String[] args) {
        // Example creation of a linked list
        Node node1 = new Node(1);
        Node node2 = new Node(2);
        node1.next = node2;
        node1.random = node2; // Random pointer points to node2
        node2.random = node1; // Random pointer points to node1

        // Create a deep copy of the list
        Node copiedListHead = copyRandomList(node1);

        // Output the values of the copied list to verify
        System.out.println(copiedListHead.val);  // Output: 1
        System.out.println(copiedListHead.next.val);  // Output: 2
        System.out.println(copiedListHead.random.val);  // Output: 2
    }
}
```

### 2. Optimal Solution (Without Extra Space) (CODE)

```java
class Node {
    int val;
    Node next;
    Node random;

    Node(int val) {
        this.val = val;
        this.next = null;
        this.random = null;
    }
}

public class Solution {
    public static Node copyRandomList(Node head) {
        if (head == null) {
            return null;
        }

        // First pass: Create new nodes and place them next to the original nodes
        Node current = head;
        while (current != null) {
            Node newNode = new Node(current.val);
            newNode.next = current.next;
            current.next = newNode;
            current = newNode.next;
        }

        // Second pass: Set the random pointers for the new nodes
        current = head;
        while (current != null) {
            if (current.random != null) {
                current.next.random = current.random.next;
            }
            current = current.next.next;
        }

        // Third pass: Separate the original list and the copied list
        current = head;
        Node copiedHead = head.next;
        Node copiedCurrent = copiedHead;

        while (current != null) {
            current.next = current.next.next;
            if (copiedCurrent.next != null) {
                copiedCurrent.next = copiedCurrent.next.next;
            }
            current = current.next;
            copiedCurrent = copiedCurrent.next;
        }

        return copiedHead;
    }

    public static void main(String[] args) {
        Node node1 = new Node(1);
        Node node2 = new Node(2);
        node1.next = node2;
        node1.random = node2;
        node2.random = node1;

        Node copiedListHead = copyRandomList(node1);

        System.out.println(copiedListHead.val);  // Output: 1
        System.out.println(copiedListHead.next.val);  // Output: 2
        System.out.println(copiedListHead.random.val);  // Output: 2
    }
}
```
