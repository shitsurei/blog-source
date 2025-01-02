---
title: PHP数据库编程学习笔记1
copyright: true
sticky: false
date: 2018-11-11 21:51:07
permalink: php7
categories: PHP
tags: [数据库连接,mysql扩展库]

---

数据库软件是位于操作系统之上对数据提供持久化支持的主要程序，在软件编程中有着广泛的应用，主要分为三层结构，分别是客户端（面向应用软件所提供的接口），数据库管理系统DBMS（mysql，Oracle，SQL server等）和数据对象（表，存储过程，函数等）。
由于历史的原因，PHP在初期并没有对面向对象编程风格太多支持，而在后期转向面向对象的过程中为了向下兼容，PHP在数据库的连接方面保留了不同的扩展库。以mysql数据库的连接为例，PHP提供了三种扩展库，用来支持不同的编程风格，PHP通过扩展库中的函数操作数据库。其中mysql扩展库最早，主要支持面向过程的编程风格，最新的PHP7中以及不再支持该扩展库，取而代之的是mysqli扩展库。

<!-- more -->

## mysql扩展库调用过程

<div class="note info"><p>请将本标题下的所有代码段链接起来学习理解。</p></div>

### 获取连接

```php
# 连接参数：IP+端口（默认3306），用户名，密码
$conn=mysql_connect("127.0.0.1","root","mysql");
if ($conn) {
	echo "success";
}
else
{
	# 返回错误信息
	die("failure".mysql_error());
}
```

### 选择数据库

```php
mysql_select_db("dbname") or die(mysql_error());
```

### 设置操作编码

```php
#保证PHP程序按照utf8操作
mysql_query("set names utf8");
```

### 发送sql指令

SQL指令分为四类：其中ddl（数据定义语句）一般在数据库建库时使用，很少通过应用软件连接数据库发送指令更改数据库、表和字段的属性；而dtl数据事务语句在mysql扩展库中并没有函数支持，而是通过发送SQL指令实现，进而在mysqli扩展库中增加了事务处理的相关函数。

* dml

数据操作语句，包括 update insert delete

```php
$sql="insert into tablename(key1,key2,key3,key4) values('value1','value2','value3','value4') "
### 注意在插入密码时，最好使用md5()进行加密，格式为md5('password')
$res=mysql_query($sql,$conn);
if ($res) {
	# mysql_affected_rows用于返回受影响的行数
	if (mysql_affected_rows($conn)>0) {
		echo "operate success";
	}
	else
	{
		# 删除操作有可能无删除数据
		echo "没有受影响的行数";
	}
}
else
{
	# 操作失败，输出失败原因
	die("operate failure".mysql_error());
}
```

* dql

数据查询语句 select

```php
$sql="select * from tablename";
#第一个参数是语句，第二个参数是连接符，用于确认是哪个连接（缺省时使用上一个打开的连接）
$res=mysql_query($sql,$conn);
```

<div class="note warning"><p>
1. res在从数据库结果集中取完数据后指向内存中的数据，因此在这句后关闭conn连接也不影响后续数据的使用，但是这句后释放结果集资源后则无法再操作数据
2. 返回的res是一个资源类型 mysql_result
3. 如果执行dml语句 返回类型是 bool
</p></div>

### 接受返回结果并处理

返回的数据集属于一个资源类型，PHP提供了多种封装形式，故可以采用多种取法处理返回结果。

* 取法1 mysql_fetch_row 会返回一个索引数组，并依次取下一行

```php
while ($row=mysql_fetch_row($res)) {
	echo "<br>$row[0]--$row[1]--$row[2]--$row[3]";
}
```

* 取法2 mysql_fetch_assoc 会返回一个关联数组（键值对），并依次取下一行

```php
while ($row=mysql_fetch_assoc($res)) {
	echo "<br>$row['key1']--$row['key2']--$row['key3']--$row['key4']";
}
```
* 取法3 mysql_fetch_array 会返回索引数组和关联数组（两套），并依次取下一行

```php
while ($row=mysql_fetch_array($res)) {
	#两种方式都可以取到
	echo "<br>$row[0]--$row[1]--$row[2]--$row[3]";
	echo "<br>$row['key1']--$row['key2']--$row['key3']--$row['key4']";
}
```
* 取法4 mysql_fetch_object 会把一行数据当做一个对象返回，并依次取下一行

```php
while ($row=mysql_fetch_object($res)) {
	echo $row->key1."---".$row->key2."---".$row->key3."---".$row->key4;
}
```
* 取法5 mysql_fetch_field 会把表的表头属性名当做一个数组返回

```php
while ($field_info=mysql_fetch_field($res)) {
	echo "<br>".$field_info->name;
}
```
* 取法6 mysql_affected_rows和mysql_num_fields 返回所取数据的行数和列数

```php
$rows=mysql_affected_rows($conn);//行数
$colums=mysql_num_fields($res);//列数
```

### 释放资源
```php
# 释放结果集中的资源，回收内存
mysql_free_result($res);
```

### 关闭连接
```php
# 可以不写，脚本执行结束后会自动关闭，执行语句后也不会立刻关闭
mysql_close($conn);
```

## mysql封装类

以上代码的可维护性和复用性不高，在面向对象编程中，我们通常将其封装成一个工具类SqlTool.class.php

```php
/**
 * 数据库操作工具类
 * 适用于mysql扩展库
 */
class SqlTool
{
	private $connection;
	private $host;
	private $username;
	private $password;
	private $dbname;
	# 构造函数，创建连接
	function __construct($host,$username,$password,$dbname)
	{
		$this->connection=mysql_connect($host,$username,$password);
		if ($this->connection) {
			$this->dbname=$dbname;
			mysql_select_db($dbname,$this->connection);
			mysql_query("set names utf8");
		}
		else
		{
			die("connection failure:".mysql_error());
		}
	}
	# 析构函数，关闭连接
	function __destruct()
	{
		mysql_close($this->connection);
	}
	# 将查询的数据作为对象索引数组返回
	function execute_dql($sql)
	{
		$res=mysql_query($sql,$this->connection) or die(mysql_error());
		$res_set=array();
		while ($row=mysql_fetch_object($res)) {
			array_push($res_set, $row);
		}
		mysql_free_result($res);
		return $res_set;
	}

	function execute_dml($sql)
	{
		$res=mysql_query($sql,$this->connection);
		if ($res) {
			return mysql_affected_rows($this->connection);//返回受影响的行数
		}
		else
		{
			return -1;//语句执行失败
		}
	}
	# 将查询的表头信息作为索引数组返回
	function query_field()
	{
		$res=mysql_query("select * from".$tablename,$this->connection) or die(mysql_error());
		$filed=array();
		while ($field_info=mysql_fetch_field(result)) {
			array_push($filed, $field_info->name);
		}
		mysql_free_result($res);
		return $filed;
	}
}
```

<hr />
