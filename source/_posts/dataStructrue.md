---
copyright: true
sticky: false
permalink: dataStructrue
tags: [算法,数据结构]
date: 2020-01-30 11:53:28
title: 数据结构概述
categories: 算法和数据结构
image: https://shitsurei-pictures.oss-cn-beijing.aliyuncs.com/pics/dataStructrue.jpg
---

计算机领域许多概念都是生活中实际问题的抽象表达，因为这一学科在诞生时本身就具有很强的工程倾向，其目的就是高效的解决现实中的许多问题，在数据结构这一部分尤为明显。例如我们在生活中常常面临的物品堆积问题和排队问题就分别可以抽象成栈和队列这两种数据结构，由于实际情况中这些问题非常普遍也使得这两种数据结构成为工程实践过程中最常用的两种数据结构，这也是我们在学习算法和数据结构时最先接触这两种数据结构的原因。那么究竟什么是数据结构呢，今天我们就来一探究竟。

<!-- more -->

## 所谓“结构”

我们在学习数据结构之前首先要对结构的划分有一个清晰的认识，否则后面对一些名词的分类和含义在理解上可能会出现一些偏差，例如线性表和链表是同一个结构吗？如果是包含关系，两者谁的范围又更广呢？要搞清楚这些问题，我们先得明确两个名词——“物理结构”和“逻辑结构”。

### 物理结构

我们知道，计算机存储数据是通过一个个顺序排列的存储单元来实现的，每个存储单元只能通过高低电平来表示0、1两个值，那么不论我们要存储多么复杂的结构，实际上也是通过一个个存储单元的序列来表示的，我们把数据在存储单元中的存储方式叫做“物理结构”。有同学可能会不理解，如果存储序列是像数组一样一个接一个的并列着，那么不是只能像数组一样按顺序一个个存储吗，还能有别的存储方法吗。其实除了这样的**顺序存储**方式，还有一种存储方式叫做**链式存储**，指的是前一个元素中包含下一个元素的起始地址，就像火车的车厢一样前一节的尾部挂着后一节的头部，也可以理解为排队过程中每个人只要记得自己前面的人是谁就不怕下一次无法还原这个排序（当然第二个例子不太恰当，因为链式存储中一般都是前一个元素记住后一个元素是谁）。

从上面的介绍我们可以推断出两种物理结构各自的存储特点了，在前面讲算法的时间复杂度时我们曾提过，获取数组元素的时间复杂度为O(1)，因为数组在内存空间上就是顺序存储的物理结构，CPU可以通过简单地数值计算进行内存寻址，因此顺序存储的优点在于快速读取，不论是获取序列中的第几个元素，其时间复杂度都是相同的。但是顺序存储的数据是不便于扩展的，因为你无法确定待扩展的下一个内存地址是否被占用，如果占用的话，将扩展的元素放到下一个空闲的位置又无法保证每个元素的连续性，这样只能开辟一块更大的空闲内存地址范围，将原来的序列复制到新申请的位置，继而存放新的元素，如果序列的扩展操作频繁，这种方式必然是低效的，根源就是**无法解决内存空间的碎片化**（注意此处的无法解决指无法在较小的成本下解决该问题）。

而链式存储的优缺点和顺序存储正好相反，链式存储不要求元素之间的位置连续，那么就便于我们对序列进行扩展和删减，避免了内存空间碎片化所造成的问题。但是由于整个数据的序列关系保存在每个元素中，所以我们获取某个元素时必须要遍历整个序列，时间为O(N)，这样就不适用于需要频繁读取的操作，时间成本较高。另外由于链式存储中前一个元素要携带下一个元素的地址信息，因此相比顺序存储还需申请额外的内存空间。

### 逻辑结构

了解了物理结构，我们就知道数据怎么在计算机中存储了，你可能会疑惑那为什么还要有逻辑结构呢。因为人毕竟不是计算机，两者的思考方式和擅长的方面完全不同，计算机可以很快的计算出两个十位数相乘的结果，却难以识别一只猫，人则刚好相反，所以逻辑结构通俗的理解就是站在人的角度去组织数据，使用数据的分类方式。逻辑结构由简到繁可以大致划分为四类——集合、线性表、树和图，虽然每种结构有一些概念层面的定义，但是还是很容易让人联想到现实生活中的事物，这里我们先按下不表，后面会对每种结构进行展开。

这里我们需要着重说明的是**物理结构和逻辑结构之间没有严格的对应关系**，每种逻辑结构既可以用顺序存储来实现，也可以用链式存储实现，**比较复杂的逻辑结构还需要两种存储方式结合起来实现**。在**逻辑结构层面我们更加关心的是这个结构需要实现哪些功能**，而在**物理层面我们则是考虑如何通过封装来实现所需功能**，这样解释可能有些同学还是觉得比较抽象和陌生，不知道该如何来操作，那是因为在我们学习编程语言的过程中，不论你是C语言，C++，C#，Java还是Python等等，语言的设计者已经对许多逻辑结构和功能进行了封装，我们最开始接触的数组结构其实就是顺序存储的线性结构的简单实现，而链式存储则在不同语言中有不同的实现，例如C语言等中的指针，Java中类的定义和引用。毕竟语言的发展使得我们越来越少与操作系统和硬件打交道，许多逻辑结构已经被语言自带的库封装的很好用了，我们只需要调用接口记忆可以实现其功能，但是学习这些结构的内部功能和实现也是很有必要的，如果一个东西对我们而言只是一个黑盒，那我们就无法将其用到极致。

## 集合

这里我们以最简单的集合结构来举例，集合的定义很简单，就是元素之前没有任何关系，既没有顺序先后，也没有连接所属关系，仅仅是位于一个范围中，有些结构的设计中集合中的元素是不可以重复的，这种限制使得集合更偏向于数学中的概念，我们这里也按照这种设定去实现。集合的操作一般可以分为以下几个，同时为每个功能命名：

1. 创建集合（构造方法）
2. 计算集合大小（size）
3. 判断集合是否为空（isEmpty）
4. 向集合中添加元素（push）
5. 从集合中随机取出一个元素（pop）

<div class="note warning"><p>由于我们使用Java进行代码的实现，语言特性中的自动垃圾回收机制使得我们不用考虑对申请的内存空间进行手动释放，如果是使用C语言实现则需要考虑封装析构方法</p></div>
### 顺序存储实现

首先，我们采用顺序存储的方式来实现集合结构，顺序存储的局限在于集合的容量会有上限，因此我们需要考虑在集合中加入元素时处理扩展集合容量的问题；另一个问题是从集合中获取元素和加入元素时由于集合是无序的，需要保证随机性，又由于顺序存储时元素之间是相互挨着的，所以我们可以通过随机数人为地打乱插入和取出的次序，以此来模拟集合的无序性。但是这样一来又会随之伴生两个问题，一是随着插入的元素越来越多，整个内存空间上可以被用来放置新元素的位置越来越少，在还没有对集合进行扩容之前，我们随机选择的插入位置已经被占用的概率越来越高；二是相对的在集合中没有多少元素的情况下，我们随机从集合中取出一个元素时，随机选择的位置没有元素的概率也会越来越高。其中第一个问题类似于我们在解决哈希冲突时所遇到的问题，解决这个问题最简单的方法就是从当前位置依次往下寻找下一个空位放进去，这样解决相比重新随机位置要高效很多，但是随机性就会差一些，因为寻找空闲位置总是沿着相同的方向和步长，这里我们就要加入一些随机条件来稀释其规律性，增加随机性。我们将整个集合结构封装成一个SeqCollection类，下面我们来看一下代码：

<div class="note warning"><p>出于简化代码逻辑，避免过多与核心功能无关的代码，我们将元素类型设定为整型数字</p></div>
* 首先是类的五个内部成员变量：

```java
//当前集合的容量上限
private int ceiling = 5;
//用于增加解决地址冲突时寻址的随机性设置方向标识
private boolean flag = true;
//用于存储集合中的元素
private int value[];
//用于保存数组各个位置是否存放了元素
private boolean occupy[];
//当前集合中元素的数目
private int size;
```

* 其次是类的构造函数和初始化方法，我们提供可用于设定初始集合容量上限的构造方法，从而在使用者对集合大小有一定估计的情况下节省空间和扩容成本：

```java
public SeqCollection() {
    init();
}

public SeqCollection(int ceiling) {
    this.ceiling = ceiling;
    init();
}

/**
 * 初始化方法
 */
private void init() {
    value = new int[ceiling];
    occupy = new boolean[ceiling];
    size = 0;
}
```

* 接下来是四个内部的私有方法，虽然不直接作为接口对外访问，但却是集合结构的核心逻辑代码，这四个函数辅助实现了集合的功能接口

```java
	/**
     * @Description:集合扩容方法，每次扩容至当前容量的两倍
     */
private void expansion() {
    int new_value[] = new int[size * 2];
    boolean new_occupy[] = new boolean[size * 2];
    for (int i = 0; i < size; i++) {
        new_value[i] = value[i];
        new_occupy[i] = true;
    }
    value = new_value;
    occupy = new_occupy;
    ceiling = size * 2;
}

	/**
     * @Description:随机获取一个可插入元素的索引
     *
     * @return
     */
private int pushIndex() {
    int index = (int) (Math.random() * ceiling);
    if (occupy[index]) {
        //设置flag变量用于出现地址冲突时寻址的方向随机性
        if (flag) {
            while (occupy[index]) {
                index++;
                if (index == ceiling)
                    index = 0;
            }
        } else {
            while (occupy[index]) {
                index--;
                if (index == -1)
                    index = ceiling - 1;
            }
        }
        flag = !flag;
    }
    return index;
}

	/**
     * @Description:随机获取一个已插入元素的索引
     *
     * @return
     */
private int popIndex() {
    int index = (int) (Math.random() * ceiling);
    if (!occupy[index]) {
        if (flag) {
            while (!occupy[index]) {
                index++;
                if (index == ceiling)
                    index = 0;
            }
        } else {
            while (!occupy[index]) {
                index--;
                if (index == -1)
                    index = ceiling - 1;
            }
        }
        flag = !flag;
    }
    return index;
}

	/**
     * @Description:判断某个元素是否已经存在于集合中，该方法用于排除元素重复的情况
     *
     * @param elem
     * @return
     */
private boolean exist(int elem) {
    boolean exist = false;
    for (int i = 0; i < ceiling; i++) {
        if (value[i] == elem && occupy[i]) {
            exist = true;
            break;
        }
    }
    return exist;
}

```

* 最后是对外提供的接口方法：

```java
	/**
     * @Description:获取集合内元素个数
     *
     * @return
     */
public int size() {
    return size;
}

	/**
     * @Description:判断集合是否为空
     *
     * @return
     */
public boolean isEmpty() {
    return size == 0;
}

	/**
     * @Description:向集合中添加元素，添加成功返回true，该元素已存在返回false
     *
     * @param elem
     * @return
     */
public boolean push(int elem) {
    //先判断要插入的元素是否已经存在
    if (!exist(elem)) {
        //如果集合中的元素已达上限先进行扩容
        if (size == ceiling)
            expansion();
        int index = pushIndex();
        value[index] = elem;
        occupy[index] = true;
        size++;
        return true;
    }
    return false;
}

	/**
     * @Description:从集合中随机拿出一个元素
     *
     * @return
     */
public int pop() {
    try {
        //集合为空时报错
        if (isEmpty())
            throw new Exception("collection empty , pop failed");
        int index = popIndex();
        occupy[index] = false;
        size--;
        return value[index];
    } catch (Exception e) {
        e.printStackTrace();
        System.exit(0);
    }
    return 0;
}
```

### 链式存储实现

接下来我们采用链式存储的方式实现集合结构，相比于顺序存储链式存储其实更适合来实现集合结构，因为顺序存储先天就带有的位置优势在集合结构中不太用的上，同时集合结构的重要操作就是放入元素和取出元素，也就是说在物理层面上的修改数据操作较多，这点也更适合链式存储来实现。因此我们唯一要解决的问题是保证集合中放入元素和取出元素的随机性，同时由于元素之间不相邻，也不用处理索引冲突的问题。下面我们来看ChaCollection类的代码：

* 首先是集合类的成员变量和节点类Node，成员变量只包含一个头节点head，这样的好处在于头结点的值可以用来保存着整个集合元素的数量，因此整个集合结构就是一个带头节点的单链表

```java
//头结点
private Node head;
/**
 * @Description:内部类
 */
class Node {
    //存储下一个元素节点的位置
    public Node next;
    //存储当前元素的值
    public int value;

    public Node(Node next, int value) {
        this.next = next;
        this.value = value;
    }
}
```

其次是类的构造函数，链式存储的构造函数实现起来十分简单，只需要初始化头结点即可

```java
public ChaCollection() {
        head = new Node(null, 0);
    }
```

接下来是一个内部的私有方法，判断是否元素以及存在集合中，这也是链式存储实现起来方便之处

```java
/**
     * @param elem
     * @return
     * @Description:判断某个元素是否已经存在于集合中
     */
    private boolean exist(int elem) {
        boolean exist = false;
        if (!isEmpty()) {
            Node p;
            p = head.next;
            do {
                if (p.value == elem) {
                    exist = true;
                    break;
                }
                p = p.next;
            } while (p != null);
        }
        return exist;
    }
```

最后是对外提供的接口方法：

```java
/**
 * @return
 * @Description:获取集合内元素个数
 */
public int size() {
    return head.value;
}

/**
 * @return
 * @Description:判断集合是否为空
 */
public boolean isEmpty() {
    return head.next == null;
}

/**
 * @param elem
 * @return
 * @Description:向集合中添加元素，添加成功返回true，该元素已存在返回false
 */
public boolean push(int elem) {
    if (exist(elem)) {
        return false;
    } else {
        //随机选择位置放入元素
        int pushIndex = (int) (Math.random() * head.value);
        Node p = head, q;
        while (pushIndex-- > 0)
            p = p.next;
        q = new Node(null, elem);
        q.next = p.next;
        p.next = q;
        head.value++;
        return true;
    }
}

/**
 * @return
 * @Description:从集合中随机拿出一个元素
 */
public int pop() {
    try {
        if (isEmpty())
            throw new Exception("collection empty , pop failed");
        int popIndex = (int) (Math.random() * head.value);
        Node p = head, q;
        while (popIndex-- > 0)
            p = p.next;
        q = p.next;
        p.next = p.next.next;
        head.value--;
        return q.value;
    } catch (Exception e) {
        e.printStackTrace();
        System.exit(0);
    }
    return 0;
}
```

### 测试

这里我们也给出两种实现结构的测试代码与测试结果，验证代码的正确性和获取数据的随机性。测试代码如下：

```java
public static void main(String[] args) {
    System.out.println("顺序存储结果集：");
    SeqCollection seqCollection = new SeqCollection();
    for (int j = 0; j < 10; j++) {
        for (int i = 0; i < 10; i++) {
            seqCollection.push(i);
        }
        for (int i = 0; i < 10; i++) {
            System.out.print(seqCollection.pop() + " ");
        }
        System.out.println();
    }
    System.out.println("链式存储结果集：");
    ChaCollection chaCollection = new ChaCollection();
    for (int j = 0; j < 10; j++) {
        for (int i = 0; i < 10; i++) {
            chaCollection.push(i);
        }
        for (int i = 0; i < 10; i++) {
            System.out.print(chaCollection.pop() + " ");
        }
        System.out.println();
    }

}
```

验证结果如下：

```
顺序存储结果集：
2 0 5 3 6 1 7 4 8 9 
9 7 2 1 4 5 3 6 0 8 
3 1 8 6 9 4 2 7 0 5 
1 5 2 6 8 9 7 4 0 3 
9 7 1 0 6 2 8 5 4 3 
1 7 0 6 5 3 2 4 8 9 
3 7 0 2 6 8 1 5 9 4 
3 0 6 8 4 9 1 7 5 2 
6 7 5 8 2 1 0 4 9 3 
1 0 5 7 9 8 4 3 2 6 
链式存储结果集：
0 9 7 4 5 1 6 3 2 8 
5 0 7 6 8 4 2 3 9 1 
8 4 6 3 5 2 7 1 9 0 
1 6 0 2 9 4 5 3 8 7 
9 8 4 0 7 1 3 6 2 5 
9 8 2 4 5 3 0 1 7 6 
5 1 6 7 8 4 0 9 3 2 
2 1 4 5 7 9 0 8 6 3 
1 0 4 7 8 5 3 6 9 2 
0 2 3 6 1 4 8 9 7 5
```

<hr />
