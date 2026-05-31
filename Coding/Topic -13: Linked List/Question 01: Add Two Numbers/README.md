**Question:** 2. Add Two Numbers

You are given two non-empty linked lists representing two non-negative integers. The digits are stored in reverse order, and each of their nodes contains a single digit. Add the two numbers and return the sum as a linked list. You may assume the two numbers do not contain any leading zero, except the number 0 itself.

## Optimal Solution

**Approach:**
- We add the numbers digit by digit.
- We maintain a carry while adding the digits and store the result in a new linked list.
- If the sum of two digits (plus carry) is greater than or equal to 10, store the digit and pass the carry to the next addition.
- There are edge cases, that we have handled in code, look for that.

**Time Complexity:**
- O(n + m): We traverse both linked lists once, where n and m are the lengths of l1 and l2, respectively.

**Space Complexity:**
- O(max(n, m)): The space complexity depends on the length of the result list, which can be at most max(n, m) + 1 (due to carry).

### Code:

```java
public class Solution {
    // Definition for singly-linked list.
    public static class ListNode {
        int val;
        ListNode next;
        ListNode() {}
        ListNode(int val) { this.val = val; }
        ListNode(int val, ListNode next) { this.val = val; this.next = next; }
    }

    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode res = new ListNode(0);  // Dummy node
        ListNode curr = res;  // Pointer to the current node of the result list
        int carry = 0;

        while (l1 != null || l2 != null) {
            int x = (l1 != null) ? l1.val : 0;  // Get value from l1, or 0 if l1 is null
            int y = (l2 != null) ? l2.val : 0;  // Get value from l2, or 0 if l2 is null

            int sum = x + y + carry;
            carry = sum / 10;  // Calculate carry
            curr.next = new ListNode(sum % 10);  // Create a new node with the sum mod 10
            curr = curr.next;  // Move to the next node

            if (l1 != null) l1 = l1.next;  // Move to the next node in l1
            if (l2 != null) l2 = l2.next;  // Move to the next node in l2
        }

        // If there's a carry left at the end, add a new node with carry value
        if (carry > 0) {
            curr.next = new ListNode(carry);
        }

        return res.next;  // Return the next node as the first node was dummy
    }

    public static void main(String[] args) {
        // Create the first linked list l1 = [2 -> 4 -> 3]
        ListNode l1 = new ListNode(2);
        l1.next = new ListNode(4);
        l1.next.next = new ListNode(3);

        // Create the second linked list l2 = [5 -> 6 -> 4]
        ListNode l2 = new ListNode(5);
        l2.next = new ListNode(6);
        l2.next.next = new ListNode(4);

        // Call the addTwoNumbers method
        Solution solution = new Solution();
        ListNode result = solution.addTwoNumbers(l1, l2);

        // Print the result linked list
        System.out.print("Output: ");
        while (result != null) {
            System.out.print(result.val);
            if (result.next != null) {
                System.out.print(" -> ");
            }
            result = result.next;
        }
    }
}
```
