---
title: Centos7备份和还原
date: 2018-12-22 17:24:24
tags:  "Linux"
mathjax: true

---

Centos7备份系统和还原系统。<!--more-->

如果你备份过Windows系统，那么你一定记忆犹新：首先需要找到一个备份工具(通常都是私有软件)，然后重启电脑进入备份工具提供的软件环境，在这里备份或者恢复Windows系统。Norton Ghost是备份Windows系统时经常使用的备份工具。

## 备份系统

首先成为root用户：

	sudo su

然后进入文件系统的根目录(当然，如果你不想备份整个文件系统，你也可以进入你想要备份的目录，包括远程目录或者移动硬盘上的目录)：

	cd /

下面是我用来备份系统的完整命令：

	
	tar cvpzf backup.tgz --exclude=/proc --exclude=/lost+found --exclude=/mnt --exclude=/sys --exclude=backup.tgz /

- “tar”当然就是我们备份系统所使用的程序了。
- cvpfz”是tar的选项，意思是“创建档案文件”、“保持权限”(保留所有东西原来的权限)、“使用gzip来减小文件尺寸”。
- “backup.gz”是我们将要得到的档案文件的文件名。 
- “/”是我们要备份的目录，在这里是整个文件系统。 

在档案文件名“backup.gz”和要备份的目录名“/”之间给出了备份时必须排除在外的目录。有些目录是无用的，例如“/proc”、“/lost+ found”、“/sys”。

备份完成后，在文件系统的根目录将生成一个名为“backup.tgz”的文件，它的尺寸有可能非常大。现在你可以把它烧录到DVD上或者放到你认为安全的地方去。


你还可以用Bzip2来压缩文件，Bzip2比gzip的压缩率高，但是速度慢一些。如果压缩率对你来说很重要，那么你应该使用Bzip2，用“j”代替命令中的“z”，并且给档案文件一个正确的扩展名“bz2”。完整的命令如下：

	tar cvpjf backup.tar.bz2 –-exclude=/proc –exclude=/lost+found -–exclude=/backup.tar.bz2 –-exclude=/mnt –exclude=/sys /


## 恢复系统

在进行恢复系统的操作时一定要小心！如果你不清楚自己在做什么，那么你有可能把重要的数据弄丢，请务必小心！ 


切换到root用户，并把文件“backup.tgz”拷贝到分区的根目录下。 

在 Linux中有一件很美妙的事情，就是你可以在一个运行的系统中恢复系统，而不需要用boot-cd来专门引导。当然，如果你的系统已经挂掉不能启动了， 你可以用Live CD来启动，效果是一样的。

使用下面的命令来恢复系统：

	tar xvpfz backup.tgz -C /


如果你的档案文件是使用Bzip2压缩的，应该用：

	tar xvpfj backup.tar.bz2 -C /

注意：上面的命令会用档案文件中的文件覆盖分区上的所有文件。


执行恢复命令之前请再确认一下你所键入的命令是不是你想要的，执行恢复命令可能需要一段不短的时间。 恢复命令结束时，你的工作还没完成，别忘了重新创建那些在备份时被排除在外的目录：

	mkdir proc
	mkdir lost+found
	mkdir mnt
	mkdir sys

