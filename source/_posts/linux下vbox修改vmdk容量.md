---
title: linux下vbox修改vmdk容量
tags: []
date: 2014-12-29 16:01:21
---

1、必须先转换成vdi格式才能修改大小：

<div class="highlight"><pre>VBoxManage clonehd xx.vmdk xx.vdi --format vdi
</pre></div>

2、修改vdi磁盘大小：

<div class="highlight"><pre>VBoxManage modifyhd xx.vdi --resize 75600
</pre></div>

3、修改xxx.vbox配置文件，删除xx.vmdk，添加xx.vdi。

4、启动虚拟机，在虚拟机内部使用resize2fs修改分区大小。

<div class="highlight"><pre>resize2fs /dev/sdxx xxM
</pre></div>