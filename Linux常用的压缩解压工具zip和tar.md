---
title: Linux常用的压缩/解压工具：zip和tar
date: 2018-08-11 22:28:50
tags: Linux
categories: "实验楼学习笔记之Linux"
top: 10000
---

知识点：zip命令、tar命令、压缩与解压常用组合<!--more-->

# 常用压缩包格式

| 文件后缀名	| 说明 |
|---|
| *.zip	| zip 程序打包压缩的文件 |
| *.rar	| rar 程序压缩的文件 |
| *.7z	| 7zip 程序压缩的文件 |
| *.tar	| tar 程序打包，未压缩的文件 |
| *.gz	| gzip 程序（GNU zip）压缩的文件 |
| *.xz	| xz 程序压缩的文件 |
| *.bz2	| bzip2 程序压缩的文件 |
| *.tar.gz	| tar 打包，gzip 程序压缩的文件 |
| *.tar.xz	| tar 打包，xz 程序压缩的文件 |
| *tar.bz2	| tar 打包，bzip2 程序压缩的文件 |
| *.tar.7z	| tar 打包，7z 程序压缩的文件 |


# zip压缩

	zip -r -q -o shiyanlou.zip /home/shiyanlou/Desktop

将Desktop目录下的子文件打包成shiyanlou.zip压缩文件，`-r`表示递归打包包含子目录的全部内容，`-q`表示安静模式，不向屏幕显示内容，`-o`表示输出文件，须在其后紧跟打包输出文件名（shiyanlou.zip）。

	du -h shiyanlou.zip 

`du`命令查看打包文件的大小。`-h`表示文件大小可读，`-d`表示查看所查文件的深度，打印在屏幕上。

加密创建zip包：</br>
使用`-e`参数

	zip -r -e -o shiyanlou_e.zip /home/shiyanlou/Desktop

跟着屏幕提示输入密码

**注意**：： 关于` zip `命令，因为 Windows 系统与 Linux/Unix 在文本文件格式上的一些兼容问题，比如换行符（为不可见字符），在 Windows 为 CR+LF（Carriage-Return+Line-Feed：回车加换行），而在 Linux/Unix 上为 LF（换行），所以如果在不加处理的情况下，在 Linux 上编辑的文本，在 Windows 系统上打开可能看起来是没有换行的。如果你想让你在 Linux 创建的 zip 压缩文件在 Windows 上解压后没有任何问题，那么你还需要对命令做一些修改：

	zip -r -l -o shiyanlou.zip /home/shiyanlou/Desktop

# unzip解压zip文件

	unzip shiyanlou.zip

u使用安静模式,将文件解压到指定目录：

	unzip -q shiyanlou.zip -d ziptest

上述目录不存在，将会自动创建，如果不想解压只想查看压缩包的内容，可以使用`-l`参数：

	unzip -l shiyanlou.zip

**注意**：中文的兼容性问题，windows系统创建的压缩文件，如果有中文文档或文件名为中文，默认采用GBK或其他编码，而Linux上默认使用的是UTF-8编码。

使用`-o`参数指定编码类型：

	unzip -o GBK 中文压缩文件.zip

# tar打包工具

tar 原本只是一个打包工具，只是同时还是实现了对 7z、gzip、xz、bzip2 等工具的支持，这些压缩工具本身只能实现对文件或目录（单独压缩目录中的文件）的压缩，没有实现对文件的打包压缩，所以我们也无需再单独去学习其他几个工具，tar 的解压和压缩都是同一个命令，只需参数不同，使用比较方便。

**创建一个tar包：**

	tar -cf shiyanlou.tar home/shiyanlou/Desktop

![](https://i.imgur.com/t43P6f3.png)
不能使用相对路径，不然会提示图中问题

上面命令中，`-c`表示创建一个tar包文件，`-f`用户指定创建的文件，注意文件名必须紧跟在`-f`参数之后，比如不能写出`tar -fc shiiyanlou.tar`,可以写成`tar -f shiyanlou.tar -c `。</br>
`-v`参数以可视的方式输出打包的文件，

**解压一个tar包：**
</br>解压参数：`-x`
</br>指定已存在的目录： `-C`</br>

	tar -xf shiyanlou.tar -C tardir

**只查看不解包文件：**
</br>参数： -t

	tar -tf shiyanlou.tar


保留文件属性和跟随链接（符号链接或软链接），有时候我们使用 tar 备份文件当你在其他主机还原时希望保留文件的属性（`-p` 参数）和备份链接指向的源文件而不是链接本身（`-h` 参数）：
	tar -cphf etc.tar /etc

**使用gzip来压缩文件**

添加`-z`参数

	tar -czf shiyanlou.tar.gz home/shiyanlou/Desktop


**解压`*.tar.gz`文件**
	
	tar -xzf shiyanlou.tar.gz


要使用其它的压缩工具创建或解压相应文件只需要更改一个参数即可：

| 压缩文件格式 | 参数 |
|---|
| *.tar.gz | -z |
| *.tar.xz | -J |
| *tar.bz2 | -j |

tar 命令的参数很多，不过常用的就是上述这些，需要了解更多你可以查看 man 手册获取帮助。

# 总结


常用命令：

- zip：
 - 打包 ：zip something.zip something （目录请加 -r 参数）
 - 解包：unzip something.zip
 - 指定路径：-d 参数
- tar：
 - 打包：tar -cf something.tar something
 - 解包：tar -xf something.tar
 - 指定路径：-C 参数