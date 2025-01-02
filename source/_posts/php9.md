---
copyright: true
sticky: false
permalink: php9
tags: [数据库连接,mysqli扩展库]
date: 2018-11-18 22:47:51
title: PHP数据库编程学习笔记3
categories: PHP

---

前面我们提到过，数据库编程中的SQL指令分为四种，最后一种dtl数据事务语句还没有讲到。熟悉数据库的朋友应该知道，数据库是一个并行系统，在系统运行的过程中，不可能同一时刻只有一个用户在访问某个数据，当不同用户对同一个数据进行访问时，系统必须保证数据的同步，这就涉及到事务。
事务用于保证数据的一致性，由一组相关的dml语句组成，该组语句要么全部成功要么全部失败。事务具有acid四大特性：原子性、一致性、隔离性、持久性。在mysqli扩展库中提供了相应的函数支持，用来处理事务。同时，该扩展库还支持预编译技术，用于对多条SQL语句进行批处理。

<!-- more -->

## 事务处理

```php
$mysqli=new MySQLi("localhost","root","mysql","test");
if ($mysqli->connect_error) {
	die("connect failed".$mysqli->connect_error);
}

# 关闭默认的直接提交
# 相当于mysql控制台执行以下步骤： start transaction; savepoint a;
$mysqli->autocommit(false);

$sql1="update account set balance=balance-2 where id=1";
$sql2="update account set balance=balance+2 where id=2";

$res1=$mysqli->query($sql1);
$res2=$mysqli->query($sql2);

if ($res1&&$res2) {
	# 事务成功统一提交
	# 相当于mysql控制台执行以下步骤： commit;
	$mysqli->commit();
}
else
{
	# 失败全部回滚
	# 相当于mysql控制台执行以下步骤： rollback to a;
	$mysqli->rollback();
}

$mysqli->close();
```

## stmt预编译

PHP向mysql数据库发送批量相同模板指令时，可以第一次发送先编译好，之后只需要发送数据即可,批处理有两个优点：
* 效率高，节省数据库编译时间开销
* 安全性高，可以防止sql注入攻击

```php
/**
 * 模拟要添加的用户类
 */
class User
{
	public $id;
	public $name;
	public $age;
	public $password;
	function __construct($name,$age,$password)
	{
		$this->name=$name;
		$this->age=$age;
		$this->password=$password;
	}
}
```
### dml语句预编译处理方式

```php
$mysqli=new MySQLi("localhost","root","mysql","test");

# 用于批量绑定的参数
$u1=new User("n1",22,"p1");
$u2=new User("n2",23,"p2");
$u3=new User("n3",24,"p3");
$user_set=array($u1,$u2,$u3);

$sql_dml="insert into user(name,age,password) values(?,?,?);";
# 创建预编译对象
$mysqli_stmt=$mysqli->prepare($sql_dml);
# 绑定数据并执行
foreach ($user_set as $key => $value) {

	# 绑定参数（使用可变参数，类似于printf的写法）
	## 类型（s 字符串 i 整数 d 浮点数）和参数需要严格对应
	$mysqli_stmt->bind_param("sis",$value->name,$value->age,$value->password);

	# 执行
	$res=$mysqli_stmt->execute();
	if (!$res) {
		echo "failed".$mysqli_stmt->error;
	}
}
```

### dql语句预编译处理方式

```php
$mysqli=new MySQLi("localhost","root","mysql","test");

# 用于接受批量结果集的数组
$res_set=array();
# 用于批量绑定的参数
$param_set=array(20,23,25);

$sql_dql="select * from user where age>?;";
# 创建预编译对象
$mysqli_stmt=$mysqli->prepare($sql_dql);
# 绑定数据并执行
foreach ($param_set as $key => $value) {
	# 绑定参数
	$mysqli_stmt->bind_param("i",$value);
	# 绑定结果集（引用传参）
	$mysqli_stmt->bind_result($id,$name,$age,$password);
	# 执行指令
	$mysqli_stmt->execute();
	# 用于暂时存放一次结果集的数组
	$res=array();
	# 取出绑定的结果集
	while ($mysqli_stmt->fetch()) {
		$u=new User($name,$age,$password);
		$u->id=$id;
		array_push($res, $u);
	}
	array_push($res_set, $res);
	# 释放资源（返回的结果集）
	$mysqli_stmt->free_result();
}
# 关闭预编译语句
$mysqli_stmt->close();

# 批量结果集输出
foreach ($res_set as $key => $value) {
	echo "<br>new result";
	foreach ($value as $key1 => $value1) {
		echo "<br>--".$value1->id.$value1->name.$value1->age.$value1->password;
	}
}
```

## mysqli扩展库中对表格信息的访问

```php
$sql="select * from user;";
$res=$mysqli->query($sql);
## 获取返回结果集的总行数和总列数
echo "<br>--".$res->num_rows.$res->field_count;;
## 返回表头信息
while ($field=$res->fetch_field()) {
	# 字段属性名
	echo "--".$field->name;//--id--name--age--password
	# 字段最大长度
	echo "--".$field->max_length;//--2--9--2--32
	# 其他查手册
}
```

<hr />
