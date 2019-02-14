---
title: 安装hadoop2.0注意事项
tags: []
date: 2013-10-19 23:50:31
---

1.  千万不能添加一条指向自己的hosts记录，ubuntu默认会建立，请一定删除。
2.  安装统一的内网域名解析服务器dnsmasq辅助管理haddop。
3.  dnsmasq域名服务器配置完成后需要重启（service dnsmasq restart）。
4.  配置统一的rsa密钥，hadoop各节点之间无密码登录。
5.  需要在整个集群中使用NTP服务。

[具体安装方法请参见hadoop官网](http://hadoop.apache.org/docs/r2.2.0/hadoop-project-dist/hadoop-common/ClusterSetup.html)