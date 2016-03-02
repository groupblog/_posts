title: Collinear Points
categories: project:princeton algorithms
tags: sorting
date: 2016-02-24
---
![](/images/pattern recognition.png)
### Introduction
> <font size=2>A sorting algorithm is an algorithm that puts elements of a list in a certain order. The most-used orders are numerical order and lexicographical order. More formally, the output must satisfy two conditions:  
1. The output is in nondecreasing order (each element is no smaller than the previous element according to the desired total order);  
2. The output is a permutation (reordering) of the input.  
- [wikipedia](https://en.wikipedia.org/wiki/Sorting_algorithm)</font>

Efficient sorting is important for optimizing the use of other algorithms (such as search and merge algorithms) which require input data to be in sorted lists; it is also often useful for canonicalizing data and for producing human-readable output.
<!--more-->
----


### sorting types

1. selection sort: This algorithm divides the input list into two parts: the left part is sorted, and the rest is unsorted. Then choose the smallest element in the unsorted list, exchanging it with the leftmost unsorted element, so that sorted list would be one more element bigger.  
2. inserting sort: This algorithm also divides the list into two parts. And iterates getting one element from unsorted list, finds the location it belongs within the sorted list, inserts it there. It repeats until no input elements remain.
3. shell sort: This algorithm is similar to inserting sort. It arrange the list of element by sorting only every **h**th element start from anyone, then choose a smaller h to sort until h equals to 1.  
4. merge sort: This algorithm works in two steps. First, divide the unsorted list into n sublists, each containing 1 element so that these sublisted are considered sorted. Second, iterates merging neighbor sublists to form a new sorted sublist. Finally there would be only one sorted list.
5. quick sort: This algorithm belongs to divide and conquer. quick sort at first choose a pivot from array, then put all low element to left, high element to right in the array. Then recursively dealing with the subarrays with the same procedure.

|<font size=1>  types </font> |<font size=1>  Best  </font>|<font size=1> Average </font>|<font size=1> Worst </font>|<font size=1> Memory </font>|<font size=1> Stable </font>|
| :| |
|<font size=1>  selection sort </font> |<font size=1>  n*n  </font>|<font size=1> n*n </font>|<font size=1> n*n </font>|<font size=1> 1 </font>|<font size=1> no </font>|
|<font size=1>  inserting sort </font> |<font size=1>  n  </font>|<font size=1> n*n </font>|<font size=1> n*n </font>|<font size=1> 1 </font>|<font size=1> yes </font>|
|<font size=1>  shell sort </font> |<font size=1>  n  </font>|<font size=1> n^(3/2) </font>|<font size=1> nlog^2(n) </font>|<font size=1> 1 </font>|<font size=1> no </font>|
|<font size=1>  merge sort </font> |<font size=1>  nlogn  </font>|<font size=1> nlogn </font>|<font size=1> nlogn </font>|<font size=1> n </font>|<font size=1> yes </font>|
|<font size=1>  quick sort </font> |<font size=1>  nlogn  </font>|<font size=1> nlogn </font>|<font size=1> n*n </font>|<font size=1> logn </font>|<font size=1> no </font>|

<font size=1>**Time, Space complexity and stability of different sorting algorithms**</font>

---
### project
For this project, we should write a program to recognize line patterns in a given set of points. More concretely, given a set of N distinct points in the plane, find every line segment that connects a subset of 4 or more of the points.  

The goal of this project is to implement 3 class(LineSegment is already provided):  
```java
public class Point implements Comparable<Point> {
   public Point(int x, int y)                         // constructs the point (x, y)

   public   void draw()                               // draws this point
   public   void drawTo(Point that)                   // draws the line segment from this point to that point
   public String toString()                           // string representation

   public               int compareTo(Point that)     // compare two points by y-coordinates, breaking ties by x-coordinates
   public            double slopeTo(Point that)       // the slope between this point and that point
   public Comparator<Point> slopeOrder()              // compare two points by slopes they make with this point
}
public class LineSegment {
   public LineSegment(Point p, Point q)        // constructs the line segment between points p and q
   public   void draw()                        // draws this line segment
   public String toString()                    // string representation
}
public class BruteCollinearPoints {
   public BruteCollinearPoints(Point[] points)    // finds all line segments containing 4 points
   public           int numberOfSegments()        // the number of line segments
   public LineSegment[] segments()                // the line segments
}
public class FastCollinearPoints {
   public FastCollinearPoints(Point[] points)     // finds all line segments containing 4 or more points
   public           int numberOfSegments()        // the number of line segments
   public LineSegment[] segments()                // the line segments
}
```

The first class definds a point, it should be comparable based on y-coordinates so that we could sort it in the later procedure. It also provide comparator which sort other points based on slope with this point.

The second class find all line segments who have 4 points lie on it by checking the three slopes. This solution could be pretty slow cause the running time could be N^4 in the worst case.

The third class also find all line segments, but it at first set every point as the origin then sort other points by slope, checking valid line segments. This solution could have a better running time: N^2(logN)

> **Note:**
>  1. We could use a hashmap to store every slope and corresponding line segments' largest point, checking if we already store this line or not.
>  2. In each traverse of all points based on one origin point, we could use a list to store all points having same slope. If current slope is different, we could just clean the list and reuse again.

- [project detail discription](http://coursera.cs.princeton.edu/algs4/assignments/collinear.html)

- [github code link](https://github.com/xiaofeixiawang/algorithms/tree/master/princeton_hw3/src)

---
