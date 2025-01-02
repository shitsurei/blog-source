---
copyright: true
sticky: false
permalink: algorithmTools
tags: [算法,数据结构]
date: 2019-05-07 15:43:19
title: 对数器和比较器的应用
categories: 算法和数据结构
description: 算法和数据结构学习过程中的小工具
image: https://shitsurei-pictures.oss-cn-beijing.aliyuncs.com/pics/timg.jpg
---

在学习数据结构和算法的过程中，我们常常会遇到这样的问题：看到或想出某个时间复杂度或空间复杂度较低的算法，想要验证这个算法是否正确，但一时无法在OJ系统中找到相同的题目，只是有限的几个样本数据很难确定算法是否真的能AC，这时我们就可以借助对数器这一强有力的工具来帮助我们解决。

<!-- more -->

<div class="note info"><p>以下学习笔记内容由java语言编写。</p></div>

## 六步实现对数器

什么是对数器，简单来说就是验证你的算法是否“完全正确”的工具，听起来很高大上，实现起来却很简单，只需要简简单单六个步骤

### 1.有一个你想要测试的方法a

这个方法可能来源于你在面对一道算法题时冥思苦想得出的一个思路，虽然看起来没什么问题，但是面对大样本的输入时不敢保证每一个结果都正确；又或者来源于你看到某篇帖子或论坛上某个大神的迷之脑回路的一个解法，想要验证这个解法是否正确。总之，这个方法你理解也好不理解也罢，目前为止还没有什么有效结果来支持这一算法的正确性。

### 2.实现一个绝对正确的方法b

面对任何一道算法题，它的解一定不是唯一的，例如一个简单的排序，我们也学过冒泡，插入，选择，快排，希尔，桶排，堆排等等十几种排序方法。但是，解和解之间是有差别的，最终的差别往往体现在算法的时间复杂度和空间复杂度上，面对同一道题目，我们既可以采用最直接最简单也最暴力的方法去计算，也可以写出优雅高效的算法，这也是衡量一个程序员代码水平的重要指标。
	
对于大多数算法题目来说，如果你给出的解法在时间复杂度上超过O(N<sup>2</sup>)时，这个算法往往是没有意义的，因为这样的时间复杂度在工程领域根本不具备可操作性；但是，这样的方法往往是我们解决一个问题的第一思路，这个思路最符合人的思维逻辑（往往具备线性，全覆盖性等特点），对于这一算法的正确性也最有把握。因此，用这样的绝对正确但时间复杂度和空间复杂度又差的方法产生的结果作为参照依据是对数器的核心思路。

### 3.实现一个随机样本产生器

有了算法，这时应该考虑测试数据从哪来，在我们一开始得到方法a时，必然也会用有限的几个样本数据对算法的准确性做初步的测试，三五个正确的输入输出之后似乎算法没有什么问题，但要做进一步大样本的测试又不是人力所能及的，这时我们就要通过语言所提供给我们的一些工具来构造大样本的富有随机性的输入输出了。构造随机样本产生器，我们主要要考虑以下三个问题：
	
1. **输入数据的主要类型是什么？**
	
	可能你会认为，算法题目有那么多，几乎每一道题要求的输入数据都不相同，难道要每遇到一道题，就去手动构造一个对数器？其实不然，有一定数据结构与算法基础的同学都知道，基本的数据类型只有四种：集合结构，线性结构，树形结构，网状结构（也成图）。这四种属于逻辑结构，即人为区分出来的结构，对应到计算机内存中，每种逻辑结构又可以通过顺序存储和链式存储两种物理结构来表示（两种物理结构各有利弊，一般根据对数据常用的操作来决定选择哪种），因此通常你会遇到的主要数据类型不会超过十种，如果你能对每一种类型的构造方法都进行一定可拓展性的补充，就足以应对大部分的算法题目。

2. **输入数据的数据状况如何？**

	由于算法题目的要求往往具备一定的实际意义，因此其输入数据的数据状况也有相应的要求，以我们最常见的整型为例，如果题目的输入数据为年龄，则不应该出现负数或太大的数字，如果输入数据为星期数，则只能是1到7这七个整型数字之一，这就要求我们在构造随机样本生成器时，对随机产生的数据做一定处理，保证其输入的元素均在题目要求的合理范围内，避免进行无意义的计算。
	
3. **边界值的考虑**

	有一定开发经验的朋友一定深有体会，写代码过程中80%的时间都是在对边界值做处理，保证代码的健壮性（任何合法或不合法的输入输出值都不会使得程序崩溃），在做算法题目时也是如此，题目所给出的样本值往往是有限个合法或某一种不合法的输入情况，而在其他不合法的输入情况下，题目也没有明确说明不会有该情况发生，这时就需要我们全方位的考虑输入值在各种情况下如何得到合理的输出，尤其是对边界值的处理。这里的边界值包括0值的输入、空值的输入或空字符串的输入等，以及相关变量初始化和终止条件的控制。

<div class="note info"><p>后两点最好在随机样本产生器的构造函数中就留好接口，减少后期对构造函数反复修改的成本。</p></div>

### 4.把方法a和方法b比对很多次来验证方法a是否正确

有了随机样本生成器之后，我们便可以对样本进行大规模测试，将两个方法每一次计算得到的结果进行对比，判断这两次计算得到的结果是否完全相等，这里依然需要注意几个问题：

1. **样本数量如何控制？**

	虽然在这一步我们意图进行大规模的样本测试，但是也很难对工程级的数据量进行测试，原因显而易见，想要比对两次计算所得结果，首先就要两个算法分别跑一遍之后才能进行比较，故整个流程是串行的，其时间复杂度为两个算法的时间复杂度之和。从数量级来看整个比较过程的时间复杂度和算法b的时间复杂度相同，如果算法b对工程级的数据量可以接受，那么算法a的时间复杂度的优势意义没有了，因此我们依然是对有限的数据量进行测试。

	那么既不能太多——时间耗不起，又不能太少——样本覆盖率太小，因此通过经验分析，我们认为在测试数量达到数十万次以上是一个比较合理的范围；这也需要根据数据类型和数据情况做相应的调整，例如输入样本为整型数组，数组的长度和取值范围也是考虑因素，如果数组本身已经很大，一次计算所要花费的时间就很大，那么数十万次的测试时间也是难以接受的。这里笔者推荐大家在计算机当前所能承受的计算程度上，逐级增加数据量，从而逼近当前计算能力所能逼近的数据量上限。

2. **如何对输出结果进行比较？**

	在得到两个计算结果之后，我们需要对两者进行比较，这一步我们需要创建比较函数。事实上，这一步通常比大家想的要难的多，虽然也有些题目的结果为单一的基本类型，直接用等号判断即可，但大量的题目是无法直接比较得出答案的。难以比较的结果主要分为以下几种：
	
	* 输出结果带有一定随机性。例如，输出结果是一个无序集合，算法a和算法b输出的结果是乱序的，需要一定程度的遍历，检索和记录；
	* 输出结果为一个对象。无法直接比较两者的引用来验证，需要比较器（重写compareTo方法）来协助验证过程；
	* 输出结果的类型需要一定转换。在讨论输入类型时我们提到四种基本逻辑结构，越是靠后的结构越复杂，有时通过将复杂的物理结构进行压缩（类似“降维打击”）可以有效提高数据的运算速度。例如，二叉树的比较是一个比较复杂的问题，但是当把二叉树序列化之后得到的字符串可以唯一标识一个树，这样字符串之间的比较就简单得多。
	
	总之，对于计算结果的对比验证，其难度有时不亚于一道算法题目，需要在验证函数完成后多测试，确保验证函数的正确性，否则之后产生的问题会很难定位。

<div class="note warning"><p>在对输入数据进行运算时，为了避免算法对原始输入数据的影响和算法之间的相互影响，通常开辟不同的内存空间存储相同值的输入数据，供方法a和方法b进行运算。</p></div>

### 5.如果有一个样本使得比对出错，打印样本分析是哪个方法出错

样本验证出错是常有的情况，我们自己写出来的代码很难一次就AC，能够及时发现bug，修正代码的逻辑才是需要更加重视的能力。这里要注意的是，当发现错误时，我们需要保留出错时的相关信息，帮助我们锁定出问题的位置，才能迅速的修正问题。一般而言，在对数器的主函数中，就应当写明出现问题时打印当前比较中原始输入的数据，方法a执行的结果，方法b执行的结果等，分析这些信息来判断出错的函数（不是每次出错都是方法a的问题，有时方法b也会产生部分错误，但这些错误很快就会暴露出来）。分析错误的流程如下：

1. 先将通过人工推导的方式将原始输入数据推导一遍，得到输出数据，通过三方数据相互比对确定哪一个或几个数据出现问题
2. 在找到数据出问题的方法，将原始输入数据代入其中，人工推导一遍代码的运算逻辑，找出出错的位置
3. 修改出错的代码逻辑

### 6.当样本数量很多时比对测试依然正确，可以确定方法a已经正确

当最初设定的样本数量全部通过之后，可以打印相应的信息进行标识，到此为止，我们已经可以基本确定，方法a是准确的了。之所以说基本确定，因为这样的验证方法仅仅是基于归纳统计得出的结果，而非通过严格的数学证明。但往往严格的数学证明需要花费大量的时间和精力，尤其是在贪心策略的相关题目上，提出一个贪心策略很容易，验证一个贪心策略也不是很难，而证明一个贪心策略需要深厚的数学功底，如果当前的目标是提升自己的算法能力，那么使用对数器来验证算法就够用了。

## 对数器的代码实现

```java

public class AlgorithmDevice {
	/**
	 * 生成随机数组
	 * @param maxSize	数组最大长度
	 * @param maxValue	数组元素最大值
	 * @param hasMinus	元素是否包含负数
	 * @param withEmpty	数组是否可以为空
	 * @return
	 */
	public static int[] geneRandomArray(int maxSize,int maxValue,boolean hasMinus,boolean withEmpty) {
		int[] arr;
		if(withEmpty)
			arr = new int[(int)(Math.random()*(maxSize+1))];
		else
			arr = new int[(int) (Math.random() * (maxSize - 1 + 1)) + 1];
		
		for(int i = 0 ; i < arr.length ; i++) {
			if (hasMinus)
				arr[i] = (int)(Math.random()*(maxValue+1))-(int)(Math.random()*(maxValue+1));
			else
				arr[i] = (int)(Math.random()*(maxValue+1));
		}
		return arr;
	}
	/**
	 * 验证两个数组是否完全相等
	 * @param arr1
	 * @param arr2
	 * @return
	 */
	public static boolean validate(int[] arr1, int[] arr2) {
		if((arr1==null&&arr2!=null)||(arr2==null&&arr1!=null))
			return false;
		if(arr1==null&&arr2==null)
			return true;
		if(arr1.length!=arr2.length)
			return false;
		boolean equal = true;
		for(int i = 0 ; i < arr1.length ; i++) {
			if(arr1[i]!=arr2[i]) {
				equal = false;
				break;
			}
		}
		return equal;
	}
	/**
	 * 	验证两个链表是否完全相等
	 * @param n1
	 * @param n2
	 * @return
	 */
	public static boolean validateLinkList(Node n1,Node n2) {
		if(n1==null&&n2==null)
			return true;
		boolean equal = true;
		while(n1!=null&&n2!=null) {
			if(n1.value!=n2.value) {
				equal = false;
				break;
			}
			n1 = n1.next;
			n2 = n2.next;
		}
		if((n1==null&&n2!=null)||(n1!=null&&n2==null))
			equal = false;
		return equal;
	}
	//主函数
	public static void main(String[] args) {
		//设置初始值：验证次数，随机样本长度，最大值
		int textTime = 50000;
		int maxSize = 20;
		int maxValue = 50;
		//循环开关
		boolean ac = true;
		for(int i = 0 ; i < textTime ; i++) {
			//遵循方法隔离原则，方法a和b分别操作不同的内存区域
			int[] ran = geneRandomArray(maxSize, maxValue,false,false);
			//clone方法为数组的深拷贝
			int[] arr1 = ran.clone();
			int[] arr2 = ran.clone();
			
			//输出结果为基本数据类型的验证
			/*int num1 = a(arr1);
			int num2 = b(arr2);
			if(num1!=num2) {
				System.out.println(num1);
				System.out.println(num2);
				ac = false;
				break;
			}*/
			//输出结果为数组的验证
			/*if(!validate(arr1, arr2)) {
				ac = false;
				FoundSort.show(ran, false);
				FoundSort.show(arr1, false);
				FoundSort.show(arr2, false);
				break;
			}*/
			//……
		}
		if(ac)
			System.out.println("Nice!");
		else
			System.out.println("Fucking wrong!");
	}
}
```

## 比较器（Comparator）

### 含义

比较器是java从JDK1.2版本开始就有的泛型接口，用来确定同类对象之间的顺序关系，从JDK8之后，该接口被声明为函数式接口，可以通过lambda表达式进行调用。

### 使用场景

1. 上文中我们提到用比较器来比较两个对象的是否“相等”，我们都知道在java中对象作为一个引用类型，是不能够直接用等号`==`来进行比较的，这样只会比较两个对象是否指向同一块内存区域，显然不具有实际的比较意义。有些类中为了便于比较，封装了自己的比较方法，例如String类的equals方法，故对于自定义类型的比较，需要根据需求自定义该类实例的比较方法。
2. 在排序相关的算法过程中往往涉及到比较的环节，只有元素之间能够进行比较，才能确定顺序（且该顺序应该是确定唯一的，而非人为规定的循环顺序——“石头剪刀布”），因此为了简化代码，分离逻辑，将比较相关的代码放入比较器中。

### 代码实现

```java
package foundation;

import java.util.Arrays;
import java.util.Comparator;
import java.util.PriorityQueue;
import java.util.TreeSet;

public class CompareDevice {
	public static void main(String[] args) {
		Student s1 = new Student(1, "tom", 22);
		Student s2 = new Student(2, "lina", 12);
		Student s3 = new Student(3, "john", 25);
		Student[] stus = new Student[] { s3, s1, s2 };
		printStu(stus);
		/**
		 * 比较器的返回值： 
		 * 负数 ----> 第一个参数应该放在前面 
		 * 0  ----> 两个参数相等 
		 * 正数 ----> 第二个参数应该放在前面
		 */
		//根据ID进行比较
		Comparator<Student> idComparator = (o1, o2) -> o2.id - o1.id;
		//根据年龄进行比较
		Comparator<Student> ageComparator = (o1, o2) -> o2.age - o1.age;
		//数组排序时传入比较器，按比较器规则进行排序
		Arrays.sort(stus, idComparator);
		printStu(stus);
		Arrays.sort(stus, ageComparator);
		printStu(stus);
		//生成堆的过程可以按照比较器进行排序
		PriorityQueue<Student> heap = new PriorityQueue<>(idComparator);
		heap.add(s1);
		heap.add(s3);
		heap.add(s2);
		while (!heap.isEmpty()) {
			Student e = heap.poll();
			System.out.println(e.id + "\t" + e.name + "\t" + e.age);
		}
	}

	public static void printStu(Student[] stus) {
		System.out.println("=======================");
		for (Student e : stus) {
			System.out.println(e.id + "\t" + e.name + "\t" + e.age);
		}
		System.out.println("=======================");
	}
}

class Student {
	int id;
	String name;
	int age;

	public Student(int id, String name, int age) {
		super();
		this.id = id;
		this.name = name;
		this.age = age;
	}
}
```

<hr />
