# Question 16: Reorder List

**Question:** https://leetcode.com/problems/reorder-list/description/

## 1. Brute Force Solution

**Brief Summary of Approach:**
1. **Convert Linked List to Array:** Traverse the linked list once and store the nodes in an array (ArrayList).
2. **Use Two Pointers:** One pointer (left) starts from the beginning, and the other (right) starts from the end. Alternate nodes from both ends.
3. **Reconstruct the List:** By linking the nodes from both ends, we reorder the list.
4. **Terminate the List:** Set the next of the last node to null.

**Time Complexity:** O(N): Two passes through the list.
**Space Complexity:** O(N): We store all the nodes in an array.

## 2. Optimal Solution

**Approach:**
1. Find the middle of the linked list using the slow and fast pointer technique.
2. First detach the link and Reverse the second half of the list.
3. Merge the two halves together by alternating nodes.

**Time Complexity:** O(N) — three passes.
**Space Complexity:** O(1) — no extra space except pointers.

---

### 1. Brute Force Solution (CODE)

```java
import java.util.*;

class ListNode {
    int val;
    ListNode next;
    ListNode(int val) { this.val = val; this.next = null; }
}

public class ReorderList_BruteForce {
    public void reorderList(ListNode head) {
        if (head == null) return;

        // Step 1: Convert the linked list to an ArrayList
        List<ListNode> nodes = new ArrayList<>();
        ListNode current = head;
        while (current != null) {
            nodes.add(current);
            current = current.next;
        }

        // Step 2: Reorder using two pointers
        int left = 0;
        int right = nodes.size() - 1;
        while (left < right) {
            nodes.get(left).next = nodes.get(right);
            left++;
            if (left == right) break;
            nodes.get(right).next = nodes.get(left);
            right--;
        }

        // Step 3: Terminate the list
        nodes.get(left).next = null;
    }

    public static void main(String[] args) {
        ReorderList_BruteForce solution = new ReorderList_BruteForce();

        ListNode head = new ListNode(1);
        head.next = new ListNode(2);
        head.next.next = new ListNode(3);
        head.next.next.next = new ListNode(4);
        head.next.next.next.next = new ListNode(5);

        solution.reorderList(head);

        ListNode current = head;
        while (current != null) {
            System.out.print(current.val + " "); // Expected Output: 1 5 2 4 3
            current = current.next;
        }
    }
}
```

### 2. Optimal Solution (CODE)

```java
import java.util.*;

class ListNode {
    int val;
    ListNode next;
    ListNode(int val) { this.val = val; this.next = null; }
}

public class ReorderList_Optimal {
    public void reorderList(ListNode head) {
        if (head == null) return;

        // Step 1: Find the middle using slow and fast pointers
        ListNode slow = head;
        ListNode fast = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }

        // Step 2: Reverse the second half starting from slow
        ListNode prev = null;
        ListNode current = slow;
        while (current != null) {
            ListNode next = current.next;
            current.next = prev;
            prev = current;
            current = next;
        }

        // Step 3: Merge the two halves
        ListNode first = head;
        ListNode second = prev;
        while (second.next != null) {
            ListNode temp1 = first.next;
            ListNode temp2 = second.next;
            first.next = second;
            second.next = temp1;
            first = temp1;
            second = temp2;
        }
    }

    public static void main(String[] args) {
        ReorderList_Optimal solution = new ReorderList_Optimal();

        ListNode head = new ListNode(1);
        head.next = new ListNode(2);
        head.next.next = new ListNode(3);
        head.next.next.next = new ListNode(4);
        head.next.next.next.next = new ListNode(5);

        solution.reorderList(head);

        ListNode current = head;
        while (current != null) {
            System.out.print(current.val + " "); // Expected Output: 1 5 2 4 3
            current = current.next;
        }
    }
}
```

