---
copyright: true
sticky: false
permalink: php10
tags: [get,post]
date: 2018-11-29 23:24:54
title: PHP预定义超全局变量学习笔记
categories: PHP

---

所谓预定义超全局变量，指的是全部作用域中始终可用的内置变量，函数中无需通过global关键字访问。PHP提供了9中超全局变量，有助于方便快捷的写代码

<!-- more -->

我们之前学习过PHP的基本语法中有一种全局变量，在整个作用域（当前PHP脚本文件）中可见，而预定义的超全局变量，除了有全局变量的特点之外，可以无需声明直接引用。

## $_GET

使用场景：浏览器通过超链接传送信息给服务器

```php
echo "<a href='get.php?param1=你好啊'>传输数据</a>";//点击跳转到get.php页面
## 处理低版本IE浏览器（5/6）的中文乱码问题
/*
$str=urlencode("中文字符");
echo "<a href='get.php?param1='".$str.">传输数据</a>";
*/
```

通过URL参数传递给当前脚本的变量的数组，原理如下：

1. 浏览器点击超链接，遵循http协议发送请求
2. Apache服务器接受到请求后调用PHP模块
3. PHP模块处理请求，创建进程并封装相关参数到超全局数组$_GET中
4. PHP脚本处理超全局数组中的参数，处理完成后返回给服务器
5. 服务器遵循http协议返回响应给浏览器
6. 浏览器解析响应数据，展示界面

* 不同的进程对应不同的浏览器请求，封装不同的超全局数组，请求较多时要考虑服务器的并发承受能力
* 禁止在php.ini中启用register_globals（默认取全局数组中的变量），防止sql注入

## $_POST

使用场景：
1. 浏览前通过表单传输信息给服务器（注意多选框的name值应该以数组方式填写）
2. 流数据的传输，文件、图片等

```php
echo "
<h1>用户注册</h1>
<form action='post.php' method='post'>
用户名：<input type='text' name='username'/><br/>
密码：<input type='password' name='password'/><br/>
性别：男<input type='radio' name='sex' value='1'/>女<input type='radio' name='sex' value='0'/><br/>
爱好：唱歌<input name='hobby[]' type='checkbox' value='1'/>读书<input name='hobby[]' type='checkbox' value='2'/>跳舞<input name='hobby[]' type='checkbox' value='3'/><br/>
年级：<select name='class'>
<option value='1'>一年级</option>
<option value='2'>二年级</option>
<option value='3'>三年级</option>
</select><br/>
<textarea rows='10' cols='50' name='introduce'></textarea><br/>
<input type='submit' value='提交'>
</form>
";
/*
提交跳转到post.php页面
echo "<pre>";
print_r($_POST);
Array
(
    [username] => aaa
    [password] => 222
    [sex] => 1
    [hobby] => Array
        (
            [0] => 1
            [1] => 3
        )

    [class] => 1
)
echo "</pre>";
*/
```

## $_REQUEST

其中包含$_GET/$_POST/$_COOKIE三个超全局数组的信息，更完整。如果不知道提交方式，可以通过$_SERVER中的参数判断，官方不建议使用这个变量。

```php
if ($_SERVER['REQUEST_METHOD']=="post") {
	$echo $_POST['xxx'];
}elseif ($_SERVER['REQUEST_METHOD']=="get") {
	$echo $_GET['xxx'];
}
```

## $_SERVER

主要包含了http请求行和请求头的信息，以及客户端和服务器的一些信息，例如服务器文档根目录

```php
echo "<pre>";
print_r($_SERVER);
echo "</pre>";
```

* 获取请求该页面的客户机的IP地址 **$_SERVER['REMOTE_ADDR']**
* 获取服务器端的软件版本 **$_SERVER['SERVER_SOFTWARE']**

## $_ENV

该全局数组可以获取全局变量，出于安全性的考虑默认禁用，在开发和生产环境中不要启用。

```php
echo "<pre>";
print_r($_ENV);
echo "</pre>";
```

## $GLOBALS

包含全部变量的全局组合数组，变量的名字就是数组的键，一个自定义的全局变量也会被该数组管理。

```php
$a=22;
echo "<pre>";
print_r($GLOBALS);
echo "</pre>";
/*
Array
(
    [_GET] => Array
        (
        )

    [_POST] => Array
        (
        )

    [_COOKIE] => Array
        (
            [Hm_lvt_94d8610c17caaa5b58069613ba4c67cf] => 1541080289,1541147321,1541260617
            [__atuvc] => 8|44
            [scroll-cookie] => 0|/
        )

    [_FILES] => Array
        (
        )

    [GLOBALS] => Array
 *RECURSION*
    [a] => 22
)
*/
```

<hr />
