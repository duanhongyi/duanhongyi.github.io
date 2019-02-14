---
title: ubuntu16.04系统精简
tags: []
date: 2016-08-10 16:08:26
---

一、更新系统

<div class="highlight"><pre>apt-get update
apt-get dist-upgrade
</pre></div>

二、查看所有内核

<div class="highlight"><pre>sudo dpkg --get-selections <span class="p">|</span>grep linux
</pre></div>

三、清除不用的内核

<div class="highlight"><pre>apt-get purge linux-image-extra-x.x.x-x-generic <span class="se">\</span>
    linux-image-x.x.x-x-generic <span class="se">\</span>
    linux-headers-x.x.x-x <span class="se">\</span>
    linux-headers-x.x.x-x-generic
</pre></div>

四、删除无用的软件

<div class="highlight"><pre>apt-get purge mdadm <span class="se">\</span>
    git <span class="se">\</span>
    acpid <span class="se">\</span>
    vim* <span class="se">\</span>
    ubuntu-cloudimage-keyring <span class="se">\</span>
    ubuntu-core-launcher <span class="se">\</span>
    software-properties-common <span class="se">\</span>
    lxc* <span class="se">\</span>
    lxd* <span class="se">\</span>
    perl

apt-get autoremove
</pre></div>

五、清理文件

<div class="highlight"><pre>apt-get clean all
</pre></div>