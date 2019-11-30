---
title: Public auditing for cloud storage
date: 2019-10-27 11:44:14
tags: 论文
mathjax: true
---

云存储中数据的完整性审计方案论文阅读。坚持一天一篇Paper！！！<!--more-->

 目录
------
[TOC]

## Identity-based public auditing for cloud storage systems against malicious auditors via blockchain  

文章链接： http://engine.scichina.com/publisher/scp/journal/SCIS/62/3/10.1007/s11432-018-9462-0?slug=fulltext (Science China Information Sciences, 2019)

采用BlockChain来防止云服务器和TPA的共谋。

论文中存在的问题：

- 签名的部分，$T_i$没必要放在签名中，这样只会增加了通信开销，完全可以将$T_i$设成$T$当作用户的公钥。因为$T_i$与数据块无关。

##  Efficient ID-based public auditing for the outsourced data in cloud storage  

文章链接： https://www.sciencedirect.com/science/article/pii/S0020025516000591 (Information Sciences, 2016)

提出了一种基于身份的完整性审计方案。

缺点：

- 没有解决密钥托管问题（key escrow）
- 没有保证用户的数据隐私，TPA可以获取用户的数据
- 最严重的问题是该方案是不安全的，论文 [Insecurity of an identity-based public auditing protocol for
  the outsourced data in cloud storage ](https://www.sciencedirect.com/science/article/pii/S0020025516309847 )指出了该方案存在的两种攻击，但是没有给出解决方案。

## Efficient and Secure Identity-Based Public Auditing for Dynamic Outsourced Date with proxy

文章链接：http://www.itiis.org/digital-library/manuscript/file/1828/TIIS+Vol+11,+No+10-19.pdf (  KSII TRANSACTIONS ON INTERNET AND INFORMATION SYSTEMS, 2017)

提出以一种基于代理的公开审计方法，将标签的生成委托给Proxy。

- TPA进行公开审计
- Proxy对用户的数据生成标签

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20191101105600.png)

个人理解：实用性不大，这样大大增加了数据的通信开销。而且标签的计算开销也不是特别大。

## SeShare: Secure cloud data sharing based on blockchain and public auditing  

文章链接： https://onlinelibrary.wiley.com/doi/pdf/10.1002/cpe.4359 (WILEY, 2017)

创新点：

- 采用区块链来防止多个用户同时更新文件时出错。

通过区块链中下一个块的指针指向前一个文件，这样如果A、B两个用户同时更新文件，如果A先向云更新数据，这样最后的那个文件指针变了，如果B再来更新文件，因为他拥有的文件指针与当前区块链的文件指针不同，所以无法更新。

问题：

- 作者采用的公开审计方案无法提供隐私保护
- 前提条件使用用户和云不能共谋，而且云必须是诚实的，该方案中云也可以计算数据的标签（极其危险）。

##   Efficient Privacy-Preserving Certificateless Provable Data Possession Scheme for Cloud Storage  
文章连接： https://ieeexplore.ieee.org/document/8821311 (IEEE Access, 2019)

存在问题：

- 无法防止服务器的篡改攻击和伪造攻击

[Insecurity of an identity-based public auditing protocol for the outsourced data in cloud storage](https://www.sciencedirect.com/science/article/pii/S0020025516309847 )这篇文章的两种攻击方法都可以对该文章中的方案进行攻击。

创新点：

- 提供了隐私保护
- 避免了证书管理

## CIPPPA: Conditional Identity Privacy-Preserving Public Auditing for Cloud-Based WBANs against Malicious Auditors

文章链接： https://ieeexplore.ieee.org/document/8758993 （ IEEE Transactions on Cloud Computing( Early Access )， 2019 ）

该论文中的方案针对医院中病人信息存储的场景。将病人信息存储在云端，需要保证数据的隐私以及数据拥有者的隐私，同时还需要可追踪。

创新：

- 对用户的身份进行匿名
- PKG可以追踪和撤回有不良行为的病人的真实身份
- 采用以太坊区块链技术来防止TPA共谋，即欺骗用户

身份匿名和可追踪部分可学习。

##   Enabling Identity-Based Integrity Auditing and Data Sharing With Sensitive Information Hiding for Secure Cloud Storage  

文章链接： https://ieeexplore.ieee.org/document/8395433  (IEEE Transactions on Information Forensics and Security, 2019)

文章提出了一个针对医院病人信息保护的云存储公开审计方案，主要是保护病人的隐私，以及医院的隐私。

系统模型：

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20191111142223.png)

为了保证病人的隐私，User上传给Sanitizer的数据中，对病人个人信息部分进行盲化，而Sanitizer的主要作用是对医院信息进行盲化，将病人的个人信息部分用通配符替换，以及将标签转换成正确的标签。

创新点：

- 提出了一个保证病人个人信息以及医院信息隐私的公开审计方法

##   An Efficient Public Auditing Protocol With Novel Dynamic Structure for Cloud Data  

文章链接： https://ieeexplore.ieee.org/document/7931714 ( IEEE Transactions on Information Forensics and Security ( Volume: 12 , [Issue: 10](https://ieeexplore.ieee.org/xpl/tocresult.jsp?isnumber=7956594) , Oct. 2017 ) )

创新点：

- 设计了一个双向链信息表和数据块位置矩阵，来进行快速搜索
- 全局审计和抽象审计：全局审计可以防止用户和TPA共谋，即TPA审计用户的所有数据并告知结果给云，个人觉得无法实现防止共谋，因为证据还是需要TPA审计，TPA可以伪造结果告诉云。
- 支持动态操作
- 支持批量审计：多用户和多文件

缺点：

- 没有考虑隐私保护问题

## Oruta: privacy-preserving public auditing for shared data in the cloud

文章链接： http://ieeexplore.ieee.org/document/6710128/ (IEEE Transactions on Cloud Computing,  ( Volume: 2 , [Issue: 1](https://ieeexplore.ieee.org/xpl/tocresult.jsp?isnumber=6804708) , Jan.-March 2014 ) )

文章实现了群组数据的公开审计方案，可以保护数据隐私，和用户隐私。

- 采用一个环签名，可以很好的实现群组公开审计，每个用户上传的数据，TPA都可以审计，而且无需直到数据上传的用户
- 提供数据隐私保护，TPA无法获取数据隐私
- 支持执行多审计任务

##   Public Integrity Auditing for Dynamic Data Sharing With Multiuser Modification  

文章链接： https://ieeexplore.ieee.org/document/7086294  （ [IEEE Transactions on Information Forensics and Security](https://ieeexplore.ieee.org/xpl/RecentIssue.jsp?punumber=10206) ( Volume: 10 , [Issue: 8](https://ieeexplore.ieee.org/xpl/tocresult.jsp?isnumber=7127092) , Aug. 2015 ) ）

文章提出了一种针对群组共享数据公开审计方案，支持群组用户修改数据，以及用户撤销。

- 支持用户撤销，用户撤销后，主用户生成一个数据发送云更新数据标签
- 采用(U,N)Shamir Secret Sharing 方案实现了高级的用户撤销，云中的U个节点生成一个标签的一个部分，然后聚合起来得到完整的标签
- 支持多文件审计



## Enabling Efficient User Revocation in Identity-based Cloud Storage Auditing for Shared Big Data

文章链接： https://ieeexplore.ieee.org/document/8345742  ( [IEEE Transactions on Dependable and Secure Computing](https://ieeexplore.ieee.org/xpl/RecentIssue.jsp?punumber=8858) ( Early Access ) )

文章中提出了一种群组用户共享数据公开审计方案，支持用户撤销，而且撤销时不需要更新标签，而只要更新未撤销用户的标签。

提出的方案基于身份，群组有一个群组密钥，所有未撤销用户可知。改群组密钥由两个密码一起生成，包括：由PKG生成的身份密钥和群组管理员生成的部分私钥，两者相加组成了群组私钥。两者生成的密钥，群组用户都是可以验证的，判断是否合法。

对于用户撤销，需要群组管理员更新部分私钥，其中需要使用一个变量即群组中撤销用户的数量。

## Efficient auditing for shared data in the cloud with secure user revocation and computations outsourcing

文章链接： https://www.sciencedirect.com/science/article/pii/S0167404817302663 ([Computers & Security](https://www.sciencedirect.com/science/journal/01674048)

[Volume 73](https://www.sciencedirect.com/science/journal/01674048/73/supp/C), March 2018, Pages 492-506)

文章提出了一种共享数据公开审计方案，支持用户撤销。

采用Shamire secret sharing，当用户撤销后，重签名。