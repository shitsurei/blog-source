---
title: PHP面向对象学习笔记2
copyright: true
sticky: false
date: 2018-11-05 18:32:58
permalink: php6
categories: PHP
tags: [封装,继承,多态]

---

任何一种面向对象的编程语言，无一例外的都要支持封装、继承、多态三大特性。所谓封装，即上一节所谈到的抽象原则的事项方法：提取一类事物的共有属性和行为，形成一个物理模型。java和PHP都是提供类来作为封装的基础组件，而C++中则提供了namespace和类两种设施用于实现封装；而继承，则是对应于自然社会中同类事物的不同特质，复用同类事物的相同特性，从而提升开发效率；多态表现出同类事物在某个行为上的不同表现，PHP对多态的支持力度尚不及java，在方法重载上需要借助魔术函数来实现。类似的，PHP也提供了抽象类和接口来支持面向对象风格程序的开发。

<!-- more -->

## 封装

1. 把抽象出来的数据和操作封装在一起，数据被保护在内部，程序的其他部分只有通过被授权的操作（成员方法）才能对数据进行操作，这样有利于对访问权限做判断，或根据参数对数据做处理
2. 访问形式是通过get和set方法访问私有变量，类似javabean的写法；同时PHP提供了魔术方法\__set和\__get管理所有的私有变量，但是不推荐使用这种方式，他破坏了私有变量的封装性，且不同私有变量无法进行不同处理

> 访问控制修饰符
> * PHP提供了三种访问控制修饰符** public private protected** 均可以用于修饰变量和方法（静态或非静态）
> * 默认的访问修饰符是 **public**

```php
class Person
{
	#表示全局，内部、外部、子类都可以访问
	public $name;
	#表示私有，只有类的内部可以访问
	private $age;
	#表示受保护，可以在本类和子类中访问
	protected $address;

	function __construct($name,$age,$address)
	{
		$this->name=$name;
		$this->age=$age;
		$this->address=$address;
	}
	private function show()
	{
		echo $this->name."|".$this->age."|".$this->address."<br>";
	}
	public function show1()
	{
		#成员方法之间可以相互调用，但必须通过this关键字
		$this->show();
	}
	#魔术方法
	public function __set($pro_name,$pro_val)
	{
		$this->pro_name=$pro_val;
	}
	public function __get($pro_name)
	{
		if (isset($pro_name)) {
			return $this->pro_name;
		}
		else
		{
			return null;
		}
	}
}
$p1=new Person("john",20,"shanghai");
$p1->show();//报错 Call to private method 私有方法不能直接访问
$p1->show1();//john|20|shanghai 通过成员方法访问
echo "$p1->age";//报错，Cannot access private property 私有变量不能直接访问
echo "$p1->address";//报错，Cannot access protected property 受保护变量不能直接访问
```

## 继承

* 通过 **extends** 关键字实现继承
* PHP只支持单继承，即一个子类只能继承一个父类（直接继承），但通过多重继承可以实现多继承
* 继承时只能继承父类的public和protected属性和方法，不能继承父类的私有属性和方法

```php
#父类
class Student
{
	public $name;
	protected $age;
	protected $address;
	private $kind = 1;
	function __construct($name,$age,$address)
	{
		$this->name=$name;
		$this->age=$age;
		$this->address=$address;
	}
	public function show()
	{
		echo $this->name."|".$this->age."|".$this->address."<br>";
	}
}
#子类
class Pupil extends Student
{
	public function show_kind()
	{
		echo "show_kind".$this->kind;
	}
}
class Graduate extends Student
{
	function __construct($name,$age,$address)
	{
		echo "子类的构造方法";
		#两种方式显示的调用父类的构造函数，普通方法同样采用这种方法
		#Student::__construct($name,$age,$address);
		#parent不能大写
		parent::__construct($name,$age,$address);
	}
}

$p1->show_kind();//报错 Undefined property 没有继承父类的kind属性

#子类没有定义构造函数时默认继承父类的构造函数
$p1=new Pupil("tom",12,"beijing");
$p1->show();//tom|12|beijing

#子类定义构造函数时默认运行子类的构造方法
$g1=new Graduate("john",22,"shanghai");
$g1->show();//子类的构造方法john|22|shanghai

```

## 多态

### 方法重载（overload）

方法重载指的是同一个类中的相同函数名、不同参数个数或类型的函数，在调用时通过参数的不同来区分

<div class="note warning"><p>PHP中默认不支持方法重载，需要通过魔术函数**__call**来实现。</p></div>

```php
class People
{
	# java的方法重载写法
	/*
	public function eating()
	{
		echo "eating";
	}
	public function eating($food)
	{
		echo "eating".$food;
	}
	*/
	# PHP的方法重载写法
	public function eat0()
	{
		echo "eating";
	}
	public function eat1($food)
	{
		echo "eating".$food;
	}
	#魔术函数 __call 模拟方法重载
	#一个对象调用某个不存在的方法时，系统会自动调用__call方法
	#第一个参数是函数名，第二个参数是函数参数，以数组形式传递
	function __call($method,$p)
	{
		if ($method=="eat") {
			if (count($p)==0) {
				$this->eat0();
			}elseif (count($p)==1) {
				$this->eat1($p[0]);
			}
		}
	}
}

$p1=new People;
# java的方法重载调用方法
# $p1->eating();
# $p1->eating("bread");//Cannot redeclare People::eat() 不支持这种重载方式
# php的方法重载调用方法
$p1->eat();//eating
$p1->eat("bread");//eatingbread

```

### 方法重写（overwrite）

方法重写（方法覆盖）指的是父类的方法在子类中不同的实现，要求方法名和参数完全相同，访问修饰符可以不同，但必须满足子类的访问范围\>=父类的访问范围

* 子类父类都是private修饰的相同方法也不能覆盖，因为覆盖的前提是继承父类，私有方法不能继承
* 所谓的覆盖并非完全覆盖，而是父类子类中方法名和参数相同时优先调用子类中的方法

```php
class Animal
{
	protected $name;
	protected $price;
	function __construct($name,$price)
	{
		$this->name=$name;
		$this->price=$price;
	}
	public function speak()
	{
		echo "unknow";
	}
}
class Dog extends Animal
{
	public function speak()
	{
		echo "wang";
		#覆盖后依然可以显式的调用父类的方法
		parent::speak();//unknow
		Animal::speak();//unknow
	}
}
class Cat extends Animal
{
	public function speak()
	{
		echo "miao";
	}
}
$d1=new Dog("mimi",250);
$c1=new Cat("sunge",2050);
$d1->speak();//wang
$c1->speak();//miao
```

## 基础设施

### 抽象类和抽象方法

* 抽象类用于不需要被实例化的类，主要为了让子类去实现
* 被abstract修饰的类就是抽象类，可以包含抽象方法或普通方法
* 被abstract修饰的方法就是抽象方法，抽象方法不能包含方法体，用分号结尾
* 含有的抽象方法的类就是抽象类，必须用abstract修饰
* 一个类继承了抽象类之后必须实现所有的抽象方法，除非该类也是抽象类

```php
#定义一个抽象类
abstract class A
{
	#创建一个好凑想方法
	abstract function b();
}
#继承抽象类
class C extends A
{
	#实现抽象方法
	function b()
	{
		echo "b";
	}
}
$c1=new C;
$c1->b();//b
```

### 接口

* 接口用于定义规范（方法、属性），实现高内聚低耦合
* 定义接口使用 **interface** 关键字，实现接口使用 **implements** 关键字
* 一个类可以实现多个接口
* 实现接口时类必须实现接口及其该接口所继承的其他接口中的所有抽象方法

> 使用场景：
> 定规范 
> 团队合作写代码 
> 多个平级的类都要实现某个功能，但实现方式不同

```php
interface D
{
	#接口中可以有属性，要求必须是公开的常量
	#默认的方法修饰符是public
	const D1 = 0;
	#接口中的方法都不能有方法体
	public function e();
	public function f($g,$h);
}
interface D2{}

class I implements D,D2
{
	public function e()
	{
		echo "e";
	}
	public function f($g,$h)
	{
		echo "f".$g.$h;
	}
}
$i1=new I;
echo D::D1;//0
$i1->e();//e
$i1->f("gg","hh");//fgghh

#接口之间可以多继承
interface J{}
interface K{}
interface L extends J,K
{

}
```

> 补充说明：
> * 一个类可以同时继承抽象类和实现接口
> * 实现接口是对单一继承的补充
> * 可以在不破坏类层级关系的前提下对某个类功能扩展


### 静态关键字

* final


> 1. 如果父类中的方法被声明为final，则子类无法覆盖此方法
> 2. 如果一个类被声明为final，则这个类不能被继承
> 3. 需求：出于安全考虑，不希望自己写的类被继承或自己写的方法被改写
> 4. final不能修饰属性


* const

> 1. 用于修饰成员变量，使得变量无法被修改
> 2. 定义常量，定义初始就要赋初值，否则会报错；前面不要加修饰符（默认为public），否则也会报错

```php
class M
{
	const Con = 0;
	public function get_con()
	{
		echo self::Con;
	}
}
interface N
{
	const Cons = 1;
}
#访问常量
echo M::Con;//0
echo N::Cons;//1
$m1=new M;
$m1->get_con();//0
```


<hr />
