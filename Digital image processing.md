title: Digital image processing
categories: 
    - Project
    - Berkeley Cs61b
tags: [List, LinkedList]
date: 2016-03-14
---
<img src="https://farm2.staticflickr.com/1648/25818617691_54a9f37859_m.jpg" width="770" height="381">
### Introduction
> <font size=2>Image processing is a method to perform some operations on an image, in order to get an enhanced image or to extract some useful information from it. It is a type of signal processing in which input is an image and output may be image or characteristics/features associated with that image. - [digital image processing](https://sisu.ut.ee/imageprocessing/book/1)</font>

Digital image processing can be widely used in many field. Some of the major fields include image sharpening and restoration, medical field and remote sensing.
<!--more-->
----       


### Image blurring and edge detection
For a normal image, it looks more sharp or more detailed if we are able to perceive all the objects ant their shapes correctly in it. In blurring, we simple reduce the edge content and makes the transition from one color to the other very smooth. In image terms this means each pixel in the source image gets spread over and mixed into surrounding pixels. Another way to look at this is that each pixel in the destination image is made up out of a mixture of surrounding pixels from the source image.
Edge detection is the name for a set of mathematical methods which aim at identifying points in a digital image at which the image brightness changes sharply or, more formally, has discontinuities. The points at which image brightness changes sharply are typically organized into a set of curved line segments termed edges. - [wikipedia](https://en.wikipedia.org/wiki/Edge_detection)


### run-length encoding
> <font size=2>Run-length encoding (RLE) is a very simple form of lossless data compression in which runs of data (that is, sequences in which the same data value occurs in many consecutive data elements) are stored as a single data value and count, rather than as the original run. This is most useful on data that contains many such runs. Consider, for example, simple graphic images such as icons, line drawings, and animations. It is not useful with files that don't have many runs as it could greatly increase the file size. - [wikipedia](https://en.wikipedia.org/wiki/Run-length_encoding)</font>


### Linked List
Linked list is a kind of data structure consisting of a group of nodes which together represent a sequence. It's a linear collection of data elements, called nodes pointing to the next node by means of pointer. Under the simplest form, each node is composed of data and a reference to the next node in the sequence.


|<font size=1>  Operation </font> |<font size=1>  Time Complexity  </font>|
| :| |
| <font size=1> insert </font>|<font size=1>  1 </font>|
| <font size=1> delete </font>|<font size=1>  n </font>|
| <font size=1> search </font>|<font size=1>  n </font>|
| <font size=1> traverse </font>|<font size=1>  n </font>|


<font size=1>**different operations on linked list**</font>


---
### Project
For this project, we will read and write files in the TIFF image format and implement two image processing operations: blurring and edge detection. Also we should realized that the files can be compressed if there are many adjacent pixels of the same color, the compressed form is called a run-length encoding. the run-length encoding could also be converted to image.

The goal of this project is to implement 2 class:
```java
public class PixImage {
	/**
	 * PixImage() constructs an empty PixImage with a specified width and
	 * height. Every pixel has red, green, and blue intensities of zero (solid
	 * black).
	 *
	 * @param width
	 *            the width of the image.
	 * @param height
	 *            the height of the image.
	 */
	public PixImage(int width, int height)
	/**
	 * getWidth() returns the width of the image.
	 *
	 * @return the width of the image.
	 */
	public int getWidth()
	/**
	 * getHeight() returns the height of the image.
	 *
	 * @return the height of the image.
	 */
	public int getHeight()
	/**
	 * getRed() returns the red intensity of the pixel at coordinate (x, y).
	 *
	 * @param x
	 *            the x-coordinate of the pixel.
	 * @param y
	 *            the y-coordinate of the pixel.
	 * @return the red intensity of the pixel at coordinate (x, y).
	 */
	public short getRed(int x, int y)
	/**
	 * getGreen() returns the green intensity of the pixel at coordinate (x, y).
	 *
	 * @param x
	 *            the x-coordinate of the pixel.
	 * @param y
	 *            the y-coordinate of the pixel.
	 * @return the green intensity of the pixel at coordinate (x, y).
	 */
	public short getGreen(int x, int y)
	/**
	 * getBlue() returns the blue intensity of the pixel at coordinate (x, y).
	 *
	 * @param x
	 *            the x-coordinate of the pixel.
	 * @param y
	 *            the y-coordinate of the pixel.
	 * @return the blue intensity of the pixel at coordinate (x, y).
	 */
	public short getBlue(int x, int y)
	/**
	 * setPixel() sets the pixel at coordinate (x, y) to specified red, green,
	 * and blue intensities.
	 *
	 * If any of the three color intensities is NOT in the range 0...255, then
	 * this method does NOT change any of the pixel intensities.
	 *
	 * @param x
	 *            the x-coordinate of the pixel.
	 * @param y
	 *            the y-coordinate of the pixel.
	 * @param red
	 *            the new red intensity for the pixel at coordinate (x, y).
	 * @param green
	 *            the new green intensity for the pixel at coordinate (x, y).
	 * @param blue
	 *            the new blue intensity for the pixel at coordinate (x, y).
	 */
	public void setPixel(int x, int y, short red, short green, short blue)
	/**
	 * toString() returns a String representation of this PixImage.
	 *
	 * This method isn't required, but it should be very useful to you when
	 * you're debugging your code. It's up to you how you represent a PixImage
	 * as a String.
	 *
	 * @return a String representation of this PixImage.
	 */
	public String toString()
	/**
	 * boxBlur() returns a blurred version of "this" PixImage.
	 *
	 * If numIterations == 1, each pixel in the output PixImage is assigned a
	 * value equal to the average of its neighboring pixels in "this" PixImage,
	 * INCLUDING the pixel itself.
	 *
	 * A pixel not on the image boundary has nine neighbors--the pixel itself
	 * and the eight pixels surrounding it. A pixel on the boundary has six
	 * neighbors if it is not a corner pixel; only four neighbors if it is a
	 * corner pixel. The average of the neighbors is the sum of all the neighbor
	 * pixel values (including the pixel itself) divided by the number of
	 * neighbors, with non-integer quotients rounded toward zero (as Java does
	 * naturally when you divide two integers).
	 *
	 * Each color (red, green, blue) is blurred separately. The red input should
	 * have NO effect on the green or blue outputs, etc.
	 *
	 * The parameter numIterations specifies a number of repeated iterations of
	 * box blurring to perform. If numIterations is zero or negative, "this"
	 * PixImage is returned (not a copy). If numIterations is positive, the
	 * return value is a newly constructed PixImage.
	 *
	 * IMPORTANT: DO NOT CHANGE "this" PixImage!!! All blurring/changes should
	 * appear in the new, output PixImage only.
	 *
	 * @param numIterations
	 *            the number of iterations of box blurring.
	 * @return a blurred version of "this" PixImage.
	 */
	public PixImage boxBlur(int numIterations)
	/**
	 * sobelEdges() applies the Sobel operator, identifying edges in "this"
	 * image. The Sobel operator computes a magnitude that represents how strong
	 * the edge is. We compute separate gradients for the red, blue, and green
	 * components at each pixel, then sum the squares of the three gradients at
	 * each pixel. We convert the squared magnitude at each pixel into a
	 * grayscale pixel intensity in the range 0...255 with the logarithmic
	 * mapping encoded in mag2gray(). The output is a grayscale PixImage whose
	 * pixel intensities reflect the strength of the edges.
	 *
	 * See http://en.wikipedia.org/wiki/Sobel_operator#Formulation for details.
	 *
	 * @return a grayscale PixImage representing the edges of the input image.
	 *         Whiter pixels represent stronger edges.
	 */
	public PixImage sobelEdges()
}

public class RunLengthEncoding implements Iterable {
	/**
	 * RunLengthEncoding() (with two parameters) constructs a run-length
	 * encoding of a black PixImage of the specified width and height, in which
	 * every pixel has red, green, and blue intensities of zero.
	 *
	 * @param width
	 *            the width of the image.
	 * @param height
	 *            the height of the image.
	 */

	public RunLengthEncoding(int width, int height)
	/**
	 * RunLengthEncoding() (with six parameters) constructs a run-length
	 * encoding of a PixImage of the specified width and height. The runs of the
	 * run-length encoding are taken from four input arrays of equal length. Run
	 * i has length runLengths[i] and RGB intensities red[i], green[i], and
	 * blue[i].
	 *
	 * @param width
	 *            the width of the image.
	 * @param height
	 *            the height of the image.
	 * @param red
	 *            is an array that specifies the red intensity of each run.
	 * @param green
	 *            is an array that specifies the green intensity of each run.
	 * @param blue
	 *            is an array that specifies the blue intensity of each run.
	 * @param runLengths
	 *            is an array that specifies the length of each run.
	 *
	 *            NOTE: All four input arrays should have the same length (not
	 *            zero). All pixel intensities in the first three arrays should
	 *            be in the range 0...255. The sum of all the elements of the
	 *            runLengths array should be width * height. (Feel free to quit
	 *            with an error message if any of these conditions are not
	 *            met--though we won't be testing that.)
	 */

	public RunLengthEncoding(int width, int height, int[] red, int[] green, int[] blue, int[] runLengths)
	/**
	 * getWidth() returns the width of the image that this run-length encoding
	 * represents.
	 *
	 * @return the width of the image that this run-length encoding represents.
	 */
	public int getWidth()
	/**
	 * getHeight() returns the height of the image that this run-length encoding
	 * represents.
	 *
	 * @return the height of the image that this run-length encoding represents.
	 */
	public int getHeight()
	/**
	 * iterator() returns a newly created RunIterator that can iterate through
	 * the runs of this RunLengthEncoding.
	 *
	 * @return a newly created RunIterator object set to the first run of this
	 *         RunLengthEncoding.
	 */
	public RunIterator iterator()
	/**
	 * toPixImage() converts a run-length encoding of an image into a PixImage
	 * object.
	 *
	 * @return the PixImage that this RunLengthEncoding encodes.
	 */
	public PixImage toPixImage()
	/**
	 * toString() returns a String representation of this RunLengthEncoding.
	 *
	 * This method isn't required, but it should be very useful to you when
	 * you're debugging your code. It's up to you how you represent a
	 * RunLengthEncoding as a String.
	 *
	 * @return a String representation of this RunLengthEncoding.
	 */
	public String toString()
	/**
	 * RunLengthEncoding() (with one parameter) is a constructor that creates a
	 * run-length encoding of a specified PixImage.
	 * 
	 * Note that you must encode the image in row-major format, i.e., the second
	 * pixel should be (1, 0) and not (0, 1).
	 *
	 * @param image
	 *            is the PixImage to run-length encode.
	 */
	public RunLengthEncoding(PixImage image)
	/**
	 * check() walks through the run-length encoding and prints an error message
	 * if two consecutive runs have the same RGB intensities, or if the sum of
	 * all run lengths does not equal the number of pixels in the image.
	 */
	public void check()
	/**
	 * setPixel() modifies this run-length encoding so that the specified color
	 * is stored at the given (x, y) coordinates. The old pixel value at that
	 * coordinate should be overwritten and all others should remain the same.
	 * The updated run-length encoding should be compressed as much as possible;
	 * there should not be two consecutive runs with exactly the same RGB color.
	 *
	 * @param x
	 *            the x-coordinate of the pixel to modify.
	 * @param y
	 *            the y-coordinate of the pixel to modify.
	 * @param red
	 *            the new red intensity to store at coordinate (x, y).
	 * @param green
	 *            the new green intensity to store at coordinate (x, y).
	 * @param blue
	 *            the new blue intensity to store at coordinate (x, y).
	 */
	public void setPixel(int x, int y, short red, short green, short blue)
}
```

The first class is to use 3d array to represent a 3 channel image and implementing image blurring and edge detection.
The second class is to use a linked list to represent run-length encoding and realizing the conversion between it with normal image.


> **Note:**
>  1. when dealing with image blurring, we use a for loop for n times to blur the image n times, every time we should create a new array and put the new value for each element. Be careful about the edge elements.
>  2. when dealing with edge detection, we at first get the x and y gradient and mix to get the energy for each element. In order not to overflow, we should use a helper method mag2gray to map the energy to a reasonable range.
>  3. for the conversion between run-length encoding and normal image, we should think carefully all the cases when changing a pixel's value. The possible situation is that 1 node in run-length encoding could divide into 2 nodes, 3 nodes or just 1 node. Also 2 nodes or 3 nodes in run-length encoding could be merged into 1 node.

- [project detail discription](https://www.cs.berkeley.edu/~jrs/61b/hw/pj1/readme)

- [github code link]https://github.com/xiaofeixiawang/berkeley_cs61b/tree/master/berkeley_project1/src)

---
