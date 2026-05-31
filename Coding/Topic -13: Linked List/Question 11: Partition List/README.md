# Question 11: Partition List

**Question:** 86. Partition List

## 1. Brute Force Solution — explain this in interview

**Brief Explanation (Steps for Interviews):**

1. **Initialize Dummy Nodes:** We create two dummy nodes, left and right, which serve as the heads of two new lists:
   - left: For nodes with values less than x.
   - right: For nodes with values greater than or equal to x.
2. **Traverse the Original List:**
   - If the current node's value is less than x, it gets added to the left list.
   - Otherwise, it gets added to the right list.
3. **Connect the Two Lists:** After traversing the original list, we connect the last node of the left list to the first node of the right list.
4. **Return the New List:** The head of the left list is returned as the new partitioned list.

**Time Complexity:** O(n), where n is the number of nodes. Each node is visited exactly once.
**Space Complexity:** O(1) extra space for variables.

## 2. Optimal Solution

**Approach:** The optimal solution follows a similar approach but instead of using additional space, we modify the list in place. We use two pointers: one for values less than x and another for values greater than or equal to x.

**Steps:**
1. Initialize two dummy nodes, before and after.
2. Traverse the original linked list, and build the before list (nodes < x) and the after list (nodes >= x).
3. Once traversal is complete, connect the before list to the after list.
4. Return the combined list.

**Time Complexity:** O(n)
**Space Complexity:** O(1)

---

### 1. Brute Force Solution (CODE) — explain this in interview

```java
class ListNode {
    int val;
    ListNode next;

    ListNode(int val) {
        this.val = val;
        this.next = null;
    }
}

class Solution {
    // Method to partition the linked list around the value x
    public ListNode partition(ListNode head, int x) {
        // Create two dummy nodes to form two separate lists
        ListNode left = new ListNode(0);  // List for nodes less than x
        ListNode right = new ListNode(0); // List for nodes greater than or equal to x

        // Pointers to maintain the tails of the left and right lists
        ListNode leftTail = left;
        ListNode rightTail = right;

        // Traverse the original list
        while (head != null) {
            if (head.val < x) {
                leftTail.next = head;     // Attach current node to left list
                leftTail = leftTail.next; // Move the leftTail forward
            } else {
                rightTail.next = head;    // Attach current node to right list
                rightTail = rightTail.next; // Move the rightTail forward
            }
            head = head.next;             // Move to the next node in the original list
        }

        // Connect the end of the left list to the start of the right list
        leftTail.next = right.next;

        // End the right list to avoid any cycles in the linked list
        rightTail.next = null;

        // Return the new head of the partitioned list
        return left.next;
    }

    public static void main(String[] args) {
        ListNode node1 = new ListNode(1);
        ListNode node2 = new ListNode(4);
        ListNode node3 = new ListNode(3);
        ListNode node4 = new ListNode(2);
        ListNode node5 = new ListNode(5);
        ListNode node6 = new ListNode(2);

        node1.next = node2;
        node2.next = node3;
        node3.next = node4;
        node4.next = node5;
        node5.next = node6;

        Solution solution = new Solution();
        ListNode result = solution.partition(node1, 3);

        System.out.print("Partitioned List: ");
        ListNode current = result;
        while (current != null) {
            System.out.print(current.val + " ");
            current = current.next;
        }
        System.out.println();
    }
}
```

### 2. Optimal Solution (CODE)

```java
import java.util.*;

public class PartitionList_Optimal {
    public ListNode partition(ListNode head, int x) {
        ListNode beforeHead = new ListNode(0);
        ListNode afterHead = new ListNode(0);
        ListNode before = beforeHead;
        ListNode after = afterHead;

        while (head != null) {
            if (head.val < x) {
                before.next = head;
                before = before.next;
            } else {
                after.next = head;
                after = after.next;
            }
            head = head.next;
        }

        after.next = null; // Terminate after list
        before.next = afterHead.next; // Connect before list to after list
        return beforeHead.next;
    }

    public static void main(String[] args) {
        PartitionList_Optimal solution = new PartitionList_Optimal();

        ListNode head = new ListNode(1);
        head.next = new ListNode(4);
        head.next.next = new ListNode(3);
        head.next.next.next = new ListNode(2);
        head.next.next.next.next = new ListNode(5);
        head.next.next.next.next.next = new ListNode(2);

        ListNode result = solution.partition(head, 3);
        while (result != null) {
            System.out.print(result.val + " ");
            result = result.next;
        }
        // Expected Output: 1 2 2 4 3 5
    }
}
```
