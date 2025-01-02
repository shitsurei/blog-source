---
title: PHP面向对象学习笔记1
copyright: true
sticky: false
date: 2018-11-03 23:04:44
permalink: php5
categories: PHP
tags: [对象,类,静态方法,静态变量]
---

编程语言在发展过程中大致经历了三个阶段，早期开发面临的情况是简单重复的科学计算，机械笨重的硬件平台，因此编程语言要尽可能的贴近底层，便于开发。随着软硬件技术水平的进步，编程语言也取得了长足的发展，从最初面向机器的汇编语言，到后来面向过程的C语言（封装函数，便于功能的复用），再到现在面向对象的java、PHP（封装对象，便于组建的复用），编程语言越来越接近自然语言和自然人的思维方式，这一改变大大降低了编程语言学习的门槛。可以展望，在不远的未来，我们很有可能用自然语言去编写程序，正是这样一层一层的封装，把内部结构包裹起来，只暴露外部接口，将我们的开发过程和底层隔离开，因此抽象和封装是现代编程语言最重要的特点，也是现代社会运行分工协作的方式。

<!-- more -->

## 类和对象

<div class="note info"><p>请将本标题下的所有代码段链接起来学习理解。</p></div>

### 类的创建

* 成员属性，可以是基本数据类型或符合数据类型
* 构造方法，默认的访问修饰符是**public**
* 析构方法，主要作用是释放资源，例如数据库连接，图片资源等，并非销毁对象本身（即无默认的析构方法）
* 成员方法，默认的访问修饰符是**public**

```php
class Animal 
{
	#成员属性
	public $kind;
	public $number;
	#构造方法
	public function __construct($kind,$number){
		#this代表当前对象,实质上是对当前对象的引用,只能在构造方法中使用
		#创建对象时默认先调用对象的构造方法,假设没定义,系统默认提供空构造方法,this会通过对象变量名所保存的地址去堆区的对象出做初始化操作
		$this->kind=$kind;
		$this->number=$number;
	}
	#析构方法
	#一个类只有一个析构函数，且该方法不能传递参数
	#程序退出（进程结束）或该对象成为垃圾对象（一个对象没有任何引用指向他）时自动调用，同一类的不同对象的调用顺序是先创建的对象后被销毁（栈的FILO机制）
	function __destruct(){
		echo "$this->kind被销毁";
	}
	#成员方法
	public function walk(){
		echo "walking";
	}
	public function eat($food){
		echo "eating".$food;
	}
}
```

### 对象的创建

```php
#对象的基本创建（实例化），后面括号可选 new
$cat=new Animal("未定义",0);//construct method
$dog=new Animal("未定义",0);//construct method
var_dump($cat);//object(Animal)#2 (2) { ["kind"]=> string(9) "未定义" ["number"]=> int(0) }
var_dump($dog);//object(Animal)#2 (2) { ["kind"]=> string(9) "未定义" ["number"]=> int(0) }
```

### 访问对象

* 访问对象的属性 **->**

```php
$cat->kind="猫科";
$cat->number=2;
$dog->kind="犬科";
$dog->number=3;
var_dump($cat);//object(Animal)#1 (2) { ["kind"]=> string(6) "猫科" ["number"]=> int(2) }
var_dump($dog);//object(Animal)#1 (2) { ["kind"]=> string(6) "猫科" ["number"]=> int(2) }
```

* 访问对象的方法 **->**

```php
$cat->walk();//walking
$cat->eat(" meat");//eating meat
```

> **对象的内存模型**
> 对象创建时在栈中存放变量名同时在堆中开辟空间用于存放真正的对象，变量名保存的是指向堆中对象位置的地址，因此PHP当中对象是按引用传递的，每个对象的变量都持有对象的引用，而非对象的拷贝

```php
$tiger=$cat;
echo $tiger->kind;//猫科
```

## 静态变量和静态方法

* 静态变量 **static** 与其相对的成为实例变量，同一个类的所有对象都共享的变量
* 类外部可以直接用类名访问静态变量，意味着静态变量不依赖于对象而存在
* 静态方法（类方法） **static** 不能访问非静态属性，普通成员方法可以访问静态变量和非静态变量
* 类外部可以直接用类名访问静态方法，意味着静态变量不依赖于对象而存在

```php
class People
{
	public $name;
	#定义并初始化一个静态变量，定义位置在常量区
	public static $num = 0;
	function __construct($name)
	{
		$this->name=$name;
		$this->num=20;//访问方式错误，但是不创建对象不会报错，说明PHP是运行时才执行的语言
	}
	#定义一个静态方法
	static function get_num()
	{
		echo self::$num;
	}
	function add()
	{
		#类内部访问静态变量，两种访问方式
		People::$num++;
		self::$num++;

		echo $this->name."加入";

		#类内部访问静态方法
		self::get_num();//2
		People::get_num();//2
	}
}
$p1 = new People("Tom");
$p1->add();//22

#类外部访问静态变量
echo People::$num;//2

#类外部访问静态方法
$p1->get_num();//6
#直接用类名访问静态方法，意味着静态变量不依赖于对象而存在
People::get_num();//6
```

<hr />
