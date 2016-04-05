---
title: Linux文本处理命令sed和awk介绍
date: 2016-03-22 01:34:21
tags:
categories: Linux的日常
---

> 很久没写博客了，最近在准备寻找暑期实习，在做笔试的过程中遇到一道考察Linux文本命令的题目，当时没有什么头绪。因为我个人日常其实比较少用到会直接使用shell命令在执行文本操作，如果有需要我可能更多的倾向于使用python这种更强大的脚本语言。但是在了解了有关的命令操作后，感觉在一些简单的文档处理方面，确实会比较方便，所以记录一下

## 三大文本处理命令

Linux有三个常用的文本处理命令，分别是`grep`，`sed`和`awk`。其中`grep`是大家使用的比较多的，它擅长通过正则寻找到特定内容，在文本处理以外的地方也有广泛的使用，在这就不具体的讲了。`awk`和`sed`相对于`grep`来说，不光能够在文档中进行查找，它们还能进行更强大的处理。`sed`擅长文本替换，`awk`擅长数据流处理。

## Sed——文本替换利器

### 基本介绍
`Sed`是一个非交互的流编辑器。它可以从文件或者标准输入中接受文本输入，对输入中特定的某些行逐行进行处理，然后输出到标准输出或者文件中。`sed`经常使用作为一个管道命令的组成部分。

### 基本操作

|操作|名称|作用|
|:-----:|:----:|:---------:|
|`[address-range]/p`|打印|根据条件打印特定行|
|`[address-range]/d`|删除|删除特定行
|`s/pattern1/pattern2/`|替换|用`pattern2`替换一行中第一个匹配的`pattern1`实例
|`[address-range]/s/pattern1/pattern2/`|替换|在指定行进行替换
|`[address-range]/y/pattern1/pattern2/`|转换 |将`pattern1`中的字符逐一转换为`pattern2`中的字符
|`[address] i pattern Filename`|插入 |将变化插入文件中，通常更多使用`-i`的选项
|`g`|全局选项|将变化应用到全局，而不只是每一行的第一个匹配

### 操作实例

+ 首处替换
{% codeblock line_number:false lang:bash %}
sed 's/text/replace_text/' file   # 替换每一行的第一处匹配的text
{% endcodeblock %}
+ 全局替换
{% codeblock line_number:false lang:bash %}
sed 's/text/replace_text/g' file   # 替换每一处匹配的text
# 如果加上-i，则会直接替换原文件内容
{% endcodeblock %}
+ 移除空白行
{% codeblock line_number:false lang:bash %}
sed '/^$/d' file
{% endcodeblock %}

+ 打印目标行
{% codeblock line_number:false lang:bash %}
sed -n '/Jones/p' file    # 打印只含有"Jones"的行
{% endcodeblock %}

## Awk——数据流处理工具

### 基本介绍
`awk`是一个带有C特性的文本处理语言，他的虽然不能像`python`那样完成所有的事情，但是在文本处理方面，他可以简单快捷的完成大部分任务

### 基本用法

**最基础的用法**  
`awk`最基础的用法是可以按照列来处理数据，比如变量`$1`，`$2`代表一列中的第一部分和第二部分内容。
比如`awk < file '{ print $2 }'`就表示输出每一行的第一个字段。如果要同时输出第二第三个字段可以使用`awk < file '{ print $2, $3 }'`。

**输入分割**  
`awk`默认的是采用空格和`tab`对字段进行分割，也可以使用`-F`选项设置`awk`的分割字符。比如我想要获得Linux系统中所有用户的家目录，可以使用`awk < /etc/passwd -F: '{ print $6 }'`来完成。

**数学计算**  
`awk`是一个弱类型语言，变量既可以是字符串也可以是数字，取决于他们的使用方式。所有数字都是浮点型。比如要实现华氏度到摄氏度的转换，可以用` awk '{ print ($1-32)*(5/9) }'`来实现。  
运算符的选择基本和C语言是一致的，虽然C语言中的拓展结构还无法工作。在`awk`中，字符串的拼接可以简单的通过把两个字符串表达式写在一起实现。`+`总是表示加法。因此会出现如下情况，希望能理解：
{% codeblock line_number:false lang:bash %}
echo 5 4 | awk '{ print $1 + $2 }' # prints 9
echo 5 4 | awk '{ print $1 $2 }' # prints 54
echo 5 4 | awk '{ print $1, $2 }' # prints "5 4"
{% endcodeblock %}


### 基本结构
**变量**
`awk`有一些内置的变量。`$1`等是一些例子；对于初学者来说还有一些其他的例子，比如`NF`表示该行的所有字段的数量（`$NF`就表示最后一个字段），还有`$0`表示整个的输入行。  
你还使用你自己喜欢的名字可以创建你自己的变量（当然保留字除外）。你不需要实现声明它们，如果变量没有被明确的赋值，它们默认是`""`或者`0`。比如说，你想要打印一行中所有的数字的平均数，你可以用下面的语句来实现：
{% codeblock line_number:false lang:bash %}
awk '{ tot=0; for (i=1; i<=NF; i++) tot += $i; print tot/NF; }'
{% endcodeblock %}

**语句块**
如果想要计算所有列中每行第一个数字的平均数，你可以这么做：
{% codeblock line_number:false lang:bash %}
awk '{ tot += $1; n += 1; }  END { print tot/n; }'
{% endcodeblock %}
注意到上面的例子中有两个不同的语句块，其中第二个语句块前有一个`END`，这表示在所有的输入处理完后执行该语句块。语句块前面的部分只是表示一个条件，事实上你可以用任何表达式放在一个语句块的前面，表示当满足某些条件时才执行语句块。比如`awk ' $1==0 { print $2 }'`表示当每一行的第一列为`0`时，打印该行的第二列。你也能用正则表达式，比如`awk ' /^test/ { print $2 }'`。如果你没有加任何的限定条件，那么该语句块会在每一行都执行。其中`START`和`END`是两个特殊情况，它们分别表示输入前和输入后执行语句块。
