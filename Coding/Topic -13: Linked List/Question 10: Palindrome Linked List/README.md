
# Question 10: Palindrome Linked List

**Question:** 234. Palindrome Linked List

## 1. Brute Force Solution (Converting to List)

**Explanation:**
1. **Convert Linked List to Array:** Traverse through the linked list and store each node's value in an ArrayList.
2. **Check for Palindrome:** Use two pointers, one starting from the beginning and the other from the end of the list. Compare the elements at these pointers. If they are not equal, return false. If you reach the middle without finding any discrepancies, return true.

**Time Complexity:** O(n)
**Space Complexity:** O(n), due to the storage of the linked list values in an array.

### Code (with comments and main method):

```java
import java.util.*;

class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; }
}

public class Solution {
    public boolean isPalindrome(ListNode head) {
        if (head == null) {
            return true; // An empty list is a palindrome
        }

        // Step 1: Convert linked list to array
        ListNode temp = head;
        List<Integer> values = new ArrayList<>();
        while (temp != null) {
            values.add(temp.val);
            temp = temp.next;
        }

        // Step 2: Check if the array is a palindrome
        int left = 0;
        int right = values.size() - 1;
        while (left < right) {
            if (!values.get(left).equals(values.get(right))) {
                return false; // Not a palindrome
            }
            left++;
            right--;
        }

        return true; // Is a palindrome
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        // Test case: Creating a palindrome linked list 1 -> 2 -> 2 -> 1
        ListNode head = new ListNode(1);
        head.next = new ListNode(2);
        head.next.next = new ListNode(2);
        head.next.next.next = new ListNode(1);

        // Expected output: true
        System.out.println(solution.isPalindrome(head));
    }
}
```

## 2. Optimal Solution (Using Two Pointers and Reversing Half of the List)

**Explanation:**
1. **Find the Middle:** Use the slow and fast pointer technique to find the middle of the linked list.
2. **Reverse the Second Half:** Starting from the slow pointer (the middle), reverse the second half of the linked list.
3. **Compare Both Halves:** Create two pointers: one starting from the head and the other starting from the head of the reversed second half. Compare the values.

**Time Complexity:** O(n)
**Space Complexity:** O(1), as we only use a few pointers.

### Code (with comments):

```java
class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; }
}

public class Solution {
    public boolean isPalindrome(ListNode head) {
        if (head == null || head.next == null) {
            return true; // An empty list or a single node list is a palindrome
        }

        // Step 1: Find the middle of the linked list using the slow and fast pointer approach
        ListNode slow = head;
        ListNode fast = head;
        while (fast != null && fast.next != null) {
            slow = slow.next; // Move slow pointer one step
            fast = fast.next.next; // Move fast pointer two steps
        }

        // Step 2: Reverse the second half of the linked list
        ListNode prev = null;
        ListNode current = slow;
        while (current != null) {
            ListNode nextTemp = current.next; // Store the next node
            current.next = prev; // Reverse the link
            prev = current; // Move prev pointer to the current node
            current = nextTemp; // Move to the next node
        }

        // Step 3: Compare the first half and the reversed second half
        ListNode left = head; // Start from the beginning
        ListNode right = prev; // Start from the reversed second half
        while (right != null) {
            if (left.val != right.val) {
                return false; // Not a palindrome
            }
            left = left.next; // Move left pointer forward
            right = right.next; // Move right pointer forward
        }

        return true; // Is a palindrome
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        // Test case: Creating a palindrome linked list 1 -> 2 -> 2 -> 1
        ListNode head = new ListNode(1);
        head.next = new ListNode(2);
        head.next.next = new ListNode(2);
        head.next.next.next = new ListNode(1);

        // Expected output: true
        System.out.println(solution.isPalindrome(head));
    }
}
```
