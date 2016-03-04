title: Percolation Theory
categories: 
    - project
    - princeton algorithms
tags: union-find
date: 2016-02-20
---
<img src="/images/percolation.png">
### Introduction
> <font size=2>In statistical physics and mathematics, percolation theory describes the behavior of connected clusters in a random graph. The applications of percolation theory to materials science and other domains are discussed in the article percolation. - [wikipedia](https://en.wikipedia.org/wiki/Percolation_theory)</font>

Percolation model can be used in  many situations: It can represent how gas go through to the surface or how to find whether 2 people have connection in a social network. We can use union-find algorithm to build the connection.
<!--more-->
----       


### Union Find

> <font size=2>Union Find is an algorithm which uses a disjoint-set data structure to solve the following problem: Say we have some number of items. We are allowed to merge any two items to consider them equal (where equality here obeys all of the properties of an Equivalence Relation). At any point, we are allowed to ask whether two items are considered equal or not. - [algorithmist](http://www.algorithmist.com/index.php/Union_Find)</font>


|<font size=1>  Algorithms </font> |<font size=1>  Time Complexity  </font>|
| :| |
| <font size=1> Quick find </font>|<font size=1>  M N </font>|
| <font size=1> Quick union </font>| <font size=1>M N </font>|
| <font size=1> weighted QU</font> | <font size=1>N + M log n </font>|
| <font size=1> weighted QU + path compression</font> |<font size=1> N + M lg* N </font>|


<font size=1>**M union-find operations on a set of N objects**</font>

----
### Project
For this project, we use a N by N grid to represent the percolation model, at beginning all the sites of the grid is blocked, we randomly open the sites until the system is be percolated.

The goal of this project is to implement 2 class:
```java
 public class Percolation {
   public Percolation(int N) // create N-by-N grid, with all sites blocked
   public void open(int i, int j) // open site if it is not open already
   public boolean isOpen(int i, int j)  // is site (row i, column j) open?
   public boolean isFull(int i, int j)  // is site (row i, column j) full?
   public boolean percolates()   // does the system percolate?
   public static void main(String[] args)  // test client (optional)
}

public class PercolationStats {
   public PercolationStats(int N, int T)  // perform T independent experiments on an N-by-N grid
   public double mean()  // sample mean of percolation threshold
   public double stddev()  // sample standard deviation of percolation threshold
   public double confidenceLo()   // low  endpoint of 95% confidence interval
   public double confidenceHi()   // high endpoint of 95% confidence interval
   public static void main(String[] args)  // test client (described below)
}
```

The first class is to simulate the percolation model, We need to open certain site and give sites' condition.

The second is to estimate the value of the percolation threshold via **Monte Carlo simulation**.


> **Note:**
>  1. The way to reduce time complexity  is to create 2 **virtual nodes**, one is the top and one is the bottom.
>  2. We need to consider how to avoid the backwash problem. The easy way to solve this problem is to use 2 union-find object to trace `all sites + 2 virtual nodes` and `all sites + the top virtual node`.

- [project detail discription](http://coursera.cs.princeton.edu/algs4/assignments/percolation.html)

- [github code link(1)](https://github.com/StevenZhao7/algs4.git)
- [github code link(2)](https://github.com/xiaofeixiawang/algorithms/tree/master/princeton_hw1/src)

----
