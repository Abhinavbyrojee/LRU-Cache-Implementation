# LRU-Cache-Implementation
Implementation of LRU (Least Recently Used) Cache using core Data Structures. The project demonstrates O(1) get and put operations using Hash Map and Doubly Linked List. Designed to showcase strong understanding of DSA concepts and interview-level problem solving.
"""
LRU Cache Implementation using Data Structures

Data Structures Used:
- Hash Map (Dictionary)
- Doubly Linked List

Operations:
- get(key)  -> O(1)
- put(key,value) -> O(1)
"""

class Node:
    def __init__(self, key, value):
        self.key = key
        self.value = value
        self.prev = None
        self.next = None


class LRUCache:
    def __init__(self, capacity):
        if capacity <= 0:
            raise ValueError("Capacity must be greater than 0")

        self.capacity = capacity
        self.cache = {}  # Hash Map

        # Dummy head and tail nodes
        self.head = Node(0, 0)
        self.tail = Node(0, 0)

        self.head.next = self.tail
        self.tail.prev = self.head

    # Add node right after head (most recently used)
    def _add_node(self, node):
        node.prev = self.head
        node.next = self.head.next

        self.head.next.prev = node
        self.head.next = node

    # Remove an existing node from the linked list
    def _remove_node(self, node):
        prev_node = node.prev
        next_node = node.next

        prev_node.next = next_node
        next_node.prev = prev_node

    # Move accessed node to the front
    def _move_to_front(self, node):
        self._remove_node(node)
        self._add_node(node)

    # Remove least recently used node
    def _remove_lru(self):
        lru_node = self.tail.prev
        self._remove_node(lru_node)
        del self.cache[lru_node.key]

    def get(self, key):
        if key not in self.cache:
            return -1

        node = self.cache[key]
        self._move_to_front(node)
        return node.value

    def put(self, key, value):
        if key in self.cache:
            node = self.cache[key]
            node.value = value
            self._move_to_front(node)
        else:
            if len(self.cache) >= self.capacity:
                self._remove_lru()

            new_node = Node(key, value)
            self.cache[key] = new_node
            self._add_node(new_node)


# ------------------- DEMO -------------------
if __name__ == "__main__":
    cache = LRUCache(2)

    cache.put(1, "A")
    cache.put(2, "B")
    print(cache.get(1))  # Output: A

    cache.put(3, "C")    # Evicts key 2
    print(cache.get(2))  # Output: -1

    cache.put(4, "D")    # Evicts key 1
    print(cache.get(1))  # Output: -1
    print(cache.get(3))  # Output: C
    print(cache.get(4))  # Output: D
