title: Burrows Wheeler
categories: 
    - Project
    - Princeton Algorithms
tags: [Compression, Decompression]
date: 2016-05-14
---
<img src="https://farm8.staticflickr.com/7209/26412654054_a350ac4de8.jpg" width="486" height="362">
### Introduction
> <font size=2>The Burrows–Wheeler transform rearranges a character string into runs of similar characters. The transform is done by sorting all rotations of the text into lexicographic order, by which we mean that the 8 rotations appear in the second column in a different order, in that the 8 rows have been sorted into lexicographical order. We then take as output the last column and the number k = 7 of the row that the non rotated row ends up in. - [wikipedia](https://en.wikipedia.org/wiki/Burrows–Wheeler_transform)</font>

Burrows Wheeler is useful for compression, since it tends to be easy to compress a string that has runs of repeated characters by techniques such as move-to-front transform and run-length encoding. More importantly, the transformation is reversible, without needing to store any additional data. The BWT is thus a "free" method of improving the efficiency of text compression algorithms, costing only some extra computation.
<!--more-->
----       


### Run-Length Encoding
> <font size=2>Run-length encoding (RLE) is a very simple form of lossless data compression in which runs of data (that is, sequences in which the same data value occurs in many consecutive data elements) are stored as a single data value and count, rather than as the original run. This is most useful on data that contains many such runs. Consider, for example, simple graphic images such as icons, line drawings, and animations. It is not useful with files that don't have many runs as it could greatly increase the file size. - [wikipedia](https://en.wikipedia.org/wiki/Run-length_encoding)</font>

### Huffman Compression
> <font size=2>In computer science and information theory, a Huffman code is a particular type of optimal prefix code that is commonly used for lossless data compression. The output from Huffman's algorithm can be viewed as a variable-length code table for encoding a source symbol (such as a character in a file). The algorithm derives this table from the estimated probability or frequency of occurrence (weight) for each possible value of the source symbol. As in other entropy encoding methods, more common symbols are generally represented using fewer bits than less common symbols. - [wikipedia](https://en.wikipedia.org/wiki/Huffman_coding)</font>

### LZW Compression
> <font size=2>LZW compression is the compression of a file into a smaller file. A particular LZW compression algorithm takes each input sequence of bits of a given length (for example, 12 bits) and creates an entry in a table (sometimes called a "dictionary" or "codebook") for that particular bit pattern, consisting of the pattern itself and a shorter code. As input is read, any pattern that has been read before results in the substitution of the shorter code, effectively compressing the total amount of input to something smaller. - [WhatIs](http://whatis.techtarget.com/definition/LZW-compression)</font>


|<font size=1>  year </font> |<font size=1>  scheme  </font>|<font size=1>  bits/char  </font>|
| :| |
|<font size=1>  1967 </font> |<font size=1>  ASCII  </font>|<font size=1>  7.00  </font>|
|<font size=1>  1950 </font> |<font size=1>  Huffman  </font>|<font size=1>  4.70  </font>|
|<font size=1>  1977 </font> |<font size=1>  LZ77  </font>|<font size=1>  3.94  </font>|
|<font size=1>  1984 </font> |<font size=1>  LZMW  </font>|<font size=1>  3.32  </font>|
|<font size=1>  1987 </font> |<font size=1>  LZH  </font>|<font size=1>  3.30  </font>|
|<font size=1>  1987 </font> |<font size=1>  move-to-front  </font>|<font size=1>  3.24  </font>|
|<font size=1>  1995 </font> |<font size=1>  Burrows-Wheeler  </font>|<font size=1>  2.29  </font>|


<font size=1>**lossless data compression benchmarks**</font>


---
### Project
For this project, we at first create a class named Circular suffix array, which describes the abstraction of a sorted array of the N circular suffixs of a string of length N and we can get the index of each sorted suffixes. Then we implement Burrows-Wheeler transform with the help of Circular suffix array, getting the row number first to indicate the start position and transform the file by recording the last column in the sorted suffixes array. Finally we use the Move-to-front encoding to move a character to the front of the sequence every time we read a character from the input message.

The goal of this project is to implement 3 class:
```java
public class MoveToFront {
    // apply move-to-front encoding, reading from standard input and writing to standard output
    public static void encode()

    // apply move-to-front decoding, reading from standard input and writing to standard output
    public static void decode()

    // if args[0] is '-', apply move-to-front encoding
    // if args[0] is '+', apply move-to-front decoding
    public static void main(String[] args)
}
public class CircularSuffixArray {
    public CircularSuffixArray(String s)  // circular suffix array of s
    public int length()                   // length of s
    public int index(int i)               // returns index of ith sorted suffix
    public static void main(String[] args)// unit testing of the methods (optional)
}
public class BurrowsWheeler {
    // apply Burrows-Wheeler encoding, reading from standard input and writing to standard output
    public static void encode()

    // apply Burrows-Wheeler decoding, reading from standard input and writing to standard output
    public static void decode()

    // if args[0] is '-', apply Burrows-Wheeler encoding
    // if args[0] is '+', apply Burrows-Wheeler decoding
    public static void main(String[] args)
}
```


> **Note:**
>  1. For the MoveToFront class, we use a linkedlist to store the sequence. And when encoding the input message, we should use a iterator to find the matched character in the linkedlist.
>  2. For the CircularSuffixArray class, we create a index array to indicate the start position of each sorted suffix in the input string, and implementing the Comparator class to compare two sorted suffixes.
>  3. For the BurrowsWheeler's decoding, we use a hashmap with the key of character and value of queue, storing every index of such character in the input message.

- [project detail discription](http://coursera.cs.princeton.edu/algs4/assignments/burrows.html)

- [github code link](https://github.com/xiaofeixiawang/algorithms/tree/master/princeton_hw10/src)

---
