---

title:  VS2017 socket新函数代替旧函数引起的编译错误
date: 2019-03-31 10:44:14
tags: socket
mathjax: true

---

错误描述：error C4996: 'inet_addr': Use inet_pton() or InetPton() instead or define _WINSOCK_DEPRECATED_NO_WARNINGS to disable deprecated API warnings<!--more-->

错误：

![](https://i.imgur.com/DNySPKs.png)


## 解决本法：

在解决方案资源管理器中，找到项目

![](https://i.imgur.com/KNaUH75.png)

右键对应项目，点击属性（最后一个）

![](https://i.imgur.com/xjc7JGw.png)

出现如下界面，选择C/C++->常规->SDL检查

![](https://i.imgur.com/iwUdX6e.png)

将SDL检查的值改成__否__，最后点击应用和确定

![](https://i.imgur.com/am1QNys.png)

完美解决

