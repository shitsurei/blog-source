---
title: PHP学习笔记4
copyright: true
sticky: false
date: 2018-11-02 15:01:25
permalink: php4
categories: PHP
tags: [错误处理,异常处理]
---

PHP中的异常处理（广义）和其他语言有些不同，在PHP中，最初时没有异常处理机制的，只有自身的错误处理机制，用来处理脚本编译运行过程中出现的语法错误和运行环境问题，根据错误的严重程度分为不同的级别；后来在引入异常处理机制的过程中为了和已有的错误处理机制不冲突，就将PHP中的异常处理设定为只能捕获用户自定义的异常，而对于编译过程中的语法问题，PHP默认由自身的错误处理机制处理，用户无法进行捕获，这样两种机制各司其职，分别应对不同的情况。相比于其它语言中将所有运行中的错误都当做异常来处理，PHP的异常处理机制学习起来较为繁琐。

<!-- more -->

## 错误处理

### 错误级别

| 值 | 常量名 | 描述 | 举例 |
| :---: | :---: | :---: | :---: |
| 1 | E_ERROR | 这类错误一般不可恢复，例如内存分配导致的问题。导致脚本终止不再继续运行。  | Error：Invalid parameters. Invalid parameter name |
| 2 | E_WARNING | 运行时警告 (非致命错误)。脚本不会终止运行。  | Warning: require_once |
| 4 | E_PARSE | 编译时语法解析错误。如字符、变量或结束的地方写规范有误。 | Parse error: syntax error, unexpected $end in |
| 8 | E_NOTICE | 运行时通知。如变量未定义等。 | Notice: Undefined variable: p in E:index.php on line 17 |
| 16 | E_CORE_ERROR | 在PHP初始化启动过程中发生的致命错误。  | |
| 32 | E_CORE_WARNING | PHP初始化启动过程中发生的警告 (非致命错误) 。| |
| 64 | E_COMPILE_ERROR | 致命编译时错误。 | |
| 128 | E_COMPILE_WARNING | 编译时警告 (非致命错误)。  | |
| 256 | E_USER_ERROR | 用户产生的错误信息。由用户使用PHP函数 trigger_error() 产生。 | |
| 512 | E_USER_WARNING | 用户产生的警告信息。由用户使用PHP函数 trigger_error() 产生。 | |
| 1024 | E_USER_NOTICE | 用户产生的通知信息。由用户使用PHP函数 trigger_error() 产生。 | |

### 三种错误处理方式

* die函数

> die()语句，可以输出提示信息后退出，不再执行之后的代码

```php
if (!file_exists("filename")) {
	#die("diediedie");//diediedie
}
#简洁写法
file_exists("filename") or die("over");//over
```

* 自定义错误处理

1.自定义错误处理器，用于处理系统错误

```php
#创建自定义错误函数（处理器）
function my_error($error_level,$error_mess)
{
	echo "	<font size='5' color='red'>$error_level</font><br/>";
	echo "错误信息:$error_mess";
	exit();
}
#改写回调函数 set_error_handler 处理器，改变系统默认的错误处理函数
#第一个参数是错误函数名，第二个参数是错误级别
set_error_handler("my_error",E_WARNING);

#$fp=fopen("aaa.php", "r");//2(5号红色字体) 错误信息:fopen(aaa.php): failed to open stream: No such file or directory
```

2.自定义错误触发器，用于处理逻辑错误

```php
#trigger_error("业务逻辑不合理");//Notice: 业务逻辑不合理 in F:\work\php_workspace\错误异常处理1.php on line 59
#创建自定义错误函数
function my_error2($error_level,$error_mess)
{
	echo "<font size='3' color='green'>$error_level</font><br/>";
	echo "错误信息:$error_mess";
	exit();
}
#设置修改系统默认的错误触发器
set_error_handler("my_error2",E_USER_NOTICE);
trigger_error("业务逻辑不合理");//1024(3号绿色字体) 错误信息:业务逻辑不合理
#注意，trigger_error默认的错误级别是E_USER_NOTICE，，当set_error_handler设置的错误级别不是E_USER_NOTICE时，trigger_error函数需要指明
set_error_handler("my_error2",E_USER_WARNING);
trigger_error("业务逻辑不合理",E_USER_WARNING);//512(3号绿色字体) 错误信息:业务逻辑不合理
```

<div class="note info"><p>总结，自定义错误处理时需要厘清以下要点：
1. 自定义的错误属于系统错误还是业务逻辑错误，前者采用错误处理器，使用不带_USER的错误级别；后者采用错误触发器，使用带_USER的错误级别
2. 需要输出哪些错误信息和说明
3. 错误的严重程度如何，发生错误提示后是否还要继续执行之后的代码，即是否用exit函数中断程序

</p></div>

* 错误日志

PHP支持向服务器的错误记录系统或文件发送错误日志，包括本地保存和远程发送，默认错误日志输出信息的在php.in中的error_log配置，也可以通过在自定义错误函数中设置error_log()函数实现

```php
function my_error3($error_level,$error_mess)
{
	date_default_timezone_set("PRC");
	$error_info="错误时间：".date("Y-m-d G:i:s")."\t错误号：".$error_level."--".$error_mess;
	echo "错误信息已保存至\myerror.txt";
	#第一个参数是要输出的错误信息，第二个参数是错误信息发送位置，第三个参数取决于第二个参数，设置发送的目的地地址
	/*
	0 message 发送到 PHP 的系统日志，使用操作系统的日志机制或者一个文件，取决于 error_log 指令设置了什么。这是个默认的选项。  
	1 message 发送到参数 destination 设置的邮件地址。第四个参数 extra_headers 只有在这个类型里才会被用到。  
	2 不再是一个选项。  
	3 message 被发送到位置为 destination 的文件里。字符 message 不会默认被当做新的一行。  
	4 message 直接发送到 SAPI 的日志处理程序中。  
	*/
	error_log($error_info."\r\n",3,"myerror.txt");
	exit();
}
set_error_handler("my_error3",E_USER_WARNING);
trigger_error("错误日志测试",E_USER_WARNING);//错误信息已保存至\myerror.txt
#当前文件目录下的myerror.txt文件追加信息：错误号是：512--错误日志测试
```

## 异常处理

### try-catch-finally

该语句用于在指定错误发生时改变脚本的正常流程，可以有效的控制错误

```php
function f1($a)
{
	if ($a) {
		echo "a";
	}
	else
	{
		#抛出异常，第一个参数是异常信息，第二个参数为用户自定义异常编码，默认为0
		throw new Exception("a Error", 1);
	}
}

function f2($b)
{
	if ($b) {
		echo "b";
	}
	else
	{
		#抛出异常
		throw new Exception("b Error", 2);
		
	}
}

try{
	echo "enter";//输出
	f1(1);//a
	echo "stop";//输出
	#当异常被抛出后代码不会继续执行，PHP会尝试查找匹配的代码块
	f2(0);//code:2 message:b Error line:26 file:F:\work\php_workspace\错误异常处理2.php
	echo "end";
}
# 捕获try中的潜在异常，可以使用多个catch块捕获多种异常
catch(Exception $e)
{
	#捕获后的异常如果有能力处理，可以在此处处理
	echo "code:".$e->getCode();
	echo "message:".$e->getMessage();
	#异常抛出位置
	echo "line:".$e->getLine();
	#异常抛出文件及路径
	echo "file:".$e->getFile();

	#捕获后的异常也可以继续抛出，直到系统默认的顶级异常处理器处理
	#throw $e;//Fatal error: Uncaught Exception
}
finally
{
	#异常处理完后执行代码
	echo "finally codes";//最后输出
}
```

### 顶级异常处理器

对于当前代码段抛出后未捕获的异常会继续向上抛出，直到被PHP默认的顶级异常处理器捕获，用户也可以自己定义自己的顶级异常处理器

```php
#自定义一个顶级异常处理器
function my_exception($e)
{
	echo "我的顶级异常处理器".$e->getMessage();
}
#修改默认的顶级异常处理函数
set_exception_handler("my_exception");
try {
	#f2(0);//我的顶级异常处理器b Error
} catch (Exception $e) {
	throw $e;
} finally{

}

try {
	#PHP不会自动抛出异常，错误代码会自动调用错误处理机制
	$a=1/0;//报错 Warning: Division by zero
} catch (Exception $e) {
	echo "err";//不输出
}
```

<hr />
