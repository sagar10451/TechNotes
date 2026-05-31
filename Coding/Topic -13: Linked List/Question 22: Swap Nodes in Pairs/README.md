# Question 22: Swap Nodes in Pairs

**Question:** https://leetcode.com/problems/swap-nodes-in-pairs/description/

## 1. Brute Force (Recursive) — Start from here in interview

**Step-by-Step Process:**
1. **Dummy Node Creation:** A dummy node is created and linked to the head.
2. **Recursive Swap Method:** Swaps two consecutive nodes in pairs.
   - If fewer than two nodes left, terminate (base case).
   - Identify first and second nodes to swap.
   - Swap by adjusting next pointers.
   - Recursive call on remaining list.
3. **Return:** dummy.next is the new head.

**Swapping Logic:**
- Save the next node after second (secondNext).
- second.next = first → second now points to first.
- node.next = second → previous node points to second.
- first.next = secondNext → first points to the rest.

**Time:** O(n)
**Space:** O(n) due to recursion stack

## 2. Optimal (Iterative)

**Steps:**
1. **Dummy Node:** Create a dummy node pointing to head.
2. **Iterate:** Use a loop to traverse, identify first and second nodes, swap pointers.
3. **Update Pointers:** Adjust previous node's next to new head of swapped pair.
4. **Continue:** Move to next pair.
5. **Return:** dummy.next.

**Time Complexity:** O(n)
**Space Complexity:** O(1)

---

### 1. Brute Force Solution (CODE)

```java
import java.util.*;

class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; }
}

public class BruteForceSolution {
    public ListNode swapPairs(ListNode head) {
        if (head == null || head.next == null) return head;

        ListNode newHead = head.next;
        ListNode prev = null;
        ListNode current = head;

        while (current != null && current.next != null) {
            ListNode nextPair = current.next.next;
            ListNode second = current.next;

            second.next = current; // Swap
            current.next = nextPair;

            if (prev != null) {
                prev.next = second;
            }

            prev = current;
            current = nextPair;
        }

        return newHead;
    }

    public static void main(String[] args) {
        ListNode head = new ListNode(1);
        head.next = new ListNode(2);
        head.next.next = new ListNode(3);
        head.next.next.next = new ListNode(4);

        BruteForceSolution bruteForce = new BruteForceSolution();
        ListNode newHead = bruteForce.swapPairs(head);

        ListNode current = newHead;
        while (current != null) {
            System.out.print(current.val + " ");
            current = current.next;
        }
    }
}
```

### 2. Optimal Solution (CODE)

```java
class ListNode {
    int val;
    ListNode next;
    ListNode(int val) { this.val = val; this.next = null; }
    ListNode() {}
}

public class Solution {
    public ListNode swapPairs(ListNode head) {
        ListNode dummy = new ListNode();
        dummy.next = head;
        ListNode node = dummy;

        while (node != null) {
            ListNode first = node.next;
            ListNode second = null;
            if (first != null) {
                second = first.next;
            }

            if (second != null) {
                ListNode secondNext = second.next;
                second.next = first;
                node.next = second;
                first.next = secondNext;
                node = first;
            } else {
                break;
            }
        }

        return dummy.next;
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        ListNode head = new ListNode(1);
        head.next = new ListNode(2);
        head.next.next = new ListNode(3);
        head.next.next.next = new ListNode(4);

        ListNode result = solution.swapPairs(head);

        ListNode current = result;
        while (current != null) {
            System.out.print(current.val + " ");  // Expected output: 2 1 4 3
            current = current.next;
        }
    }
}
```
