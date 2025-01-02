---
copyright: true
sticky: false
permalink: design4
date: 2018-11-22 17:00:37
title: 装饰者模式
categories: 设计模式
tags: []
description: 设计模式学习笔记

---

玩过3D游戏的朋友可能对换装系统非常熟悉，游戏人物通过在商城购买或与玩家交易获得的装备可以用来改变游戏人物的外观或者提升游戏角色的属性，但你是否想过这样一套换装系统是如何实现的呢；再举一个暴露年龄的例子，很久以前PC端软件盛行的时代，年轻人大多热衷于美化自己的QQ秀，给自己聊天界面的人物形象购买一套好看的服饰。上述两个案例都涉及到了我们今天要说的装饰者模式这一设计思想，装饰者模式能够做到动态的给某一个实体增加功能或职责，但又不破坏该类的完整性，换句话说就是内部组装功能，最后返回类并调用功能。

<!-- more -->

<div class="note info"><p>以下学习笔记内容由java语言编写。</p></div>

以换装系统为例，现需求如下：要求玩家能够任意的组合自已的服饰，形成一套装扮。

## 面向过程风格

* 服饰类

```java
//该类中包含所有服饰相关的包装函数
public class Avatar {
	private String name;

	public Avatar(String name) {
		super();
		this.name = name;
	}
	
	public void wear_head(){
		System.out.print("头饰 ");
	}
	
	public void wear_ear(){
		System.out.print("耳饰 ");
	}
	
	public void wear_hand(){
		System.out.print("手饰 ");
	}
	
	public void wear_under(){
		System.out.print("内衣 ");
	}
	
	public void wear_coat(){
		System.out.print("外套 ");
	}
	
	public void show(){
		System.out.println("装扮的"+this.name);
	}
}
```

* 客户端类

```java
public class Enter {
	public static void main(String[] args) {
		Avatar a1 = new Avatar("玩家1");
		System.out.println("第一种装饰：");
		a1.wear_hand();
		a1.wear_under();
		a1.show();
	
		Avatar a2 = new Avatar("玩家2");
		System.out.println("第二种装饰：");
		a2.wear_coat();
		a2.wear_ear();
		a2.wear_head();
		a2.show();
	}
}
```

输出结果如下:

> 第一种装饰：
> 手饰 内衣 装扮的玩家1
> 第二种装饰：
> 外套 耳饰 头饰 装扮的玩家2

这个版本的代码虽然完成了要求的功能，且在一定程度上分离了界面和业务逻辑，但是还是将业务逻辑都封装在了同一个类中，这样的设计有两个显而易见的弊端：第一，通常商城中的装扮是很多的，而一个人物身上所能穿戴的是有限的，这样每一次生成新的对象时，大量用不到的代码也一同参与了编译，效率很低；第二，当商城推出新的饰品时，不得不修改该类，增加新的包装函数，显然违背了我们上一次所提到的开放封闭原则。

## 面向对象风格

* 游戏角色类

```java
public class Player {

	private String name;

	public Player(String name) {
		this.name = name;
	}

	public void show(){
		System.out.println("装扮的"+this.name);
	}
}
```

* 饰品父类

```java
public class Avatar {
	
	public void wear(){
		return;
	}
}
```

* 饰品子类

```java
public class AvatarCoat extends Avatar{
	public void wear() {
		System.out.print("外套 ");
	}
}
public class AvatarEar extends Avatar{
	public void wear() {
		System.out.print("耳饰 ");
	}
}
public class AvatarHand extends Avatar{
	public void wear() {
		System.out.print("手饰 ");
	}
}
public class AvatarHead extends Avatar{
	public void wear() {
		System.out.print("头饰 ");
	}
}
public class AvatarUnder extends Avatar{
	public void wear() {
		System.out.print("内衣 ");
	}
}
```

* 客户端类

```java
public class Enter {
	public static void main(String[] args) {
		System.out.println("第一种装饰：");
		Player p1 = new Player("玩家1");
		Avatar a = new AvatarCoat();
		a.wear();
		a = new AvatarEar();
		a.wear();
		p1.show();
		
		System.out.println("第二种装饰：");
		Player p2 = new Player("玩家2");
		a = new AvatarHand();
		a.wear();
		a = new AvatarHead();
		a.wear();
		a = new AvatarUnder();
		a.wear();
		p2.show();
	}
}
```

输出结果如下：

> 第一种装饰：
> 外套 耳饰 装扮的玩家1
> 第二种装饰：
> 手饰 头饰 内衣 装扮的玩家2

相比第一个版本，这次的饰品模块变成继承关系的饰品父类和具体装饰子类，减少了实例化饰品过程中的系统开销，再增加新饰品时也无需对已有模块进行改动，代码的质量有了很大的改善。当我们仔细分析代码就能发现，这个版本中装饰的过程并不是在装饰模块内部完成的，而是角色构建好之后将功能一个个调用实现的，饰品虽然和角色分属不同的类，但应该是和角色相关联的，我们可以将他们设计成聚合的关系，同时也可以利用装饰者模式来完成该功能。

## 装饰者模式

> **装饰者模式（Decorator）**：动态地给一个对象添加一些额外的职责，就增加功能来说，装饰模式比生成子类更为灵活。

* 角色类

```java
//定义一个对象接口，可以动态的给这些对象添加职责
public class Person {
	private String name;
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public void show(){
		System.out.println("装扮的"+name);
	}
}
```

* 饰品父类

```java
//继承角色类
public class Avatar extends Person{
	protected Person p;
	public void decorate(Person p){
		this.p = p;
	}
	public void show(){
		if (this.p!=null) {
			p.show();
		}
	}
}
```

* 饰品子类

```java
public class AvatarCoat extends Avatar{
	public void show() {
		System.out.print("外套 ");
		super.show();
	}
}
public class AvatarEar extends Avatar{
	public void show() {
		System.out.print("耳饰 ");
		super.show();
	}	
}
public class AvatarHand extends Avatar{
	public void show() {
		System.out.print("手饰 ");
		super.show();
	}
}
public class AvatarHead extends Avatar{
	public void show() {
		System.out.print("头饰 ");
		super.show();
	}
}
public class AvatarUnder extends Avatar{
	public void show() {
		System.out.print("内衣 ");
		super.show();
	}
}
```

* 客户端类

```java
public class Enter {
	public static void main(String[] args) {
		Person p = new Person();
		AvatarCoat ac = new AvatarCoat();
		AvatarEar ae = new AvatarEar();
		AvatarHand ah = new AvatarHand();
		AvatarUnder au = new AvatarUnder();
		AvatarHead ahe = new AvatarHead();
		
		p.setName("玩家1");
		System.out.println("第一种装饰：");
		ac.decorate(p);
		ae.decorate(ac);
		ah.decorate(ae);
//		ah.decorate(ah);//自己装饰自己，报错
		ah.show();
		
		p.setName("玩家2");
		System.out.println("第二种装饰：");
		ahe.decorate(p);
		au.decorate(ahe);
		ac.decorate(au);
		ac.show();
	}
}
```

输出结果如下：

> 第一种装饰：
> 手饰 耳饰 外套 装扮的玩家1
> 第二种装饰：
> 外套 内衣 头饰 装扮的玩家2

标准的装饰者模式中

<hr />
