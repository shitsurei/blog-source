---
copyright: true
sticky: false
permalink: bucketSort
tags: [算法,数据结构,基数排序,计数排序]
date: 2019-05-28 11:20:29
title: 非基于比较的排序——桶排序
categories: 算法和数据结构

---

前面我们学习过的O(N*log(N))级别的三个排序算法都是基于比较的排序算法，在工程中有着广泛的应用，但是还有一种时间复杂度更低，性能相对更好的排序算法——桶排序，而桶排序根据实现方式又分为基数排序和计数排序两种。那么为什么桶排序作为时间性能上更加高效的算法，却并没有在工程领域得到广泛的应用呢？原因在于桶排序算法不是基于比较的排序，而是基于词频的排序算法，那么一个排序算法基于什么到底对其有什么样的影响呢，本文就来分析和讨论一下。

<!-- more -->

## 基于词频的排序算法

前面我们在学习排序算法时反复说排序的过程无非分为比较和交换两个步骤，但有时当我们已经**对要排序的数据状况有了一定程度的了解**之后，就可以设计出不需要比较原有序列中的元素就对其进行排序的算法，桶排序就是基于这样的设计理念产生的。可能你乍一听会想，这怎么可能呢？我都没有比较其中的元素，怎么可能知道两个元素的先后次序呢？其实，在你排序之前已经知道了，这就是所谓的桶。将排序中所有的可能情况分类成桶，桶与桶之间的相对次序一开始就是明确的，接下来我们只需要把元素放进相应的桶中，等到最后再从桶中挨个把元素拿出来，这时整个序列就被桶这个容器归类和排序了。

### 计数排序

我们先来看一种最朴素也最暴力的实现方式——计数排序，形象来说就是“一个萝卜一个坑”，假设我们要排序的元素是十万个大小范围在【1~30】之间的整型数字，那么我们只需要准备30个桶，每个桶即是一个计数器，用于记录每个数字出现的次数。在遍历整个序列的过程中，遍历到某个数字就将对应桶中的计数器加一，等整个序列遍历结束，我们再根据这三十个桶的计数结果，按照桶的顺序和计数器的个数还原出这十万个数，就形成了有序序列。整个过程的时间复杂度为O(N)，这种排序方式相比于归并排序，快排和堆排序都要更快。

### 基数排序

看了计数排序的算法描述，你可能会对其提出质疑：如果要排序的范围比整个序列的大小还要大呢，岂不是时间和空间上都很浪费？确实，计数排序对数据状况的要求非常严格，很难遇到适用的场景，因此桶排序更为常用的一种实现方式为基数排序。所谓基于词频的排序，他与基于比较的排序最大的不同点在于需要提前去考虑待排序序列的数据状况，合理的将待排序的所有可能情况进行分类，且**分类的方式必须是无交集，无缺失**。分类的结果指明了需要用到的桶的数量，由于桶排序中我们需要遍历所有的桶来获取桶中的信息，所以桶的划分一定要越少越好，桶的数量直接影响算法的时间和空间复杂度。

计数排序中得益于特殊的数据状况，我们按照单个元素划分桶的方式就足以解决问题，但是实际情况下往往待排序序列的变化范围是很大的，有可能整个序列只有几万个元素，但是变化范围可能在正负几亿之间，这时我们就需要对我们的桶进行一个精巧的设计，使得只用有限个桶实现序列的排序，基数排序应运而生。其思想可以描述成**按位多次排序**，即虽然元素的变化范围很大，但是对应到位数上即便是上亿的数也不过9到10位，而每一位是由0到9中某一个数字来确定的，因此我们可以只通过10个桶，按位将序列中的元素放入桶中，此时虽然桶内和桶之间的元素都是无序的，但是某一位的数据却是有序的，我们将按位有序的结果记录下来，进行下一位的排序，依次执行到最后一位，整个序列的元素调整至有序。

> 注：上述中的基数排序同样适用于非十进制数的排序，如二进制，八进制，十六进制等，此时桶的个数也调整为相应的2，8和16。

## 基数排序的实现

计数排序由于只需要在每个桶中记录单个元素的出现次数，所以直接用数组结构实现即可，由于元素的变化范围较小，所以不会出现数组所需空间较多的情况；而基数排序的桶中要保存序列中的元素值，因此其所需要的空间就很难确定，有可能出现空桶，也有可能所有的元素都跑到同一个桶中，因此采用链表结构较好，可以只申请O(N)的空间，而使用数组来放置元素则需要申请O(k*N)的空间才能保证每一次的排序过程都可以放下所有元素，其中k为待排序元素的进制数。

### 案例分析

下面我们通过一个十进制的例子，来对基数排序的过程作进一步分析，考虑代码的设计。

- 待排序序列：996，354，227，56，12，335，443，2，8，2009

第一步：准备10个桶，分别用来放置某一位是0到9的元素

第二步：遍历序列找出最大值，以最大值的位数作为排序的次数，该例中最大数为2009，位数为4

第三步：遍历该序列，根据最低位将元素放入相应的桶中

| 桶号 | 元素1 | 元素2 | 元素3 | ……   |
| ---- | ----- | ----- | ----- | ---- |
| 0    |       |       |       |      |
| 1    |       |       |       |      |
| 2    | 12    | 2     |       |      |
| 3    | 443   |       |       |      |
| 4    | 354   |       |       |      |
| 5    | 335   |       |       |      |
| 6    | 996   | 56    |       |      |
| 7    | 227   |       |       |      |
| 8    | 8     |       |       |      |
| 9    | 2009  |       |       |      |
|      |       |       |       |      |

第四步：将桶中的元素依次重新排成序列

* 新序列（个位）：12，2，443，354，335，996，56，227，8，2009

第五步：重复第三步和第四步，比较的位数依次递增

| 桶号 | 元素1 | 元素2 | 元素3 | ……   |
| ---- | ----- | ----- | ----- | ---- |
| 0    | 2     | 8     | 2009  |      |
| 1    | 12    | 227   |       |      |
| 2    |       |       |       |      |
| 3    | 335   |       |       |      |
| 4    | 443   |       |       |      |
| 5    | 354   | 56    |       |      |
| 6    |       |       |       |      |
| 7    |       |       |       |      |
| 8    |       |       |       |      |
| 9    | 996   |       |       |      |
|      |       |       |       |      |

* 新序列（十位）：2，8，2009，12，227，335，443，354，56，996

| 桶号 | 元素1 | 元素2 | 元素3 | 元素4 | 元素5 |
| ---- | ----- | ----- | ----- | ----- | ----- |
| 0    | 2     | 8     | 2009  | 12    | 56    |
| 1    |       |       |       |       |       |
| 2    | 227   |       |       |       |       |
| 3    | 335   | 354   |       |       |       |
| 4    | 443   |       |       |       |       |
| 5    |       |       |       |       |       |
| 6    |       |       |       |       |       |
| 7    |       |       |       |       |       |
| 8    |       |       |       |       |       |
| 9    | 996   |       |       |       |       |
|      |       |       |       |       |       |

* 新序列（百位）：2，8，2009，12，56，227，335，354，443，996

> 最后一次排序时，除了2009放入2号桶中，其余所有元素放入0号桶

- 新序列（千位）：2，8，12，56，227，335，354，443，996，2009

最终整个序列有序。

### 函数实现

通过案例的排序过程，我们对整个流程有了一个大致的把握，其实基数排序的算法并不难理解，主要的操作也无非是入桶和出桶这两种操作，重点是对整个流程的把握和桶这一结构的设计。

#### 桶结构的设计

数据结构永远是最基础也最重要的一环，需要用到特殊数据结构的算法我们首先要对结构进行合理的设计。基数排序中桶的个数是确定的（0号到9号共10个桶），但桶中所要存储的元素个数是不确定的（可能出现空桶也可能出现一个桶中要存所有元素），所以我们可以将桶的集合用数组结构表示，而每个桶中元素的存储使用链表结构表示，这样设计可以有效节省存储空间。

```java
    /**
     * 桶内链表节点
     */
    public static class Elem {
        int value;
        Elem next;

        public Elem(int value, Elem next) {
            this.value = value;
            this.next = next;
        }
    }
```

如代码所示，我们定义桶中的元素节点结构，包含元素值和指向桶内下一个元素的指针。同时，为了便于我们之后对桶内元素的判断，我们给每一个桶设置一个头结点，头结点中存储无意义的节点值，同时将该桶中的所有元素串起来。桶的初始化代码如下所示：

```java
    /**
     * 初始化各个桶的头结点
     *
     * @param buckets
     */
    public static void initBuckets(Elem[] buckets) {
        for (int i = 0; i < buckets.length; i++) {
            buckets[i] = new Elem(Integer.MIN_VALUE, null);
        }
    }
```

#### 主要操作

前面我们说桶排序的核心操作包括入桶和出桶两项，但就整个流程来说，还包括案例中我们所做的求序列中最大值的位数、桶中元素生成新的序列等操作，下面我们来一一分析。

* 入桶操作：每一个桶都是一个带头节点的链表，元素入桶时我们需要用到相应的元素值和桶指针，通过指针不断向下遍历，将当前元素值链到该桶的最后一个元素之后。代码如下：

```java
    /**
     * 元素入桶
     *
     * @param value
     * @param bucket
     */
    public static void insertBucket(int value, Elem bucket) {
        while (bucket.next != null) {
            bucket = bucket.next;
        }
        bucket.next = new Elem(value, null);
    }
```

* 出桶操作：当我们遍历完一遍元素序列之后，我们需要将桶中的元素重新串成新的序列，为了节省空间可以直接对原序列空间进行覆盖。代码如下：

```java
    /**
     * 将桶中的数据覆盖回原序列
     *
     * @param arr
     * @param buckets
     * @return
     */
    public static void generateNewArray(int[] arr, Elem[] buckets) {
        int index = 0;
        for (int i = 0; i < buckets.length; i++) {
            Elem temp = buckets[i].next;
            while (temp != null) {
                arr[index++] = temp.value;
                temp = temp.next;
            }
        }
    }
```

* 确定需要排序的次数：即求序列中最大值的位数。代码如下：

```java
    /**
     * 求数组中最大元素的位数
     *
     * @param arr
     * @return
     */
    public static int maxIndex(int[] arr) {
        int maxIndex = 0;
        for (int i = 0; i < arr.length; i++) {
            if (arr[i] > arr[maxIndex]) {
                maxIndex = i;
            }
        }
        int temp = arr[maxIndex];
        maxIndex = 1;
        while (temp / 10 != 0) {
            maxIndex++;
            temp /= 10;
        }
        return maxIndex;
    }
```

* 清空桶：当一次排序结束后我们将桶中的数据 写回原序列，之后要将所有桶清空，以便下一次排序时元素入桶。代码如下：

```java
    /**
     * 清空所有桶
     *
     * @param buckets
     */
    public static void cleanBucket(Elem[] buckets) {
        for (int i = 0; i < buckets.length; i++) {
            buckets[i].next = null;
        }
    }
```

* 主函数：外部函数组织了基数排序中所有操作的执行流程，其中关键的步骤在于如何确定当前元素该进入哪个桶，即如何取出元素相应位上的数。代码如下：

```java
    /**
     * 基数排序主函数
     *
     * @param arr
     */
    public static void radix(int[] arr) {
        //1.求最大值元素的位数
        int maxIndex = maxIndex(arr);
        //2.准备桶，初始化
        Elem[] buckets = new Elem[10];
        initBuckets(buckets);
        //3.按位循环，由低到高
        for (int i = 0; i < maxIndex; i++) {
            //4.遍历序列
            for (int j = 0; j < arr.length; j++) {
                //5.元素入桶（注意找桶的逻辑）
                insertBucket(arr[j], buckets[arr[j] / (int) Math.pow(10, i) % 10]);
            }
            //6.生成新的序列
            generateNewArray(arr, buckets);
            //7.清空桶
            cleanBucket(buckets);
        }
    }
```

### 算法分析

#### 稳定性

在学习基于比较的排序算法时，我们说算法的稳定性是看元素的交换过程是否是跨元素的来判断，那么基于词频的排序算法的稳定性又如何来考量呢。通过分析算法的流程我们发现，在基数排序中**相同值的元素必然在每一次排序的过程中都是处于同一个桶之中**的，而元素入桶和出桶的顺序又是有序的，因此**位于前面的元素在入桶时先入桶，出桶时先出桶，**故基数排序是稳定的。

#### 空间复杂度

如果是使用链表结构实现的桶，那么基数排序的过程中只需要动态的申请O(N)的空间来暂存桶中元素的顺序，因此空间复杂度为O(N)

#### 时间复杂度

我们可以抛开求最大值位数、生成序列、清空桶等不构成复杂度最高的操作，只观察主函数中元素入桶的执行流程来推算整个算法的时间复杂度，抛开代码中比较具体的针对十进制数的排序，桶的个数应该是以单个位上的数字范围来确定的，二进制就是两个桶，八进制就是八个桶，十六进制则需要十六个桶，我们可以使算法时间复杂度的估算更具一般性。下面我们先将涉及到复杂度计算的变量抽象出来，有以下几个：

* n：待排序的数据规模，即代码中内层循环的次数——数组arr的个数
* d：待排序的序列最大值的位数，即代码中外层循环的次数——maxIndex
* k：桶的个数，即每一位数字可能出现的个数，代码中十进制的可能性为10

对n和d这两个变量可以通过观察主函数很容易的看出对时间复杂度的影响，但是桶的个数是如何影响时间的呢。其实抽象地看，计数排序之所以只有O(N)的时间复杂度，是因为桶的数量很多，这是一种用空间来换时间的策略，所以桶越多排序的效率一定是越快的。那么具体到代码，我们应该如何定量的衡量呢。这里需要提到我们在设计桶结构时为了节省空间而采用链表结构来实现，这样带来的副作用是我们在元素入桶时需要承受一个查找桶的末尾的时间，因此排序的时间就和待排序的数据状况挂钩，如果每次入桶的数在十个桶中的分配很平均，那么元素入桶的时间就会很短，反之极端情况下所有元素都放进同一个桶中时，元素入桶的时间就是一个递增的数列。公式推导如下：

![bucketSort1](https://shitsurei-pictures.oss-cn-beijing.aliyuncs.com/pics/bucketSort1.jpg)

如图所示，最好的情况下所有元素每一次都在k个桶之内平均分配，则一次排序的入桶过程要花费k次的1到n/k累加和，总共排序d次，时间复杂度为O(N<sup>2</sup>)；最差的情况下所有元素在每次排序时都进同一个桶（即排序的元素值都相同），则一次排序的入桶过程要花费k次的1到n累加和，总共排序d次，时间复杂度为O(N<sup>2</sup>)。最好和最差的时间复杂度虽然相差k<sup>2</sup>倍，但都是O(N<sup>2</sup>)级别，似乎和我们在其他地方看到的结论有些出入，这是什么原因呢。其实，这又是时间和空间上的一次取舍，还记得我们在设计桶的物理结构时，为了节省空间而使用链式结构作为每个桶中存储元素的方式吗，这样使得我们在元素入桶的时候，因为不知道链的长度而不得不承受一个遍历链表查找链尾的时间，增加了时间复杂度。

显然桶排序作为一种对数据状况有一定限制的排序算法，低时间复杂度是其和其他排序算法相“抗衡”的优势，如果时间复杂度为O(N<sup>2</sup>)，那么该算法就变得异常鸡肋，我们必须想办法降低时间复杂度。很容易想到的一种策略就是改变桶的物理结构，使用数组替换链表，数组对任意位置的元素可以实现O(1)时间的读取和设置，这样每个元素入桶的时间就是相同的，所有元素入桶的时间复杂度为O(N)，**且此时的时间复杂度不存在最好和最差情况**，整个算法的时间复杂度为d\*k\*N。如果是十进制元素浮动范围在亿左右的情况，常数项一般会在100左右，虽然很大，但是仍然不妨碍这个算法的时间复杂度被控制在O(N)的规模上，一旦待排序元素的规模变大，该算法在时间性能上的优势就会体现出来。就如同之前说的，时间和空间的取舍我们一旦选择了时间，空间上的弊端就暴露了出来，链表结构不存在空间上的最好最快情况，数组结构则不存在时间上的最好最坏情况，但空间上则存在。如果对多个相同元素进行排序，那么每一次入桶时所有元素就要进入同一个桶之中，这样就要求每一个桶的空间至少可以容乃所有元素，那么整个算法的空间复杂度为k\*N，也算保持在O(N)规模，但k倍的空间复杂度意味着十万个待排序元素需要申请百万的空间来辅助，对于存储资源紧张的情况也是不能接受的。

#### 基数排序优化

在我们对基数排序的时间和空间进行权衡之后，发现不论是选择时间还是空间，另一方的成本都使得该算法的性价比降得很低，那么是否我们可以想办法来对该算法进行优化，从而得出一个两全其美的策略呢。其实是有的，我们可以从两方面来考虑，如果选择时间放弃空间，那么k\*N的空间是很难节省的，因为你无法确定待排序的数据情况，一旦出现单个桶中空间不足的情况就会出现内存溢出的问题；那么如果从空间入手，时间成本上我们可以考虑如何节省遍历链表的时间，由于元素入桶时我们往往只关心桶底的元素，我们只是希望将新入桶的元素挂在桶底，而非查找桶中的某个元素，因此每一次对桶内链表的遍历其实是没有必要的，我们完全可以使用一个指向桶底元素的指针来记录每次新元素要入桶的位置，这样每次元素入桶的时间就从O(N)降到了O(1)，并且我们只是增加了k个（桶的个数个）指针的有限空间就解决了这个问题。如此一来，整个算法的时间复杂度就变成了d\*k\*N，空间复杂度变成了k+N，时间空间复杂度同时控制在了O(N)级别，经过一系列优化该算法的竞争力也开始体现出来，同时由于入桶的时间和入桶的数据状况没有关系，因此不存在最好和最坏的时间复杂度。

下面我们来看一下原版代码需要改动的地方，首先就是数据结构的改动，为了主函数中的代码更加简洁，逻辑层次更分明，我们对桶集合进行了更高一级的封装，将每个桶的头和尾指针都封装在了一个桶集合对象中（当然你也可以用两个元素节点数组表示桶集合，那样主函数也要做相应的调整），头指针集合用于桶的遍历并覆写回原数组，尾指针集合用于元素入桶时记录位置，初始化时头尾指针都指向链表的头结点。代码如下：

```java
    /**
     * 桶集合
     */
    public static class Buckets {
        Elem[] heads;
        Elem[] tails;

        public Buckets(int system){
            this.heads = new Elem[system];
            this.tails = new Elem[system];
            for (int i = 0; i < heads.length; i++) {
                heads[i] = new Elem(Integer.MIN_VALUE, null);
                tails[i] = heads[i];
            }
        }
    }
```

其次是元素入桶的过程，从原来的遍历入桶变成尾指针的后移完成，这里是整个算法时间复杂度降低的核心。代码如下：

```java
    /**
     * 元素入桶
     *
     * @param value
     * @param buckets
     * @param index
     */
    public static void insertBucket(int value, Buckets buckets, int index) {
        buckets.tails[index].next = new Elem(value, null);
        buckets.tails[index] = buckets.tails[index].next;
    }
```

再次是覆写完成后清空桶的操作，新版本中需要同时将头尾指针的位置还原到初始化状态。代码如下：

```java
    /**
     * 清空所有桶
     *
     * @param heads
     */
    public static void cleanBucket(Elem[] heads, Elem[] tail) {
        for (int i = 0; i < heads.length; i++) {
            heads[i].next = null;
            tail[i] = heads[i];

        }
    }
```

最后是主函数中对应的修改，相关方法的参数做了修改，故相应的方法调用传参也要发生变化，最主要的是入桶时要将桶的编号单独作为参数传入。代码如下：

```java
    /**
     * 基数排序主函数
     *
     * @param arr
     */
    public static void radix(int[] arr) {
        //1.求最大值元素的位数
        int maxIndex = maxIndex(arr);
        //2.准备桶，初始化
        Buckets buckets = new Buckets(10);
        //3.按位循环，由低到高
        for (int i = 0; i < maxIndex; i++) {
            //4.遍历序列
            for (int j = 0; j < arr.length; j++) {
                //5.元素入桶（注意找桶的逻辑）
                insertBucket(arr[j], buckets, arr[j] / (int) Math.pow(10, i) % 10 );
            }
            //6.生成新的序列
            generateNewArray(arr, buckets.heads);
            //7.清空桶
            cleanBucket(buckets.heads,buckets.tails);
        }

    }
```

## 桶的使用

在我们介绍完桶排序的两种具体实现之后我们再来看看桶这一数据结构，由于桶排序是不基于比较的排序方法，因此其使用场景是受限的，对待排序集合的数据状况有一定要求（这一点后文还会再展开），因此其使用的优先程度并不高，工程领域的重要程度也不及快排和归并排序，我们更应该把关注的重点放到桶这一数据结构上来。相信经过上面的介绍，我们对桶已经有了一定的认识，桶其实就是一个分类的容器，我们想要对数据做精细的处理时，避免不了对给定集合中数据的分类，而分类后数据暂存的容器我们都可以将其称之为桶，它的实现可以是数组，链表，二叉树（堆）等等。下面我们通过一道算法题来感受一下桶的妙用。

> 题目： 给定一个数组， 求如果排序之后， 相邻两数的最大差值， 要求时间复杂度O(N)， 且要求不能用非基于比较的排序。 
>
> 案例：输入【1，2，6，3，7】；输出 3

* 分析：题目中明确要求了不能用非基于比较的排序，意味着我们不能通过计数排序或基数排序算法先将整个序列变有序之后再从头到尾遍历得到最大差值，而就我们已知的基于比较的排序算法中，也没有时间复杂度可以达到O(N)级别的排序算法，因此这道题对我们的要求就是在整个序列未完成排序的情况下求出排序后相邻两数的最大差值。
* 思路：虽然我们无法使用桶排序算法，但我们依然可以借助桶这一概念，正如前文提到的，桶的作用就是一个分类器，我们把数组中有相同特征的元素放进相同的桶中，通过桶之间的逻辑关系来求解最终的答案。这样说有些抽象，以上文中提到过的基数排序为例，我们想要得到的是元素之间的顺序关系，那么桶的设计就要体验出数字之间的顺序，这样当某一位上相同数字的元素放入同一个桶中之后，本轮元素的顺序关系就可以得到。回到这道题中，我们希望得到元素之间的差值关系，那么可以尝试构造出桶与桶之间的差值关系，将差距不大的元素放入同一个桶中，这样我们就不需要逐个比较元素之间的差值，毕竟这样做有意义的前提是我们需要将数组排序，下面来看我们的方案。
* 方案：遍历找出N个元素中的最大值和最小值，将最大值和最小值之间的范围等分N+1份；构造N+1个桶用来将大小在范围内的元素放入相应的桶中，每个桶记录入桶的元素最大值和最小值；遍历数组，元素入桶；遍历每个桶，找出桶与桶之间的最大值和最小值的最大差值即为所求结果。
* 证明：这里我们来证明一下这个方案的可行性，首先N个元素放入N+1个桶中，至少有一个空桶。其次我们是以数组中的最大值和最小值作为范围进行桶的划分的，因此第一个桶和最后一个桶必不为空桶，分别至少有最小值和最大值一个元素，故空桶一定位于中间的某个桶中。下面我们只需要证明序列的**最大差值**不出现在桶内元素之间而是**出现在跨桶元素之间**即可，这里可以使用**反证法**来说明（具体公式证明过程略）。假设整个序列的最大差值出现在某一个桶中，记为maxGap，桶的范围记为R，则必然有maxGap≤R，又已知第0号桶到第N号桶之间必然存在至少一个空桶，记为E号桶。此时E-1号桶和E+1号桶可能为空也可能有元素，当这两个桶中有元素，且E-1号桶中的最大元素（记为max<sup>E-1</sup>）为该桶范围的最大值，E+1号桶中的最小元素（记为min<sup>E+1</sup>）为该桶范围的最小值时，max<sup>E-1</sup>和min<sup>E+1</sup>之间的差值最小，为R+2＞R=maxGap，因此必然存在一个差值大于我们假设的桶内最大差值，故假设不成立，最大差值来自桶与桶之间，证明完毕。
* 注意：有同学可能会有些疑惑方案中为什么要逐个遍历桶与桶之间的差值关系，而不是直接使用空桶两边的差值最为最终答案。诚如证明中所论证的，我们设计桶分类元素求最大差值只是想要确保元素之间的最大差值并非来源于桶内元素，并且我们也只能证明这一点，而无法证明最大差值一定来自于空桶两侧的元素。这里我们可以借用证明中所用到的max<sup>E-1</sup>和min<sup>E+1</sup>这一特殊情况来举一个反例说明这一点，假设空桶两侧正好存在这两个元素，而在E+2号桶中存在唯一一个元素正好是该桶范围的最大值，这里记为max<sup>E+2</sup>，此时E号桶空桶两侧的最大差值为R+2，而非空桶E+1和E+2之间的最大差值约为2R，当R＞2时即最大差值不来自空桶两侧，这样的反例非常容易构造，大家可以尝试验证一下。

下面我们来看代码，有了上面的分析我们的思路就比较清晰了，可以将整个过程分为以下几个部分：

1. 遍历数组，求最大最小值
2. 设计桶结构并对其初始化
3. 计算每个元素所对应的桶，并将元素入桶
4. 遍历桶找出最大差值

```java
/**
     * 求数组中的最大值和最小值
     *
     * @param arr
     * @return
     */
    public static int[] absoluteValue(int[] arr) {
        if (arr == null || arr.length < 2)
            return new int[]{0, 0};
        int max = Integer.MIN_VALUE, min = Integer.MAX_VALUE;
        for (int i = 0; i < arr.length; i++) {
            max = Math.max(max, arr[i]);
            min = Math.min(min, arr[i]);
        }
        return new int[]{max, min};
    }
```

```java
/**
     * 确定该数应该放入哪个桶
     * 这里进行了一个简单的数值计算，注意计算结果需要保证边界值不重复不遗漏
     * @param num
     * @param len
     * @param max
     * @param min
     * @return
     */
    public static int determainIndex(int num, int len, int max, int min) {
        return (int) ((num - min) * len / (max - min));
    }
```

```java
/**
     * @Description 桶结构设计
     * 其中布尔变量用于记录桶内是否进入过元素，不必须但增加该变量可以提高代码的可读性
     */
    public static class Bucket {
        int maxValue;
        int minValue;
        boolean enter;
    }

    /**
     * @param buckets
     * @Description 初始化桶集合
     */
    public static void initBucket(Bucket[] buckets) {
        for (int i = 0; i < buckets.length; i++) {
            buckets[i] = new Bucket();
        }
    }
```

```java
/**
     * @Description 元素入桶
     * @param bucket
     * @param elemValue
     */
    public static void insertBucket(Bucket bucket, int elemValue) {
        if (bucket.enter) {
            bucket.minValue = Math.min(bucket.minValue, elemValue);
            bucket.maxValue = Math.max(bucket.maxValue, elemValue);
        } else {
            bucket.enter = true;
            bucket.maxValue = elemValue;
            bucket.minValue = elemValue;
        }
    }
```

```java
/**
     * @Description 遍历桶集合寻找最大差值
     * @param buckets
     * @return
     */
    public static int calMaxMinus(Bucket[] buckets) {
        int max = Integer.MIN_VALUE;
        int temp = buckets[0].maxValue;
        for (int i = 1; i < buckets.length; i++) {
            if (buckets[i].enter) {
                max = Math.max(max, buckets[i].minValue - temp);
                temp = buckets[i].maxValue;
            }
        }
        return max;
    }
```

```java
/**
     * @param arr
     * @return
     * @Description 主函数
     */
    public static int maxMinus(int[] arr) {
        if (arr == null || arr.length < 2) {
            return -1;
        }
        int length = arr.length;
        Bucket[] buckets = new Bucket[length + 1];
        initBucket(buckets);
        int maxMin[] = maxMinElem(arr);
        int max = maxMin[0];
        int min = maxMin[1];
        if (max == min) {
            return 0;
        }
        for (int i = 0; i < arr.length; i++) {
            int index = determainIndex(arr[i], length, min, max);
            insertBucket(buckets[index], arr[i]);
        }
        int maxMinus = calMaxMinus(buckets);
        return maxMinus;
    }
```



## 桶排序的局限性

在实际的工程领域中，桶排序的适用范围相较于其他基于比较的排序算法而言是具有局限性的，为什么这么说。一个基于比较的排序算法，我们所要具备的基础仅仅是元素之间比较大小的标准，知道了如何比较元素之间的大小我们就可以实现该比较方法的比较器函数，即便是面对未知的数据状况我们也可以通过比较算法得到最终有序结果；而桶排序则不然，基于词频统计的排序我们既要直到元素之间的大小顺序，还要对数据状况有一定的了解，如果待排序的数据范围过大又不太适合使用桶排序，即便是基数排序这样精致的设计桶的使用，也付出了一定的空间代价，这也印证了我们在学习桶排序时发现虽然这个算法很好用，省时省力但它的算法地位相对于快排的地位没有那么高，因为他总与数据状况有关，总有瓶颈。

<hr />
