---
layout:            post
title:             "如何自定义Ubuntu的CoreDump文件"
menutitle:         "如何自定义Ubuntu的CoreDump文件"
category:          Linux
author:            dean
tags:              coredump ubuntu
language:          ZH
---
在编写Ubuntu的应用的时候，往往无法正常生成CoreDump文件。这是因为Ubuntu通过[apport](https://wiki.ubuntu.com/Apport)来截获并显示一些core dump信息。我们可以通过自定义来生成core dump文件并通过ADB来调试。
### 步骤
####1.禁用apport
  执行`sudo vim /etc/default/apport`
  设置`enable`为0。
####2.查看当前配置
  `sysctl kernel.core_pattern`
####3.新建文件
  新建`60-core-pattern.conf`,编辑插入下面内容并保存。
  ```kernel.core_pattern = /var/crash/core_%e_%p```
####4.执行
```bash
sudo cp 60-core-pattern.conf /etc/sysctl.d/
```
####5.执行
```bash
sudo sysctl --system
```
####6.查看当前core文件设置
执行`ulimit -a`,如果第一行内容为`core file size          (blocks, -c) 0`,则执行`ulimit -c unlimited`。确保第一行为`core file size          (blocks, -c) unlimited`。
####7.完成
通过以上设置，当出现core dump，会在`/var/crash`目录下生成core dump文件