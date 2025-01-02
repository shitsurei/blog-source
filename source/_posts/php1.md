---
title: PHP学习笔记1
tags: [基本语法,数据类型,字符串函数]
copyright: true
sticky: false
date: 2018-10-23 22:29:46
permalink: php1
categories: PHP
---

PHP的底层由C语言编写，是一种运行在服务器端解释执行的脚本语言，具有以下几个特性：
1.由于不需要编译，其变量类型在运行时进行创建和确定，是一种弱类型语言
2.PHP代码可以和HTML代码互相嵌套，易于学习和开发

<!-- more -->

##基本语法

### 注释方式

```php
	//单行注释方式1
	#单行注释方式2
	/*
	多行注释方式
	*/
```

### 变量定义

* 定义变量,以$开头，其余规则和c相同

```php
	#注意，PHP没有创建变量的命令，变量会在首次为其赋值时被创建
	#不赋值的变量不分配内存空间,类型类NULL
	$a;
	var_dump($a);//NULL
	$a=1;
	#PHP是弱类型语言，数据类型会根据上下文进行变化
	$b=1.5;
	$c=$a+$b;
	echo $c;	//2.5
```

### 变量作用域

* PHP的变量有三种作用域local，global，static
* 默认作用域

```php
	#函数之外声明的拥有global作用域，只能在函数以外访问
	 $d=1;
	function scope()
	{
		#函数之内声明的拥有local作用域，只能在函数以内访问
		$e=2;
		echo $e;//2
		echo $d;//Notice: Undefined variable
	}
	scope();
	echo $d;//1
	echo $e;//Notice: Undefined variable
```

* global关键词


```php
	#global关键词用于函数内访问全局变量
	$f=5;
	$g=6;
	function global_test()
	{
		global $f,$g;
		echo $f;//5
		echo $g;//6
	}
	global_test();

	#在函数内部的$GLOBALS[index]数组存储了所有的全局变量，下标为变量名
	function globals_test(){
		echo $GLOBALS['f'];//5
		echo $GLOBALS['g'];//6
	}
	globals_test();
```

* static关键字

```php
	#static关键字用于函数内部变量的静态化，即调用函数后变量不回收
	function static_test(){
		static $h=0;
		echo $h;
		$h++;
	}
	static_test();//0
	static_test();//1
	static_test();//2
	static_test();//3
	#该变量仍然是函数的局部变量
	echo $h;//Notice: Undefined variable
```

### 输出方式

* echo

```php
	#echo能够输出一个以上的字符串
	#拼串方式
	echo "a","b","c";//abc
	#显示字符串和变量
	$i=3;
	echo "i=$i";//i=3
	#显示数组元素
	$arr=array(1,2,3);
	echo "arr[0]={$arr[0]}";//arr[0]=1
```

* print


```php
	#print只能输出一个字符串，并始终返回 1
	$j=print "i=$i";//i=3
	print $j;//1
```

## 数据类型

> var_dump(变量);函数输出变量的类型和值，非十进制自动转为十进制,字符串类型输出变量长度和值

### 整型

PHP不支持无符号整数(一个整数占用四个字节，一个字节占8bit，PHP整数二进制的最高位只能表示符号，0表示整数，1表示负数)

```php
#PHP_INT_SIZE该常量显示当前机器一个整数用几个字节表示
echo PHP_INT_SIZE;//8
#PHP_INT_MAX该常量表示int所能表示的最大整数
echo PHP_INT_MAX;//9223372036854775807
#超出整型最大表示数会自动变成float类型
echo "<br/>";
$a=12;
var_dump($a);//int(12)
$b=1.5;
var_dump($b);//float(1.5)
$c=053;
var_dump($c);//int(43)
$d=0x5a;
var_dump($d);//int(90)
```

### 浮点型

字长与平台相关，通常最大值为1.8e308，精度是14

> 小数的精度从左边第一个非零数开始算起

```php

$e=2.4e3;
var_dump($e);//float(2400)
$f=8E-5;
var_dump($f);//float(8.0E-5)

```

### bool类型

以下值也会被当做false对待：整型0，浮点数0.0，空字符串，字符串"0"，空数组，NULL


```php

$is_true=true;
$is_true=false;
var_dump($is_true);//bool(false)

```

### 数组


```php

$arr = array('a', 'b');
var_dump($arr);//array(2) { [0]=> string(1) "a" [1]=> string(1) "b" }

```

### 字符串类型

> 单双引号均可
> 双引号：\$表示变量，\\表示转移符
> 单引号：仅输出\$符号，仅输出\\符号
> 单引号只解析单引号，双引号只解析双引号


```php

$txt2='string2';
var_dump($txt2);//string(7) "string2"

```

### NULL值


```php

$h=null;
var_dump($h);//NULL

```

### 对象


```php

class Car
{
  var $color;
  function __construct($color="green") {
    $this->color = $color;
  }
  function what_color() {
    return $this->color;
  }
}
$g=new Car("white");
echo $g->what_color();
var_dump($g);//object(Car)#1 (1) { ["color"]=> string(5) "white" }

```


## 字符串函数

### 计算字符串长度

strlen函数，返回字符串长度

```php

echo strlen($txt);//12

```


### 字符串匹配检索

返回找到匹配串的下标(从0开始)，找不到返回false,可选项第三个参数可以限定开始检索的位置

```php

$txt="hello worLd.";
$find = strpos($txt, "a");
if($find)
{
	echo "find";//不输出
}else
{
	echo "not_find";//输出
}

```

* strpos

```php

$txt="hello worLd.";
#strpos函数，第一次出现，区分大小写
echo strpos($txt, "l");//2

```

* strrpos

```php

$txt="hello worLd.";
#strrpos函数，最后一次出现，区分大小写
echo strrpos($txt, "l");//3

```

* strripos

```php

$txt="hello worLd.";
#strripos函数，最后一次出现，不区分大小写
echo strripos($txt, "l");//9

```

### 字符串检索替换

str_ireplace不区分大小写，str_replace区分大小写

```php

$txt="hello worLd.";
$txt = str_ireplace('l', '233', $txt);
echo $txt;

#也可以用于替换数组,最后一个参数用于统计替换次数
$color = array('red' , 'blue' , 'black');
$color2 = str_ireplace('red', 'yellow', $color , $index);
echo $color2[0];//yellow
echo $index;//1

```

### 字符串相似度匹配

```php

#计算两个字符串的相似度，返回百分比
similar_text("Hello World","Hello Shanghai",$percent);
echo "$percent%";//48%

```

### 比较字符串大小

> strcmp区分大小写，strcasecmp不区分大小写

* =0 - 如果两个字符串相等
* <0 - 如果 string1 小于 string2
* \>0 - 如果 string1 大于 string2

```php

echo strcmp("Hello world!","Hello world!");//0

```

### 字符串拆分

```php

$city="北京 上海 天津 西安 武汉";
$city_arr=explode(" ", $city);
print_r($city_arr);//Array ( [0] => 北京 [1] => 上海 [2] => 天津 [3] => 西安 [4] => 武汉 )

```

<hr />
