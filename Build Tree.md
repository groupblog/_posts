title: Build Tree from other Data Structures
categories:
    - LeetCode
tags: [List, Binary Search Tree, Tree]
date: 2016-03-21
---

![](https://farm2.staticflickr.com/1559/25336741793_0edc085365.jpg)

### Introduction
Tree data structure is applied generally during programming such as sorting and searching. Tree data structure even can be used in dealing with string such as prefix tree. However, we sometimes get the data is not stored as a tree, so it is normally to for us to transfer other data structures into a tree so as to handle the problem more effectively. In this article, we would talk about how transfer the sorted array and sorted list to the balanced binary search tree. Besides, we can transfer two arrays, gotten by traversing inorder and preorder or postorder. We also can build a tree from a string, where the values are split by ‘,’ and the null node is marked as ‘#’. However, we can build a tree form various data structure, which is an essential methods in algorithms.

### Build Tree from Sorted Array and Sorted List
#### Build Tree form Sorted Array
We know that if we traverse the binary search tree (BST) inorder the output value would be sorted. Base on this character we can choose the middle value as the root and the left side array as its left subtree and the right side array as the right subtree. The left side array is also a sorted array which need to transfer smaller BST, which is the exactly the same problem as the total one. So we can use divide and conquer method. The following is the detail steps in the function.
*	Get the middle of the array make it as root. (By doing this we will ensure that half of the elements of array will be on the left side of the root and half on the right side.)
*	Take the left half of the array, call recursively and add it to root.left.
*	Take the right half of the array, call recursively and add it to root.right.

The following picture is the process of building tree, which is a balanced tree.

![](https://farm2.staticflickr.com/1487/25664888060_3d910760f0.jpg)

#### Build Tree from Sorted List
Converting the sorted list is same as converting the sorted array to a BST. The detailed strops are as following:
Say mid is the mid­dle node in the linked list.
*	Recursively construct left subtree from start to mid-1
*	Make the middle node as root and assign the left subtree to it.
*	Recursively construct right subtree from mid+1 to end.
*	Assign the right subtree to root.

``` java
    private TreeNode sortedListToBST(int start, int end) {
        if(start > end) return null;
        int mid = (start + end) / 2;

        TreeNode left = sortedListToBST(start, mid - 1);
        TreeNode parent = new TreeNode(list.val);
        list = list.next;
        parent.left = left;

        TreeNode right = sortedListToBST(mid + 1, end);
        parent.right = right;

        return parent;
    }
```
### Build Tree form Inorder and Preorder or PostOrder Array
The following pictures are two examples of the inorder and postorder array and the inorder and preorder array. And show the first step we need to do. It also marks the two sub problems.

![](https://farm2.staticflickr.com/1510/25844668382_7917946bc1.jpg)

![](https://farm2.staticflickr.com/1694/25336741633_ab4ac60607.jpg)

The following is the detail steps to build tree from the inorder and postorder array:
*	Read the last element from postorder array which is root node.
*	Search the same element in inorder traversal, when the element is found in inorder traversal, then we can very well say that all the elements present before the node found are Left children/sub-tree and all the elements present after the node found are Right children/sub-tree of node found.
*	Find the index of left subtree inorder and postorder array and the index of the right subtree inorder and postorder array. Building them as sub problem and recursively to deal with the sub problems.

![](https://farm2.staticflickr.com/1571/25939566926_906a2ee6a9.jpg)

One of the most important thing is that we should get the right index of the two sub problems. The following code show the input parameter of two transition with postorder array or preorder array.

``` java
// inorder and preorder array
root.left = buildTree(preorder, preStart + 1, order, orderStart, index - 1);
root.right = buildTree(preorder, preStart + index - orderStart + 1, order, index + 1, orderEnd);

// inorder and postorder array
root.right = buildTree(postorder, postStart - 1, inorder, orderStart, index + 1);
root.left = buildTree(postorder, postStart - (orderStart - index + 1), inorder, index - 1, orderEnd);
```

### Conclusion
In some time we need to compress the Tree into a string and then reconstruct a tree form the string. However, it is very general to come up with building tree from other data structures. You can get the realization of theses algorithms from the following github link with index 105, 106, 108, 109 and 297.
- [github code link](https://github.com/spacime/Online-Judge/tree/master/LeetCode)
