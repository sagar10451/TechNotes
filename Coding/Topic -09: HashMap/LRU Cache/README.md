# Question 05: LRU Cache

**Question:** 146. LRU Cache

## Approach-1: Queue(Deque) + HashMap

**Idea:**
- Use a Deque (double-ended queue) to maintain the order of keys based on their usage.
  - The front of the queue holds the least recently used (LRU) key.
  - The back of the queue holds the most recently used key.
- Use a HashMap to store the key-value pairs for O(1) access.
  - The key is stored in both the queue and the HashMap.
  - The value is stored only in the HashMap.

**Operations:**

**get(key):**
- Check if the key exists in the HashMap.
- If it exists, remove it from the queue (to reset its order) and add it to the back of the queue. Return the value from the HashMap.
- If it doesn't exist, return -1.

**put(key, value):**
- If the key already exists, update its value in the HashMap and refresh its position in the queue.
- If the key doesn't exist and the cache is at capacity, remove the front key from the queue and delete it from the HashMap.
- Add the new key to the back of the queue and store its value in the HashMap.

### Time Complexity:
- **get(key):** O(n) in the worst case due to deque adjustment (removing from middle).
- **put(key, value):** O(n) in the worst case.

### Space Complexity:
- O(capacity) — Both the HashMap and Deque store up to capacity elements.

### Code:

```java
import java.util.Deque; // Import for double-ended queue
import java.util.HashMap; // Import for HashMap
import java.util.LinkedList; // Import for LinkedList implementation of Deque

// Class to implement LRU Cache functionality
class LRUCache {
    private final int capacity; // Maximum capacity of the cache
    private final Deque<Integer> queue; // Deque to store keys in the order of their usage
    private final HashMap<Integer, Integer> cache; // HashMap to store key-value pairs

    // Constructor to initialize the cache with a given capacity
    public LRUCache(int capacity) {
        this.capacity = capacity; // Set the maximum capacity
        this.queue = new LinkedList<>(); // Initialize the queue (doubly linked list)
        this.cache = new HashMap<>(); // Initialize the HashMap for storing key-value pairs
    }

    // Method to get the value associated with a key
    public int get(int key) {
        // Check if the key exists in the cache (HashMap)
        if (!cache.containsKey(key)) {
            return -1; // If the key is not found, return -1
        }

        // Key is accessed; refresh its position in the queue
        queue.remove(key); // Remove the key from its current position in the queue
        queue.addLast(key); // Add the key to the back of the queue (most recently used position)

        return cache.get(key); // Return the value associated with the key from the HashMap
    }

    // Method to add a new key-value pair to the cache or update an existing key
    public void put(int key, int value) {
        // Check if the key already exists in the cache
        if (cache.containsKey(key)) {
            cache.put(key, value); // Update the value in the HashMap
            queue.remove(key); // Refresh the key's position by removing it from the queue
            queue.addLast(key); // Add the key to the back of the queue
        } else {
            // If the cache is full (size equals capacity), evict the least recently used key
            if (queue.size() == capacity) {
                int leastUsedKey = queue.pollFirst(); // Remove the front key from the queue (least recently used)
                cache.remove(leastUsedKey); // Remove the least recently used key from the HashMap
            }

            // Add the new key-value pair to the cache
            queue.addLast(key); // Add the new key to the back of the queue
            cache.put(key, value); // Store the key-value pair in the HashMap
        }
    }

    // Main method to demonstrate the functionality of the LRU Cache
    public static void main(String[] args) {
        LRUCache lruCache = new LRUCache(2); // Create an LRU Cache with a capacity of 2

        // Add key-value pairs to the cache
        lruCache.put(1, 1); // Cache: {1=1}
        lruCache.put(2, 2); // Cache: {1=1, 2=2}

        // Retrieve a value from the cache
        System.out.println(lruCache.get(1)); // Output: 1 (Cache: {2=2, 1=1}, refreshes key 1)

        // Add another key-value pair, causing an eviction
        lruCache.put(3, 3); // Evicts key 2 (Cache: {1=1, 3=3})
        System.out.println(lruCache.get(2)); // Output: -1 (Key 2 is not in the cache)

        // Add another key-value pair, causing another eviction
        lruCache.put(4, 4); // Evicts key 1 (Cache: {3=3, 4=4})
        System.out.println(lruCache.get(1)); // Output: -1 (Key 1 is not in the cache)
        System.out.println(lruCache.get(3)); // Output: 3 (Cache: {4=4, 3=3}, refreshes key 3)
        System.out.println(lruCache.get(4)); // Output: 4 (Cache: {3=3, 4=4}, refreshes key 4)
    }
}
```

---

## Approach-2: Doubly Linked List + HashMap

**Doubly Linked List (DLL):**
- Each node in the DLL stores a key-value pair.
- DLL allows efficient insertion and deletion at both ends in O(1) time.
- Head of the list represents the most recently used (MRU) element.
- Tail of the list represents the least recently used (LRU) element.

**HashMap:**
- Maps each key to its corresponding node in the DLL.
- Ensures O(1) access to any key-value pair.

**Key Operations:**

**get(key):**
- Check if the key exists in the HashMap.
- If it exists, move the corresponding node to the head of the DLL (`addToHead()`) (mark as most recently used) and return the value.
- If it doesn't exist, return -1.

**put(key, value):**
- If the key already exists: Update the value and move the node to the head of the DLL (`addToHead()`)
- If the key doesn't exist: If the cache is full, remove the node at the tail (`remove()`) (least recently used). Add a new node at the head of the DLL and update the HashMap.

### Complexity Analysis:
- **Time Complexity:**
  - get: O(1) for HashMap access + O(1) for DLL adjustments.
  - put: O(1) for HashMap operations + O(1) for DLL adjustments.
- **Space Complexity:**
  - HashMap: Stores up to capacity elements → O(capacity).
  - Doubly Linked List: Stores up to capacity nodes → O(capacity).
  - Total: O(capacity).

### Code:

```java
import java.util.HashMap;

// Node class to represent each key-value pair in the doubly linked list
class Node {
    int key, value; // Key-value pair
    Node prev, next; // Pointers to the previous and next nodes in the list

    // Constructor to initialize a node
    Node(int key, int value) {
        this.key = key;
        this.value = value;
    }
}

// LRUCache class
class LRUCache {
    private final int capacity; // Maximum capacity of the cache
    private final HashMap<Integer, Node> map; // HashMap for key-to-node mapping
    private final Node head, tail; // Dummy head and tail nodes for the doubly linked list

    // Constructor to initialize the cache
    public LRUCache(int capacity) {
        this.capacity = capacity; // Set the capacity
        this.map = new HashMap<>(); // Initialize the HashMap

        // Create dummy head and tail nodes to simplify edge cases
        head = new Node(0, 0);
        tail = new Node(0, 0);
        head.next = tail;
        tail.prev = head;
    }

    // Method to get the value associated with a key
    public int get(int key) {
        if (!map.containsKey(key)) {
            return -1; // Return -1 if the key is not found
        }

        // Move the accessed node to the head of the DLL (mark as most recently used)
        Node node = map.get(key);
        remove(node);
        addToHead(node);

        return node.value; // Return the value
    }

    // Method to add or update a key-value pair in the cache
    public void put(int key, int value) {
        if (map.containsKey(key)) {
            // If the key already exists, update its value and move it to the head
            Node node = map.get(key);
            node.value = value;
            remove(node);
            addToHead(node);
        } else {
            // If the cache is full, remove the least recently used node
            if (map.size() == capacity) {
                Node lru = tail.prev; // Node at the tail is the least recently used
                remove(lru);
                map.remove(lru.key);
            }

            // Add the new key-value pair as the most recently used node
            Node newNode = new Node(key, value);
            map.put(key, newNode);
            addToHead(newNode);
        }
    }

    // Helper method to remove a node from the doubly linked list
    private void remove(Node node) {
        node.prev.next = node.next; // PreviousNode - Node - Tail // PreviousNode ka next ko Tail se point karo
        node.next.prev = node.prev; // PreviousNode - Node - Tail // Tail ka previous should point to PreviousNode
    }

    // Helper method to add a node to the head of the doubly linked list
    private void addToHead(Node node) {
        node.next = head.next; // head ka next(head.next) should point to (Node.next)
        head.next.prev = node; // Link the current head's next node back to the new node
        head.next = node; // Update the head's next pointer to the new node
        node.prev = head; // Link the new node back to the head
    }

    // Main method to test the LRU Cache
    public static void main(String[] args) {
        LRUCache lruCache = new LRUCache(2); // Create an LRU Cache with a capacity of 2

        lruCache.put(1, 1); // Add key 1 with value 1
        lruCache.put(2, 2); // Add key 2 with value 2
        System.out.println(lruCache.get(1)); // Access key 1, should return 1

        lruCache.put(3, 3); // Add key 3, evicts key 2 (LRU)
        System.out.println(lruCache.get(2)); // Access key 2, should return -1 (not found)

        lruCache.put(4, 4); // Add key 4, evicts key 1 (LRU)
        System.out.println(lruCache.get(1)); // Access key 1, should return -1 (not found)
        System.out.println(lruCache.get(3)); // Access key 3, should return 3
        System.out.println(lruCache.get(4)); // Access key 4, should return 4
    }
}
```
