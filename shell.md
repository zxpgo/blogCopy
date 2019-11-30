---
title: shell基本语法
date: 2019-09-20 11:44:14
tags: shell
mathjax: true
---

shell基本语法，包括变量，条件语句，分支，输入输出以及awk等等。<!--more-->

# shell基本语法

## 变量

- 环境变量: env，可以当作一个全局变量来用

- 本地变量 - 自定义的变量

  - 变量定义
    - shell中的变量没有数据类型，默认值都是string类型
    - VAR=hello 或者 VAR="hello"
    - 注意事项：
      - 注意等号前后不能有空格
      - 定义shell变量的时候，建议变量名大写
  - 用export命令可以把本地变量到处为环境变量
    - export VARNAME 或者 export VARNAME=value
    - 永久提升为环境变量： 将变量和提升语句放入到`/etc/profile`或者`~/.bashrc`文件中

- 位置变量

  - $0: 相等于C语言main函数的argv[0]，当前执行的文件名
  - $1: 输入的第一个参数，依此类推

- 特殊的变量：

  - $#:  参数的个数
  - $*： 表示参数列表，输出所有参数
  - $@： 表示参数列表，输出所有参数
  - $$：进程ID
  - $?:  执行上一个命令的返回值，0表示没有错，执行成功

  `$@`和`$*`的区别：

  - 一般情况下一样
  - `"$@"`和`“$*”`不一样
    - for循环时，“$*”将所有的参数作为一个整体
    - for循环时，"$@"有多少个参数被拆分成几部分

- set和unset命令

  - set: 列出系统中的所有环境变量和函数（不常用）
  - unset: 用于删除一个变量 `unset VAR`

- 算术运算

  ```shell
  VAR=9
  VAR=$((VAR+10))
  echo $VAR
  VAR=$[VAR+10]
  echo $VAR
  ```

- 进制运算

  $[base#n]

  - base：表示进制
  - #: 连接符
  - n: 数字

  ```shell
  VAR=$[2#11]
  echo $VAR #输出3
  VAR=$[7#11]
  echo $VAR #输出8
  ```

- 转移字符 \

  ```shell
  echo \$?
  #输出 $?
  ```

- 单引号和双引号

  - VAR='$date' : 单引号中的内容原样输出
  - VAR="$date": 会继续双引号中的命令，输出结果字符串

- 将一个命令输出放到一个变量中

  ```shell
  VAR=`date'
  VAR=$(date)
  echo $VAR
  ```

  

## 条件测试

### 条件测试命令

- test
- [] - test 一样
- [[]]
  - []或者[[]]中的数据和括号的左右需要有一个空格
  - [ -d filename]

```shell
if [ num -gt 9 ]
if [[  ]]
```

### 判断条件是否成立

如果返回值为0： 成立

如果返回值非0： 不成立

### 文件状态测试

- linux中的文件种类：7种
  - 普通文件: f
    - 
  - 目录: d 
  - 符号链接: l
  - 套接字: s
  - 管道: p
  - 字符设备: c
  - 块设备： b

```shell
test -d /root
echo $?
[ -f filename ]
echo $?
```

### 常见字符串测试

| -z string    | 字符串string为空串时(长度为0)为真 |
| ------------ | --------------------------------- |
| -n string    | 字符串为非空串时返回真            |
| str1 = str2  | 判断两个字符串是否相等            |
| str1 == str2 | 等同于=                           |
| str1 != str2 | 判断两个字符串不相等              |

### 常见的数值测试

- 等于： eq
- 不等于: ne
- 大于: gt
- 大于等于:  ge
- 小于： lt
- 小于等于： le 

```shell
[ 9 -eq 8 ]
echo $? #1
[ 3 -lq 8 ]
echo $? #0
```

### 测试时使用的逻辑操作符

a表示and

o表示or

!表示not

```shell
[ 9 -eq 8 -a 3 -lq 8]
echo $?
[ 9 -eq 8 -o 3 -lq 8]
echo $?
[ ! 9 -eq 8 ] #需要注意空格
echo $?
```

### test [] [[]]

- **test**和**[**是bash的内部命令
- **[[**是bash程序语言的关键字

- 绝大多数情况下，这个三个功能通用。但是命令和关键字总是有区别的。

- 区别：
  - 在[[中使用&&和||
  - 在[中使用-a和-o表示逻辑与和逻辑或

```shell
[[ 9 -eq 8 && 3 -lq 8 ]]
echo $?
[[ 9 -eq 8 || 3 -lq 8 ]]
echo $?
```

## 分支

### if/then/elif/else/fi

语法格式：

```shell
if [ 条件判断语句 ]; then
	处理语句
	处理语句
fi

#或者
if [ 条件判断语句 ]
then
	处理语句
	处理语句
fi

if [ 条件判断语句 ]
then
	处理语句
	处理语句
elif [ 判断条件 ]
then
	处理语句
	处理语句
else
    处理语句 
fi
```

空命令

```shell
if :; then #总是成立，等价于c中的if(1)
	处理命令
fi
```

### case/esac

基本语法：

```shell
case 变量 in 
yes|Yes|y|Y)
	处理语句
	处理语句
	;;        #相等于break
No|no|n|N)
	处理语句
	处理语句
	;;
*)            #其他情况,相等于default
	处理语句
	;;
esac
```

### for/do/done

语法格式：

```shell
for 变量 in list(列表); do
	控制语句
	控制语句
done
#example
for VAR in a b c d e f g; do
	echo $VAR
done
#example 
for VAR in $(ls); do #输出当前目录下的所有文件
	echo $VAR
done

for VAR in `ls`; do
	echo $VAR
done
```

### while/do/done

语法格式：

```shell
while [ 条件测试语句 ]; do
	控制语句
	控制语句
done

#或者
while [ 条件测试语句 ]
do
	控制语句; 控制语句;done #其他分支类似
```

read阻塞终端，将输入到终端的数据存储到变量中。

```shell
read VAR
```

自加操作：

```shell
count=$[count+1]
```

## 输入和输出

输出：

echo

- echo 字符串
- -e: 解析字符串中的/n
- -n:  去掉echo默认加上的换行符

```shell
echo -e "hello\nworld" #必须加上双引号
```

文件重定向：

cmd > file

```shell
cmd > file
#example
ls > file

cmd >> file #内容追加到file文件中
#example
pwd >> file

cmd > file 2>&1  #标准输出和标准错误都重定向到文件中
```

`cmd 1 > file 2>&1`

意思是标准输出重定向到文件中，标准错误重定向到标准输出，最后表示标准错误和标准输出都重定向到文件中

- 1表示标准输出
- 2标准标准错误

 注意：加上&表示一个文件描述符，如果不加&表示1是一个文件

## awk

处理行和列，主要用于列的处理

- awk缺省的行分隔符是换行

- 缺省的列分割符是连续的空格和TAB

  - 如何取出每一列

    - $0: 当前行，还没有拆分
    - $1: 第一列
    - $2: 第二列
    - 依此类推

  - 如果不是缺省的如何指定分隔符: -F后面指定分隔符

    `awk -F: `

```shell
awk -F: '{print $6}' /etc/passwd
```

一般格式：

- awk 参数 ‘/pattern/{actions}’ 目标文件
- awk 参数 ‘condition{actions}’ 目标文件
  - condition -- 条件
    - 特殊条件
      - awk 'BEGIN{FS=":"}{print $1}' /etc/passwd
        - BEGIN用来在操作开始之前指定一些内容，比如分隔符
      - awk '{x=x+1}END{print x}'
        - END表示遍历结束后，执行的操作
  - patterN -- 正则表达式
  - actions -- 匹配成功后的一系列操作

如何定义变量：

- 需要变量直接写即可，默认值为0
- 定义变量的时候直接指定一个初始值
  - awk -v x=10 '条件/正则表达式/{actions}' 文件名

从`ps aux`得到的数据中找出pid>1000&&pid<2000的进程个数：

```shell
ps aux | awk '$2>=1000 && $2<=2000{print $2}' #得到所有满足条件的进程PID

ps aux | awk '$2>=1000 && $2<=2000{x=x+1}END{print x}' #输入满足条件的进程个数，x的默认初始值为0

ps aux | awk -v x=0 '$2>=1000 && $2<=2000{x=x+1}END{print x}' #使用-v对x设定初始值
```



给脚本添加执行权限：`chomd u+x test.h`














