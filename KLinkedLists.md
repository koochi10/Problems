### Problem
Merge k sorted linked lists and return it as one sorted list. [https://leetcode.com/problems/merge-k-sorted-lists/description/]

#### Approach #1 Brute Force [Time Limit Exceeded]

**Intuition**

Okay we have K Linked Lists in an array an we want to combine the nodes into a singular sorted Linked List. We can visualize each sorted linked List in array like this:

![K Linked Lists](https://i.imgur.com/dmMYdyH.png)

Okay cool, now if we want to find the next smallest element all we have to do is iterate through our array to calculate the smallest node. From there we can steal that node from its respective linked lists and iterate it's list respectively.

![K Linked Lists](http://i.imgur.com/gM4q9YG.png)

Since 1 is the smallest element in our array we take it out and add it to our newly created Linked lists. If we repeat this process eventually we will get our new fully sorted linked list consisted from all the nodes in our diagram.


**Algorithm**

Here is one implementation of the above algorithm. Suppose we have a function `ListNode mergeKLists(ListNode[] lists)` ....

**Java**

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        if(lists == null || lists.length == 0)
            return null;
        ListNode sentinel = new ListNode(-1);
        ListNode node = sentinel;
        while(true) {
            //find the next smallest
            int smallest = Integer.MAX_VALUE;
            int smallNodeIndex = -1;
            for(int i = 0; i<lists.length; i++) {
                if(lists[i] == null) {
                    continue;
                }
                if(lists[i].val <= smallest) {
                    smallNodeIndex = i;
                    smallest = lists[i].val;
                }
            }
            if(smallNodeIndex == -1)
                break;
            node.next = lists[smallNodeIndex];
            node = node.next;
            lists[smallNodeIndex] = lists[smallNodeIndex].next;
        }
        return sentinel.next;
    }
}
```

**Complexity Analysis**

* Time complexity : O(n*k). Where n is the length of our linked lists.

---
#### Approach #2 Priority Queue [Accepted]

**Algorithm**

If we look closely at our above algorithm we notice some inefficient searching. Every time we look for the next smallest element in our array we have to reiterate over all the elements in the array even though we would have only changed one element. To solve this problem we can use a Priority Queue or a Heap that orders our nodes in a tree like fashion where pulling out the next smallest element would take O(log(n)) time instead of O(n) that we are currently doing.
Here is one possible implementation utilizing the priority queue.
**Java**

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        Comparator<ListNode> comparer = new Comparator<ListNode>() {
		    @Override
		    public int compare(ListNode left, ListNode right) {
                //return -1 if less than 0 if equal or 1 if greater
                if(left.val < right.val)
                    return -1;
                else if(left.val > right.val)
                    return 1;
                else
                    return 0;
		    }
		};
        PriorityQueue<ListNode> queue = new PriorityQueue(comparer);
        HashMap<ListNode, Integer> map = new HashMap<>();
        for(int i = 0; i<lists.length; i++) {
            if(lists[i] != null) {
                map.put(lists[i], i);
                queue.add(lists[i]);
            }
        }
        ListNode sentinel = new ListNode(0);
        ListNode currNode = sentinel;
        while(queue.size() > 0) {
            ListNode node = queue.poll();
            currNode.next= node;
            currNode = currNode.next;

            //iterate one more position in the linkedlist at the specific index and add the new node to the Queue

            int index = map.get(node);
            map.remove(node);

            lists[index] = lists[index].next;
            if(lists[index] != null) {
                map.put(lists[index], index);
                queue.add(lists[index]);
            }
        }
        return sentinel.next;
    }
}
```

**Complexity Analysis**

* Time complexity :O(nlog(k)). Where n is the length of our linked lists
* Space complexity : O(n*k). Accounts for the space in our Priority Queue / Map
