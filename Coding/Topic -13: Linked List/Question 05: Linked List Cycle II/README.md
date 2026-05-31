# Question 05: Linked List Cycle II

**Question:** 142. Linked List Cycle II

Given the head of a linked list, return the node where the cycle begins. If there is no cycle, return null.

## 1. Brute Force Solution (Using a Hash Set)

**Approach:**
- Traverse the linked list and use a hash set to store all visited nodes.
- If a node is visited again (i.e., it's already in the set), this is the start of the cycle.
- Return the node where the cycle starts.

**Steps:**
1. Traverse the list and add each node to a set.
2. If the node already exists in the set, that node is the start of the cycle.
3. If no cycle is found, return null.

**Time Complexity:** O(N), where N is the number of nodes.
**Space Complexity:** O(N) for storing the nodes in a hash set.

## 2. Optimal Solution (Floyd's Cycle Detection Algorithm)

**Approach:**
- Use the Tortoise and Hare approach, similar to detecting if a cycle exists.
- Once a cycle is detected, reset one pointer to the head of the list and move both pointers one step at a time.
- The point where the two pointers meet again is the start of the cycle.

**Steps:**
1. Use the Tortoise and Hare method to find if there is a cycle.
2. If a cycle is detected, reset one pointer to the head.
3. Move both pointers one step at a time until they meet; this meeting point is the start of the cycle.
4. If no cycle is detected, return null.

**Time Complexity:** O(N), where N is the number of nodes.
**Space Complexity:** O(1), since no extra space is used apart from pointers.

---

### 1. Brute Force Solution (CODE)

```java
import java.util.*;

public class LinkedListCycleII_BruteForce {
    static class ListNode {
        int val;
        ListNode next;
        ListNode(int x) {
            val = x;
            next = null;
        }
    }

    public static ListNode detectCycle(ListNode head) {
        Set<ListNode> visited = new HashSet<>();
        while (head != null) {
            if (visited.contains(head)) {
                return head;
            }
            visited.add(head);
            head = head.next;
        }
        return null;
    }

    public static void main(String[] args) {
        ListNode head = new ListNode(3);
        head.next = new ListNode(2);
        head.next.next = new ListNode(0);
        head.next.next.next = new ListNode(-4);
        head.next.next.next.next = head.next; // Creates a cycle at node with value 2

        ListNode cycleNode = detectCycle(head);
        System.out.println(cycleNode != null ? cycleNode.val : "No cycle");  // Output: 2
    }
}
```

### 2. Optimal Solution (Floyd's Cycle Detection) (CODE)

```java
import java.util.*;

public class LinkedListCycleII_Optimal {
    static class ListNode {
        int val;
        ListNode next;
        ListNode(int x) {
            val = x;
            next = null;
        }
    }

    public static ListNode detectCycle(ListNode head) {
        if (head == null) {
            return null;
        }

        ListNode slow = head;
        ListNode fast = head;

        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;

            //if these pointers meet, then reset slow to head.
            if (slow == fast) {
                slow = head;
                while (slow != fast) {
                    slow = slow.next;
                    fast = fast.next;
                }
                return slow;
            }
        }
        return null;
    }

    public static void main(String[] args) {
        ListNode head = new ListNode(3);
        head.next = new ListNode(2);
        head.next.next = new ListNode(0);
        head.next.next.next = new ListNode(-4);
        head.next.next.next.next = head.next; // Creates a cycle at node with value 2

        ListNode cycleNode = detectCycle(head);
        System.out.println(cycleNode != null ? cycleNode.val : "No cycle");  // Output: 2
    }
}
```

