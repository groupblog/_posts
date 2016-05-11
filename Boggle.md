title: Boggle
categories: 
    - Project
    - Princeton Algorithms
tags: [TrieTree, Depth-First Search]
date: 2016-05-11
---
<img src="https://farm8.staticflickr.com/7782/26923218076_6fbd8b3afe.jpg" width="460" height="215">
### Introduction
> <font size=2>Boggle is a world game, which could be solved by Trie data structure. In computer science, a trie, also called digital tree and sometimes radix tree or prefix tree (as they can be searched by prefixes), is an ordered tree data structure that is used to store a dynamic set or associative array where the keys are usually strings. - [wikipedia](https://en.wikipedia.org/wiki/Trie)</font>

A common application of a trie is storing a predictive text or autocomplete dictionary, such as found on a mobile telephone. Such applications take advantage of a trie's ability to quickly search for, insert, and delete entries.
<!--more-->
----       


### R-way Tries
> <font size=2>There are several ways to represent tries, corresponding to different trade-offs between memory use and speed of the operations. The basic form is that of a linked set of nodes, where each node contains an array of child pointers, one for each symbol in the alphabet (so for the English alphabet, one would store 26 child pointers and for the alphabet of bytes, 256 pointers). - [wikipedia](https://en.wikipedia.org/wiki/Trie#Implementation_strategies)</font>

### Ternary Search Tries
> <font size=2>In computer science, Each node of a ternary search tree stores a single character, an object (or a pointer to an object depending on implementation), and pointers to its three children conventionally named equal kid, lo kid and hi kid, which can also be referred respectively as middle (child), lower (child) and higher (child). - [wikipedia](https://en.wikipedia.org/wiki/Ternary_search_tree)</font>


|<font size=1>  implementation </font> |<font size=1>  search hit  </font>|<font size=1>  search miss  </font>|<font size=1>  insert  </font>|<font size=1>  space  </font>|
| :| |
|<font size=1>  red-black BST </font> |<font size=1>  L+clg^2(N)  </font>|<font size=1>  clg^2(N)  </font>|<font size=1>  clg^2(N)  </font>|<font size=1>  4N  </font>|
|<font size=1>  hashing(linear probing) </font> |<font size=1>  L  </font>|<font size=1>  L  </font>|<font size=1>  L  </font>|<font size=1>  4N to 16N  </font>|
|<font size=1>  R-way trie </font> |<font size=1>  L  </font>|<font size=1>  logN  </font>|<font size=1>  L  </font>|<font size=1>  (R+1)N  </font>|
|<font size=1>  TST </font> |<font size=1>  L+lnN  </font>|<font size=1>  lnN  </font>|<font size=1>  L+lnN  </font>|<font size=1>  4N  </font>|


<font size=1>**different implementation about symbol-table**</font>


---
### Project
For this project, we use a trie tree to store the dictionary, and use dfs algorithm to get every kind of sequence of characters from Boggle board and check their validity by trie tree.

The goal of this project is to implement 1 class:
```java
public class BoggleSolver
{
    // Initializes the data structure using the given array of strings as the dictionary.
    // (You can assume each word in the dictionary contains only the uppercase letters A through Z.)
    public BoggleSolver(String[] dictionary)

    // Returns the set of all valid words in the given Boggle board, as an Iterable.
    public Iterable<String> getAllValidWords(BoggleBoard board)

    // Returns the score of the given word if it is in the dictionary, zero otherwise.
    // (You can assume the word contains only the uppercase letters A through Z.)
    public int scoreOf(String word)
}
```


> **Note:**
>  1. For the Trie tree, we store a corresponding string as a value for a ending character, so that we can get the string easily without tracking the characters from the start.
>  2. A built in TrieST data structure is already provide for us, but we can only check for a word start from root, which is inefficient for our problem. We should emulate the TrieST(get and put function) by implement by ourselves, so that at every point in the board, we check if it has a corresponding value.
>  3. Every time we get a 'Q' from board, we should use get function to match both 'Q' and 'U' in the trie tree.

- [project detail discription](http://coursera.cs.princeton.edu/algs4/assignments/boggle.html)

- [github code link](https://github.com/xiaofeixiawang/algorithms/blob/master/princeton_hw9/src)

---
