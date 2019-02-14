---
title: Crossing　GFW
tags: []
date: 2014-07-23 17:31:51
---

1、执行ssh隧道

<div class="highlight"><pre>ssh -Nf -o ServerAliveInterval=29 -D 18080 user@ip
</pre></div>

2、安装privoxy

<div class="highlight"><pre>apt-get install privoxy
</pre></div>

3、配置privoxy，修改/etc/privoxy/config，在最后添加:

<div class="highlight"><pre>forward-socks5 / 127.0.0.1:18080 .
</pre></div>

4、privoxy的默认端口是8118，代理方式是http。

5、重启privoxy，在浏览器配置代理。

6、完成