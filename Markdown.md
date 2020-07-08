# Markdown 11种基本语法 #

#### 1. 标题设置（让字体变大，和word的标题意思一样）

在Markdown当中设置标题，有两种方式：  
第一种：通过在文字下方添加“=”和“-”，他们分别表示一级标题和二级标题。  
第二种：在文字开头加上 “#”，通过“#”数量表示几级标题。（一共只有1~6级标题，1级标题字体最大）

#### 2. 块注释（blockquote）
>通过在文字开头添加“>”表示块注释。（当>和文字之间添加五个blank时，块注释的文字会有变化。）

#### 3. 斜体
*将需要设置为斜体的文字两端使用1个“\*”或者“\_”夹起来*

#### 4. 粗体
**将需要设置为斜体的文字两端使用2个“\*\*”或者“\__”夹起来**

#### 5. 无序列表
在文字开头添加(\*, +, -)实现无序列表。如：

* Candy.
* Gum.
* Booze.  


#### 6. 有序列表
使用数字后面跟上句号。（还要有空格）如：

1. Red
2. Green
3. Blue

#### 7. 链接（Links）
Markdown中有两种方式，实现链接，分别为内联方式和引用方式。  
内联方式：This is an [example link](http://example.com/).  
引用方式：I get 10 times more traffic from [Google][1] than from [Yahoo][2] or [MSN][3].  

[1]: http://google.com/        "Google"
[2]: http://search.yahoo.com/  "Yahoo Search"
[3]: http://search.msn.com/    "MSN Search"


#### 8. 图片（Images）
图片的处理方式和链接的处理方式，非常的类似。  
内联方式：![alt text](/path/to/img.jpg "Title")  
引用方式：![alt text][id] 

[id]: /path/to/img.jpg "Title"

#### 9. 代码（HTML中所谓的Code）
实现方式有两种：  
第一种：简单文字出现一个代码框。使用`<blockquote>`。（\`不是单引号而是左上角的ESC下面~中的`）  
第二种：大片文字需要实现代码框。使用Tab和四个空格。

#### 10. 脚注（footnote）
实现方式如下：  
hello[^hello]

[^hello]: hi

#### 11. 下划线
在空白行下方添加三条“-”横线。（前面讲过在文字下方添加“-”，实现的2级标题）

#### 12. 表格
1|2|3
-|-|-
a|b|c

#### 13. Typora使用快捷键

无序列表：							输入-之后输入空格
有序列表：							输入数字+“.”之后输入空格
任务列表：							-[空格]空格 文字
标题：									 ctrl+数字
表格：								 	ctrl+t
生成目录：							[TOC]按回车
选中一整行：						ctrl+l
选中单词：						 	ctrl+d
选中相同格式的文字：	ctrl+e
跳转到文章开头：			  ctrl+home
跳转到文章结尾：			  ctrl+end
搜索：									  ctrl+f
替换：								  	ctrl+h
引用：								  	输入>之后输入空格
代码块：							 	ctrl+alt+f
加粗：								  	ctrl+b
倾斜：								  	ctrl+i
下划线：						 		ctrl+u
**删除线：						 		alt+shift+5**
插入图片：							直接拖动到指定位置即可或者ctrl+shift+i
插入链接：							ctrl+k