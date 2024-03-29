# 数据结构与算法
(https://www.youtube.com/playlist?list=PLuRm_QtoJ4J-Ld1BPZ7F2VLfNgfgjpj17)

---

## 1.数据结构和算法绪论

程序设计 = 数据结构 + 算法

数据结构就是数据元素相互之间存在的一种或多种特定关系的集合。

数据结构：

- 逻辑结构
- 物理结构

四大逻辑结构：

- 集合结构：数据元素同属于一个集合。
- 线性结构：数据元素之间是一对一的关系。
- 树形结构：数据元素之间存在一对多的层次关系。
- 图形结构：数据元素之间是多对多的关系。

物理（存储）结构：

- 顺序存储： e.g.数组。对于时常要变化的结构，顺序存储不科学。
- 链式存储：更灵活。分两个域 - 数据域，指针域。

---

## 2.谈谈算法

算法初体验： e.g.求1+2+3+···+100。


算法的五个基本特性：

- 输入：算法有零个或多个输入。
- 输出：算法至少有一个或多个输出。打印或返回值。
- 有穷性
- 确定性
- 可行性

算法设计的要求：

- 正确性
	- 四个层次：
		- 算法程序没有语法错误。
		- 算法程序对于合法的输入能产生满足要求的输出。
		- 算法程序对于非法的输入能产生满足规格的说明。
		- 算法程序对于故意刁难的测试输入都有满足要求的输出结果。
- 可读性
- 健壮性
- 时间效率高、存储量低

---

## 3.时间复杂度和空间复杂度

算法效率的度量：（抛开硬件和软件因素）

- 算法的好坏
- 问题的输入规模

函数的渐进增长：

- 常数项、与最高次项相乘的常数、其他次要项都不重要，可忽略。
- 只需关注最高项的阶数。

### 算法的时间复杂度

**判断一个算法的好坏，只通过少量数据不能做出准确判断，容易以偏概全。**

![常见的时间复杂度.png](img/常见的时间复杂度.png)

![常见的时间复杂度排序.png](img/常见的时间复杂度排序.png)

最坏运行时间是一种保证。通常提到的运行时间都是指最坏情况的运行时间。

### 算法的空间复杂度

写代码时的一个技巧，可以用空间开销换取时间开销。但具体情况具体分析。

提及“复杂度”，通常指时间复杂度。

对时间复杂度的追求更符合算法的潮流。

---

## 4.线性表(List)

线性表可为空，即空表。

线性表中从1开始，不从0。

**抽象数据类型**（Abstract Data Type，ADT）：

- 一个数学模型及定义在该模型上的一组操作。e.g.类。
- 把数据类型和相关操作捆绑在一起。e.g.面向对象高级语言把属性和方法捆绑在一起。

**内存上的数据只能被覆盖。**

**线性表的两种物理存储结构:**

- 顺序存储结构：用一段地址连续的存储单元依次存储线性表的数据元素。
	- 特点：随机存储结构
	- ![线性表顺序存储结构的特点.png](img/线性表顺序存储结构的特点.png)
	- ![线性表顺序存储结构的优缺点.png](img/线性表顺序存储结构的优缺点.png)
	- **若线性表需要频繁查找，很少进行插入和删除操作时，或事先知道表长，宜采用顺序存储结构。** e.g.用户注册账户，只有注册时需要插入，绝大多数情况都是读取。

- 链式存储结构：用一组任意的存储单元存储线性表的数据元素，这组存储单元可以在内存中未被占用的任意位置，数据分散在内存的各个角落。
	- 既要存储数据元素信息 + 还要存储后继元素的存储地址（指针或链）
	- 结点(Node) = 数据域 + 指针域 

### 链式存储结构

- 单链表：每个Node中只包含一个指针域。
	-  ![单链表.png](img/单链表.png)
	- 头指针是链表的必要元素，不能为空。否则链表不存在。
	- 单链表读取的核心思想：工作指针后移。
	- **对于插入或删除数据频繁的操作，或表中元素个数不知道或变化较大时，宜用单链表** e.g.游戏中玩家的装备列表，随着游戏进度，可能会随时增加或删除。
	- 单链表的整表建立方法：
		- 头插法
		- 尾插法
	- **学线性表实际上就是学单链表。因为它比较关键。**

- 静态链表：用数组描述的链表。游标实现法。
	- 目的：给没有指针的编程语言设计的一种实现单链表功能的方法。

![快慢指针原理.png](img/快慢指针原理.png)

- 循环链表
	- 不要头指针，新增尾指针rear。
	- if (rear == rear -> next)，则为空链表。
	- 时间复杂度为O(1)。
	- 有环：链表的尾结点指向链表中的某个结点。
	- 约瑟夫问题（16，31）、魔术师发牌问题、拉丁方阵问题

- 双向链表
	- ![双向链表结点结构](img/双向链表结点结构.png)
	- 比单链表更复杂。
	- 用空间换取时间，提高算法的时间性能。

---

## 5.栈(Stack)

栈是一种后进先出(last in first out, LIFO)的线性表。

清空一个栈：将栈中的元素全部作废，但栈本身的物理空间不改变，实际上里面的数据还存在。只需将 s->top = s->base。

销毁一个栈：释放掉该栈所占据的物理内存空间。

- 栈的顺序存储结构（通常使用）
- 栈的链式存储结构（栈链）（只是一个知识点，知道就好）
	- 栈顶指针和单链表的头指针合二为一。栈顶相当于表头。

逆波兰表达式（RPN）：不需要括号的后缀表达式。操作符在前，运算符在后。应用栈的操作。

中缀表达式转换为后缀表达式：

![中缀表达式转换为后缀表达式规则](img/中缀表达式转换为后缀表达式规则.png)

--- 

## 6.队列(Queue)

队列是一种先进先出(first in first out, FIFO)的线性表。

- 队列的链式存储结构（通常使用）（链队列）
	- 队列的头指针(front)指向链表的头结点，尾指针(rear)指向终端结点。
	- 空队列时，front和rear都指向头结点。
	- 链队列建立在内存的动态区，当一个队列没用了，应该将它及时销毁，以免过多地占用内存空间。

- 队列的顺序存储结构
	- 循环队列

--- 

## 7.递归(Recursion)

递归就是自己调自己。

**递归是效率低下的算法，能不用就不用，可以用迭代来代替**(for循环，while循环···)。

- 迭代使用循环结构。
- 递归使用选择结构。

优点：递归使程序的结构更清晰、简介、易懂，从而减少读代码的时间。

缺点：大量的递归会建立函数的副本，消耗大量的时间和内存。

- 斐波那契函数
- 求n!
- 汉诺塔
- 八皇后问题 - 回溯算法

--- 

## 8.字符串(String)

字符串比大小比的是ASCII码的大小。 小写字母 > 大写字母

- 字符串的顺序存储结构：用数组存。（通常使用）
- 字符串的链式存储结构

### 串的模式匹配
子串（模式串）在主串（目标串）中的定位操作。

- BF(Brute Force)算法
	- 效率低下。（S是主串，T是子串。）
	- ![BF算法](img/BF算法.png)

- KMP算法
	- 大大地避免重复遍历的情况。
	- 核心：避免不必要的回溯。
	- **问题由模式串决定，不是由目标串决定。**
	- next数组：当模式匹配串T失配时，next数组对应的元素指导应该用T串的哪个元素进行下一轮的匹配。

--- 

## 9.树(Tree)

度(drgree)：结点的分支数目。

整个树的度 = 结点的度（结点的分支数目）的最大值

根是第一层。以此类推。

树的深度(depth)：树的层数。

森林(forest)：m棵互不相交的树的集合。

### 树的存储结构

**一个存储结构设计得是否合理，取决于基于该存储结构的运算是否合适、方便，时间复杂度好不好等。**

- 双亲表示法
- 孩子表示法
- 双亲孩子表示法
- 孩子兄弟表示法

### 二叉树 (Binary Tree)

每个结点最多有两棵子树。左子树和右子树有顺序，不能颠倒。即使只有一棵子树，也要区分是左还是右。

![二叉树定义](img/二叉树定义.png)

![完全二叉树特点](img/完全二叉树特点.png)

二叉树的存储按照国际惯例采用链式存储结构，因为顺序存储方式的适用性不强。

二叉链表：一个数据域和两个指针域。(lchild | data | rchild)

![二叉链表](img/二叉链表.png)

二叉树的遍历方法

- 前序遍历
	- ![二叉树的前序遍历](img/二叉树的前序遍历.png)
- 中序遍历
	- ![二叉树的中序遍历](img/二叉树的中序遍历.png)
- 后序遍历
	- ![二叉树的后序遍历](img/二叉树的后序遍历.png)
- 层序遍历
	- ![二叉树的层序遍历](img/二叉树的层序遍历.png)


线索二叉树

采用中序遍历将浪费的空间用于存储前驱和后继。
![线索二叉树](img/线索二叉树.png)

### 树和森林的遍历

![树的遍历方式](img/树的遍历方式.png)
![树和森林的遍历](img/树和森林的遍历.png)

### 霍夫曼树 (Huffman)

霍夫曼树定义与原理
![霍夫曼树定义与原理](img/霍夫曼树定义与原理.png)

WPL的值越小，构造出来的二叉树性能越优。

霍夫曼树：WPL的值最小，最优二叉树。

### 霍夫曼编码

霍夫曼编码，首个实用的压缩编码方案，无损压缩。基于霍夫曼树的原理构成。可以有效地压缩数据节省20%-90%空间。

定长编码：比如ASCII码。

变长编码：单个编码的长度不一致，可根据整体出现频率来调节。

前缀码：没有任何码字是其他码字的前缀。

### 字典树

字典树，也叫“前缀树”，是一种树形结构，在解决字符串相关问题中非常高效。其提供非常快速的检索功能，常用于搜索字典中的单词，为搜索引擎提供自动搜索建议，甚至能用于IP路由选择。

下面展示了"top", "thus" 和 "their" 这三个词是如何存储在字典树中的：

![dictionary-tree.jpg](img/dictionary-tree.jpg)