---

title: Shell编程-文件包含(八)
date: 2019-03-28 13:58:14
tags: Shell
mathjax: true

---

和其他语言一样，Shell也可以包含外部脚本。这样可以很方便的封装一些公用的代码作为一个独立的文件。<!--more-->

Shell文件包含的语法格式如下：

	. filename #注意点号(.)和文件名中间有一个空格
	或
	source filename

### 实例

创建两个shell脚本文件。

test1.sh 代码如下：


	#! /bin/bash
	
	url="https://www.zxpblog.cn"


test2.sh 代码如下：

	#! /bin/bash
	
	. ./test1.sh
	
	echo "The url is $url"

输出：

	[root@localhost bash]# chmod +x test2.sh
	[root@localhost bash]# ./test2.sh
	The url is https://www.zxpblog.cn