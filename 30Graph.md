图的表示：如何存储微博、微信等社交网络中的好友关系？
===

&emsp;&emsp;如何存储微博、微信等这些社交网络的好友关系吗？

&emsp;&emsp;涉及图的算法有很多，也非常复杂，比如图的搜索、最短路径、最小生成树、二分图等。



##### 如何理解“图”？

&emsp;&emsp;图（Graph）和树都是非线性表数据结构，和树比起来，图是一种更加复杂的非线性表结构。

&emsp;&emsp;树中的元素称为节点，图中的元素叫作顶点（vertex）。图中的一个顶点可以与任意其他顶点建立连接关系，这种建立的关系叫作边（edge）。

<img src="assert/VertexAndEdgeInGraph.jpg" alt="图中的顶点和边" style="zoom:50%;" />

&emsp;&emsp;社交网络，就是一个非常典型的图结构。

&emsp;&emsp;把微信每个用户看作一个顶点。如果两个用户之间互加好友，那就在两者之间建立一条边。所以，整个微信的好友关系就可以用一张图来表示。其中，每个用户有多少个好友，对应到图中，就叫作顶点的度（degree），就是跟顶点相连接的边的条数。

&emsp;&emsp;微博的社交关系跟微信还有点不一样，或者说更加复杂一点。微博允许单向关注，也就是说，用户 A 关注了用户 B，但用户 B 可以不关注用户 A。那如何用图来表示这种单向的社交关系呢？

&emsp;&emsp;可以把刚刚讲的图结构稍微改造一下，引入边的“方向”的概念。

&emsp;&emsp;如果用户 A 关注了用户 B，就在图中画一条从 A 到 B 的带箭头的边，来表示边的方向。如果用户 A 和用户 B 互相关注了，那就画一条从 A 指向 B 的边，再画一条从 B 指向 A 的边。把这种边有方向的图叫作“有向图”。以此类推，把边没有方向的图就叫作“无向图”。

<img src="assert/DirectedGraph.jpg" alt="有向图" style="zoom:50%;" />

&emsp;&emsp;无向图中有“度”这个概念，表示一个顶点有多少条边。在有向图中，把度分为入度（In-degree）和出度（Out-degree）。

&emsp;&emsp;顶点的入度，表示有多少条边指向这个顶点；顶点的出度，表示有多少条边是以这个顶点为起点指向其他顶点。对应到微博的例子，入度就表示有多少粉丝，出度就表示关注了多少人。

&emsp;&emsp;QQ 中的社交关系要更复杂的一点。不知道你有没有留意过 QQ 亲密度这样一个功能。QQ 不仅记录了用户之间的好友关系，还记录了两个用户之间的亲密度，如果两个用户经常往来，那亲密度就比较高；如果不经常往来，亲密度就比较低。如何在图中记录这种好友关系的亲密度呢？

&emsp;&emsp;这里就要用到另一种图，带权图（weighted graph）。在带权图中，每条边都有一个权重（weight），可以通过这个权重来表示 QQ 好友间的亲密度。

<img src="assert/WeightedGraph.jpg" alt="带权图" style="zoom:50%;" />



##### 邻接矩阵存储方法

&emsp;&emsp;图最直观的一种存储方法就是，**邻接矩阵（Adjacency Matrix）**。

&emsp;&emsp;邻接矩阵的底层依赖一个二维数组。对于无向图来说，如果顶点 i 与顶点 j 之间有边，就将 A[i][j] 和 A[j][i] 标记为 1；对于有向图来说，如果顶点 i 到顶点 j 之间，有一条箭头从顶点 i 指向顶点 j 的边，那就将 A[i][j] 标记为 1。同理，如果有一条箭头从顶点 j 指向顶点 i 的边，就将 A[j][i] 标记为 1。对于带权图，数组中就存储相应的权重。

<img src="assert/AdjacencyMatrix.jpg" alt="带权图" style="zoom:50%;" />

&emsp;&emsp;用邻接矩阵来表示一个图，虽然简单、直观，但是比较浪费存储空间。

&emsp;&emsp;对于无向图来说，如果 A[i][j] 等于 1，那 A[j][i] 也肯定等于 1。实际上，只需要存储一个就可以了。也就是说，无向图的二维数组中，如果将其用对角线划分为上下两部分，那只需要利用上面或者下面这样一半的空间就足够了，另外一半白白浪费掉了。

&emsp;&emsp;如果存储的是稀疏图（Sparse Matrix），顶点很多，但每个顶点的边并不多，那邻接矩阵的存储方法就更加浪费空间了。比如微信有好几亿的用户，对应到图上就是好几亿的顶点。但是每个用户的好友并不会很多，一般也就三五百个而已。如果用邻接矩阵来存储，那绝大部分的存储空间都被浪费了。

&emsp;&emsp;但这也并不是说，邻接矩阵的存储方法就完全没有优点。
1. 邻接矩阵的存储方式简单、直接，因为基于数组，所以在获取两个顶点的关系时，就非常高效。
2. 用邻接矩阵存储图的另外一个好处是方便计算。这是因为，用邻接矩阵的方式存储图，可以将很多图的运算转换成矩阵之间的运算。比如求解最短路径问题时会提到一个[Floyd-Warshall](https://zh.wikipedia.org/wiki/Floyd-Warshall%E7%AE%97%E6%B3%95) 算法，就是利用矩阵循环相乘若干次得到结果。



##### 邻接表存储方法

&emsp;&emsp;针对上面邻接矩阵比较浪费内存空间的问题，来看另外一种图的存储方法，邻接表（Adjacency List）。

&emsp;&emsp;邻接表很像散列表。每个顶点对应一条链表，链表中存储的是与这个顶点相连接的其他顶点。图中画的是一个有向图的邻接表存储方式，每个顶点对应的链表里面，存储的是指向的顶点。对于无向图来说，也是类似的，不过，每个顶点的链表中存储的，是跟这个顶点有边相连的顶点。

<img src="assert/AdjacencyList.jpg" alt="邻接表存储方法" style="zoom:50%;" />

&emsp;&emsp;邻接矩阵存储起来比较浪费空间，但是使用起来比较节省时间。相反，邻接表存储起来比较节省空间，但是使用起来就比较耗时间。

&emsp;&emsp;如果要确定，是否存在一条从顶点 2 到顶点 4 的边，那就要遍历顶点 2 对应的那条链表，看链表中是否存在顶点 4。而且，链表的存储方式对缓存不友好。所以，比起邻接矩阵的存储方式，在邻接表中查询两个顶点之间的关系就没那么高效了。

&emsp;&emsp;在基于链表法解决冲突的散列表中，如果链过长，为了提高查找效率，可以将链表换成其他更加高效的数据结构，比如平衡二叉查找树等。邻接表长得很像散列。所以，也可以将邻接表同散列表一样进行“改进升级”。

&emsp;&emsp;可以将邻接表中的链表改成平衡二叉查找树。实际开发中，可以选择用红黑树。这样，就可以更加快速地查找两个顶点之间是否存在边了。当然，这里的二叉查找树可以换成其他动态数据结构，比如跳表、散列表等。除此之外，还可以将链表改成有序动态数组，可以通过二分查找的方法来快速定位两个顶点之间否是存在边。



##### 解答开篇

&emsp;&emsp;如何存储微博、微信等社交网络中的好友关系？

&emsp;&emsp;前面分析了，微博、微信是两种“图”，前者是有向图，后者是无向图。在这个问题上，两者的解决思路差不多。

&emsp;&emsp;数据结构是为算法服务的，所以具体选择哪种存储方法，与期望支持的操作有关系。针对微博用户关系，假设需要支持下面这样几个操作：

* 判断用户 A 是否关注了用户 B；

* 判断用户 A 是否是用户 B 的粉丝；

* 用户 A 关注用户 B；

* 用户 A 取消关注用户 B；

* 根据用户名称的首字母排序，分页获取用户的粉丝列表；

* 根据用户名称的首字母排序，分页获取用户的关注列表。

&emsp;&emsp;图的两种主要的存储方法，邻接矩阵和邻接表。因为社交网络是一张稀疏图，使用邻接矩阵存储比较浪费存储空间。所以，这里采用邻接表来存储。

&emsp;&emsp;不过，用一个邻接表来存储这种有向图是不够的。去查找某个用户关注了哪些用户非常容易，但是如果要想知道某个用户都被哪些用户关注了，也就是用户的粉丝列表，是非常困难的。

&emsp;&emsp;基于此，需要一个逆邻接表。邻接表中存储了用户的关注关系，逆邻接表中存储的是用户的被关注关系。对应到图上，邻接表中，每个顶点的链表中，存储的就是这个顶点指向的顶点，逆邻接表中，每个顶点的链表中，存储的是指向这个顶点的顶点。如果要查找某个用户关注了哪些用户，可以在邻接表中查找；如果要查找某个用户被哪些用户关注了，从逆邻接表中查找。

<img src="assert/ContraryAdjacencyList.jpg" alt="逆邻接表存储方法" style="zoom:50%;" />

&emsp;&emsp;基础的邻接表不适合快速判断两个用户之间是否是关注与被关注的关系，所以选择改进版本，将邻接表中的链表改为支持快速查找的动态数据结构。红黑树、跳表、有序动态数组还是散列表呢？

&emsp;&emsp;因为需要按照用户名称的首字母排序，分页来获取用户的粉丝列表或者关注列表，用跳表这种结构再合适不过了。因为，跳表插入、删除、查找都非常高效，时间复杂度是 O($log{n}$)，空间复杂度上稍高，是 O(n)。最重要的一点，跳表中存储的数据本来就是有序的了，分页获取粉丝列表或关注列表，就非常高效。

&emsp;&emsp;如果对于小规模的数据，比如社交网络中只有几万、几十万个用户，可以将整个社交关系存储在内存中，上面的解决思路是没有问题的。但是如果像微博那样有上亿的用户，数据规模太大，就无法全部存储在内存中了。这个时候该怎么办呢？

&emsp;&emsp;可以通过哈希算法等数据分片方式，将邻接表存储在不同的机器上。你可以看下面这幅图，在机器 1 上存储顶点 1，2，3 的邻接表，在机器 2 上，存储顶点 4，5 的邻接表。逆邻接表的处理方式也一样。当要查询顶点与顶点关系的时候，就利用同样的哈希算法，先定位顶点所在的机器，然后再在相应的机器上查找。

<img src="assert/StripingStorageOfAdjacencyMatrixAndAdjacencyList.jpg" alt="邻接表分片存储方法" style="zoom:50%;" />

&emsp;&emsp;除此之外，还有另外一种解决思路，就是利用外部存储（比如硬盘），因为外部存储的存储空间要比内存会宽裕很多。数据库是经常用来持久化存储关系数据的，所以我这里介绍一种数据库的存储方式。

&emsp;&emsp;我用下面这张表来存储这样一个图。为了高效地支持前面定义的操作，可以在表上建立多个索引，比如第一列、第二列，给这两列都建立索引。

<img src="assert/GraphStorageInDatabase.jpg" alt="数据库存储图" style="zoom:50%;" />



##### 内容小结

&emsp;&emsp;图是非线性表数据结构，主要概念：无向图、有向图、带权图、顶点、边、度、入度、出度。图的两个主要的存储方式：邻接矩阵和邻接表。

&emsp;&emsp;邻接矩阵存储方法的缺点是比较浪费空间，但是优点是查询效率高，而且方便矩阵运算。邻接表存储方法中每个顶点都对应一个链表，存储与其相连接的其他顶点。尽管邻接表的存储方式比较节省存储空间，但链表不方便查找，所以查询效率没有邻接矩阵存储方式高。针对这个问题，邻接表还有改进升级版，即将链表换成更加高效的动态数据结构，比如平衡二叉查找树、跳表、散列表等。



##### 课后思考

&emsp;&emsp;关于开篇思考题，只讲了微博这种有向图的解决思路，那像微信这种无向图，应该怎么存储呢？

&emsp;&emsp;除了我今天举的社交网络可以用图来表示之外，符合图这种结构特点的例子还有很多，比如知识图谱（Knowledge Graph）。关于图这种数据结构，你还能想到其他生活或者工作中的例子吗？





学了这么久的数据结构和算法，今天突然顿悟，基础的数据结构就是数组和链表， 而后面更加复杂的 树 队列 图 等等 都可以通过数组和链表等方式存储， 出现树 队列 图 等数据结构的原因 就是为了解决 部分问题处理过程中时间复杂度过高的问题， 所以数据结构就是为了算法而生的！ 尤其是学习了时间复杂度过后 在工作和学习过程中 就应该分析自己的代码复杂度 以进行优化或者选择更好的数据结构和算法！这样才能写出更好的代码更好的解决问题





地图
网络
Gradle这个编译工具，内部组织task的方式用的是有向图

Android framework层提供了一个CoordinatorLayout，其内部协调子view的联动，也是用的图
作者回复: 👍





请教老师
解决现实问题的时候当存储图有多种选择，例如:
1.用邻接表自己存
2.关系型库
3.图数据库
那么这三种方式每一种的适用场景，优缺点分别是什么呢？该如何取舍
展开
作者回复: 1 内存中用临界表
2 要持久化存储就用数据库
2 超大图 并且涉及大量图计算。用专业的图数据库






1. 微信好友关系存储方式。无向图，也可以使用邻接表的方式存储每个人所对应的好友列表。为了支持快速查找，好友列表可以使用红黑树存储。
2. 生活工作中应用图的例子。很多，互联网上网页之间通过超链接连接成一张有向图；城市乃至全国交通网络是一张加权图；人与人之间的人际关系够成一张图，著名的六度分割理论据说就是基于这个得到的。





学到现在有一种特别明显的感受就是描述一种需求模型可以有很多种组合数据结构，而这些复杂数据结构都是基础数据结构组合起来的，而这些数据结构去的选择又是基于需求模型对时间和空间这两个维度来的，所以解决问题的关键是都需求的理解以及对数据结构的熟练运用。





有序动态数组能否讲解一下
作者回复: 数据有序排列的动态数组





判断用户 A 是否关注了用户 B； 判断用户 A 是否是用户B的粉丝。这两个操作我怎么觉得是一个意思呢？
作者回复: 好像是的 第二个应该是 判断a是否被b关注





微信社交关系的存储方式

因为顶点的数量大且关系相对少，所以不适合用邻接矩阵来存储，应该用邻接表来存储。
微信社交关系的相关操作：1. 判断A、B是否为好友关系 2. A删除B，断开与B的好友关系 3. 展示出A的所有好友，并按名称首字母进行排序

因为是无向图，所以仅需要一个邻接表就行了，然后将链表改造为跳表增加查找速度且在列出好友是会比较方便。最后，若有n台机器可供使用，那么可以对n取余来划分这些数据到不同的机器上，毕竟微信的用户量太大，一个机器的内存应该是不够用的。






刚刚还在写Topology Sort，就是leetcode那个给课程先后顺序排列的题。我还知道社交Graph在推荐系统中应用非常广泛（腾讯的人亲口说这是他们很多产品的最大亮点，因为可以做社交）。使用了social trust的推荐系统非常的robust且能够经受大规模水军的攻击，因为水军无法取得用户的trust（graph中无法建立联系），所以水军的行为在推荐系统中会被认为对给用户的影响会非常的小，使得系统的预测基本不变。





微信也是有向图吧……微信单方面删除好友之后另一方仍然会显示在好友列表中的啊(俗称僵尸)
作者回复: 哈哈，在这个问题上，从你的昵称来看，你最有发言权了。





复杂网络可以说也是基于图，抽象出来的随机网络、小世界网络、无标度网络等都可以用图表示；根据图的组成基本要素：节点和边，现实世界中只要可以将具体的事物抽象出节点，并且节点之间是有联系的，那么应该都可以成为图；比如以城市机场作为节点，城市之间的航班飞行网络；...





问题一：
微信好友：
1、微信的好友关系是稀疏矩阵，为了减少空间浪费，使用邻接表；
2、为了提高查找效率，将邻接表中的链表改为支持快速查找的动态数据结构，这里使用红黑树、跳表都可以，考虑到好友列表是按照字母排序的，可以使用跳表
问题二：
图的例子还有：操作系统的资源分配图是有向图，用来分析死锁问题。





有向图的矩阵下标2 0和3 2的1和0感觉画反了，还有带权图线上面表示的是5矩阵里面表示的5 3不是很理解
作者回复: adj行列都是从1开始的。我检查了一下 好像没画错





感觉最后数据库中那张表用起来就能满足需求了，上面说的图的优势是什么呢





第1题：使用邻接表存储，并且使用改进升级版（使用跳表或散列表等）
第2题：1）我司所开发的工作流项目描述的就是有向图。2）小到公交车/地铁网络图，大到国家的铁路分布图。3）韩国偶像局，人物之间的暗恋关系。4）ETL跑批时，各JOB之间的依赖关系。。。等等等等太多了





微信的用户无向图中，首先为了节约空间，采用的要是邻接表的方式，由于数据量巨大，进一步关于存储的优化和老师文中记述的类似。
图的数据结构相对其他数据结构而言是更加贴合生活场景的，事物和联系的信息可以映射为节点和边，例如百度在地图中的寻路功能应该是要利用到节点和边权重等方面的信息，期待老师对图的用法做更深入的讲解。
最后我希望提一个关于邻接表的问题，文中邻接表中，‘节点’指向的是下一个‘节点’的信息，那么‘边’的信息应该如何保存？要是‘节点’指向的是‘边’的信息，‘边’自己又包含另一头‘节点’的下标，这样的存储方式虽然不是很直观，但是也是一种有效的存储方式。老师是否可以就‘邻接表’上‘边’的存储讲解一下？
展开
作者回复: 实际上 并不需要显示的存储边 具体存储方式你可以看下一节课开头的代码







老师，请问图可以直接用链表表示吗？一个节点有两个指针，一个入度的指针，一个出度的指针。虽然说这样子的查询效率会很低



以本章的第一张图片为例，一直没太明白如果顶点A和顶点F之间有关系，或者顶点A和顶点E之间有关系，那么使用邻接矩阵该如何表示这种关系呢？
展开
作者回复: 需要先把a、f、e等转化成1、2、3....这样的编号，然后用邻接矩阵中的值来表示关系。比如，1表示有关系，0表示没关系。