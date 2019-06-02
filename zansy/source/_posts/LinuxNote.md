title: Linux 基础教程（第二版）整理
author: zansy
tags: []
categories:
  - 读薄专业书计划
date: 2017-10-21 11:46:00
---
第一章第四章就是计算机操作系统中的内容，在此简略记录，详情翻阅《计算机操作系统》。本书重点其实是shell程序编写，以后有空了补上
<!--more-->
## 第一章 Linux系统概述
### 1.1 计算机基础知识
#### 1.1.1 硬件
从功能上讲，由五大功能部件组成，即运算器、控制器、存储器、输入设备和输出设备。
#### 1.1.2 软件
- 程序是计算机完成一项任务的指令的集合
- 数据是由程序使用或生成的不同类型的信息
- 软件通常可分为三大类，即系统软件、应用软件和支撑软件（也可以将后两者合为应用软件）

### 1.2 操作系统概述
#### 1.2.1 什么是操作系统
- 操作系统是控制和管理计算机系统内个钟硬件和软件资源，有效地组织多道程序运行的系统软件（或程序集合），是用户和计算机之间的接口。
1. 操作系统是软件，而且是系统软件，就是说，它由一整套程序组成
2. 它的几本职能是控制和管理系统内各种资源，有效地组织多道程序的运行
3. 它提供众多服务，方便用户使用、扩充硬件功能

#### 1.2.2 操作系统的功能
基本功能包括存储管理、进程和处理机管理、文件管理、设备管理和用户接口服务。
#### 1.2.3 操作系统的类型
一般把操作系统分为多道批处理系统、分时系统、实时系统、网络操作系统和分布式操作系统
### 1.3 Linux系统的历史和现状
### 1.4 Linux系统的特点
1. 与 UNIX 兼容
2. 自由软件，源码公开
3. 性能高，安全性强
4. 便于定制和再开发
5. 互操作性强
6. 全面的多任务和真正的32位操作系统

### 1.5 Linux的发展优势和存在的问题
### 1.6 Linux的常用版本
## 第二章 常用命令及文件操作
### 2.1 命令行方式
#### 2.1.1 进入命令行界面
#### 2.1.2 提示符
“$”表示当前用户是普通用户，“＃”表示是超级用户
### 2.2 简单命令
- who 命令：将列出所有正在使用系统的用户，所用终端名和注册到系统的时间。
- echo 命令：可以将命令行中的参数显示到标准输出（即屏幕）上
- date 命令：用于在屏幕上显示或设置系统的日期和时间
- cal 命令：可以显示公元1-9999年任意一年或者任意一个月的日历（两个参数时，1月份2年份；一个参数时年份）
- clear 命令：清除屏幕上的信息
- passwd 命令：把原来的密码改成一个更安全的字符串
- ls 命令：列出指定目录的内容
- pwd 命令：显示出当前目录的路径
- su命令：可以更改用户的身份

### 2.3 命令格式
#### 2.3.1 一般命令格式
- 注意：
1. shell 可以鉴别输入命令的大小写
2. 如果一个命令太长，一行放不下时，要在本行行尾输入“\”字符，并按Enter 键继续输入。

#### 2.3.2 常用命令一般格式
##### 1.who命令
<span style="border-bottom:2px solid black;">说明</span>
who命令将列出所有正在使用系统的用户
<span style="border-bottom:2px solid black;">常用选项</span>
`-b, --boot` 显示系统最近引导的时间
`-H, --heading` 打印出各列的标题
`-m` 仅显示与stdin相关的主机名和用户
`-q, --count` 显示所有注册用户名和注册用户数目
`-t, -time`显示系统时钟最后修改情况
##### 2.echo命令
<span style="border-bottom:2px solid black;">说明</span>
在屏幕上显示命令行中所给出的字符串
<span style="border-bottom:2px solid black;">常用选项</span>
`-n`输出字符串后光标不换行
##### 3.date命令
<span style="border-bottom:2px solid black;">说明</span>
如果没有选项和参数，将直接显示系统当前的日期和时间。
<span style="border-bottom:2px solid black;">常用选项</span>
`-d, --date=STRING`显示由给出的字符串指定的时间，不是now
`-s, --set=STRING`设定由给出字符串指定的时间
`-u, --utc,  --universal`显示或设定格林尼治时间
###### 格式控制字符串
`date '+Today is %D, and now id %r'`
(1) 时间表示形式

- %H: 0001-23表示小时
- %I: 0001-12小时- %M: 0001-59分钟
- %p: 显示出本地时间的AM/PM
- %r: 用hh:mm:ss AM 形式表示（十二时制）
- %T: 二十四时制
- %Z: 时区

(2) 日期表示形

- %a Sun Mon（缩写）
- %A Sunday Monday（全称）
- %b 缩写月份- %B 全称月份
- %c 用 Sat Jul 28 12:30:00 CST 2001 形式表示本地日期和时间
- %d 用 0102-31 表示每月的第几日
- %D 用 mm/dd/yy 形式表示日期

(3) [MMDDhhmm[[CC]YY[.ss]]]含义表示 月份 日期 小时 分钟 年份前两位（可选）年份后两位（选）秒数（选）
<span style="border-bottom:2px solid black;">示例</span>
`$ date -u --date='2 days ago'` 显示前天的时间
##### 4.cal命令
<span style="border-bottom:2px solid black;">说明</span>
可显示公元1-9999年中任意一年或者任意一个月的日历。如果无参数，则显示当前月份的日历。
<span style="border-bottom:2px solid black;">常用选项</span>
`-1` 默认方式。只显示一个月的日历。
`-3` 显示上月／本月／下月连续三个月的日历
`-s` 以周日为一周的第一天。默认。
`-m` 以周一为一周的第一天。
`-j` 显示儒勒日期，即一个月中的日号按照在一年中是多少天来显示。
`-y` 显示本年度的日历
<span style="border-bottom:2px solid black;">注意</span>
如果cal命令后只有一个参数，则该参数被解释为年份，而不是月份。否则参数一表月份，参数二表年份。
### 2.4文件及其类型
#### 文件名通配符
`*`

- 与0个或多个任意字符匹配
- 星号匹配的是当前目录下的所有文件，但以`.`开头的隐含文件除外
- `.*`只与隐含文件匹配

`?`

问号只与单个任意的字符匹配
`[]`

- 方括号与问号相似，只与一个字符匹配.区别在于：问号与任意一个字符匹配，方括号只与括号中列出的字符之一匹配。
- 可以用短横线表示一个范围内的字符，如`letter[1-5]`就是简写形式。（注意括号中的字符都按升序排列；方括号中可以列出多个范围，如`letter[A-Za-z]`）
- 如果在左方括号之后是一个感叹号`[!`或者脱字符`[^`，则表示与不在方括号中出现的字符匹配。

### 2.5常用文件操作命令
##### 1.cat命令
<span style="border-bottom:2px solid black;"> 说明</span>
该命令有两项功能，其一是用来显示文件的内容，依次读取由参数FILE所指明的文件，将它们的内容输出到标准输出上；其二是连接两个或多个文件，如：`cat f1 f2>f3`
<span style="border-bottom:2px solid black;">常用选项</span>
`-b --number-noblank` 从1开始对所有非空输出行进行编号
`-E --show-end` 在每一行的末尾显示一个$
`-n --number` 从1开始对所有输出行编号
`-s --squeeze-blank` 将多个相邻的空行合并成一个空行
<span style="border-bottom:2px solid black;">注意</span>
当文件较大时，发生滚动屏，因此一般用`more`等命令分屏显示
可以按`Ctrl+S`停止滚动屏，`Ctrl+Q`恢复滚动屏，`Ctrl+C`可以终止该命令执行。
##### 2.more命令
<span style="border-bottom:2px solid black;">说明</span>
一次显示一屏文本，满屏后停下来，并且在屏幕底部出现一个提示信息，给出至今已显示的该文件的百分比。

- `Space键` 显示文本的下一屏内容
- `Enter键` 只显示文本的下一行内容
- `斜线符(/)` 接着输入一个模式，可以在文本中寻找下一个匹配的模式
- `H键` 显示帮助屏
- `B键` 显示上一屏内容
- `Q键` 退出more命令

<span style="border-bottom:2px solid black;">常用选项</span>
`-num`指定一个整数，表示一屏显示多少行
`-d` 在每屏的底部显示更友好的百分比提示信息
`-c或-p` 不滚动屏。在显示下一屏之前先清屏
`-s` 将文件中连续的空白行压缩成一个空白行显示
`+/` 显示在每个文件之前，先搜索由该选项后的模式指定的字符串
`+num` 从行号num开始
##### 3.less命令
<span style="border-bottom:2px solid black;">说明</span>
与`more`命令相似，也用来分屏显示文件的内容。
二者差别：`less`命令允许用户利用向上或向下方向键方便地向前或向后浏览文件，而`more`命令只能向前浏览（除非在提示后按下B键，显示上一页内容）。
##### 4.head命令
<span style="border-bottom:2px solid black;">说明</span>
在屏幕上显示指定文件的开头若干行，行数由参数值确定。显示行数的默认是10。如果给出多个文件，则会标出相应文件的名称。如果没有给出文件名或文件名为“-”则读取标准输入。
<span style="border-bottom:2px solid black;">常用选项</span>
`-c --bytes=[-]N` 显示每个文件前面N个字节，如果数字N前带有“-”，则分别显示每个文件除最后N个字节以外的所有内容。
`-n --lines=[-]N` 显示指定文件的前面N行
`-q --quiet --silent` 不显示给定文件的标题
`-v --verbose` 始终显示给定文件的标题
<span style="border-bottom:2px solid black;">示例</span>
`head -5 FILE`显示文件的前5行
`head --bytes=-100 FILE` 显示文件除最后100个字节之外的所有内容
##### 5.tail命令
<span style="border-bottom:2px solid black;">说明</span>
在屏幕上显示指定文件FILE的末尾10行
<span style="border-bottom:2px solid black;">常用选项</span>
`-c --bytes=N` 输出最后N个字
`-f` 当文件增长时输出附加的数据
`-n --lines=N`输出最后的N行，如果使用+N选项，则从每个文件的第N行开始输出
`-q --quiet --silent` 不显示给定文件的标题
`-v --verbose` 始终显示给定文件的标题
<span style="border-bottom:2px solid black;">示例</span>
`tail -n +20 FILE`从第20行显示文件的内容。
`tail -c 10 FILE`显示文件的最后10个字符
##### 6.touch命令
<span style="border-bottom:2px solid black;">说明</span>
会修改指定文件FILE的时间标签，把已存在文件的时间标签更新为系统当前的时间。如果文件不存在，则建立一个空的同名文件。
<span style="border-bottom:2px solid black;">常用选项</span>
`-a` 仅改变指定文件的存取时间
`-c --no-create` 不创建任何文件
`-m`仅改变指定文件的修改时间
`-t STAMP`使用STAMP指定的时间标签，而不是系统当前的时间。格式为[[CC]YY]MMDDhhmm[.ss]，其中CC表示年份的前两位
##### 7.file命令
<span style="border-bottom:2px solid black;">说明</span>
对每个参数FILE进行检查，并予以分类
<span style="border-bottom:2px solid black;">常用选项</span>
`-b --brief`在输出行前不加文件名
`-r --raw` 对不可打印的字符不以八进制形式\000输出
#### 2.5.2匹配、排序及显示指定内容的命令
##### 1.grep命令
<span style="border-bottom:2px solid black;">说明</span>
用来在文本文件中查找指定模式的词或短语，并在标准输出上显示包括给定字符串模式的所有行。
该命令组包括三个命令：`grep` `egrep` `fgrep`
`grep`命令一次只能搜索一个指定的模式，`egrep` 等同于`grep -E`，可以使用扩展的字符串模式进行搜索；`fgrep`等同于`grep -F` 是快速搜索命令，检索固定字符串，但不识别正则表达式。
<span style="border-bottom:2px solid black;">常用选项</span>
`-E` 将模式解释成扩展的正则表达式
`-F` 将模式解释成一系列以换行符分开的单纯的字符串，其中任何一个都可被匹配
`-b --byte-offset` 在输出的每一行前面显示包含匹配字符串的行在文件中的位置，用自己饿偏移量来表示。
`-c --count`只显示文件中包含匹配字符串的行的总数
`-f FILE --file=FILE` 从文件中获取模式，每行一个。
`-i --ignore-case` 匹配比较时不区分字母的大小写
`-R -r --recursive` 以递归方式查询目录下的所有子目录中的文件
`-n`在输出包含匹配模式的行之前，加上该行的行号
`-v` 只显示不包含匹配字符串的文本行
`-x` 只显示整个行都严格匹配的行
##### 2.sort命令
<span style="border-bottom:2px solid black;">说明</span>
将逐行对指定文件中的所有行进行排序，并将结果显示在标准输出上
<span style="border-bottom:2px solid black;">常用选项</span>
`-m --merge`对意境排好序的文件统一进行合并，但不做排序
`-c --check`检查给定的文件是否已排好序，若没有，则显示出错消息，不做排序
`-u --unique`与`-c`选项一起用，严格地按照顺序检查，否则，对排序后的重复行只输出第一行。
`-o --output=FILE`将排序输出放到指定的文件FILE中，如果该文件不存在，则创建一个同名文件。
改变排序规则的选项主要有
`-d --dictionary-order` 按字典顺序排序，比较时仅考虑空白字符和字母数字字符。
`-f --ignore-case` 忽略字母的大小写
`-i --ignore-nonprinting` 忽略非打印字符
`-M --month-sort` 规定月份的比较次序是JAN<FEB<...<DEC
`-r --reverse` 按逆序排序，默认排序输出是按升序排序的。
`-k --key=n1[,n2]` 指定从文本行的第n1字段开始至第n2字段中间的内容作为排序关键字
`-b --ignore-leading-blanks` 比较关键字时忽略前导的空白符
`-t --field-separater=SEP`将指定的字符SEP作为字段间的分隔符
<span style="border-bottom:2px solid black;">示例</span>
`$ head mfile>more_h10`将文件mfile的前10行定向到文件more_h10中
`$ sort more_h10` 对文件more_h10排序
##### 3.uniq命令
<span style="border-bottom:2px solid black;">说明</span>
读取输入文件，并比较相邻的行，去掉重复的行，只留下其中的一行。
该命令加工后的结果写到输出文件中。
输入文件和输出文件必须不同。
<span style="border-bottom:2px solid black;">常用选项</span>
`-c --count`显示输出时，在每行的行首加上该行在文件中出现的次数
`-d --repeated` 只显示重复行
`-f --skip-fields=N`忽略比较前N个字段
`-s --skip-chars=N` 忽略比较前N个字符
`-u --unique` 只显示文件中不重复的行。
#### 2.5.3比较文件内容的命令
##### 1.comm命令
<span style="border-bottom:2px solid black;">说明</span>
对两个已经排好序的文件进行比较。
如果没有想象，comm从这两个文件中读取并依次生成1.仅在文件1中出现的行。2.仅在文件2出现的行。3.在两个文件中都存在的行。
<span style="border-bottom:2px solid black;">常用选项</span>
`-1`不输出仅在文件1中出现的行
`-2`不输出仅在文件2中出现的行
`-3`不输出两个文件中都存在的行
<span style="border-bottom:2px solid black;">示例</span>
`$ comm -12 m1 m2` 只显示m1 m2共有的行
##### 2.diff命令
<span style="border-bottom:2px solid black;">说明</span>
逐行比较两个文本文件，并找出它们的不同。
它比comm命令完成更复杂的检查，并且不要求两个文件预先排好序。
如果两个文件完全一样，则不显示任何输出。
<span style="border-bottom:2px solid black;">常用选项</span>
`-b` 忽略空格造成的差别
`-c` 输出格式是带上下文的三行格式
`-C n`输出格式是有上下文的n行格式
`-e`输出一个合法的ed脚本
`-I`忽略字母大小写的区别
`-r` 当文件1和文件2都是目录时，递归比较找到的各子目录。
#### 2.5.4 复制、删除和移动文件的命令
##### 1.cp命令
<span style="border-bottom:2px solid black;">说明</span>
如果源文件是普通文件，则该命令把它复制到指定的目标文件中；如果是目录，就需要使用`-r`选项，将整个目录下所有的文件和子目录都复制到目标位置。
<span style="border-bottom:2px solid black;">常用选项</span>
`-a` 递归地将源目录下的所有子目录及其文件都复制到目标目录中，并且保留文件链接和文件属性不变。
`-f --force` 如果现存的目标文件不能打开，则强行删除不加提示。
`-i --interactive` 与`-f`不同，它在覆盖目标文件之前先给出提示，要求用户予以确认。
`-p`除复制源文件的内容外，还将其修改时间和存取权限也复制到新文件中
`-R -r`递归复制目录，即将源目录下的所有文件及其各级子目录都复制到目标位置。
`-l` 不复制，而是创建指向源文件的链接文件，链接文件名由目标文件给出。
<span style="border-bottom:2px solid black;">注意</span>
cp命令复制一个文件，而原文件保持不变。
如果把一个文件复制到一个目标文件，而目标文件已存在，则该目标文件的内容将被破坏
##### 2.rm命令
<span style="border-bottom:2px solid black;">说明</span>
可以删除用户在命令行上指定的每个文件
<span style="border-bottom:2px solid black;">常用选项</span>
`-f --force`忽略不存在的文件，并且不给出提示信息
`-r -R --recursive`递归地删除指定目录及其下属的各级子目录和相应的文件。
`-i` 交互式地删除文件
##### 3.mv命令
<span style="border-bottom:2px solid black;">说明</span>
可以用来将源文件移至一个目标文件中，或将一组文件移至一个目标目录中
源文件被移至目标文件有两种不同的结果：
1. 如果目标文件是到某一目录文件的路径，源文件会被移到此目录下，且文件名不变。
2. 如果目标文件不是目录文件，则源文件会变成此目标文件名，并覆盖已存在的同名文件。

<span style="border-bottom:2px solid black;">常用选项</span>
`-i,--interactive` 交互式操作
`-f`与-i相反，禁止交互式操作
`-t --target-directory=DIRECTORY` 把所有的源文件或目录都移到DIRECTORY指示的目标中

#### 2.5.5 文件内容统计命令
##### wc 命令
<span style="border-bottom:2px solid black;">说明</span>
统计出指定文件的字节数、字数、行数，并输出结果
wc输出列的顺序和树木不受选项顺序和数目的影响，总是按以下格式显示，并且每列只占一列：行数 字数 字节数 文件名
<span style="border-bottom:2px solid black;">常用选项</span>
`-c --bytes` 统计字节数
`-l --lines` 统计行数
`-m --chars` 统计字符数
`-w --words`统计字数

## 第三章 目录及其操作
### 3.1 目录概念
1. Linux 树型目录结构
2. 根目录
3. 工作目录和主目录

#### 3.1.2 路径名
1. 绝对路径名
2. 相对路径名

#### 3.1.3 用户及文件存取权限
- 4种不同类型的用户，各有不同的职责和操作权限
1. 文件主
2. 同组用户
3. 可以访问系统的其他用户
4. 超级用户，具有管理系统的特权

- 文件主：Linux 为每个文件都分配了一个文件所有者，称为文件主，并赋予文件主唯一的注册名
- 用户组：当系统管理员为你建立账户之后，会分配一个组ID和一个特定的用户组名
- 用户存取权限：Linux 系统中的每个文件和目录都有存取许可权限，用它来确定谁可以通过何种方式对文件和目录进行访问和操作
- 文件存取权限：读r、写w、执行x
- 目录存取权限：读r、写w、执行x

### 3.2 常用目录操作命令
#### 3.2.1 创建和删除目录
##### 1.mkdir 命令
<span style="border-bottom:2px solid black;">说明</span>
`mkdir [] dirname` 创建由dirname命名的目录，无路径名则当前路径下新建，有则指定路径下新建
<span style="border-bottom:2px solid black;">常用选项</span>
`-m,--mode=MODE` 将新建目录的存取权限设置为MODE，存取权限用给定的八进制数字表示
`-p,--parents` 可一次建立多个目录，即如果为新建目录制定的路径dirname中有些父目录不存在，就可以自动建立它们
<span style="border-bottom:2px solid black;">示例</span>
在目录/a/b 下建立子目录test，并且只有文件主有读写和执行权限，其他人无权访问：`$ mkdir --mode=700 /a/b/test`
在当前目录中建立bin和其下os目录，权限设置为文件主可读写执行，同组用户可读和执行，其他用户无权访问：`$mkdir -p -m 750 bin/os`
##### 2.删除目录
<span style="border-bottom:2px solid black;">说明</span>
从一个目录中删除一个或多个子目录 `rmdir [] dirname`
<span style="border-bottom:2px solid black;">常用选项</span>
`-p,--parents` 递归删除目录dirname，当子目录删除后其父目录也为空时，也一同被删除
`-v,-verbose` 对每个被处理的目录输出相关诊断信息
<span style="border-bottom:2px solid black;">注意</span>

- 子目录被删除之前应该是空目录
- 当前工作目录必须在被删除目录之外，也不得是其子目录
- 还可以用带有-r选项的rm命令递归删除一个目录中的所有文件和该目录本身，但很危险

#### 3.2.2 改变工作目录和显示目录内容
##### 1.cd命令
<span style="border-bottom:2px solid black;">说明</span>
改变当前目录
利用(..)形式可以把工作目录向上移动两级 `cd ../..`
##### 2.pwd命令
<span style="border-bottom:2px solid black;">说明</span>
显示出当前工作目录的绝对路径
##### 3.ls命令
<span style="border-bottom:2px solid black;">说明</span>
列出指定目录的内容 `ls [] [FILE]`
如果FILE时目录，列出其中所有子目录与文件的信息
如果是文件，列出有关该文件属性的信息
<span style="border-bottom:2px solid black;">常用选项</span>
`-a,--all` 显示指定目录下所有子目录和文件，包括“.”开头的隐藏文件
`-A,--almost-all` 显示指定目录下所有子目录和文件，包括“.”开头的隐藏文件，但不列出“.”和“..”目录项
`-b,--escape` 当文件名中包含不可显示的字符时，用\ddd（三位八进制）形式显示该字符
`-c` 按文件的修改时间排序
`-C` 分成多列显示各项
`-d` 如果参数是目录，只显示它的名字，不显示其内容
`-F,--classify` 在列出的文件名后面加上不同的符号，以区分不同类型的文件，可以添加的符号具体参阅P56
`-l` 以长格式显示文件的详细信息，具体字段含义说明参阅P56
#### 3.2.3 链接文件的命令
Linux具有为一个文件起多个名字的功能，称为链接。被链接的文件可以存放在相同的或不同的目录下。
##### 1.硬链接
- 建立硬链接时，在另外的目录或本目录中增加目标文件的一个目录项，这样一个文件就登记在多个目录中。
- ln 命令创建硬链接。ln命令会增加链接数，rm命令会减少链接数。一个文件除非链接数为0，否则不会从文件系统中被物理删除。
- 注意：
1. 不能对目录文件做硬链接
2. 不能在不同的文件系统之间做硬链接

##### 2.符号链接
- 符号链接也称为软链接，是讲一个路径名连接到一个文件。
- 与硬链接不同的是，符号链确实是一个新文件，当然它具有不同的I节点号；而硬链接没有建立新文件
- 用ln -s 命令建立符号链接时，源文件最好用绝对路径名

##### 3.ln 命令
<span style="border-bottom:2px solid black;">说明</span>
用来创建链接，对象可以是文件，也可以是目录
<span style="border-bottom:2px solid black;">常用选项</span>
`-f,--force` 删除已有的目的地文件
`-i,--interactive` 提示是否删除目的地文件
`-s,-symbolic` 建立符号链接
`-v,--verbose` 显示每个被链接文件的名字
<span style="border-bottom:2px solid black;">注意</span>
符号链接文件不是一个独立的文件，它的许多属性依赖于源文件，所以给符号链接文件设置存取权限是没有意义的
<span style="border-bottom:2px solid black;">示例</span>
将/a/b/c 下的文件 cc.c 链接到目录 /a/d 下的文件 dd.c
`ln c/cc.c /a/d/dd.c`

#### 3.2.4 改变文件或目录存取权限
##### 1.chmod 命令
<span style="border-bottom:2px solid black;">说明</span>
用于改变或设置文件或目录的存取权限 `chmod [] MODE [,MODE] 文件`
<span style="border-bottom:2px solid black;">常用选项</span>
`-c,--changes` 与-v选项类似，但是仅当做了改变才报告结果
`-v,--verbose` 详细列出该命令对每个处理文件所做的工作
`-R,--recursive` 递归地修改文件和目录的权限
<span style="border-bottom:2px solid black;">示例</span>
将文件ex1的权限重新设置为文件主可以读和执行，组用户可以执行，其他用户无权访问
`chmod a+x ex1`

##### 2.umask 命令
<span style="border-bottom:2px solid black;">说明</span>
用来设置限制新建文件权限的掩码 `umask [-p] [-S] [mode]`

#### 3.2.5 改变用户组和文件主
##### 1.chgrp 命令
<span style="border-bottom:2px solid black;">说明</span>
用来改变文件或目录所属的用户组 `chgrp [] GROUP FILE`
<span style="border-bottom:2px solid black;">常用选项</span>
`-R,-recursive` 递归地改变指定目录及其下面的所有子目录和文件的用户组
`-v,--verbose` 详细列出该命令对每个处理文件所做的工作
<span style="border-bottom:2px solid black;">示例</span>
将/a/b 及其子目录下的所有文件的用户组改为 friend：
`# chgrp -R friend /a/b`
##### 2.chown 命令
<span style="border-bottom:2px solid black;">说明</span>
改变某个文件或目录的所有者和／或所属的组 `chown [] OWNER/GROUP FILE`
<span style="border-bottom:2px solid black;">常用选项</span>
`-R,-recursive` 递归地改变指定目录及其所有子目录和文件的文件主
`-v,--verbose` 详细列出该命令对每个处理文件所做的工作
<span style="border-bottom:2px solid black;">注意</span>
只有超级用户或者文件主才可以使用该命令
<span style="border-bottom:2px solid black;">示例</span>
将目录/a/b 及其下面的所有文件、子目录的文件主改为c
`#chown -R c /a/b`

### 3.3 连接帮助命令
Linux提供了联机帮助手册，利用它，可以方便地查看所有命令的完整说明，包括命令愈发、个选项的意义及相关命令等
#### 3.3.1 man 命令
<span style="border-bottom:2px solid black;">说明</span>
格式化并显示某一命令的链接帮助手册页 `man [] 参数`
<span style="border-bottom:2px solid black;">常用选项</span>
`-M 路径` 指定查找man手册页的路径
`-P 分页程序` 指定显示手册所使用的分页程序
`-S 章节` 指定查找手册页的章节列表
`-a` 按照默认，man在显示它找到第一个手册页后就退出
`-d` 这个选项并不实际显示手册页的内容，但是打印许多调试信息
`-D` 既显示手册页的内容，也打印调试信息
`-w,--path` 不显示手册页，只显示将被格式化和显示的文件所在的位置
<span style="border-bottom:2px solid black;">示例</span>
查看pwd命令的用法： `man pwd`
#### 3.3.2 help 命令
<span style="border-bottom:2px solid black;">说明</span>
用来查看所有shell内置命令的帮助信息 `help [-s] [pattern]
## 第四章
### 4.1 进程概念
#### 4.1.1 多道程序设计
##### 1.顺序程序活动的特点
##### 2.多道程序设计
##### 3.程序并发执行的特征
#### 4.1.2 进程概念
##### 1.进程定义
##### 2.进程的基本特征
### 4.2 进程状态
#### 4.2.1 进程的基本状态
#### 4.2.2 进程状态的转换
#### 4.2.3 进程族系
### 4.3 进程管理命令
#### 4.3.1 查看进程状态
##### 1.ps 命令
<span style="border-bottom:2px solid black;">说明</span>
监视系统内活动进程的状态
<span style="border-bottom:2px solid black;">常用选项</span>
<span style="border-bottom:2px solid black;">示例</span>
#### 4.3.2 进程管理
##### 1.kill命令
<span style="border-bottom:2px solid black;">说明</span>
通过向进程发送指定的信号来结束相应进程pid
pid可以是以下值：
1. n 表示该进程的pid
2. 0 表示当前进程组中的所有进程
3. -1 表示pid大于1的所有进程
4. -n 表示进程组为n的所有进程
5. 命令名 表示由该命令名产生的全部进程

##### 2. sleep 命令
<span style="border-bottom:2px solid black;">说明</span>
进程暂停执行一段时间，时间值以秒数为单位
在时间值后面可以有以下后缀：
s——秒
m——分钟
h——小时
d——天数
##### 3.nice 命令
<span style="border-bottom:2px solid black;">说明</span>
用于降低一个进程的优先级 `nice [选项] [命令]`
<span style="border-bottom:2px solid black;">常用选项</span>
`-n,--adjustment=N` 将进程的优先数增加N所指定的值。N的默认值是10
##### 4.nohup 命令
<span style="border-bottom:2px solid black;">说明</span>
允许用户运行在挂起（阻塞）或者从系统注销以后又希望继续运行的命令
##### 5.wait命令
<span style="border-bottom:2px solid black;">说明</span>
等待指定的进程，并返回其终止状态 `wait [n]` n可以是进程的ID或者作业描述符 
### 4.4 其他常用命令
#### 4.4.1 磁盘使用情况统计
##### 1.df 命令
<span style="border-bottom:2px solid black;">说明</span>
可以报告文件系统中未用磁盘空间的情况 `df [选项] [FILE]`
##### 2.du 命令
<span style="border-bottom:2px solid black;">说明</span>
可以统计处每个目录占用磁盘空间的情况，该命令可以逐级进入每一个子目录并显示该目录的磁盘利用情况
#### 4.4.2 文件压缩和解压缩
##### 1.grip 命令
<span style="border-bottom:2px solid black;">说明</span>
用来对文件进行压缩的解压缩
gzip -d 恢复成原始形式
##### 2.unzip 命令
<span style="border-bottom:2px solid black;">说明</span>
对ZIP格式的压缩文件进行解压缩
## 第五章 文本编辑
### 5.1进入和退出vi
#### 5.1.1 进入vi
`vi example.c`
#### 5.1.2 退出vi
`:wq`保存并退出
`:ZZ`只有当修改过时才将缓存区内容写到文件上
`:x`同`:ZZ`
`:q!`不保存退出
### 5.2 vi的工作方式
#### 1.命令方式
当输入命令vi进入编辑器时就处于vi的命令方式
#### 2.插入方式
通过输入vi的插入命令i 附加命令a 打开命令o 替换命令s 修改命令c 或取代命令r 可以从命令方式进入插入方式
#### 3.ex转义方式
vi与ex编辑器主要区别是用户界面
### 5.3 文本输入命令
#### 1.插入命令
`i` 输入内容在光标位置之前
`I` 在光标所在行行首插入文本
#### 2.附加命令
`a`在该命令之后输入的字符都插在光标之后
`A`在光标所在行的行尾添加文本
#### 3.打开命令
`o` 在光标所在行的下面新开辟一行
`O` 在光标所在行的上面新开辟一行
### 5.5文本修改命令
#### 删除字符
`x` 删除光标所在字符
如果前面给出一个值，例如`5x`，则由光标所在字符开始向右删除5个字符
`X`删除光标前面字符
如果前面给出数值，如`8X`，则由光标之前的那个字符开始，向左删除8个字符
#### 删除文本对象
`dd` 删除光标所在的整行
`D` 从光标位置开始删除到行尾
`d`与光标移动命令组合而成的命令删除从光标位置开始至光标移动命令限定的位置之间的所有字符。
#### 复原命令
`u` 撤销一次操作
`U` 把当前行回复成它被编辑之前的状态
#### 重复命令
`.` 重复实现刚才的插入命令或删除命令
#### 修改命令
`c` 删除光标之前的内容并输入
`C`删除光标之后的内容并输入
`cc` 删除整行并输入
#### 取代命令
`r` 用随后输入的单个字符取代光标所在的字符
如果前面给一个值，如`3r` 则从光标位置开始向右共有3个字符被新输入覆盖
`R` 用随后输入的文本取代光标所在字符及其右边的相应字符
如果给出一个值，如`5R`则新输入的内容重复出现5次，依次覆盖光标及其之后的字符序列
#### 替换命令
`s`与`r`功能类似，区别：s命令在完成置换同时，从命令方式转换成插入方式
`S`用新输入的正文替换整个当前一行
#### 字符串检索
`/模式<Enter>` 向前检索
`?模式<Enter>` 向后检索
## 第七章
### 7.1 shell概述
#### 7.1.1 shell 的特点和类型
##### 1.shell 的特点
##### 2.常用shell 类型
#### 7.1.2 shell脚本建立和执行
- 建立：`vi 文件名`
- 执行方式1：以脚本名作为参数 `bash 脚本名 [参数]` 如`bash ex2`
- 执行方式2：将shell脚本的权限设置为可执行，然后在提示符下直接执行
1.`chmod a+x 文件名`
2.1`PATH=$PATH:`就把当前目录添加到目录搜索路径中 2.2`文件名` (直接执行该文件)
2.2（不采取2.1方式则利用路径名方式）`./文件名`
- 执行shell程序 7.1
```
[root@localhost ~]# chmod a+x ex1
[root@localhost ~]# ./ex1
```
- 带有控制结构的shell程序 7.2
```
[root@localhost ~]# cat ex1
#!/bin/bash
if test $ # = 0
        then ls
        else
                for i
                do
                        ls -l $ i|grep '^d'
                done
        fi
[root@localhost ~]# bash ex1
anaconda-ks.cfg  Desktop  ex1  install.log  install.log.syslog
```

### 7.2 shell变量和算术运算
#### 7.1.2 简单shell变量
- 简单的变量定义和赋值：变量名＝字符串
- 引用变量值要在变量名前面加上`$`符号：`echo $dir`
- 用echo命令显示变量值 7.3
- 注意：如果变量值必须出现在长字符串的开头或中间，为了区分可以用花括号。如`cat ${dir}qc/m1.c`

#### 7.2.2 数组
- 赋值：`数组名[下标]=值`
- declare命令显式声明一个数组： `decalare -a 数组名`
- 读取数组元素值：`${数组名[下标]}`
- 数组初始化：`数组名=(值1 值2 值n)`
- 使用`@` `*`作为下标，显示数组所有元素 `echo ${week[*]}`

#### 7.2.3 位置参数
- 如果位置变量名大于9，那么必须使用一对花括号把它们括起来，如{10}
- `$0`始终表示命令名或shell脚本名
- 如果实际给定的命令行参数多于9个，还想使用`$1-$9`来引用，则需要用shift命令移动位置参数.
- 每执行一次shift命令，就把命令行上的实参向左移动一位
- shift 命令不能将`$0`移走，即`$0`的值不会发生变化
- shift命令可以带有一个整数作为参数
- 可以利用set命令为位置参数赋值或重新赋值
- `$0`不能用set命令赋值，它的值总是命令名

#### 7.2.4 预先定义的特殊变量
在shell中，预先定义了几个有特殊含义的shell变量，它们的值只能由shell根据实际情况进行赋值，而不能通过用户重新设置
1. `$#` 除脚本名外命令行上参数的个数
2. `$?` 上一条前台命令执行后的返回值（退出码），十进制数。多数shell命令执行成功时，则返回值为0，否则返回非0值
3. `$$` 当前进程的进程号
4. `$!` 上一个后台命令对应的进程号
5. `$*` 在命令行上实际给出的所有实参
6. `$@` 详见P127