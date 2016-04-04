title: KdTree
categories: 
    - Project
    - Princeton Algorithms
tags: [Tree, Binary Search Tree, Balanced Search Tree]
date: 2016-02-28
---
<img src="https://farm2.staticflickr.com/1638/25284856443_8862b317c4_m.jpg" width="145" height="143">
### Introduction
> <font size=2>The k-d tree is a binary tree in which every node is a k-dimensional point. Every non-leaf node can be thought of as implicitly generating a splitting hyperplane that divides the space into two parts, known as half-spaces. Points to the left of this hyperplane are represented by the left subtree of that node and points right of the hyperplane are represented by the right subtree. - [wikipedia](https://en.wikipedia.org/wiki/K-d_tree)</font>

k-d trees are useful data structure for several applications, such as searches involving a multidimensional search key (e.g. range searches and nearest neighbor searches). k-d trees are a special case of binary space partitioning trees.
<!--more-->
----       


### Balanced search tree

> <font size=2>Balanced search tree is a kind of node-based binary search tree where costs are guaranteed to be logarithmic. Such trees hvae near-perfect balance, where the height is guaranteed to be no larger than 2lgN. This structures provide efficient implementations for mutable ordered lists, and can be used for other abstract data structures such as associative arrays, priority queues and sets. - [wikipedia](https://en.wikipedia.org/wiki/Self-balancing_binary_search_tree)</font>


|<font size=1>  data structure </font> |<font size=1>  search hit  </font>|<font size=1>  insert  </font>|<font size=1>  delete  </font>|
| :| |
|<font size=1>  binary search tree </font> |<font size=1>  1.39lgN  </font>|<font size=1>  1.39lgN  </font>|<font size=1>  ?  </font>|
|<font size=1>  2-3 tree(BST) </font> |<font size=1>  clgN  </font>|<font size=1>  clgN  </font>|<font size=1>  clgN  </font>|
|<font size=1>  red-black BST </font> |<font size=1>  lgN  </font>|<font size=1>  lgN  </font>|<font size=1>  lgN  </font>|


<font size=1>**time complexity of different data structures in average case**</font>

----
### Project
For this project, we usc both red-black BST and 2d-tree to represent a set of points in the unit square to support efficient range search and nearest neighbor search.

The goal of this project is to implement class for red-black BST and 2d-tree. Both of them have the below interfaces.
```java
public class PointSET {
   public         PointSET()                               // construct an empty set of points 
   public           boolean isEmpty()                      // is the set empty? 
   public               int size()                         // number of points in the set 
   public              void insert(Point2D p)              // add the point to the set (if it is not already in the set)
   public           boolean contains(Point2D p)            // does the set contain point p? 
   public              void draw()                         // draw all points to standard draw 
   public Iterable<Point2D> range(RectHV rect)             // all points that are inside the rectangle 
   public           Point2D nearest(Point2D p)             // a nearest neighbor in the set to point p; null if the set is empty 

   public static void main(String[] args)                  // unit testing of the methods (optional) 
}
```

This class will use two predefined classes named Point2D and RectHV. Below are their interfaces.
```java
public class Point2D implements Comparable<Point2D> {
   public Point2D(double x, double y)              // construct the point (x, y)
   public  double x()                              // x-coordinate 
   public  double y()                              // y-coordinate 
   public  double distanceTo(Point2D that)         // Euclidean distance between two points 
   public  double distanceSquaredTo(Point2D that)  // square of Euclidean distance between two points 
   public     int compareTo(Point2D that)          // for use in an ordered symbol table 
   public boolean equals(Object that)              // does this point equal that object? 
   public    void draw()                           // draw to standard draw 
   public  String toString()                       // string representation 
}

public class RectHV {
   public    RectHV(double xmin, double ymin,      // construct the rectangle [xmin, xmax] x [ymin, ymax] 
                    double xmax, double ymax)      // throw a java.lang.IllegalArgumentException if (xmin > xmax) or (ymin > ymax)
   public  double xmin()                           // minimum x-coordinate of rectangle 
   public  double ymin()                           // minimum y-coordinate of rectangle 
   public  double xmax()                           // maximum x-coordinate of rectangle 
   public  double ymax()                           // maximum y-coordinate of rectangle 
   public boolean contains(Point2D p)              // does this rectangle contain the point p (either inside or on boundary)? 
   public boolean intersects(RectHV that)          // does this rectangle intersect that rectangle (at one or more points)? 
   public  double distanceTo(Point2D p)            // Euclidean distance from point p to closest point in rectangle 
   public  double distanceSquaredTo(Point2D p)     // square of Euclidean distance from point p to closest point in rectangle 
   public boolean equals(Object that)              // does this rectangle equal that object? 
   public    void draw()                           // draw to standard draw 
   public  String toString()                       // string representation 
}
```


> **Note:**
>  1. in 2d-tree, we create a class Node to store each point, which contains two chindren and a boolean to judge whether this node is compared by x or y value. Search and insert operations are similar to BST, but at the root we use the x-coordinate, then at the next level, we use the y-coordiate, then at the next level the x-coordiate, and so forth.
>  2. For the range search operation, if the query rectangle does not intersect the rectangle corresponding to a node, we don't need to explore this subtrees.
>  3. For the nearest neighbor search operation, if distance with current closest point is smaller than the distance between the query point and the rectangle corresponding to a node, we don't need to explore this subtrees.

- [project detail discription](http://coursera.cs.princeton.edu/algs4/assignments/kdtree.html)

- [github code link](https://github.com/xiaofeixiawang/algorithms/tree/master/princeton_hw5/src)

---
