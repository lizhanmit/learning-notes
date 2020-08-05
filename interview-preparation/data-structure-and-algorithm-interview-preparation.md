# Data Structures & Algorithms Interview Preparation

## LeetCode Explore

### Data Structures

#### Array 

列表中没有索引，这是数组与列表最大的不同点。

数组中的元素在内存中是连续存储的，且每个元素占用相同大小的内存。

#### String 

我们可以用 “==” 来比较两个字符串吗？

这取决于下面这个问题的答案：我们使用的语言是否支持运算符重载？

- 如果答案是 yes （例如 C++、Python）。我们可以使用 `==` 来比较两个字符串；
- 如果答案是 no （例如 Java），我们可能无法使用 `==` 来比较两个字符串。当我们使用 `==` 时，它实际上会比较这两个对象是否是同一个对象。

对于 Java来说，由于字符串是不可变的，

- 如果你确实希望你的字符串是可变的，则可以使用 `toCharArray` 将其转换为字符数组。
- 如果你经常必须连接字符串，最好使用一些其他的数据结构，如 `StringBuilder`。

#### Hash Table

Two types:

- HashSet是集合数据结构的实现之一，用于存储非重复值。
- HashMap是映射数据结构的实现之一，用于存储(key, value)键值对。

哈希函数是哈希表中最重要的组件。

理想情况下，完美的哈希函数将是键和桶之间的一对一映射。然而，在大多数情况下，哈希函数并不完美，它需要在桶的数量和桶的容量之间进行权衡。

如果在同一个桶中有太多的值，这些值将被保留在一个高度平衡的二叉树搜索树中。

---

## Experience

[我是如何学习数据结构与算法的？](http://www.sohu.com/a/272031279_478315)

链表和树(二叉堆)，但这是最基本的，刷题之前要掌握的，对于数据结构，我列举下一些比较重要的：

1、链表（如单向链表、双向链表）。

2、树（如二叉树、平衡树、红黑树）。(二叉树和二叉搜索树是最常用的树)

3、图（如最短路径的几种算法）。

4、队列、栈、矩阵。

**对于这些，自己一定要动手实现一遍。**

---

## Q & A

### Linked List

Q: Detect cycle in a linked list.

A: Take two pointers - a fast pointer which moves forward two steps once and a slow pointer which moves forward one step once. Initially they are at the beginning node. If there is cycle in the linked list, these two pointers must meet somewhere.

### Hash Table

Q: 给定一个整数数组，查找数组是否包含任何重复项。

A: Use HashSet. 

1. Traverse the array.
2. Check if the element already exists in the HashSet. 
3. If yes, return true. If no, insert the element into the HashSet. 
4. If the traversal ends, return false.

Q: 给定一个整数数组，返回两个数字的索引，使它们相加得到特定目标。(Two sum)

A: Use HashMap.

1. Traverse the array.
2. Use target value to minus the element value, and get complement value. 
3. Check if the complement value already exists as a key in the HashMap. 
4. If yes, add these two indexes in an array list. 
5. Put (element value, its index) in the HashMap.
6. Return the array list when the traversal ends.

Q: 给定一个字符串，找到它的第一个不重复的字符，并返回它的索引。如果不存在，则返回 -1。

A: Use HashMap. Traverse the string twice.

1. Traverse the string. Put (character, count of appearance) into the HashMap. 
2. Traverse the string the second time. Use each character as the key and get value from the HashMap. 
3. If the value is 1, return the index of this character.
4. If the traversal ends, return -1.

