---
title: PHP数据库编程学习笔记2
copyright: true
sticky: false
permalink: php8
tags: [数据库连接,mysqli扩展库]
date: 2018-11-16 22:34:00
categories: PHP

---

在新版的PHP7中，PHP越来越向面向对象风格靠拢，原有的mysql已经不再支持，因此PHP更加推荐用户使用mysqli扩展库，相比mysql扩展库，mysqli扩展库具有以下优势：
* 稳定性，安全性，效率有所提升
* 支持面向对象编程风格，同时也向后兼容，支持面向过程编程风格（具备两套函数库）

<!-- more -->

## mysqli扩展库调用过程（面向对象风格）

<div class="note info"><p>请将本标题下的所有代码段链接起来学习理解。</p></div>

### 获取连接

```php
# 创建mysqli对象
$mysqli=new MySQLi("localhost","root","mysql","test");
## 验证是否连接成功
if ($mysqli->connect_error) {
	die("connect failed".$mysqli->connect_error);
}
```

### 发送sql指令

mysqli扩展库支持批量执行SQL语句。

* dml语句

```php
$sql="insert into user(name,age,password) values('a',30,md5('qcz'))";
$res=$mysqli->query($sql);
```

* 批量执行dml语句

```php
$sqls="insert into user(name,age,password) values('b',23,md5('zxq'));";
$sqls.="insert into user(name,age,password) values('c',23,md5('xam'));";
$sqls.="insert into user(name,age,password) values('d',24,md5('mt'));";
$sqls.="update user set age=20 where id=1;";
$sqls.="delete from user where id=2;";
$res=$mysqli->multi_query($sqls);
```

* dql语句

```php
$sql="select * from user";
$res=$mysqli->query($sql);
```

* 批量执行dql语句

```php
$sqls="select * from user where age>23;";
$sqls.="select * from user where age<=23;";
$res=$mysqli->multi_query($sqls);
```

### 处理结果

* dml语句

```php
if ($res) {
	# 操作影响行数
	if ($mysqli->affected_rows>0) {
		echo "success";
	}
	else
	{
		echo "no effects";
	}
}
else
{
	echo "operate failed".$mysqli->error;
}
```

* dql语句

```php
while ($row=$res->fetch_row()) {
	foreach ($row as $key => $value) {
		echo "<br>--$value";
	}
}
```

* 批量执行dql语句（执行成功返回批量结果集，失败返回false）

```php
# 必须使用do while循环取值
if ($res) {
	# 如果执行成功说明至少有一个结果集
	do {
		# store_result从mysqli中取出第一个结果集，即mysqli_result对象
		$result=$mysqli->store_result();
		while ($row=$result->fetch_row()) {
			foreach ($row as $key => $value) {
				echo "<br>--$value";
			}
		}
		# 关闭当前结果集
		$result->free();
		# more_results 判断是否还有剩余未取出的结果集
		if ($mysqli->more_results()) {
			echo "<br>new result";
		}
		else
		{
			break;
		}
	# next_result 不加判断的取下一个结果集
	} while ($mysqli->next_result());
}
```

### 关闭资源

```php
# 释放内存（执行dml语句不需要释放资源）
$res->free();
# 关闭连接
$mysqli->close();
```

## mysqli扩展库调用过程（面向过程风格）

```php
# 1 获取mysqli连接
$mysqli=mysqli_connect("localhost","root","mysql","test");
## 验证是否连接成功
if (!$mysqli) {
	die("connect failed".mysqli_connect_error($mysqli));
}
# 2 向数据库发送sql语句（ddl dml dql……）
$sql="select * from user";
$res=mysqli_query($mysqli,$sql);
# 3 处理得到的结果
while ($row=mysqli_fetch_row($res)) {
	foreach ($row as $key => $value) {
		echo "<br>--$value";
	}
}
# 4 关闭资源
## 释放内存
mysqli_free_result($res);
## 关闭连接（遵循 晚创建早释放 的原则）
mysqli_close($mysqli);
```

> 操作mysqli扩展库时与mysql扩展库相似：
> * 如果执行的是dml语句，则返回bool类型；如果执行dql语句，则返回查询结果mysqli_result结果集对象
> * 取res结果集的方式仍然有四种与mysql扩展库中的方法一一对应：
>  * $res->fetch_assoc()
>  * $res->fetch_row()
>  * $res->fetch_array()
>  * $res->fetch_object()
> * 释放资源有三种方式：
>  * $mysqli->free()
>  * $mysqli->close()
>  * $mysqli->free_result()

## mysqli封装类

以上代码的可维护性和复用性不高，在面向对象编程中，我们通常将其封装成一个工具类SqlTool.class.php

```php
/**
 * 数据库操作工具类
 * 适用于mysqli扩展库
 */
class SqlTool
{
	private $mysqli;
	function __construct($host,$username,$password,$dbname)
	{
		$this->mysqli=new MySQLi($host,$username,$password,$dbname);
		if ($this->mysqli->connect_error) {
			die("connect failed".$mysqli->connect_error);
		}
		$this->mysqli->query("set names utf8");
	}
	function __destruct()
	{
		$this->mysqli->close();
	}
	public function execute_dql($sql)
	{
		$res=$this->mysqli->query($sql) or die($sql."--failed--".$this->mysqli->error);
		$res_set=array();
		while ($row=$res->fetch_object()) {
			array_push($res_set, $row);
		}
		$res->free();
		return $res_set;
	}
	public function execute_dqls($sqls)
	{
		$res=$this->mysqli->multi_query($sqls) or die("sqls failed--".$this->mysqli->error);
		$res_set=array();
		if ($res) {
			do {
				$result=$this->mysqli->store_result();
				$res_set_obj=array();
				while ($row=$result->fetch_object()) {
					array_push($res_set_obj, $row);
				}
				array_push($res_set, $res_set_obj);
				$result->free();
				if (!$this->mysqli->more_results()) {
					break;
				}
			} while ($this->mysqli->next_result());
		}
		return $res_set;
	}
	public function execute_dml($sql,$is_multi)
	{
		if ($is_multi) {
			$res=$this->mysqli->multi_query($sql) or die($sql."--failed--".$this->mysqli->errno.$this->mysqli->error);
		}
		else
		{
			$res=$this->mysqli->query($sql) or die($sql."--failed--".$this->mysqli->errno.$this->mysqli->error);
		}
		if ($res) {
			return $this->mysqli->affected_rows;
		}
		else
		{
			return -1;
		}
	}
}
```

封装类的使用

```php
$sqltool=new SqlTool("localhost","root","mysql","test");
# 查
$sql="select * from user";
$res=$sqltool->execute_dql($sql);
# 处理
foreach ($res as $key => $value) {
	echo "<br>--".$value->name;
}

# 批量查
$sqls="select * from user where age>23;";
$sqls.="select * from user where age<=23;";
$res=$sqltool->execute_dqls($sqls);
# 处理
foreach ($res as $key => $value) {
	echo "<br>new result";
	foreach ($value as $key1 => $value1) {
		echo "<br>--".$value1->id.$value1->name;
	}
}

# 增
$sql="insert into user(name,age,password) values('a',25,md5('xyj'))";
# 删
$sql="delete from user1 where id=4";
$res=$sqltool->execute_dml($sql);

# 批量
$sqls="insert into user(name,age,password) values('b',23,md5('zxq'));";
$sqls.="insert into user(name,age,password) values('c',23,md5('xam'));";
$sqls.="insert into user(name,age,password) values('d',24,md5('mt'));";
$sqls.="update user set age=20 where id=1;";
$sqls.="delete from user where id=5;";
$sqls.="delete from user where id=6;";
$sqls.="delete from user where id=7;";
$res=$sqltool->execute_dml($sqls,true);

# 处理
switch ($res) {
	case 0:
		echo "no effects";
		break;
	case -1:
		echo "failed";
		break;
	default:
		echo "success";
		break;
}
```

<hr />
