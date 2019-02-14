---
title: 迁移swift步骤
tags: []
date: 2014-10-27 09:43:17
---

1、 备份/etc/rsyncd.conf文件和/etc/swift/目录。

2、 安装操作系统，并预留sda4

3、 制作存储硬盘swift

<div class="highlight"><pre>mkfs.xfs /dev/sda4
echo &quot;/dev/sda4 /srv/node/sda4 xfs noatime,nodiratime,nobarrier,logbufs=8 0 0&quot; &gt;&gt; /etc/fstab
mkdir -p /srv/node/sda4
mount /srv/node/sda4
chown -R swift:swift /srv/node
</pre></div>

4、 安装swift

5、 将第一步备份的swfit配置文件复原

6、创建必要的文件夹并修改必要的权限

<div class="highlight"><pre>mkdir -p /var/cache/swift
chown -R swift:swift /var/cache/swift/
mkdir -p /var/run/swift
chown -R swift:swift /var/run/swift
chown -R swift:swift /etc/swift
</pre></div>

7、 修改/etc/default/rsync

<div class="highlight"><pre>RSYNC_ENABLE=true
</pre></div>

8、 安装memcached并修改监听为`-l 0.0.0.0`,并重启memcached

<div class="highlight"><pre>service memcached restart
</pre></div>