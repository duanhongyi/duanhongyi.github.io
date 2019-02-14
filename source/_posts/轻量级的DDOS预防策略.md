---
title: 轻量级的DDOS预防策略
tags: []
date: 2017-04-08 21:18:09
---

一、在`/etc/sysctl.conf`中添加：

<div class="highlight"><pre>net.ipv4.tcp_max_syn_backlog<span class="o">=</span>2048
net.ipv4.tcp_syncookies<span class="o">=</span>1
net.ipv4.tcp_syn_retries <span class="o">=</span> 0
</pre></div>

二、iptables设置

<div class="highlight"><pre><span class="c">#防止SYN攻击 轻量级预防</span>
iptables -N syn-flood
iptables -A INPUT -p tcp --syn -j syn-flood
iptables -I syn-flood -p tcp -m limit --limit 3/s --limit-burst <span class="m">6</span> -j RETURN
iptables -A syn-flood -j REJECT
<span class="c">#防止DOS太多连接进来,可以允许外网网卡每个IP最多15个初始连接,超过的丢弃</span>
iptables -A INPUT -i eth0 -p tcp --syn -m connlimit --connlimit-above <span class="m">15</span> -j DROP
iptables -A INPUT -p tcp -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT  -p tcp --syn -m limit --limit 12/s --limit-burst <span class="m">24</span> -j ACCEPT
iptables -A FORWARD -p tcp --syn -m limit --limit 1/s -j ACCEPT
</pre></div>