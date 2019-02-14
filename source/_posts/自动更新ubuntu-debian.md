---
title: 自动更新ubuntu/debian
tags: []
date: 2013-02-19 23:50:31
---

unattended-upgrades是一个 Debian 软件包，用以自动安装安全（和其他）更新。如果你计划使用它，你要有一些方法监控你的系统，例如，同时安装 apt-listchanges软件包并设置它给你发送有关更新的邮件。

1.  先安装程序

    > <div class="highlight"><pre>apt-get install unattended-upgrades
> </pre></div>

2.  然后执行

    > <div class="highlight"><pre>dpkg-reconfigure unattended-upgrades //选择yes
> </pre></div>

3.  修改配置文件

    > <div class="highlight"><pre>vim  /etc/apt/apt.conf.d/50unattended-upgrades  //配置
> vim /etc/apt/apt.conf.d/20auto-upgrades  //更新周期
> </pre></div>