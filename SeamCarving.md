title: Seam Carving
categories: 
    - Project
    - Princeton Algorithms
tags: [Depth-First Search,BackTracking]
date: 2016-04-07
---
<img src="https://farm2.staticflickr.com/1480/25664295713_11df0debd8_z.jpg" width="640" height="211">
### Introduction
> <font size=2>Seam carving (or liquid rescaling) is an algorithm for content-aware image resizing. It functions by establishing a number of seams (paths of least importance) in an image and automatically removes seams to reduce image size or inserts seams to extend it. - [wikipedia](https://en.wikipedia.org/wiki/Seam_carving)</font>

The purpose of the algorithm is image retargeting, which is the problem of displaying images without distortion on media of various sizes (cell phones, projection screens) using document standards, like HTML, that already support dynamic changes in page layout and text but not images.
<!--more-->
----       

### Minimum Spanning Tree

> <font size=2>A minimum spanning tree is a spanning tree of a connected, undirected graph. It connects all the vertices together with the minimal total weighting for its edges. - [wikipedia](https://en.wikipedia.org/wiki/Minimum_spanning_tree)</font>

Usually we use greedy algorithm to find a minimum spanning tree from a undirected graph. A greedy algorithm is an algorithm that follows the problem solving heuristic of making the locally optimal choice at each stage with the hope of finding a global optimum.

Usually there are two main greedy algorithm implementations: Kruskal's algorithm and Prim's algorithm. The first one finds an edge of the least possible weight that connects any two trees in the forest, and computes MST in time proportional to ElogE. While the second one build the tree one vertex at a time, from an arbitrary starting vertex, at each step adding the cheapest possible connection from the tree to another vertex, and computes MST in time proportional to ElogV.

### Shortest Path

In graph theory, the shortest path problem is to find a path between two vertices in a graph such that the sum of the weights of its constituent edges is minimized. Specifically, single source shortest path is to find shortest path from one vertex s to every other vertex.

Usually there are three main shortest path algorithms: Dijkstra's Algorithm, Topological sort Algorithm and Bellman-Ford Algorithm. The first one is consider vertices in increasing order of distance from starting vertex and add vertex to tree and relax all edges pointing from that vertex. While the second one is consider vertices in topological order and relax all edges pointing from that vertex. And the third one is to repeat V times of relaxing all E edges.


|<font size=1>  algorithm </font> |<font size=1>  restriction  </font>|<font size=1>  typical case  </font>|<font size=1>  worst case  </font>|<font size=1>  extra space  </font>|
| :| |
|<font size=1>  topological sort </font> |<font size=1>  no directed cycles  </font>|<font size=1>  E+V  </font>|<font size=1>  E+V  </font>|<font size=1>  V  </font>|
|<font size=1>  Dijkstra </font> |<font size=1>  no negative weights  </font>|<font size=1>  ElogV  </font>|<font size=1>  ElogV  </font>|<font size=1>  V  </font>|
|<font size=1>  Bellman-Ford </font> |<font size=1>  no negative cycles  </font>|<font size=1>  EV  </font>|<font size=1>  EV  </font>|<font size=1>  V  </font>|


<font size=2>**single source shortest-paths implementation: cost summary**</font>

----
### Project
For this project, we at first calculate the energy of each pixel for the given picture, then find a vertical or horizontal seam of minimum total energy with a DFS algorithm, finally remove the seam from the image.

The goal of this project is to implement 3 class:
```java
public class SeamCarver {
   public SeamCarver(Picture picture)                // create a seam carver object based on the given picture
   public Picture picture()                          // current picture
   public     int width()                            // width of current picture
   public     int height()                           // height of current picture
   public  double energy(int x, int y)               // energy of pixel at column x and row y
   public   int[] findHorizontalSeam()               // sequence of indices for horizontal seam
   public   int[] findVerticalSeam()                 // sequence of indices for vertical seam
   public    void removeHorizontalSeam(int[] seam)   // remove horizontal seam from current picture
   public    void removeVerticalSeam(int[] seam)     // remove vertical seam from current picture
}
```


> **Note:**
>  1. the given picture could be removed seam for multiple times, so every time to find a new seam, we should calculate the energy for each pixel of the picture again.
>  2. For finding the path with minimum sum of energies, we create two 2-d arrays, storing path and sum of energies starting from the last pixel separately. And use BackTracking algorithm to add the energies and update path from last pixel to first one. Every time calculating the sumEnergy for current layer, choose the minimum sumEnergy from the next layer.

- [project detail discription](http://coursera.cs.princeton.edu/algs4/assignments/seamCarving.html)

- [github code link](https://github.com/xiaofeixiawang/algorithms/tree/master/princeton_hw7/src)

---
