---
title: 简单工厂模式
copyright: true
sticky: false
date: 2018-11-13 12:52:07
permalink: design1
categories: 设计模式
tags: [面向对象风格,耦合,内聚]
image: https://shitsurei-pictures.oss-cn-beijing.aliyuncs.com/pics/design10.jpg
description: 设计模式学习笔记

---

<hr />

<!-- more -->

<div class="note info"><p>以下学习笔记内容由java语言编写。</p></div>

从一个简单的控制台计算器程序开始，编写程序在控制台输入两个运算数和操作符，完成加减乘除运算。

## 面向过程风格

```java
import java.util.Scanner;

public class Calculator {
	private static Scanner scan;

	public static void main(String[] args) {
		try {
			//输入参数
			scan = new Scanner(System.in);
			System.out.println("请输入数字A");
			double a = scan.nextDouble();			
			System.out.println("请输入运算符，包括+-*/：");
			String op = scan.next();
			System.out.println("请输入数字B");
			double b = scan.nextDouble();
			//运算、输出和异常处理
			switch (op) {
			case "+":
				System.out.println("计算结果为"+(a+b));
				break;
			case "-":
				System.out.println("计算结果为"+(a-b));				
				break;
			case "*":
				System.out.println("计算结果为"+(a*b));				
				break;
			case "/":
				if (b==0) {
					throw new Exception("被除数不能为0");
				}else{			
					System.out.println("计算结果为"+(a/b));				
				}
				break;
			default:
				System.out.println("输入运算符有误");
				break;
			}
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
```

以上代码虽然实现了计算器功能，但是不够灵活，可复用、可扩展、可维护性能并不好，如果能够将界面和业务分离，把计算功能抽象出来，就能复用到不同的界面，例如web端、移动端等，于是就有了第二个版。

## 业务逻辑封装

* 业务

```java
public class Operation {
	private double num1;
	private double num2;
	private double num3;
	private String op;
	//构造函数传入计算数和操作符
	public Operation(double num1,double num2,String op){
		this.num1=num1;
		this.num2=num2;
		this.op=op;
	}
	//计算方法
	public double cal(){
		try {
			switch (op) {
			case "+":
				num3=num1+num2;
				break;
			case "-":
				num3=num1-num2;
				break;
			case "*":
				num3=num1*num2;
				break;
			case "/":
				if (num2==0) {					
					throw new Exception("被除数不能为零");
				}
				num3=num1/num2;
				break;
			default:
				throw new Exception("操作符输入有误");
			}
		} catch (Exception e) {
			e.printStackTrace();
		}
		return num3;
	}
}
```

* 界面

```java
import java.util.Scanner;

public class Enter {
	private static Scanner scan;
	public static void main(String[] args) {
		//输入参数
		scan = new Scanner(System.in);
		System.out.println("请输入数字A");
		double a = scan.nextDouble();			
		System.out.println("请输入运算符，包括+-*/：");
		String op = scan.next();
		System.out.println("请输入数字B");
		double b = scan.nextDouble();
		//实例化计算类
		Operation o1 = new Operation(a, b, op);
		//调用计算方法输出结果
		System.out.println("计算结果为"+o1.cal());
	}
}
```

面向对象三大特性封装继承多态，上述代码只用到了封装特性，虽然将业务和界面分离，但是业务逻辑的代码依然没有实现很好的分离，无法应对更多的需求，例如：

1. 计算类中的不同计算方法并未分离，当只需要执行一种运算时，其他运算也不可避免的参与了编译
2. 当需要增加计算方法时要求拥有计算类的修改权限

基于以上缺陷，有了面向对象风格的第三个版本。

## 面向对象风格

![design11](https://shitsurei-pictures.oss-cn-beijing.aliyuncs.com/pics/design11.jpg)

* 运算父类

```java
public class Operation {
	//将操作数设置为私有成员变量
	private double num1;
	private double num2;
	private double num3;
	//构造方法传入参数
	public Operation(double num1, double num2) {
		super();
		this.num1 = num1;
		this.num2 = num2;
	}
	//设置get方法使得子类在计算时获取操作数
	public double getNum1() {
		return num1;
	}
	public double getNum2() {
		return num2;
	}
	//父类计算函数，在子类中覆盖
	public double cal(){
		return num3;
	}
}

```

* 运算子类-加

```java
//计算子类继承父类，重写父类的计算函数，封装各自的计算逻辑
public class OperateAdd extends Operation{
	
	public OperateAdd(double num1, double num2) {
		super(num1, num2);
	}

	public double cal(){
		return this.getNum1()+this.getNum2();
	}
}
```

* 运算子类-减

```java
public class OperateMinus extends Operation{
	public OperateMinus(double num1, double num2) {
		super(num1, num2);
	}

	public double cal() {
		return this.getNum1()-this.getNum2();
	}
}
```

* 运算子类-乘

```java
public class OperateMultiply extends Operation{
	public OperateMultiply(double num1, double num2) {
		super(num1, num2);
	}

	public double cal() {
		return this.getNum1()+this.getNum2();
	}
}
```

* 运算子类-除

```java
public class OperateDivide extends Operation{
	public OperateDivide(double num1, double num2) {
		super(num1, num2);
	}

	public double cal() {
		try {
			if (this.getNum2()==0) {
				throw new Exception("被除数不能为零");
			}
		} catch (Exception e) {
			e.printStackTrace();
			System.exit(0);
		}
		return this.getNum1()/this.getNum2();
	}
}
```

* 界面

```java
import java.util.Scanner;

public class Enter {
	private static Scanner scan;
	//声明静态计算父类
	private static Operation o;
	public static void main(String[] args) {
		try {
			//输入参数
			scan = new Scanner(System.in);
			System.out.println("请输入数字A");
			double a = scan.nextDouble();			
			System.out.println("请输入运算符，包括+-*/：");
			String op = scan.next();
			System.out.println("请输入数字B");
			double b = scan.nextDouble();
			//根据运算符实例化计算子类（对象上转型）
			switch (op) {
			case "+":
				o = new OperateAdd(a, b);				
				break;
			case "-":
				o = new OperateMinus(a, b);
				break;
			case "*":
				o = new OperateMultiply(a, b);				
				break;
			case "/":
				o = new OperateDivide(a, b);				
				break;
			default:
				throw new Exception("输入运算符有误");
			}
			//调用计算函数并输出结果
			System.out.println("计算结果为"+o.cal());			
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}

```

上述代码充分利用了面向对象的封装继承和多态原则，通过子类对父类的继承和子类实例化时对象上转型，保证每次运算不用编译额外的运算过程，提高了代码的效率，提升了业务逻辑的可扩展性，当需求变化要加入新的运算时只需写入新的子类，继承计算父类并覆盖父类的计算方法即可。

## 简单工厂模式

实际上上一个面向对象的版本已经出现了简单工厂模式的雏形，只需要把界面中的实例化过程进一步封装就符合了简单工厂模式。

![design12](https://shitsurei-pictures.oss-cn-beijing.aliyuncs.com/pics/design12.jpg)

* 运算工厂

```java
public class OperationFactory {
	//通过运算符实例化不同的计算子类并返回
	public static Operation createOperation(String op){
		Operation o = null;
		try {
			switch (op) {
			case "+":
				o = new OperateAdd();				
				break;
			case "-":
				o = new OperateMinus();
				break;
			case "*":
				o = new OperateMultiply();				
				break;
			case "/":
				o = new OperateDivide();				
				break;
			default:
				throw new Exception("输入运算符有误");
			}
		} catch (Exception e) {
			e.printStackTrace();
			System.exit(0);
		}
		return o;
	}
}
```

* 运算父类

```java
public class Operation {
	private double num1;
	private double num2;
	private double num3;

	public double getNum1() {
		return num1;
	}

	public void setNum1(double num1) {
		this.num1 = num1;
	}

	public double getNum2() {
		return num2;
	}

	public void setNum2(double num2) {
		this.num2 = num2;
	}

	public double cal(){
		return num3;
	}
}
```
* 运算子类-加

```java
public class OperateAdd extends Operation{
	public double cal(){
		return this.getNum1()+this.getNum2();
	}
}
```
* 运算子类-减

```java
public class OperateMinus extends Operation{
	public double cal() {
		return this.getNum1()-this.getNum2();
	}
}

```
* 运算子类-乘

```java
public class OperateMultiply extends Operation{
	public double cal() {
		return this.getNum1()+this.getNum2();
	}
}
```
* 运算子类-除

```java
public class OperateDivide extends Operation{
	public double cal() {
		try {
			if (this.getNum2()==0) {
				throw new Exception("被除数不能为零");
			}
		} catch (Exception e) {
			e.printStackTrace();
			System.exit(0);
		}
		return this.getNum1()/this.getNum2();
	}
}
```
* 界面

```java
import java.util.Scanner;

public class Enter {
	private static Scanner scan;
	public static void main(String[] args) {
		try {
			//输入参数
			scan = new Scanner(System.in);
			System.out.println("请输入数字A");
			double a = scan.nextDouble();			
			System.out.println("请输入运算符，包括+-*/：");
			String op = scan.next();
			System.out.println("请输入数字B");
			double b = scan.nextDouble();
			//通过计算工厂实例化计算子类
			Operation o = OperationFactory.createOperation(op);
			//通过set方法传入计算参数
			o.setNum1(a);
			o.setNum2(b);
			//调用计算方法输出参数
			System.out.println("计算结果为"+o.cal());	
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
```

<hr />
