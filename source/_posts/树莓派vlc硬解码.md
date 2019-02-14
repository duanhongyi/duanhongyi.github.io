---
title: 树莓派vlc硬解码
tags: []
date: 2016-07-27 17:11:37
---

1、安装vlc

<div class="highlight"><pre>sudo apt-get update
sudo apt-get upgrade
sudo apt-get install vlc
</pre></div>

2、获得vlc源代码

<div class="highlight"><pre>sudo apt-get build-dep vlc
apt-get <span class="nb">source </span>vlc
<span class="nb">cd </span>vlc-2.2.1
</pre></div>

3、编辑debian/rules，找到--with-kde-solid位置，添加中间5行数据。

<div class="highlight"><pre>--with-kde-solid=/usr/share/kde4/apps/solid/actions/ \
--enable-omxil \
--enable-omxil-vout \
--enable-rpi-omxil \
--disable-mmal-codec \
--disable-mmal-vout \
$(NULL)
</pre></div>

4、 edit the debian/vlc-nox.install.in file，找到第一行libomxil_plugin.so位置，添加最后一行数据。

<div class="highlight"><pre>usr/lib/vlc/plugins/codec/libomxil_plugin.so
usr/lib/vlc/plugins/codec/libomxil_vout_plugin.so
</pre></div>

5、build deb

<div class="highlight"><pre>debuild -i -us -uc -b
</pre></div>

6、安装deb包

<div class="highlight"><pre><span class="nb">cd</span> .. <span class="o">&amp;&amp;</span> sudo dpkg -i vlc-nox_2.2.1-3_armhf.deb
</pre></div>