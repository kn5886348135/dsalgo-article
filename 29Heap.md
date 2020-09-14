堆的应用：如何快速获取到Top 10最热门的搜索关键词？
===

&emsp;&emsp;搜索引擎的热门搜索排行榜功能实现，搜索引擎每天会接收大量的用户搜索请求，它会把这些用户输入的搜索关键词记录下来，然后再离线地统计分析，得到最热门的 Top 10 搜索关键词。

&emsp;&emsp;**假设现在有一个包含 10 亿个搜索关键词的日志文件，如何能快速获取到热门榜 Top 10 的搜索关键词呢？**

&emsp;&emsp;堆这种数据结构几个非常重要的应用：优先级队列、求 Top K 和求中位数。



##### 堆的应用一：优先级队列

&emsp;&emsp;队列最大的特性就是先进先出，在优先级队列中，数据的出队顺序不是先进先出，而是按照优先级来，优先级最高的，最先出队。

&emsp;&emsp;实现一个优先级队列的方法有很多，但是用堆来实现是最直接、最高效的。堆和优先级队列非常相似。一个堆就可以看作一个优先级队列。很多时候，它们只是概念上的区分而已。往优先级队列中插入一个元素，就相当于往堆中插入一个元素；从优先级队列中取出优先级最高的元素，就相当于取出堆顶元素。

&emsp;&emsp;优先级队列的应用场景非常多，比如，赫夫曼编码、图的最短路径、最小生成树算法等等。不仅如此，很多语言中，都提供了优先级队列的实现，比如，Java 的 PriorityQueue，C++ 的 priority_queue 等。

**1. 合并有序小文件**

&emsp;&emsp;假设有 100 个小文件，每个文件的大小是 100MB，每个文件中存储的都是有序的字符串。希望将这些 100 个小文件合并成一个有序的大文件。这里就会用到优先级队列。

&emsp;&emsp;整体思路有点像归并排序中的合并函数。从这 100 个文件中，各取第一个字符串，放入数组中，然后比较大小，把最小的那个字符串放入合并后的大文件中，并从数组中删除。

&emsp;&emsp;假设，这个最小的字符串来自于 13.txt 这个小文件，就再从这个小文件取下一个字符串，并且放到数组中，重新比较大小，并且选择最小的放入合并后的大文件，并且将它从数组中删除。依次类推，直到所有的文件中的数据都放入到大文件为止。

&emsp;&emsp;这里用数组这种数据结构，来存储从小文件中取出来的字符串。每次从数组中取最小字符串，都需要循环遍历整个数组，显然，这不是很高效。有没有更加高效方法呢？

&emsp;&emsp;这里就可以用到优先级队列，也可以说是堆。将从小文件中取出来的字符串放入到小顶堆中，那堆顶的元素，也就是优先级队列队首的元素，就是最小的字符串。将这个字符串放入到大文件中，并将其从堆中删除。然后再从小文件中取出下一个字符串，放入到堆中。循环这个过程，就可以将 100 个小文件中的数据依次放入到大文件中。

&emsp;&emsp;知道，删除堆顶数据和往堆中插入数据的时间复杂度都是 O($log{n}$)，n 表示堆中的数据个数，这里就是 100。

**2. 高性能定时器**

&emsp;&emsp;假设有一个定时器，定时器中维护了很多定时任务，每个任务都设定了一个要触发执行的时间点。定时器每过一个很小的单位时间（比如 1 秒），就扫描一遍任务，看是否有任务到达设定的执行时间。如果到达了，就拿出来执行。

![定时器](assert/Timer.jpg)

&emsp;&emsp;但是，这样每过 1 秒就扫描一遍任务列表的做法比较低效，主要原因有两点：第一，任务的约定执行时间离当前时间可能还有很久，这样前面很多次扫描其实都是徒劳的；第二，每次都要扫描整个任务列表，如果任务列表很大的话，势必会比较耗时。

&emsp;&emsp;针对这些问题，就可以用优先级队列来解决。按照任务设定的执行时间，将这些任务存储在优先级队列中，队列首部（也就是小顶堆的堆顶）存储的是最先执行的任务。

&emsp;&emsp;这样，定时器就不需要每隔 1 秒就扫描一遍任务列表了。它拿队首任务的执行时间点，与当前时间点相减，得到一个时间间隔 T。

&emsp;&emsp;这个时间间隔 T 就是，从当前时间开始，需要等待多久，才会有第一个任务需要被执行。这样，定时器就可以设定在 T 秒之后，再来执行任务。从当前时间点到（T-1）秒这段时间里，定时器都不需要做任何事情。

&emsp;&emsp;当 T 秒时间过去之后，定时器取优先级队列中队首的任务执行。然后再计算新的队首任务的执行时间点与当前时间点的差值，把这个值作为定时器执行下一个任务需要等待的时间。

&emsp;&emsp;这样，定时器既不用间隔 1 秒就轮询一次，也不用遍历整个任务列表，性能也就提高了。



##### 堆的应用二：利用堆求 Top K

&emsp;&emsp;把这种求 Top K 的问题抽象成两类。一类是针对静态数据集合，也就是说数据集合事先确定，不会再变。另一类是针对动态数据集合，也就是说数据集合事先并不确定，有数据动态地加入到集合中。

&emsp;&emsp;针对静态数据，如何在一个包含 n 个数据的数组中，查找前 K 大数据呢？可以维护一个大小为 K 的小顶堆，顺序遍历数组，从数组中取出取数据与堆顶元素比较。如果比堆顶元素大，就把堆顶元素删除，并且将这个元素插入到堆中；如果比堆顶元素小，则不做处理，继续遍历数组。这样等数组中的数据都遍历完之后，堆中的数据就是前 K 大数据了。

&emsp;&emsp;遍历数组需要 O(n) 的时间复杂度，一次堆化操作需要 O($log{K}$) 的时间复杂度，所以最坏情况下，n 个元素都入堆一次，所以时间复杂度就是 O(nlogK)。

&emsp;&emsp;针对动态数据求得 Top K 就是实时 Top K。怎么理解呢？我举一个例子。一个数据集合中有两个操作，一个是添加数据，另一个询问当前的前 K 大数据。

&emsp;&emsp;如果每次询问前 K 大数据，都基于当前的数据重新计算的话，那时间复杂度就是 O(n$log{K}$)，n 表示当前的数据的大小。实际上，可以一直都维护一个 K 大小的小顶堆，当有数据被添加到集合中时，就拿它与堆顶的元素对比。如果比堆顶元素大，就把堆顶元素删除，并且将这个元素插入到堆中；如果比堆顶元素小，则不做处理。这样，无论任何时候需要查询当前的前 K 大数据，都可以里立刻返回给他。



##### 堆的应用三：利用堆求中位数

&emsp;&emsp;中位数，就是处在中间位置的那个数。如果数据的个数是奇数，把数据从小到大排列，那第 $\frac{n}{2}$+1 个数据就是中位数；如果数据的个数是偶数的话，那处于中间位置的数据有两个，第 $\frac{n}{2}$ 个和第 $\frac{n}{2}$+1 个数据，这个时候，可以随意取一个作为中位数，比如取两个数中靠前的那个，就是第 $\frac{n}{2}$ 个数据。

![中位数](assert/MedianA.jpg)

&emsp;&emsp;对于一组静态数据，中位数是固定的，可以先排序，第 $\frac{n}{2}$ 个数据就是中位数。每次询问中位数的时候，直接返回这个固定的值就好了。所以，尽管排序的代价比较大，但是边际成本会很小。但是，如果面对的是动态数据集合，中位数在不停地变动，如果再用先排序的方法，每次询问中位数的时候，都要先进行排序，那效率就不高了。

&emsp;&emsp;借助堆这种数据结构，不用排序，就可以非常高效地实现求中位数操作。

&emsp;&emsp;维护两个堆，一个大顶堆，一个小顶堆。大顶堆中存储前半部分数据，小顶堆中存储后半部分数据，且小顶堆中的数据都大于大顶堆中的数据。

&emsp;&emsp;也就是说，如果有 n 个数据，n 是偶数，从小到大排序，那前 $\frac{n}{2}$ 个数据存储在大顶堆中，后 $\frac{n}{2}$ 个数据存储在小顶堆中。这样，大顶堆中的堆顶元素就是要找的中位数。如果 n 是奇数，情况是类似的，大顶堆就存储 $\frac{n}{2}$+1 个数据，小顶堆中就存储 $\frac{n}{2}$ 个数据。

![中位数](assert/MedianB.jpg)

&emsp;&emsp;数据是动态变化的，当新添加一个数据的时候，如何调整两个堆，让大顶堆中的堆顶元素继续是中位数呢？

&emsp;&emsp;如果新加入的数据小于等于大顶堆的堆顶元素，就将这个新数据插入到大顶堆；如果新加入的数据大于等于小顶堆的堆顶元素，就将这个新数据插入到小顶堆。

&emsp;&emsp;这个时候就有可能出现，两个堆中的数据个数不符合前面约定的情况：如果 n 是偶数，两个堆中的数据个数都是 $\frac{n}{2}$；如果 n 是奇数，大顶堆有 $\frac{n}{2}$+1 个数据，小顶堆有 $\frac{n}{2}$ 个数据。这个时候，可以从一个堆中不停地将堆顶元素移动到另一个堆，通过这样的调整，来让两个堆中的数据满足上面的约定。

![中位数](assert/MedianC.jpg)

&emsp;&emsp;于是，就可以利用两个堆，一个大顶堆、一个小顶堆，实现在动态数据集合中求中位数的操作。插入数据因为需要涉及堆化，所以时间复杂度变成了 O($log{n}$)，但是求中位数只需要返回大顶堆的堆顶元素就可以了，所以时间复杂度就是 O(1)。

&emsp;&emsp;实际上，利用两个堆不仅可以快速求出中位数，还可以快速求其他百分位的数据，原理是类似的。“如何快速求接口的 99% 响应时间？”现在就来看下，利用两个堆如何来实现。

&emsp;&emsp;中位数的概念就是将数据从小到大排列，处于中间位置，就叫中位数，这个数据会大于等于前面 50% 的数据。99 百分位数的概念可以类比中位数，如果将一组数据从小到大排列，这个 99 百分位数就是大于前面 99% 数据的那个数据。

&emsp;&emsp;如果你还是不太理解，我再举个例子。假设有 100 个数据，分别是 1，2，3，……，100，那 99 百分位数就是 99，因为小于等于 99 的数占总个数的 99%。

![中位数](assert/MedianD.jpg)

&emsp;&emsp;如果有 100 个接口访问请求，每个接口请求的响应时间都不同，比如 55 毫秒、100 毫秒、23 毫秒等，把这 100 个接口的响应时间按照从小到大排列，排在第 99 的那个数据就是 99% 响应时间，也叫 99 百分位响应时间。

&emsp;&emsp;如果有 n 个数据，将数据从小到大排列之后，99 百分位数大约就是第 n$*$99$\%$ 个数据，同类，80 百分位数大约就是第 n*80$\%$ 个数据。

&emsp;&emsp;维护两个堆，一个大顶堆，一个小顶堆。假设当前总数据的个数是 n，大顶堆中保存 n$*$99$\%$ 个数据，小顶堆中保存 n$*$1$\%$ 个数据。大顶堆堆顶的数据就是要找的 99$\%$ 响应时间。

&emsp;&emsp;每次插入一个数据的时候，要判断这个数据跟大顶堆和小顶堆堆顶数据的大小关系，然后决定插入到哪个堆中。如果这个新插入的数据比大顶堆的堆顶数据小，那就插入大顶堆；如果这个新插入的数据比小顶堆的堆顶数据大，那就插入小顶堆。

&emsp;&emsp;但是，为了保持大顶堆中的数据占 99$\%$，小顶堆中的数据占 1$\%$，在每次新插入数据之后，都要重新计算，这个时候大顶堆和小顶堆中的数据个数，是否还符合 99:1 这个比例。如果不符合，就将一个堆中的数据移动到另一个堆，直到满足这个比例。移动的方法类似前面求中位数的方法，这里我就不啰嗦了。

&emsp;&emsp;通过这样的方法，每次插入数据，可能会涉及几个数据的堆化操作，所以时间复杂度是 O($log{n}$)。每次求 99$\%$ 响应时间的时候，直接返回大顶堆中的堆顶数据即可，时间复杂度是 O(1)。



##### 解答开篇

&emsp;&emsp;。假设现在有一个包含 10 亿个搜索关键词的日志文件，如何快速获取到 Top 10 最热门的搜索关键词呢？

&emsp;&emsp;处理这个问题，有很多高级的解决方法，比如使用 MapReduce 等。但是，如果将处理的场景限定为单机，可以使用的内存为 1GB。那这个问题该如何解决呢？

&emsp;&emsp;因为用户搜索的关键词，有很多可能都是重复的，所以首先要统计每个搜索关键词出现的频率。可以通过散列表、平衡二叉查找树或者其他一些支持快速查找、插入的数据结构，来记录关键词及其出现的次数。

&emsp;&emsp;假设选用散列表。就顺序扫描这 10 亿个搜索关键词。当扫描到某个关键词时，去散列表中查询。如果存在，就将对应的次数加一；如果不存在，就将它插入到散列表，并记录次数为 1。以此类推，等遍历完这 10 亿个搜索关键词之后，散列表中就存储了不重复的搜索关键词以及出现的次数。

&emsp;&emsp;然后，再根据前面讲的用堆求 Top K 的方法，建立一个大小为 10 的小顶堆，遍历散列表，依次取出每个搜索关键词及对应出现的次数，然后与堆顶的搜索关键词对比。如果出现次数比堆顶搜索关键词的次数多，那就删除堆顶的关键词，将这个出现次数更多的关键词加入到堆中。

&emsp;&emsp;以此类推，当遍历完整个散列表中的搜索关键词之后，堆中的搜索关键词就是出现次数最多的 Top 10 搜索关键词了。

&emsp;&emsp;不知道你发现了没有，上面的解决思路其实存在漏洞。10 亿的关键词还是很多的。假设 10 亿条搜索关键词中不重复的有 1 亿条，如果每个搜索关键词的平均长度是 50 个字节，那存储 1 亿个关键词起码需要 5GB 的内存空间，而散列表因为要避免频繁冲突，不会选择太大的装载因子，所以消耗的内存空间就更多了。而的机器只有 1GB 的可用内存空间，所以无法一次性将所有的搜索关键词加入到内存中。这个时候该怎么办呢？

&emsp;&emsp;在哈希算法那一节讲过，相同数据经过哈希算法得到的哈希值是一样的。可以哈希算法的这个特点，将 10 亿条搜索关键词先通过哈希算法分片到 10 个文件中。

&emsp;&emsp;具体可以这样做：创建 10 个空文件 00，01，02，……，09。遍历这 10 亿个关键词，并且通过某个哈希算法对其求哈希值，然后哈希值同 10 取模，得到的结果就是这个搜索关键词应该被分到的文件编号。

&emsp;&emsp;对这 10 亿个关键词分片之后，每个文件都只有 1 亿的关键词，去除掉重复的，可能就只有 1000 万个，每个关键词平均 50 个字节，所以总的大小就是 500MB。1GB 的内存完全可以放得下。

&emsp;&emsp;针对每个包含 1 亿条搜索关键词的文件，利用散列表和堆，分别求出 Top 10，然后把这个 10 个 Top 10 放在一块，然后取这 100 个关键词中，出现次数最多的 10 个关键词，这就是这 10 亿数据中的 Top 10 最频繁的搜索关键词了。



##### 内容小结

&emsp;&emsp;优先级队列是一种特殊的队列，优先级高的数据先出队，而不再像普通的队列那样，先进先出。实际上，堆就可以看作优先级队列，只是称谓不一样罢了。求 Top K 问题又可以分为针对静态数据和针对动态数据，只需要利用一个堆，就可以做到非常高效率的查询 Top K 的数据。求中位数实际上还有很多变形，比如求 99 百分位数据、90 百分位数据等，处理的思路都是一样的，即利用两个堆，一个大顶堆，一个小顶堆，随着数据的动态添加，动态调整两个堆中的数据，最后大顶堆的堆顶元素就是要求的数据。



##### 课后思考

&emsp;&emsp;有一个访问量非常大的新闻网站，希望将点击量排名 Top 10 的新闻摘要，滚动显示在网站首页 banner 上，并且每隔 1 小时更新一次。如果你是负责开发这个功能的工程师，你会如何来实现呢？



1，对每篇新闻摘要计算一个hashcode，并建立摘要与hashcode的关联关系，使用map存储，以hashCode为key，新闻摘要为值
2，按每小时一个文件的方式记录下被点击的摘要的hashCode
3，当一个小时结果后，上一个小时的文件被关闭，开始计算上一个小时的点击top10
4，将hashcode分片到多个文件中，通过对hashCode取模运算，即可将相同的hashCode分片到相同的文件中
5，针对每个文件取top10的hashCode，使用Map<hashCode,int>的方式，统计出所有的摘要点击次数，然后再使用小顶堆（大小为10）计算top10,
6，再针对所有分片计算一个总的top10,最后合并的逻辑也是使用小顶堆，计算top10
7，如果仅展示前一个小时的top10,计算结束
8，如果需要展示全天，需要与上一次的计算按hashCode进行合并，然后在这合并的数据中取top10
9，在展示时，将计算得到的top10的hashcode，转化为新闻摘要显示即可

1，合并有序小文件
https://github.com/kkzfl22/datastruct/tree/master/src/main/java/com/liujun/datastruct/heap/solution/margeSmailFile

2，高性能定时器的应用
https://github.com/kkzfl22/datastruct/tree/master/src/main/java/com/liujun/datastruct/heap/solution/highTimeSchedule
3，求topk
https://github.com/kkzfl22/datastruct/tree/master/src/main/java/com/liujun/datastruct/heap/solution/topK
4，求中位数
https://github.com/kkzfl22/datastruct/tree/master/src/main/java/com/liujun/datastruct/heap/solution/midnum
5 ,大文件的关键字的统计
https://github.com/kkzfl22/datastruct/tree/master/src/main/java/com/liujun/datastruct/heap/solution/bigFileTopN






“如果新加入的数据小于等于大顶堆的堆顶元素，就将这个新数据插入到大顶堆；如果新加入的数据大于等于小顶堆的堆顶元素，就将这个新数据插入到小顶堆。”

1. 这里不太对劲，前文中说到，小顶堆的堆顶大于大顶堆的堆顶。
如果新进元素在小顶堆堆顶和大顶堆堆顶元素值之间，没有规定插入哪个堆。

我觉得，是不是只要判断一次就可以了。新进元素值大于等于小顶堆堆顶元素的，插入小顶堆，否则插入大顶堆。
当某一个堆数据过多时再重新移动堆顶元素。
2. 求中位数的源数据中，是否允许重复数据？
作者回复: 1 你说的对 我改下 多谢指正
2 可以重复





定时任务这个例子感觉有问题吧，定时任务是动态加入的，假设当前堆顶的任务是一个小时后的，难道这一个小时都不做扫描吗，随时可能会加入需要更早执行的任务





10亿关键词分片之后 每个文件并不一定有1亿的关键词吧 老师





老师，分片求取前十是不是有bug，如果有一个关键词在每一组分片中都是前第十一位，在整个十亿中个数总和是第一位，是不是用分片求出了错误的结果呢？
作者回复: 不会的 相同的关键词经过哈希之后只会到一台机器





老师，请问为什么评价算法性能是根据时间和空间复杂度，而不是别的参数？是因为计算机结构是冯诺依曼体系，除了输入输出设备和控制器，就剩下运算器和存储器了吗？
作者回复: 你理解的没错





堆求topK的静态数据 应该是先把堆填满 再拿数组中的元素跟堆顶比较吧






思考题：1，维护两个散列表，一个是一小时新增的点击量的散列表，以新闻id为键，点击次数为值。一个是全部点击量的散列表。每隔一小时把新增的散列表的数据同步到全部点击量的散列表。然后把这小时内有变化的全部点击量的散列表的数据（即此小时有新增点击量的新闻数据）和维护的10个元素小顶堆堆顶进行比较，比堆顶的点击量大的，则使用该元素替换堆顶，再进行堆化。比堆顶点击量小的则不做处理。然后比较完，根据堆顶的10个元素的id，从数据库读取相应的新闻摘要显示在banner上。除此之外，还要把变化后的全部点击量散列表同步到数据库。因为保存的是新闻id，所以散列表长度不会很大，所占用的内存也不会很大。而每个小时新增的访问量的新闻id数也不会很多，毕竟很多人只会阅读热门消息。所以新增的点击量的新闻数据假设为k,则每小时同步小顶堆的时间负责度为o(klg 10);





1:建一个散射列表，key为点击网址，value为点击次数。散射列表通过从log中计算得来。
2:建一个10个数据的小顶堆，数据值为点击次数，扫描散射列表，新元素次数比堆顶元素大则删除堆顶元素，插入新元素，小则继续扫描散射列表。
3:扫描完整个散射列表后，即得到top 10点击量，将点击网址存储在数组A中。数组A一个小时更新一次。
4:散射列表实时更新，小顶堆也实时更新，以一小时为间隔，将小顶堆结果更新到数组A中。





方案前提，所有数据都保存在一台服务器的内存中，不考虑HA、数据更新冲突等情况。假设每条新闻都有一个全局唯一的新闻ID，使用hashmap(map_a)来保存每篇新闻的访问量，key为新闻ID，value为当前访问总次数。使用另一个hashmap(map_b)来保存一个周期内map_a中value值发生变化的key。

整个方案分为三个阶段，堆的初始化、hashmap实时变更、堆更新。
初始化阶段：建立一个大小为10的小顶堆，遍历此时的hashmap，完成堆的初始化。
hashmap实时变更阶段：保存在当前周期内，将map_a中value产生变化的key到map_b中。
堆更新阶段：在一个周期结束后，遍历map_b，并将map_a中保存的value与当前堆顶进行比较，如果大于堆顶，则删除堆顶，并插入该value，如果小于堆顶则不做处理。遍历完map_b之后，该堆保有了上个周期访问量top10的新闻id和value。最后清空map_b，为下一个周期作准备。最坏时间复杂度为O(nlog10)，其中n为map_b中key的数量。






看了这一章，发现堆删除任意元素这个方法毫无意义啊。只有删除堆顶元素才有意义
作者回复: 是的啊 没有说过删除任意元素呢





topK 是不是应该先要填满堆，后面插入的时候再做删除操作
作者回复: 是的。





如果我要1%到99%响应时间，这样建的堆就有点多了
作者回复: 这需求...具体问题具体分析吧





王老师 第一点合并有序小文件 为什么要用到优先级队列 和 堆还是不理解。两个比最小取出合并，只要两个数组是有序就可以了，快排成有序，从小到大比较合并，不可以吗，为什么要用到优先级队列，方便老师解答下吗
作者回复: 没太看懂你说的 用优先级是为了效率





遍历这 10 亿个关键词，并且通过某个哈希算法对其求哈希值，然后哈希值同 10 取模，得到的结果就是这个搜索关键词应该被分到的文件编号。
这样并不能保证每个文件都是一亿条数据吧？可能多也可能少吧？
展开
作者回复: 是的 你说的没错





Hadoop、Spark入门demo——wordcount了解下






可以先用散列表存帖子和点击数的关系，然后每一小时定时遍历散列表用文中的方法，往大小为10的小顶堆插数据？





文章中『解答开篇』部分，说是扫描1亿个热门关键词，这应该是错别字吧，应该是10亿个吧。看了好几遍，我应该没理解错吧😄
老师说使用散列表统计10亿个搜索关键词的频率，但是这里的约束条件是10亿个关键词中确实有很多重复，而且去重之后的数据，内存中是能够放得下的。如果单机内存放不下，应该就不能这么做了

---------------------------------------------------------

以上是我早上本来要留言的，但是并没有一字不漏的看完文章。我回头一想不对，文章中肯定会考虑到这个情况。当我看完，我就把以上留言删了。

唉，阅读时，犯了一个低级错误，记录在此，提醒自己
作者回复: 是我写错了 不好意思 马上修改



原理上跟统计热门搜索关键词类似。后台起一个定时任务，从最新被新点击的新闻日志文件中统计出每条新闻的点击量(也得类似于老师那样使用散列表)，然后建立和维护内存中大小为10的最大堆，这样网站点击次数Top10的新闻就被统计出来了。

这题也可以使用MapReduce算法





最近看PriorityBlockingQueue源码，offer与poll的底层就是使用的堆这种数据结构。看此类的代码联系到了了本章的内容，理解起来就爽歪歪了