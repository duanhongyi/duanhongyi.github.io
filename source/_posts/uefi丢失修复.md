---
title: uefi丢失修复
tags: []
date: 2014-11-11 16:04:44
---

1、我们假设系统根目录是在/dev/sda4

2、挂载系统目录

<div class="highlight"><pre>mount /dev/sda4 /mnt
</pre></div>

3、挂载其他目录：

<div class="highlight"><pre>for f in proc sys dev ; do mount --bind /$f /mnt/$f ; done
</pre></div>

3、查询历史的efi，并删除不需要的启动项，我们假设假设需要删除`Boot0000*`

<div class="highlight"><pre>efibootmgr -v
efibootmgr -b 0000* -B
</pre></div>

4、修复bootloader

<div class="highlight"><pre>chroot /mnt
grub-install --bootloader-id ubuntu /dev/sda
</pre></div>