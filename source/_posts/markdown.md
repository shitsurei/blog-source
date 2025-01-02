---
title: markdown学习笔记
permalink: markdown-learning
date: 2018-10-14 23:34
categories: 其他
tags: [markdown,MarkdownPad2]
image: https://shitsurei-pictures.oss-cn-beijing.aliyuncs.com/pics/test_pic.jpg
top: true
---

markdown可以理解为一种解释型的编程语言（类似于Python），在转换的时候根据不同的标签转换为HTML语句，从而实践不同的排版效果。

<!--more-->

## 1.区块元素

###段落

markdown的段落由一个或多个连续的文本行组成，每个文本行前后要有一个以上的空行,且可以对段落中的文字进行**加粗**、*斜体*、<u>加下划线</u>或~~删除字样~~的处理。

```
markdown的段落由一个或多个连续的文本行组成
每个文本行前后要有一个以上的空行
且可以对段落中的文字进行**加粗**、*斜体*、<u>加下划线</u>或~~删除字样~~的处理。
注意：加粗可以选中文字后使用快捷键Ctrl+B，斜体可以选中文字后使用快捷键Ctrl+I。
```

### 标题

# 这是一级标题

## 这是二级标题

### 这是三级标题

```
 # 这是一级标题
 ## 这是二级标题
 ### 这是三级标题
 以此类推，最多支持六级标题，注意#后要有空格且行首不能有空格，否则无法渲染。
```
### 区块引用

>这里是区块引用的内容
>
>>区块引用也可以采取嵌套形式

```
>这里是区块引用的内容
>>区块引用也可以采取嵌套形式
```

### 列表

* 无序列表项
* 无序列表项
* 无序列表项

```
* 无序列表项
* 无序列表项
* 无序列表项
此处*可以替换为+或-，注意符号与内容之间要有空格
```

1. 有序列表项
2. 有序列表项
3. 有序列表项

```
1. 有序列表项
2. 有序列表项
3. 有序列表项
此处第二项以及以后的开头数字和.可以用-替代，同样注意符号与内容之间的空格
```
### 表格

| 表头1 | 表头2 | 表头3 |
| --- | ---: | :---: |
| 条目1内容1aaaaaa | 条目1内容2 | 条目1内容3 |
| 条目2内容1 | 条目2内容2bbbbbb | 条目2内容3 |
| 条目3内容1 | 条目3内容2 | 条目3内容3cccccc |

```
默认每列元素左对齐，通过：设置对齐方式
| 表头1 | 表头2 | 表头3 |
| --- | ---: | :---: |
| 条目1内容1aaaaaa | 条目1内容2 | 条目1内容3 |
| 条目2内容1 | 条目2内容2bbbbbb | 条目2内容3 |
| 条目3内容1 | 条目3内容2 | 条目3内容3cccccc |
```

### 角标文字

这是一段有上方角标<sup>你好</sup>和下方角标<sub>世界</sub>的文字段落。

```
这是一段有上方角标<sup>你好</sup>和下方角标<sub>世界</sub>的文字段落，鼠标停留在文字上显示全程。
```

### 文本居中

{% cq %}
人生乃是一面镜子，
从镜子里认识自己，
我要称之为头等大事，
也只是我们追求的目的！
{% endcq %}

```
{% cq %}
人生乃是一面镜子，
从镜子里认识自己，
我要称之为头等大事，
也只是我们追求的目的！
{% endcq %}
```

### note标签

<div class="note default"><p>default</p></div>

```
<div class="note default"><p>default</p></div>

```

<div class="note primary"><p>primary</p></div>

```
<div class="note primary"><p>primary</p></div>

```

<div class="note success"><p>success</p></div>

```
<div class="note success"><p>success</p></div>

```

<div class="note info"><p>info</p></div>

```
<div class="note info"><p>info</p></div>

```

<div class="note warning"><p>warning</p></div>

```
<div class="note warning"><p>warning</p></div>

```

<div class="note danger"><p>danger</p></div>

```
<div class="note danger"><p>danger</p></div>

```

### tabs 标签

{% tabs 选项卡, 2 %}
<!-- tab -->
**这是选项卡 1** 哇，你找到我了！φ(≧ω≦*)♪～
<!-- endtab -->
<!-- tab -->
**这是选项卡 2** 默认显示
<!-- endtab -->
<!-- tab -->
**这是选项卡 3** 哇，你找到我了！φ(≧ω≦*)♪～
<!-- endtab -->
{% endtabs %}

```
{% tabs 选项卡, 2 %}
<!-- tab -->
**这是选项卡 1** 哇，你找到我了！φ(≧ω≦*)♪～
<!-- endtab -->
<!-- tab -->
**这是选项卡 2** 默认显示
<!-- endtab -->
<!-- tab -->
**这是选项卡 3** 哇，你找到我了！φ(≧ω≦*)♪～
<!-- endtab -->
{% endtabs %}

```

### 专有名词缩写

这是一段有专有名词<acronym title="National Basketball Association">NBA</acronym>和<abbr title="西安电子科技大学">西电</abbr>的文字段落。

```
这是一段有专有名词<acronym title="National Basketball Association">NBA</acronym>和<abbr title="西安电子科技大学">西电</abbr>的文字段落。
```

### 代码区块

这是一段包含行内代码`<a href="shitsurei.github.io">个人博客</a>`的文字

```
这是一段包含行内代码`<a href="shitsurei.github.io">个人博客</a>`的文字
注意：设置行内引用可以选中文字后使用快捷键Ctrl+K。
```

```javascript
var num = 0;
for (var i = 0; i < 5; i++) {
    num+=i;
}
console.log(num);
```

### Todo列表（待办事宜 ）

- [ ] 待办事件1
- [x] 待办事件2
- [x] 待办事件3

```
- [ ] 待办事件1
- [x] 待办事件2
- [x] 待办事件3
```

## 2. 资源链接

### 文字链接

这段文字中包含[个人博客](https://shitsurei.github.io/)的链接。

```
这段文字中包含[个人博客](https://shitsurei.github.io/)的链接。
```

### 图片链接

![夏至未至](https://shitsurei-pictures.oss-cn-beijing.aliyuncs.com/pics/test_pic.jpg)

![Caption](http://ww3.sinaimg.cn/mw690/81b78497jw1emfgwjrh2pj21hc0u01g3.jpg)

```
采取相对路径，需要先上传文件至相应的文件夹中
![夏至未至](/uploads/test_pic.jpg)
采取绝对路径，对路径格式有要求，不一定成功
![Caption](http://ww3.sinaimg.cn/mw690/81b78497jw1emfgwjrh2pj21hc0u01g3.jpg)
```

### 表情

:bowtie::smile::laughing::blush::smiley::relaxed::smirk::heart_eyes::kissing_heart::kissing_closed_eyes::flushed::relieved::satisfied::grin::wink:

```
:bowtie::smile::laughing::blush::smiley::relaxed::smirk::heart_eyes::kissing_heart::kissing_closed_eyes::flushed::relieved::satisfied::grin::wink:
更多表情信息参考：
https://www.webpagefx.com/tools/emoji-cheat-sheet/
```

## 3. 各种语言代码高亮

### 普通代码块

```
alert('Hello World!');
```

	print 'helloworld'

```
代码块课通过`包裹或tab键缩进标识
```

### javascript

```javascript
alert( 'Hello, world!' );
```

### python

```python
print 'helloworld'
```

### ruby

```ruby
def foo
  puts 'foo'
end
```

### c++

```c++
#include <iostream>

using namespace std;

void foo(int arg1, int arg2)
{

}

int main()
{
  string str;
  foo(1, 2);
  cout << "Hello World" << endl;
  return 0;
}
```

### python

```python
@requires_authorization
class SomeClass:
    pass

if __name__ == '__main__':
    # A comment
    print 'hello world'
```

## 4. 其他细节

### 文章简介

每篇博客的简介（用于首页显示）和正文部分通过`<!--more-->`语句分隔。

### 引擎渲染

不同的markdown引擎的解释效果是不同的，原因是采用的技术不同，有的是PHP有的是JS，本文主要针对jekyll+next的实现效果（即文章所演示出来的效果）