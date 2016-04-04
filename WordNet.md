title: WordNet
categories: 
    - Project
    - Princeton Algorithms
tags: [Breadth-First Search,Directed Graph]
date: 2016-04-03
---
<img src="https://farm2.staticflickr.com/1659/26127972192_c5a171e7a4_z.jpg" width="552" height="404">
### Introduction
> <font size=2>WordNet is a lexical database for the English language. It groups English words into sets of synonyms called synsets, provides short definitions and usage examples, and records a number of relations among these synonym sets or their members. WordNet can thus be seen as a combination of dictionary and thesaurus. - [wikipedia](https://en.wikipedia.org/wiki/WordNet)</font>

WordNet has been used for a number of different purposes in information systems, including word sense disambiguation, information retrieval, automatic text classification, automatic text summarization, machine translation and even automatic crossword puzzle generation
<!--more-->
----       

### Directed/Undirected Graph

> <font size=2>An undirected graph is a graph in which edges have no orientation. The edge (x, y) is identical to the edge (y, x), i.e., they are not ordered pairs, but sets {x, y} (or 2-multisets) of vertices. The maximum number of edges in an undirected graph without a loop is n(n − 1)/2. - [wikipedia][1]</font>
[1]:https://en.wikipedia.org/wiki/Graph_(discrete_mathematics)#Undirected_graph

> <font size=2>A directed graph or digraph is a graph in which edges have orientations. It is written as an ordered pair G = (V, A) (sometimes G = (V, E)) with: V a set whose elements are called vertices, nodes, or points; A a set of ordered pairs of vertices, called arrows, directed edges (sometimes simply edges with the corresponding set named E instead of A), directed arcs, or directed lines. - [wikipedia][2]</font>
[2]:https://en.wikipedia.org/wiki/Graph_(discrete_mathematics)#Directed_graph


|<font size=1>  Representation </font> |<font size=1>  Space  </font>|<font size=1>  Insert edge from v to w  </font>|<font size=1>  Edge from v to w  </font>|<font size=1>  Iterate over vertices pointing from v  </font>|
| :| |
|<font size=1>  list of edges </font> |<font size=1>  E  </font>|<font size=1>  1  </font>|<font size=1>  E  </font>|<font size=1>  E  </font>|
|<font size=1>  adjacency matrix </font> |<font size=1>  V*V  </font>|<font size=1>  1  </font>|<font size=1>  1  </font>|<font size=1>  V  </font>|
|<font size=1>  adjacency lists </font> |<font size=1>  E+V  </font>|<font size=1>  1  </font>|<font size=1>  outdegree(V)  </font>|<font size=1>  outdegree(V)  </font>|


<font size=2>**Graph Representations**</font>


### Depth-First Search/Breadth-First Search
> <font size=2>Depth-first search (DFS) is an algorithm for traversing or searching tree or graph data structures. One starts at the root (selecting some arbitrary node as the root in the case of a graph) and explores as far as possible along each branch before backtracking - [wikipedia](https://en.wikipedia.org/wiki/Depth-first_search)<br/>
Breadth-first search (BFS) is an algorithm for traversing or searching tree or graph data structures. It starts at the tree root (or some arbitrary node of a graph, sometimes referred to as a 'search key'[1]) and explores the neighbor nodes first, before moving to the next level neighbors - [wikipedia](https://en.wikipedia.org/wiki/Breadth-first_search)</font>

----
### Project
For this project, we use 2 hashmap to store id2noun and noun2id pairs separately, and use a Digraph class to create a digraph, use BreadthFirstDirectedPaths class to find a shortest ancestral path between two nouns in the digraph.

The goal of this project is to implement 3 class:
```java
public class WordNet {

   // constructor takes the name of the two input files
   public WordNet(String synsets, String hypernyms)

   // returns all WordNet nouns
   public Iterable<String> nouns()

   // is the word a WordNet noun?
   public boolean isNoun(String word)

   // distance between nounA and nounB (defined below)
   public int distance(String nounA, String nounB)

   // a synset (second field of synsets.txt) that is the common ancestor of nounA and nounB
   // in a shortest ancestral path (defined below)
   public String sap(String nounA, String nounB)

   // do unit testing of this class
   public static void main(String[] args)
}

public class SAP {

   // constructor takes a digraph (not necessarily a DAG)
   public SAP(Digraph G)

   // length of shortest ancestral path between v and w; -1 if no such path
   public int length(int v, int w)

   // a common ancestor of v and w that participates in a shortest ancestral path; -1 if no such path
   public int ancestor(int v, int w)

   // length of shortest ancestral path between any vertex in v and any vertex in w; -1 if no such path
   public int length(Iterable<Integer> v, Iterable<Integer> w)

   // a common ancestor that participates in shortest ancestral path; -1 if no such path
   public int ancestor(Iterable<Integer> v, Iterable<Integer> w)

   // do unit testing of this class
   public static void main(String[] args)
}

public class Outcast {
   public Outcast(WordNet wordnet)         // constructor takes a WordNet object
   public String outcast(String[] nouns)   // given an array of WordNet nouns, return an outcast
   public static void main(String[] args)  // see test client below
}
```

The first class is to store 2 files' information. Use two hashmap to store synsets and use Digraph to store hypernyms.
The second class is to find the shortest ancestral paths. Use BreadthFirstDirectedPaths to find distance between two nouns, and traverse every nouns from the digraph to find a noun with minimum distance to the given 2 nouns.
The third class is to compute the sum of the distances between each noun and every other one and return a noun for which distance is maximum.


> **Note:**
>  1. one noun could have multiple ids and one id could have multiple nouns, so in the hashmap noun2id, the value should be a list of ids.
>  2. In order to check the given file is a legal digraph, we need to use a DirectedCycle class to check if digraph has a cycle, and the number of edges should be equal or bigger than the number of vertexs.
>  3. To find a noun with the minimum distance to two given nouns, there should ba a path between this noun and the two given nouns, also the distance from this noun to the two given nouns should be the smallest compared with other nouns in the digraph.

- [project detail discription](http://coursera.cs.princeton.edu/algs4/assignments/wordnet.html)

- [github code link](https://github.com/xiaofeixiawang/algorithms/tree/master/princeton_hw6/src)

---
