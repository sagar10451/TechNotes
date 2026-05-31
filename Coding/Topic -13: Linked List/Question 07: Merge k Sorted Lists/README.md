
# Question 07: Merge k Sorted Lists

**Question:** 23. Merge k Sorted Lists

## 1. Brute Force

**Approach:** Collect all elements from the k sorted lists into a single list and then sort this combined list.

**Time Complexity:** O(N log N), where N is the total number of nodes across all lists.
**Space Complexity:** O(N), for storing the elements in a new list.

## 2. Min-Heap (Priority Queue)

**Approach:** Use a min-heap to efficiently retrieve the smallest element among the heads of the k lists. Continuously extract the minimum element and add the next element from the corresponding list.

**Time Complexity:** O(N log k), where N is the total number of nodes, and k is the number of lists.
**Space Complexity:** O(k), for storing the heap which contains at most k elements at any time.

**Explanation of the Code:**
- **Min-Heap Creation:** A min-heap (priority queue) is created to keep track of the nodes based on their values.
- **Adding Initial Nodes:** Each linked list's head node is added to the min-heap if it's not null.
- **Merging the Lists:** A dummy node is created. The algorithm continuously polls the min-heap to get the node with the smallest value. After adding the node to the merged list, if the node has a next node, that next node is added to the min-heap.
- **Return the Result:** The merged linked list is returned starting from the node next to the dummy node.

---

### 1. Brute Force (CODE)

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; }
}

class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        // Step 1: Create a list to collect all node values
        List<Integer> allValues = new ArrayList<>();

        // Step 2: Traverse each linked list and add values to the list
        for (int i = 0; i < lists.length; i++) {
            ListNode current = lists[i]; // Get the current linked list
            while (current != null) {
                allValues.add(current.val); // Collecting values
                current = current.next; // Move to the next node
            }
        }

        // Step 3: Sort the collected values
        Collections.sort(allValues);

        // Step 4: Create a new sorted linked list from the sorted values
        ListNode dummy = new ListNode(0); // Dummy node to simplify list creation
        ListNode currentNode = dummy; // Pointer to construct the new linked list

        // Iterate through the sorted values and create new nodes
        for (int i = 0; i < allValues.size(); i++) {
            currentNode.next = new ListNode(allValues.get(i)); // Create a new node
            currentNode = currentNode.next; // Move to the next node
        }

        return dummy.next; // Return the head of the sorted linked list
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        ListNode[] lists = new ListNode[3];

        lists[0] = new ListNode(1);
        lists[0].next = new ListNode(4);
        lists[0].next.next = new ListNode(5);

        lists[1] = new ListNode(1);
        lists[1].next = new ListNode(3);
        lists[1].next.next = new ListNode(4);

        lists[2] = new ListNode(2);
        lists[2].next = new ListNode(6);

        ListNode mergedList = solution.mergeKLists(lists);

        ListNode printNode = mergedList;
        while (printNode != null) {
            System.out.print(printNode.val + " ");
            printNode = printNode.next;
        }
    }
}
```

### 2. Min-Heap (Priority Queue) (CODE)

```java
import java.util.PriorityQueue;

class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; }
}

class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        // Step 1: Create a min-heap (priority queue) to store the nodes
        PriorityQueue<ListNode> minHeap = new PriorityQueue<>(
            (a, b) -> Integer.compare(a.val, b.val) // Compare node values for the heap
        );

        // Step 2: Add the head of each linked list to the min-heap
        for (int i = 0; i < lists.length; i++) {
            if (lists[i] != null) { // Check if the list is not empty
                minHeap.offer(lists[i]); // Add the head node to the heap
            }
        }

        // Step 3: Create a dummy node to simplify merging the lists
        ListNode dummy = new ListNode(0); // Dummy node
        ListNode current = dummy; // Pointer to construct the merged list

        // Step 4: Extract the nodes from the heap and build the merged linked list
        while (!minHeap.isEmpty()) {
            ListNode node = minHeap.poll(); // Get the node with the smallest value
            current.next = node; // Add it to the merged list
            current = current.next; // Move the current pointer

            // If there's a next node in the list, add it to the min-heap
            if (node.next != null) {
                minHeap.offer(node.next); // Add the next node to the heap
            }
        }

        return dummy.next; // Return the head of the merged linked list
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        ListNode[] lists = new ListNode[3];

        lists[0] = new ListNode(1);
        lists[0].next = new ListNode(4);
        lists[0].next.next = new ListNode(5);

        lists[1] = new ListNode(1);
        lists[1].next = new ListNode(3);
        lists[1].next.next = new ListNode(4);

        lists[2] = new ListNode(2);
        lists[2].next = new ListNode(6);

        ListNode mergedList = solution.mergeKLists(lists);

        ListNode printNode = mergedList;
        while (printNode != null) {
            System.out.print(printNode.val + " ");
            printNode = printNode.next;
        }
    }
}
```

