---
copyright: true
sticky: false
permalink: stable
tags: [稳定性,算法]
date: 2019-05-12 13:41:06
title: 排序算法的稳定性分析
categories: 算法和数据结构
image: 
---

学习排序算法，稳定性是一个绕不开的话题；稳定的算法不仅能够节省排序的成本，在工程实践中也有着重要的意义，那么究竟如何判断一个算法是不是稳定的呢，本文通过研究和分析一些常用算法的稳定性，对稳定性的实质做一定探讨。

<!-- more -->

## 稳定性的定义

排序算法的稳定性指依据当前比较原则进行排序前和排序后，相等元素的相对次序保持不变。这句话的意思显而易见，即如果对【5,3<sup>1</sup>,7,3<sup>2</sup>,4】进行排序，结果必须是【3<sup>1</sup>,3<sup>2</sup>,4,5,7】,才能说该算法是稳定的，反之出现【3<sup>2</sup>,3<sup>1</sup>,4,5,7】则说该算法是不稳定的。

那么，要求算法有这样的特性有什么意义呢。我们设想一下这样的场景：在一张大表内包含了整个年级所有的班的同学的考试成绩，当然为了区别每个同学是来自哪个班的，相应的也有班级这个字段来标识。现在，我们依据总成绩这个字段来将全年级的同学进行一个排名，排序的结果当然是各个班级的学生穿插在一起，大致如下表所示：

| 姓名 | 班级 | 成绩 |
| :--- | :--- | :--- |
| 张三 | 2 | 95 |
| 李四 | 1 | 93 |
| 王五 | 2 | 90 |
| 赵六 | 3 | 80 |
| 丁七 | 3 | 70 |
| …… | …… | …… |

在得到这样的排序结果之后，我们可以大致了解到每个同学在全年级中的名次，那么如果想要得到每个同学在班级中的名次呢，我们能否在现有排序的基础上，以班级这个字段为依据进行排序呢？如果，我们所采用的排序算法不稳定，那么很可能我们在对班级字段进行排序后得到下面这个结果：

| 姓名 | 班级 | 成绩 |
| :--- | :--- | :--- |
| 李四 | 1 | 93 |
| 王五 | 2 | 90 |
| 张三 | 2 | 95 |
| 赵六 | 3 | 80 |
| 丁七 | 3 | 70 |
| …… | …… | …… |

我们发现，在班级这个字段进行重排的过程中，张三这条字段虽然调整到李四之后，但是他和王五的相对次序与原表中发生了改变，这样第一次排序中产生的成绩降序这个实际意义在班级这个小的序列中就无法作为参考依据，那么如果我们希望查询学生在班级中的名次，在原大表中不论怎么重排都是无法做到的，任何一个字段的排序就会使得之前排好的其他字段的排序关系失效，我们不得不开辟新的空间来存储各个班的学生数据，花费额外的时间成本和空间成本，这样无疑是非常浪费的。

通过这个案例我们可以看出稳定的排序算法能够对多次排序的结果有记忆功能，帮助我们节省大量的时间和空间成本，从上述案例我们似乎可以发现，稳定与否的关键在于一次排序过程中，数据的位置交换有没有跨越值相等的元素，如果没有跨越，那么结果一定是稳定的，否则是不稳定的。那么到底是不是这样的，我们来通过一些具体的算法来分析一下。

## 常用算法的稳定性分析

### 冒泡排序

冒牌排序可以按照递增、递减以及大数冒泡和小数冒泡交叉分为四种情况，这里我们以递增顺序，大数向后冒泡为例，代码如下：

```java
	/**
	 * 冒泡排序：大数向后冒泡
	 * @param arr
	 */
	public static void bubble(int[] arr) {
		for(int i = arr.length; i > 0 ; i--) {
			for(int j = 0;  j < i-1 ; j++) {
				if(arr[j]>arr[j+1])
					swap(arr, j, j+1);
			}
		}
	}
```

假设原序列为【1,5,3<sup>1</sup>,2,7,4,6,3<sup>2</sup>】，那么每一次冒泡的结果如下：

1. 【1,3<sup>1</sup>,2,5,4,6,3<sup>2</sup>,7】
2. 【1,2,3<sup>1</sup>,4,5,3<sup>2</sup>,6,7】
3. 【1,2,3<sup>1</sup>,4,3<sup>2</sup>,5,6,7】
4. 【1,2,3<sup>1</sup>,3<sup>2</sup>,4,5,6,7】
5. 【1,2,3<sup>1</sup>,3<sup>2</sup>,4,5,6,7】
6. 【1,2,3<sup>1</sup>,3<sup>2</sup>,4,5,6,7】
7. 【1,2,3<sup>1</sup>,3<sup>2</sup>,4,5,6,7】

我们看到，排序结果中两个3的相对次序没有发生变化，说明这个算法是稳定的，分析一下代码可以发现，当两个元素比较是否应该改变相对次序时的代码为`if(arr[j]>arr[j+1])`，因为用的是大于号，而不是大于等于号，所以结果是稳定的，但是如果将其改成大于等于号的话，也不会影响冒泡排序的结果，但是原本相等元素的相对次序就会被打乱，因此可以说冒泡排序可以被设计成稳定的。

### 选择排序

选择排序也可以根据递增、递减以及选择大数和选择小数分为四种情况，这里以递增顺序，选择小数为例，代码如下：

```java
	/**
	 * 选择排序：选小数往前放
	 * @param arr
	 */
	public static void select(int[] arr) {
		for(int i = 0 ; i < arr.length ; i++) {
			int min_index = i;
			for(int j = i+1 ; j < arr.length ; j++) {
				if(arr[j]<arr[min_index])
					min_index = j;
			}
			if(min_index!=i)
				swap(arr, i, min_index);
		}
	}
```

假设原序列为【3<sup>1</sup>,1,7,4,6,3<sup>2</sup>,5,2】，那么每一次选择的结果如下：

1. 【1,3<sup>1</sup>,7,4,6,3<sup>2</sup>,5,2】
2. 【1,2,7,4,6,3<sup>2</sup>,5,3<sup>1</sup>】
3. 【1,2,3<sup>2</sup>,4,6,7,5,3<sup>1</sup>】
4. 【1,2,3<sup>2</sup>,3<sup>1</sup>,6,7,5,4】
5. 【1,2,3<sup>2</sup>,3<sup>1</sup>,4,7,5,6】
6. 【1,2,3<sup>2</sup>,3<sup>1</sup>,4,5,7,6】
7. 【1,2,3<sup>2</sup>,3<sup>1</sup>,4,5,6,7】

可以看到排序结果中两个3的相对次序发生了变化，并且在第二次选择的过程中就已经出现了相对次序的混乱，原因是，第二次选择时最小的数位于原本相对次序靠后的3之后，交换时第一个3直接跨过了第二个3和2进行了交换，而在之后的过程中，`if(arr[j]<arr[min_index])`这行代码使得最小值元素的下标在遇到相同元素时不会发生变化，所以第一个3再也没有机会换到第二个3之前。

那么我们尝试修改代码的细节，看是否能改变算法的不稳定性，首先我们需要明确的是，待排序的数据状况是随机的，我们不能要求小数一定位于相等的大数的前面，因此一定会出现上述案例中小数跨越中间的大数和前面的大数交换的情况。因此我们只能考虑是否能在之后的交换过程中调整回原本的顺序，将这一句代码`if(arr[j]<arr[min_index])`中的小于号改为小于等于号，这样原本序列的选择排序结果就如下：

1. 【1,3<sup>1</sup>,7,4,6,3<sup>2</sup>,5,2】
2. 【1,2,7,4,6,3<sup>2</sup>,5,3<sup>1</sup>】
3. 【1,2,3<sup>1</sup>,4,6,3<sup>2</sup>,5,7】
5. 【1,2,3<sup>1</sup>,3<sup>2</sup>,6,7,5,4】
6. 【1,2,3<sup>1</sup>,3<sup>2</sup>,4,7,5,6】
7. 【1,2,3<sup>1</sup>,3<sup>2</sup>,4,5,7,6】
8. 【1,2,3<sup>1</sup>,3<sup>2</sup>,4,5,6,7】

结果看起来似乎是成功了，我们来看问题出在哪，第三步选择时，上一个代码选择了从前往后遇到的第一个3进行交换，而这个代码选择了最后一个3进行交换，这样就将第二步选择时造成的乱序修正了，但是这样的修改是普遍适用的吗，我们再来看一个例子：

1. 【1,3<sup>1</sup>,7,4,3<sup>2</sup>,6,3<sup>3</sup>,5,2】
2. 【1,2,7,4,3<sup>2</sup>,6,3<sup>3</sup>,5,3<sup>1</sup>】
3. 【1,2,3<sup>1</sup>,4,3<sup>2</sup>,6,3<sup>3</sup>,5,7】
4. 【1,2,3<sup>1</sup>,3<sup>3</sup>,3<sup>2</sup>,6,4,5,7】
5. 【1,2,3<sup>1</sup>,3<sup>3</sup>,3<sup>2</sup>,6,4,5,7】
6. 【1,2,3<sup>1</sup>,3<sup>3</sup>,3<sup>2</sup>,4,6,5,7】
7. 【1,2,3<sup>1</sup>,3<sup>3</sup>,3<sup>2</sup>,4,5,6,7】
8. 【1,2,3<sup>1</sup>,3<sup>3</sup>,3<sup>2</sup>,4,5,6,7】
9. 【1,2,3<sup>1</sup>,3<sup>3</sup>,3<sup>2</sup>,4,5,6,7】

奇怪的是这次选择排序的结果虽然我们依据了修改后的代码逻辑，但是依然出现结果的不稳定性，这是为什么？仔细分析一下代码，我们可以发现之所以在上一个案例中选择排序的结果能够保证稳定性，其实是对排序过程中的数据状况也有隐含的要求，即被打乱的相等数据在开始确定最终站位之前，必须要保持原相对次序的逆序。因为相同的数字在确定最终站位时必然是依次确定的，那么你希望排序结果的相对次序是正序，那么排序之前的相对次序一定是逆序，这是很容易理解的。这个案例中，第三步开始，第一个3的位置已经确定，而在这一步之前三个3的相对次序还不是逆序，因此确定的结果必然也是乱序的。

通过上述分析，我们发现不论对原程序的逻辑做何种意义上的修改，都是要求其数据状况遵循一定的规律，在此代码逻辑的基础上这显然是不可能的。如果我们强行要保证这一算法的稳定性，似乎只能在每次交换元素之前，遍历跨度区间，找出与被交换元素相等的元素，进行依次的后移操作，以保证被交换的元素是相等元素中相对次序最靠前的元素。但是这无疑增加了算法的时间成本，选择排序本来作为一个O(N<sup>2</sup>)的算法，这样再套一层遍历，直接变成一个O(N<sup>3</sup>)的算法，为了保证稳定性而如此增加时间复杂度，无疑是得不偿失的。

### 插入排序

插入排序同样可以根据递增、递减以及向前插入和向后插入分为四种情况，这里以递增顺序，向前插入为例，代码如下：

```java
	/**
	 * 插入排序：从前往后选数字顺序插入
	 * @param arr
	 */
	public static void insert(int[] arr) {
		for(int i = 1 ; i < arr.length ; i++) {
			int index = i;
			for(int j = 0 ; j < i ; j++) {
				if(arr[i]<arr[j]) {
					index = j;
					break;
				}
			}
			if(index!=i) {
				int temp = arr[i];
				for(int j = i ; j > index ; j--)
					swap(arr, j-1, j);
				arr[index] = temp;
			}
			//show(arr,true);
		}
	}
```

假设原序列为【3<sup>1</sup>,1,7,4,6,3<sup>2</sup>,5,2】，那么每一次插入的结果如下：

1. 【1,3<sup>1</sup>,7,4,6,3<sup>2</sup>,5,2】
2. 【1,3<sup>1</sup>,7,4,6,3<sup>2</sup>,5,2】
3. 【1,3<sup>1</sup>,7,4,6,3<sup>2</sup>,5,2】
4. 【1,3<sup>1</sup>,4,7,6,3<sup>2</sup>,5,2】
5. 【1,3<sup>1</sup>,4,6,7,3<sup>2</sup>,5,2】
6. 【1,3<sup>1</sup>,3<sup>2</sup>,4,6,7,5,2】
7. 【1,3<sup>1</sup>,3<sup>2</sup>,4,5,6,7,2】
8. 【1,2,3<sup>1</sup>,3<sup>2</sup>,4,5,6,7】

我们看到，排序结果中两个3的相对次序没有发生变化，说明这个算法是稳定的，分析一下代码可以发现，当查找下一个未排序的元素该插入到哪个元素之前时的代码为`if(arr[j]<arr[j+1])`，因为用的是小于号，而不是小于等于号，所以结果是稳定的，但是如果将其改成小于等于号的话，也不会影响插入排序的结果，但是原本相等元素的相对次序就会被打乱，因此可以说插入排序可以被设计成稳定的。

## 稳定性实质

通过分析这些常见的稳定和不稳定的排序算法，我们发现，排序的过程无非可以分成两个步骤（这里指的是只借助O(1)辅助空间的算法），比较和交换。比较是排序的依据，交换是排序的过程，稳定的算法往往在交换元素时不会跨越元素，只是两两之间交换，这样当遇到相等的元素时，我们就可以很容易通过代码逻辑避免这种交换的出现，从而保证算法的稳定性。其实回想一下我们学过的算法，凡是涉及到跨元素进行交换的排序算法，例如快排和堆排，都是不稳定的排序算法。

综上我们在判断一个算法的稳定性时，只要多关注元素在交换的过程中是否有跨元素，便能够很好的进行判断。

<hr />
