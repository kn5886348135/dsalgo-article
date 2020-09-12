红黑树（上）：为什么工程中都用红黑树这种二叉树？
===

&emsp;&emsp;树、二叉树、二叉查找树中，二叉查找树是最常用的一种二叉树，它支持快速插入、删除、查找操作，各个操作的时间复杂度跟树的高度成正比，理想情况下，时间复杂度是 O($log{n}$)。

&emsp;&emsp;二叉查找树在频繁的动态更新过程中，可能会出现树的高度远大于 $log_2{n}$ 的情况，从而导致各个操作的效率下降。极端情况下，二叉树会退化为链表，时间复杂度会退化到 O(n)。要解决这个复杂度退化的问题，需要设计一种平衡二叉查找树，也就是红黑树。

&emsp;&emsp;很多书籍里，但凡讲到平衡二叉查找树，就会拿红黑树作为例子。不仅如此，如果你有一定的开发经验，你会发现，在工程中，很多用到平衡二叉查找树的地方都会用红黑树。你有没有想过，为什么工程中都喜欢用红黑树，而不是其他平衡二叉查找树呢？



##### 什么是“平衡二叉查找树”？

&emsp;&emsp;平衡二叉树的严格定义是这样的：二叉树中任意一个节点的左右子树的高度相差不能大于 1。完全二叉树、满二叉树其实都是平衡二叉树，但是非完全二叉树也有可能是平衡二叉树。

<img src="assert/BalancedBinarySearchTree.jpg" alt="平衡二叉查找树" style="zoom:50%;" />

&emsp;&emsp;平衡二叉查找树不仅满足上面平衡二叉树的定义，还满足二叉查找树的特点。最先被发明的平衡二叉查找树是[AVL 树](https://zh.wikipedia.org/wiki/AVL%E6%A0%91)，它严格符合平衡二叉查找树的定义，即任何节点的左右子树高度相差不超过 1，是一种高度平衡的二叉查找树。

&emsp;&emsp;但是很多平衡二叉查找树其实并没有严格符合上面的定义（树中任意一个节点的左右子树的高度相差不能大于 1），比如红黑树，它从根节点到各个叶子节点的最长路径，有可能会比最短路径大一倍。

&emsp;&emsp;对于平衡二叉查找树这个概念，要从这个数据结构的由来，去理解“平衡”的意思。

&emsp;&emsp;发明平衡二叉查找树这类数据结构的初衷是，解决普通二叉查找树在频繁的插入、删除等动态更新的情况下，出现时间复杂度退化的问题。

&emsp;&emsp;**平衡二叉查找树中“平衡”的意思，其实就是让整棵树左右看起来比较“对称”、比较“平衡”，不要出现左子树很高、右子树很矮的情况。这样就能让整棵树的高度相对来说低一些，相应的插入、删除、查找等操作的效率高一些**。

&emsp;&emsp;如果现在设计一个新的平衡二叉查找树，只要树的高度不比 $log_2{n}$ 大很多（比如树的高度仍然是对数量级的），尽管它不符合严格的平衡二叉查找树的定义，但仍然可以说，这是一个合格的平衡二叉查找树。



##### 如何定义一棵“红黑树”？

&emsp;&emsp;平衡二叉查找树其实有很多，比如，Splay Tree（伸展树）、Treap（树堆）等，但是提到平衡二叉查找树，听到的基本都是红黑树。它的出镜率甚至要高于“平衡二叉查找树”这几个字，有时候，甚至默认平衡二叉查找树就是红黑树，那现在就来看看这个“明星树”。

&emsp;&emsp;红黑树的英文是“Red-Black Tree”，简称 R-B Tree。它是一种不严格的平衡二叉查找树，它的定义是不严格符合平衡二叉查找树的定义的。

&emsp;&emsp;红黑树中的节点，一类被标记为黑色，一类被标记为红色。除此之外，一棵红黑树还需要满足这样几个要求：

* 根节点是黑色的；

* 每个叶子节点都是黑色的空节点（NIL），也就是说，叶子节点不存储数据；

* 任何相邻的节点都不能同时为红色，也就是说，红色节点是被黑色节点隔开的；

* 每个节点，从该节点到达其可达叶子节点的所有路径，都包含相同数目的黑色节点；

&emsp;&emsp;叶子节点都是黑色的空节点，它主要是为了简化红黑树的代码实现而设置的。

<img src="assert/R-BTree.jpg" alt="红黑树" style="zoom:50%;" />



##### 为什么说红黑树是“近似平衡”的？

&emsp;&emsp;平衡二叉查找树的初衷，是为了解决二叉查找树因为动态更新导致的性能退化问题。所以，“平衡”的意思可以等价为性能不退化。“近似平衡”就等价为性能不会退化的太严重。

&emsp;&emsp;二叉查找树很多操作的性能都跟树的高度成正比。一棵极其平衡的二叉树（满二叉树或完全二叉树）的高度大约是 $log_2{n}$，所以如果要证明红黑树是近似平衡的，只需要分析，红黑树的高度是否比较稳定地趋近 $log_2{n}$ 就好了。


* 如果将红色节点从红黑树中去掉，那单纯包含黑色节点的红黑树的高度是多少呢？
    红色节点删除之后，有些节点就没有父节点了，它们会直接拿这些节点的祖父节点（父节点的父节点）作为父节点。所以，之前的二叉树就变成了四叉树。

<img src="assert/HeightOfR-BTree.jpg" alt="红黑树的高度" style="zoom:50%;" />

&emsp;&emsp;红黑树的定义里有这么一条：从任意节点到可达的叶子节点的每个路径包含相同数目的黑色节点。从四叉树中取出某些节点，放到叶节点位置，四叉树就变成了完全二叉树。所以，仅包含黑色节点的四叉树的高度，比包含相同节点个数的完全二叉树的高度还要小。

&emsp;&emsp;完全二叉树的高度近似 $log_2{n}$，这里的四叉“黑树”的高度要低于完全二叉树，所以去掉红色节点的“黑树”的高度也不会超过 $log_2{n}$。

&emsp;&emsp;**现在知道只包含黑色节点的“黑树”的高度，那现在把红色节点加回去，高度会变成多少呢**？

&emsp;&emsp;在红黑树中，红色节点不能相邻，有一个红色节点就要至少有一个黑色节点，将它跟其他红色节点隔开。红黑树中包含最多黑色节点的路径不会超过 $log_2{n}$，所以加入红色节点之后，最长路径不会超过 2$log_2{n}$，也就是说，红黑树的高度近似 2$log_2{n}$。

&emsp;&emsp;红黑树的高度只比高度平衡的 AVL 树的高度（$log_2{n}$）仅仅大了一倍，在性能上，下降得并不多。这样推导出来的结果不够精确，实际上红黑树的性能更好。



##### 解答开篇

&emsp;&emsp;为什么在工程中大家都喜欢用红黑树这种平衡二叉查找树？

&emsp;&emsp;Treap、Splay Tree，绝大部分情况下，它们操作的效率都很高，但是也无法避免极端情况下时间复杂度的退化。尽管这种情况出现的概率不大，但是对于单次操作时间非常敏感的场景来说，它们并不适用。

&emsp;&emsp;AVL 树是一种高度平衡的二叉树，所以查找的效率非常高，但是，AVL 树为了维持这种高度的平衡，就要付出更多的代价。每次插入、删除都要做调整，就比较复杂、耗时。所以，对于有频繁的插入、删除操作的数据集合，使用 AVL 树的代价就有点高了。

&emsp;&emsp;红黑树只是做到了近似平衡，并不是严格的平衡，所以在维护平衡的成本上，要比 AVL 树要低。

&emsp;&emsp;红黑树的插入、删除、查找各种操作性能都比较稳定。对于工程应用来说，要面对各种异常情况，为了支撑这种工业级的应用，更倾向于这种性能稳定的平衡二叉查找树。



##### 内容小结

&emsp;&emsp;红黑树算是最难掌握的一种数据结构，红黑树最难的地方是它的实现。

&emsp;&emsp;**学习数据结构和算法，要学习它的由来、特性、适用的场景以及它能解决的问题。对于红黑树，也不例外。**

&emsp;&emsp;红黑树是一种平衡二叉查找树。它是为了解决普通二叉查找树在数据更新的过程中，复杂度退化的问题而产生的。红黑树的高度近似 $log_2{n}$，所以它是近似平衡，插入、删除、查找操作的时间复杂度都是 O($log{n}$)。

&emsp;&emsp;红黑树是一种性能非常稳定的二叉查找树，所以，在工程中，但凡是用到动态插入、删除、查找数据的场景，都可以用到它。不过，它实现起来比较复杂，如果自己写代码实现，难度会有些高，这个时候，其实更倾向用跳表来替代它。



##### 课后思考

&emsp;&emsp;动态数据结构支持动态地数据插入、删除、查找操作，除了红黑树，前面还学习过哪些呢？能对比一下各自的优势、劣势，以及应用场景吗？




动态数据结构的动态是什么意思？
动态是指在运行时该数据结构所占的内存会扩大或缩小。
数组是一种静态的数据结构，在创建时内存大小已经确定，不管有没有插入数据。而链表是动态的数据结构，插入数据 alloc 内存，删除数据 release 内存。
栈、队列、散列表、跳表、树都是抽象的数据结构，它们在内存中存在的形式都要依赖于数组或者链表。
如果用链表实现，很明显它们是动态的数据结构；如果用数组实现，那么在扩容的时候会创建更大内存的数组，原数组被废弃，从抽象角度看，它们仍旧是动态的。

作者回复: 我看smallfly大牛好像对动态数据结构有些误解，可能其他同学也会有。所以，我解释一下：动态数据结构是支持动态的更新操作，里面存储的数据是时刻在变化的，通俗一点讲，它不仅仅支持查询，还支持删除、插入数据。而且，这些操作都非常高效。如果不高效，也就算不上是有效的动态数据结构了。所以，这里的红黑树算一个，支持动态的插入、删除、查找，而且效率都很高。链表、队列、栈实际上算不上，因为操作非常有限，查询效率不高。那现在你再想一下还有哪些支持动态插入、删除、查找数据并且效率都很高的的数据结构呢？？





散列表：插入删除查找都是O(1), 是最常用的，但其缺点是不能顺序遍历以及扩容缩容的性能损耗。适用于那些不需要顺序遍历，数据更新不那么频繁的。

跳表：插入删除查找都是O($log{n}$), 并且能顺序遍历。缺点是空间复杂度O(n)。适用于不那么在意内存空间的，其顺序遍历和区间查找非常方便。

红黑树：插入删除查找都是O($log_2{n}$), 中序遍历即是顺序遍历，稳定。缺点是难以实现，去查找不方便。其实跳表更佳，但红黑树已经用于很多地方了。



看了这篇文章还是有很多疑惑，主要是 不理解红黑树满足的几个性质，为什么要那样要求？



动态数据结构有链表，栈，队列，哈希表等等。链表适合遍历的场景，插入和删除操作方便，栈和队列可以算一种特殊的链表，分别适用先进后出和先进先出的场景。哈希表适合插入和删除比较少（尽量少的扩容和缩容），查找比较多的时候。红黑树对数据要求有序，对数据增删查都有一定要求的时候。（个人看法，欢迎老师指正）
作者回复: 👍 刚看错了。写的不错






红黑树的节点颜色，是如何确定的，如何知道在新增一个节点时，该节点是什么颜色？

从红黑树需要满足的四个要求来看：
1. 根节点为黑色
2. 所有叶子节点为黑色，且不存储数据
3. 相邻两个节点不能都为红色
4. 从某节点到其所有叶子节点的路径中，黑色节点数相同

从这四点要求，好像我一根树全是黑色，也是满足其定义的。这里用红黑两色区分各节点，意义是啥？

作者回复: 新插入的节点都是红色的。全黑不可能的。红黑区分的意义你等下一节课看看能懂不





我觉得红黑树对于初学者来说最大的疑惑是，红黑节点有什么区别，是怎么演化来的？老师讲的很好，但是这个问题并没有在文中解决，所以不能深刻地理解红黑树的存在价值。我找到一篇文章，在这方面讲的很清楚，可以作为这篇讲义的补充：https://www.cnblogs.com/tiancai/p/9072813.html




我除了看懂红黑树是一种“性能上比较均衡”的二叉树这个结论外，完全没搞懂它为啥能获得这个“比较平衡”的结果



没明白红节点与黑节点的存在意义



动态数据结构比如本篇的平衡二叉查找树，还有就是跳表，跳表也支持动态插入，删除，查询，也很快，不同点是跳表还能支持快速的范围查询。比如leveldb中的memtable，redis都是使用跳表实现的，而也有用红黑树实现的memtable。
除此之外，跳表还支持多写多读，而红黑树不可以，这些场景下显然用跳表合适。




动态的数据结构可能跳跃链表算一个，实现比红黑树简单，查询，删除，插入都可以；大顶堆，小顶堆应该也还行。



没太看懂去掉红色节点生成全黑四叉树，又把红色节点加进去的目的是什么？感觉不懂这个整个红黑树就没学懂。



“从四叉树中取出某些节点，放到叶节点位置，四叉树就变成了完全二叉树”
老师，这里的某些节点应该怎么取，才能让四叉树变成二叉树，不是很明白，希望老师讲解一下，谢谢



老师好，请问之后讲到heap sort这些的时候会提到斐波那契堆吗？最近在看相关但是觉得网上的资料讲得都不太清楚。




这节课除了要知道红黑树的定义外，还知道了它的应用场景和各操作的时间复杂度，应用场景就是需要很多动态更新的情况。
在分析下它的优势。首先二叉查找树相比于散列表的优势在于“查找”二字，就是支持快速查找，因为左右子树的值是有序的。其次，平衡二叉树的优势在于“平衡”，即不会出现普通二叉查找树退化到左右子树相差极大的情况。最后，平衡二叉查找树则综合了上述两者的有点，在工业项目中广泛应用。





红黑树对应不对
性质1. 节点是红色或黑色。
性质2. 根节点是黑色。
性质3 每个叶节点（NIL节点，空节点）是黑色的。
性质4 每个红色节点的两个子节点都是黑色。(从每个叶子到根的所有路径上不能有两个连续的红色节点)
性质5. 从任一节点到其每个叶子的所有路径都包含相同数目的黑色节点



为了平衡，为了不退化，引起的一个链式反应
平衡二叉查找树（AVL树）------任意节点的左右子树的高度相差不能大于1
平衡相对的是不平衡，左右基本一致可以认为是广义的平衡，而严格的平衡就是高度差不出现大于1 的情况

平衡二叉查找树 VS 红黑树
红黑树是非严格的平衡二叉树，避开了维持严格平衡带来的复杂的调整平衡的操作 --- 红黑是一种非常稳定的二叉查找树
红黑树的性质：
1、根结点是黑的，叶子节点也是黑的，任意节点到叶子节点的黑色节点个数相同，红点会被黑点隔开

问答（动态数据结构切要求是高效的）：
散列表，数组，跳表，红黑树



老师，你这里提到：我画了两个红黑树的图例，你可以对照着看下。
这下面的两张图，左边第一个，四个叶子节点都是红色呀？我有点看不懂。。
规则不是说叶子节点都是不存储数据的黑色空节点吗？这里是不是画错了？
如果你是说省略了黑色叶子节点，那就是说这四个红色节点下其实还有叶子节点？但是这样又不能满足规则四：每个节点，从该节点到达其可达叶子节点的所有路径，都包含相同数目的黑色节点...除非第三层的第三个黑色子节点下也有叶子节点？
作者回复: 是的 都会有nil的黑色叶子节点的