# Question 08: Merge Two Sorted Lists

**Question:** https://leetcode.com/problems/merge-two-sorted-lists/description/

## Approach-1: Recursive

### Steps and Explanation:

**Problem Breakdown:**
- Given two sorted linked lists, merge them into one sorted linked list.
- Both input lists are sorted in non-decreasing order.

**Key Idea:**
- The smallest element from either list should be placed at the start of the merged list.
- Recursively merge the remaining elements.

**Steps:**
1. **Base Case:** If either of the input lists is null, return the other list.
2. **Recursive Step:** Compare the values of the nodes from l1 and l2:
   - If l1.val is smaller, link l1.next to the result of recursively merging l1.next with l2, and return l1.
   - If l2.val is smaller or equal, link l2.next to the result of recursively merging l1 with l2.next, and return l2.

**Time Complexity:** O(n + m) where n and m are the lengths of the two linked lists.
**Space Complexity:** O(n + m) due to the recursive call stack.

### DRY Run:

Input: l1 = 1 -> 2 -> 4, l2 = 1 -> 3 -> 4

- Step 1: l1.val=1, l2.val=1 → take l2, recurse(l1, l2.next)
- Step 2: l1.val=1, l2.val=3 → take l1, recurse(l1.next, l2)
- Step 3: l1.val=2, l2.val=3 → take l1, recurse(l1.next, l2)
- Step 4: l1.val=4, l2.val=3 → take l2, recurse(l1, l2.next)
- Step 5: l1.val=4, l2.val=4 → take l2, recurse(l1, l2.next)
- Step 6: l2 is null → return l1

**Final Merged List:** 1 -> 1 -> 2 -> 3 -> 4 -> 4 -> null

### Code:

```java
class Solution {
    // Recursive method to merge two sorted linked lists
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        // Base case: If either list is null, return the other list
        if (l1 == null) {
            return l2;
        }
        if (l2 == null) {
            return l1;
        }

        // Compare the values of the nodes at l1 and l2
        if (l1.val < l2.val) {
            l1.next = mergeTwoLists(l1.next, l2);
            return l1;
        } else {
            l2.next = mergeTwoLists(l1, l2.next);
            return l2;
        }
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        ListNode l1 = new ListNode(1, new ListNode(2, new ListNode(4)));
        ListNode l2 = new ListNode(1, new ListNode(3, new ListNode(4)));

        ListNode merged = solution.mergeTwoLists(l1, l2);
        printList(merged);
    }

    public static void printList(ListNode node) {
        while (node != null) {
            System.out.print(node.val + " -> ");
            node = node.next;
        }
        System.out.println("null");
    }
}

class ListNode {
    int val;
    ListNode next;
    ListNode(int val) { this.val = val; this.next = null; }
    ListNode(int val, ListNode next) { this.val = val; this.next = next; }
}
```

## Approach-2: Iterative

### Steps and Explanation:

**Key Idea:** Use a dummy node to simplify the merging process and maintain a pointer to build the merged list iteratively.

**Steps:**
1. **Base Cases:** If one of the lists is null, return the other list.
2. **Dummy Node:** Create a dummy node to serve as the head of the merged list.
3. **Iterate Through Both Lists:** Compare current values and append the smaller one.
4. **Link Remaining Nodes:** After the loop, link the remaining part of either list.
5. **Return Result:** Return dummy.next.

**Time Complexity:** O(n + m)
**Space Complexity:** O(1) — constant space, merged list is created in-place.

### Code:

```java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) return l2;
        else if (l2 == null) return l1;

        ListNode dummy = new ListNode(0);
        ListNode curr = dummy;

        while (l1 != null && l2 != null) {
            if (l1.val <= l2.val) {
                curr.next = l1;
                l1 = l1.next;
            } else {
                curr.next = l2;
                l2 = l2.next;
            }
            curr = curr.next;
        }

        curr.next = l1 == null ? l2 : l1;

        return dummy.next;
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        ListNode l1 = new ListNode(1, new ListNode(2, new ListNode(4)));
        ListNode l2 = new ListNode(1, new ListNode(3, new ListNode(4)));

        ListNode merged = solution.mergeTwoLists(l1, l2);
        printList(merged); // Expected: 1 -> 1 -> 2 -> 3 -> 4 -> 4 -> null
    }

    public static void printList(ListNode node) {
        while (node != null) {
            System.out.print(node.val + " -> ");
            node = node.next;
        }
        System.out.println("null");
    }
}

class ListNode {
    int val;
    ListNode next;
    ListNode(int val) { this.val = val; this.next = null; }
    ListNode(int val, ListNode next) { this.val = val; this.next = next; }
}
```

