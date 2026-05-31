# Question 04: Linked List Cycle

**Question:** 141. Linked List Cycle

Given head, the head of a linked list, determine if the linked list has a cycle in it. Return true if there is a cycle in the linked list. Otherwise, return false.

## 1. Brute Force Solution (Using a Hash Set)

**Approach:**
- Traverse the linked list and use a hash set to keep track of visited nodes.
- If a node is visited again (i.e., it's already in the set), the list contains a cycle.
- If we reach the end of the list without revisiting a node, there is no cycle.

**Time Complexity:** O(N), where N is the number of nodes.
**Space Complexity:** O(N) for storing nodes in the hash set.

## 2. Optimal Solution (Floyd's Cycle Detection Algorithm - Tortoise and Hare)

**Approach:**
- Use two pointers, slow and fast.
- Move slow pointer one step at a time, and fast pointer two steps at a time.
- If the fast pointer meets the slow pointer, then a cycle exists.
- If the fast pointer reaches the end of the list (null), then no cycle exists.

**Steps:**
1. Initialize two pointers, slow and fast, both starting at the head.
2. Move slow by one step and fast by two steps in each iteration.
3. If slow and fast meet, a cycle is detected.
4. If fast reaches null (end of list), there is no cycle.

**Time Complexity:** O(N), where N is the number of nodes.
**Space Complexity:** O(1), constant space used.

---

### 1. Brute Force Solution (CODE)

```java
import java.util.*;

public class LinkedListCycle_BruteForce {
    static class ListNode {
        int val;
        ListNode next;
        ListNode(int x) {
            val = x;
            next = null;
        }
    }

    public static boolean hasCycle(ListNode head) {
        Set<ListNode> visited = new HashSet<>();
        while (head != null) {
            if (visited.contains(head)) {
                return true;
            }
            visited.add(head);
            head = head.next;
        }
        return false;
    }

    public static void main(String[] args) {
        ListNode head = new ListNode(3);
        head.next = new ListNode(2);
        head.next.next = new ListNode(0);
        head.next.next.next = new ListNode(-4);
        head.next.next.next.next = head.next; // Creates cycle

        System.out.println(hasCycle(head));  // Output: true
    }
}
```

### 2. Optimal Solution (Floyd's Cycle Detection) (CODE)

```java
import java.util.*;

public class LinkedListCycle_Optimal {
    static class ListNode {
        int val;
        ListNode next;
        ListNode(int x) {
            val = x;
            next = null;
        }
    }

    public static boolean hasCycle(ListNode head) {
        //no node or only 1 node means no cycle
        if (head == null || head.next == null) {
            return false;
        }

        ListNode slow = head;
        ListNode fast = head;

        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
            if (slow == fast) {
                return true;
            }
        }
        return false;
    }

    public static void main(String[] args) {
        ListNode head = new ListNode(3);
        head.next = new ListNode(2);
        head.next.next = new ListNode(0);
        head.next.next.next = new ListNode(-4);
        head.next.next.next.next = head.next; // Creates cycle

        System.out.println(hasCycle(head));  // Output: true
    }
}
```

