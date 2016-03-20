title: Slow and Fast Pointers
categories:
    - LeetCode
tags: [LinkedList, List]
date: 2016-03-19
---
![](https://farm2.staticflickr.com/1694/25922739775_553ece5c1f_z.jpg)

### Introduction
When dealing with list data structure, we can’t get the middle item as the array using index of the item. Normally, we can traverse the list to get the length of the list, N. Then we traverse the list again stopping at N/2. It makes us to traverse the list two time. Actually, with the help of the two pointers, we can find the middle item traversing once.
<!--more-->
### Find the Middle Item
Finding the middle item of a list, we create a slow pointer and fast pointer. In each loop when we traverse the list, the slow pointer step one distance and the fast pointer step two distance. Showing in the above figure, we can find that when the fast pointer reach the end of the list, the slow pointer’s position is at the middle of the item. A lot of problems used this method, such as Merge sort and Delete the Last nth node of a list.


```java
ListNode slow = head;
ListNode fast = head.next;

while(fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
}
```

### Find the Existence of Cycle in a List
Slow and fast pointers also can be used in finding the existence of cycle in a List. Firstly, the slow and faster pointers are at the beginning of the list. Based on the character of two pointers, the fast pointer must move into the cycle before the slow pointer. Then the slow pointer enter into the cycle. The faster pointer will meet the slow point due to the difference of their speeds. The detail is shown by following pictures.
![](https://farm2.staticflickr.com/1550/25922739785_d7bdfd99ef_z.jpg)
![](https://farm2.staticflickr.com/1556/25922739795_448152ea9d_z.jpg)

``` java
    public boolean hasCycle(ListNode head) {
        if(head == null || head.next == null) return false;
        ListNode slow = head;
        ListNode fast = head.next;

        while(fast != null && fast.next != null) {
            if(slow == fast) return true;
            if(fast == null || slow == null) return false;
            slow = slow.next;
            fast = fast.next.next;
        }

        return false;
}
```
### Find the Existence of Cycle in a List II
Besides finding the existence of cycle, we also can find the beginning position of the cycle. After we make sure that there is a cycle in the list. Then we move the fast pointer to the beginning of the list and the fast pointer should have the same speed as slow pointer. The position the slow pointer and fast pointer meet again is the beginning of the cycle.

### Conclusion
Slow and fast pointers can be used in many situation in dealing with the list. You will meet them again and again when dealing with list data structure. Practice makes perfect.
