title: Randomized Queues and Deques
categories: project:princeton algorithms
tags: Queue
date: 2016-02-22
---

### Introduction
> <font size=2>In computer science, a queue is a particular kind of abstract data type or collection in which the entities in the collection are kept in order and the principal (or only) operations on the collection are the addition of entities to the rear terminal position, known as enqueue, and removal of entities from the front terminal position, known as dequeue. - [wikipedia][1]</font>
[1]: https://en.wikipedia.org/wiki/Queue_(abstract_data_type)

Queues provide services in computer science, transport, and operations research where various entities such as data, objects, persons, or events are stored and held to be processed later. In these contexts, the queue performs the function of a buffer.
<!--more-->
----


### implementation

Queue could be implemented both by linked list and resized array. As for resized array, one of the things we'll need to keep track of is the number of elements in the queue. And when the number of element is out of bounds, we should enlarge the size of array. In contrast, when the number of element is less than 1/4 of the size of array, we should shrink the size of array. As for linked list, we only need two special sentinel nodes named head and tail to represent the two sides of the queue(in doubly-linked list)


|<font size=1>  queue operations </font> |<font size=1>  Time Complexity(resized array)  </font>|<font size=1> Time Complexity(linked list) </font>|
| :| |
|<font size=1>  enqueue </font> |<font size=1>  O(1)  </font>|<font size=1> O(1) </font>|
|<font size=1>  dequeue </font> |<font size=1>  O(n)  </font>|<font size=1> O(1) </font>|
|<font size=1>  size </font> |<font size=1>  O(1)  </font>|<font size=1> O(n) </font>|


<font size=1>**Time complexity of different operations based on two implementatioin**</font>

---
### project
For this project, we need to implement two different kind of queue.
**Dequeue**: A double-ended queue or deque (pronounced "deck") is a generalization of a stack and a queue that supports adding and removing items from either the front or the back of the data structure.
**Randomized queue**: A randomized queue is similar to a stack or queue, except that the item removed is chosen uniformly at random from items in the data structure.
The API are as follows:
```java
public class Deque<Item> implements Iterable<Item> {
   public Deque()                           // construct an empty deque
   public boolean isEmpty()                 // is the deque empty?
   public int size()                        // return the number of items on the deque
   public void addFirst(Item item)          // add the item to the front
   public void addLast(Item item)           // add the item to the end
   public Item removeFirst()                // remove and return the item from the front
   public Item removeLast()                 // remove and return the item from the end
   public Iterator<Item> iterator()         // return an iterator over items in order from front to end
   public static void main(String[] args)   // unit testing
}

public class RandomizedQueue<Item> implements Iterable<Item> {
   public RandomizedQueue()                 // construct an empty randomized queue
   public boolean isEmpty()                 // is the queue empty?
   public int size()                        // return the number of items on the queue
   public void enqueue(Item item)           // add the item
   public Item dequeue()                    // remove and return a random item
   public Item sample()                     // return (but do not remove) a random item
   public Iterator<Item> iterator()         // return an independent iterator over items in random order
   public static void main(String[] args)   // unit testing
}
```

All of the above mentioned operations should be realized in constant time.


> **Note:**
>  1. For the Deque, we should use doubly linked list to implement. When insert or remove, considering a special situation.
>  2. For the RandomizedQueue, every time we remove a element, we could put the last element to this position.

- [project detail discription](http://coursera.cs.princeton.edu/algs4/assignments/queues.html)

- [github code link](https://github.com/xiaofeixiawang/algorithms/tree/master/princeton_hw2/src)

---
